---
title: Ativar encriptação transparente de dados para base de dados de alongamento
description: Ativar encriptação transparente de dados (TDE) para base de dados de alongamento do servidor SQL em Azure
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034003"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Ativar encriptação transparente de dados (TDE) para base de dados de alongamento seletiva em Azure
> [!div class="op_single_selector"]
> * [Portal Azure](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

A Encriptação transparente de dados (TDE) ajuda a proteger contra a ameaça de atividades maliciosas através da encriptação e desencriptação em tempo real da base de dados, cópias de segurança associadas e ficheiros de registo de transações em repouso sem exigir alterações na aplicação.

A Encriptação de Dados Transparente encripta o armazenamento de uma base de dados completa ao utilizar uma chave simétrica chamada chave de encriptação de base de dados. A chave de encriptação da base de dados está protegida por um certificado de servidor incorporado. O certificado de servidor incorporado é único para cada servidor Azure. A Microsoft roda automaticamente estes certificados pelo menos a cada 90 dias. Para obter uma descrição geral do TDE, consulte a [Encriptação transparente de dados (TDE)].

## <a name="enabling-encryption"></a>Ativar a encriptação
Para ativar o TDE para uma base de dados Azure que armazene os dados migrados de uma base de dados do SQL Server ativada por extensão, faça as seguintes coisas:

1. Abra a base de dados no [portal Azure](https://portal.azure.com)
2. Na lâmina de base de dados, clique no botão **Definições**
3. Selecione a opção transparente de **encriptação** de dados![][1]
4. Selecione a definição **de On** e, em seguida, selecione **Guardar**
   ![][2]

## <a name="disabling-encryption"></a>Encriptação incapacitante
Para desativar o TDE para uma base de dados Azure que armazene os dados migrados de uma base de dados do SQL Server ativado por extensão, faça as seguintes coisas:

1. Abra a base de dados no [portal Azure](https://portal.azure.com)
2. Na lâmina de base de dados, clique no botão **Definições**
3. Selecione a opção transparente de **encriptação** de dados
4. Selecione a definição **Off** e, em seguida, selecione **Guardar**

<!--Anchors-->
[Encriptação de Dados Transparente (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
