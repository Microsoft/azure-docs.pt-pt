---
title: 'Tutorial: Configure IoT Edge device - Machine Learning on Azure IoT Edge'
description: Neste tutorial, irá configurar uma Máquina Virtual Azure executando o Linux como um dispositivo Azure IoT Edge que funciona como uma porta de entrada transparente.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp
ms.openlocfilehash: 353ed321ce3b6161b28bf67d852a81f809880603
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81733017"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>Tutorial: Configure um dispositivo IoT Edge

> [!NOTE]
> Este artigo faz parte de uma série para um tutorial sobre a utilização de Machine Learning Azure em IoT Edge. Se chegou diretamente a este artigo, encorajamo-lo a começar com o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série para obter os melhores resultados.

Neste artigo, configuramos uma máquina virtual Azure que executa o Linux para ser um dispositivo IoT Edge que funciona como uma porta de entrada transparente. Uma configuração transparente de gateway permite que os dispositivos se conectem ao Hub Azure IoT através do portal sem saber que o portal existe. Ao mesmo tempo, um utilizador que interage com os dispositivos no Azure IoT Hub desconhece o dispositivo de gateway intermédio. Em última análise, adicionaremos análises de borda ao nosso sistema adicionando módulos IoT Edge à porta de entrada transparente.

Os passos neste artigo são normalmente realizados por um desenvolvedor de nuvem.

## <a name="create-certificates"></a>Criar certificados

Para que um dispositivo funcione como porta de entrada, tem de ser capaz de se ligar de forma segura a dispositivos a jusante. O Azure IoT Edge permite-lhe utilizar uma infraestrutura de chaves públicas (PKI) para estabelecer ligações seguras entre dispositivos. Neste caso, estamos a permitir que um dispositivo IoT a jusante se conecte a um dispositivo IoT Edge que atua como uma porta de entrada transparente. Para manter uma segurança razoável, o dispositivo a jusante deve confirmar a identidade do dispositivo IoT Edge. Para obter mais informações sobre como os dispositivos IoT Edge utilizam certificados, consulte os detalhes de utilização do [certificado Azure IoT Edge](iot-edge-certs.md).

Nesta secção, criamos os certificados auto-assinados usando uma imagem do Docker que depois construímos e executamos. Optámos por usar uma imagem do Docker para completar este passo porque reduz significativamente o número de passos necessários para criar os certificados na máquina de desenvolvimento do Windows. Consulte os [certificados de demonstração Create para testar as funcionalidades do dispositivo IoT Edge](how-to-create-test-certificates.md) para entender o que automatizamos com a imagem do Docker.

1. Inscreva-se no seu VM de desenvolvimento.

2. Crie uma nova pasta `c:\edgeCertificates`com o caminho e o nome.

3. Se ainda não estiver em funcionamento, inicie **o Docker para windows** a partir do menu Windows Start.

4. Abra o Visual Studio Code.

5. Selecione **File** > **Open Folder...** e escolha **C:\\\\\\fonte IoTEdgeAndMlSample CreateCertificates**.

6. No painel explorer, clique à direita no **dockerfile** e escolha **Build Image**.

