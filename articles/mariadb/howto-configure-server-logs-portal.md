---
title: Aceder a registos de consulta lenta - Portal Azure - Base de Dados Azure para MariaDB
description: Este artigo descreve como configurar e aceder aos registos de consulta lenta na Base de Dados Azure para MariaDB a partir do portal Azure.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 4/13/2020
ms.openlocfilehash: c5ee948daecafc061910f36d2ac95d15338bfb38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662513"
---
# <a name="configure-and-access-azure-database-for-maria-db-slow-query-logs-from-the-azure-portal"></a>Configurar e aceder à Base de Dados de Azure para registos de consulta lenta de Maria DB a partir do portal Azure

Pode configurar, listar e baixar a [Base de Dados Azure para registos de consulta lenta mariaDB](concepts-server-logs.md) a partir do portal Azure.

## <a name="prerequisites"></a>Pré-requisitos
Os passos deste artigo requerem que tenha [a Base de Dados Azure para o servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md).

## <a name="configure-logging"></a>Confiria a exploração madeireira
Configure o acesso ao registo de consulta lenta. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione a sua Base de Dados Azure para o servidor MariaDB.

3. Sob a secção **de Monitorização** na barra lateral, selecione **registos do Servidor**. 
   ![Screenshot das opções de registos do Servidor](./media/howto-configure-server-logs-portal/1-select-server-logs-configure.png)

4. Para ver os parâmetros do servidor, **selecione Clique aqui para ativar registos e configurar parâmetros de registo**.

5. Ligue **slow_query_log** para **ON**.

6. Selecione para onde descodundo os registos para utilizar **log_output**. Para enviar registos para armazenamento local e registos de diagnóstico do Monitor Azure, selecione **Ficheiro**. 

7. Altere quaisquer outros parâmetros necessários. 

8. Selecione **Guardar**. 

   :::image type="content" source="./media/howto-configure-server-logs-portal/3-save-discard.png" alt-text="Screenshot de parâmetros de registo de consulta lenta e guardar.":::

A partir da página **'Parâmetros do Servidor',** pode voltar à lista de registos fechando a página.

## <a name="view-list-and-download-logs"></a>Ver lista e baixar registos
Após o início do registo, pode ver uma lista de registos de consulta lenta disponíveis e descarregar ficheiros de registo individuais. 

1. Abra o portal do Azure.

2. Selecione a sua Base de Dados Azure para o servidor MariaDB.

3. Sob a secção **de Monitorização** na barra lateral, selecione **registos do Servidor**. A página mostra uma lista dos seus ficheiros de registo.

   ![Screenshot da página de registos do Servidor, com lista de registos em destaque](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > A convenção de nomeação do registo é **mysql-slow-< o nome do seu servidor>-yyymmddh.log**. A data e hora utilizadas no nome do ficheiro é a hora em que o registo foi emitido. Os ficheiros de registo são rodados a cada 24 horas ou 7,5 GB, o que vier primeiro.

4. Se necessário, utilize a caixa de pesquisa para reduzir rapidamente a um registo específico, com base na data e hora. A procura está no nome do registo.

5. Para descarregar ficheiros de registo individuais, selecione o ícone de seta para baixo ao lado de cada ficheiro de registo na linha de tabela.

   ![Screenshot da página de registos do Servidor, com ícone de seta em baixo realçado](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Configurar os registos de diagnóstico

1. Sob a secção **de monitorização** na barra lateral, selecione **definições de diagnóstico** Adicione a  >  **definição de diagnóstico**.

   ![Screenshot das opções de definições de diagnóstico](./media/howto-configure-server-logs-portal/add-diagnostic-setting.png)

1. Forneça um nome de definição de diagnóstico.

1. Especificar quais os dados que afundam para enviar os registos de consulta lenta (conta de armazenamento, centro de eventos ou espaço de trabalho Log Analytics).

1. Selecione **MySqlSlowLogs** como o tipo de registo.
![Screenshot das opções de configuração de definições de diagnóstico](./media/howto-configure-server-logs-portal/configure-diagnostic-setting.png)

1. Depois de configurar os sumidouros de dados para canalizar os registos de consulta lenta para, **selecione Save**.
![Screenshot das opções de configuração de definições de diagnóstico, com o Save em destaque](./media/howto-configure-server-logs-portal/save-diagnostic-setting.png)

1. Aceda aos registos de consulta lenta explorando-os nos sumidouros de dados configurados. Pode levar até 10 minutos para os registos aparecerem.

## <a name="next-steps"></a>Passos seguintes
- Consulte [os registos de consulta lenta do Access no CLI](howto-configure-server-logs-cli.md) para aprender a descarregar registos de consulta lenta programáticamente.
- Saiba mais sobre [registos de consulta lenta](concepts-server-logs.md) na Base de Dados Azure para MariaDB.
- Para obter mais informações sobre as definições de parâmetros e registos, consulte a documentação MariaDB nos [registos](https://mariadb.com/kb/en/library/slow-query-log-overview/).