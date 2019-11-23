---
title: Transparent Data Encryption (Portal)
description: Transparent Data Encryption (TDE) no SQL Data Warehouse
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 4435c5f246d6dd20fd0b2c560634f6980d6aa2ff
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685775"
---
# <a name="get-started-with-transparent-data-encryption-tde-in-sql-data-warehouse"></a>Introdução ao Transparent Data Encryption (TDE) no SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Visão geral de segurança](sql-data-warehouse-overview-manage-security.md)
> * [Autenticação](sql-data-warehouse-authentication.md)
> * [Criptografia (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Encryption (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>Permissões necessárias
Para habilitar Transparent Data Encryption (TDE), você deve ser um administrador ou um membro da função dbmanager.

## <a name="enabling-encryption"></a>Habilitando a criptografia
Para habilitar o TDE para um SQL Data Warehouse, siga as etapas abaixo:

1. Abra o banco de dados no [portal do Azure](https://portal.azure.com)
2. Na folha banco de dados, clique no botão **configurações**
3. Selecione a opção **Transparent data encryption** ![][1]
4. Selecione a configuração **ativado** ![][2]
5. Selecione **salvar**
   ![][3]  

## <a name="disabling-encryption"></a>Desabilitando a criptografia
Para desabilitar o TDE para um SQL Data Warehouse, siga as etapas abaixo:

1. Abra o banco de dados no [portal do Azure](https://portal.azure.com)
2. Na folha banco de dados, clique no botão **configurações**
3. Selecione a opção **Transparent data encryption** ![][1]
4. Selecione a configuração **desativado** ![][4]
5. Selecione **salvar**
   ![][5]  

## <a name="encryption-dmvs"></a>DMVs de criptografia
A criptografia pode ser confirmada com as DMVs a seguir:

* [sys.databases]
* [sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
