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
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66154785"
---
Abrir uma porta ou criar um ponto de extremidade, a uma máquina virtual (VM) no Azure através da criação de um filtro de rede numa sub-rede ou uma interface de rede VM. Estes filtros que controlam o tráfego de entrada e saído, coloca num grupo de segurança de rede anexado ao recurso que recebe o tráfego.

O exemplo neste artigo demonstra como criar um filtro de rede que utiliza a porta TCP 80 (presume-se já a utilizar os serviços apropriados e aberto quaisquer regras de firewall do SO na VM) padrão.

Depois de criar uma VM que está configurada para atender solicitações da web na porta TCP padrão 80, pode:

1. Crie um grupo de segurança de rede.

2. Criar uma regra de segurança de entrada a permitir o tráfego e atribuir valores para as seguintes definições:

   - **Intervalos de portas de destino**: 80

   - **Intervalos de portas de origem**: * (permite que qualquer porta de origem)

   - **Valor de prioridade**: Introduza um valor que é menor do que a regra de entrada de negação de 65,500 e superior numa prioridade de que não o predefinido catch-all.

3. Associe o grupo de segurança de rede com a interface de rede VM ou a sub-rede.

Embora esse exemplo usa uma regra simples para permitir o tráfego HTTP, também pode utilizar grupos de segurança de rede e regras para criar configurações de rede mais complexas. 




