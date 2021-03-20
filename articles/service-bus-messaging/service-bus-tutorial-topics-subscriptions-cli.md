---
title: Utilize o CLI Azure para criar tópicos e subscrições de Service Bus
description: Neste quickstart, você aprende a criar um tópico de Service Bus e subscrições para esse tópico usando o Azure CLI
ms.date: 06/23/2020
ms.topic: quickstart
author: spelluru
ms.author: spelluru
ms.openlocfilehash: 080b089efa276779420f6d9bc8e76272f1e65788
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90069701"
---
# <a name="use-azure-cli-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Use o CLI Azure para criar um tópico de Service Bus e subscrições para o tema
Neste quickstart, você usa O CLI Azure para criar um tópico de Service Bus e, em seguida, criar subscrições para esse tópico. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>O que são os tópicos e as subscrições do Service Bus?
Os tópicos e as subscrições do Service Bus suportam um modelo de comunicação de mensagens de *publicação/subscrição*. Ao utilizar tópicos e subscrições, os componentes de uma aplicação distribuída não comunicam diretamente entre si; trocam antes mensagens através de um tópico, que funciona como um intermediário.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Em contraste com as filas do Service Bus, em que cada mensagem é processada por um único consumidor, os tópicos e as subscrições fornecem uma forma de comunicação de um a muitos, utilizando um padrão de publicação/subscrição. É possível registar várias subscrições num tópico. Quando uma mensagem é enviada para um tópico, é depois disponibilizada para que cada subscrição a processe de forma independente. Uma subscrição num tópico é semelhante a uma fila virtual que recebe cópias das mensagens que foram enviadas para o tópico. Pode registar opcionalmente regras de filtragem para um tópico por subscrição, o que lhe permite filtrar ou restringir quais as mensagens a um tópico que são recebidas através das quais as subscrições de tópicos são recebidas.

Os tópicos e subscrições do Service Bus permitem-lhe dimensionar para processar um grande número de mensagens através de um grande número de utilizadores e aplicações.

## <a name="prerequisites"></a>Pré-requisitos
Se não tiver uma subscrição do Azure, pode criar uma [conta gratuita][free account] antes de começar.

Neste arranque rápido, você usa Azure Cloud Shell que você pode lançar após a placar no portal Azure. Para mais detalhes sobre a Azure Cloud Shell, consulte [a visão geral da Azure Cloud Shell](../cloud-shell/overview.md). Também pode [instalar](/cli/azure/install-azure-cli) e utilizar o Azure PowerShell na sua máquina. 

## <a name="create-a-service-bus-topic-and-subscriptions"></a>Criar um tópico do Service Bus e subscrições
Cada [subscrição de um tópico](service-bus-messaging-overview.md#topics) pode receber uma cópia de cada mensagem. Os tópicos são totalmente compatíveis no que diz respeito a protocolo e semântica com as filas do Service Bus. Os tópicos do Service Bus suportam uma vasta gama de regras de seleção com condições de filtro, com ações opcionais que definem ou modificam propriedades de mensagem. Sempre que uma regra tem correspondência, é criada uma mensagem. Para saber mais sobre regras, filtros e ações, clique nesta [hiperligação](topic-filters.md).

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Lance Azure Cloud Shell selecionando o ícone mostrado na imagem seguinte. Mude para o modo **Bash** se a Cloud Shell estiver no modo **PowerShell.** 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Lançar Cloud Shell":::
3. Executar o seguinte comando para criar um grupo de recursos Azure. Atualize o nome do grupo de recursos e a localização, se desejar. 

    ```azurecli-interactive
    az group create --name MyResourceGroup --location eastus
    ```
4. Executar o seguinte comando para criar um espaço de nome de mensagens de ônibus de serviço. Atualize o nome do espaço de nome para ser único. 

    ```azurecli-interactive
    namespaceName=MyNameSpace$RANDOM
    az servicebus namespace create --resource-group MyResourceGroup --name $namespaceName --location eastus
    ```
5. Executar o seguinte comando para criar um tópico no espaço de nomes. 

    ```azurecli-interactive
    az servicebus topic create --resource-group MyResourceGroup   --namespace-name $namespaceName --name MyTopic
    ```
6. Crie a primeira subscrição do tópico
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S1    
    ```
6. Criar a segunda subscrição do tópico
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S2    
    ```
6. Criar a terceira subscrição do tópico
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S3    
    ```
7. Crie um filtro na primeira subscrição com um filtro utilizando propriedades personalizadas `StoreId` (é um dos `Store1` , e `Store2` `Store3` ).

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --subscription-name S1 --name MyFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3')"    
    ```
8. Crie um filtro na segunda subscrição com um filtro utilizando propriedades do cliente `StoreId = Store4` ()

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S2 --name MySecondFilter --filter-sql-expression "StoreId = 'Store4'"    
    ```
9. Crie um filtro na terceira subscrição com um filtro utilizando propriedades do cliente `StoreId` (não em `Store1` , ou `Store2` `Store3` `Store4` .

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --subscription-name S3 --name MyThirdFilter --filter-sql-expression "StoreId NOT IN ('Store1','Store2','Store3', 'Store4')"     
    ```
10. Executar o seguinte comando para obter a cadeia de ligação primária para o espaço de nome. Utilize este fio de ligação para ligar à fila e enviar e receber mensagens. 

    ```azurecli-interactive
    az servicebus namespace authorization-rule keys list --resource-group MyResourceGroup --namespace-name $namespaceName --name RootManageSharedAccessKey --query primaryConnectionString --output tsv    
    ```

    Note a cadeia de ligação e o nome do tópico. Usa-as para enviar e receber mensagens. 
    

## <a name="next-steps"></a>Passos seguintes
Para aprender a enviar mensagens para um tópico e receber essas mensagens através de uma subscrição, consulte o seguinte artigo: selecione a linguagem de programação no TOC. 

> [!div class="nextstepaction"]
> [Publicar e subscrever para mensagens](service-bus-dotnet-how-to-use-topics-subscriptions.md)


[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
