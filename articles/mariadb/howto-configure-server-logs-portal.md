---
title: Acesso a registos de consultas lentas - Portal Azure - Base de Dados Azure para MariaDB
description: Este artigo descreve como configurar e aceder aos registos de consulta lenta na Base de Dados Azure para MariaDB a partir do portal Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 89bdd209315445519c35f3ef2c2f1ad2555106ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531401"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Configure e aceda a registos de consultas lentas do portal Azure

Pode configurar, listar e transferir a Base de [Dados Azure para registos de consultas lentas MariaDB](concepts-server-logs.md) a partir do portal Azure.

## <a name="prerequisites"></a>Pré-requisitos
Os passos neste artigo exigem que tenha [base de dados Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md).

## <a name="configure-logging"></a>Configurar a exploração madeireira
Configure o acesso ao registo de consulta lenta. 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Selecione a sua Base de Dados Azure para o servidor MariaDB.

3. Na secção **de monitorização** na barra lateral, selecione **registos do Servidor**. 
   ![Screenshot das opções de logs do Servidor](./media/howto-configure-server-logs-portal/1-select-server-logs-configure.png)

4. Para ver os parâmetros do servidor, selecione **Clique aqui para ativar registos e configurar parâmetros**de registo .

5. Altere os parâmetros que precisa de ajustar, incluindo virar **slow_query_log** para **ON**. Todas as alterações que fizer nesta sessão são destacadas em roxo. 

   Depois de ter alterado os parâmetros, selecione **Guardar**. Ou pode descartar as suas mudanças.

   ![Screenshot das opções de parâmetros do servidor](./media/howto-configure-server-logs-portal/3-save-discard.png)

A partir da página Parâmetros do **Servidor,** pode voltar à lista de registos fechando a página.

## <a name="view-list-and-download-logs"></a>Ver registos de lista e de scarregamento
Após o início do registo, pode ver uma lista de registos de consultas lentas disponíveis e baixar ficheiros de registo individuais. 

1. Abra o portal do Azure.

2. Selecione a sua Base de Dados Azure para o servidor MariaDB.

3. Na secção **de monitorização** na barra lateral, selecione **registos do Servidor**. A página mostra uma lista dos seus ficheiros de registo.

   ![Screenshot da página de logs do Servidor, com lista de registos destacados](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > A convenção de nomeação do registo é **mysql-slow-< o seu nome de servidor>-yyymmddhh.log**. A data e a hora utilizadas no nome do ficheiro são a altura em que o registo foi emitido. Os ficheiros de registo são rodados a cada 24 horas ou 7,5 GB, o que vier primeiro.

4. Se necessário, utilize a caixa de pesquisa para reduzir rapidamente a um registo específico, com base na data e hora. A procura está no nome do registo.

5. Para descarregar ficheiros de registo individuais, selecione o ícone de seta para baixo ao lado de cada ficheiro de registo na linha da tabela.

   ![Screenshot da página de logs do Servidor, com ícone de seta baixa realçado](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Configurar os registos de diagnóstico

1. Na secção **de monitorização** na barra lateral, selecione **Definições** > de diagnóstico**Adicione a definição de diagnóstico**.

   ![Screenshot das opções de definições de diagnóstico](./media/howto-configure-server-logs-portal/add-diagnostic-setting.png)

1. Forneça um nome de definição de diagnóstico.

1. Especifique quais os dados que afundam para enviar os registos de consulta lenta (conta de armazenamento, centro de eventos ou espaço de trabalho log Analytics).

1. Selecione **MySqlSlowLogs** como o tipo de registo.
![Screenshot das opções de configuração de configurações de diagnóstico](./media/howto-configure-server-logs-portal/configure-diagnostic-setting.png)

1. Depois de configurar os afundamentos de dados para canalizar os registos de consulta lenta, selecione **Guardar**.
![Screenshot das opções de configuração de configurações de diagnóstico, com Guardar destacado](./media/howto-configure-server-logs-portal/save-diagnostic-setting.png)

1. Aceda aos registos de consulta lenta explorando-os nos sumidouros de dados configurados. Pode levar até 10 minutos para os registos aparecerem.

## <a name="next-steps"></a>Passos seguintes
- Consulte [registos de consultas lentas de Acesso no CLI](howto-configure-server-logs-cli.md) para aprender a descarregar registos de consultas lentas programáticamente.
- Saiba mais sobre os registos de [consultas lentas](concepts-server-logs.md) na Base de Dados Azure para MariaDB.
- Para obter mais informações sobre as definições de parâmetros e o registo, consulte a documentação mariaDB nos [registos.](https://mariadb.com/kb/en/library/slow-query-log-overview/)