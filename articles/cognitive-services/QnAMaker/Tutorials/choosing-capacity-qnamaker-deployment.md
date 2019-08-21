---
title: Capacidade de recursos para a implementação - QnA Maker
titleSuffix: Azure Cognitive Services
description: Antes de criar o serviço QnA Maker, deve decidir qual dos escalões de serviços acima é adequada para si.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/20/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 2b2879a1ebcf01a0433873e8da77c4ce55a078cd
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647003"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Escolher a capacidade para a sua implementação do QnA Maker

O serviço QnA Maker, utiliza uma dependência em três recursos do Azure:
1.  Serviço de aplicações (para o tempo de execução)
2.  Azure Search (para armazenar e Pesquisar QnAs)
3.  Informações da aplicação (opcionais, para armazenar registos de chat e telemetria)

Antes de criar o serviço QnA Maker, deve decidir qual dos escalões de serviços acima é adequada para si. 

Normalmente, existem três parâmetros, que precisa considerar:

1. **A taxa de transferência de que você precisa do serviço**: Selecione o [plano de aplicativo](https://azure.microsoft.com/pricing/details/app-service/plans/) apropriado para seu serviço de aplicativo com base em suas necessidades. Pode [aumentar verticalmente](https://docs.microsoft.com/azure/app-service/manage-scale-up) ou reduzir verticalmente a aplicação. Isso deve também influenciar a seleção de SKU de pesquisa do Azure, veja mais detalhes [aqui](https://docs.microsoft.com/azure/search/search-sku-tier).

1. **Tamanho e o número de bases de dados de conhecimento**: Escolha o [SKU do Azure Search](https://azure.microsoft.com/pricing/details/search/) apropriado para seu cenário. Pode publicar o bases de dados de conhecimento de n-1 numa camada específica, onde N é os índices máximos permitidos na camada. Também pode verificar o tamanho máximo e o número de documentos permitidas por camada.

    Por exemplo, se sua camada tiver 15 índices permitidos, você poderá publicar 14 bases de dados de conhecimento (1 índice por base de dados de conhecimento publicado). O índice décimo-quinto é usado para todas as bases de dados de conhecimento para criação e teste. 

1. **Número de documentos como fontes**: O SKU gratuito do serviço de gerenciamento de QnA Maker limita o número de documentos que você pode gerenciar por meio do portal e as APIs para 3 (de 1 MB de tamanho cada). O padrão de SKU não tem limites ao número de documentos, que pode gerir. Ver mais detalhes [aqui](https://aka.ms/qnamaker-pricing).

A tabela seguinte fornece algumas diretrizes de alto nível.

|                        | Gestão do QnA Maker | Serviço de Aplicações | Azure Search | Limitações                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Experimentação        | SKU gratuito             | Escalão Gratuito   | Escalão Gratuito    | Publicar até 2 KBs, tamanho de 50 MB  |
| Ambiente de desenvolvimento/teste   | SKU Standard         | Partilhado      | Básica        | Publicar até 14 KBs, tamanho de 2 GB    |
| Ambiente de produção | SKU Standard         | Básica       | Standard     | Publicar até 49 KBs, 25 GB de tamanho |

Para atualizar a sua pilha de QnA Maker, consulte [atualizar o serviço QnA Maker](../How-To/upgrade-qnamaker-service.md).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Atualizar o serviço QnA Maker](../How-To/upgrade-qnamaker-service.md)
