---
title: Registos de auditoria de acesso - Portal Azure - Base de Dados Azure para MariaDB
description: Este artigo descreve como configurar e aceder aos registos de auditoria na Base de Dados Azure para MariaDB a partir do portal Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: 506bf076c955beb5c5e57811bbdb42bfedb8cbe3
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382974"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>Configure e aceda aos registos de auditoria no portal Azure

Pode configurar a Base de [Dados Azure para registos](concepts-audit-logs.md) de auditoria MariaDB e definições de diagnóstico a partir do portal Azure.

> [!IMPORTANT]
> A funcionalidade de registo de auditoria encontra-se atualmente em pré-visualização.

## <a name="prerequisites"></a>Pré-requisitos

Para passar por este guia de como guiar, você precisa:

- [Base de Dados Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Configure a exploração madeireira de auditoria

Ativar e configurar a exploração de auditoria.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione a sua Base de Dados Azure para o servidor MariaDB.

1. Na secção **Definições** na barra lateral, selecione **parâmetros do Servidor**.
    ![Parâmetros do servidor](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Atualize o parâmetro **audit_log_enabled** para ON.
    ![Ativar registos de auditoria](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Selecione os tipos de [eventos](concepts-audit-logs.md#configure-audit-logging) a iniciar por atualização do parâmetro **audit_log_events.**
    ![Eventos de registo de auditoria](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Adicione qualquer utilizador MariaDB a ser excluído do registo através da atualização do parâmetro **audit_log_exclude_users.** Especifique os utilizadores fornecendo o seu nome de utilizador MariaDB.
    ![Registo de auditoria exclui utilizadores](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. Depois de ter alterado os parâmetros, pode clicar em **Guardar**. Ou pode **descartar** as suas alterações.
    ![Guardar](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Configurar os registos de diagnóstico

1. Na secção **de monitorização** na barra lateral, selecione **definições de diagnóstico**.

1. Clique em "+ Adicionar ![definição de diagnóstico" Adicione a definição de diagnóstico](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Forneça um nome de definição de diagnóstico.

1. Especifique quais os dados afundados para enviar os registos de auditoria (conta de armazenamento, centro de eventos e/ou log Analytics espaço de trabalho).

1. Selecione "MySqlAuditLogs" como o tipo de registo.
![Configurar a definição de diagnóstico](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. Depois de configurar os dados para canalizar os registos de auditoria, pode clicar em **Guardar**.
![Salvar a definição de diagnóstico](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Aceda aos registos de auditoria explorando-os nos sumidouros de dados configurados. Pode levar até 10 minutos para os registos aparecerem.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [registos](concepts-audit-logs.md) de auditoria na Base de Dados Azure para MariaDB
- Saiba como configurar registos de auditoria no [Azure CLI](howto-configure-audit-logs-cli.md)