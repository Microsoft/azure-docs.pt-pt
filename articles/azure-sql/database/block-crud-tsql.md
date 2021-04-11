---
title: O Bloco T-SQL ordena para criar ou modificar recursos Azure SQL
titleSuffix: Block T-SQL commands to create or modify Azure SQL resources
description: Este artigo detalha uma funcionalidade que permite aos administradores da Azure bloquear comandos T-SQL para criar ou modificar recursos Azure SQL
ms.service: sql-database
ms.subservice: security
ms.custom: ''
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.topic: article
ms.date: 03/31/2021
ms.reviewer: ''
ROBOTS: NOINDEX
ms.openlocfilehash: 4ecaa8a3ee1d11ea13563ae5c74835b8d62fd960
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556200"
---
# <a name="what-is-block-t-sql-crud-feature"></a>O que é a funcionalidade CRUD do Bloco T-SQL?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb-sqlmi.md)]


Esta funcionalidade permite que os administradores da Azure bloqueiem a criação ou modificação dos recursos Azure SQL através do T-SQL. Isto é aplicado ao nível da subscrição para bloquear os comandos T-SQL de afetar os recursos SQL em qualquer base de dados Azure SQL ou instância gerida.

## <a name="overview"></a>Descrição Geral

Para bloquear a criação ou modificação de recursos através do T-SQL e impor a gestão de recursos através de um modelo de Gestor de Recursos Azure (modelo ARM) para uma determinada subscrição, as funcionalidades de pré-visualização do nível de subscrição no portal Azure podem ser utilizadas. Isto é particularmente útil quando você está usando [Políticas Azure](/azure/governance/policy/overview) para impor padrões organizacionais através de modelos ARM. Uma vez que o T-SQL não adere às Políticas de Azure, pode ser aplicado um bloco em T-SQL para criar ou modificar operações. A sintaxe bloqueada inclui declarações CRUD (criar, atualizar, excluir) para bases de dados em Azure SQL, `CREATE DATABASE` especificamente, `ALTER DATABASE` e `DROP DATABASE` declarações. 

As operações T-SQL CRUD podem ser bloqueadas através do portal Azure, [PowerShell](/powershell/module/az.resources/register-azproviderfeature)ou [Azure CLI](/cli/azure/feature#az_feature_register).

## <a name="permissions"></a>Permissões

Para registar ou remover esta funcionalidade, o utilizador Azure deve ser membro da função Proprietário ou Contribuinte da subscrição.

## <a name="examples"></a>Exemplos

A seguinte secção descreve como pode registar ou não registar uma funcionalidade de pré-visualização com o provedor de recursos Microsoft.Sql no portal Azure: 

### <a name="register-block-t-sql-crud"></a>Bloco de Registo T-SQL CRUD

1. Aceda à sua subscrição no portal Azure.
2. Selecione no **separador Funcionalidades de Pré-visualização.** 
3. Selecione **bloco T-SQL CRUD**.
4. Depois de selecionar no **Block T-SQL CRUD,** abrirá uma nova janela, selecione **Register**, para registar este bloco com o fornecedor de recursos Microsoft.Sql.

![Selecione "Block T-SQL CRUD" na lista de funcionalidades de pré-visualização](./media/block-tsql-crud/block-tsql-crud.png)

![Com o "Block T-SQL CRUD" verificado, selecione Registar](./media/block-tsql-crud/block-tsql-crud-register.png)

  
### <a name="re-register-microsoftsql-resource-provider"></a>Re-registrar o fornecedor de recursos .sql Microsoft 
Depois de registar o bloco de T-SQL CRUD com o fornecedor de recursos Microsoft.Sql, tem de voltar a registar o fornecedor de recursos Microsoft.Sql para que as alterações entrem em vigor. Para voltar a registar o fornecedor de recursos Microsoft.Sql:

1. Aceda à sua subscrição no portal Azure.
2. Selecione no **separador Fornecedores de Recursos.**
3. Procure e selecione o fornecedor de recursos **Microsoft.Sql.**
4. **Selecione Re-registrar.** 

> [!NOTE]
> A etapa de reinscrição é obrigatória para que o bloco T-SQL seja aplicado à sua subscrição. 

![Re-registrar o fornecedor de recursos Microsoft.Sql](./media/block-tsql-crud/block-tsql-crud-re-register.png)

### <a name="removing-block-t-sql-crud"></a>Remoção do bloco T-SQL CRUD
Para remover o bloco em T-SQL criar ou modificar operações da sua subscrição, primeiro não regiser o bloco T-SQL anteriormente registado. Em seguida, re-registe o fornecedor de recursos Microsoft.Sql como mostrado acima para a remoção do bloco T-SQL para entrar em vigor. 


## <a name="next-steps"></a>Passos seguintes

- [Uma visão geral das capacidades de segurança da Base de Dados Azure SQL](security-overview.md)
- [Azure SQL Base de Dados de segurança boas práticas](security-best-practice.md)
