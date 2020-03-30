---
title: incluir ficheiro
description: incluir ficheiro
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 0a228f6d8b4f72acd3783f27bf192fe6bd13f988
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "75392159"
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
    | **Assinatura** | A sua subscrição | A subscrição sob a qual esta nova instância do SignalR Service é criada. | 
    | **[Grupo de Recursos](../../azure-resource-manager/management/overview.md)** |  myResourceGroup | Nome do novo grupo de recursos no qual irá criar a sua instância do SignalR Service. | 
    | **Localização** | E.U.A. Oeste | Escolher uma [região](https://azure.microsoft.com/regions/) próxima de si. |
    | **Nível de preços** | Gratuito | Experimente gratuitamente o Azure SignalR Service. |
    | **Contagem de unidades** |  Não aplicável | A contagem de unidades especifica quantas ligações a sua instância do SignalR Service pode aceitar. Pode configurar apenas no escalão Standard. |
    | **Modo de serviço** |  Sem servidor | Para utilização com funções Azure ou API REST. |

    ![Criar o Serviço SignalR](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-create.png)

1. Selecione **Criar** para começar a implementar a instância do SignalR Service.

1. Depois de a instância ser implementada, abra-a no portal e localize a sua página Definições. Mude a definição do Modo de Serviço para *Serverless* apenas se estiver a utilizar o Serviço de Sinalizador Azure através da ligação das funções Azure ou da API REST. Deixe-o em *Clássico* ou *Padrão* de outra forma.