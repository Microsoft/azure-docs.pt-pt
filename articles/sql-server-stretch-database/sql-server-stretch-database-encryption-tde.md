---
title: Habilitar Transparent Data Encryption para Stretch Database
description: Habilitar Transparent Data Encryption (TDE) para SQL Server Stretch Database no Azure
services: sql-server-stretch-database
documentationcenter: ''
ms.assetid: a44ed8f5-b416-4c41-9b1e-b7271f10bdc3
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/14/2016
author: blazem-msft
ms.author: blazem
ms.reviewer: jroth
manager: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 465338bb793ad7b4fc406e9d1c073b9dc0dc06c8
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034003"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Habilitar Transparent Data Encryption (TDE) para Stretch Database no Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

O Transparent Data Encryption (TDE) ajuda a proteger contra a ameaça de atividades mal-intencionadas, executando criptografia e descriptografia em tempo real do banco de dados, backups associados e arquivos de log de transações em repouso, sem a necessidade de alterações no aplicativo.

O TDE criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada chave de criptografia do banco de dados. A chave de criptografia de banco de dados é protegida por um certificado de servidor interno. O certificado de servidor interno é exclusivo para cada servidor do Azure. A Microsoft gira automaticamente esses certificados pelo menos a cada 90 dias. Para obter uma descrição geral do TDE, consulte [Encriptação de Dados Transparente (TDE)].

## <a name="enabling-encryption"></a>Habilitando a criptografia
Para habilitar o TDE para um banco de dados do Azure que está armazenando o dado migrado de um banco de SQL Server habilitado para Stretch, faça o seguinte:

1. Abra o banco de dados no [portal do Azure](https://portal.azure.com)
2. Na folha banco de dados, clique no botão **configurações**
3. Selecione a opção **Transparent data encryption** ![][1]
4. Selecione a configuração **ativado** e, em seguida, selecione **salvar**
   ![][2]

## <a name="disabling-encryption"></a>Desabilitando a criptografia
Para desabilitar o TDE para um banco de dados do Azure que está armazenando o dado migrado de um banco de SQL Server habilitado para Stretch, faça o seguinte:

1. Abra o banco de dados no [portal do Azure](https://portal.azure.com)
2. Na folha banco de dados, clique no botão **configurações**
3. Selecione a opção **Transparent Data Encryption**
4. Selecione a configuração **desativado** e, em seguida, selecione **salvar**

<!--Anchors-->
[Encriptação de Dados Transparente (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
