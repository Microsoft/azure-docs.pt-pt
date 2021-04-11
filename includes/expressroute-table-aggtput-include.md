---
title: incluir ficheiro
description: incluir ficheiro
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 12/05/2019
ms.author: duau
ms.custom: include file
ms.openlocfilehash: b1465b9bf855d592d195277b616c860a23c179f6
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504710"
---
| Gateway SKU | Ligações por segundo | Número de fluxos | Mega-bits por segundo | Pacotes por segundo | Largura de banda do circuito | Número de rotas anunciadas por gateway (para MSEE) | Número de rotas aprendidas por gateway (a partir do MSEE) | Número de VMs na rede virtual |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **SKU básico (precotado)** | N/D | N/D | 500 | N/D | N/D | N/D | N/D | N/D |
| **SKU/ErGw1AZ padrão** | 7,000 | 400,000 | >1.000 | >100.000 | 1 Gbps |  500 | 4000 | 2.000 (Reduzir para 1.000 durante a manutenção, restaura depois.) | 
| **SKU/ErGw2AZ de Alto Desempenho** | 14,000 | 840,000 | >2.000 | 250,000 | 1 Gbps | 500 | ~9.500 (Reduzir para 4.000 se mais de 6.500 VMs estiverem na rede virtual.) | 4500 |
| **Ultra Performance SKU/ErGw3AZ** | 16 000 | 950,000 | ~10.000 | 1 000 000 | 1 Gbps | 500 | ~9.500 | 11,000 |
