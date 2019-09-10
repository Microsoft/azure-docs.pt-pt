---
title: incluir ficheiro
description: incluir ficheiro
services: backup
author: rayne-wiselman
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 9e9c09c1825f5c8383a708e8bd343146396f878e
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "67184659"
---
Os limites seguintes aplicam-se a cópia de segurança do Azure.

| **Limite** | **Predefinição** |
| --- | --- |
| Servidores ou computadores que podem ser registrados em um cofre. | Windows Server/cliente Windows/System Center Data Protection Manager: 50. <br/><br/> VMs IaaS: 1.000.  |
| Tamanho de uma fonte de dados no armazenamento do cofre. |54.400-GB máximo. O limite não se aplica ao backup de VM IaaS. |
| Cofres de backup em uma assinatura do Azure. |500 cofres por região. |
| Agendar backups diários. |Windows Server/cliente: Três por dia.<br/> System Center DPM: Dois por dia. <br/> VMs IaaS: Uma vez por dia.  |
| Discos de dados anexados a uma VM do Azure para backup. | 16 |
| Disco de dados individual anexado à VM do Azure para backup.| 4\.095 GB|
