---
title: Analise os padrões de utilização do Azure CDN [ Microsoft Docs
description: Este artigo descreve os diferentes tipos de relatórios de análise disponíveis para os produtos Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: magattus
ms.openlocfilehash: 238dea3c136daf13d3db7be41bed103a0cbf7636
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593675"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analisar padrões de utilização da CDN do Azure

Depois de ativar o CDN para a sua aplicação, pode monitorizar o uso do CDN, verificar a saúde da sua entrega e resolver potenciais problemas. O Azure CDN fornece estas capacidades das seguintes formas: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Análise de núcleo através de registos de diagnóstico azure

A análise do núcleo está disponível para pontos finais cdN para todos os níveis de preços. Os registos de diagnóstico azure permitem que a análise do núcleo seja exportada para armazenamento Azure, centros de eventos ou registos do Monitor Azure. Os registos do Azure Monitor oferecem uma solução com gráficos que são configuráveis e personalizáveis pelo utilizador. Para obter mais informações sobre os registos de diagnóstico do Azure, consulte os [registos de diagnóstico do Azure.](cdn-azure-diagnostic-logs.md)

## <a name="verizon-core-reports"></a>Relatórios do núcleo de Verizon

Como utilizador da Azure CDN com um **Padrão CDN Azure da Verizon** ou Do perfil De **Verizon,** pode ver relatórios centrais da Verizon no portal suplementar Verizon. Os relatórios principais da Verizon são acessíveis através da opção **Manage** a partir do portal Azure e oferecem uma variedade de gráficos e vistas. Para mais informações, consulte os [Relatórios Do Núcleo de Verizon.](cdn-analyze-usage-patterns.md)

## <a name="verizon-custom-reports"></a>Relatórios personalizados de Verizon

Como utilizador da Azure CDN com um **Padrão CDN Azure da Verizon** ou Do perfil De **Verizon,** pode ver relatórios personalizados da Verizon no portal suplementar Verizon. Os relatórios personalizados da Verizon são acessíveis através da opção **Gerir** a partir do portal Azure. A página de relatórios personalizados de Verizon mostra o número de acessos ou dados transferidos para cada cname de borda pertencente a um perfil De CDN Azure. Os dados podem ser agrupados pelo código de resposta HTTP ou estado de cache durante qualquer período de tempo. Para mais informações, consulte [Relatórios Personalizados de Verizon](cdn-verizon-custom-reports.md).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Azure CDN Premium dos relatórios Verizon

Com **o Azure CDN Premium da Verizon,** também pode aceder aos seguintes relatórios:
   * [Relatórios avançados do HTTP](cdn-advanced-http-reports.md)
   * [Estatísticas em tempo real](cdn-real-time-stats.md)
   * [Desempenho do nó edge](cdn-edge-performance.md)

