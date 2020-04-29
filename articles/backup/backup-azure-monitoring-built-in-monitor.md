---
title: Monitor Azure Backup trabalhos protegidos
description: Neste artigo, conheça as capacidades de monitorização e notificação das cargas de trabalho do Azure Backup utilizando o portal Azure.
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: de5a82f5ad1d8113b27c07484f2f08f4cf97c759
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80294928"
---
# <a name="monitoring-azure-backup-workloads"></a>Monitorização de cargas de trabalho de backup azure

O Azure Backup fornece múltiplas soluções de backup baseadas na exigência de backup e topologia de infraestrutura (On-premises vs Azure). Qualquer utilizador ou administrador de reserva deve ver o que se passa em todas as soluções e espera-se que seja notificado em cenários importantes. Este artigo detalha as capacidades de monitorização e notificação fornecidas pelo serviço de backup Azure.

## <a name="backup-jobs-in-recovery-services-vault"></a>Backup Jobs no cofre dos Serviços de Recuperação

O Azure Backup fornece capacidades de monitorização e alerta incorporadas para as cargas de trabalho protegidas pelo Azure Backup. Nas definições do cofre dos Serviços de Recuperação, a secção **de Monitorização** fornece empregos e alertas embutidos.

![Monitorização incorporada do cofre RS](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

Os trabalhos são gerados quando são executadas operações como configurar cópias de segurança, cópia de segurança, restaurar, eliminar a cópia de segurança, e assim por diante.

Os postos de trabalho das seguintes soluções Azure Backup são mostrados aqui:

- Cópias de segurança de VMs do Azure
- Backup do Ficheiro Azure
- Backup de carga de trabalho azure como SQL e SAP HANA
- Agente do Azure Backup (MAB)

Os trabalhos do System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) não são apresentados.

> [!NOTE]
> As cargas de trabalho azure, tais como os backups SQL e SAP HANA dentro dos VMs Azure, têm um grande número de trabalhos de backup. Por exemplo, as cópias de segurança de log podem ser executadas por cada 15 minutos. Assim, para tais cargas de trabalho DB, apenas são apresentadas operações acionadas pelo utilizador. As operações de backup programadas NÃO são apresentadas.

## <a name="backup-alerts-in-recovery-services-vault"></a>Alertas de backup no cofre dos Serviços de Recuperação

Os alertas são principalmente cenários em que os utilizadores são notificados para que possam tomar medidas relevantes. A secção **Backup Alerts** mostra alertas gerados pelo serviço de backup Azure. Estes alertas são definidos pelo serviço e o utilizador não pode criar quaisquer alertas personalizados.

### <a name="alert-scenarios"></a>Cenários de alerta

Os seguintes cenários são definidos pelo serviço como cenários alerta.

- Falhas de Cópia de Segurança/Restauro
- Cópia de segurança concluída com êxito com avisos para o Agente do Azure Backup (MAB)
- Parar a proteção com dados de retenção/Parar a proteção com eliminar dados

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Os alertas das seguintes soluções de Backup Azure são mostrados aqui

- Cópias de segurança de VMs do Azure
- Cópias de segurança dos Ficheiros do Azure
- Backups de carga de trabalho azure tais como SQL, SAP HANA
- Agente do Azure Backup (MAB)

> [!NOTE]
> Os alertas do System Center Data Protection Manager (SC-DPM), do Microsoft Azure Backup Server (MABS) não são apresentados aqui.

### <a name="consolidated-alerts"></a>Alertas Consolidados

Para soluções de backup de carga azure tais como SQL e SAP HANA, as cópias de segurança de log podem ser geradas com muita frequência (até 15 minutos de acordo com a apólice). Assim, também é possível que as falhas de backup de log também sejam muito frequentes (até cada 15 minutos). Neste cenário, o utilizador final ficará sobrecarregado se for levantado um alerta para cada ocorrência de falha. Assim, um alerta é enviado para a primeira ocorrência e se as falhas subsequentes forem devido à mesma causa raiz, então não são gerados mais alertas. O primeiro alerta é atualizado com a contagem de falhas. Mas se o alerta for inativado pelo utilizador, a próxima ocorrência irá desencadear outro alerta e este será tratado como o primeiro alerta para essa ocorrência. É assim que o Azure Backup realiza a consolidação de alerta para backups SQL e SAP HANA.

