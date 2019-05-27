---
title: Atualize o agente de cópia de segurança do Azure
description: Estas informações explicam por que deve atualizar o agente de cópia de segurança do Azure e onde pode transferir a atualização.
services: backup
cloud: ''
suite: ''
author: markgalioto
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 8/29/2018
ms.author: markgal
ms.openlocfilehash: 56310b7356dd9e263238234cf3e28bd498fa70fc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66127822"
---
## <a name="upgrade-the-mars-agent"></a>Atualize o agente de MARS

As versões do agente do serviço de recuperação do Azure (MARS) da Microsoft abaixo 2.0.9083.0 têm uma dependência no serviço de controlo de acesso do Azure (ACS). O agente de MARS é também referido como o agente de cópia de segurança do Azure. No Azure de 2018 [preterido o serviço de controlo de acesso do Azure (ACS)](../articles/active-directory/develop/active-directory-acs-migration.md). A partir de 19 de Março de 2018, todas as versões do agente MARS abaixo 2.0.9083.0 irão experienciar falhas de cópia de segurança. Para evitar ou resolver falhas de cópia de segurança, [atualizar o agente MARS para a versão mais recente](https://go.microsoft.com/fwlink/?linkid=229525). Para identificar os servidores que necessitam de uma atualização do agente MARS, siga os passos a [blog de cópia de segurança para atualizar agentes de MARS](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). O agente de MARS é utilizado para fazer cópias de segurança de ficheiros e pastas e dados de estado do sistema para o Azure. System Center DPM e o Azure Backup Server utilizam o agente de MARS para efetuar cópias de segurança de dados para o Azure.
