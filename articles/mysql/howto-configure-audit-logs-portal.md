---
title: Aceder a registos de auditoria - Portal Azure - Base de Dados Azure para o MySQL
description: Este artigo descreve como configurar e aceder aos registos de auditoria na Base de Dados Azure para o MySQL a partir do portal Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: c406fa6b49e800912edb5738b4d60596d828fc94
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91570495"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Configurar e aceder a registos de auditoria para Azure Database for MySQL no portal Azure

Pode configurar a [Base de Dados Azure para registos de auditoria do MySQL](concepts-audit-logs.md) e definições de diagnóstico a partir do portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para passar por este guia, precisa:

- [Base de Dados Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Configurar a exploração de auditoria

>[!IMPORTANT]
> Recomenda-se apenas registar os tipos de eventos e utilizadores necessários para os seus fins de auditoria para garantir que o desempenho do seu servidor não seja fortemente impactado.

Ativar e configurar a sessão de registo de auditoria.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione a base de dados Azure para o servidor MySQL.

1. Na secção **Definições** na barra lateral, selecione **os parâmetros do Servidor**.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/server-parameters.png" alt-text="Parâmetros do servidor":::

1. Atualize o parâmetro **audit_log_enabled** para ON.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-enabled.png" alt-text="Parâmetros do servidor":::

1. Selecione os [tipos de eventos](concepts-audit-logs.md#configure-audit-logging) a registar atualizando o parâmetro **audit_log_events.**
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-events.png" alt-text="Parâmetros do servidor":::

1. Adicione quaisquer utilizadores do MySQL a serem incluídos ou excluídos do registo, atualizando os parâmetros **audit_log_exclude_users** e **audit_log_include_users.** Especifique os utilizadores fornecendo o seu nome de utilizador MySQL.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png" alt-text="Parâmetros do servidor":::

1. Depois de alterar os parâmetros, pode clicar em **Guardar**. Ou pode **descartar as** suas alterações.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/save-parameters.png" alt-text="Parâmetros do servidor":::

## <a name="set-up-diagnostic-logs"></a>Configurar os registos de diagnóstico

1. Na secção **de monitorização** na barra lateral, selecione **definições de diagnóstico**.

1. Clique em "+ Adicionar definição de diagnóstico" :::image type="content" source="./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png" alt-text="Parâmetros do servidor" como o tipo de registo.
:::image type="content" source="./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png" alt-text="Parâmetros do servidor":::

1. Uma vez configurados os sumidouros de dados para canalizar os registos de auditoria, pode clicar em **Guardar**.
:::image type="content" source="./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png" alt-text="Parâmetros do servidor":::

1. Aceda aos registos de auditoria explorando-os nos sumidouros de dados que configura. Pode levar até 10 minutos para os registos aparecerem.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [os registos de auditoria](concepts-audit-logs.md) na Base de Dados Azure para o MySQL
- Saiba como configurar os registos de auditoria no [Azure CLI](howto-configure-audit-logs-cli.md)