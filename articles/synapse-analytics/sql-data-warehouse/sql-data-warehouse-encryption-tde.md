---
title: Encriptação transparente de dados (Portal)
description: Encriptação transparente de dados (TDE) em Azure Synapse Analytics
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 669a02bb45ef61d1e66d719850defcfaa19b838d
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350536"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Começar com encriptação de dados transparentes (TDE)
> [!div class="op_single_selector"]
> * [Visão geral de segurança](sql-data-warehouse-overview-manage-security.md)
> * [Autenticação](sql-data-warehouse-authentication.md)
> * [Encriptação (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Encriptação (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>Permissões Obrigatórias
Para ativar a Encriptação transparente de dados (TDE), deve ser um administrador ou um membro do papel de dbmanager.

## <a name="enabling-encryption"></a>Ativar a encriptação
Para ativar o TDE, siga os passos abaixo:

1. Abra a base de dados no [portal Azure](https://portal.azure.com)
2. Na lâmina de base de dados, clique no botão **Definições**
3. Selecione as definições do portal de opção ![de encriptação de dados **transparentes**](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Selecione ![as definições do portal de definição **em**](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png)
5. Selecione **guardar**
   ![definições do portal](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png)  

## <a name="disabling-encryption"></a>Encriptação incapacitante
Para desativar o TDE, siga os passos abaixo:

1. Abra a base de dados no [portal Azure](https://portal.azure.com)
2. Na lâmina de base de dados, clique no botão **Definições**
3. Selecione as definições do portal de opção ![de encriptação de dados **transparentes**](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Selecione ![as definições do portal de definição **Off**](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png)
5. Selecione **guardar**
   ![a definição do portal 2](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png)  

## <a name="encryption-dmvs"></a>DMVs de encriptação
A encriptação pode ser confirmada com os seguintes DMVs:

* [bases de dados sys.]
* [sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[bases de dados sys.]: https://msdn.microsoft.com/library/ms178534.aspx
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
