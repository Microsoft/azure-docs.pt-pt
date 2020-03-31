---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 09/12/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ec6cbcbc93fe87634c87caeb0041b75ec916a22f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184229"
---
Abre uma porta, ou cria um ponto final, para uma máquina virtual (VM) em Azure, criando um filtro de rede numa subnet ou numa interface de rede VM. Coloca estes filtros, que controlam o tráfego de entrada e saída, num grupo de segurança da rede ligado ao recurso que recebe o tráfego.

O exemplo neste artigo demonstra como criar um filtro de rede que utiliza a porta Padrão TCP 80 (é assumido que já iniciou os serviços apropriados e abriu quaisquer regras de firewall solado no VM).

Depois de criar um VM configurado para servir pedidos web na porta padrão de TCP 80, pode:

1. Crie um grupo de segurança de rede.

2. Criar uma regra de segurança de entrada que permita o tráfego e atribuir valores às seguintes definições:

   - **Gamas de porta de destino**: 80

   - **Gamas**de porta de origem: * (permite qualquer porta de origem)

   - **Valor prioritário**: Introduza um valor inferior a 65.500 e superior em prioridade do que a regra de entrada por defeito.

3. Associe o grupo de segurança da rede com a interface ou sub-rede vM.

Embora este exemplo utilize uma regra simples para permitir o tráfego HTTP, também pode usar grupos e regras de segurança de rede para criar configurações de rede mais complexas. 




