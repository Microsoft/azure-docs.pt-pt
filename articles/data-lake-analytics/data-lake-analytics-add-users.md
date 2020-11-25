---
title: Adicione os utilizadores a uma conta Azure Data Lake Analytics
description: Saiba como adicionar corretamente os utilizadores à sua conta Data Lake Analytics utilizando o Add User Wizard e a Azure PowerShell.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 05/24/2018
ms.openlocfilehash: c04b1bbd62e156aeb8d3a0ebb244cfbc753dec52
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020828"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Adicionar um novo utilizador no portal do Azure

## <a name="start-the-add-user-wizard"></a>Inicie o Assistente de Utilizador adicionar
1. Abra o seu Azure Data Lake Analytics via https://portal.azure.com .
2. Clique **em Adicionar O Assistente do Utilizador**.
3. No passo **de utilizador Select,** encontre o utilizador que pretende adicionar. Clique em **Selecionar**.
4. **o passo de função Select,** escolha Data Lake Analytics **Developer**. Esta função tem o conjunto mínimo de permissões necessárias para submeter/monitorizar/gerir os empregos U-SQL. Atribuir a esta função se o grupo não se destinar a gerir os serviços da Azure.
5. Na etapa de permissões de **catálogo Select,** selecione quaisquer bases de dados adicionais às quais o utilizador precise de acesso. Ler e escrever Acesso à base de dados estática predefinida chamada "mestre" é necessário para apresentar empregos. Quando tiver terminado, clique em **OK**.
6. No passo final chamado **Atribuir permissões selecionadas** rever as alterações que o assistente irá es fazer. Clique em **OK**.


## <a name="configure-acls-for-data-folders"></a>Configurar ACLs para pastas de dados
Conceder "R-X" ou "RWX", se necessário, em pastas que contenham dados de entrada e dados de saída.


## <a name="optionally-add-the-user-to-the-azure-data-lake-storage-gen1-role-reader-role"></a>Opcionalmente, adicione o utilizador à função de **leitor** de funções Azure Data Lake Storage Gen1.
1.  Encontre a sua conta Azure Data Lake Storage Gen1.
2.  Clique em **Utilizadores**.
3. Clique em **Adicionar**.
4.  Selecione um papel Azure para atribuir este grupo.
5.  Atribua-se ao papel de Leitor. Esta função tem o conjunto mínimo de permissões necessárias para navegar/gerir dados armazenados em ADLSGen1. Atribuir a esta função se o Grupo não se destinar a gerir os serviços da Azure.
6.  Digite o nome do Grupo.
7.  Clique em **OK**.

## <a name="adding-a-user-using-powershell"></a>Adicionar um utilizador usando PowerShell

1. Siga as instruções deste guia: [Como instalar e configurar a Azure PowerShell](/powershell/azure/).
2. Descarregue o [Add-AdlaJobUser.ps1](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1) script PowerShell.
3. Executar o script PowerShell. 

O comando da amostra para dar acesso ao utilizador para submeter empregos, ver novos metadados de emprego e ver metadados antigos é:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>Passos seguintes

* [Descrição geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Começar com data lake analytics usando o portal Azure](data-lake-analytics-get-started-portal.md)
* [Gerir a Azure Data Lake Analytics utilizando a Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
