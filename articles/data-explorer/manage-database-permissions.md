---
title: Gerenciar permissões de banco de dados no Azure Data Explorer
description: Este artigo descreve os controles de acesso baseado em função para bancos de dados e tabelas no Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b4d5e56e990c0353f44209c6b19ae2d1727de27a
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030102"
---
# <a name="manage-azure-data-explorer-database-permissions"></a>Gerenciar permissões do banco de dados Data Explorer do Azure

O Azure Data Explorer permite que você controle o acesso a bancos de dados e tabelas usando um modelo de *controle de acesso baseado em função* . Nesse modelo, as *entidades* (usuários, grupos e aplicativos) são mapeadas para *funções*. As entidades de segurança podem acessar recursos de acordo com as funções que são atribuídas.

Este artigo descreve as funções disponíveis e como atribuir entidades a essas funções usando os comandos portal do Azure e gerenciamento de Data Explorer do Azure.

## <a name="roles-and-permissions"></a>Funções e permissões

O Azure Data Explorer tem as seguintes funções:

|Função                       |Permissões                                                                        |
|---------------------------|-----------------------------------------------------------------------------------|
|Administrador de banco de dados             |Pode fazer qualquer coisa no escopo de um banco de dados específico.|
|Usuário do banco de dados              |Pode ler todos os dados e metadados no banco de dado. Além disso, eles podem criar tabelas (tornando-se o administrador da tabela para essa tabela) e funções no banco de dados.|
|Visualizador de banco de dados            |Pode ler todos os dados e metadados no banco de dado.|
|Ingestão de banco de dados          |Pode ingerir os dados para todas as tabelas existentes no banco de dados, mas não para consultar os mesmos.|
|Monitor de banco de dados           |Pode executar '. show... ' comandos no contexto do banco de dados e suas entidades filho.|
|Administrador de tabela                |Pode fazer qualquer coisa no escopo de uma tabela específica. |
|Ingerir tabela             |Pode ingerir dados no escopo de uma tabela específica, mas não consultar os dados.|

## <a name="manage-permissions-in-the-azure-portal"></a>Gerenciar permissões no portal do Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

1. Navegue até o cluster de Data Explorer do Azure.

1. Na seção **visão geral** , selecione o banco de dados para o qual você deseja gerenciar permissões.

    ![Selecionar base de dados](media/manage-database-permissions/select-database.png)

1. Selecione **permissões** e **Adicionar**.

    ![Permissões de banco de dados](media/manage-database-permissions/database-permissions.png)

1. Em **adicionar permissões de banco de dados**, selecione a função à qual você deseja atribuir a entidade de segurança e, em seguida, **selecione entidades**.

    ![Adicionar permissões de banco de dados](media/manage-database-permissions/add-permission.png)

1. Pesquise a entidade de segurança, selecione-a e, em seguida, **selecione**.

    ![Gerenciar permissões no portal do Azure](media/manage-database-permissions/new-principals.png)

1. Selecione **Guardar**.

    ![Gerenciar permissões no portal do Azure](media/manage-database-permissions/save-permission.png)

## <a name="manage-permissions-with-management-commands"></a>Gerenciar permissões com comandos de gerenciamento

1. Entre no [https://dataexplorer.azure.com](https://dataexplorer.azure.com)e adicione o cluster se ele ainda não estiver disponível.

1. No painel esquerdo, selecione o banco de dados apropriado.

1. Use o comando `.add` para atribuir entidades a funções: `.add database databasename rolename ('aaduser | aadgroup=user@domain.com')`. Para adicionar um usuário à função de usuário do banco de dados, execute o comando a seguir, substituindo o nome do banco de dados e o usuário.

    ```Kusto
    .add database <TestDatabase> users ('aaduser=<user@contoso.com>')
    ```

    A saída do comando mostra a lista de usuários existentes e as funções às quais eles estão atribuídos no banco de dados.
    
    Para obter exemplos referentes à Azure Active Directory e ao modelo de autorização Kusto, consulte [princípios e provedores de identidade](https://docs.microsoft.com/azure/kusto/management/access-control/principals-and-identity-providers)

## <a name="next-steps"></a>Passos seguintes

[Escrever consultas](write-queries.md)
