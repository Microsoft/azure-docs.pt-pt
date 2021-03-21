---
title: Use a Azure PowerShell para criar uma fila de ônibus de serviço
description: Neste arranque rápido, você aprende a criar um espaço de nomes de Service Bus e uma fila no espaço de nomes usando o Azure PowerShell.
author: spelluru
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8cf024735c66e6bae9d334e3d8ce8d0a0eed1426
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95799095"
---
# <a name="use-azure-powershell-to-create-a-service-bus-namespace-and-a-queue"></a>Use a Azure PowerShell para criar um espaço de nome de ônibus de serviço e uma fila
Este quickstart mostra-lhe como criar um espaço de nomes de Service Bus e uma fila usando o Azure PowerShell. Também mostra como obter credenciais de autorização que uma aplicação do cliente pode usar para enviar/receber mensagens de/para a fila. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]


## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, certifique-se de que tem uma subscrição Azure. Se não tiver uma subscrição do Azure, pode criar uma [conta gratuita][] antes de começar. 

Neste arranque rápido, você usa Azure Cloud Shell que você pode lançar após a placar no portal Azure. Para mais detalhes sobre a Azure Cloud Shell, consulte [a visão geral da Azure Cloud Shell](../cloud-shell/overview.md). Também pode [instalar](/powershell/azure/install-Az-ps) e utilizar o Azure PowerShell na sua máquina. 


## <a name="provision-resources"></a>Recursos de aprovisionamento
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Lançar Azure Cloud Shell selecionando o ícone mostrado na seguinte imagem: 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Lançar Cloud Shell":::
3. Na janela Cloud Shell inferior, mude de **Bash** para **PowerShell**. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/cloud-power-shell.png" alt-text="Mudar para o modo PowerShell":::    
4. Executar o seguinte comando para criar um grupo de recursos Azure. Atualize o nome do grupo de recursos e a localização, se desejar. 

    ```azurepowershell-interactive
    New-AzResourceGroup –Name ContosoRG –Location eastus
    ```
5. Executar o seguinte comando para criar um espaço de nome de mensagens de ônibus de serviço. Neste exemplo, `ContosoRG` é o grupo de recursos que criou no passo anterior. `ContosoSBusNS` é o nome do espaço de nomes do Service Bus criado nesse grupo de recursos. 

    ```azurepowershell-interactive
    New-AzServiceBusNamespace -ResourceGroupName ContosoRG -Name ContosoSBusNS -Location eastus
    ```
6. Execute o seguinte para criar uma fila no espaço de nome que criou no passo anterior. 

    ```azurepowershell-interactive
    New-AzServiceBusQueue -ResourceGroupName ContosoRG -NamespaceName ContosoSBusNS -Name ContosoOrdersQueue 
    ```
7. Obtenha a cadeia de ligação primária para o espaço de nomes. Utilize este fio de ligação para ligar à fila e enviar e receber mensagens. 

    ```azurepowershell-interactive    
    Get-AzServiceBusKey -ResourceGroupName ContosoRG -Namespace ContosoSBusNS -Name RootManageSharedAccessKey
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

[conta livre]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

