---
title: Configurar e acessar logs de servidor para o banco de dados do Azure para MariaDB no portal do Azure
description: Este artigo descreve como configurar e acessar os logs de servidor no banco de dados do Azure para MariaDB no portal do Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: c8be9519d3393330b3022fadd2de6a49e58ecdcf
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703506"
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Configurar e acessar os logs de servidor no portal do Azure

Você pode configurar, listar e baixar o [banco de dados do Azure para MariaDB logs de consulta lentos](concepts-server-logs.md) do portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para percorrer este guia de instruções, você precisa de:
- [Banco de dados do Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Configurar registro em log
Configure o acesso ao log de consultas lentas. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione o banco de dados do Azure para o servidor MariaDB.

3. Na seção **monitoramento** na barra lateral, selecione **logs do servidor**. 
   logs do servidor ![Select, clique para configurar @ no__t-1

4. Selecione o título **clique aqui para habilitar logs e configurar parâmetros de log** para ver os parâmetros do servidor.

5. Altere os parâmetros que você precisa ajustar, incluindo a ativação de "slow_query_log" como "ON". Todas as alterações feitas nesta sessão são realçadas em roxo. 

   Depois de alterar os parâmetros, você pode clicar em **salvar**. Ou você pode **descartar** suas alterações.

   ![Clique em salvar ou descartar](./media/howto-configure-server-logs-portal/3-save-discard.png)

6. Retorne à lista de logs clicando no **botão fechar** (ícone X) na página **parâmetros do servidor** .

## <a name="view-list-and-download-logs"></a>Exibir lista e baixar logs
Depois que o log for iniciado, você poderá exibir uma lista de logs de consultas lentas disponíveis e baixar arquivos de log individuais no painel logs do servidor. 

1. Abra o portal do Azure.

2. Selecione o banco de dados do Azure para o servidor MariaDB.

3. Na seção **monitoramento** na barra lateral, selecione **logs do servidor**. A página mostra uma lista de seus arquivos de log, conforme mostrado:

   ![Lista de logs](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > A Convenção de nomenclatura do log é **MySQL-lento-< o nome do servidor >-yyyymmddhh. log**. A data e a hora usadas no nome de arquivo é a hora em que o log foi emitido. Os arquivos de log são girados a cada 24 horas ou 7,5 GB, o que vier primeiro.

4. Se necessário, use a **caixa de pesquisa** para restringir rapidamente um log específico com base na data/hora. A pesquisa está no nome do log.

5. Baixe arquivos de log individuais usando o botão **baixar** (ícone de seta para baixo) ao lado de cada arquivo de log na linha da tabela, conforme mostrado:

   ![Clique no ícone baixar](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Configurar os registos de diagnóstico

1. Na seção **monitoramento** na barra lateral, selecione **configurações de diagnóstico**.

1. Clique em "+ adicionar configuração de diagnóstico" @no__t-configuração de diagnóstico 0Add @ no__t-1

1. Forneça um nome de configuração de diagnóstico.

1. Especifique quais coletores de dados enviarão os logs de consulta lentos (conta de armazenamento, Hub de eventos e/ou espaço de trabalho Log Analytics).

1. Selecione "MySqlSlowLogs" como o tipo de log.
configuração de diagnóstico ![Configure @ no__t-1

1. Depois de configurar os coletores de dados para canalizar os logs de consulta lentos, você pode clicar em **salvar**.
configuração de diagnóstico ![Save @ no__t-1

1. Acesse os logs de consulta lento explorando-os nos coletores de dados que você configurou. Pode levar até 10 minutos para que os logs sejam exibidos.

## <a name="next-steps"></a>Passos seguintes
- Consulte [acessar logs de consulta lentos na CLI](howto-configure-server-logs-cli.md) para saber como baixar logs de consulta lentos programaticamente.
- Saiba mais sobre [logs de consulta lentos](concepts-server-logs.md) no banco de dados do Azure para MariaDB.
- Para obter mais informações sobre as definições de parâmetro e registro em log, consulte a documentação do MariaDB sobre [logs](https://mariadb.com/kb/en/library/slow-query-log-overview/).