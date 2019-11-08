---
title: Visão geral do Azure FarmBeats
description: Fornece uma visão geral do Azure FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 12882860b8a9beadd4e56cbb151d670fac4da43b
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73798698"
---
# <a name="overview-of-azure-farmbeats"></a>Visão geral do Azure FarmBeats

O Azure FarmBeats é uma coleção de recursos e serviços do Azure, projetada para ajudá-lo a criar rapidamente soluções inteligentes controladas por dados em agricultura. O Azure FarmBeats é uma oferta do Azure Marketplace que permite adquirir, agregar e processar dados relacionados a agricultura de várias fontes, como sensores, drones, câmeras, satélite, sem investir em recursos de engenharia de dados aprofundados.

> [!NOTE]
> O Azure FarmBeats está atualmente em visualização pública. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). O Azure FarmBeats é fornecido sem um contrato de nível de serviço. Use o [Fórum do Azure FarmBeats](https://aka.ms/FarmBeatsMSDN ) para obter suporte.

Usando o Azure FarmBeats, você poderá obter dados de várias fontes, como sensores, satélite, drones, tudo no contexto de um farm (uma área geográfica de interesse).

Você poderá:

- Agregar conjuntos de agricultural de vários provedores
- Compile rapidamente modelos de ia/ML (inteligência artificial/linguagem de máquina) ao meio da fusíveis de diferentes conjuntos de os

O Azure FarmBeats fornece uma maneira robusta e fácil de fazer o seguinte:

- Crie um mapa para o farm usando o arquivo geojson.
- Avalie a integridade do farm usando o índice de vegetação e o índice de água com base em imagens de satélite.
- Obtenha recomendações sobre quantos sensores usar e onde colocá-los.
- Acompanhe a condição do farm Visualizando dados de aterramento coletados por sensores de vários fornecedores de sensor.
- Obtenha o mapa de umidade de solo com base na fusão de dados de sensor e satélite.
- Obter informações acionáveis criando modelos de ia/ML com base em conjuntos de valores agregados.
- Crie ou aumente sua solução de agricultura digital fornecendo conselhos de integridade do farm.

Os componentes do Azure FarmBeat são discutidos nas seções a seguir deste artigo.

## <a name="data-hub"></a>Hub de dados

Uma camada de API, que habilita a agregação, a normalização e a condefinição de vários conjuntos de valores de agricultura entre provedores. A partir desta versão prévia, você pode aproveitar dois provedores de sensores [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/), [Teralytic](https://teralytic.com/), um provedor [de imagens de satélite Sentinel-2](https://sentinel.esa.int/web/sentinel/home)e dois provedores de imagens de drone [senseFly](https://www.sensefly.com/) , [SlantRange](https://slantrange.com/). O Hub de dados foi projetado como uma plataforma de API e estamos trabalhando com muitos outros provedores para integrar com o Azure FarmBeats, para que você tenha mais opções ao criar sua solução.

## <a name="accelerator"></a>Accelerator

Uma solução de exemplo, criada com base no Hub de dados, que impulsiona a interface do usuário e o desenvolvimento do modelo. Esse aplicativo Web aproveita as APIs para demonstrar a visualização de dados de sensor ingeridos como gráficos e visualização de saída de modelo como mapas. Por exemplo, você pode usar o acelerador para criar rapidamente um farm e obter facilmente um mapa de índice vegetação ou um mapa de posicionamento de sensor para esse farm.

## <a name="resources"></a>Recursos

Visite o [blog](https://aka.ms/AzureFarmBeats) e os [fóruns](https://aka.ms/FarmBeatsMSDN)do FarmBeats.

## <a name="next-steps"></a>Passos seguintes

Para começar a usar o Azure FarmBeats, visite o [Azure Marketplace](https://aka.ms/FarmBeatsMarketplace) para implantar.
