---
title: Gerir permissões de base de dados no Azure Data Explorer
description: Este artigo descreve controlos de acesso baseados em papéis para bases de dados e tabelas no Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b4d5e56e990c0353f44209c6b19ae2d1727de27a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76030102"
---
# <a name="manage-azure-data-explorer-database-permissions"></a>Gerir permissões de base de dados do Azure Data Explorer

O Azure Data Explorer permite controlar o acesso a bases de dados e tabelas, utilizando um modelo *de controlo de acesso baseado em papéis.* Neste modelo, os *principais* (utilizadores, grupos e aplicações) são mapeados para *funções.* Os diretores podem aceder aos recursos de acordo com as funções que lhes são atribuídas.

Este artigo descreve as funções disponíveis e como atribuir os principais a essas funções utilizando o portal Azure e comandos de gestão do Azure Data Explorer.

## <a name="roles-and-permissions"></a>Funções e permissões

O Azure Data Explorer tem as seguintes funções:

|Função                       |Permissões                                                                        |
|---------------------------|-----------------------------------------------------------------------------------|
|Administração de base de dados             |Pode fazer qualquer coisa no âmbito de uma determinada base de dados.|
|Utilizador de base de dados              |Pode ler todos os dados e metadados na base de dados. Além disso, podem criar tabelas (tornando-se a administração da tabela para essa tabela) e funções na base de dados.|
|Visualizador de base de dados            |Pode ler todos os dados e metadados na base de dados.|
|Base de dados ingestor          |Pode ingerir dados para todas as tabelas existentes na base de dados, mas não consultar os dados.|
|Monitor de base de dados           |Pode executar '.show...' comandos no contexto da base de dados e das suas entidades infantis.|
|Administrador de mesa                |Pode fazer qualquer coisa no âmbito de uma determinada mesa. |
|Quadro ingestor             |Pode ingerir dados no âmbito de uma determinada tabela, mas não consultar os dados.|

## <a name="manage-permissions-in-the-azure-portal"></a>Gerir permissões no portal Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

1. Navegue para o seu cluster Azure Data Explorer.

1. Na secção **Visão Geral,** selecione a base de dados onde pretende gerir as permissões.

    ![Selecionar base de dados](media/manage-database-permissions/select-database.png)

1. Selecione **Permissões** **e,** em seguida, adicionar .

    ![Permissões de base de dados](media/manage-database-permissions/database-permissions.png)

1. Em **adicionar permissões**de base de dados, selecione a função a que pretende atribuir o comitente e, em seguida, **selecione os principais**.

    ![Adicionar permissões de base de dados](media/manage-database-permissions/add-permission.png)

1. Procure o diretor, selecione-o e, em seguida, **selecione**.

    ![Gerir permissões no portal Azure](media/manage-database-permissions/new-principals.png)

1. Selecione **Guardar**.

    ![Gerir permissões no portal Azure](media/manage-database-permissions/save-permission.png)

## <a name="manage-permissions-with-management-commands"></a>Gerir permissões com comandos de gestão

1. Inscreva-se [https://dataexplorer.azure.com](https://dataexplorer.azure.com)e adicione o seu cluster se ainda não estiver disponível.

1. No painel esquerdo, selecione a base de dados adequada.

1. Utilize `.add` o comando para atribuir os `.add database databasename rolename ('aaduser | aadgroup=user@domain.com')`princípios às funções: . Para adicionar um utilizador à função de utilizador da Base de Dados, execute o seguinte comando, substituindo o nome da base de dados e o utilizador.

    ```Kusto
    .add database <TestDatabase> users ('aaduser=<user@contoso.com>')
    ```

    A saída do comando mostra a lista de utilizadores existentes e as funções a que estão atribuídos na base de dados.
    
    Por exemplo, referentes ao Diretório Ativo Azure e ao modelo de autorização Kusto, consulte [Princípios e Fornecedores](https://docs.microsoft.com/azure/kusto/management/access-control/principals-and-identity-providers) de Identidade

## <a name="next-steps"></a>Passos seguintes

[Escrever consultas](write-queries.md)
