---
title: Configurar o dispositivo do IoT Edge - Machine Learning no Azure IoT Edge | Documentos da Microsoft
description: Configure uma Máquina Virtual com o Linux como um dispositivo Azure IoT Edge que atue como gateway transparente.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a2096004a7b389f627c528a8dfb4768ac001f390
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155631"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>Tutorial: Configurar um dispositivo IoT Edge

> [!NOTE]
> Este artigo faz parte de uma série de para um tutorial sobre como utilizar o Azure Machine Learning do IoT Edge. Se o ter chegado neste artigo diretamente, é recomendável que começa com o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série para obter melhores resultados.

Neste artigo, vamos configurar uma Máquina Virtual em execução no Linux para ser um dispositivo Azure IoT Edge que atue como gateway transparente. A configuração do gateway transparente permite que os dispositivos ligar ao IoT Hub do Azure através do gateway sem saber o gateway existente. Ao mesmo tempo, um usuário interagir com os dispositivos no IoT Hub não tem do dispositivo de gateway intermédio. Por fim, usamos o gateway transparente para adicionar análises avançadas em nosso sistema ao adicionar módulos do IoT Edge para o gateway.

Os passos neste artigo são, normalmente realizados por um desenvolvedor de cloud.

## <a name="generate-certificates"></a>Gerar certificados

Para um dispositivo para funcionar como um gateway, ele precisa ser capaz de se liguem a dispositivos de downstream. O Azure IoT Edge permite-lhe utilizar uma infraestrutura de chaves públicas (PKI) para configurar ligações seguras entre dispositivos. Neste caso, estamos está a permitir que um dispositivo downstream ligar a um dispositivo IoT Edge que atua como um gateway transparente. Para manter a segurança razoável, o dispositivo de downstream deve confirmar a identidade do dispositivo IoT Edge. Para obter mais informações sobre como os dispositivos do IoT Edge utilizam certificados, consulte [detalhes de utilização de certificados do Azure IoT Edge](iot-edge-certs.md).

