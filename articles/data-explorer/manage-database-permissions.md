---
title: Gerir permissões de base de dados no Explorador de dados do Azure
description: Este artigo descreve os controlos de acesso baseado em funções para bases de dados e tabelas no Explorador de dados do Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 36e1bb77be1e825e42f0e5d25457214a8b5f882d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60758798"
---
# <a name="manage-azure-data-explorer-database-permissions"></a>Gerir permissões de base de dados do Explorador de dados do Azure

O Explorador de dados do Azure permite-lhe controlar o acesso a bancos de dados e tabelas, com um *controlo de acesso baseado em funções* modelo. Sob este modelo *principais* (utilizadores, grupos e aplicações) estão mapeadas para *funções*. Entidades de segurança podem aceder a recursos de acordo com as funções que lhes foram atribuídas.

Este artigo descreve as funções disponíveis e como atribuir entidades de segurança a essas funções com o portal do Azure e comandos de gestão do Explorador de dados do Azure.

## <a name="roles-and-permissions"></a>Funções e permissões

O Explorador de dados do Azure tem as seguintes funções:

|Função                       |Permissões                                                                        |
|---------------------------|-----------------------------------------------------------------------------------|
|Administrador de banco de dados             |Pode fazer tudo no escopo de um banco de dados específico.|
|Utilizador de base de dados              |Pode ler todos os dados e metadados no banco de dados. Além disso, pode criar tabelas (tornando-se o administrador de tabela para a tabela) e as funções na base de dados.|
|Visualizador de base de dados            |Pode ler todos os dados e metadados no banco de dados.|
|Ingestor de base de dados          |Pode ingerir dados para todas as tabelas existentes na base de dados, mas não consultar os dados.|
|Monitor da base de dados           |Pode executar comandos de ".show..." no contexto da base de dados e as entidades filhas.|
|Administrador de tabela                |Pode fazer qualquer coisa no âmbito de uma determinada tabela. |
|Ingestor de tabela             |Pode ingerir dados no âmbito de uma tabela específica, mas não consultar os dados.|

## <a name="manage-permissions-in-the-azure-portal"></a>Gerir as permissões no portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Navegue para o seu cluster do Explorador de dados do Azure.

1. Na **descrição geral** secção, selecione a base de dados em que pretende gerir as permissões.

    ![Selecione a base de dados](media/manage-database-permissions/select-database.png)

1. Selecione **permissões** , em seguida, **adicionar**.

    ![Permissões de base de dados](media/manage-database-permissions/database-permissions.png)

1. Sob **adicionar permissões de base de dados**, selecione a função que pretende atribuir o principal para, em seguida, **selecione principais**.

    ![Adicionar permissões de base de dados](media/manage-database-permissions/add-permission.png)

1. Procurar o principal, selecione-o, em seguida, **selecione**.

    ![Gerir as permissões no portal do Azure](media/manage-database-permissions/new-principals.png)

1. Selecione **Guardar**.

    ![Gerir as permissões no portal do Azure](media/manage-database-permissions/save-permission.png)

## <a name="manage-permissions-with-management-commands"></a>Gerir permissões com comandos de gestão

1. Iniciar sessão no [ https://dataexplorer.azure.com ](https://dataexplorer.azure.com)e adicione o seu cluster se ainda não estiver disponível.

1. No painel esquerdo, selecione a base de dados adequado.

1. Utilize o `.add` comando para atribuir a entidades de segurança para as funções: `.add database databasename rolename ('aaduser | aadgroup=user@domain.com')`. Para adicionar um utilizador à função de utilizador de base de dados, execute o seguinte comando, substituindo o nome de base de dados e o utilizador.

    ```Kusto
    .add database <TestDatabase> users ('aaduser=<user@contoso.com>')
    ```

    O resultado do comando mostra a lista de utilizadores existentes e as funções que lhes foram atribuídas na base de dados.

## <a name="next-steps"></a>Passos Seguintes

[Escrever consultas](write-queries.md)
