---
ms.service: hpc-cache
ms.topic: include
ms.date: 03/15/2021
author: ekpgh
ms.author: v-erkel
ms.openlocfilehash: 2804b3bb36a31c9cfbf4cce8db94b7469c8975c7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563248"
---
| Modelo de utilização | Modo caching | Verificação de back-end | Atraso máximo de desatrada |
|--|--|--|--|
| Leia escritos pesados e pouco frequentes | Ler | Nunca | Nenhum |
| Mais de 15% escreve | Leitura/escrita | 8 horas | 20 minutos |
| Os clientes contornam a cache | Ler | 30 segundos | Nenhum |
| Mais de 15% de escritos, verificação frequente de back-end (30 segundos) | Leitura/escrita | 30 segundos | 20 minutos |
| Mais de 15% de escritos, verificação frequente de back-end (60 segundos) | Leitura/escrita | 60 segundos | 20 minutos |
| Mais de 15% escreve, frequentemente write-back | Leitura/escrita | 30 segundos | 30 segundos |
| Leia pesado, verificando o servidor de apoio a cada 3 horas | Ler | 3 horas | Nenhum |
