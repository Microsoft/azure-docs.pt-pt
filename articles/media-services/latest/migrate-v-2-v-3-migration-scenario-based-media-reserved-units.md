---
title: Orientação de migração de Unidades Reservadas para Meios (MRUs)
description: Este artigo dá-lhe orientação baseada em cenários de MRU que o ajudará a migrar do Azure Media Services V2 para V3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 5ca01bcea348b866c0f40f82ebe90dc31d032739
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927068"
---
# <a name="media-reserved-units-mrus-scenario-based-migration-guidance"></a>Unidades reservadas de mídia (MRUs) orientação de migração baseada em cenários

![logotipo do guia de migração](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![etapas de migração 2](./media/migration-guide/steps-4.svg)

Este artigo dá-lhe orientação baseada em cenários de MRU que o ajudará a migrar do Azure Media Services V2 para V3.

- Para novas contas V3 criadas no portal Azure, ou com a versão 2020-05-01 da API V3, já não é obrigado a definir Unidades Reservadas de Mídia (MRUs). O sistema irá agora escalar automaticamente para cima e para baixo com base na carga.
- Se tiver uma conta V3 ou V2 criada antes da versão 2020-05-01 da API, ainda pode controlar a concordância e o desempenho dos seus trabalhos utilizando Unidades Reservadas de Meios. Para obter mais informações, veja Scaling Media Processing (Dimensionar o Processamento de Multimédia). Pode gerir as MRUs utilizando o CLI 2.0 para Serviços de Mídia V3 ou utilizando o portal Azure.  
- Se não vir a opção de gerir as MRUs no portal Azure, está a executar uma conta que foi criada com a API 2020-05-01 ou mais tarde.
- Se estiver familiarizado com a definição do seu tipo MRU para S3, o seu desempenho melhorará ou permanecerá o mesmo.
- Se é um cliente V2 existente, precisa de criar uma nova conta V2 para suportar a sua aplicação existente antes da conclusão da migração. 
- Indexer V1 ou outros processadores de mídia que não estejam totalmente precotados mas que possam ser ativados novamente. 

Para obter mais informações sobre as MRUs, consulte [unidades reservadas](concept-media-reserved-units.md) para meios de comunicação e [como escalar as unidades reservadas aos meios de comunicação.](media-reserved-units-cli-how-to.md)

## <a name="mru-concepts-tutorials-and-how-to-guides"></a>Conceitos de MRU, tutoriais e como guiar

### <a name="concepts"></a>Conceitos

[Unidades Reservadas aos Meios de Comunicação Social](concept-media-reserved-units.md)

### <a name="how-to-guides"></a>Guias de procedimentos

[Como escalar unidades reservadas para os meios de comunicação](media-reserved-units-cli-how-to.md)

## <a name="samples"></a>Amostras

Também pode [comparar o código V2 e V3 nas amostras de código](migrate-v-2-v-3-migration-samples.md).

## <a name="next-steps"></a>Próximos passos

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
