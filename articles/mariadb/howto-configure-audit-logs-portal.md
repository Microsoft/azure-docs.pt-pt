---
title: Aceder a registos de auditoria - Portal Azure - Base de Dados Azure para MariaDB
description: Este artigo descreve como configurar e aceder aos registos de auditoria na Base de Dados Azure para MariaDB a partir do portal Azure.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/24/2020
ms.openlocfilehash: f1f3cda4ed9cdac6f6e03e88601b0a35d5c53faf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98663150"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>Configurar e aceder a registos de auditoria no portal Azure

Pode configurar a [Base de Dados Azure para registos de auditoria mariaDB](concepts-audit-logs.md) e definições de diagnóstico a partir do portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para passar por este guia, precisa:

- [Base de Dados Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Configurar a exploração de auditoria

>[!IMPORTANT]
> Recomenda-se apenas registar os tipos de eventos e utilizadores necessários para os seus fins de auditoria para garantir que o desempenho do seu servidor não seja fortemente impactado.

Ativar e configurar a sessão de registo de auditoria.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione a sua Base de Dados Azure para o servidor MariaDB.

1. Na secção **Definições** na barra lateral, selecione **os parâmetros do Servidor**.
    ![Parâmetros do servidor](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Atualize o parâmetro **audit_log_enabled** para ON.
    ![Ativar registos de auditoria](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Selecione os [tipos de eventos](concepts-audit-logs.md#configure-audit-logging) a registar atualizando o parâmetro **audit_log_events.**
    ![Eventos de registo de auditoria](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Adicione quaisquer utilizadores MariaDB a serem excluídos da exploração madeireira atualizando o parâmetro **audit_log_exclude_users.** Especifique os utilizadores fornecendo o seu nome de utilizador MariaDB.
    ![Registo de auditoria exclui utilizadores](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. Depois de alterar os parâmetros, pode clicar em **Guardar**. Ou pode **descartar as** suas alterações.
    ![Guardar](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Configurar os registos de diagnóstico

1. Na secção **de monitorização** na barra lateral, selecione **definições de diagnóstico**.

1. Clique em "+ Adicionar definição de diagnóstico" ![ Adicionar definição de diagnóstico](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Forneça um nome de definição de diagnóstico.

1. Especificar quais os dados que afundam para enviar os registos de auditoria (conta de armazenamento, centro de eventos e/ou espaço de trabalho Log Analytics).

1. Selecione "MySqlAuditLogs" como o tipo de registo.
![Configuração de diagnóstico configuração de configuração](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. Uma vez configurados os sumidouros de dados para canalizar os registos de auditoria, pode clicar em **Guardar**.
![Salvar a definição de diagnóstico](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Aceda aos registos de auditoria explorando-os nos sumidouros de dados que configura. Pode levar até 10 minutos para os registos aparecerem.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [os registos de auditoria](concepts-audit-logs.md) na Base de Dados Azure para MariaDB
- Saiba como configurar os registos de auditoria no [Azure CLI](howto-configure-audit-logs-cli.md)