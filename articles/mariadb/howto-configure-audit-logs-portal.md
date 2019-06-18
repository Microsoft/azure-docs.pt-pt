---
title: Configurar e aceder aos registos de auditoria da base de dados do Azure para MariaDB no portal do Azure
description: Este artigo descreve como configurar e aceder os registos de auditoria na base de dados do Azure para MariaDB do portal do Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: c9ee106972cc78a08709d2ce55d2dfddc96edbf7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079239"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>Configurar e aceder aos registos de auditoria no portal do Azure

Pode configurar o [base de dados do Azure para MariaDB registos de auditoria](concepts-audit-logs.md) e definições de diagnóstico do portal do Azure.

> [!IMPORTANT]
> Funcionalidade de registo de auditoria está atualmente em pré-visualização.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia de procedimentos, terá de:

- [Base de dados do Azure para MariaDB server](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Configurar o registo de auditoria

Ativar e configurar o registo de auditoria.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione a sua base de dados do Azure para MariaDB server.

1. Sob o **configurações** secção na barra lateral, selecione **parâmetros do servidor**.
    ![Parâmetros do servidor](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Atualização do **audit_log_enabled** parâmetro on.
    ![Ativar registos de auditoria](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Selecione os eventos de ter sessão iniciada ao atualizar o **audit_log_events** parâmetro.
    ![Eventos de registo de auditoria](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Adicione algum usuário MariaDB a excluir de registo ao atualizar o **audit_log_exclude_users** parâmetro. Especifique os utilizadores ao fornecer o respetivo nome de utilizador MariaDB.
    ![Utilizadores de exclusão de log de auditoria](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. Assim que tiver alterado os parâmetros, pode clicar em **guardar**. Ou pode **descartar** as suas alterações.
    ![Guardar](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Configurar os registos de diagnóstico

1. Sob o **monitorização** secção na barra lateral, selecione **das definições de diagnóstico**.

1. Clique em "+ Adicionar definição de diagnóstico" ![Adicionar definição de diagnóstico](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Forneça um nome de definição de diagnóstico.

1. Especifique a Coletores de que os dados para enviar os registos de auditoria (conta de armazenamento, hub de eventos, e/ou área de trabalho do Log Analytics).

1. Selecione "MySqlAuditLogs" como o tipo de registo.
![Configurar a definição de diagnóstico](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. Assim que tiver configurado para encaminhar os registos de auditoria para os sinks de dados, pode clicar em **guardar**.
![Guardar a definição de diagnóstico](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Explorar os mesmos no sinks de dados que configurou para aceder os registos de auditoria. Poderá demorar até 10 minutos para os registos a aparecer.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [registos de auditoria](concepts-audit-logs.md) na base de dados do Azure para MariaDB.