7. No diálogo, aceite o valor predefinido para o nome de imagem e etiqueta: **criar certificados: mais recente**.

    ![Criar certificados no Código do Estúdio Visual](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

8. Espere que a construção termine.

    > [!NOTE]
    > Pode ver um aviso sobre uma chave pública desaparecida. É seguro ignorar este aviso. Da mesma forma, verá um aviso de segurança que recomenda verificar/repor permissões na sua imagem, o que é seguro ignorar para esta imagem.

9. Na janela do terminal Visual Studio Code, executar o recipiente de createcertificates.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. Docker vai pedir acesso ao **c:\\ ** unidade. Selecione **Partilhe-o**.

11. Forneça as suas credenciais quando solicitado.

12. Uma vez que o recipiente termine de funcionar, verifique se os seguintes ficheiros em **c:\\edgeCertificates:**

    * c:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * c:\\\\edgeCertificates\\novo-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Enviar certificados para cofre de chaves Azure

Para armazenar os nossos certificados de forma segura e para torná-los acessíveis a partir de vários dispositivos, enviaremos os certificados para o Cofre de Chaves Azure. Como pode ver na lista acima, temos dois tipos de ficheiros de certificado: PFX e PEM. Trataremos o PFX como certificados key vault para serem enviados para o Cofre chave. Os ficheiros PEM são texto simples e vamos tratá-los como segredos do Cofre Chave. Utilizaremos o Cofre chave associado ao espaço de trabalho Azure Machine Learning que criámos ao executar os [Cadernos Azure.](tutorial-machine-learning-edge-04-train-model.md#run-azure-notebooks)

1. A partir do [portal Azure,](https://portal.azure.com)navegue até ao seu espaço de trabalho azure machine learning.

2. A partir da página geral do espaço de trabalho Azure Machine Learning, encontre o nome do **Cofre chave**.

    ![Nome do cofre da chave de cópia](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. Na sua máquina de desenvolvimento, carregue os certificados para o Cofre chave. Substitua ** \<\> a subscriçãoId** e ** \<\> keyvaultname** com as suas informações sobre recursos.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. Se solicitado, inscreva-se no Azure.

5. O guião será executado por alguns minutos com saída que lista as novas entradas do Key Vault.

    ![Saída de script do cofre chave](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>Criar dispositivo do IoT Edge

Para ligar um dispositivo Azure IoT Edge a um hub IoT, criamos primeiro uma identidade para o dispositivo no centro. Pegamos na cadeia de ligação da identidade do dispositivo na nuvem e utilizamo-la para configurar o tempo de funcionamento no nosso dispositivo IoT Edge. Uma vez que um dispositivo configurado se conecta ao centro, somos capazes de implementar módulos e enviar mensagens. Também podemos alterar a configuração do dispositivo físico IoT Edge alterando a sua identidade de dispositivo correspondente no hub IoT.

Para este tutorial, criamos a nova identidade do dispositivo usando o Visual Studio Code. Também pode completar estes passos utilizando o [portal Azure](how-to-register-device.md#register-in-the-azure-portal), ou [Azure CLI](how-to-register-device.md#register-with-the-azure-cli).

1. Na sua máquina de desenvolvimento, abra o Código do Estúdio Visual.

2. Expanda a moldura **do Hub Azure IoT** a partir da vista do explorador do Código do Estúdio Visual.

3. Clique na elipse e selecione **Create IoT Edge Device**.

4. Dê um nome ao dispositivo. Por conveniência, utilizamos o nome **aaTurbofanEdgeDevice** para que se classificasse até ao topo dos dispositivos listados.

5. O novo dispositivo aparecerá na lista de dispositivos.

    ![Ver novo aaTurbofanEdgeDevice no explorador de código VS](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Implementar máquina virtual Azure

Utilizamos o [Azure IoT Edge na imagem ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) do Azure Marketplace para criar o nosso dispositivo IoT Edge para este tutorial. O Azure IoT Edge na imagem Ubuntu instala o mais recente tempo de execução do Azure IoT Edge e as suas dependências de arranque. Implementamos o VM usando `Create-EdgeVM.ps1`um script PowerShell; um modelo de `IoTEdgeVMTemplate.json`Gestor de Recursos, ; e um guião de concha, `install packages.sh`.

### <a name="enable-programmatic-deployment"></a>Ativar a implementação programática

Para utilizar a imagem do Marketplace numa implementação escrita, precisamos de permitir a implementação programática para a imagem.

1. Inicie sessão no Portal do Azure.

1. Selecione **Todos os serviços**.

1. Na barra de pesquisa, introduza e selecione **Marketplace**.

1. Na barra de pesquisa do Marketplace, introduza e selecione **Azure IoT Edge em Ubuntu**.

1. Selecione a hiperligação **iniciar** para implantar programáticamente.

1. Selecione o botão **Ativar** **e,** em seguida, guardar .

    ![Ativar a implementação programática para VM](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Verá uma notificação de sucesso.

### <a name="create-virtual-machine"></a>Criar a máquina virtual

Em seguida, execute o script para criar a máquina virtual para o seu dispositivo IoT Edge.

1. Abra uma janela PowerShell e navegue para o diretório **EdgeVM.**

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

2. Execute o script para criar a máquina virtual.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. Quando solicitado, forneça valores para cada parâmetro. Para subscrição, grupo de recursos e localização recomendamos que use o mesmo que tem para todos os recursos ao longo deste tutorial.

    * **Id de subscrição azure**: encontrado no portal Azure
    * **Nome do Grupo de Recursos**: nome memorável para agrupar os recursos para este tutorial
    * **Localização**: Localização azul onde a máquina virtual será criada. Por exemplo, westus2 ou norte da Europa. Para mais informações, consulte todas as [localizações do Azure.](https://azure.microsoft.com/global-infrastructure/locations/)
    * **AdminUsername**: o nome da conta de administração que utilizará para iniciar sessão na máquina virtual
    * **AdminPassword**: a palavra-passe para definir para o Nome De Utilizador De Administrador na máquina virtual

4. Para que o script possa configurar o VM, precisa de iniciar sessão no Azure com as credenciais associadas à Subscrição Azure que está a utilizar.

5. O guião confirma a informação para a criação do seu VM. Selecione **y** ou **Enter** para continuar.

6. O script funciona durante vários minutos enquanto executa os seguintes passos:

    * Crie o grupo de recursos se já não existir
    * Criar a máquina virtual
    * Adicione exceções ao INE para as portas 22 (SSH), 5671 (AMQP), 5672 (AMPQ) e 443 (TLS)
    * Instalar o [Azure CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)

7. O script produz a cadeia de ligação SSH para a ligação ao VM. Copie a corda de ligação para o próximo passo.

    ![Copiar linha de ligação SSH para VM](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Ligue-se ao seu dispositivo IoT Edge

As próximas várias secções configuram a máquina virtual Azure que criamos. O primeiro passo é ligar-se à máquina virtual.

1. Abra um pedido de comando e colhe a cadeia de ligação SSH que copiou da saída do script. Introduza as suas próprias informações para o nome de utilizador, sufixo e região de acordo com os valores fornecidos ao script PowerShell na secção anterior.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. Quando solicitado a validar a autenticidade do hospedeiro, escreva **sim** e selecione **Enter**.

3. Quando solicitado, forneça a sua senha.

4. Ubuntu exibe uma mensagem de boas-vindas `<username>@<machinename>:~$`e, em seguida, você deve ver um pedido como .

## <a name="download-key-vault-certificates"></a>Baixar certificados key vault

No início deste artigo, enviámos certificados para o Key Vault para os disponibilizar para o nosso dispositivo IoT Edge e para o nosso dispositivo de folhas. O dispositivo de folha é um dispositivo a jusante que utiliza o dispositivo IoT Edge como porta de entrada para comunicar com o IoT Hub.

Trataremos do dispositivo de folhamais tarde no tutorial. Nesta secção, descarregue os certificados para o dispositivo IoT Edge.

1. A partir da sessão SSH na máquina virtual Linux, inscreva-se no Azure com o Azure CLI.

    ```azurecli
    az login
    ```

1. Será solicitado a abrir um <https://microsoft.com/devicelogin> navegador e fornecer um código único. Pode executar estes passos na sua máquina local. Feche a janela do navegador quando terminar a autenticação.

1. Quando autenticar com sucesso, o Linux VM iniciará o seu insessão e listará as suas subscrições Azure.

1. Detete a subscrição Azure que pretende utilizar para comandos Azure CLI.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. Crie um diretório no VM para os certificados.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Descarregue os certificados que guardou no cofre chave: new-edge-device-full-chain.cert.pem, new-edge-device.key.pem, e azure-iot-test-only.root.ca.cert.pem

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Atualizar a configuração do dispositivo IoT Edge

O tempo de execução do `/etc/iotedge/config.yaml` IoT Edge utiliza o ficheiro para persistir na sua configuração. Precisamos atualizar três informações neste ficheiro:

* **Cadeia de ligação**do dispositivo : a cadeia de ligação da identidade deste dispositivo no IoT Hub
* **Certificados:** os certificados a utilizar para ligações efetuadas com dispositivos a jusante
* **Nome de anfitrião:** o nome de domínio totalmente qualificado (FQDN) do dispositivo VM IoT Edge.

O *Azure IoT Edge na imagem de Ubuntu* que usamos para criar o IoT Edge VM vem com um script de concha que atualiza o config.yaml com a cadeia de ligação.

1. No Visual Studio Code clique corretamente no dispositivo IoT Edge e, em seguida, selecione **Copy Device Connection String**.

    ![Linha de ligação de cópia do Código do Estúdio Visual](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. Na sua sessão SSH, faça o comando para atualizar o ficheiro config.yaml com a corda de ligação do dispositivo.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Em seguida, atualizaremos os certificados e o nome de anfitrião editando diretamente o config.yaml.

1. Abra o ficheiro config.yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. Atualize a secção de certificados do config.yaml removendo o líder `#` e definindo o caminho de modo que o ficheiro se pareça com o seguinte exemplo:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Certifique-se de que os **certificados:** a linha não tem espaço branco anterior e que cada um dos certificados aninhados é retalhado por dois espaços.

    O clique direito no nano colará o conteúdo da sua prancheta à posição do cursor atual. Para substituir a corda, utilize as setas do teclado para navegar na corda que pretende substituir, elimine a corda e, em seguida, clique à direita para colar a partir do tampão.

3. No portal Azure, navegue para a sua máquina virtual. Copie o nome DNS (FQDN da máquina) da secção **Devisão Geral.**

4. Colá-lo na secção de nome de anfitrião do config.yml. Certifique-se de que o nome é todo minúsculo.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. Guarde e feche`Ctrl + X` `Y`o `Enter`ficheiro ( . . ).

6. Reinicie o daemon iotedge.

    ```bash
    sudo systemctl restart iotedge
    ```

7. Verifique o estado do Daemon IoT Edge (após o comando, escreva ":q" para sair).

    ```bash
    systemctl status iotedge
    ```

8. Se vir erros (texto colorido\[pré-fixado com " ERROR\]") no estado Examine os registos daemon para obter informações detalhadas sobre erros.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```

## <a name="next-steps"></a>Passos seguintes

Acabamos de configurar um Azure VM como Gateway Transparente Azure IoT Edge. Começamos por gerar certificados de teste que enviamos para o Cofre chave Azure. Em seguida, usamos um modelo de script e gestor de recursos para implementar o VM com a imagem "Ubuntu Server 16.04 LTS + Azure IoT Edge runtime" do Azure Marketplace. Com o VM em funcionamento, conectamos via SSH, inscrevio no Azure e descarregámos certificados da Key Vault. Fizemos várias atualizações para a configuração do Tempo de Execução de Borda IoT, atualizando o ficheiro config.yaml.

Para mais informações, consulte [como um dispositivo IoT Edge pode ser usado como um portal](iot-edge-as-gateway.md) e [configurar um dispositivo IoT Edge para funcionar como um portal transparente](how-to-create-transparent-gateway.md).

Continue para o próximo artigo para construir módulos IoT Edge.

> [!div class="nextstepaction"]
> [Criar e implementar módulos IoT Edge personalizados](tutorial-machine-learning-edge-06-custom-modules.md)