Nesta secção, vamos criar os certificados autoassinados com uma imagem de Docker que, em seguida, criar e executar. Podemos optar por utilizar uma imagem do Docker para concluir este passo de como ele reduzido significativamente o número de passos necessários para criar os certificados na máquina de desenvolvimento Windows. Ver [gerar certificados com o Windows](how-to-create-transparent-gateway.md#generate-certificates-with-windows) para obter os detalhes sobre como gerar os certificados sem utilizar um contentor. [Gerar certificados com o Linux](how-to-create-transparent-gateway.md#generate-certificates-with-linux) tem o conjunto de instruções que podemos automatizados com a imagem do Docker.

1. Inicie sessão na sua máquina de virtual de desenvolvimento.

2. Abra uma linha de comandos e execute o seguinte comando para criar um diretório na VM.

    ```cmd
    mkdir c:\edgeCertificates
    ```

3. Inicie **Docker para Windows** do menu Iniciar do Windows.

4. Abra o Visual Studio Code.

5. Selecione **arquivo** > **Abrir pasta...**  e escolha **c:\\origem\\IoTEdgeAndMlSample\\CreateCertificates**.

6. Com o botão direito no dockerfile e escolha **criar imagem**.

7. Na caixa de diálogo, aceite o valor predefinido para o nome da imagem e a etiqueta: **createcertificates:latest**.

8. Aguarde que a compilação concluir.

    > [!NOTE]
    > Poderá ver um aviso para sobre uma chave pública em falta. É seguro ignorar este aviso. Da mesma forma, verá um aviso de segurança que recomenda que verificação/reset permissões em sua imagem, que é seguro ignorar para esta imagem.

9. Na janela de terminal do Visual Studio Code, execute o contentor de createcertificates.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. Docker solicitará acesso para o **c:\\**  unidade. Selecione **partilhá-lo**.

11. Forneça as credenciais quando lhe for pedido.

12. Uma vez a contentor estiver concluída em execução, a verificação para os seguintes ficheiros no **c:\\edgeCertificates**:

    * c:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * c:\\edgeCertificates\\private\\new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Carregar certificados para o Azure Key Vault

Para armazenar nosso certificados de forma segura e torná-los acessíveis a partir de vários dispositivos, irá carregar os certificados no Azure Key Vault. Como pode ver na lista anterior, temos dois tipos de ficheiros de certificado: PFX e PEM. Podemos tratará o PFX como certificados do Key Vault para ser carregado para o Cofre de chaves. Os ficheiros PEM são texto simples e irão tratar como segredos do Cofre de chave. Nós usaremos o Cofre de chaves associadas com a área de trabalho de serviço do Azure Machine Learning que criou ao executar o [blocos de notas do Azure](tutorial-machine-learning-edge-04-train-model.md#run-azure-notebooks).

1. Partir do [portal do Azure](https://portal.azure.com), navegue até à sua área de trabalho do serviço do Azure Machine Learning.

2. Na página Descrição geral da área de trabalho de serviço do Azure Machine Learning, encontrar o nome da **Key Vault**.

    ![Copie o nome do Cofre de chaves](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. No computador de desenvolvimento, carregue os certificados para o Key Vault. Substitua **\<subscriptionId\>** e **\<keyvaultname\>** com as suas informações de recursos.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. Se lhe for pedido, inicie sessão no Azure.

5. O script será executado durante alguns minutos, com a saída que lista as novas entradas de Key Vault.

    ![Saída de script do Key Vault](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>Criar dispositivo do IoT Edge

Para ligar um dispositivo Azure IoT Edge para um hub IoT, vamos primeiro criar uma identidade para o dispositivo no hub. Pegamos a cadeia de ligação da identidade de dispositivo na cloud e utilizá-lo para configurar o tempo de execução no nosso dispositivo IoT Edge. Depois do dispositivo foi configurado e liga-se para o hub, é possível implementar módulos e enviar mensagens. Também podemos alterar a configuração do dispositivo físico do IoT Edge ao alterar a configuração da identidade de dispositivo correspondente no IoT hub.

Para este tutorial, vamos criar a nova identidade de dispositivo com o Visual Studio Code. Também pode concluir estes passos com o [portal do Azure](how-to-register-device-portal.md), ou [CLI do Azure](how-to-register-device-cli.md).

1. No computador de desenvolvimento, abra o Visual Studio Code.

2. Abra o **dispositivos do IoT Hub do Azure** quadro da vista do Explorador do Visual Studio Code.

3. Clique no botão de reticências e selecione **criar dispositivo do IoT Edge**.

4. Dê um nome de dispositivo. Para sua comodidade, utilizamos **aaTurbofanEdgeDevice** para ordena à frente de todos os dispositivos de cliente criado anteriormente por meio do agente de dispositivo para enviar os dados de teste.

5. O novo dispositivo irá aparecer na lista de dispositivos.

    ![Vista aaTurbofanEdgeDevice novo no Explorador do VS Code](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Implementar a Máquina Virtual do Azure

Vamos utilizar o [do Azure IoT Edge no Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) imagem no Azure Marketplace para criar nosso dispositivo IoT Edge para este tutorial. O Azure IoT Edge numa imagem de Ubuntu instala o tempo de execução mais recente do Azure IoT Edge e as respetivas dependências na inicialização. Podemos implementar a VM com um script do PowerShell `Create-EdgeVM.ps1`; um modelo do Resource Manager `IoTEdgeVMTemplate.json`; e um script de shell, `install packages.sh`.

### <a name="enable-programmatic-deployment"></a>Ativar a implementação programática

Para utilizar a imagem do marketplace numa implantação com script, é necessário ativar a implementação programática para a imagem.

1. Inicie sessão no Portal do Azure.

1. Selecione **Todos os serviços**.

1. Na barra de pesquisa, introduza e selecione **Marketplace**.

1. Na barra de pesquisa, introduza e selecione **do Azure IoT Edge no Ubuntu**.

1. Selecione o **pretende implementar através de programação? Introdução ao** hyperlink.

1. Selecione o **habilitar** botão, em seguida, **guardar**.

    ![Ativar a implementação programática para VM](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Verá uma notificação de êxito.

### <a name="create-virtual-machine"></a>Criar a máquina virtual

Em seguida, execute o script para criar a máquina virtual para o seu dispositivo IoT Edge.

1. Abra uma janela do PowerShell e navegue para o **EdgeVM** diretório.

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

2. Execute o script para criar a máquina virtual.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. Quando lhe for pedido, forneça valores para cada parâmetro. Para a subscrição, grupo de recursos e localização, recomendamos que utilizam o mesmo que tenha para todos os recursos neste tutorial.

    * **ID de subscrição do Azure**: foi encontrado no portal do Azure
    * **Nome do grupo de recursos**: nome fácil de memorizar para agrupar os recursos para este tutorial
    * **Localização**: Localização do Azure onde será criada a máquina virtual. Por exemplo, westus2 ou northeurope. Para obter mais informações, ver todos [localizações do Azure](https://azure.microsoft.com/global-infrastructure/locations/).
    * **AdminUsername**: o nome para a conta de administrador irá utilizar para iniciar sessão para a máquina virtual
    * **AdminPassword**: a palavra-passe para definir para o AdminUsername na máquina virtual

4. Para o script poder configurar a VM, terá de iniciar sessão no Azure com as credenciais associadas com a subscrição do Azure que está a utilizar.

5. O script confirma as informações para a criação da sua VM. Selecione **y** ou **Enter** para continuar.

6. O script é executado durante vários minutos, pois ele executa os seguintes passos:

    * Criar o grupo de recursos, caso ainda não exista
    * Criar a máquina virtual
    * Adicionar exceções de NSG para a VM para as portas 22 (SSH), 5671 (AMQP), 5672 (AMPQ) e 443 (SSL)
    * Instalar o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest))

7. O script produz a cadeia de ligação de SSH para ligar à VM. Copie a cadeia de ligação para a próxima etapa.

    ![Copie a cadeia de ligação de SSH para a VM](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Ligar ao seu dispositivo IoT Edge

As próximas seções vários configurar a máquina virtual do Azure que criar. O primeiro passo é ligar à máquina virtual.

1. Abra um prompt de comando e cole a cadeia de ligação de SSH que copiou da saída do script. Introduza as suas próprias informações de nome de utilizador, sufixo e região, de acordo com os valores fornecidos para o script do PowerShell na secção anterior.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. Quando lhe for pedido para validar a autenticidade do anfitrião, escreva **Sim** e selecione **Enter**.

3. Quando lhe for pedido, forneça a palavra-passe.

4. Ubuntu exibe uma mensagem de boas-vindas e, em seguida, deverá ver um aviso semelhante `<username>@<machinename>:~$`.

## <a name="download-key-vault-certificates"></a>Transferir certificados do Key Vault

No início deste artigo, nós carregamos certificados para o Key Vault para que fiquem disponíveis para nosso dispositivo IoT Edge e o nosso dispositivo de folha, o que é um dispositivo de downstream que utiliza o dispositivo do IoT Edge como um gateway para comunicar com IoT Hub. Será lidamos com o dispositivo de folha mais tarde no tutorial. Nesta secção, baixe os certificados no dispositivo IoT Edge.

1. A partir da sessão SSH na máquina virtual do Linux, inicie sessão no Azure com a CLI do Azure.

    ```bash
    az login
    ```

1. Será solicitado para abrir um browser para <https://microsoft.com/devicelogin> e fornecer um código único. Pode executar estes passos no seu computador local. Feche a janela do browser quando concluir a autenticação.

1. Quando se autenticar com êxito, a VM do Linux irá iniciar sessão e listar as subscrições do Azure.

1. ASet a subscrição do Azure que pretende utilizar para comandos da CLI do Azure.

    ```bash
    az account set --subscription <subscriptionId>
    ```

1. Crie um diretório na VM para os certificados.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Transferir os certificados armazenados no Cofre de chaves: nova-edge-dispositivo-full-chain.cert.pem e do azure-iot-teste-only.root.ca.cert.pem device.key.pem novo edge

    ```bash
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Atualizar a configuração do dispositivo IoT Edge

O runtime do IoT Edge utiliza /etc/iotedge/config.yaml o ficheiro para manter a respetiva configuração. Precisamos de três partes de informações existentes neste ficheiro de atualização:

* **Cadeia de ligação do dispositivo**: a cadeia de ligação de identidade neste dispositivo no IoT Hub
* **Certificados:** os certificados a utilizar para ligações efetuadas com dispositivos jusante
* **Nome do anfitrião:** o nome de domínio completamente qualificado (FQDN) do dispositivo IoT Edge da VM.

O *do Azure IoT Edge no Ubuntu* imagem que usamos para criar a VM do IoT Edge é fornecido com um script de shell que atualiza o config.yaml com a cadeia de ligação.

1. No Visual Studio Code com o botão direito no dispositivo IoT Edge, em seguida, selecione **cadeia de ligação do dispositivo de cópia**.

    ![Copie a cadeia de ligação do Visual Studio Code](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. Na sua sessão SSH, execute o comando para atualizar o ficheiro de config.yaml com a cadeia de ligação do dispositivo.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Em seguida, atualizaremos os certificados e o nome de anfitrião editando diretamente o config.yaml.

1. Abra o ficheiro de config.yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. Atualize a secção de certificados do config.yaml removendo a líderes `#` e definindo o caminho para o arquivo será semelhante ao seguinte exemplo:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Certifique-se de que o "certificados:" não tem nenhum anterior espaço em branco e que cada um dos certificados é precedida por dois espaços.

    Clicar com o botão direito no nano irá colar o conteúdo de sua área de transferência para a posição atual do cursor. Para substituir a cadeia de caracteres, utilize as setas do teclado para navegar para a cadeia de caracteres que pretende substituir, eliminar a cadeia de caracteres, em seguida, com o botão direito para colar do buffer.

3. No portal do Azure, navegue para a máquina virtual. Copiar o nome DNS (FQDN da máquina) a partir da **descrição geral** secção.

4. Cole o FQDN na secção de nome de anfitrião do config.yml. Certifique-se de que o nome é todo em minúsculas.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. Guarde e feche o ficheiro (`Ctrl + X`, `Y`, `Enter`).

6. Reinicie o daemon de iotedge.

    ```bash
    sudo systemctl restart iotedge
    ```

7. Verificar o estado de que o Daemon do IoT Edge (após o comando, digite ": p" para sair).

    ```bash
    systemctl status iotedge
    ```

8. Se vir erros (colorido texto com o prefixo "\[erro\]") nos registos de daemon de Examine estado para obter informações de erro detalhada.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```

## <a name="next-steps"></a>Passos Seguintes

Podemos acabou de concluir a configuração de uma VM do Azure como Gateway transparente do Azure IoT Edge. Começamos através da geração de certificados de teste, o que nós carregamos para o Azure Key Vault. Em seguida, usamos um script e o modelo do Resource Manager para implementar a VM com "Ubuntu Server 16.04 LTS + tempo de execução do Azure IoT Edge" imagem no Azure marketplace. O script demorou a etapa extra de instalar a CLI do Azure ([instalar a CLI do Azure com apt](https://docs.microsoft.com/cli/azure/install-azure-cli-apt)). Com a VM cópia de segurança e a execução, ligadas através de SSH, tem sessão iniciada no Azure, transferido certificados do Key Vault e efetuadas várias atualizações para a configuração do Runtime do IoT Edge, atualizando o arquivo config.yaml. Para obter mais informações sobre como utilizar o IoT Edge como um gateway, consulte [como um dispositivo do IoT Edge pode ser utilizado como um gateway](iot-edge-as-gateway.md). Para obter mais informações sobre como configurar um dispositivo IoT Edge como gateway transparente, consulte [configurar um dispositivo IoT Edge para atuar como gateway transparente](how-to-create-transparent-gateway.md).

Avance para o artigo seguinte para criar um IoT Edge módulos.

> [!div class="nextstepaction"]
> [Criar e implementar módulos personalizados do IoT Edge](tutorial-machine-learning-edge-06-custom-modules.md)
