---
title: Zona de análise espacial e colocação de linha
titleSuffix: Azure Cognitive Services
description: Saiba como configurar zonas e linhas com Análise Espacial
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: 7e2a64c14d7d7a1d20b64b746969aca1e60ab218
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "90939410"
---
# <a name="zone-and-line-placement-guide"></a>Guia de colocação de zona e linha

Este artigo fornece orientações para como definir zonas e linhas para operações de análise espacial para obter uma análise precisa dos movimentos dos povos num espaço. Isto aplica-se a todas as operações. 

As zonas e linhas são definidas utilizando o parâmetro JSON SPACEANALYSIS_CONFIG. Consulte o artigo [de operações de análise espacial](spatial-analysis-operations.md) para obter mais informações.

## <a name="guidelines-for-drawing-zones"></a>Orientações para zonas de desenho

Lembre-se que cada espaço é diferente; terá de atualizar a posição ou o tamanho, dependendo das suas necessidades.

Se quiser ver uma secção específica da sua vista para a câmara, crie a maior zona que puder, cobrindo a área específica do piso em que está interessado, mas não incluindo outras áreas em que não está interessado. Isto aumenta a precisão dos dados recolhidos e evita falsos positivos de áreas que não quer rastrear. Tenha cuidado ao colocar os cantos do seu polígono e certifique-se de que não estão fora da área que deseja seguir.  

### <a name="example-of-a-well-shaped-zone"></a>Exemplo de uma zona bem moldada

A zona deve ser grande o suficiente para acomodar três pessoas em pé ao longo de cada borda e focada na área de interesse. A análise espacial identificará pessoas cujos pés são colocados na zona, por isso, ao desenhar zonas na imagem 2D, imagine a zona como um tapete deitado no chão.

![Zona bem moldada](./media/spatial-analysis/zone-good-example.png)

### <a name="examples-of-zones-that-arent-well-shaped"></a>Exemplos de zonas que não estão bem moldadas

Os exemplos a seguir mostram zonas mal moldadas. Nestes exemplos, a área de interesse é o espaço em frente à exibição *it's Game Time.*

**A zona não está no chão.**

![Zona mal moldada](./media/spatial-analysis/zone-not-on-floor.png) 

**A zona é muito pequena.**

![zona é muito pequena](./media/spatial-analysis/zone-too-small.png)

**A zona não captura totalmente a área à volta do ecrã.**

![zona não captura totalmente a área em torno da tampa final](./media/spatial-analysis/zone-bad-capture.png)

**A zona está muito perto da borda da imagem da câmara e não captura o ecrã certo.**

![zona é muito perto da borda da imagem da câmara e não captura o ecrã certo](./media/spatial-analysis/zone-edge.png)

**A zona está parcialmente bloqueada pela prateleira, por isso as pessoas e o chão não estão totalmente visíveis.**

![zona está parcialmente bloqueada, para que as pessoas não sejam totalmente visíveis](./media/spatial-analysis/zone-partially-blocked.png)

### <a name="example-of-a-well-shaped-line"></a>Exemplo de uma linha bem em forma

A linha deve ser longa o suficiente para acomodar toda a entrada. A análise espacial identificará pessoas cujos pés cruzam a linha, por isso, quando desenhar linhas na imagem 2D, imagine que está a desenhá-las como se estivessem no chão. 

Se possível, estenda a linha mais larga do que a entrada real. Se isto não resultar em cruzamentos extra (como na imagem abaixo quando a linha está contra uma parede) então estenda-a.

![Linha bem em forma](./media/spatial-analysis/zone-line-good-example.png)

### <a name="examples-of-lines-that-arent-well-shaped"></a>Exemplos de linhas que não estão bem moldadas

Os exemplos a seguir mostram linhas mal definidas.

**A linha não cobre toda a entrada no chão.**

![A linha não cobre toda a entrada no chão.](./media/spatial-analysis/zone-line-bad-coverage.png)

**A linha é muito alta e não cobre toda a porta.**

![A linha é muito alta e não cobre a totalidade da porta.](./media/spatial-analysis/zone-line-too-high.png)

## <a name="next-steps"></a>Passos seguintes

* [Implementar uma aplicação web People Counting](spatial-analysis-web-app.md)
* [Configure operações de análise espacial](./spatial-analysis-operations.md)
* [Registo e resolução de problemas](spatial-analysis-logging.md)
* [Guia de colocação de câmera](spatial-analysis-camera-placement.md)
