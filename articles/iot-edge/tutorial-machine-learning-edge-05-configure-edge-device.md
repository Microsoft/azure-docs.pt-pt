---
title: 'Tutorial: Dispositivo Configure IoT Edge - Machine Learning on Azure IoT Edge'
description: Neste tutorial, irá configurar uma Máquina Virtual Azure que executa o Linux como um dispositivo Azure IoT Edge que funciona como uma porta de entrada transparente.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, devx-track-azurecli
ms.openlocfilehash: b85984207742e0b8991ab65875dd22505b918185
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92736742"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>Tutorial: Configurar um dispositivo IoT Edge

> [!NOTE]
> Este artigo faz parte de uma série para um tutorial sobre a utilização de Azure Machine Learning em IoT Edge. Se você chegou a este artigo diretamente, nós o encorajamos a começar com o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série para os melhores resultados.

Neste artigo, configuramos uma máquina virtual Azure que executa o Linux para ser um dispositivo IoT Edge que funciona como uma porta de entrada transparente. Uma configuração transparente do gateway permite que os dispositivos se conectem ao Azure IoT Hub através do gateway sem saber que o gateway existe. Ao mesmo tempo, um utilizador que interage com os dispositivos no Azure IoT Hub desconhece o dispositivo de gateway intermédio. Em última análise, adicionaremos análises de bordas ao nosso sistema adicionando módulos IoT Edge ao gateway transparente.

Os passos deste artigo são normalmente realizados por um desenvolvedor de nuvem.

## <a name="create-certificates"></a>Criar certificados

Para que um dispositivo funcione como um gateway, tem de ser capaz de se ligar de forma segura aos dispositivos a jusante. O Azure IoT Edge permite-lhe utilizar uma infraestrutura de chaves públicas (PKI) para estabelecer ligações seguras entre dispositivos. Neste caso, estamos a permitir que um dispositivo IoT a jusante se conecte a um dispositivo IoT Edge agindo como um gateway transparente. Para manter uma segurança razoável, o dispositivo a jusante deve confirmar a identidade do dispositivo IoT Edge. Para obter mais informações sobre como os dispositivos IoT Edge utilizam certificados, consulte [os detalhes de utilização do certificado Azure IoT Edge](iot-edge-certs.md).

Nesta secção, criamos os certificados auto-assinados usando uma imagem docker que depois construímos e corremos. Escolhemos usar uma imagem docker para completar este passo porque reduz significativamente o número de passos necessários para criar os certificados na máquina de desenvolvimento do Windows. Consulte [Os certificados de demonstração criar para testar as funcionalidades do dispositivo IoT Edge](how-to-create-test-certificates.md) para entender o que automatizamos com a imagem do Docker.

1. Inscreva-se no seu VM de desenvolvimento.

2. Criar uma nova pasta com o caminho e o nome `c:\edgeCertificates` .

3. Se ainda não estiver em execução, inicie **o Docker para** windows a partir do menu Windows Start.

4. Abra o Visual Studio Code.

5. Selecione Pasta Aberta **de**  >  **Ficheiros...** e escolha **C: \\ fonte \\ IoTEdgeAndMlSample \\ CreateCertificates** .

6. No painel Explorer, clique à direita no **estivador** e escolha **a Imagem de Construção.**

