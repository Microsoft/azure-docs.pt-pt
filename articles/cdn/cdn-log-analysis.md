---
title: Analisar padrões de utilização da CDN do Azure | Documentos da Microsoft
description: Este artigo descreve os diferentes tipos de relatórios de análise disponíveis para produtos da CDN do Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: magattus
ms.openlocfilehash: ef713c954d6eab05259547a277db12a1e9036bcf
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56650551"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analisar padrões de utilização da CDN do Azure

Depois de ativar a CDN para a sua aplicação, pode monitorizar a utilização da CDN, verificar o estado de funcionamento da sua entrega e resolver problemas potenciais. A CDN do Azure oferece estas capacidades das seguintes formas: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Análise principal através de registos de diagnóstico do Azure

Análise principal está disponível para pontos finais da CDN para todos os escalões de preço. Análise principal a ser exportados para o armazenamento do Azure, os hubs de eventos de permitir que os registos de diagnóstico do Azure ou registos do Azure Monitor. Registos de Monitor do Azure oferece uma solução com gráficos configurável pelo utilizador e personalizável. Para obter mais informações sobre os registos de diagnóstico do Azure, consulte [registos de diagnóstico do Azure](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Relatórios de núcleos da Verizon

Como um utilizador de CDN do Azure com um **CDN do Azure Standard da Verizon** ou **CDN do Azure Premium da Verizon** perfil, pode ver relatórios de núcleos da Verizon no portal suplementar Verizon. Relatórios de núcleos da Verizon está acessível através da **gerir** opção do portal do Azure e oferece uma variedade de gráficos e vistas. Para obter mais informações, consulte [relatórios de núcleos da Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Relatórios personalizados da Verizon

Como um utilizador de CDN do Azure com um **CDN do Azure Standard da Verizon** ou **CDN do Azure Premium da Verizon** perfil, pode ver relatórios personalizados da Verizon no portal suplementar Verizon. Relatórios personalizados da Verizon está acessível através da **gerir** opção do portal do Azure. A página de relatórios personalizados da Verizon mostra o número de resultados ou dados transferido para cada extremidade CName que pertencem a um perfil de CDN do Azure. Os dados podem ser agrupados por Estado de código ou cache de resposta HTTP durante qualquer período de tempo. Para obter mais informações, consulte [relatórios personalizados da Verizon](cdn-verizon-custom-reports.md).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Relatórios do Azure CDN Premium da Verizon

Com o **CDN do Azure Premium da Verizon**, também pode acessar os seguintes relatórios:
   * [Relatórios HTTP avançados](cdn-advanced-http-reports.md)
   * [Estatísticas em tempo real](cdn-real-time-stats.md)
   * [Desempenho do nó edge](cdn-edge-performance.md)

