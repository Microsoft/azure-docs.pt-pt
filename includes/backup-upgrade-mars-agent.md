---
title: Atualizar o agente de backup Azure
description: Esta informação explica por que deve atualizar o agente de backup Azure e onde descarregar o upgrade.
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
ms.openlocfilehash: c22fbcd07286ddffedd8fc2fdc12017b9338d7f7
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161861"
---
## <a name="upgrade-the-mars-agent"></a>Atualizar o agente MARS

As versões do agente do Microsoft Azure Recovery Service (MARS) abaixo de 2.0.9083.0 têm uma dependência do serviço de Controlo de Acesso Azure (ACS). O agente MARS também é referido como o agente De reserva Azure. Em 2018, o Azure deprecou o serviço de Controlo de [Acesso Azure (ACS)](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). A partir de 19 de março de 2018, todas as versões do agente MARS abaixo de 2.0.9083.0 sofrerão falhas de backup. Para evitar ou resolver falhas de backup, [atualize o seu agente MARS para a versão mais recente](https://go.microsoft.com/fwlink/?linkid=229525). Para identificar servidores que necessitem de uma atualização do agente MARS, siga os passos no [blog Backup para atualizar os agentes MARS](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). O agente MARS é usado para fazer o back-up de ficheiros e pastas e os dados do estado do sistema para o Azure. System Center DPM e Azure Backup Server usam o agente MARS para fazer cópias de segurança para o Azure.
