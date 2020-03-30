---
title: Tiroteio de problemas no Azure Internet Analyzer Microsoft Docs
description: A referência de resolução de problemas para o Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a265278652c16b4682707470d183a02a55b9a0ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77069222"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Tiroteio de problemas do Analisador de Internet Azure

Este artigo contém passos de resolução de problemas para questões comuns do Analisador de Internet.

## <a name="things-to-keep-in-mind"></a>Aspetos a ter em conta
- O script do cliente deve ser incorporado num website **HTTPS.** As medições não serão recolhidas se o script for executado num website de texto simples (**http://**) ou local (**file://).**
- Os dados de medição só serão recolhidos se o script do perfil do perfil do Analisador de Internet tiver sido incorporado numa aplicação que está a receber tráfego real de utilizadores. O tráfego sintético (por exemplo, O Azure WebApp Performance Tests) não executa normalmente o código Javascript incorporado, pelo que não serão geradas medições por esse tipo de tráfego.

## <a name="azure-portal"></a>Portal do Azure
**"Não foi gerado um cartão de pontuação para a combinação de filtro seletiva" na secção Scorecards**
- Os cartões de pontuação são gerados diariamente (no final de cada dia, hora utc).
- Os cartões de pontuação só são gerados se forem recolhidas mais de 100 medições para a combinação de filtros selecionada (Teste, Período de Tempo, País, etc.).

**"Contagem total de medição" é zero para um ou ambos os pontos finais num teste**
- As séries temporais e as contagens de medição são calculadas uma vez por hora, por isso terá de esperar pelo menos esse tempo para que novos dados de medição apareçam.
- O Analisador de Internet conta apenas medições bem sucedidas (isto é, respostas HTTP 200) para a sua análise. Se um ou ambos os pontos finais de um teste forem inacessíveis ou devolverem um código HTTP não-200, aparecerão com zero medições totais.

## <a name="next-steps"></a>Passos seguintes
Leia o [FaQ do Analisador](internet-analyzer-faq.md) de Internet
