---
title: Utilize um ouvinte de rede distribuído (DNN) em vez de um ouvinte VNN para grupos de disponibilidade em VMs do Servidor SQL.
description: Mensagem para redirecionar os clientes para a utilização do ouvinte DNN em vez do ouvinte VNN.
ms.topic: include
author: MashaMSFT
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b4ea8428ae23537cde584bf6944732f65ebd2f26
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92168002"
---
> [!NOTE]
> Os clientes no SQL Server 2019 CU8 e mais tarde no Windows 2016 e mais tarde podem substituir o ouvinte tradicional VNN e o Azure Load Balancer por um [ouvinte de rede distribuído (DNN).](../virtual-machines/windows/availability-group-distributed-network-name-dnn-listener-configure.md) Ignore os restantes passos deste artigo que criam o ouvinte e o equilibrador de carga.
