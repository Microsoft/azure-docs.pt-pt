---
title: Gerir registos - Portal Azure - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como configurar e aceder aos registos do servidor (.ficheiros de registo) na Base de Dados Azure para PostgreSQL - Servidor Único do portal Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 3da48a9b6d3acb1f2811bc279de7963fa1d83918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763696"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>Configure e aceda à Base de Dados Azure para registos postgresQL - Servidor Único do portal Azure

Pode configurar, listar e transferir a Base de [Dados Azure para registos PostgreSQL](concepts-server-logs.md) a partir do portal Azure.

## <a name="prerequisites"></a>Pré-requisitos
Os passos neste artigo exigem que tenha [base de dados Azure para servidor PostgreSQL](quickstart-create-server-database-portal.md).

## <a name="configure-logging"></a>Configurar a exploração madeireira
Configure o acesso aos registos de consulta e registos de erros. 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Selecione o servidor da Base de Dados do Azure para PostgreSQL.

3. Na secção **de monitorização** na barra lateral, selecione **registos do Servidor**. 

   ![Screenshot das opções de logs do Servidor](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Para ver os parâmetros do servidor, selecione **Clique aqui para ativar registos e configurar parâmetros**de registo .

5. Mude os parâmetros que precisa de ajustar. Todas as alterações que fizer nesta sessão são destacadas em roxo.

   Depois de ter alterado os parâmetros, selecione **Guardar**. Ou pode descartar as suas mudanças. 

   ![Screenshot das opções de parâmetros do servidor](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

A partir da página Parâmetros do **Servidor,** pode voltar à lista de registos fechando a página.

## <a name="view-list-and-download-logs"></a>Ver registos de lista e de scarregamento
Após o início do registo, pode ver uma lista de registos disponíveis e descarregar ficheiros de registo individuais. 

1. Abra o portal do Azure.

2. Selecione o servidor da Base de Dados do Azure para PostgreSQL.

3. Na secção **de monitorização** na barra lateral, selecione **registos do Servidor**. A página mostra uma lista dos seus ficheiros de registo.

   ![Screenshot da página de logs do Servidor, com lista de registos destacados](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > A convenção de nomeação do diário é **postgresql-yyyy-mm-dd_hh0000.log**. A data e a hora utilizadas no nome do ficheiro são a altura em que o registo foi emitido. Os ficheiros de registo giram a cada hora ou 100 MB, o que vier primeiro.

4. Se necessário, utilize a caixa de pesquisa para reduzir rapidamente a um registo específico, com base na data e hora. A procura está no nome do registo.

   ![Screenshot da página de logs do Servidor, com caixa de pesquisa e resultados destacados](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Para descarregar ficheiros de registo individuais, selecione o ícone de seta para baixo ao lado de cada ficheiro de registo na linha da tabela.

   ![Screenshot da página de logs do Servidor, com ícone de seta baixa realçado](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>Passos seguintes
- Consulte [os registos do servidor Access no CLI](howto-configure-server-logs-using-cli.md) para aprender a descarregar registos programáticamente.
- Saiba mais sobre [os registos](concepts-server-logs.md) de servidores na Base de Dados Azure para PostgreSQL. 
- Para obter mais informações sobre as definições do parâmetro e a exploração madeireira PostgreSQL, consulte a documentação PostgreSQL sobre [relatórios de erros e registos](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).

