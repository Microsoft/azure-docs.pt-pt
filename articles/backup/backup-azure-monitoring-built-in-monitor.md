---
title: 'Cópia de segurança do Azure: Cargas de trabalho protegidas do monitor do Azure Backup'
description: Monitorizar cargas de trabalho de cópia de segurança do Azure através do portal do Azure
services: backup
author: pvrk
manager: shivamg
keywords: Cópia de segurança do Azure; Alertas;
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: pullabhk
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: 8d3e3257f16fe4e0f846c2268bfefc2771387de6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58112505"
---
# <a name="monitoring-azure-backup-workloads"></a>Cargas de trabalho de cópia de segurança do Azure

Cópia de segurança do Azure fornece várias soluções de cópia de segurança com base na topologia requisito e a infraestrutura de cópia de segurança (no local vs do Azure). Qualquer utilizador de cópia de segurança ou o administrador deverá ver o que está acontecendo em todas as soluções e espera-se ser notificado em cenários importantes. Este artigo fornece detalhes sobre as capacidades de monitorização e a notificação fornecidas pelo serviço de cópia de segurança do Azure.

## <a name="backup-jobs-in-recovery-services-vault"></a>Tarefas de cópia de segurança no cofre dos serviços de recuperação

O Azure Backup fornece incorporados de monitorização e alertas capacidades para cargas de trabalho a ser protegidas pelo Azure Backup. Nos serviços de recuperação cofre definições, o **monitorização** secção fornece incorporados tarefas e alertas.

![RS monitorização incorporadas do Cofre](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

Tarefas são geradas quando as operações como a configuração da cópia de segurança, cópia de segurança, restaurar, eliminar cópia de segurança e assim por diante, são executadas.

Tarefas a partir das seguintes soluções de cópia de segurança do Azure são mostradas aqui:

  - Cópias de segurança de VMs do Azure
  - Cópia de segurança de ficheiros do Azure
  - Cópia de segurança de cargas de trabalho do Azure, como o SQL
  - Agente do Azure Backup (MAB)

Tarefas do System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) não são apresentadas.

> [!NOTE]
> Cargas de trabalho do Azure como cópias de segurança SQL em VMs do Azure tem grande número de cópias de segurança. Por exemplo, os backups de log podem executar para cada 15 minutos. Por conseguinte, para cargas de trabalho desse tipo DB, operações de utilizador única acionada são apresentadas. Operações de cópia de segurança agendadas não são apresentadas.

## <a name="backup-alerts-in-recovery-services-vault"></a>Alertas de cópias de segurança no cofre dos serviços de recuperação

Os alertas são principalmente cenários onde os utilizadores são avisados para que eles possam agir relevantes. O **alertas de cópia de segurança** seção mostra alertas gerados pelo serviço de cópia de segurança do Azure. Estes alertas são definidos pelo serviço e o utilizador não personalizado, pode criar todos os alertas.

### <a name="alert-scenarios"></a>Cenários de alerta
Os cenários seguintes são definidos pelo serviço como cenários de alerta.

  - Falhas de cópia de segurança/restauro
  - Cópia de segurança foi concluída com êxito com avisos
  - Pare a proteção com reter dados/pare a proteção com eliminação de dados

### <a name="exceptions-when-an-alert-is-not-raised"></a>Exceções quando não for gerado um alerta
Existem algumas exceções quando não for gerado um alerta num caso de falha, eles são:

  - O utilizador cancelou explicitamente a tarefa em execução
  - A tarefa falha porque outra tarefa de cópia de segurança está em curso (nada para tomar decisões sobre aqui, uma vez que temos apenas aguardar o conclusão do trabalho anterior)
  - A tarefa de cópia de segurança da VM falhará porque a VM do Azure backup já não existe

As exceções acima sejam criadas, desde a compreensão que o resultado destas operações (principalmente utilizador acionada) aparece imediatamente em clientes de portal/PS/CLI. Por este motivo, o utilizador reconhece imediatamente e não necessita de uma notificação.

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Os alertas de soluções de cópia de segurança do Azure seguintes são mostrados aqui:

  - Cópias de segurança VM do Azure
  - Cópias de segurança de ficheiros do Azure
  - Cópias de segurança de cargas de trabalho do Azure, como o SQL
  - Agente do Azure Backup (MAB)

> [!NOTE]
> Os alertas do System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) não são apresentados aqui.

### <a name="alert-types"></a>Tipos de alerta
Com base na gravidade do alerta, alertas podem ser definidos em três tipos:

  - **Crítico**: Em princípio, qualquer cópia de segurança ou recuperação de falha (agendadas ou acionadas de usuário) poderia levar à geração de um alerta e seria mostrada como um alerta crítico e também operações destrutivas como eliminar cópia de segurança.
  - **Aviso**: Se a operação de cópia de segurança for concluída com êxito, mas com alguns avisos, estão listados como alertas de aviso.
  - **Informativa**: Até hoje, nenhum alerta informativo é gerada pelo serviço de cópia de segurança do Azure.

## <a name="notification-for-backup-alerts"></a>Notificação de alertas de cópia de segurança

> [!NOTE]
> Configuração de notificação pode ser feita apenas através do Portal do Azure. Suporte de modelo do CLI/PS/REST API/Azure Resource Manager não é suportado.

Assim que for gerado um alerta, os utilizadores são notificados. Cópia de segurança do Azure fornece um mecanismo de notificação incorporadas por email. Um pode especificar endereços de e-mail individuais ou listas de distribuição para ser notificado quando é gerado um alerta. Também pode escolher se pretende ser notificado de todos os alertas individuais ou agrupá-los num resumo de hora a hora e, em seguida, receba uma notificação.

![Notificação por e-mail RS cofre incorporadas](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Quando a notificação está configurada, receberá um e-mail de boas-vindos ou introdutório. Isto confirma que a cópia de segurança do Azure pode enviar e-mails para estes endereços de quando é desencadeado um alerta.<br>

Se a frequência foi definida para um resumo de hora a hora e um alerta foi emitido e resolvido dentro de uma hora, não é uma parte do resumo de hora a hora futura.

> [!NOTE]
> 
> * Se uma operação destrutiva, tal como **parar a proteção com eliminação de dados** é executada, é gerado um alerta e é enviado um e-mail para proprietários de subscrições, os administradores e coadministradores, mesmo que não as notificações estão configuradas para o serviço de recuperação cofre.
> * Para configurar a notificação para tarefas concluídas com êxito de utilização [do Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="next-steps"></a>Passos Seguintes

[Monitorizar a cópia de segurança cargas de trabalho através do Azure Monitor](backup-azure-monitoring-use-azuremonitor.md)
