---
title: Restrições de funcionalidades
description: As restrições de recurso do banco de dados SQL do Azure aprimoram a segurança do banco de dados restringindo recursos em seu banco de dados que podem ser invasores para obter acesso a informações neles.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 03/22/2019
ms.openlocfilehash: ce10daca23299f838e4086426fa89d9cade314ea
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823437"
---
# <a name="azure-sql-database-feature-restrictions"></a>Restrições de recursos do banco de dados SQL do Azure

Uma fonte comum de ataques de SQL Server é por meio de aplicativos Web que acessam o banco de dados em que várias formas de ataques de injeção de SQL são usadas para obter informações sobre o banco de dados.  O ideal é que o código do aplicativo seja desenvolvido para não permitir a injeção de SQL.  No entanto, em grandes bases de código que incluem código herdado e externo, nunca é possível ter certeza de que todos os casos foram resolvidos, portanto, as injeções de SQL são um fato da vida que precisamos nos proteger.  A meta das restrições de recursos é impedir que algumas formas de injeção de SQL vazassem informações sobre o banco de dados, mesmo quando a injeção de SQL é bem-sucedida.

## <a name="enabling-feature-restrictions"></a>Habilitando restrições de recursos

A habilitação de restrições de recursos é feita usando o procedimento armazenado `sp_add_feature_restriction` da seguinte maneira:

```sql
EXEC sp_add_feature_restriction <feature>, <object_class>, <object_name>
```

Os seguintes recursos podem ser restritos:

| Funcionalidade          | Descrição |
|------------------|-------------|
| N'ErrorMessages' | Quando restrito, todos os dados de usuário dentro da mensagem de erro serão mascarados. Consulte a [restrição de recurso de mensagens de erro](#error-messages-feature-restriction) |
| N'Waitfor'       | Quando restrito, o comando retornará imediatamente sem nenhum atraso. Consulte a [restrição de recurso WAITFOR](#waitfor-feature-restriction) |

O valor de `object_class` pode ser `N'User'` ou `N'Role'` para indicar se `object_name` é um nome de usuário ou um nome de função no banco de dados.

O exemplo a seguir fará com que todas as mensagens de erro para o usuário `MyUser` sejam mascaradas:

```sql
EXEC sp_add_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="disabling-feature-restrictions"></a>Desabilitando restrições de recursos

A desabilitação das restrições de recursos é feita usando o procedimento armazenado `sp_drop_feature_restriction` da seguinte maneira:

```sql
EXEC sp_drop_feature_restriction <feature>, <object_class>, <object_name>
```

O exemplo a seguir desabilita o mascaramento de mensagens de erro para o usuário `MyUser`:

```sql
EXEC sp_drop_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="viewing-feature-restrictions"></a>Exibindo restrições de recursos

A exibição `sys.sql_feature_restrictions` apresenta todas as restrições de recurso definidas no momento no banco de dados. Ele tem as seguintes colunas:

| Nome da coluna | Data type | Descrição |
|-------------|-----------|-------------|
| Classe       | nvarchar(128) | Classe de objeto ao qual a restrição se aplica |
| objeto      | nvarchar(256) | Nome do objeto ao qual a restrição se aplica |
| recurso     | nvarchar(128) | Recurso que é restrito |

## <a name="feature-restrictions"></a>Restrições de recursos

### <a name="error-messages-feature-restriction"></a>Restrição de recurso de mensagens de erro

Um método comum de ataque de injeção de SQL é injetar código que cause um erro.  Ao examinar a mensagem de erro, um invasor pode aprender informações sobre o sistema, permitindo outros ataques mais direcionados.  Esse ataque pode ser especialmente útil quando o aplicativo não exibe os resultados de uma consulta, mas exibe mensagens de erro.

Considere um aplicativo Web que tenha uma solicitação na forma de:

```html
http://www.contoso.com/employee.php?id=1
```

Que executa a seguinte consulta de banco de dados:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Se o valor passado como o parâmetro `id` para a solicitação do aplicativo Web for copiado para substituir $EmpId na consulta de banco de dados, um invasor poderá fazer a seguinte solicitação:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(DB_NAME() AS INT)=0
```

E o seguinte erro será retornado, permitindo que o invasor aprendesse o nome do banco de dados:

```sql
Conversion failed when converting the nvarchar value 'HR_Data' to data type int.
```

Depois de habilitar a restrição de recurso de mensagens de erro para o usuário do aplicativo no banco de dados, a mensagem de erro retornada é mascarada para que nenhuma informação interna sobre o banco de dados seja vazada:

```sql
Conversion failed when converting the ****** value '******' to data type ******.
```

Da mesma forma, o invasor pode fazer a seguinte solicitação:

```html
http://www.contoso.com/employee.php?id=1 AND CAST(Salary AS TINYINT)=0
```

E o seguinte erro seria retornado, permitindo que o invasor aprendesse o salário do funcionário:

```sql
Arithmetic overflow error for data type tinyint, value = 140000.
```

Usando a restrição de recursos de mensagens de erro, o banco de dados retornaria:

```sql
Arithmetic overflow error for data type ******, value = ******.
```

### <a name="waitfor-feature-restriction"></a>Restrição de recurso WAITFOR

Uma injeção de SQL cego é quando um aplicativo não fornece um invasor com os resultados do SQL injetado ou com uma mensagem de erro, mas o invasor pode inferir informações do banco de dados construindo uma consulta condicional na qual as duas ramificações condicionais Reserve um período de tempo diferente para executar. Comparando o tempo de resposta, o invasor pode saber qual ramificação foi executada e, portanto, aprender informações sobre o sistema. A variante mais simples desse ataque é usar a instrução `WAITFOR` para introduzir o atraso.

Considere um aplicativo Web que tenha uma solicitação na forma de:

```html
http://www.contoso.com/employee.php?id=1
```

Que executa a seguinte consulta de banco de dados:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

Se o valor passado como o parâmetro de ID para as solicitações do aplicativo Web for copiado para substituir $EmpId na consulta de banco de dados, um invasor poderá fazer a seguinte solicitação:

```html
http://www.contoso.com/employee.php?id=1; IF SYSTEM_USER='sa' WAITFOR DELAY '00:00:05'
```

E a consulta levaria mais 5 segundos se a conta de `sa` estava sendo usada. Se `WAITFOR` restrição de recurso estiver desabilitada no banco de dados, a instrução `WAITFOR` será ignorada e não as informações serão vazadas usando esse ataque.