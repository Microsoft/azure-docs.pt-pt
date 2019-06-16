---
title: Configurar e aceder aos registos de servidor da base de dados do Azure para MariaDB no portal do Azure
description: Este artigo descreve como configurar e aceder os registos do servidor na base de dados do Azure para MariaDB do portal do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 3dbf7064e409230916668e62ef861c0ce149fdbb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065630"
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Configurar e registos do servidor de acesso no portal do Azure

Pode configurar, listar e transfira o [base de dados do Azure para registos de consulta lenta de MariaDB](concepts-server-logs.md) do portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de procedimentos, terá de:
- [Base de dados do Azure para MariaDB server](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Configurar o registo
Configure o acesso ao registo de consulta lenta. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione a sua base de dados do Azure para MariaDB server.

3. Sob o **monitorização** secção na barra lateral, selecione **os registos do servidor**. 
   ![Selecionados os registos do servidor, clique para configurar](./media/howto-configure-server-logs-portal/1-select-server-logs-configure.png)

4. Selecione o cabeçalho **clique aqui para ativar os registos e configurar os parâmetros de registo** para ver os parâmetros de servidor.

5. Altere os parâmetros que precisam para ajustar, incluindo ativar "slow_query_log" como "ON". Todas as alterações efetuadas nesta seção são realçadas em Roxo. 

   Assim que tiver alterado os parâmetros, pode clicar em **guardar**. Ou pode **descartar** as suas alterações.

   ![Clique em guardar ou rejeitar](./media/howto-configure-server-logs-portal/3-save-discard.png)

6. Regressar à lista de registos ao clicar o **botão Fechar** (ícone de X) no **parâmetros do servidor** página.

## <a name="view-list-and-download-logs"></a>Ver lista e transferir registos
Assim que começa de registo, pode ver uma lista de registos de consulta lenta disponíveis e transferir ficheiros de registo individuais no painel de registos do servidor. 

1. Abra o portal do Azure.

2. Selecione a sua base de dados do Azure para MariaDB server.

3. Sob o **monitorização** secção na barra lateral, selecione **os registos do servidor**. A página mostra uma lista dos ficheiros de registo, conforme mostrado:

   ![Lista de registos](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > É a Convenção de nomenclatura do registo **mysql - lenta - < nome do servidor >-yyyymmddhh.log**. A data e hora usados no nome do ficheiro é o tempo é quando o registo foi emitido. Ficheiros de registo são revezados cada 24 horas ou 7.5 GB, o que ocorrer primeiro.

4. Se necessário, use o **caixa de pesquisa** restringir rapidamente para um registo específico com base na data/hora. A pesquisa é o nome do registo.

5. Transferir ficheiros de registo individuais utilizando o **transferir** botão (para baixo do ícone de seta) ao lado de cada ficheiro de registo na linha da tabela, conforme mostrado:

   ![Clique em ícone de download](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [lento de registos de consulta](concepts-server-logs.md) na base de dados do Azure para MariaDB.
- Para obter mais informações sobre as definições de parâmetros e o registo, consulte a documentação de MariaDB sobre [registos](https://mariadb.com/kb/en/library/slow-query-log-overview/).

<!--- See [Access Server Logs in CLI](howto-configure-server-logs-in-cli.md) to learn how to download logs programmatically. -->
