---
title: Obter a cadeia de ligação no portal do Azure
description: Obter a cadeia de ligação no portal do Azure
keywords: ligação de SQL, cadeia de ligação
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 6ead2e0ea326b5c3f2e76e7aa9cc4ab3c50d4154
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66164287"
---
### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Obter a cadeia de ligação no portal do Azure
Utilize o [portal do Azure](https://portal.azure.com/) para obter a cadeia de ligação que é necessária para o seu programa de cliente interagir com a base de dados do Azure SQL.

1. Selecione **todos os serviços** > **bases de dados SQL**.

2. Introduza o nome da base de dados na caixa de texto de filtro perto do canto superior esquerdo dos **bases de dados SQL** painel.

3. Selecione a linha da base de dados.

4. Depois que é apresentado o painel da base de dados, para sua comodidade visual, selecione o **Minimizar** botões para fechar os painéis que utilizou para procurar e filtrar de base de dados.

5. No painel da base de dados, selecione **Mostrar cadeias de ligação de base de dados**.

6. Copie a cadeia de ligação adequado. ou seja, se pretende usar a biblioteca de ligação do ADO.NET, copie a cadeia de caracteres apropriada do **ADO.NET** separador.

    ![Copie a cadeia de ligação do ADO para a base de dados][20-CopyAdoConnectionString]

7. Edite a cadeia de ligação, conforme necessário. ou seja, inserir a palavra-passe na cadeia de ligação ou remover "@&lt;servername&gt;" do nome de utilizador, se o nome de utilizador ou nome do servidor é demasiado longo.

8. Num formato ou outro, cole as informações da cadeia de ligação no código do programa de cliente.

Para obter mais informações, consulte [cadeias de ligação e ficheiros de configuração](https://msdn.microsoft.com/library/ms254494.aspx).

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
