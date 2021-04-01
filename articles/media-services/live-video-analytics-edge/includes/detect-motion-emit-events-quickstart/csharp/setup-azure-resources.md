---
ms.openlocfilehash: 6b1df49ed0f2c543706d7584a1ade5dc5554a564
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101750524"
---
Este tutorial requer os seguintes recursos Azure:

* IoT Hub
* Conta de armazenamento
* Conta Azure Media Services
* Linux VM em Azure, com [tempo de execução IoT Edge](../../../../../iot-edge/how-to-install-iot-edge.md) instalado

Para este arranque rápido, recomendamos que utilize o [script de configuração de recursos live video analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) para implementar os recursos necessários na sua subscrição Azure. Para tal, siga estes passos:

1. Abra o [Azure Cloud Shell](https://ms.portal.azure.com/#cloudshell/).
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/quickstarts/cloud-shell.png" alt-text="Cloud Shell":::
1. Se estiver a utilizar a Cloud Shell pela primeira vez, será solicitado que selecione uma subscrição para criar uma conta de armazenamento e uma partilha de Ficheiros Microsoft Azure. Selecione **Criar armazenamento** para criar uma conta de armazenamento para as informações da sessão Cloud Shell. Esta conta de armazenamento é separada da conta que o script criará para utilizar com a sua conta Azure Media Services.
1. No menu suspenso no lado esquerdo da janela Cloud Shell, selecione **Bash** como seu ambiente.

    ![Selecionador de ambiente](../../../media/quickstarts/env-selector.png)
1. Execute o seguinte comando.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    Após a conclusão com sucesso do script, deverá ver todos os recursos necessários na sua subscrição. Um total de 12 recursos serão configurados pelo script:
    1. **Streaming Endpoint** - Isto ajudará na reprodução do ativo AMS gravado.
    1. **Máquina virtual** - Esta é uma máquina virtual que funcionará como o seu dispositivo de borda.
    1. **Disco** - Este é um disco de armazenamento que está ligado à máquina virtual para armazenar meios e artefactos.
    1. **Grupo de segurança de rede** - Isto é usado para filtrar o tráfego de rede de e para os recursos Azure numa rede virtual Azure.
    1. **Interface de rede** - Isto permite que uma Máquina Virtual Azure comunique com internet, Azure e outros recursos.
    1. **Ligação de bastião** - Isto permite-lhe ligar-se à sua máquina virtual utilizando o seu navegador e o portal Azure.
    1. **Endereço IP público** - Isto permite que os recursos do Azure comuniquem à Internet e aos serviços Azure virados para o público
    1. **Rede virtual** - Isto permite que muitos tipos de recursos Azure, como a sua máquina virtual, comuniquem de forma segura entre si, a internet e as redes no local. Saiba mais sobre [redes Virtuais](../../../../../virtual-network/virtual-networks-overview.md)
    1. **IoT Hub** - Este funciona como um centro de mensagens central para comunicação bidis entre a sua aplicação IoT, os módulos IoT Edge e os dispositivos que gere.
    1. **Conta de serviço de** mídia - Isto ajuda na gestão e streaming de conteúdos de mídia em Azure.
    1. **Conta de armazenamento** - Tem de ter uma conta de armazenamento primário e pode ter qualquer número de contas de armazenamento secundária associadas à sua conta de Serviços de Comunicação Social. Para mais informações, consulte [as contas do Azure Storage com as contas da Azure Media Services](../../../../latest/storage-account-concept.md).
    1. **Registo de contentores** - Isto ajuda a armazenar e gerir as imagens privadas do seu contentor Docker e artefactos relacionados.

1. Depois de terminar o script, selecione os suportes encaracolados para expor a estrutura da pasta. Você verá alguns ficheiros no *diretório de amostras ~/clouddrive/Lva.* De interesse neste arranque rápido são:

     * ***~/clouddrive/lva-sample/edge-deployment/.env*** - Este ficheiro contém propriedades que o Código do Estúdio Visual utiliza para implantar módulos num dispositivo de borda.
     * ***~/clouddrive/lva-sample/appsetting.json*** - Visual Studio Code usa este ficheiro para executar o código de amostra.
     
    Vai precisar destes ficheiros quando configurar o seu ambiente de desenvolvimento no Código do Estúdio Visual na secção seguinte. Talvez queira copiá-los num ficheiro local por enquanto.
    
    ![Definições da aplicação](../../../media/quickstarts/clouddrive.png)

> [!TIP]
> Se encontrar problemas com os recursos da Azure que são criados, por favor, consulte o nosso **[guia de resolução de problemas](../../../troubleshoot-how-to.md#common-error-resolutions)** para resolver alguns problemas comumente encontrados.