---
title: Obtenha a cadeia de ligação do portal Azure
description: Obtenha a cadeia de ligação do portal Azure
keywords: conexão sql,cadeia de ligação
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 6ead2e0ea326b5c3f2e76e7aa9cc4ab3c50d4154
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67184339"
---
### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Obtenha a cadeia de ligação do portal Azure
Utilize o [portal Azure](https://portal.azure.com/) para obter a cadeia de ligação necessária para que o seu programa de clientes interaja com a Base de Dados Azure SQL.

1. Selecione todas as**bases de dados SQL**de todos **os serviços** > .

2. Introduza o nome da sua base de dados na caixa de texto do filtro perto da parte superior esquerda da lâmina de bases de **dados SQL.**

3. Selecione a linha para a sua base de dados.

4. Depois da lâmina aparecer na sua base de dados, para conveniência visual, selecione os botões **Minimizar** para colapsar as lâminas utilizadas para a navegação e filtragem da base de dados.

5. Na lâmina da sua base de dados, selecione mostrar cordas de **ligação**de base de dados .

6. Copie a corda de ligação apropriada. ou seja, se pretender utilizar a biblioteca de ligação ADO.NET, copie a corda adequada do separador **ADO.NET.**

    ![Copie a cadeia de ligação ADO para a sua base de dados][20-CopyAdoConnectionString]

7. Editar a corda de ligação conforme necessário. isto é, insira a sua palavra-passe&lt;na&gt;cadeia de ligação ou remova "@ servername" do nome de utilizador se o nome de utilizador ou o nome do servidor forem demasiado longos.

8. Num ou noutro formato, colhe a informação de cadeia de ligação no código do programa do seu cliente.

Para mais informações, consulte [as cordas de Ligação e os ficheiros](https://msdn.microsoft.com/library/ms254494.aspx)de configuração .

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
