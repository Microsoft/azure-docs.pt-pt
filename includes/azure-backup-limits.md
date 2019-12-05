---
title: incluir ficheiro
description: incluir ficheiro
services: backup
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: ca6c57b139b195b94dc7171336cfcc34d18b1872
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74829032"
---
Os limites seguintes aplicam-se a cópia de segurança do Azure.

| **Limite** | **Predefinição** |
| --- | --- |
| Servidores ou computadores que podem ser registrados em um cofre. | Windows Server/cliente Windows/System Center Data Protection Manager: 50. <br/><br/> VMs IaaS: 1.000.  |
| Tamanho de uma fonte de dados no armazenamento do cofre. |54.400-GB máximo. O limite não se aplica ao backup de VM IaaS. |
| Cofres de backup em uma assinatura do Azure. |500 cofres por região. |
| Agendar backups diários. |Windows Server/cliente: três dias.<br/> System Center DPM: dois dias. <br/> VMs IaaS: uma vez por dia.  |
| Discos de dados anexados a uma VM do Azure para backup. | 16 |
| Disco de dados individual anexado à VM do Azure para backup.| 32 TB|
