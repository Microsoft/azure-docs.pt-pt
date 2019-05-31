---
title: Restrições de funcionalidade de base de dados SQL do Azure | Documentos da Microsoft
description: Restrições de funcionalidade de base de dados SQL do Azure melhora a segurança de base de dados ao restringir funcionalidades da base de dados que podem ser pelos atacantes para obter acesso a informações nos mesmos.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 03/22/2019
ms.openlocfilehash: ac7a074e78def504a10b4daa07971f919f414a88
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66259456"
---
# <a name="azure-sql-database-feature-restrictions"></a>Restrições de funcionalidade de base de dados SQL do Azure

Uma fonte comum de ataques do SQL Server, é por meio de aplicativos web que acessam o banco de dados, onde as várias formas de ataques de injeção de SQL são utilizadas para obter informações sobre a base de dados.  O ideal é que o código da aplicação foi desenvolvido para que ele não permite a injeção de SQL.  No entanto, em grandes-bases de código que incluem código legado e externo, um nunca se sabe que foram solucionados todos os casos, portanto, injeções de SQL são um fato da vida que temos para proteger contra.  O objetivo de restrições de recurso é impedir que algumas formas de injeção de SQL de vazamento de informações sobre a base de dados, mesmo quando a injeção de SQL é efetuada com êxito.

## <a name="enabling-feature-restrictions"></a>Ativar restrições de recurso

Ativar restrições de recurso é feito usando o `sp_add_feature_restriction` procedimento armazenado da seguinte forma:

```sql
EXEC sp_add_feature_restriction <feature>, <object_class>, <object_name>
```

As seguintes funcionalidades podem ser restringidas:

| Funcionalidade          | Descrição |
|------------------|-------------|
| N'ErrorMessages' | Quando restritas, quaisquer dados de utilizador na mensagem de erro serão mascarados. Consulte [mensagens de erro de restrição de recursos](#error-messages-feature-restriction) |
| N'Waitfor'       | Quando restrito, o comando retornará imediatamente sem demora. Consulte [WAITFOR restrição de funcionalidade](#waitfor-feature-restriction) |

O valor de `object_class` pode ser uma `N'User'` ou `N'Role'` para indicar se `object_name` é um nome de utilizador ou um nome de função na base de dados.

O exemplo a seguir fará com que todas as mensagens de erro para o utilizador `MyUser` a ser mascarados:

```sql
EXEC sp_add_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="disabling-feature-restrictions"></a>Desabilitando restrições de recurso

Desabilitar restrições de recurso é feito usando o `sp_drop_feature_restriction` procedimento armazenado da seguinte forma:

```sql
EXEC sp_drop_feature_restriction <feature>, <object_class>, <object_name>
```

O exemplo seguinte desativa a máscara de mensagem de erro para o utilizador `MyUser`:

```sql
EXEC sp_drop_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="viewing-feature-restrictions"></a>Restrições de recurso de visualização

O `sys.sql_feature_restrictions` vista apresenta todas as restrições de recurso atualmente definido na base de dados. Ele tem as seguintes colunas:

| Nome da coluna | Tipo de dados | Descrição |
|-------------|-----------|-------------|
| Classe       | nvarchar(128) | Classe de objeto ao qual se aplica a restrição |
| objeto      | nvarchar(256) | Nome do objeto ao qual se aplica a restrição |
| Funcionalidade     | nvarchar(128) | Recurso que é restrito |

## <a name="feature-restrictions"></a>Restrições de recurso

### <a name="error-messages-feature-restriction"></a>Restrição de funcionalidade de mensagens de erro

Um método de ataque de injeção SQL comum é injetar o código que faz com que um erro.  Ao examinar a mensagem de erro, um invasor pode obter informações sobre o sistema, permitindo ataques mais direcionadas adicionais.  Este ataque pode ser especialmente útil quando o aplicativo não apresenta os resultados de uma consulta, mas apresentar mensagens de erro.

Considere uma aplicação web que tem um pedido na forma de:

```html
http://www.contoso.com/employee.php?id=1
```

Que executa a seguinte consulta de base de dados:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Se o valor passado como o `id` parâmetro para o pedido de aplicação web é copiado para substituir $EmpId na consulta de base de dados, um invasor poderia fazer com que o pedido seguinte:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(DB_NAME() AS INT)=0
```

E seria retornado o seguinte erro, permitindo ao atacante obter o nome da base de dados:

```sql
Conversion failed when converting the nvarchar value 'HR_Data' to data type int.
```

Depois de restrição para o usuário do aplicativo na base de dados de recursos de mensagens de erro de ativação, a mensagem de erro devolvida está oculto, para que nenhuma informação interna sobre a base de dados for vazando:

```sql
Conversion failed when converting the ****** value '******' to data type ******.
```

Da mesma forma, o invasor poderia tornar o pedido seguinte:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(Salary AS TINYINT)=0
```

E seria retornado o seguinte erro, permitindo ao atacante obter salário do funcionário:

```sql
Arithmetic overflow error for data type tinyint, value = 140000.
```

Utilizar a restrição de funcionalidade de mensagens de erro, irá devolver a base de dados:

```sql
Arithmetic overflow error for data type ******, value = ******.
```

### <a name="waitfor-feature-restriction"></a>Restrição de funcionalidade WAITFOR

Um cego Injeção de SQL é quando um aplicativo não fornece um invasor com os resultados do SQL injetado ou com uma mensagem de erro, mas o invasor pode inferir informações da base de dados com a construção de uma consulta condicional em que as duas ramificações condicionais demorar uma quantidade diferente de tempo para ser executado. Ao comparar o tempo de resposta, o invasor pode saber o ramo foi executado e, deste modo, saiba mais informações sobre o sistema. A variante mais simples deste ataque está a utilizar o `WAITFOR` instrução para introduzir o atraso.

Considere uma aplicação web que tem um pedido na forma de:

```html
http://www.contoso.com/employee.php?id=1
```

que executa a seguinte consulta de base de dados:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Se o valor passado como o parâmetro de id para os pedidos de aplicação web é copiado para substituir $EmpId na consulta de base de dados, um invasor pode fazer o pedido seguinte:

```html
http://www.contoso.com/employee.php?id=1; IF SYSTEM_USER='sa' WAITFOR DELAY '00:00:05'
```

E a consulta exigiria um 5 segundos adicionais se o `sa` estava a ser utilizada a conta. Se `WAITFOR` restrição da funcionalidade está desativada na base de dados, o `WAITFOR` instrução será ignorada e não as informações for vazando com este ataque.