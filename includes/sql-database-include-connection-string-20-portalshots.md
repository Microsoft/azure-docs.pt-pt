---
title: Obtenha a cadeia de ligação a partir do portal Azure
description: Obtenha a cadeia de ligação a partir do portal Azure
keywords: conexão sql,cadeia de conexão
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 6ead2e0ea326b5c3f2e76e7aa9cc4ab3c50d4154
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184339"
---
### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Obtenha a cadeia de ligação a partir do portal Azure
Utilize o [portal Azure](https://portal.azure.com/) para obter a cadeia de ligação necessária para que o seu programa cliente interaja com a Base de Dados Azure SQL.

1. Selecione **Todas as**  >  **bases de dados SQL de serviços.**

2. Introduza o nome da sua base de dados na caixa de texto do filtro, perto da parte superior esquerda da lâmina das **bases de dados SQL.**

3. Selecione a linha para a sua base de dados.

4. Depois de a lâmina aparecer para a sua base de dados, para obter uma conveniência visual, selecione os botões **Minimizar** para colapsar as lâminas utilizadas para navegar e filtrar a base de dados.

5. Na lâmina da sua base de dados, selecione **As cordas de ligação da base de dados**.

6. Copie a cadeia de ligação apropriada. ou seja, se pretender utilizar a biblioteca de ligação ADO.NET, copie o fio apropriado a partir do separador **ADO.NET.**

    ![Copie a cadeia de ligação ADO para a sua base de dados][20-CopyAdoConnectionString]

7. Edite a cadeia de ligação conforme necessário. ou seja, insira a sua palavra-passe na cadeia de ligação ou remova &lt; o nome de utilizador "@servername" &gt; do nome de utilizador se o nome de utilizador ou o nome do servidor forem demasiado longos.

8. Num formato ou noutro, cole a informação da cadeia de ligação no código do programa do cliente.

Para obter mais informações, consulte [as cordas de ligação e os ficheiros de configuração](https://msdn.microsoft.com/library/ms254494.aspx).

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
