---
title: incluir ficheiro
description: incluir ficheiro
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 2/4/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: c3bb0c7eb6effeb5165eace565a9db5dac1d39d6
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553819"
---
| Recurso | Limite predefinido |
| --- | --- |
| Dados processados |1.000 TB/firewall/mês. <sup>1</sup> |
|Regras|10 000, todos regra combinado de tipos.|
|Global peering|Não suportado. Deve ter, pelo menos, uma implementação de firewall por região.|
|Tamanho mínimo de AzureFirewallSubnet |/26|
|Intervalo de portas nas regras de rede e da aplicação|0-64,000. Trabalho está em curso para reduzir esta limitação.|
|


<sup>1</sup>se precisar de aumentar estes limites, contacte o suporte do Azure.
