---
title: Acesso a registos de consultas lentas - Portal Azure - Base de Dados Azure para MySQL
description: Este artigo descreve como configurar e aceder aos registos lentos na Base de Dados Azure para MySQL a partir do portal Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: 59faf63312bd7cc657f8b96ca3110707ea997c02
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81273614"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Configure e aceda a registos de consultas lentas do portal Azure

Pode configurar, listar e transferir a Base de [Dados Azure para registos de consultas lentas MySQL](concepts-server-logs.md) a partir do portal Azure.

## <a name="prerequisites"></a>Pré-requisitos
Os passos neste artigo exigem que tenha [base de dados Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md).

## <a name="configure-logging"></a>Configurar a exploração madeireira
Configure o acesso ao registo de consulta lenta MySQL. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione a sua Base de Dados Azure para o servidor MySQL.

3. Na secção **de monitorização** na barra lateral, selecione **registos do Servidor**. 
   ![Screenshot das opções de logs do Servidor](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Para ver os parâmetros do servidor, selecione **Clique aqui para ativar registos e configurar parâmetros**de registo .

5. Ligue **slow_query_log** para **ligar**.

6. Selecione onde fazer os registos para utilizar **log_output**. Para enviar registos tanto para o armazenamento local como para os registos de diagnóstico do Monitor Azure, selecione **File**. 

7. Mude os outros parâmetros necessários. 

8. Selecione **Guardar**. 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="Screenshot de parâmetros de registo de consulta lenta e guardar.":::

A partir da página Parâmetros do **Servidor,** pode voltar à lista de registos fechando a página.

## <a name="view-list-and-download-logs"></a>Ver registos de lista e de scarregamento
Após o início do registo, pode ver uma lista de registos de consultas lentas disponíveis e baixar ficheiros de registo individuais.

1. Abra o portal do Azure.

2. Selecione a sua Base de Dados Azure para o servidor MySQL.

3. Na secção **de monitorização** na barra lateral, selecione **registos do Servidor**. A página mostra uma lista dos seus ficheiros de registo.

   ![Screenshot da página de logs do Servidor, com lista de registos destacados](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > A convenção de nomeação do registo é **mysql-slow-< o seu nome de servidor>-yyymmddhh.log**. A data e a hora utilizadas no nome do ficheiro são a altura em que o registo foi emitido. Os ficheiros de registo são rodados a cada 24 horas ou 7,5 GB, o que vier primeiro. 

4. Se necessário, utilize a caixa de pesquisa para reduzir rapidamente a um registo específico, com base na data e hora. A procura está no nome do registo.

5. Para descarregar ficheiros de registo individuais, selecione o ícone de seta para baixo ao lado de cada ficheiro de registo na linha da tabela.

   ![Screenshot da página de logs do Servidor, com ícone de seta baixa realçado](./media/howto-configure-server-logs-in-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Configurar os registos de diagnóstico

1. Na secção **de monitorização** na barra lateral, selecione **Definições** > de diagnóstico**Adicione as definições de diagnóstico**.

   ![Screenshot das opções de definições de diagnóstico](./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png)

1. Forneça um nome de definição de diagnóstico.

1. Especifique quais os dados que afundam para enviar os registos de consulta lenta (conta de armazenamento, centro de eventos ou espaço de trabalho log Analytics).

1. Selecione **MySqlSlowLogs** como o tipo de registo.
![Screenshot das opções de configuração de configurações de diagnóstico](./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png)

1. Depois de configurar os afundamentos de dados para canalizar os registos de consulta lenta, selecione **Guardar**.
![Screenshot das opções de configuração de configurações de diagnóstico, com Guardar destacado](./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png)

1. Aceda aos registos de consulta lenta explorando-os nos sumidouros de dados configurados. Pode levar até 10 minutos para os registos aparecerem.

## <a name="next-steps"></a>Passos seguintes
- Consulte Acesso sessão de [consultas lentas no CLI](howto-configure-server-logs-in-cli.md) para aprender a descarregar registos de consultas lentas programáticamente.
- Saiba mais sobre os registos de [consultas lentas](concepts-server-logs.md) na Base de Dados Azure para mySQL.
- Para obter mais informações sobre as definições de parâmetros e a exploração madeireira MySQL, consulte a documentação MySQL nos [registos](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).