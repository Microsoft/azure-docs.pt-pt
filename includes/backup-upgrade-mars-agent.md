---
title: Atualizar o Agente de Backup Azure
description: Esta informação explica por que deve atualizar o Agente de Backup Azure e onde descarregar o upgrade.
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 8/29/2018
ms.author: dacurwin
ms.openlocfilehash: 227ec87081817b273b66684e31a0ebd0e11134a0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197130"
---
## <a name="upgrade-the-mars-agent"></a>Atualizar o Agente MARS

As versões do Agente de Recuperação do Microsoft Azure (MARS) abaixo de 2.0.9083.0 têm uma dependência do serviço de Controlo de Acesso Azure. O agente MARS também é referido como o Agente de Apoio Azure.

Em 2018, a Microsoft [deprecou o serviço de Controlo de Acesso Azure.](../articles/active-directory/azuread-dev/active-directory-acs-migration.md) A partir de 19 de março de 2018, todas as versões do Agente MARS abaixo de 2.0.9083.0 sofrerão falhas de backup. Para evitar ou resolver falhas de backup, [atualize o seu Agente MARS para a versão mais recente](https://go.microsoft.com/fwlink/?linkid=229525). Para identificar servidores que necessitem de uma atualização do Agente MARS, siga os passos no [blog Backup para atualizar agentes MARS](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/).

O Agente MARS é usado para fazer o back-up de ficheiros e pastas e dados do estado do sistema para o Azure. System Center DPM e Azure Backup Server usam o agente MARS para fazer cópias de segurança para o Azure.
