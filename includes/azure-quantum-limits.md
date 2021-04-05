---
title: incluir ficheiro
description: incluir ficheiro
author: danielstocker
ms.service: azure-quantum
ms.topic: include
ms.date: 01/08/2021
ms.author: dasto
ms.openlocfilehash: 2106a48a583f120f8b4dde4eb32a30f1a1b1d85b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98947207"
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

| Recurso | Limite Predefinido | Limite Máximo |
| --- | --- | --- |
| Empregos simultâneos baseados na CPU | até 100 empregos simultâneos | mesmo que limite de incumprimento |
| Empregos simultâneos baseados na FPGA | até 10 empregos simultâneos | mesmo que limite de incumprimento |
| Horas de solucionador | 1.000 horas por mês  | até 50.000 horas por mês |

Se precisar de solicitar um aumento de limite, contacte o Suporte Azure. 

Para mais informações, por favor reveja a [página de preços da Azure Quantum](https://aka.ms/AQ/Pricing).
Para obter informações sobre ofertas de terceiros, por favor reveja a página de fornecedor relevante no portal Azure.
