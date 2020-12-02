---
ms.openlocfilehash: 5fa8377b3cdddb2818c04c5a75e347ff73dc07d8
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509320"
---
Este tutorial requer os seguintes recursos Azure:

* IoT Hub
* Conta de armazenamento
* Conta Azure Media Services
* Linux VM em Azure, com [tempo de execução IoT Edge](../../../../../iot-edge/how-to-install-iot-edge.md) instalado

Para este arranque rápido, recomendamos que utilize o [script de configuração de recursos live video analytics](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) para implementar os recursos necessários na sua subscrição Azure. Para tal, siga estes passos:

1. Abra o [Azure Cloud Shell](https://shell.azure.com).
1. Se estiver a utilizar a Cloud Shell pela primeira vez, será solicitado que selecione uma subscrição para criar uma conta de armazenamento e uma partilha de Ficheiros Microsoft Azure. Selecione **Criar armazenamento** para criar uma conta de armazenamento para as informações da sessão Cloud Shell. Esta conta de armazenamento é separada da conta que o script criará para utilizar com a sua conta Azure Media Services.
1. No menu suspenso no lado esquerdo da janela Cloud Shell, selecione **Bash** como seu ambiente.

    ![Selecionador de ambiente](../../../media/quickstarts/env-selector.png)
1. Execute o seguinte comando.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    Se o script terminar com sucesso, deverá ver todos os recursos necessários na sua subscrição.
1. Depois de terminar o script, selecione os suportes encaracolados para expor a estrutura da pasta. Você verá alguns ficheiros no *diretório de amostras ~/clouddrive/Lva.* De interesse neste arranque rápido são:

     * ***~/clouddrive/lva-sample/edge-deployment/.env** _ - Este ficheiro contém propriedades que o Código do Estúdio Visual utiliza para implantar módulos num dispositivo de borda.
     _ ***~/clouddrive/lva-sample/appsetting.json*** - Visual Studio Code usa este ficheiro para executar o código de amostra.
     
    Vai precisar destes ficheiros quando configurar o seu ambiente de desenvolvimento no Código do Estúdio Visual na secção seguinte. Talvez queira copiá-los num ficheiro local por enquanto.
    
    ![Definições da aplicação](../../../media/quickstarts/clouddrive.png)
