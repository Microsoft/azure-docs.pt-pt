---
title: Aceder a registos de consulta lenta - Portal Azure - Base de Dados Azure para MySQL
description: Este artigo descreve como configurar e aceder aos registos lentos na Base de Dados Azure para o MySQL a partir do portal Azure.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 3/15/2021
ms.openlocfilehash: 91569780aa71861e07c7e96bec5eac879642760d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103496223"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Configurar e aceder a registos de consulta lenta a partir do portal Azure

Pode configurar, listar e baixar a [Base de Dados Azure para registos de consulta lenta mySQL](concepts-server-logs.md) a partir do portal Azure.

## <a name="prerequisites"></a>Pré-requisitos
Os passos deste artigo requerem que tenha [a Base de Dados Azure para o servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md).

## <a name="configure-logging"></a>Confiria a exploração madeireira
Configure o acesso ao registo de consulta lenta MySQL. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione a base de dados Azure para o servidor MySQL.

3. Sob a secção **de Monitorização** na barra lateral, selecione **registos do Servidor**. 
   :::image type="content" source="./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png" alt-text="Screenshot das opções de registos do Servidor":::

4. Para ver os parâmetros do servidor, **selecione Clique aqui para ativar registos e configurar parâmetros de registo**.

5. Ligue **slow_query_log** para **ON**.

6. Selecione para onde descodundo os registos para utilizar **log_output**. Para enviar registos para armazenamento local e registos de diagnóstico do Monitor Azure, selecione **Ficheiro**.

7. Considere a definição de "long_query_time" que represente o limiar de tempo de consulta para as consultas que serão recolhidas no ficheiro de registo de consulta lenta, os valores mínimos e predefinidos de long_query_time são 0 e 10, respectivamente.

8. Ajuste outros parâmetros, tais como log_slow_admin_statements para registar declarações administrativas. Por padrão, as declarações administrativas não são registadas, nem são consultas que não usam índices para procuras. 

9. Selecione **Guardar**. 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="Screenshot de parâmetros de registo de consulta lenta e guardar.":::

A partir da página **'Parâmetros do Servidor',** pode voltar à lista de registos fechando a página.

## <a name="view-list-and-download-logs"></a>Ver lista e baixar registos
Após o início do registo, pode ver uma lista de registos de consulta lenta disponíveis e descarregar ficheiros de registo individuais.

1. Abra o portal do Azure.

2. Selecione a base de dados Azure para o servidor MySQL.

3. Sob a secção **de Monitorização** na barra lateral, selecione **registos do Servidor**. A página mostra uma lista dos seus ficheiros de registo.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/4-server-logs-list.png" alt-text="Screenshot da página de registos do Servidor, com lista de registos em destaque":::

   > [!TIP]
   > A convenção de nomeação do registo é **mysql-slow-< o nome do seu servidor>-yyymmddh.log**. A data e hora utilizadas no nome do ficheiro é a hora em que o registo foi emitido. Os ficheiros de registo são rodados a cada 24 horas ou 7,5 GB, o que vier primeiro. 

4. Se necessário, utilize a caixa de pesquisa para reduzir rapidamente a um registo específico, com base na data e hora. A procura está no nome do registo.

5. Para descarregar ficheiros de registo individuais, selecione o ícone de seta para baixo ao lado de cada ficheiro de registo na linha de tabela.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/5-download.png" alt-text="Screenshot da página de registos do Servidor, com ícone de seta em baixo realçado":::

## <a name="set-up-diagnostic-logs"></a>Configurar os registos de diagnóstico

1. Sob a secção **de monitorização** na barra lateral, selecione **definições de diagnóstico** Adicione as  >  **definições de diagnóstico**.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png" alt-text="Screenshot das opções de definições de diagnóstico":::

2. Forneça um nome de definição de diagnóstico.

3. Especificar quais os dados que afundam para enviar os registos de consulta lenta (conta de armazenamento, centro de eventos ou espaço de trabalho Log Analytics).

4. Selecione **MySqlSlowLogs** como o tipo de registo.
:::image type="content" source="./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png" alt-text="Screenshot das opções de configuração de definições de diagnóstico":::

5. Depois de configurar os sumidouros de dados para canalizar os registos de consulta lenta para, **selecione Save**.
:::image type="content" source="./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png" alt-text="Screenshot das opções de configuração de definições de diagnóstico, com o Save em destaque":::

6. Aceda aos registos de consulta lenta explorando-os nos sumidouros de dados configurados. Pode levar até 10 minutos para os registos aparecerem.

## <a name="next-steps"></a>Passos seguintes
- Consulte [Access slow consulta Logs in CLI](howto-configure-server-logs-in-cli.md) para aprender a descarregar registos de consulta lenta programáticamente.
- Saiba mais sobre [registos de consulta lenta](concepts-server-logs.md) na Base de Dados Azure para o MySQL.
- Para obter mais informações sobre as definições de parâmetros e o registo do MySQL, consulte a documentação mySQL nos [registos](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).