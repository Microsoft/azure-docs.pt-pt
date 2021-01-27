---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: include
ms.date: 01/12/2021
ms.author: wellee
ms.custom: include file
ms.openlocfilehash: 67a996dbe1eb7090ea5f9ee9f0880698f4ba74a3
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919075"
---
Esta secção descreve as diretrizes e requisitos para a atribuição de espaços de endereço do cliente onde a unidade de escala VPN Gateway do Hub VPN do Virtual WAN é maior ou igual a 40.

### <a name="background"></a>Fundo

As portas VPN ponto-a-local no centro Virtual Wan são implantadas com várias instâncias. Cada instância de um gateway VPN ponto-a-local pode suportar até 10.000 ligações de utilizador pontuais. Como resultado, para unidades de escala superiores a 40, o WAN Virtual precisa de implantar capacidade extra, o que requer um número mínimo de piscinas de endereços atribuídas para unidades de escala diferentes.

Por exemplo, se uma unidade de escala de 100 for escolhida, 5 instâncias são implantadas para o Gateway VPN ponto-a-local no Centro Virtual. Esta implementação pode suportar 50.000 ligações simultâneas e **pelo menos** 5 piscinas de endereços distintas.

**Unidades de escala disponíveis**

| Unidade de Escala | Clientes Máximos Suportados | Número mínimo de piscinas de endereços |
|--- |--- |--- |
| 40 | 20 000 | 2 |
| 60 | 30000 | 3 |
| 80 | 40000 | 4 |
| 100 | 50000 | 5 |
| 120 | 60000 | 6 |
| 140 | 70000 | 7 |
| 160 | 80000 | 8 |
| 180 | 90000 | 9 |
| 200 | 100000 | 10 |

### <a name="specifying-address-pools"></a>Especificar piscinas de endereços

Abaixo estão algumas diretrizes para escolher piscinas de endereços. Note que as atribuições de pool de endereço VPN ponto a local são feitas automaticamente por Parte Virtual WAN.

1. Uma instância de gateway permite um máximo de 10.000 ligações simultâneas. Como tal, cada conjunto de endereços deve conter pelo menos 10.000 endereços IP exclusivos DO RFC1918.
1. As gamas de conjuntos de endereços múltiplos são combinadas automaticamente e atribuídas a uma **única** instância de gateway. Este processo é feito de forma redonda para quaisquer instâncias de gateway que tenham menos de 10.000 endereços IP. Por exemplo, uma piscina com 5.000 endereços pode ser combinada automaticamente por Virtual Wan com outra piscina que tem 8.000 endereços e é atribuída a uma única instância de gateway.
1. Um único conjunto de endereços é atribuído apenas a uma única instância de gateway por Virtual WAN.
1. As piscinas de endereços devem ser distintas. Não pode haver sobreposição entre piscinas de endereços.

> [!NOTE] 
> Se um conjunto de endereços estiver associado a uma instância de gateway que está em manutenção, o conjunto de endereços não pode ser re-atribuído a outro caso.

### <a name="example"></a>Exemplo 

O exemplo a seguir descreve uma situação em que 60 unidades de escala suportam até 30.000 ligações, mas os pools de endereços atribuídos resultam em menos de 30.000 ligações simultâneas.

O número total de ligações simultâneas suportadas nesta configuração é de 28.192. A primeira instância de gateway suporta 10.000 endereços, a segunda instância 8.192 ligações, e a terceira instância também suporta 10.000 endereços.

| Número do número do pool de endereços | Piscina de Endereços | Conexões suportadas |
|--- |--- |--- |
| 1 | 10.12.0.0/15 | 10000 |
| 2 | 10.13.0.0/19 | 8192 |
| 3 | 10.14.0.0/15 | 10000|

**Recomendação #1: Certifique-se de que o Pool de Endereços #2 tem pelo menos 10.000 endereços IP distintos. (exemplo: 10.13.0.0/15)**

**Recomendação #2: Adicione mais um conjunto de endereços. (exemplo: Pool de endereços #4 10.15.0.0/21 com endereços 2048). O Endereço Pools 2 e 4 será combinado automaticamente e permitirá que a instância gateway suporte 10.000 ligações simultâneas.**
