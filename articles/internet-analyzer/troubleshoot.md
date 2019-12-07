---
title: Solução de problemas do Azure Internet Analyzer | Microsoft Docs
description: A referência de solução de problemas do Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a7216e697680bcc049d2ceb4caec74adfc1760b0
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74897393"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Solução de problemas do Azure Internet Analyzer

Este artigo contém etapas para solução de problemas comuns do Internet Analyzer.

## <a name="azure-portal"></a>Portal do Azure
**"Não foram coletadas medições suficientes para este Scorecard" na seção scorecards**

Tenha em atenção que:
- Os dados de medição só serão coletados se o script de cliente do perfil do Internet Analyzer tiver sido inserido em um aplicativo que esteja recebendo tráfego de usuário real. O tráfego sintético (por exemplo, testes de desempenho do Azure WebApp) normalmente não executa código JavaScript inserido, portanto, nenhuma medida será gerada por esse tipo de tráfego.
- A série temporal é gerada uma vez por hora, portanto, você precisará aguardar pelo menos essa quantidade de tempo para que novos dados de medição sejam exibidos.
- Os scorecards são gerados diariamente (no final de cada dia, hora UTC).
- Os scorecards serão gerados somente se mais de 100 medidas forem coletadas para a combinação de filtro selecionada (teste, período de tempo, país, etc.).

## <a name="next-steps"></a>Passos seguintes
Leia as [perguntas frequentes do Internet Analyzer](internet-analyzer-faq.md)