### <a name="exceptions-when-an-alert-is-not-raised"></a>Exceções quando um alerta não é levantado

Há poucas exceções quando um alerta não é levantado sobre um fracasso. São:

- O utilizador cancelou explicitamente o trabalho de funcionamento
- O trabalho falha porque outro trabalho de reserva está em andamento (nada para agir aqui, uma vez que temos que esperar que o trabalho anterior termine)
- O trabalho de backup vM falha porque o Azure VM apoiado já não existe
- [Alertas Consolidados](#consolidated-alerts)

As exceções acima referidas são concebidas a partir do entendimento de que o resultado destas operações (principalmente utilizadores desencadeados) aparece imediatamente nos clientes portais/PS/CLI. Assim, o utilizador está imediatamente ciente e não precisa de uma notificação.

### <a name="alert-types"></a>Tipos de alerta

Com base na gravidade do alerta, os alertas podem ser definidos em três tipos:

- **Crítico**: Em princípio, qualquer falha de backup ou recuperação (programada ou ativada pelo utilizador) levaria à geração de um alerta e seria mostrada como um alerta crítico e também operações destrutivas, tais como eliminar a cópia de segurança.
- **Aviso**: Se a operação de reserva for bem sucedida, mas com poucos avisos, são listados como alertas de aviso.
- **Informação**: A partir de hoje, nenhum alerta informativo é gerado pelo serviço de backup Azure.

## <a name="notification-for-backup-alerts"></a>Notificação de Alertas de Backup

> [!NOTE]
> A configuração da notificação só pode ser feita através do Portal Azure. O suporte do modelo de modelo ps/CLI/REST API/Azure Resource Manager não é suportado.

Uma vez levantado um alerta, os utilizadores são notificados. A Azure Backup fornece um mecanismo de notificação incorporado por e-mail. Pode especificar endereços de e-mail individuais ou listas de distribuição a serem notificados quando um alerta é gerado. Também pode escolher se deve ser notificado para cada alerta individual ou agrupar-se numa digestão de hora a hora e depois ser notificado.

![Notificação de e-mail incorporada do Cofre RS](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Quando a notificação estiver configurada, receberá um e-mail de boas-vindas ou introdutória. Isto confirma que o Azure Backup pode enviar e-mails para estes endereços quando um alerta é levantado.<br>

Se a frequência foi definida para uma digestão de hora a hora e um alerta foi levantado e resolvido dentro de uma hora, não fará parte da próxima digestão horária.

> [!NOTE]
>
> - Se for executada uma operação destrutiva como a proteção contra o **stop com dados de eliminação,** é levantado um alerta e enviado um e-mail para proprietários de subscrições, administradores e coadministradores, mesmo que as notificações não estejam configuradas para o cofre do Serviço de Recuperação.
> - Para configurar a notificação para trabalhos bem sucedidos, utilize [o Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="inactivating-alerts"></a>Alertas de inativação

Para inativar/resolver um alerta ativo, pode clicar no item da lista correspondente ao alerta que pretende inativar. Isto abre um ecrã que apresenta informações detalhadas sobre o alerta, com um botão 'Inactivate' na parte superior. Clicar neste botão alteraria o estado do alerta para 'Inactivo'. Também pode inativar um alerta clicando à direita no item da lista correspondente a esse alerta e selecionando 'Inactivar'.

![Inativação de alerta de cofre RS](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="next-steps"></a>Passos seguintes

[Monitor Azure cargas de trabalho de backup usando O Monitor Azure](backup-azure-monitoring-use-azuremonitor.md)
