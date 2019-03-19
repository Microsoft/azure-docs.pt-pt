---
title: Segurança de nível de coluna do armazém de dados SQL do Azure | Documentos da Microsoft
description: Segurança de nível de coluna (CLS) permite aos clientes controlar o acesso às colunas da tabela de base de dados com base no contexto de execução do utilizador ou a respetiva associação a grupos. CLS simplifica o design e programação da segurança na sua aplicação. CLS permite-lhe implementar restrições de acesso através da coluna.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 06/15/2018
ms.author: kavithaj
ms.reviewer: igorstan, carlrab
ms.openlocfilehash: f1452b1f15fe176107015957cdd1cd1cff5f4e55
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58172857"
---
# <a name="column-level-security"></a>Segurança ao nível da coluna
Segurança de nível de coluna (CLS) permite aos clientes controlar o acesso às colunas da tabela de base de dados com base no contexto de execução do utilizador ou a respetiva associação a grupos.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]

CLS simplifica o design e programação da segurança na sua aplicação. CLS permite-lhe implementar restrições de acesso através da coluna para proteger dados confidenciais. Por exemplo, garantindo que os utilizadores específicos podem aceder apenas determinadas colunas de uma tabela pertinente para o respetivo departamento. A lógica de restrição de acesso está localizado na camada de base de dados em vez de ausente dos dados na outra camada de aplicativos. A base de dados aplica as restrições de acesso sempre que esse acesso a dados é tentado a partir de qualquer camada. Esta restrição torna seu sistema de segurança mais robusta e confiável, reduzindo a área de superfície do seu sistema de segurança geral. Além disso, o CLS também elimina a necessidade de introduzir vistas para filtrar colunas para impor restrições de acesso as utilizadores.

Poderia implementar CLS com o [concessão](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) instrução T-SQL. Com esse mecanismo, autenticação de SQL e o Azure Active Directory (AAD) são suportados.

![cls](./media/column-level-security/cls.png)

## <a name="syntax"></a>Sintaxe

```sql
GRANT <permission> [ ,...n ] ON
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]
    TO <database_principal> [ ,...n ]
    [ WITH GRANT OPTION ]
    [ AS <database_principal> ]
<permission> ::=
    SELECT
  | UPDATE
<database_principal> ::=
      Database_user
    | Database_role
    | Database_user_mapped_to_Windows_User
    | Database_user_mapped_to_Windows_Group
```

## <a name="example"></a>Exemplo
O exemplo seguinte mostra como restringir "TestUser" de aceder a coluna 'SSN' da tabela de "Associação":

Crie tabela de "Associação" com a coluna SSN utilizada para armazenar números da segurança social:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Permitir que "TestUser" para aceder a todas as colunas, exceto para a coluna SSN com dados confidenciais:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Consultas executadas como "TestUser" falharão se incluir a coluna SSN:

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Casos de Utilização
Alguns exemplos de como o CLS está a ser utilizado atualmente:
- Uma empresa de serviços financeiros permite que a única conta gestores a ter acesso aos números da segurança social (SSN) dos clientes, os números de telefone e outras informações de identificação pessoal (PII).
- Um fornecedor de cuidados de saúde permite que apenas os médicos e enfermeiras ter acesso a registos médicos confidenciais e não permite que os membros do departamento de faturação ver estes dados.
