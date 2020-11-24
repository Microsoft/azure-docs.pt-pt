---
title: Quickstart - Use o CLI Azure para criar uma fila de autocarros de serviço / Microsoft Docs
description: Neste arranque rápido, você aprende a usar o CLI Azure para criar um espaço de nomes de Service Bus e, em seguida, uma fila nesse espaço de nome.
author: spelluru
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.openlocfilehash: 14892b114f3bb8a0bb6c07f7cdd01a174b931cf9
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95810635"
---
# <a name="use-the-azure-cli-to-create-a-service-bus-namespace-and-a-queue"></a>Use o CLI Azure para criar um espaço de nome de ônibus de serviço e uma fila
Este quickstart mostra-lhe como criar um espaço de nome de Service Bus e uma fila usando o Azure CLI. Também mostra como obter credenciais de autorização que uma aplicação do cliente pode usar para enviar/receber mensagens de/para a fila. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Pré-requisitos
Se não tiver uma subscrição do Azure, pode criar uma [conta gratuita][free account] antes de começar.

Neste arranque rápido, você usa Azure Cloud Shell que você pode lançar após a placar no portal Azure. Para mais detalhes sobre a Azure Cloud Shell, consulte [a visão geral da Azure Cloud Shell](../cloud-shell/overview.md). Também pode [instalar](/cli/azure/install-azure-cli) e utilizar o Azure PowerShell na sua máquina. 

## <a name="provision-resources"></a>Recursos de aprovisionamento
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Lance Azure Cloud Shell selecionando o ícone mostrado na imagem seguinte. Mude para o modo **Bash** se a Cloud Shell estiver no modo **PowerShell.** 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Lançar Cloud Shell":::
3. Executar o seguinte comando para criar um grupo de recursos Azure. Atualize o nome do grupo de recursos e a localização, se desejar. 

    ```azurecli-interactive
    az group create --name ContosoRG --location eastus
    ```
4. Executar o seguinte comando para criar um espaço de nome de mensagens de ônibus de serviço.

    ```azurecli-interactive
    az servicebus namespace create --resource-group ContosoRG --name ContosoSBusNS --location eastus
    ```
5. Executar o seguinte comando para criar uma fila no espaço de nome que criou no passo anterior. Neste exemplo, `ContosoRG` é o grupo de recursos que criou no passo anterior. `ContosoSBusNS` é o nome do espaço de nomes do Service Bus criado nesse grupo de recursos. 

    ```azurecli-interactive
    az servicebus queue create --resource-group ContosoRG --namespace-name ContosoSBusNS --name ContosoOrdersQueue
    ```
6. Executar o seguinte comando para obter a cadeia de ligação primária para o espaço de nome. Utilize este fio de ligação para ligar à fila e enviar e receber mensagens. 

    ```azurecli-interactive
    az servicebus namespace authorization-rule keys list --resource-group ContosoRG --namespace-name ContosoSBusNS --name RootManageSharedAccessKey --query primaryConnectionString --output tsv    
    ```

    Note a cadeia de ligação e o nome da fila. Usa-as para enviar e receber mensagens. 


## <a name="next-steps"></a>Passos seguintes
Neste artigo, criou um espaço de nomes de Service Bus e uma fila no espaço de nomes. Para saber como enviar/receber mensagens de/para a fila, consulte um dos seguintes quickstarts na secção **Enviar e receber mensagens.** 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

