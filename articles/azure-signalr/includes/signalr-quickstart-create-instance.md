---
title: incluir ficheiro
description: incluir ficheiro
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 4e8bdc63daa7450a88c2fcf4b7e878e18cea260d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184627"
---
## <a name="create-an-azure-signalr-service-instance"></a>Criar uma instância do Azure SignalR Service

A aplicação irá ligar a uma instância do SignalR Service no Azure.

1. Selecione o botão Novo, no canto superior esquerdo do portal do Azure. No ecrã Novo, escreva *SignalR Service* na caixa de pesquisa e prima enter.

    ![Procure o SignalR Service](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-new.png)

1. Selecione **SignalR Service** nos resultados da pesquisa e, em seguida, selecione **Criar**.

1. Introduza as seguintes definições.

    | Definição      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome do recurso** | Nome globalmente exclusivo | Nome que identifica a sua nova instância do SignalR Service. Os carateres válidos são `a-z`, `0-9` e `-`.  | 
    | **Subscrição** | A sua subscrição | A subscrição sob a qual esta nova instância do SignalR Service é criada. | 
    | **[Grupo de Recursos](../../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Nome do novo grupo de recursos no qual irá criar a sua instância do SignalR Service. | 
    | **Location** | EUA Oeste | Escolher uma [região](https://azure.microsoft.com/regions/) próxima de si. |
    | **Escalão de preço** | Livre | Experimente gratuitamente o Azure SignalR Service. |
    | **Contagem de unidades** |  Não aplicável | A contagem de unidades especifica quantas ligações a sua instância do SignalR Service pode aceitar. Pode configurar apenas no escalão Standard. |

    ![Criar o Serviço SignalR](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-create.png)

1. Selecione **Criar** para começar a implementar a instância do SignalR Service.

1. Depois da instância for implementada, abri-lo no portal e localize a página de definições. Alterar a definição do modo de serviço para *sem servidor* apenas se estiver a utilizar o serviço Azure SignalR por meio de associação de funções do Azure ou REST API. Deixe-nos *clássica* ou *predefinido* caso contrário.