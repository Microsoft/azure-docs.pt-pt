---
title: Solução de problemas do Azure Internet Analyzer | Microsoft Docs
description: A referência de solução de problemas do Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a265278652c16b4682707470d183a02a55b9a0ec
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069222"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Solução de problemas do Azure Internet Analyzer

Este artigo contém etapas para solução de problemas comuns do Internet Analyzer.

## <a name="things-to-keep-in-mind"></a>Coisas a ter em mente
- O script do cliente deve ser incorporado num website **HTTPS.** As medições não serão recolhidas se o script for executado num website de texto simples (**http://** ) ou local (**file://).**
- Os dados de medição só serão coletados se o script de cliente do perfil do Internet Analyzer tiver sido inserido em um aplicativo que esteja recebendo tráfego de usuário real. O tráfego sintético (por exemplo, testes de desempenho do Azure WebApp) normalmente não executa código JavaScript inserido, portanto, nenhuma medida será gerada por esse tipo de tráfego.

## <a name="azure-portal"></a>Portal do Azure
**"Não foi gerado um cartão de pontuação para a combinação de filtro seletiva" na secção Scorecards**
- Os scorecards são gerados diariamente (no final de cada dia, hora UTC).
- Os scorecards serão gerados somente se mais de 100 medidas forem coletadas para a combinação de filtro selecionada (teste, período de tempo, país, etc.).

**"Contagem total de medição" é zero para um ou ambos os pontos finais num teste**
- As séries temporais e as contagens de medição são calculadas uma vez por hora, por isso terá de esperar pelo menos esse tempo para que novos dados de medição apareçam.
- O Analisador de Internet conta apenas medições bem sucedidas (isto é, respostas HTTP 200) para a sua análise. Se um ou ambos os pontos finais de um teste forem inacessíveis ou devolverem um código HTTP não-200, aparecerão com zero medições totais.

## <a name="next-steps"></a>Passos seguintes
Leia o [FaQ do Analisador](internet-analyzer-faq.md) de Internet
