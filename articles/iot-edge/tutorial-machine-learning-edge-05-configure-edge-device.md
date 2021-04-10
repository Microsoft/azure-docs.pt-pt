---
title: 'Tutorial: Configurar um dispositivo Azure IoT Edge - Machine learning on IoT Edge'
description: Neste tutorial, você configurará uma máquina virtual Azure executando Linux como um dispositivo Azure IoT Edge que funciona como uma porta de entrada transparente.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, devx-track-azurecli
ms.openlocfilehash: b59f8343c9dff07a32accd471f70ddf9f5309b8d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463090"
---
# <a name="tutorial-configure-an-azure-iot-edge-device"></a>Tutorial: Configurar um dispositivo Azure IoT Edge

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Neste artigo, configuramos uma máquina virtual Azure que executa o Linux para ser um dispositivo Azure IoT Edge que funciona como uma porta de entrada transparente. Uma configuração transparente do gateway permite que os dispositivos se conectem ao Azure IoT Hub através do gateway sem saber que o gateway existe. Ao mesmo tempo, um utilizador que interage com os dispositivos no IoT Hub desconhece o dispositivo de gateway intermédio. Em última análise, adicionaremos análises de bordas ao nosso sistema adicionando módulos IoT Edge ao gateway transparente.

>[!NOTE]
>Os conceitos deste tutorial aplicam-se a todas as versões do IoT Edge, mas o dispositivo de amostra que cria para experimentar o cenário corre a versão 1.1 do IoT Edge.

Os passos deste artigo são normalmente realizados por um desenvolvedor de nuvem.

Nesta secção do tutorial, aprende-se a:

> [!div class="checklist"]
>
> * Crie certificados para permitir que o seu dispositivo gateway se conecte de forma segura aos seus dispositivos a jusante.
> * Crie um dispositivo IoT Edge.
> * Crie uma máquina virtual Azure para simular o seu dispositivo IoT Edge.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo faz parte de uma série para um tutorial sobre a utilização de Azure Machine Learning em IoT Edge. Cada artigo da série baseia-se no trabalho no artigo anterior. Se chegou a este artigo diretamente, veja o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série.

## <a name="create-certificates"></a>Criar certificados

Para que um dispositivo funcione como um gateway, tem de se ligar de forma segura aos dispositivos a jusante. Com o IoT Edge, pode utilizar uma infraestrutura de chaves públicas (PKI) para estabelecer ligações seguras entre dispositivos. Neste caso, estamos a permitir que um dispositivo IoT a jusante se conecte a um dispositivo IoT Edge agindo como um gateway transparente. Para manter uma segurança razoável, o dispositivo a jusante deve confirmar a identidade do dispositivo IoT Edge. Para obter mais informações sobre como os dispositivos IoT Edge utilizam certificados, consulte [os detalhes de utilização do certificado Azure IoT Edge](iot-edge-certs.md).

Nesta secção, criamos os certificados auto-assinados usando uma imagem docker que depois construímos e corremos. Optámos por utilizar uma imagem do Docker para completar este passo porque reduz o número de passos necessários para criar os certificados na máquina de desenvolvimento do Windows. Para entender o que automatizamos com a imagem do Docker, consulte [criar certificados de demonstração para testar as funcionalidades do dispositivo IoT Edge](how-to-create-test-certificates.md).

1. Inscreva-se no seu VM de desenvolvimento.
1. Criar uma nova pasta com o caminho e o nome **c:\edgeCertificates**.

1. Se ainda não estiver em execução, inicie **o Docker para windows** a partir do menu Windows Start.

1. Abra o Visual Studio Code.

1. Selecione **pasta**  >  **de abertura de** ficheiros e, em seguida, selecione **C: fonte \\ \\ IoTEdgeAndMlSample \\ CreateCertificates**.

1. No painel **Explorer,** clique à direita **e** selecione **Build Image**.

