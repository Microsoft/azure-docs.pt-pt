---
title: 'Tutorial: configurar IoT Edge dispositivo Machine Learning no Azure IoT Edge'
description: 'Tutorial: configurar uma máquina virtual do Azure que executa o Linux como um dispositivo Azure IoT Edge que atua como um gateway transparente.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 714f0c335e2871fa1afe2f99d08870f0e39c488e
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113961"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>Tutorial: configurar um dispositivo IoT Edge

> [!NOTE]
> Este artigo faz parte de uma série de um tutorial sobre como usar Azure Machine Learning em IoT Edge. Se você chegou a este artigo diretamente, incentivamos você a começar com o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série para obter os melhores resultados.

Neste artigo, configuramos uma máquina virtual do Azure que executa o Linux para ser um Azure IoT Edge dispositivo que atua como um gateway transparente. A configuração de gateway transparente permite que os dispositivos se conectem ao Hub IoT do Azure por meio do gateway sem saber que o gateway existe. Ao mesmo tempo, um usuário que interage com os dispositivos no Hub IoT não está ciente do dispositivo de gateway intermediário. Por fim, usamos o gateway transparente para adicionar a análise de borda ao nosso sistema adicionando IoT Edge módulos ao gateway.

As etapas neste artigo normalmente são executadas por um desenvolvedor de nuvem.

## <a name="generate-certificates"></a>Gerar certificados

Para que um dispositivo funcione como um gateway, ele precisa ser capaz de se conectar com segurança a dispositivos downstream. O Azure IoT Edge permite-lhe utilizar uma infraestrutura de chaves públicas (PKI) para configurar ligações seguras entre dispositivos. Neste caso, estamos está a permitir que um dispositivo downstream ligar a um dispositivo IoT Edge que atua como um gateway transparente. Para manter a segurança razoável, o dispositivo downstream deve confirmar a identidade do dispositivo de IoT Edge. Para obter mais informações sobre como IoT Edge dispositivos usam certificados, consulte [Azure IOT Edge detalhes de uso do certificado](iot-edge-certs.md).

