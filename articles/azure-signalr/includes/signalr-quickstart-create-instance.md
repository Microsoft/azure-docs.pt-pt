---
title: incluir ficheiro
description: incluir ficheiro
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: ac77f0b6a1d90fd78db64618c1e03ade198a67c0
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882201"
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
    | **Modo de serviço** |  Sem servidor | Para uso com Azure Functions ou a API REST. |

    ![Criar o Serviço SignalR](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-create.png)

1. Selecione **Criar** para começar a implementar a instância do SignalR Service.

1. Depois que a instância for implantada, abra-a no portal e localize sua página de configurações. Altere a configuração do modo de serviço para sem *servidor* somente se você estiver usando o serviço de Signalr do Azure por meio da associação de Azure Functions ou da API REST. Deixe em modo *clássico* ou *padrão* .