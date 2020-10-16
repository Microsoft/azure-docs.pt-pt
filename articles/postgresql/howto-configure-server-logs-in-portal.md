---
title: Gerir registos - Portal Azure - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como configurar e aceder aos registos do servidor (.log files) na Base de Dados Azure para PostgreSQL - Servidor Único a partir do portal Azure.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 3b52cea1d440506caf5b8244c9643719edd8755c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91704280"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>Configurar e aceder à Base de Dados Azure para postgresQL - Registos de servidor único a partir do portal Azure

Pode configurar, listar e baixar a [Base de Dados Azure para registos PostgreSQL](concepts-server-logs.md) a partir do portal Azure.

## <a name="prerequisites"></a>Pré-requisitos
Os passos deste artigo requerem que tenha [a Base de Dados Azure para o servidor PostgreSQL](quickstart-create-server-database-portal.md).

## <a name="configure-logging"></a>Confiria a exploração madeireira
Configure o acesso aos registos de consulta e registos de erro. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione o servidor da Base de Dados do Azure para PostgreSQL.

3. Sob a secção **de Monitorização** na barra lateral, selecione **registos do Servidor**. 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png" alt-text="Screenshot das opções de registos do Servidor":::

4. Para ver os parâmetros do servidor, **selecione Clique aqui para ativar registos e configurar parâmetros de registo**.

5. Altere os parâmetros que precisa de ajustar. Todas as alterações que fizer nesta sessão estão em destaque em roxo.

   Depois de ter alterado os parâmetros, **selecione Guardar**. Ou pode descartar as suas alterações. 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="Screenshot das opções de registos do Servidor":::

A partir da página **'Parâmetros do Servidor',** pode voltar à lista de registos fechando a página.

## <a name="view-list-and-download-logs"></a>Ver lista e baixar registos
Após o início do registo, pode ver uma lista de registos disponíveis e fazer o download de ficheiros de registo individual. 

1. Abra o portal do Azure.

2. Selecione o servidor da Base de Dados do Azure para PostgreSQL.

3. Sob a secção **de Monitorização** na barra lateral, selecione **registos do Servidor**. A página mostra uma lista dos seus ficheiros de registo.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/4-server-logs-list.png" alt-text="Screenshot das opções de registos do Servidor":::

   > [!TIP]
   > A convenção de nomeação do diário de bordo é **postgresql-yyy-mm-dd_hh0000.log**. A data e hora utilizadas no nome do ficheiro é a hora em que o registo foi emitido. Os ficheiros de registo giram a cada hora ou 100 MB, o que vier primeiro.

4. Se necessário, utilize a caixa de pesquisa para reduzir rapidamente a um registo específico, com base na data e hora. A procura está no nome do registo.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/5-search.png" alt-text="Screenshot das opções de registos do Servidor":::

5. Para descarregar ficheiros de registo individuais, selecione o ícone de seta para baixo ao lado de cada ficheiro de registo na linha de tabela.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/6-download.png" alt-text="Screenshot das opções de registos do Servidor":::

## <a name="next-steps"></a>Passos seguintes
- Consulte [os registos do servidor Access no CLI](howto-configure-server-logs-using-cli.md) para aprender a descarregar registos programáticamente.
- Saiba mais sobre [os registos de servidores](concepts-server-logs.md) na Base de Dados Azure para PostgreSQL. 
- Para obter mais informações sobre as definições de parâmetros e o registo pós-SQL, consulte a documentação postgreSQL sobre [relatórios de erros e registos.](https://www.postgresql.org/docs/current/static/runtime-config-logging.html)

