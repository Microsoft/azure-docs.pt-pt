---
title: Segurança ao nível da coluna
description: A segurança em nível de coluna (CLS) permite que os clientes controlem o acesso a colunas de tabela de banco de dados com base no contexto de execução do usuário ou em sua associação de grupo. O CLS simplifica o design e a codificação de segurança em seu aplicativo. O CLS permite que você implemente restrições de acesso a colunas.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
ms.openlocfilehash: 85f705022a0ff5970d30c61206d4f2631254b7ce
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74077109"
---
# <a name="column-level-security"></a>Segurança em nível de coluna
A segurança em nível de coluna (CLS) permite que os clientes controlem o acesso a colunas de tabela de banco de dados com base no contexto de execução do usuário ou em sua associação de grupo.
Atualizar para o vídeo abaixo-como esse vídeo foi lançado, a [segurança em nível de linha](/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc&view=sql-server-2017) também está disponível em SQL data warehouse. 
> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]

O CLS simplifica o design e a codificação de segurança em seu aplicativo. O CLS permite que você implemente restrições de acesso de coluna para proteger dados confidenciais. Por exemplo, garantir que usuários específicos possam acessar apenas determinadas colunas de uma tabela pertinentes ao departamento deles. A lógica de restrição de acesso está localizada na camada de banco de dados, em vez de ficar distante da data em outra camada de aplicativo. O banco de dados aplica as restrições de acesso toda vez que o acesso a dados é tentado em qualquer camada. Essa restrição torna seu sistema de segurança mais confiável e robusto, reduzindo a área de superfície do seu sistema de segurança geral. Além disso, o CLS também elimina a necessidade de introduzir exibições para filtrar colunas para impor restrições de acesso aos usuários.

Você pode implementar o CLS com a instrução [Grant](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) T-SQL. Com esse mecanismo, há suporte para a autenticação do AAD (SQL e Azure Active Directory).

![CLS](./media/column-level-security/cls.png)

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
O exemplo a seguir mostra como restringir "testuser" para acessar a coluna "SSN" da tabela "Membership":

Crie a tabela ' Membership ' com a coluna SSN usada para armazenar números de seguro social:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Permitir que "testuser" acesse todas as colunas, exceto a coluna SSN que tem dados confidenciais:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

As consultas executadas como ' testuser ' falharão se incluírem a coluna SSN:

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Casos de Utilização
Alguns exemplos de como o CLS está sendo usado hoje:
- Uma empresa de serviços financeiros permite que apenas gerentes de contas tenham acesso aos números de CPF (cadastro de clientes sociais), números de telefone e outras informações de identificação pessoal (PII).
- Um provedor de assistência médica permite que apenas médicos e horas tenham acesso a registros médicos confidenciais, sem permitir que os membros do departamento de cobrança exibam esses dados.
