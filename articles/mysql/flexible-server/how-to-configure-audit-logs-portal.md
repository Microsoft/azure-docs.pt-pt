---
title: Configurar registos de auditoria - Portal Azure - Base de Dados Azure para MySQL - Servidor Flexível
description: Este artigo descreve como configurar e aceder aos registos de auditoria na Base de Dados Azure para o MySQL Flexible Server a partir do portal Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: cadd384bc7d3222cbc5d1179271f7b4624c9224a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91565892"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Configurar e aceder a registos de auditoria para Azure Database for MySQL - Servidor Flexível utilizando o portal Azure

> [!IMPORTANT]
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

Pode configurar a Base de Dados Azure para [registos](concepts-audit-logs.md) de auditoria do MySQL Flexible Server e definições de diagnóstico a partir do portal Azure.

## <a name="prerequisites"></a>Pré-requisitos
Os passos deste artigo requerem que tenha [um servidor flexível.](quickstart-create-server-portal.md)

## <a name="configure-audit-logging"></a>Configurar a exploração de auditoria

>[!IMPORTANT]
> Recomenda-se apenas registar os tipos de eventos e utilizadores necessários para os seus fins de auditoria para garantir que o desempenho do seu servidor não seja fortemente impactado.

Ativar e configurar a sessão de registo de auditoria.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione o seu servidor flexível.

1. Na secção **Definições** na barra lateral, selecione **os parâmetros do Servidor**.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/server-parameters.png" alt-text="Parâmetros do servidor":::

1. Atualize o parâmetro **audit_log_enabled** para ON.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/audit-log-enabled.png" alt-text="Parâmetros do servidor":::

1. Selecione os [tipos de eventos](concepts-audit-logs.md#configure-audit-logging) a registar atualizando o parâmetro **audit_log_events.**
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/audit-log-events.png" alt-text="Parâmetros do servidor":::

1. Adicione quaisquer utilizadores do MySQL a serem incluídos ou excluídos do registo, atualizando os parâmetros **audit_log_exclude_users** e **audit_log_include_users.** Especifique os utilizadores fornecendo o seu nome de utilizador MySQL.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/audit-log-exclude-users.png" alt-text="Parâmetros do servidor":::

1. Depois de alterar os parâmetros, pode clicar em **Guardar**. Ou pode **descartar as** suas alterações.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/save-parameters.png" alt-text="Parâmetros do servidor":::

## <a name="set-up-diagnostics"></a>Configurar diagnósticos

Os registos de auditoria são integrados com as definições de diagnóstico do Azure Monitor para permitir que canaliza os seus registos para registos do Monitor Azure, Centros de Eventos ou Armazenamento Azure.

1. Na secção **de monitorização** na barra lateral, selecione **definições de diagnóstico**.

1. Clique em "+ Adicionar definição de diagnóstico"  :::image type="content" source="./media/how-to-configure-audit-logs-portal/add-diagnostic-setting.png" alt-text="Parâmetros do servidor":::

1. Forneça um nome de definição de diagnóstico.

1. Especificar quais os destinos para enviar os registos de auditoria (conta de armazenamento, centro de eventos e/ou espaço de trabalho Log Analytics).

1. Selecione **MySqlAuditLogs** como o tipo de registo.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/configure-diagnostic-setting.png" alt-text="Parâmetros do servidor":::

1. Uma vez configurados os sumidouros de dados para canalizar os registos de auditoria, pode clicar em **Guardar**.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/save-diagnostic-setting.png" alt-text="Parâmetros do servidor":::

1. Aceda aos registos de auditoria explorando-os nos sumidouros de dados que configura. Pode levar até 10 minutos para os registos aparecerem.

Se canalizar os seus registos de auditoria para registos do Monitor Azure (Log Analytics), consulte algumas [consultas de amostra que](concepts-audit-logs.md#analyze-logs-in-azure-monitor-logs) pode utilizar para análise.  

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [registos de auditoria](concepts-audit-logs.md)
- Saiba mais sobre [registos de consulta lenta](concepts-slow-query-logs.md)
<!-- - Learn how to configure audit logs in the [Azure CLI](howto-configure-audit-logs-cli.md)-->