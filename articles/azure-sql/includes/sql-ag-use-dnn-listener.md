---
title: Utilize um ouvinte de rede distribuído (DNN) em vez de um ouvinte VNN para grupos de disponibilidade em VMs do Servidor SQL.
description: Mensagem para redirecionar os clientes para a utilização do ouvinte DNN em vez do ouvinte VNN.
ms.topic: include
author: MashaMSFT
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b4ea8428ae23537cde584bf6944732f65ebd2f26
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168002"
---
> [!NOTE]
> Os clientes no SQL Server 2019 CU8 e mais tarde no Windows 2016 e mais tarde podem substituir o ouvinte tradicional VNN e o Azure Load Balancer por um [ouvinte de rede distribuído (DNN).](../virtual-machines/windows/availability-group-distributed-network-name-dnn-listener-configure.md) Ignore os restantes passos deste artigo que criam o ouvinte e o equilibrador de carga.