7. No diálogo, aceite o valor predefinido para o nome de imagem e etiqueta: **criar certificados: mais recente** .

    ![Criar certificados em Código de Estúdio Visual](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

8. Espere que a construção termine.

    > [!NOTE]
    > Pode ver um aviso sobre uma chave pública desaparecida. É seguro ignorar este aviso. Da mesma forma, verá um aviso de segurança que recomenda que verifique/repõe permissões na sua imagem, que é seguro ignorar para esta imagem.

9. Na janela do terminal Visual Studio Code, verifique o recipiente de certificação de criação.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. Docker pedirá acesso ao **c: \\** unidade. **Selecione Partilhe-o** .

11. Forneça as suas credenciais quando solicitado.

12. Uma vez que o recipiente termine de funcionar, verifique se há os seguintes ficheiros em **c: \\ edgeCertificates** :

    * c: \\ edgeCertificates \\ certes \\ azure-iot-test-only.root.ca.cert.pem
    * c: \\ edgeCertificates \\ certifica \\ new-edge-device-full-chain.cert.pem
    * c: \\ edgeCertificates \\ certifica \\ novo-dispositivo de borda.cert.pem
    * c: \\ edgeCertificates \\ certifica \\ new-edge-device.cert.pfx
    * c: \\ edgeCertifica \\ o dispositivo privado \\ new-edge.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Faça upload de certificados para Azure Key Vault

Para armazenar os nossos certificados de forma segura e torná-los acessíveis a partir de vários dispositivos, enviaremos os certificados para o Cofre da Chave Azure. Como pode ver na lista acima, temos dois tipos de ficheiros de certificado: PFX e PEM. Trataremos o PFX como certificados do Cofre-Chave para serem enviados para o Cofre de Chaves. Os ficheiros PEM são texto simples e vamos tratá-los como segredos do Cofre chave. Utilizaremos o Cofre-Chave associado ao espaço de trabalho Azure Machine Learning que criámos executando os [Cadernos Azure.](tutorial-machine-learning-edge-04-train-model.md#run-azure-notebooks)

1. A partir do [portal Azure,](https://portal.azure.com)navegue até ao seu espaço de trabalho Azure Machine Learning.

2. A partir da página geral do espaço de trabalho Azure Machine Learning, encontre o nome do **Cofre-Chave** .

    ![Copiar nome do cofre da chave](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. Na sua máquina de desenvolvimento, faça o upload dos certificados para Key Vault. Substitua **\<subscriptionId\>** e **\<keyvaultname\>** pela informação dos seus recursos.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. Se solicitado, inscreva-se em Azure.

5. O script será executado por alguns minutos com a saída que lista as novas entradas do Key Vault.

    ![Saída do script do cofre de chaves](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>Criar dispositivo do IoT Edge

Para ligar um dispositivo Azure IoT Edge a um hub IoT, criamos primeiro uma identidade para o dispositivo no centro. Pegamos na cadeia de ligação da identidade do dispositivo na nuvem e utilizamo-la para configurar o tempo de funcionamento do nosso dispositivo IoT Edge. Uma vez que um dispositivo configurado se conecta ao hub, somos capazes de implementar módulos e enviar mensagens. Também podemos alterar a configuração do dispositivo físico IoT Edge alterando a sua identidade de dispositivo correspondente no hub IoT.

Para este tutorial, criamos a nova identidade do dispositivo utilizando o Código do Estúdio Visual. Também pode completar estes passos utilizando o portal Azure ou o Azure CLI.

1. Na sua máquina de desenvolvimento, abra o Código do Estúdio Visual.

2. Expanda o quadro **do Hub Azure IoT** a partir da vista do explorador do Código do Estúdio Visual.

3. Clique na elipse e selecione **Criar dispositivo de borda IoT** .

4. Dê um nome ao dispositivo. Por conveniência, usamos o nome **aaTurbofanEdgeDevice** para que se se assemeta ao topo dos dispositivos listados.

5. O novo dispositivo aparecerá na lista de dispositivos.

    ![Ver novo aaTurbofanEdgeDevice no vs Code explorer](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Implementar máquina virtual Azure

Utilizamos o [Azure IoT Edge na imagem Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) do Azure Marketplace para criar o nosso dispositivo IoT Edge para este tutorial. O Azure IoT Edge na imagem Ubuntu instala o mais recente tempo de funcionamento do Azure IoT Edge e as suas dependências no arranque. Implementamos o VM utilizando um script PowerShell, `Create-EdgeVM.ps1` ; um modelo de Gestor de Recursos, e um script de `IoTEdgeVMTemplate.json` concha, `install packages.sh` .

### <a name="enable-programmatic-deployment"></a>Ativar a implantação programática

Para utilizar a imagem do Marketplace numa implementação escrita, precisamos de permitir a implementação programática para a imagem.

1. Inicie sessão no portal do Azure.

1. Selecione **Todos os serviços** .

1. Na barra de pesquisa, insira e selecione **Marketplace.**

1. Na barra de pesquisa marketplace, insira e selecione **Azure IoT Edge em Ubuntu** .

1. Selecione a hiperligação **Get start** para implementar programáticamente.

1. Selecione o botão **Ativar** e, em seguida, **Guarde** .

    ![Ativar a implementação programática para VM](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Verá uma notificação de sucesso.

### <a name="create-virtual-machine"></a>Criar a máquina virtual

Em seguida, execute o script para criar a máquina virtual para o seu dispositivo IoT Edge.

1. Abra uma janela PowerShell e navegue para o **diretório EdgeVM.**

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

2. Executar o script para criar a máquina virtual.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. Quando solicitado, forneça valores para cada parâmetro. Para subscrição, grupo de recursos e localização recomendamos que use o mesmo que tem para todos os recursos ao longo deste tutorial.

    * **ID de assinatura Azure:** encontrado no portal Azure
    * **Nome do Grupo de Recursos** : nome memorável para agrupar os recursos para este tutorial
    * **Localização** : Local azul onde a máquina virtual será criada. Por exemplo, westus2 ou northeurope. Para mais informações, consulte todas as [localizações do Azure.](https://azure.microsoft.com/global-infrastructure/locations/)
    * **AdminUsername** : o nome da conta de administração que utilizará para iniciar sedutil na máquina virtual
    * **AdminPassword** : a palavra-passe a definir para o AdminUsername na máquina virtual

4. Para que o script possa configurar o VM, tem de iniciar seduca no Azure com as credenciais associadas à Subscrição Azure que está a utilizar.

5. O script confirma a informação para a criação do seu VM. Selecione **y** ou **Enter** para continuar.

6. O script é executado por vários minutos enquanto executa os seguintes passos:

    * Crie o grupo de recursos se já não existir
    * Criar a máquina virtual
    * Adicione exceções NSG para o VM para as portas 22 (SSH), 5671 (AMQP), 5672 (AMPQ) e 443 (TLS)
    * Instale o [Azure CLI](/cli/azure/install-azure-cli-apt)

7. O script produz a cadeia de ligação SSH para a ligação ao VM. Copie a cadeia de ligação para o próximo passo.

    ![Cadeia de ligação SSH de cópia para VM](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Ligue-se ao seu dispositivo IoT Edge

As próximas secções configuram a máquina virtual Azure que criamos. O primeiro passo é ligar-se à máquina virtual.

1. Abra uma solicitação de comando e cole a cadeia de ligação SSH copiada da saída do script. Introduza as suas próprias informações para o nome de utilizador, sufixo e região de acordo com os valores fornecidos ao script PowerShell na secção anterior.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. Quando solicitado para validar a autenticidade do anfitrião, escreva **sim** e selecione **Enter** .

3. Quando solicitado, forneça a sua senha.

4. Ubuntu exibe uma mensagem de boas-vindas e, em seguida, deve ver um pedido como `<username>@<machinename>:~$` .

## <a name="download-key-vault-certificates"></a>Baixar certificados key vault

No início deste artigo, enviamos certificados para o Key Vault para os disponibilizar para o nosso dispositivo IoT Edge e para o nosso dispositivo de folha. O dispositivo de folha é um dispositivo a jusante que utiliza o dispositivo IoT Edge como porta de entrada para comunicar com o IoT Hub.

Trataremos do dispositivo de folha mais tarde no tutorial. Nesta secção, baixe os certificados para o dispositivo IoT Edge.

1. A partir da sessão SSH na máquina virtual Linux, inscreva-se em Azure com o Azure CLI.

    ```azurecli
    az login
    ```

1. Será solicitado que abra um navegador <https://microsoft.com/devicelogin> e forneça um código único. Pode executar estes passos na sua máquina local. Feche a janela do navegador quando terminar de autenticar.

1. Quando autenticar com sucesso, o Linux VM iniciará seduções e listará as suas subscrições Azure.

1. Desconfie da subscrição Azure que pretende utilizar para comandos Azure CLI.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. Crie um diretório no VM para os certificados.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Faça o download dos certificados que guardou no cofre da chave: new-edge-device-full-chain.cert.pem, new-edge-device.key.pem, e azure-iot-test-only.root.ca.cert.pem

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Atualize a configuração do dispositivo IoT Edge

O tempo de execução IoT Edge utiliza o ficheiro `/etc/iotedge/config.yaml` para persistir na sua configuração. Precisamos atualizar três peças de informação neste ficheiro:

* **Cadeia de ligação** do dispositivo : a cadeia de ligação da identidade deste dispositivo no IoT Hub
* **Certificados:** os certificados a utilizar para ligações efetuadas com dispositivos a jusante
* **Nome hospedeiro:** o nome de domínio totalmente qualificado (FQDN) do dispositivo VM IoT Edge.

O *Azure IoT Edge na imagem Ubuntu* que usamos para criar o IoT Edge VM vem com um script de concha que atualiza o config.yaml com a cadeia de ligação.

1. No Código do Estúdio Visual clique corretamente no dispositivo IoT Edge e, em seguida, selecione **a cadeia de ligação do dispositivo de cópia** .

    ![Cadeia de ligação de cópia do Código do Estúdio Visual](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. Na sua sessão SSH, executar o comando para atualizar o ficheiro config.yaml com a cadeia de ligação do dispositivo.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Em seguida, atualizaremos os certificados e o nome anfitrião editando diretamente o config.yaml.

1. Abra o ficheiro config.yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. Atualize a secção de certificados do config.yaml removendo o líder `#` e definindo o caminho para que o ficheiro pareça o seguinte exemplo:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Certifique-se de que os **certificados:** a linha não tem espaço branco anterior e que cada um dos certificados aninhados é recuado por dois espaços.

    Clicar à direita em nano colará o conteúdo da sua prancheta para a posição atual do cursor. Para substituir a corda, utilize as setas do teclado para navegar para a cadeia que pretende substituir, elimine a corda e, em seguida, clique com o botão direito para colar a partir do tampão.

3. No portal Azure, navegue para a sua máquina virtual. Copie o nome DNS (FQDN da máquina) a partir da secção **visão geral.**

4. Cole o FQDN na secção de nome de anfitrião do config.yml. Certifique-se de que o nome é minúsculo.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. Guarde e feche o ficheiro ( `Ctrl + X` , `Y` , , `Enter` .

6. Reinicie o daemon iotedge.

    ```bash
    sudo systemctl restart iotedge
    ```

7. Verifique o estado do IoT Edge Daemon (após o comando, escreva ":q" para sair).

    ```bash
    systemctl status iotedge
    ```

8. Se vir erros (texto colorido prefixado com " \[ ERROR \] ") no estado Examine os registos do daemon para obter informações detalhadas sobre erros.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```
## <a name="next-steps"></a>Passos seguintes

Acabamos de configurar um Azure VM como Azure IoT Edge Transparent Gateway. Começamos por gerar certificados de teste que enviamos para o Cofre da Chave Azure. Em seguida, usamos um modelo de script e gestor de recursos para implementar o VM com a imagem "Ubuntu Server 16.04 LTS + Azure IoT Edge runtime" do Mercado Azure. Com o VM em funcionamento, ligámos via SSH, assinámos no Azure e descarregamos certificados do Key Vault. Fizemos várias atualizações para a configuração do IoT Edge Runtime atualizando o ficheiro config.yaml.

Continue até ao próximo artigo para construir módulos IoT Edge.

> [!div class="nextstepaction"]
> [Criar e implementar módulos IoT Edge personalizados](tutorial-machine-learning-edge-06-custom-modules.md)
