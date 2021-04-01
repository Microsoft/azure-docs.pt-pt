---
title: Ativar encriptação de dados transparentes para base de dados de alongamento
description: Ativar encriptação de dados transparentes (TDE) para base de dados de alongamento de servidor SQL em Azure
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
ms.openlocfilehash: b016987162cc8202b7ad28d4dd8e5ab2953469d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95024250"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Ativar encriptação de dados transparentes (TDE) para base de dados de alongamento em Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

A Encriptação de Dados Transparente (TDE) ajuda a proteger contra a ameaça de atividade maliciosa, realizando encriptação e desencriptação em tempo real da base de dados, cópias de segurança associadas e ficheiros de registo de transações em repouso sem exigir alterações à aplicação.

A Encriptação de Dados Transparente encripta o armazenamento de uma base de dados completa ao utilizar uma chave simétrica chamada chave de encriptação de base de dados. A chave de encriptação da base de dados está protegida por um certificado de servidor incorporado. O certificado de servidor incorporado é único para cada servidor Azure. A Microsoft roda automaticamente estes certificados pelo menos a cada 90 dias. Para obter uma descrição geral do TDE, consulte [a Encriptação de Dados Transparente (TDE)].

## <a name="enabling-encryption"></a>Habilitar a encriptação
Para ativar o TDE para uma base de dados Azure que está a armazenar os dados migrados de uma base de dados do SQL Server ativada por alongamentos, faça as seguintes coisas:

1. Abra a base de dados no [portal Azure](https://portal.azure.com)
2. Na lâmina da base de **dados,** clique no botão Definições
3. Selecione a opção de encriptação de **dados Transparente** Screenshot do portal ![ Azure, com a lâmina definições visível. Na secção Geral, destaca-se a encriptação de dados transparentes.][1]
4. Selecione a definição **On** e, em seguida, selecione **Save** 
    ![ Screenshot do portal Azure, com a lâmina de encriptação de dados transparente visível. A encriptação de dados é ligada e o botão Guardar é realçado.][2]

## <a name="disabling-encryption"></a>Encriptação incapacitante
Para desativar o TDE para uma base de dados Azure que está a armazenar os dados migrados de uma base de dados do SQL Server ativada por alongamentos, faça as seguintes coisas:

1. Abra a base de dados no [portal Azure](https://portal.azure.com)
2. Na lâmina da base de **dados,** clique no botão Definições
3. Selecione a opção **de encriptação de dados transparente**
4. Selecione a definição **Off** e, em seguida, selecione **Guardar**

<!--Anchors-->
[Encriptação de Dados Transparente (TDE)]: /sql/relational-databases/security/encryption/transparent-data-encryption


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->