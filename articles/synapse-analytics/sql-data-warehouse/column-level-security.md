---
title: O que é segurança de nível de coluna para Azure Synapse?
description: A Segurança de Nível de Coluna permite que os clientes controlem o acesso a colunas de tabelas de bases de dados com base no contexto de execução do utilizador ou na adesão ao grupo, simplificando o design e codificação de segurança na sua aplicação, e permitindo-lhe implementar restrições na coluna acesso.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 24ead458232b096a5c69ffe8b45c6298a9da9f75
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349097"
---
# <a name="column-level-security"></a>Segurança ao nível da coluna

A Segurança de Nível de Coluna permite que os clientes controlem o acesso às colunas de mesa com base no contexto de execução do utilizador ou na adesão ao grupo.


> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Desde que este vídeo foi publicado [Row level Security](/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc&view=sql-server-2017) tornou-se disponível para Azure Synapse. 

A segurança ao nível da coluna simplifica o design e codificação de segurança na sua aplicação, permitindo-lhe restringir o acesso da coluna para proteger dados sensíveis. Por exemplo, garantir que utilizadores específicos só podem aceder a determinadas colunas de uma tabela pertinente ao seu departamento. A lógica de restrição de acesso está localizada no nível de base de dados e não longe dos dados de outro nível de aplicação. A base de dados aplica as restrições de acesso sempre que o acesso a dados é tentado a partir de qualquer nível. Esta restrição torna a sua segurança mais fiável e robusta, reduzindo a área de superfície do seu sistema de segurança global. Além disso, a segurança ao nível das colunas também elimina a necessidade de introduzir pontos de vista para filtrar colunas para impor restrições de acesso aos utilizadores.

Pode implementar a segurança de nível de coluna com [a](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) declaração grant T-SQL. Com este mecanismo, a autenticação sQL e Azure Ative Diretório (AAD) são suportadas.

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
O exemplo que se `TestUser` segue mostra `SSN` como `Membership` restringir o acesso à coluna da tabela:

Crie `Membership` tabela com coluna SSN utilizada para armazenar números de segurança social:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Permitir `TestUser` o acesso a todas as colunas, com exceção da coluna SSN, que possui os dados sensíveis:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

As consultas executadas como `TestUser` falharão se incluirem a coluna SSN:

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Casos de Utilização

Alguns exemplos de como a segurança ao nível da coluna está a ser usada hoje em dia:

- Uma empresa de serviços financeiros permite que apenas os gestores de conta tenham acesso aos números de segurança social dos clientes (SSN), números de telefone e outras informações pessoalmente identificáveis (PII).
- Um prestador de cuidados de saúde permite que apenas médicos e enfermeiros tenham acesso a registos médicos sensíveis, evitando que os membros do departamento de faturação possam visualizar estes dados.
