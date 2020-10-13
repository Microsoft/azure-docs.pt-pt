---
title: O que é a segurança ao nível da coluna para a Azure Synapse?
description: Column-Level Security permite que os clientes controlem o acesso a colunas de mesa de base com base no contexto de execução ou membro do grupo do utilizador, simplificando o design e codificação de segurança na sua aplicação, e permitindo-lhe implementar restrições no acesso à coluna.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/19/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: f8bb40f9c80a0785c81c7aeacf783553bf73aa90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91259888"
---
# <a name="column-level-security"></a>Segurança ao nível da coluna

Column-Level Security permite que os clientes controlem o acesso a colunas de mesa com base no contexto de execução do utilizador ou na adesão ao grupo.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Desde que este vídeo foi [publicado, a Segurança de nível de linha](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) ficou disponível para Azure Synapse.

A segurança ao nível da coluna simplifica a conceção e codificação da segurança na sua aplicação, permitindo-lhe restringir o acesso à coluna para proteger dados sensíveis. Por exemplo, garantir que utilizadores específicos só podem aceder a determinadas colunas de uma tabela pertinentes ao seu departamento. A lógica de restrição de acesso está localizada no nível da base de dados e não longe dos dados de outro nível de aplicação. A base de dados aplica as restrições de acesso sempre que o acesso aos dados é tentado a partir de qualquer nível. Esta restrição torna a sua segurança mais fiável e robusta reduzindo a área de superfície do seu sistema de segurança global. Além disso, a segurança ao nível das colunas também elimina a necessidade de introduzir pontos de vista para filtrar colunas para impor restrições de acesso aos utilizadores.

Pode implementar a segurança ao nível da coluna com a declaração [GRANT](/sql/t-sql/statements/grant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL. Com este mecanismo, a autenticação do SQL e do Azure Ative Directory (Azure AD) é suportada.

![O diagrama mostra uma tabela esquemática com a primeira coluna chefiada por um cadeado fechado e as suas células de cor laranja enquanto as outras colunas são células brancas.](./media/column-level-security/cls.png)

## <a name="syntax"></a>Syntax

```syntaxsql
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

O exemplo a seguir mostra como restringir `TestUser` o acesso à coluna da `SSN` `Membership` tabela:

Criar `Membership` tabela com coluna SSN usada para armazenar números de segurança social:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Permitir `TestUser` o acesso a todas as colunas, com exceção da coluna SSN, que tem os dados sensíveis:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Consultas executadas como `TestUser` falharão se incluirem a coluna SSN:

```sql
SELECT * FROM Membership;

-- Msg 230, Level 14, State 1, Line 12
-- The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Casos de Utilização

Alguns exemplos de como a segurança ao nível das colunas está a ser utilizada hoje em dia:

- Uma empresa de serviços financeiros permite que apenas os gestores de conta tenham acesso aos números de segurança social dos clientes (SSN), números de telefone e outras informações pessoalmente identificáveis (PII).
- Um prestador de cuidados de saúde permite que apenas médicos e enfermeiros tenham acesso a registos médicos sensíveis, impedindo os membros do departamento de faturação de visualizarem estes dados.
