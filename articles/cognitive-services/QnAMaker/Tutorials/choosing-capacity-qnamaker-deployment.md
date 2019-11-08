---
title: Capacidade de recurso para implantação-QnA Maker
titleSuffix: Azure Cognitive Services
description: Antes de criar seu serviço de QnA Maker, você deve decidir qual camada dos serviços acima é apropriada para você.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 5cbdb6fcf9fcdf12b54ff1db4b577bb975517131
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73793951"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Escolhendo a capacidade para sua implantação de QnA Maker

O serviço de QnA Maker, assume uma dependência de três recursos do Azure:
1.  Serviço de aplicativo (para o tempo de execução)
2.  Pesquisa Cognitiva do Azure (para armazenar e Pesquisar QnAs)
3.  Informações do aplicativo (opcional, para armazenar logs de chat e telemetria)

Antes de criar seu serviço de QnA Maker, você deve decidir qual camada dos serviços acima é apropriada para você. 

Normalmente, há três parâmetros que você precisa considerar:

1. **A taxa de transferência de que você precisa do serviço**: selecione o [plano de aplicativo](https://azure.microsoft.com/pricing/details/app-service/plans/) apropriado para seu serviço de aplicativo com base em suas necessidades. Você pode [escalar](https://docs.microsoft.com/azure/app-service/manage-scale-up) ou reduzir verticalmente o aplicativo. Isso também deve influenciar sua seleção de SKU Pesquisa Cognitiva do Azure, veja mais detalhes [aqui](https://docs.microsoft.com/azure/search/search-sku-tier).

1. **Tamanho e o número de bases de dados de conhecimento**: escolha o [SKU do Azure Search](https://azure.microsoft.com/pricing/details/search/) apropriado para seu cenário. Você pode publicar as bases de dados de conhecimento N-1 em uma determinada camada, em que N é o máximo de índices permitidos na camada. Verifique também o tamanho máximo e o número de documentos permitidos por camada.

    Por exemplo, se sua camada tiver 15 índices permitidos, você poderá publicar 14 bases de dados de conhecimento (1 índice por base de dados de conhecimento publicado). O índice décimo-quinto é usado para todas as bases de dados de conhecimento para criação e teste. 

1. **Número de documentos como fontes**: a SKU gratuita do serviço de gerenciamento de QnA Maker limita o número de documentos que você pode gerenciar por meio do portal e as APIs para 3 (de 1 MB de tamanho cada). O SKU Standard não tem limites para o número de documentos que você pode gerenciar. Veja mais detalhes [aqui](https://aka.ms/qnamaker-pricing).

A tabela a seguir fornece algumas diretrizes de alto nível.

|                        | Gerenciamento de QnA Maker | Serviço de Aplicações | Pesquisa Cognitiva do Azure | Limitações                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Experimentação        | SKU gratuito             | Escalão Gratuito   | Escalão Gratuito    | Publicar até 2 KBs, 50 MB de tamanho  |
| Ambiente de desenvolvimento/teste   | SKU Standard         | Partilhada      | Básica        | Publicar até 14 KBs, tamanho de 2 GB    |
| Ambiente de produção | SKU Standard         | Básica       | Standard     | Publicar até 49 KBs, tamanho de 25 GB |

Para atualizar sua pilha de QnA Maker, consulte [atualizar seu serviço de QnA Maker](../How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Atualizar seu serviço de QnA Maker](../How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker)
