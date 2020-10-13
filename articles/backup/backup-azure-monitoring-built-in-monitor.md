---
title: Monitor Azure Backup cargas de trabalho protegidas
description: Neste artigo, conheça as capacidades de monitorização e notificação das cargas de trabalho de Backup Azure utilizando o portal Azure.
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: d04f57c19e31b946f7c360edb796bc4f0f5fcf71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89377408"
---
# <a name="monitoring-azure-backup-workloads"></a>Monitorização das cargas de trabalho de backup do Azure

O Azure Backup fornece múltiplas soluções de backup baseadas na necessidade de backup e topologia de infraestrutura (On-premis vs Azure). Qualquer utilizador ou administrador de backup deve ver o que se passa em todas as soluções e pode esperar ser notificado em cenários importantes. Este artigo detalha as capacidades de monitorização e notificação fornecidas pelo serviço Azure Backup.

## <a name="backup-jobs-in-recovery-services-vault"></a>Trabalhos de reserva no cofre dos Serviços de Recuperação

O Azure Backup fornece capacidades de monitorização e alerta incorporadas para as cargas de trabalho protegidas pelo Azure Backup. Nas definições do cofre dos Serviços de Recuperação, a secção **de monitorização** fornece empregos e alertas embutidos.

![Monitorização incorporada do cofre RS](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

Os trabalhos são gerados quando operações como configurar backup, backup, restaurar, eliminar backup, e assim por diante, são realizadas.

Os trabalhos das seguintes soluções Azure Backup são mostrados aqui:

- Cópias de segurança de VMs do Azure
- Backup de arquivo Azure
- Azure workload back up como SQL e SAP HANA
- Agente dos Serviços de Recuperação do Microsoft Azure (MARS)

Não são apresentados trabalhos do Gestor de Proteção de Dados do Centro de Sistema (SC-DPM), do Microsoft Azure Backup Server (MABS).

> [!NOTE]
> Cargas de trabalho azure como as cópias de segurança SQL e SAP HANA dentro dos VMs Azure têm um grande número de trabalhos de backup. Por exemplo, os backups de registo podem ser executados a cada 15 minutos. Assim, para tais cargas de trabalho DB, apenas são apresentadas operações acionadas pelo utilizador. As operações de reserva programadas não são apresentadas.

## <a name="backup-alerts-in-recovery-services-vault"></a>Alertas de backup no cofre dos Serviços de Recuperação

Os alertas são principalmente cenários em que os utilizadores são notificados para que possam tomar medidas relevantes. A secção **alertas de cópia** de segurança mostra alertas gerados pelo serviço Azure Backup. Estes alertas são definidos pelo serviço e o utilizador não pode criar alertas personalizados.

### <a name="alert-scenarios"></a>Cenários de alerta

Os seguintes cenários são definidos pelo serviço como cenários de alerta.

- Falhas de Cópia de Segurança/Restauro
- Backup conseguiu com avisos para o agente dos Serviços de Recuperação do Azure (MARS) da Microsoft Azure
- Parar a proteção com retenção de dados/impedir a proteção com a eliminação de dados

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Alertas das seguintes soluções Azure Backup são mostrados aqui

- Cópias de segurança de VMs do Azure
- Cópias de segurança dos Ficheiros do Azure
- Backups de carga de trabalho Azure tais como SQL, SAP HANA
- Agente dos Serviços de Recuperação do Microsoft Azure (MARS)

> [!NOTE]
> Os alertas do Gestor de Proteção de Dados do Centro de Sistema (SC-DPM), do Microsoft Azure Backup Server (MABS) não são apresentados aqui.

### <a name="consolidated-alerts"></a>Alertas Consolidados

Para soluções de backup de carga de trabalho Azure, como SQL e SAP HANA, os backups de registo podem ser gerados com muita frequência (até 15 minutos de acordo com a política). Assim, também é possível que as falhas de backup de registo também sejam muito frequentes (até a cada 15 minutos). Neste cenário, o utilizador final ficará sobrecarregado se for levantado um alerta para cada falha. Assim, um alerta é enviado para a primeira ocorrência e se as falhas posteriores são por causa da mesma causa raiz, então outros alertas não são gerados. O primeiro alerta é atualizado com a contagem de falhas. Mas se o alerta for inativado pelo utilizador, a próxima ocorrência irá desencadear outro alerta e este será tratado como o primeiro alerta para essa ocorrência. É assim que o Azure Backup realiza a consolidação de alerta para backups SQL e SAP HANA.

### <a name="exceptions-when-an-alert-is-not-raised"></a>Exceções quando um alerta não é levantado

Há poucas exceções quando um alerta não é levantado sobre um fracasso. A saber:

- O utilizador cancelou explicitamente o trabalho de execução
- O trabalho falha porque outro trabalho de reserva está em andamento (nada para agir aqui, uma vez que só temos que esperar que o trabalho anterior termine)
- O trabalho de backup VM falha porque o Azure VM apoiado já não existe
- [Alertas Consolidados](#consolidated-alerts)

As exceções acima são concebidas a partir do entendimento de que o resultado destas operações (principalmente o utilizador desencadeado) aparece imediatamente nos clientes portal/PS/CLI. Assim, o utilizador está imediatamente ciente e não precisa de uma notificação.

### <a name="alert-types"></a>Tipos de alerta

Com base na gravidade do alerta, os alertas podem ser definidos em três tipos:

- **Crítico**: Em princípio, qualquer falha de backup ou recuperação (programada ou despoletou o utilizador) levaria à geração de um alerta e seria mostrada como um alerta crítico e também operações destrutivas, como eliminar a cópia de segurança.
- **Aviso:** Se a operação de backup for bem sucedida, mas com poucos avisos, estão listados como alertas de aviso. Os alertas de aviso estão disponíveis apenas para cópias de segurança do Agente de Backup da Azure.
- **Informação :** Atualmente, nenhum alerta informativo é gerado pelo serviço de backup Azure.

## <a name="notification-for-backup-alerts"></a>Notificação para alertas de backup

> [!NOTE]
> A configuração da notificação só pode ser feita através do portal Azure. O suporte do modelo do gestor de recursos PS/CLI/REST não é suportado.

Uma vez levantado um alerta, os utilizadores são notificados. O Azure Backup fornece um mecanismo de notificação incorporado via e-mail. Pode-se especificar endereços de e-mail individuais ou listas de distribuição para ser notificado quando um alerta é gerado. Também pode escolher se deve ser notificado para cada alerta individual ou agrupar-se numa digestão de hora a hora e, em seguida, ser notificado.

![Notificação de e-mail incorporada RS Vault](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Quando a notificação estiver configurada, receberá um e-mail bem-vindo ou introdutório. Isto confirma que o Azure Backup pode enviar e-mails para estes endereços quando um alerta é levantado.<br>

Se a frequência foi definida para uma digestão de hora a hora e um alerta foi levantado e resolvido dentro de uma hora, não será parte da próxima digestão de hora a hora.

> [!NOTE]
>
> - Se uma operação destrutiva, como **a proteção de stop com os dados de eliminação,** for efetuada uma alerta e enviar um e-mail aos proprietários de subscrições, administradores e administradores, mesmo que as notificações não estejam configuradas para o cofre dos Serviços de Recuperação.
> - Para configurar a notificação para trabalhos bem sucedidos, utilize [o Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="inactivating-alerts"></a>Alertas inativos

Para inativar/resolver um alerta ativo, pode selecionar o item da lista correspondente ao alerta que pretende inativar. Isto abre um ecrã que exibe informações detalhadas sobre o alerta, com um botão **Inativado** na parte superior. A seleção deste botão alterará o estado do alerta para **Inativo**. Pode também inativar um alerta clicando à direita no item da lista correspondente a esse alerta e selecionando **Inativado**.

![Inativação de alerta RS Vault](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="next-steps"></a>Passos seguintes

[Monitorar cargas de trabalho de backup do Azure usando o Monitor Azure](backup-azure-monitoring-use-azuremonitor.md)
