---
title: incluir ficheiro
description: incluir ficheiro
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: f738daab7ddcf0403f546e7c9ffeaeccb66bc6b7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "91355653"
---
## <a name="create-an-azure-signalr-service-instance"></a>Criar uma instância do Azure SignalR Service

A aplicação irá ligar a uma instância do SignalR Service no Azure.

1. Selecione o botão Novo, no canto superior esquerdo do portal do Azure. No ecrã Novo, escreva *SignalR Service* na caixa de pesquisa e prima enter.

    ![A screenshot mostra a procura do Serviço SignalR no portal Azure.](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-new.png)

1. Selecione **SignalR Service** nos resultados da pesquisa e, em seguida, selecione **Criar**.

1. Introduza as seguintes definições.

    | Definição      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome de recurso** | Nome globalmente exclusivo | Nome que identifica a sua nova instância do SignalR Service. Os carateres válidos são `a-z`, `0-9` e `-`.  | 
    | **Subscrição** | A sua subscrição | A subscrição sob a qual esta nova instância do SignalR Service é criada. | 
    | **[Grupo de Recursos](../../azure-resource-manager/management/overview.md)** |  myResourceGroup | Nome do novo grupo de recursos no qual irá criar a sua instância do SignalR Service. | 
    | **Localização** | E.U.A. Oeste | Escolha uma [região](https://azure.microsoft.com/regions/) perto de si. |
    | **Escalão de preço** | Gratuito | Experimente gratuitamente o Azure SignalR Service. |
    | **Contagem de unidades** |  Não aplicável | A contagem de unidades especifica quantas ligações a sua instância do SignalR Service pode aceitar. Pode configurar apenas no escalão Standard. |
    | **Modo de serviço** |  Sem servidor | Para utilização com Funções Azure ou API REST. |

    ![A screenshot mostra o separador SignalR Basics com valores.](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-create.png)

1. Selecione **Criar** para começar a implementar a instância do SignalR Service.

1. Depois de implementada a instância, abra-a no portal e localize a sua página de Definições. Altere a definição do modo de serviço para *Serverless* apenas se estiver a utilizar o Serviço Azure SignalR através da ligação de funções Azure ou da API REST. Deixe-o em *Classic* ou *Default* de outra forma.