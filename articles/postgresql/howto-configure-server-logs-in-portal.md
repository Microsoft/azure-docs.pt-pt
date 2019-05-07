---
title: Configurar e aceder aos registos de servidor da base de dados do Azure para PostgreSQL - servidor único no Portal do Azure
description: Este artigo descreve como configurar e aceder os registos do servidor na base de dados do Azure para PostgreSQL - único servidor a partir do Portal do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 13a8c1ee4b7ca114211f93245d74866e5aa5d52c
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067439"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-in-the-azure-portal"></a>Configurar e aceder à base de dados do Azure para o PostgreSQL – registos de servidor único no portal do Azure

Pode configurar, listar e transfira o [base de dados do Azure para PostgreSQL registos](concepts-server-logs.md) do portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de procedimentos, terá de:
- [Base de dados do Azure para o servidor PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="configure-logging"></a>Configurar o registo
Configure o acesso aos registos de consulta e registos de erros. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione o servidor da Base de Dados do Azure para PostgreSQL.

3. Sob o **monitorização** secção na barra lateral, selecione **os registos do servidor**. 

   ![Selecione os registos do servidor e selecione "Clique aqui para ativar a..."](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Selecione o cabeçalho **clique aqui para ativar os registos e configurar os parâmetros de registo** para ver os parâmetros de servidor.

5. Altere os parâmetros que precisa de ajustar. Todas as alterações efetuadas nesta seção são realçadas em Roxo.

   Assim que tiver alterado os parâmetros, pode clicar em **guardar**. Ou pode **descartar** as suas alterações. 

   ![Longa lista de parâmetros com alterações por guardar ou rejeitar](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. Regressar à lista de registos ao clicar o **botão Fechar** (ícone de X) no **parâmetros do servidor** página.

## <a name="view-list-and-download-logs"></a>Ver lista e transferir registos
Assim que começa de registo, pode ver uma lista de registos disponíveis e transferir ficheiros de registo individuais no painel de registos do servidor. 

1. Abra o portal do Azure.

2. Selecione o servidor da Base de Dados do Azure para PostgreSQL.

3. Sob o **monitorização** secção na barra lateral, selecione **os registos do servidor**. A página mostra uma lista dos ficheiros de registo, conforme mostrado:

   ![Lista de registos do servidor](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > É a Convenção de nomenclatura do registo **postgresql-aaaa-mm-dd_hh0000.log**. A data e hora usados no nome do ficheiro é o tempo é quando o registo foi emitido. Rodar os ficheiros de registo para a cada uma hora ou 100 MB de tamanho, o que ocorrer primeiro.

4. Se necessário, use o **caixa de pesquisa** restringir rapidamente para um registo específico com base na data/hora. A pesquisa é o nome do registo.

   ![Pesquisa de exemplo nos nomes de registo](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Transferir ficheiros de registo individuais utilizando o **transferir** botão (para baixo do ícone de seta) ao lado de cada ficheiro de registo na linha da tabela, conforme mostrado:

   ![Clique em ícone de download](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>Passos Seguintes
- Ver [registos do servidor de acesso na CLI](howto-configure-server-logs-using-cli.md) para aprender a transferir os registos através de programação.
- Saiba mais sobre [registos do servidor](concepts-server-logs.md) no DB do Azure para PostgreSQL. 
- Para obter mais informações sobre as definições de parâmetros e o registo do PostgreSQL, consulte a documentação do PostgreSQL no [relatório de erros e Registro em log](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).

