---
title: Atualizar o agente de backup do Azure
description: Essas informações explicam por que você deve atualizar o agente de backup do Azure e onde baixar a atualização.
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
ms.openlocfilehash: ea1295b08aa77a3e1a03d944ddbcbf37b6d17702
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71251543"
---
## <a name="upgrade-the-mars-agent"></a>Atualizar o agente MARS

As versões do agente MARS (serviço de recuperação de Microsoft Azure) abaixo do 2.0.9083.0 têm uma dependência do serviço de controle de acesso do Azure (ACS). O agente MARS também é conhecido como o agente de backup do Azure. No 2018, [o Azure preteriu o serviço de controle de acesso (ACS) do Azure](../articles/active-directory/develop/active-directory-acs-migration.md). A partir de 19 de março de 2018, todas as versões do agente MARS abaixo de 2.0.9083.0 apresentarão falhas de backup. Para evitar ou resolver falhas de backup, [atualize seu agente Mars para a versão mais recente](https://go.microsoft.com/fwlink/?linkid=229525). Para identificar os servidores que exigem uma atualização de agente MARS, siga as etapas no [blog de backup para atualizar agentes Mars](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). O agente MARS é usado para fazer backup de arquivos e pastas e dos dados de estado do sistema no Azure. O System Center DPM e o Servidor de Backup do Azure usam o agente MARS para fazer backup de dados no Azure.
