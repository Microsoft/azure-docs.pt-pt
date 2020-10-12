---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 09/12/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ec6cbcbc93fe87634c87caeb0041b75ec916a22f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184229"
---
Abre uma porta, ou cria um ponto final, para uma máquina virtual (VM) em Azure, criando um filtro de rede numa sub-rede ou numa interface de rede VM. Coloca-se estes filtros, que controlam o tráfego de entrada e saída, num grupo de segurança de rede ligado ao recurso que recebe o tráfego.

O exemplo neste artigo demonstra como criar um filtro de rede que utiliza a porta TCP padrão 80 (presume-se que já iniciou os serviços apropriados e abriu quaisquer regras de firewall de OS no VM).

Depois de criar um VM configurado para servir pedidos web na porta TCP padrão 80, você pode:

1. Criar um grupo de segurança de rede.

2. Criar uma regra de segurança de entrada que permite o tráfego e atribuir valores às seguintes definições:

   - **Gamas portuárias de destino**: 80

   - **Gamas porta-fonte :*** (permite qualquer porta de origem)

   - **Valor prioritário**: Introduza um valor inferior a 65.500 valores e superior em prioridade do que a regra de entrada padrão de catch-all.

3. Associe o grupo de segurança da rede com a interface ou sub-rede de rede VM.

Embora este exemplo utilize uma regra simples para permitir o tráfego HTTP, também pode utilizar grupos e regras de segurança de rede para criar configurações de rede mais complexas. 