1. Na caixa de diálogo, aceite o valor predefinido para o nome de imagem e etiqueta: **criar certificados: mais recente**.

    ![Screenshot que mostra a criação de certificados no Código do Estúdio Visual.](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

1. Espere que a construção termine.

    > [!NOTE]
    > Pode ver um aviso sobre uma chave pública desaparecida. É seguro ignorar este aviso. Da mesma forma, verá um aviso de segurança que recomenda que verifique ou repõe permissões na sua imagem, o que é seguro ignorar para esta imagem.

1. Na janela do terminal Visual Studio Code, verifique o recipiente de certificação de criação.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

1. Docker pedirá acesso ao **c: \\** unidade. **Selecione Partilhe-o**.

1. Forneça as suas credenciais quando solicitado.

1. Depois de o recipiente terminar em funcionamento, verifique se há os seguintes ficheiros em **c: \\ edgeCertificates**:

    * c: \\ edgeCertificates \\ certes \\ azure-iot-test-only.root.ca.cert.pem
    * c: \\ edgeCertificates \\ certifica \\ new-edge-device-full-chain.cert.pem
    * c: \\ edgeCertificates \\ certifica \\ novo-dispositivo de borda.cert.pem
    * c: \\ edgeCertificates \\ certifica \\ new-edge-device.cert.pfx
    * c: \\ edgeCertifica \\ o dispositivo privado de ponta \\ nova.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Faça upload de certificados para Azure Key Vault

Para armazenar os nossos certificados de forma segura e torná-los acessíveis a partir de vários dispositivos, vamos enviar os certificados para o Cofre da Chave Azure. Como pode ver na lista anterior, temos dois tipos de ficheiros de certificado: PFX e PEM. Vamos tratar o ficheiro PFX como certificados do Cofre chave para serem enviados para o Key Vault. Os ficheiros PEM são texto simples, e vamos tratá-los como segredos do Cofre-Chave. Usaremos o exemplo do Cofre-Chave associado ao espaço de trabalho Azure Machine Learning que criámos executando os [cadernos Jupyter.](tutorial-machine-learning-edge-04-train-model.md#run-the-jupyter-notebooks)

1. A partir do [portal Azure,](https://portal.azure.com)vá ao seu espaço de trabalho Azure Machine Learning.

1. A partir da página geral do espaço de trabalho machine learning, encontre o nome para **Key Vault**.

    ![Screenshot que mostra copiar o nome do cofre da chave.](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

1. Na sua máquina de desenvolvimento, faça o upload dos certificados para Key Vault. Substitua **\<subscriptionId\>** e **\<keyvaultname\>** pela informação dos seus recursos.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

1. Se for solicitado, inscreva-se em Azure.

1. O script será executado por alguns minutos com a saída que lista as novas entradas do Key Vault.

    ![Screenshot que mostra a saída do script do Key Vault.](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-an-iot-edge-device"></a>Criar um dispositivo IoT Edge

Para ligar um dispositivo Azure IoT Edge a um hub IoT, criamos primeiro uma identidade para o dispositivo no centro. Pegamos na cadeia de ligação da identidade do dispositivo na nuvem e utilizamo-la para configurar o tempo de funcionamento do nosso dispositivo IoT Edge. Depois de um dispositivo configurado ligar-se ao hub, podemos implementar módulos e enviar mensagens. Também podemos alterar a configuração do dispositivo físico IoT Edge alterando a sua identidade de dispositivo correspondente no IoT Hub.

Para este tutorial, criamos a nova identidade do dispositivo utilizando o Código do Estúdio Visual. Também pode completar estes passos utilizando o portal Azure ou o Azure CLI.

1. Na sua máquina de desenvolvimento, abra o Código do Estúdio Visual.

1. Expanda o quadro **do Azure IoT Hub** a partir da vista Visual Studio Code **Explorer.**

1. Selecione a elipse e **selecione Criar dispositivo de borda IoT**.

1. Dê um nome ao dispositivo. Por conveniência, usamos o nome **aaTurbofanEdgeDevice** para que se esterdene ao topo dos dispositivos listados.

1. O novo dispositivo aparece na lista de dispositivos.

    ![Screenshot que mostra uma visão do dispositivo no Visual Studio Code Explorer.](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-an-azure-virtual-machine"></a>Implementar uma máquina virtual Azure

Utilizamos o [Azure IoT Edge na imagem Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) do Azure Marketplace para criar o nosso dispositivo IoT Edge para este tutorial. O Azure IoT Edge na imagem Ubuntu instala o mais recente tempo de funcionamento do IoT Edge e as suas dependências no arranque. Implementamos o VM utilizando:

- Um guião PowerShell, `Create-EdgeVM.ps1` .
- Um modelo de gestor de recursos Azure, `IoTEdgeVMTemplate.json` .
- Um guião de `install packages.sh` conchas, .

### <a name="enable-programmatic-deployment"></a>Ativar a implantação programática

Para utilizar a imagem do Azure Marketplace numa implementação escrita, precisamos de permitir a implementação programática para a imagem.

1. Inicie sessão no portal do Azure.

1. Selecione **Todos os serviços**.

1. Na barra de pesquisa, insira e selecione **Marketplace.**

1. Na barra de pesquisa marketplace, insira e selecione **Azure IoT Edge em Ubuntu**.

1. Selecione a hiperligação **Get start** para implementar programáticamente.

1. Selecione o botão **Ativar** e, em seguida, **selecione Guardar**.

    ![Screenshot que mostra a implementação programática ativa para uma máquina virtual.](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Verá uma notificação de sucesso.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Em seguida, execute o script para criar a máquina virtual para o seu dispositivo IoT Edge.

1. Abra uma janela PowerShell e vá ao **diretório edgeVM.**

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

1. Executar o script para criar a máquina virtual.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

1. Quando for solicitado, forneça valores para cada parâmetro. Para subscrição, grupo de recursos e localização, recomendamos que utilize os mesmos valores que tem para todos os recursos ao longo deste tutorial.

    * **ID de assinatura Azure**: Encontrado no portal Azure.
    * **Nome do Grupo de Recursos**: Nome memorável para agrupar os recursos para este tutorial.
    * **Localização**: Local azul onde a máquina virtual será criada. Por exemplo, westus2 ou northeurope. Para mais informações, consulte todas as [localizações do Azure.](https://azure.microsoft.com/global-infrastructure/locations/)
    * **AdminUsername**: O nome da conta de administração que utilizará para iniciar sedutil na máquina virtual.
    * **AdminPassword**: A palavra-passe a definir para o nome de utilizador de administração na máquina virtual.

1. Para que o script configurar o VM, inscreva-se no Azure com as credenciais associadas à subscrição Azure que está a usar.

1. O script confirma a informação para a criação do seu VM. Selecione **y** ou **Enter** para continuar.

1. O script é executado por vários minutos enquanto executa os seguintes passos:

    * Cria o grupo de recursos se já não existir
    * Cria a máquina virtual
    * Adiciona exceções NSG para o VM para as portas 22 (SSH), 5671 (AMQP), 5672 (AMPQ) e 443 (TLS)
    * Instala o [Azure CLI](/cli/azure/install-azure-cli-apt)

1. O script produz a cadeia de ligação SSH para a ligação ao VM. Copie a cadeia de ligação para o próximo passo.

    ![Screenshot que mostra a cópia da cadeia de ligação SSH para uma máquina virtual.](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Ligue-se ao seu dispositivo IoT Edge

As próximas secções configuram a máquina virtual Azure que criamos. O primeiro passo é ligar-se à máquina virtual.

1. Abra uma solicitação de comando e cole a cadeia de ligação SSH que copiou da saída do script. Introduza as suas próprias informações para o nome de utilizador, sufixo e região de acordo com os valores fornecidos ao script PowerShell na secção anterior.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

1. Quando lhe for solicitado que valide a autenticidade do anfitrião, insira **sim** e selecione **Enter**.

1. Quando for solicitado, forneça a sua senha.

1. Ubuntu exibe uma mensagem de boas-vindas, e então você deve ver um pedido como `<username>@<machinename>:~$` .

## <a name="download-key-vault-certificates"></a>Baixar certificados key vault

No início deste artigo, enviamos certificados para o Key Vault para os disponibilizar para o nosso dispositivo IoT Edge e para o nosso dispositivo de folha. O dispositivo de folha é um dispositivo a jusante que utiliza o dispositivo IoT Edge como porta de entrada para comunicar com o IoT Hub.

Trataremos do dispositivo de folha mais tarde no tutorial. Nesta secção, baixe os certificados para o dispositivo IoT Edge.

1. A partir da sessão SSH na máquina virtual Linux, inscreva-se em Azure com o Azure CLI.

    ```azurecli
    az login
    ```

1. Será solicitado que abra um navegador para uma página [de sposição de dispositivo microsoft](https://microsoft.com/devicelogin) e forneça um código único. Pode executar estes passos na sua máquina local. Feche a janela do navegador quando terminar de autenticar.

1. Quando autenticar com sucesso, o Linux VM iniciará seduções e listará as suas subscrições Azure.

1. Desconfie da subscrição Azure que pretende utilizar para comandos Azure CLI.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. Crie um diretório no VM para os certificados.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Faça o download dos certificados que guardou no cofre da chave: new-edge-device-full-chain.cert.pem, new-edge-device.key.pem, e azure-iot-test-only.root.ca.cert.pem.

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Atualize a configuração do dispositivo IoT Edge

O tempo de execução IoT Edge utiliza o ficheiro /etc/iotedge/config.yaml para persistir a sua configuração. Precisamos atualizar três peças de informação neste ficheiro:

* **Cadeia de ligação** do dispositivo : A cadeia de ligação da identidade deste dispositivo no Hub IoT
* **Certificados**: Os certificados a utilizar para ligações feitas com dispositivos a jusante
* **Nome de anfitrião**: O nome de domínio totalmente qualificado (FQDN) do dispositivo VM IoT Edge

O Azure IoT Edge na imagem Ubuntu que usamos para criar o IoT Edge VM vem com um script de concha que atualiza o ficheiro config.yaml com a cadeia de ligação.

1. No Código do Estúdio Visual, clique com o botão direito do dispositivo IoT Edge e, em seguida, selecione **a cadeia de ligação do dispositivo de cópia**.

    ![Screenshot que mostra a cópia da cadeia de ligação do Código do Estúdio Visual.](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

1. Na sua sessão SSH, executar o comando para atualizar o ficheiro config.yaml com a cadeia de ligação do dispositivo.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Em seguida, atualizaremos os certificados e o nome anfitrião editando diretamente o ficheiro config.yaml.

1. Abra o ficheiro config.yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Atualize a secção de certificados do ficheiro config.yaml removendo a liderança **#** e definindo o caminho para que o ficheiro pareça o seguinte exemplo:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Certifique-se de que os **certificados:** a linha não tem espaço branco anterior e que cada um dos certificados aninhados é recuado por dois espaços.

    Clicar à direita em nano colará o conteúdo da sua prancheta para a posição atual do cursor. Para substituir a corda, utilize as setas do teclado para ir até à cadeia que pretende substituir, eliminar a corda e, em seguida, clicar com o botão direito para colar a partir do tampão.

1. No portal Azure, vá à sua máquina virtual. Copie o nome DNS (FQDN da máquina) a partir da secção **visão geral.**

1. Cole o FQDN na secção de nome de anfitrião do ficheiro config.yml. Certifique-se de que o nome é minúsculo.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

1. Guarde e feche o ficheiro selecionando **Ctrl+X,** **Y** e **Enter**.

1. Reinicie o daemon IoT Edge.

    ```bash
    sudo systemctl restart iotedge
    ```

1. Verifique o estado do daemon IoT Edge. Depois do comando, entre **em :q** para sair.

    ```bash
    systemctl status iotedge
    ```

1. Se vir erros (texto colorido prefixado com " \[ ERROR \] ") no estado, examine os registos daemon para obter informações detalhadas sobre erros.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```
## <a name="clean-up-resources"></a>Limpar os recursos

Este tutorial faz parte de um conjunto onde cada artigo baseia-se no trabalho feito nos anteriores. Espere para limpar todos os recursos até completar o tutorial final.

## <a name="next-steps"></a>Passos seguintes

Acabamos de configurar um Azure VM como um gateway transparente IoT Edge. Começamos por gerar certificados de teste que enviamos para o Cofre chave. Em seguida, usamos um modelo de script e gestor de recursos para implementar o VM com o Ubuntu Server 16.04 LTS + Azure IoT Edge imagem de tempo de execução do Azure Marketplace. Com o VM a funcionar, ligámo-nos via SSH. Depois inscrevemo-nos no Azure e descarregámos certificados do Key Vault. Fizemos várias atualizações para a configuração do tempo de execução do IoT Edge atualizando o ficheiro config.yaml.

Continue até ao próximo artigo para construir módulos IoT Edge.

> [!div class="nextstepaction"]
> [Criar e implementar módulos IoT Edge personalizados](tutorial-machine-learning-edge-06-custom-modules.md)
