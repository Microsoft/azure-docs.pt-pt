---
title: Atualize o Agente de Backup Azure
description: Esta informação explica por que deve atualizar o Agente de Backup Azure e onde fazer o download da atualização.
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 03/03/2020
ms.author: dacurwin
ms.openlocfilehash: bd298f758d6109b908db01dd2ae3b97e5e2f714a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "78673196"
---
## <a name="upgrade-the-mars-agent"></a>Atualizar o Agente MARS

As versões do Agente microsoft Azure Recovery Services (MARS) abaixo de 2.0.9083.0 têm uma dependência do serviço Azure Access Control. O agente MARS também é referido como o Agente reserva Azure.

Em 2018, a Microsoft [depreendeu o serviço Azure Access Control](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). A partir de 19 de março de 2018, todas as versões do Agente MARS abaixo de 2.0.9083.0 irão sofrer falhas de backup. Para evitar ou resolver falhas de backup, [atualize o seu Agente MARS para a versão mais recente](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent). Para identificar servidores que necessitem de uma atualização do Agente MARS, siga os passos no [upgrade do agente Microsoft Azure Recovery Services (MARS).](../articles/backup/upgrade-mars-agent.md)

O Agente MARS é utilizado para fazer o back-up de ficheiros e pastas e os dados do estado do sistema para o Azure. O Sistema Center DPM e o Azure Backup Server utilizam o Agente MARS para fazer cópias de segurança para o Azure.
