---
title: Configurar registos de consulta lenta - Portal Azure - Base de Dados Azure para MySQL - Servidor Flexível
description: Este artigo descreve como configurar e aceder aos registos lentos na Base de Dados Azure para o MySQL Flexible Server a partir do portal Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 1416efaa325ced623b5c7514f2f9953dc0bd2781
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940496"
---
# <a name="configure-and-access-slow-query-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Configurar e aceder a registos de consulta lenta para Azure Database for MySQL - Servidor Flexível utilizando o portal Azure

> [!IMPORTANT]
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

Pode configurar, listar e baixar a Base de Dados Azure para [os registos de consulta lenta](concepts-slow-query-logs.md) do MySQL Flexible Server a partir do portal Azure.

## <a name="prerequisites"></a>Pré-requisitos
Os passos deste artigo requerem que tenha [um servidor flexível.](quickstart-create-server-portal.md)

## <a name="configure-logging"></a>Confiria a exploração madeireira
Configure o acesso ao registo de consulta lenta MySQL. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione o seu servidor flexível.

1. Na secção **Definições** na barra lateral, selecione **os parâmetros do Servidor**.
   <!-- :::image type="content" source="./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png" alt-text="Screenshot of Server logs options":::-->

1. Atualize o parâmetro **slow_query_log** para **ON**.

1. Altere quaisquer outros parâmetros necessários (ex. `long_query_time`, `log_slow_admin_statements`). Consulte os [registos de consulta lenta](./concepts-slow-query-logs.md#configure-slow-query-logging) para obter mais parâmetros.  

1. Selecione **Guardar**. 

   <!-- :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="Screenshot of slow query log parameters and save."::: -->

A partir da página **'Parâmetros do Servidor',** pode voltar à lista de registos fechando a página.

## <a name="set-up-diagnostics"></a>Configurar diagnósticos

Os registos de consulta lenta são integrados com as definições de diagnóstico do Azure Monitor para permitir que encanize os seus registos em registos do Monitor Azure, nos Centros de Eventos ou no Armazenamento Azure.

1. Sob a secção **de monitorização** na barra lateral, selecione **definições de diagnóstico**Adicione as  >  **definições de diagnóstico**.

   <!--:::image type="content" source="./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png" alt-text="Screenshot of Diagnostic settings options":::-->

1. Forneça um nome de definição de diagnóstico.

1. Especificar quais os destinos para enviar os registos de consulta lenta (conta de armazenamento, centro de eventos ou espaço de trabalho Log Analytics).

1. Selecione **MySqlSlowLogs** como o tipo de registo.
    <!--:::image type="content" source="./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png" alt-text="Screenshot of Diagnostic settings configuration options":::-->

1. Depois de configurar os sumidouros de dados para canalizar os registos de consulta lenta para, **selecione Save**.
    <!--:::image type="content" source="./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png" alt-text="Screenshot of Diagnostic settings configuration options, with Save highlighted":::-->

1. Aceda aos registos de consulta lenta explorando-os nos sumidouros de dados configurados. Pode levar até 10 minutos para os registos aparecerem.

Se canalizar os seus registos de auditoria para registos do Monitor Azure (Log Analytics), consulte algumas [consultas de amostra que](concepts-audit-logs.md#analyze-logs-in-azure-monitor-logs) pode utilizar para análise. 

## <a name="next-steps"></a>Passos seguintes
<!-- - See [Access slow query Logs in CLI](howto-configure-server-logs-in-cli.md) to learn how to download slow query logs programmatically.-->
- Saiba mais sobre [registos de consulta lenta](concepts-slow-query-logs.md)
- Para obter mais informações sobre as definições de parâmetros e o registo do MySQL, consulte a documentação mySQL nos [registos](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).
- Saiba mais [sobre registos de auditoria](concepts-audit-logs.md)