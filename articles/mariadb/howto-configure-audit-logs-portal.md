---
title: Acessar logs de auditoria-portal do Azure-banco de dados do Azure para MariaDB
description: Este artigo descreve como configurar e acessar os logs de auditoria no banco de dados do Azure para MariaDB no portal do Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: e3c87143652895c57b464cba229a0e68049ffeb2
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767522"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>Configurar e acessar os logs de auditoria no portal do Azure

Você pode configurar o [banco de dados do Azure para MariaDB logs de auditoria](concepts-audit-logs.md) e configurações de diagnóstico do portal do Azure.

> [!IMPORTANT]
> A funcionalidade de log de auditoria está atualmente em visualização.

## <a name="prerequisites"></a>Pré-requisitos

Para percorrer este guia de instruções, você precisa de:

- [Banco de dados do Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Configurar o log de auditoria

Habilite e configure o log de auditoria.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione o banco de dados do Azure para o servidor MariaDB.

1. Na seção **configurações** na barra lateral, selecione **parâmetros do servidor**.
    ![Parâmetros do servidor](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Atualize o parâmetro **audit_log_enabled** como ativado.
    ![habilitar os logs de auditoria](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Selecione os [tipos de eventos](concepts-audit-logs.md#configure-audit-logging) a serem registrados atualizando o parâmetro **audit_log_events** .
    ![eventos do log de auditoria](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Adicione os usuários do MariaDB a serem excluídos do log atualizando o parâmetro **audit_log_exclude_users** . Especifique os usuários fornecendo seu nome de usuário MariaDB.
    ![log de auditoria excluir usuários](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. Depois de alterar os parâmetros, você pode clicar em **salvar**. Ou você pode **descartar** suas alterações.
    ![salvar](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Configurar os registos de diagnóstico

1. Na seção **monitoramento** na barra lateral, selecione **configurações de diagnóstico**.

1. Clique em "+ adicionar configuração de diagnóstico" ![adicionar configuração de diagnóstico](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Forneça um nome de configuração de diagnóstico.

1. Especifique quais coletores de dados enviar os logs de auditoria (conta de armazenamento, Hub de eventos e/ou espaço de trabalho Log Analytics).

1. Selecione "MySqlAuditLogs" como o tipo de log.
![definir a configuração de diagnóstico](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. Depois de configurar os coletores de dados para canalizar os logs de auditoria, você pode clicar em **salvar**.
![salvar configuração de diagnóstico](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Acesse os logs de auditoria explorando-os nos coletores de dados que você configurou. Pode levar até 10 minutos para que os logs sejam exibidos.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [os logs de auditoria](concepts-audit-logs.md) no banco de dados do Azure para MariaDB.