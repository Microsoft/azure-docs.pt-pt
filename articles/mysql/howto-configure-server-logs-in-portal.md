---
title: Configurar e aceder aos registos de consulta lenta da base de dados do Azure para MySQL no portal do Azure
description: Este artigo descreve como configurar e aceder aos registos lentos na base de dados do Azure para MySQL do portal do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: mysql
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: b16ac525d41eb2423828a647fdb75fd3f4a80a31
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052715"
---
# <a name="configure-and-access-slow-query-logs-in-the-azure-portal"></a>Configurar e registos de consulta lenta de acesso no portal do Azure

Pode configurar, listar e transfira o [base de dados do Azure para os registos de consulta lenta MySQL](concepts-server-logs.md) do portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de procedimentos, terá de:
- [Base de dados do Azure para o servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Configurar o registo
Configure o acesso ao registo de consulta lenta MySQL. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione a sua base de dados do Azure para o servidor MySQL.

3. Sob o **monitorização** secção na barra lateral, selecione **os registos do servidor**. 
   ![Selecionados os registos do servidor, clique para configurar](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Selecione o cabeçalho **clique aqui para ativar os registos e configurar os parâmetros de registo** para ver os parâmetros de servidor.

5. Altere os parâmetros que precisa de ajustar. Todas as alterações efetuadas nesta seção são realçadas em Roxo. 

   Assim que tiver alterado os parâmetros, pode clicar em **guardar**. Ou pode **descartar** as suas alterações.

   ![Clique em guardar ou rejeitar](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. Regressar à lista de registos ao clicar o **botão Fechar** (ícone de X) no **parâmetros do servidor** página.

## <a name="view-list-and-download-logs"></a>Ver lista e transferir registos
Assim que começa de registo, pode ver uma lista de registos de consulta lenta disponíveis e transferir ficheiros de registo individuais no painel de registos do servidor.

1. Abra o portal do Azure.

2. Selecione a sua base de dados do Azure para o servidor MySQL.

3. Sob o **monitorização** secção na barra lateral, selecione **os registos do servidor**. A página mostra uma lista dos ficheiros de registo, conforme mostrado:

   ![Lista de registos](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > É a Convenção de nomenclatura do registo **mysql - lenta - < nome do servidor >-yyyymmddhh.log**. A data e hora usados no nome do ficheiro é o tempo é quando o registo foi emitido. Ficheiros de registo são revezados cada 24 horas ou 7.5 GB, o que ocorrer primeiro.

4. Se necessário, use o **caixa de pesquisa** restringir rapidamente para um registo específico com base na data/hora. A pesquisa é o nome do registo.

5. Transferir ficheiros de registo individuais utilizando o **transferir** botão (para baixo do ícone de seta) ao lado de cada ficheiro de registo na linha da tabela, conforme mostrado:

   ![Clique em ícone de download](./media/howto-configure-server-logs-in-portal/5-download.png)

## <a name="next-steps"></a>Passos Seguintes
- Ver [registos de consulta lenta de acesso na CLI](howto-configure-server-logs-in-cli.md) para aprender a transferir os registos de consulta lenta por meio de programação.
- Saiba mais sobre [lento de registos de consulta](concepts-server-logs.md) na base de dados do Azure para MySQL.
- Para obter mais informações sobre as definições de parâmetros e o registo do MySQL, consulte a documentação do MySQL na [registos](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).