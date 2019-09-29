---
title: Adicionar usuários a uma conta de Azure Data Lake Analytics
description: Saiba como adicionar usuários corretamente à sua conta de Data Lake Analytics usando o assistente para Adicionar usuário e Azure PowerShell.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: db35f16e-1565-4873-a851-bd987accdc58
ms.topic: conceptual
ms.date: 05/24/2018
ms.openlocfilehash: 16c503fe2d584d5f8256c65bfc49825b300f6a36
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672732"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Adicionar um novo utilizador no portal do Azure

## <a name="start-the-add-user-wizard"></a>Iniciar o assistente para Adicionar usuário
1. Abra o Azure Data Lake Analytics via https://portal.azure.com.
2. Clique em **Assistente para Adicionar usuário**.
3. Na etapa **Selecionar usuário** , localize o usuário que você deseja adicionar. Clique em **Selecionar**.
4. a etapa **selecionar função** , escolha **Data Lake Analytics desenvolvedor**. Essa função tem o conjunto mínimo de permissões necessárias para enviar/monitorar/gerenciar trabalhos do U-SQL. Atribua a essa função se o grupo não se destina ao gerenciamento de serviços do Azure.
5. Na etapa **selecionar permissões do catálogo** , selecione quaisquer bancos de dados adicionais aos quais o usuário precisará acessar. O acesso de leitura e gravação ao banco de dados mestre é necessário para enviar trabalhos. Quando tiver terminado, clique em **OK**.
6. Na etapa final chamada **atribuir permissões selecionadas** , revise as alterações que o assistente fará. Clique em **OK**.


## <a name="configure-acls-for-data-folders"></a>Configurar ACLs para pastas de dados
Conceda "R-X" ou "RWX", conforme necessário, em pastas que contêm dados de entrada e dados de saída.


## <a name="optionally-add-the-user-to-the-azure-data-lake-storage-gen1-role-reader-role"></a>Opcionalmente, adicione o usuário à função de **leitor** de função Azure data Lake Storage Gen1.
1.  Localize sua conta do Azure Data Lake Storage Gen1.
2.  Clique em **usuários**.
3. Clique em **Adicionar**.
4.  Selecione uma função de RBAC do Azure para atribuir esse grupo.
5.  Atribuir à função de leitor. Essa função tem o conjunto mínimo de permissões necessárias para procurar/gerenciar dados armazenados no ADLSGen1. Atribua a essa função se o grupo não se destina ao gerenciamento de serviços do Azure.
6.  Digite o nome do grupo.
7.  Clique em **OK**.

## <a name="adding-a-user-using-powershell"></a>Adicionando um usuário usando o PowerShell

1. Siga as instruções neste guia: [Como instalar e configurar Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Baixe o script do PowerShell [Add-AdlaJobUser. ps1](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1) .
3. Execute o script do PowerShell. 

O comando de exemplo para conceder acesso de usuário para enviar trabalhos, exibir novos metadados de trabalho e exibir metadados antigos é:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>Passos seguintes

* [Visão geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Introdução ao Data Lake Analytics usando o portal do Azure](data-lake-analytics-get-started-portal.md)
* [Gerenciar Azure Data Lake Analytics usando Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