Nesta seção, criamos os certificados autoassinados usando uma imagem do Docker que criamos e executamos. Optamos por usar uma imagem do Docker para concluir esta etapa, pois ela reduziu significativamente o número de etapas necessárias para criar os certificados no computador de desenvolvimento do Windows. Consulte [gerar certificados com o Windows](how-to-create-transparent-gateway.md#generate-certificates-with-windows) para obter os detalhes sobre como produzir os certificados sem usar um contêiner. [Gerar certificados com o Linux](how-to-create-transparent-gateway.md#generate-certificates-with-linux) tem o conjunto de instruções que automatizamos com a imagem do Docker.

1. Entre em sua máquina virtual de desenvolvimento.

2. Abra um prompt de linha de comando e execute o comando a seguir para criar um diretório na VM.

    ```cmd
    mkdir c:\edgeCertificates
    ```

3. Inicie o **Docker for Windows** no menu Iniciar do Windows.

4. Abra o Visual Studio Code.

5. Selecione **arquivo** > **abrir pasta...** e escolha **C:\\fonte\\IoTEdgeAndMlSample\\createcertificates**.

6. Clique com o botão direito do mouse no dockerfile e escolha **Compilar imagem**.

7. Na caixa de diálogo, aceite o valor padrão para o nome da imagem e a marca: **createcertificates: Latest**.

8. Aguarde a conclusão da compilação.

    > [!NOTE]
    > Você pode ver um aviso sobre uma chave pública ausente. É seguro ignorar este aviso. Da mesma forma, você verá um aviso de segurança que recomenda que você verifique/redefina as permissões em sua imagem, o que é seguro para ignorar para esta imagem.

9. Na janela Visual Studio Code terminal, execute o contêiner createcertificates.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. O Docker solicitará acesso à unidade **c:\\** . Selecione **compartilhá-lo**.

11. Forneça suas credenciais quando solicitado.

12. Quando o contêiner terminar a execução, verifique os seguintes arquivos em **c:\\edgeCertificates**:

    * c:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * c:\\edgeCertificates\\private\\new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Carregar certificados para Azure Key Vault

Para armazenar nossos certificados com segurança e torná-los acessíveis de vários dispositivos, carregaremos os certificados em Azure Key Vault. Como você pode ver na lista acima, temos dois tipos de arquivos de certificado: PFX e PEM. Trataremos o PFX como Key Vault certificados a serem carregados para Key Vault. Os arquivos PEM são texto sem formatação e nós os trataremos como segredos Key Vault. Usaremos o Key Vault associado ao espaço de trabalho de Azure Machine Learning que criamos executando o [Azure notebooks](tutorial-machine-learning-edge-04-train-model.md#run-azure-notebooks).

1. No [portal do Azure](https://portal.azure.com), navegue até seu espaço de trabalho do Azure Machine Learning.

2. Na página Visão geral do espaço de trabalho Azure Machine Learning, localize o nome do **Key Vault**.

    ![Copiar nome do cofre de chaves](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. Em seu computador de desenvolvimento, carregue os certificados para Key Vault. Substitua **\<subscriptionid\>** e **\<keyvaultname\>** pelas informações do recurso.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. Se solicitado, entre no Azure.

5. O script será executado por alguns minutos com a saída que lista as novas entradas de Key Vault.

    ![Key Vault saída de script](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>Criar dispositivo do IoT Edge

Para conectar um dispositivo Azure IoT Edge a um hub IoT, primeiro criamos uma identidade para o dispositivo no Hub. Pegamos a cadeia de conexão da identidade do dispositivo na nuvem e a usamos para configurar o tempo de execução em nosso dispositivo de IoT Edge. Depois que o dispositivo tiver sido configurado e se conectar ao Hub, será possível implantar módulos e enviar mensagens. Também podemos alterar a configuração do dispositivo físico IoT Edge alterando a configuração da identidade do dispositivo correspondente no Hub IoT.

Para este tutorial, criamos a nova identidade do dispositivo usando Visual Studio Code. Você também pode concluir essas etapas usando o [portal do Azure](how-to-register-device.md#register-in-the-azure-portal)ou [CLI do Azure](how-to-register-device.md#register-with-the-azure-cli).

1. No computador de desenvolvimento, abra Visual Studio Code.

2. Abra o quadro **dispositivos do Hub IOT do Azure** na exibição do Visual Studio Code Explorer.

3. Clique nas reticências e selecione **criar IOT Edge dispositivo**.

4. Dê um nome ao dispositivo. Para sua conveniência, usamos **aaTurbofanEdgeDevice** para que ele classifique antecipadamente todos os dispositivos cliente que criamos anteriormente por meio da estrutura do dispositivo para enviar os dados de teste.

5. O novo dispositivo aparecerá na lista de dispositivos.

    ![Exibir novo aaTurbofanEdgeDevice no Gerenciador de VS Code](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Implantar máquina virtual do Azure

Usamos o [Azure IOT Edge na](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) imagem do Ubuntu do Azure Marketplace para criar nosso dispositivo de IOT Edge para este tutorial. O Azure IoT Edge na imagem do Ubuntu instala o tempo de execução mais recente do Azure IoT Edge e suas dependências na inicialização. Implantamos a VM usando um script do PowerShell, `Create-EdgeVM.ps1`; um modelo do Resource Manager, `IoTEdgeVMTemplate.json`; e um script de Shell, `install packages.sh`.

### <a name="enable-programmatic-deployment"></a>Habilitar implantação programática

Para usar a imagem do Marketplace em uma implantação com script, precisamos habilitar a implantação programática para a imagem.

1. Inicie sessão no Portal do Azure.

1. Selecione **Todos os serviços**.

1. Na barra de pesquisa, insira e selecione **Marketplace**.

1. Na barra de pesquisa, insira e selecione **Azure IOT Edge no Ubuntu**.

1. Selecione o **deseja implantar programaticamente?** Link de introdução.

1. Selecione o botão **habilitar** e, em seguida, **salvar**.

    ![Habilitar a implantação programática para a VM](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Você verá uma notificação de êxito.

### <a name="create-virtual-machine"></a>Criar a máquina virtual

Em seguida, execute o script para criar a máquina virtual para seu dispositivo IoT Edge.

1. Abra uma janela do PowerShell e navegue até o diretório **EdgeVM**

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

2. Execute o script para criar a máquina virtual.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. Quando solicitado, forneça valores para cada parâmetro. Para assinatura, grupo de recursos e local, recomendamos que você use o mesmo que você tem para todos os recursos em todo este tutorial.

    * **ID da assinatura do Azure**: encontrada na portal do Azure
    * **Nome do grupo de recursos**: memorizar o nome para agrupar os recursos para este tutorial
    * **Local**: local do Azure em que a máquina virtual será criada. Por exemplo, westus2 ou northeurope. Para obter mais informações, consulte todos os [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/).
    * **AdminUsername**: o nome da conta de administrador que será usada para entrar na máquina virtual
    * **AdminPassword**: a senha a ser definida para o AdminUsername na máquina virtual

4. Para que o script possa configurar a VM, você precisa entrar no Azure com as credenciais associadas à assinatura do Azure que você está usando.

5. O script confirma as informações para a criação de sua VM. Selecione **y** ou **Enter** para continuar.

6. O script é executado por vários minutos, uma vez que executa as seguintes etapas:

    * Criar o grupo de recursos se ele ainda não existir
    * Criar a máquina virtual
    * Adicionar exceções NSG para a VM para as portas 22 (SSH), 5671 (AMQP), 5672 (AMPQ) e 443 (SSL)
    * Instalar o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest))

7. O script gera a cadeia de conexão SSH para conectar-se à VM. Copie a cadeia de conexão para a próxima etapa.

    ![Copiar cadeia de conexão SSH para VM](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Ligar ao seu dispositivo IoT Edge

As várias seções a seguir configuram a máquina virtual do Azure que criamos. A primeira etapa é conectar-se à máquina virtual.

1. Abra um prompt de comando e cole a cadeia de conexão SSH que você copiou da saída do script. Insira suas próprias informações para nome de usuário, sufixo e região de acordo com os valores fornecidos para o script do PowerShell na seção anterior.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. Quando for solicitado a validar a autenticidade do host, digite **Sim** e selecione **Enter**.

3. Quando solicitado, forneça sua senha.

4. O Ubuntu exibe uma mensagem de boas-vindas e, em seguida, você verá um prompt como `<username>@<machinename>:~$`.

## <a name="download-key-vault-certificates"></a>Baixar Key Vault certificados

Anteriormente neste artigo, carregamos certificados para Key Vault para disponibilizá-los para nosso dispositivo de IoT Edge e nosso dispositivo de folha, que é um dispositivo downstream que usa o dispositivo IoT Edge como um gateway para se comunicar com o Hub IoT. Trataremos do dispositivo de folha posteriormente no tutorial. Nesta seção, baixe os certificados para o dispositivo IoT Edge.

1. Na sessão SSH na máquina virtual Linux, entre no Azure com o CLI do Azure.

    ```bash
    az login
    ```

1. Você será solicitado a abrir um navegador para <https://microsoft.com/devicelogin> e fornecer um código exclusivo. Você pode executar essas etapas em seu computador local. Feche a janela do navegador quando terminar de autenticar.

1. Quando você se autentica com êxito, a VM do Linux entrará e listará suas assinaturas do Azure.

1. ASet a assinatura do Azure que você deseja usar para CLI do Azure comandos.

    ```bash
    az account set --subscription <subscriptionId>
    ```

1. Crie um diretório na VM para os certificados.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Baixe os certificados que você armazenou no cofre de chaves: New-Edge-Device-Full-Chain. cert. PEM, New-Edge-Device. Key. pem e Azure-IOT-Test-only. root. ca. cert. pem

    ```bash
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Atualizar a configuração do dispositivo IoT Edge

O tempo de execução de IoT Edge usa o arquivo/etc/iotedge/config.YAML para manter sua configuração. Precisamos atualizar três partes de informações neste arquivo:

* **Cadeia de conexão do dispositivo**: a cadeia de conexão da identidade deste dispositivo no Hub IOT
* **Certificados:** os certificados a serem usados para conexões feitas com dispositivos downstream
* Nome do **host:** o FQDN (nome de domínio totalmente qualificado) do dispositivo de IOT Edge da VM.

O *Azure IOT Edge na imagem do Ubuntu* que usamos para criar a VM IOT Edge vem com um script de shell que atualiza o config. YAML com a cadeia de conexão.

1. Em Visual Studio Code clique com o botão direito do mouse no dispositivo IoT Edge e selecione **copiar cadeia de conexão do dispositivo**.

    ![Copiar cadeia de conexão do Visual Studio Code](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. Em sua sessão SSH, execute o comando para atualizar o arquivo config. YAML com a cadeia de conexão do dispositivo.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Em seguida, atualizaremos os certificados e o nome do host editando diretamente o config. YAML.

1. Abra o arquivo config. YAML.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. Atualize a seção de certificados do arquivo config. YAML removendo o `#` à esquerda e definindo o caminho para que o arquivo seja semelhante ao exemplo a seguir:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Certifique-se de que "certificados:" não tem espaço em branco precedente e que cada um dos certificados é precedido por dois espaços.

    Clicar com o botão direito do mouse no nano colará o conteúdo da área de transferência na posição atual do cursor. Para substituir a cadeia de caracteres, use as setas do teclado para navegar até a cadeia de caracteres que você deseja substituir, exclua a cadeia de caracteres e clique com o botão direito do mouse para colar do buffer.

3. Na portal do Azure, navegue até sua máquina virtual. Copie o nome DNS (FQDN do computador) da seção **visão geral** .

4. Cole o FQDN na seção hostname do arquivo config. yml. Verifique se o nome está em letras minúsculas.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. Salve e feche o arquivo (`Ctrl + X`, `Y`, `Enter`).

6. Reinicie o daemon iotedge.

    ```bash
    sudo systemctl restart iotedge
    ```

7. Verifique o status do daemon de IoT Edge (após o comando, digite ": q" para sair).

    ```bash
    systemctl status iotedge
    ```

8. Se você vir erros (texto colorido prefixado com "\[erro\]") no status, examine os logs do daemon para obter informações detalhadas sobre o erro.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```

## <a name="next-steps"></a>Passos seguintes

Acabamos de concluir a configuração de uma VM do Azure como Azure IoT Edge gateway transparente. Começamos gerando certificados de teste, que carregamos para Azure Key Vault. Em seguida, usamos um modelo de script e do Resource Manager para implantar a VM com a imagem "Ubuntu Server 16, 4 LTS + Azure IoT Edge Runtime" do Azure Marketplace. O script levou a etapa extra de instalar o CLI do Azure ([instalar CLI do Azure com apt](https://docs.microsoft.com/cli/azure/install-azure-cli-apt)). Com a VM ativa e em execução, conectamos por meio de SSH, fiz logon no Azure, certificados baixados de Key Vault e fizemos várias atualizações na configuração do tempo de execução IoT Edge atualizando o arquivo config. YAML. Para obter mais informações sobre como usar IoT Edge como um gateway, consulte [como um dispositivo de IOT Edge pode ser usado como um gateway](iot-edge-as-gateway.md). Para obter mais informações sobre como configurar um dispositivo de IoT Edge como um gateway transparente, consulte [configurar um dispositivo de IOT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md).

Prossiga para o próximo artigo para criar IoT Edge módulos.

> [!div class="nextstepaction"]
> [Criar e implantar módulos de IoT Edge personalizados](tutorial-machine-learning-edge-06-custom-modules.md)
