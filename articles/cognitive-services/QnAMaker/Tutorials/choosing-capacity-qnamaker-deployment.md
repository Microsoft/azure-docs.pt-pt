---
title: Capacidade de recursos para a implementação - QnA Maker
titleSuffix: Azure Cognitive Services
description: Antes de criar o serviço QnA Maker, deve decidir qual dos escalões de serviços acima é adequada para si.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 9e197929ce08f4e0c665f96d1c4ddbd382fdfb22
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084465"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Escolher a capacidade para a sua implementação do QnA Maker

O serviço QnA Maker, utiliza uma dependência em três recursos do Azure:
1.  Serviço de aplicações (para o tempo de execução)
2.  O Azure Search (para armazenar QnAs)
3.  Informações da aplicação (opcionais, para armazenar registos de chat e telemetria)

Antes de criar o serviço QnA Maker, deve decidir qual dos escalões de serviços acima é adequada para si. 

Normalmente, existem três parâmetros, que precisa considerar:
1. **O débito precisa do serviço**: selecione o adequado [plano de aplicação](https://azure.microsoft.com/pricing/details/app-service/plans/) para o serviço de aplicações com base nas suas necessidades. Pode [aumentar verticalmente](https://docs.microsoft.com/azure/app-service/web-sites-scale) ou reduzir verticalmente a aplicação. Isso deve também influenciar a seleção de SKU de pesquisa do Azure, veja mais detalhes [aqui](https://docs.microsoft.com/azure/search/search-sku-tier).

2. **Tamanho e o número de bases de dados de conhecimento**: escolha apropriado [SKU de pesquisa do Azure](https://azure.microsoft.com/pricing/details/search/) para o seu cenário. Pode publicar o bases de dados de conhecimento de n-1 numa camada específica, onde N é os índices máximos permitidos na camada. Também pode verificar o tamanho máximo e o número de documentos permitidas por camada.

3. **Número de documentos como fontes**: O SKU gratuito do serviço de gestão do QnA Maker limita o número de documentos, pode gerenciar através do portal e as APIs para 3 (de 1 MB de tamanho de cada). O padrão de SKU não tem limites ao número de documentos, que pode gerir. Ver mais detalhes [aqui](https://aka.ms/qnamaker-pricing).

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
