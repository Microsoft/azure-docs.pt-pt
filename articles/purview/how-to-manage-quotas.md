---
title: Gerir recursos e quotas
titleSuffix: Azure Purview
description: Conheça as quotas e limites de recursos para o Azure Purview e como solicitar aumentos de quota.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 57cb1c405e00acb346421d64190a71e9211d21ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96938839"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-purview"></a>Gerir e aumentar quotas para recursos com a Azure Purview
 
A Azure Purview é um serviço de cloud para utilização pelos utilizadores de dados. Você usa a Azure Purview para gerir centralmente a governação de dados em toda a sua propriedade de dados, abrangendo ambientes em nuvem e on-prem. O serviço permite que os analistas de negócios procurem dados relevantes utilizando termos de negócio significativos. Para elevar os limites até ao máximo para a sua subscrição, contacte o suporte.
 
## <a name="azure-purview-limits"></a>Limites do Azure Purview
 
|**Recurso**|  **Limite de predefinição**  |**Limite Máximo**|
|---|---|---|
|Contas de competência por região, por inquilino (todas as assinaturas combinadas)|3|Contactar o Suporte|
|vCores disponíveis para digitalização, por conta*|160|160|
|Exames simultâneos, por conta num dado ponto. O limite baseia-se no tipo de fontes de dados digitalizadas*|5 | 10 |
|Tempo máximo que uma varredura pode correr para|7 dias|7 dias|
|Chamadas API, por conta|10M APIs/mês para 4 unidades de capacidade tamanho da plataforma. <br>40M APIs/mês para 16 capacidades de tamanho da plataforma |10M APIs/mês para 4 unidades de capacidade tamanho da plataforma. <br>40M APIs/mês para 16 capacidades de tamanho da plataforma|
|Armazenamento, por conta|10 GB para o tamanho da plataforma unidades de capacidade de 4. <br>40 GB para o tamanho da plataforma unidades de capacidade de 16 |10 GB para o tamanho da plataforma unidades de capacidade de 4. <br> 40 GB para o tamanho da plataforma unidades de capacidade de 16 |
|Dimensão dos ativos por conta|100M ativos físicos |Contactar o Suporte|
|Tamanho máximo de um ativo em um catálogo|2 MB|2 MB|
|Comprimento máximo de nome e nome de classificação do ativo|4 KB|4 KB|
|Comprimento máximo do nome e valor da propriedade do ativo|32 KB|32 KB|
|Comprimento máximo de classificação denominação e valor|32 KB|32 KB|
|Número máximo de termos glossários, por conta|100K|100K|
 
*Os cenários de execução de integração auto-hospedados estão fora do âmbito dos limites definidos na tabela acima. 
 
## <a name="next-steps"></a>Passos seguintes
 
> [!div class="nextstepaction"]
>[Tutorial: Digitalizar dados com a azure Purview](tutorial-scan-data.md)

> [!div class="nextstepaction"]
>[Tutorial: Navegue na página inicial e procure um ativo](tutorial-asset-search.md)

> [!div class="nextstepaction"]
>[Tutorial: Navegue nos ativos e veja a sua linhagem](tutorial-browse-and-view-lineage.md)
