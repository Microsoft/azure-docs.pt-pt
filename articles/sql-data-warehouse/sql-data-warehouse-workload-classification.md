---
title: Classificação do SQL Data Warehouse | Documentos da Microsoft
description: Orientações para utilizar a classificação para gerir a simultaneidade, importância e recursos de cálculo para consultas no armazém de dados SQL do Azure.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: bcc09095955a28bde3ed999f23180e08485543fc
ms.sourcegitcommit: 4133f375862fdbdec07b70de047d70c66ac29d50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2019
ms.locfileid: "57994007"
---
# <a name="sql-data-warehouse-workload-classification-preview"></a>Classificação de carga de trabalho do armazém de dados SQL (pré-visualização)

Este artigo explica o processo de classificação de carga de trabalho do armazém de dados SQL da atribuição de uma classe de recursos e a importância para pedidos recebidos.

> [!Note]
> Classificação da carga de trabalho está disponível no SQL Data Warehouse Gen2.

## <a name="classification"></a>Classificação

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

Classificação de gestão da carga de trabalho permite que as políticas de carga de trabalho a ser aplicado aos pedidos através da atribuição [classes de recursos](resource-classes-for-workload-management.md#what-are-resource-classes) e [importância](sql-data-warehouse-workload-importance.md).

Embora haja várias formas de classificar cargas de trabalho do armazém de dados, a classificação mais simples e mais comuns é a carga e a consulta. Carregar dados com o insert, update e delete instruções.  Consultar os dados usando o seleciona. Uma solução de armazém de dados, muitas vezes, terá uma política de carga de trabalho para a atividade de carga, tais como atribuir uma classe de recursos superior com mais recursos. É possível aplicar uma política de carga de trabalho diferente para consultas, tais como de menor importância em comparação comparada atividades de carga.

Também pode subclassify suas cargas de trabalho de carga e a consulta. Subclassification dá-lhe maior controlo das suas cargas de trabalho. Por exemplo, cargas de trabalho de consulta podem incluir atualizações de cubo, consultas de dashboard ou consultas ad-hoc. Pode classificar cada uma destas cargas de trabalho de consulta com classes de recursos diferente ou definições de importância. Carga também pode se beneficiar subclassification. Transformações de grandes dimensões podem ser atribuídas a classes de recursos maiores. Maior importância pode ser utilizada para garantir que os dados de vendas principais for carregador antes dos dados meteorológicos ou um feed de dados sociais.

## <a name="classification-process"></a>Processo de classificação

Classificação no SQL Data Warehouse tal hoje em dia, tem de atribuir utilizadores a uma função que tenha uma classe de recursos correspondente atribuída a ele usando [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). A capacidade de caracterizam os pedidos que ultrapassem um início de sessão para uma classe de recursos é limitada com esta capacidade. Um método mais rico para classificação está agora disponível com o [CLASSIFICADOR de carga de trabalho de criar](/sql/t-sql/statements/create-workload-classifier-transact-sql) sintaxe.  Com essa sintaxe, os utilizadores do SQL Data Warehouse podem atribuir importância e uma classe de recursos para pedidos.  

> [!NOTE]
> Classificação é avaliada por solicitação. Vários pedidos numa única sessão podem ser classificados de maneira diferente.

## <a name="classification-precedence"></a>Precedência de classificação

Como parte do processo de classificação, a precedência é no local para determinar a classe de recurso que está atribuído. Classificação com base num utilizador da base de dados tem precedência sobre associação de função. Se criar um classificador que mapeia o utilizador de base de dados de utilizador para a classe de recursos de mediumrc. Em seguida, mapear a função de base de dados de RoleA (dos quais o utilizador é membro) para a classe de recursos de largerc. O classificador que mapeia o utilizador de base de dados para a classe de recursos de mediumrc irá ter precedência sobre o classificador de que a função de base de dados RoleA é mapeado para a classe de recursos de largerc.

Se um utilizador for membro de várias funções com classes de recursos diferentes atribuídos ou combinadas de vários classificadores, o utilizador recebe a atribuição de classe de recursos mais elevada.  Este comportamento é consistente com o comportamento de atribuição de classe de recursos existente.

## <a name="system-classifiers"></a>Classificadores de sistema

Classificação da carga de trabalho tem classificadores de carga de trabalho do sistema. Os classificadores de sistema mapeiam associações de função de classe de recursos existente para alocações de recursos de classe de recursos com importância normal. Não não possível remover classificadores de sistema. Para ver os classificadores de sistema, pode executar o abaixo de consulta:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>Misturar atribuições de classe com classificadores de recursos

Criado em seu nome de classificadores de System fornecem uma forma fácil de migrar a classificação de carga de trabalho. Utilizar os mapeamentos de função de classe de recursos com precedência de classificação, pode levar a misclassification como começar a criar novo classificadores com importância.

Considere o seguinte cenário:

Armazém de dados existente •an tem um utilizador de base de dados que dbauser atribuída a função de classe de recursos de largerc. A atribuição de classe de recursos foi feita com sp_addrolemember.
Armazém de dados de •the foi atualizado com a gestão da carga de trabalho.
•To testar a nova sintaxe de classificação, a função de base de dados DBARole (que DBAUser é um membro da), tem um classificador que criou para eles mapeando-os para mediumrc e de importância alta.
•When DBAUser efetua login e executa uma consulta, a consulta será atribuída ao largerc. Uma vez que um utilizador tem precedência sobre uma associação de função.

Para simplificar a resolução de problemas misclassification, recomendamos que remova mapeamentos de função de classe de recursos à medida que cria classificadores de carga de trabalho.  O código a seguir devolve as associações de função de classe de recurso existente.  Execute [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql) para cada nome de membro retornado da classe de recurso correspondente.

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember ‘[Resource Class]’, membername
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a classificação de carga de trabalho do armazém de dados SQL e importância, consulte [criar um classificador de carga de trabalho](quickstart-create-a-workload-classifier-tsql.md) e [importância de armazém de dados SQL](sql-data-warehouse-workload-importance.md). Ver [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) para ver as consultas e a importância atribuído.
