---
title: Configurar e acessar logs de servidor no banco de dados do Azure para PostgreSQL-servidor único do portal do Azure
description: Este artigo descreve como configurar e acessar os logs de servidor no banco de dados do Azure para PostgreSQL-servidor único do portal do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: c77e708e14d34545754ca38095aedb63ff0172a1
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841509"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>Configurar e acessar o banco de dados do Azure para PostgreSQL-logs de servidor único do portal do Azure

Você pode configurar, listar e baixar os [logs do banco de dados do Azure para PostgreSQL](concepts-server-logs.md) do portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
As etapas neste artigo exigem que você tenha o [banco de dados do Azure para servidor PostgreSQL](quickstart-create-server-database-portal.md).

## <a name="configure-logging"></a>Configurar registro em log
Configure o acesso aos logs de consulta e aos logs de erros. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione o servidor da Base de Dados do Azure para PostgreSQL.

3. Na seção **monitoramento** na barra lateral, selecione **logs do servidor**. 

   ![Captura de tela de opções de logs do servidor](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Para ver os parâmetros do servidor, selecione **clique aqui para habilitar logs e configurar parâmetros de log**.

5. Altere os parâmetros que você precisa ajustar. Todas as alterações feitas nesta sessão são realçadas em roxo.

   Depois de alterar os parâmetros, selecione **salvar**. Ou você pode descartar suas alterações. 

   ![Captura de tela de opções de parâmetros de servidor](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

Na página **parâmetros do servidor** , você pode retornar à lista de logs fechando a página.

## <a name="view-list-and-download-logs"></a>Exibir lista e baixar logs
Após o início do log, você pode exibir uma lista de logs disponíveis e baixar arquivos de log individuais. 

1. Abra o portal do Azure.

2. Selecione o servidor da Base de Dados do Azure para PostgreSQL.

3. Na seção **monitoramento** na barra lateral, selecione **logs do servidor**. A página mostra uma lista de seus arquivos de log.

   ![Captura de tela da página de logs do servidor, com a lista de logs realçada](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > A Convenção de nomenclatura do log é **PostgreSQL-yyyy-mm-dd_hh0000. log**. A data e a hora usadas no nome de arquivo são a hora em que o log foi emitido. Os arquivos de log são girados a cada hora ou 100 MB, o que ocorrer primeiro.

4. Se necessário, use a caixa de pesquisa para restringir rapidamente um log específico, com base na data e hora. A pesquisa está no nome do log.

   ![Captura de tela da página logs do servidor, com a caixa de pesquisa e os resultados realçados](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Para baixar arquivos de log individuais, selecione o ícone de seta para baixo ao lado de cada arquivo de log na linha da tabela.

   ![Captura de tela da página de logs do servidor, com o ícone de seta para baixo realçado](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>Passos seguintes
- Consulte [logs do servidor de acesso na CLI](howto-configure-server-logs-using-cli.md) para saber como baixar logs programaticamente.
- Saiba mais sobre [os logs de servidor](concepts-server-logs.md) no banco de dados do Azure para PostgreSQL. 
- Para obter mais informações sobre as definições de parâmetro e log do PostgreSQL, consulte a documentação do PostgreSQL sobre [relatório de erros e registro em log](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).

