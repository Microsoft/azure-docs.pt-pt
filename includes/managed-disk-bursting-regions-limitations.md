---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 01/04/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 5658b68081fae8dab528030f7fc1cd6fe4935730
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102178364"
---
- A explosão a pedido não pode ser ativada num SSD premium que tenha menos de 512 GiB. Os SSDs premium com menos de 512 GiB utilizarão sempre a explosão baseada no crédito.
- A explosão a pedido só é suportada em SSDs premium. Se um SSD premium com rebentamento a pedido ativado for comutado para outro tipo de disco, então a rutura do disco é desativada.
- A explosão a pedido não se desativa automaticamente quando o nível de desempenho é alterado. Se quiser alterar o seu nível de desempenho mas não quiser manter o disco a rebentar, tem de o desativar.
- A explosão a pedido só pode ser ativada quando o disco é desligado de um VM ou quando o VM é parado. A explosão a pedido pode ser desativada 12 horas após a sua ativação.