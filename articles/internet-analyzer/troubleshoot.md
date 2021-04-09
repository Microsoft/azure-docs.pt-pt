---
title: Azure Internet Analyzer resolução de problemas | Microsoft Docs
description: A referência de resolução de problemas para O Analisador de Internet Azure.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: troubleshooting
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: fe173ec4d4f28444d43739ea2ed51e43021916b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "84744362"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Azure Internet Analyzer resolução de problemas

Este artigo contém etapas de resolução de problemas para problemas comuns do Analisador de Internet.

## <a name="things-to-keep-in-mind"></a>Aspetos a ter em conta
- O script do cliente deve ser incorporado num website **HTTPS.** As medições não serão recolhidas se o script funcionar num site simples **(http://)** ou local **(file://).**
- Os dados de medição só serão recolhidos se o script do perfil do Analisador de Internet tiver sido incorporado numa aplicação que esteja a receber tráfego real de utilizadores. O tráfego sintético (por exemplo, Azure WebApp Performance Tests) não executa tipicamente o código JavaScript incorporado, pelo que nenhuma medição será gerada por esse tipo de tráfego.

## <a name="azure-portal"></a>Portal do Azure
**"Não foi gerado um cartão de pontuação para a combinação de filtros selecionados" na secção Scorecards**
- Os cartões de pontuação são gerados diariamente (no final de cada dia, hora UTC).
- Os cartões de pontuação só são gerados se forem recolhidas mais de 100 medições para a combinação de filtros selecionada (Teste, Período de Tempo, País/Região, etc.).

**"Contagem total de medição" é zero para um ou ambos os pontos finais num teste**
- As séries de tempo e as contagens de medição são calculadas uma vez por hora, pelo que terá de esperar pelo menos esse tempo para que novos dados de medição apareçam.
- O Analisador de Internet conta apenas medições bem sucedidas (ou seja, respostas HTTP 200) para a sua análise. Se um ou ambos os pontos finais de um teste não forem inacessíveis ou devolverem um código HTTP não-200, aparecerão com zero medições totais.

## <a name="next-steps"></a>Passos seguintes
Leia o [Analisador de Internet FAQ](internet-analyzer-faq.md)
