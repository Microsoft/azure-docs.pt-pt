---
title: Adicionar utilizadores a uma conta do Azure Data Lake Analytics
description: Saiba como adicionar corretamente os utilizadores à sua conta do Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: db35f16e-1565-4873-a851-bd987accdc58
ms.topic: conceptual
ms.date: 05/24/2018
ms.openlocfilehash: 8323c4e1b236444f55dab826d2567491f5f0f736
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60629327"
---
# <a name="adding-a-user-in-the-azure-portal"></a>A adição de um utilizador no portal do Azure

## <a name="start-the-add-user-wizard"></a>Iniciar o Assistente de utilizador para adicionar
1. Abra o Azure Data Lake Analytics através do https://portal.azure.com.
2. Clique em **Assistente de utilizador para adicionar**.
3. Na **selecionar utilizador** passo, encontre o utilizador que pretende adicionar. Clique em **Selecionar**.
4. o **selecionar função** passo, escolha **programador do Data Lake Analytics**. Esta função tem o conjunto mínimo de permissões necessárias para submeter/monitor/gerir tarefas de U-SQL. Atribua a esta função, se o grupo não se destina para o gerenciamento de serviços do Azure.
5. Na **selecionar permissões de catálogo** passo, selecione quaisquer bases de dados adicionais esse utilizador terá acesso a. Leitura e de acesso de escrita à base de dados mestra é necessário para submeter tarefas. Quando tiver terminado, clique em **OK**.
6. No passo final chamado **atribuir permissões selecionadas** rever as alterações que fará com que o assistente. Clique em **OK**.


## <a name="configure-acls-for-data-folders"></a>Configurar ACLs para pastas de dados
Conceder "R-X" ou "RWX", conforme necessário, em pastas que contêm dados de entrada e saída de dados.


## <a name="optionally-add-the-user-to-the-azure-data-lake-storage-gen1-role-reader-role"></a>Opcionalmente, adicione o utilizador à função de geração 1 de armazenamento do Azure Data Lake **leitor** função.
1.  Localize a conta de geração 1 de armazenamento do Azure Data Lake.
2.  Clique em **utilizadores**.
3. Clique em **Adicionar**.
4.  Selecione uma função de RBAC do Azure para atribuir este grupo.
5.  Atribua a função de leitor. Esta função tem o conjunto mínimo de permissões necessárias para procurar/gerir dados armazenados no ADLSGen1. Atribua a esta função, se o grupo não se destina para o gerenciamento de serviços do Azure.
6.  Escreva o nome do grupo.
7.  Clique em **OK**.

## <a name="adding-a-user-using-powershell"></a>Adicionar um utilizador com o PowerShell

1. Siga as instruções neste guia: [Como instalar e configurar o Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Transfira o [Add-AdlaJobUser.ps1](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1) script do PowerShell.
3. Execute o script do PowerShell. 

O comando de exemplo para dar acesso de utilizador para submeter tarefas, ver novos metadados de tarefa e vista de metadados antigo são:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>Passos Seguintes

* [Descrição geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Introdução ao Data Lake Analytics com o portal do Azure](data-lake-analytics-get-started-portal.md)
* [Gerir o Azure Data Lake Analytics com o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

