---
title: Encriptação de dados transparente no SQL Data Warehouse (Portal) | Documentos da Microsoft
description: Encriptação de dados transparente (TDE) no SQL Data Warehouse
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: db6e2ad41c842e9118b2c004f8024afd894347b4
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453257"
---
# <a name="get-started-with-transparent-data-encryption-tde-in-sql-data-warehouse"></a>Começar com dados encriptação transparente (TDE) no SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Descrição geral da segurança](sql-data-warehouse-overview-manage-security.md)
> * [Autenticação](sql-data-warehouse-authentication.md)
> * [Encriptação (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Encryption (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>Permissões obrigatórias
Para ativar a encriptação de dados transparente (TDE), tem de ser um administrador ou membro da função dbmanager.

## <a name="enabling-encryption"></a>Ativar a encriptação
Para ativar a TDE para um SQL Data Warehouse, siga os passos abaixo:

1. Abrir a base de dados no [portal do Azure](https://portal.azure.com)
2. No painel da base de dados, clique nas **definições** botão
3. Selecione o **encriptação de dados transparente** opção ![][1]
4. Selecione o **no** definição ![][2]
5. Selecione **guardar**
   ![][3]  

## <a name="disabling-encryption"></a>A desativação da encriptação
Para desativar a TDE para um SQL Data Warehouse, siga os passos abaixo:

1. Abrir a base de dados no [portal do Azure](https://portal.azure.com)
2. No painel da base de dados, clique nas **definições** botão
3. Selecione o **encriptação de dados transparente** opção ![][1]
4. Selecione o **desativar** definição ![][4]
5. Selecione **guardar**
   ![][5]  

## <a name="encryption-dmvs"></a>DMVs de encriptação
Encriptação pode ser confirmada com as DMVs a seguir:

* [sys.databases]
* [sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
