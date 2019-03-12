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
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554130"
---
Os limites seguintes aplicam-se a cópia de segurança do Azure.

| **Limite** | **Predefinição** |
| --- | --- |
| Servidores ou computadores que podem ser registados num cofre. | Windows Server/Windows Client/System Center Data Protection Manager: 50. <br/><br/> VMs de IaaS: 1,000.  |
| Tamanho de uma origem de dados no armazenamento do cofre. |54,400-GB no máximo. O limite não se aplica a cópia de segurança de VM de IaaS. |
| Cofres de cópia de segurança de uma subscrição do Azure. |500 cofres por região. |
| Agende cópias de segurança diárias. |Windows Server/cliente: Três por dia.<br/> O System Center DPM: Duas por dia. <br/> VMs de IaaS: Uma vez por dia.  |
| Discos de dados anexados a uma VM do Azure para cópia de segurança. | 16 |
| Disco de dados individuais ligado à VM do Azure para cópia de segurança.| 4095 GB|
