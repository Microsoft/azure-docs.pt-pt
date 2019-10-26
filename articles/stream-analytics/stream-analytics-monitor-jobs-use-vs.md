---
title: Monitorar e gerenciar Azure Stream Analytics com o Visual Studio
description: Este artigo descreve como usar o Visual Studio para monitorar e gerenciar trabalhos de Azure Stream Analytics.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 684f034393dd3f53900100dd964d9ea07d200897
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934965"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-visual-studio"></a>Monitorar e gerenciar trabalhos de Stream Analytics com o Visual Studio

Este artigo demonstra como monitorar seu trabalho de Stream Analytics no Visual Studio. O Azure Stream Analytics Tools para Visual Studio fornece uma experiência de monitoramento semelhante a portal do Azure sem precisar sair do IDE. Você pode começar a monitorar um trabalho assim que você **Enviar para o Azure** por meio de seu **script. asaql**, ou você pode monitorar os trabalhos existentes independentemente de como eles foram criados. 

## <a name="job-summary"></a>Resumo do trabalho

O **Resumo do trabalho** e as **métricas de trabalho** fornecem um instantâneo rápido de seu trabalho. Em um relance, você pode determinar as informações de status e evento de um trabalho.]

<img src="./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-job-summary-metrics.png" alt="Stream Analytics job summary and job metrics" width="300px"/> 


## <a name="job-metrics"></a>Métricas de trabalho

Você pode recolher o **Resumo do trabalho** e clicar na guia **métricas de trabalho** para exibir um grafo com métricas importantes. Marque e desmarque os tipos de métricas para adicioná-los e removê-los do grafo.

![Métricas de Stream Analytics no Visual Studio](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-metrics.png)


## <a name="error-monitoring"></a>Monitoramento de erro

Você também pode monitorar erros clicando na guia **erros** .

![Erros de Stream Analytics no Visual Studio](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-errors.png)


## <a name="get-support"></a>Obter suporte
Para obter mais assistência, experimente nosso [Fórum de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics). 

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Criar um trabalho de Azure Stream Analytics com o Visual Studio](stream-analytics-quick-create-vs.md)
* [Instalar o Azure Stream Analytics Tools para Visual Studio](stream-analytics-tools-for-visual-studio-install.md) 


