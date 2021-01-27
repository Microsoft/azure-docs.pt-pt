---
title: incluir ficheiro
description: incluir ficheiro
author: danielstocker
ms.service: azure-quantum
ms.topic: include
ms.date: 01/08/2021
ms.author: dasto
ms.openlocfilehash: c312ee63f9f00e4eef726924fc01f2862ba2884f
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920478"
---
### <a name="provider-limits--quota"></a>Quota de & limites de fornecedores

O Serviço Quântico Azure suporta prestadores de serviços de primeira e de terceiros. Os fornecedores de terceiros possuem os seus limites e quotas. Os utilizadores podem ver ofertas e limites no portal Azure ao configurar fornecedores de terceiros na lâmina do fornecedor. 

Pode encontrar os limites de quota publicados para o primeiro fornecedor de Soluções de Otimização da Microsoft abaixo. 

#### <a name="learn--develop-sku"></a>Aprenda & desenvolver SKU

| Recurso | Limite |
| --- | --- |
| Empregos simultâneos baseados na CPU | até 5 empregos simultâneos |
| Empregos simultâneos baseados na FPGA | até 2 empregos simultâneos |
| Horas de solucionador baseadas em CPU | 20 horas por mês  |
| Horas de solucionador baseadas na FPGA | 1 hora por mês  |

Se estiver a utilizar o Learn & Develop SKU, **não pode** solicitar um aumento dos seus limites de quota. Em vez disso, deve mudar para o Performance at Scale SKU.

#### <a name="performance-at-scale-sku"></a>Desempenho na Escala SKU

| Recurso | Limite |
| --- | --- |
| Empregos simultâneos baseados na CPU | até 100 empregos simultâneos |
| Empregos simultâneos baseados na FPGA | até 10 empregos simultâneos |
| Horas de solucionador | 50.000 horas por mês  |

Se precisar de solicitar um aumento de limite, contacte o Suporte Azure. 

Para mais informações, por favor reveja a [página de preços da Azure Quantum](https://aka.ms/AQ/Pricing).
Para obter informações sobre ofertas de terceiros, por favor reveja a página de fornecedor relevante no portal Azure.
