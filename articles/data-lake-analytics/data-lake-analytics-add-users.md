---
title: Adicione utilizadores a uma conta Azure Data Lake Analytics
description: Saiba como adicionar corretamente os utilizadores à sua conta Data Lake Analytics utilizando o Add User Wizard e o Azure PowerShell.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: db35f16e-1565-4873-a851-bd987accdc58
ms.topic: conceptual
ms.date: 05/24/2018
ms.openlocfilehash: 16c503fe2d584d5f8256c65bfc49825b300f6a36
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "71672732"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Adicionar um novo utilizador no portal do Azure

## <a name="start-the-add-user-wizard"></a>Inicie o Assistente de Utilizador Adicionar
1. Abra o seu Azure https://portal.azure.comData Lake Analytics via .
2. Clique em **adicionar assistente de utilizador**.
3. No passo do **utilizador Select,** encontre o utilizador que pretende adicionar. Clique em **Selecionar**.
4. o passo **de função Select,** escolha **Data Lake Analytics Developer**. Esta função tem o conjunto mínimo de permissões necessárias para submeter/monitorizar/gerir empregos U-SQL. Atribuir a esta função se o grupo não se destinar à gestão dos serviços Azure.
5. Na etapa de **permissões** do catálogo Select, selecione quaisquer bases de dados adicionais às quais o utilizador necessitará de ter acesso. Ler e Escrever O acesso à base de dados principal é necessário para apresentar postos de trabalho. Quando tiver terminado, clique em **OK**.
6. No passo final chamado **Atribuir permissões selecionadas,** reveja as alterações que o assistente irá fazer. Clique em **OK**.


## <a name="configure-acls-for-data-folders"></a>Configure ACLs para pastas de dados
Conceda "R-X" ou "RWX", conforme necessário, em pastas que contenham dados de entrada e dados de saída.


## <a name="optionally-add-the-user-to-the-azure-data-lake-storage-gen1-role-reader-role"></a>Opcionalmente, adicione o utilizador ao papel de **Leitor** de Leitore sinuoso do Azure Data Lake Storage Gen1.
1.  Encontre a sua conta De armazenamento de dados Azure Gen1.
2.  Clique em **Utilizadores**.
3. Clique em **Adicionar**.
4.  Selecione uma Função Azure RBAC para atribuir este grupo.
5.  Atribuir ao papel de Leitor. Esta função tem o conjunto mínimo de permissões necessárias para navegar/gerir dados armazenados em ADLSGen1. Atribuir a esta função se o Grupo não se destinar à gestão dos serviços azure.
6.  Digite o nome do Grupo.
7.  Clique em **OK**.

## <a name="adding-a-user-using-powershell"></a>Adicionar um utilizador usando powerShell

1. Siga as instruções neste guia: Como instalar e configurar o [Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Descarregue o script [Add-AdlaJobUser.ps1](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1) PowerShell.
3. Executa o guião powerShell. 

O comando da amostra para dar ao utilizador acesso a submeter postos de trabalho, ver novos metadados de emprego e ver metadados antigos é:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>Passos seguintes

* [Descrição geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Começar com data lake analytics usando o portal Azure](data-lake-analytics-get-started-portal.md)
* [Gerencie o Azure Data Lake Analytics utilizando o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

