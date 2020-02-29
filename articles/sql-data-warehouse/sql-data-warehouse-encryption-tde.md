---
title: Encriptação transparente de dados (Portal)
description: Encriptação transparente de dados (TDE) em Azure Synapse Analytics
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
ms.openlocfilehash: c121f9c16895a749922525d1ba85ee2c2e60cfb0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195861"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Começar com encriptação de dados transparentes (TDE)
> [!div class="op_single_selector"]
> * [Visão geral de segurança](sql-data-warehouse-overview-manage-security.md)
> * [Autenticação](sql-data-warehouse-authentication.md)
> * [Encriptação (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Encriptação (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>Permissões necessárias
Para ativar a Encriptação transparente de dados (TDE), deve ser um administrador ou um membro do papel de dbmanager.

## <a name="enabling-encryption"></a>Ativar a encriptação
Para ativar o TDE, siga os passos abaixo:

1. Abra a base de dados no [portal Azure](https://portal.azure.com)
2. Na lâmina de base de dados, clique no botão **Definições**
3. Selecione a opção de **encriptação** de dados Transparente ![][1]
4. Selecione o ![][2] de definição **Em**
5. Selecione **Guardar**
   ![][3]  

## <a name="disabling-encryption"></a>Encriptação incapacitante
Para desativar o TDE, siga os passos abaixo:

1. Abra a base de dados no [portal Azure](https://portal.azure.com)
2. Na lâmina de base de dados, clique no botão **Definições**
3. Selecione a opção de **encriptação** de dados Transparente ![][1]
4. Selecione a definição **de Off** ![][4]
5. Selecione **Guardar**
   ![][5]  

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
