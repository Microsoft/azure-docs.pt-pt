---
title: Início rápido do Fivetran
description: Comece rapidamente com o Fivetran e o Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 715f891484458f3bf3febc6807c3490b88062d50
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229097"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>Comece rapidamente com o Fivetran e o SQL Data Warehouse

Este guia de início rápido descreve como configurar um novo usuário do Fivetran para trabalhar com o Azure SQL Data Warehouse. O artigo pressupõe que você tenha uma instância existente do SQL Data Warehouse.

## <a name="set-up-a-connection"></a>Configurar uma conexão

1. Localize o nome do servidor totalmente qualificado e o nome do banco de dados que você usa para se conectar ao SQL Data Warehouse.
    
    Se precisar de ajuda para encontrar essas informações, consulte [conectar-se ao Azure SQL data warehouse](sql-data-warehouse-connect-overview.md).

2. No assistente de instalação, escolha se deseja conectar seu banco de dados diretamente ou usando um túnel SSH.

   Se você optar por se conectar diretamente ao seu banco de dados, deverá criar uma regra de firewall para permitir o acesso. Esse método é o método mais simples e seguro.

   Se você optar por se conectar usando um túnel SSH, o Fivetran conectará a um servidor separado em sua rede. O servidor fornece um túnel SSH para seu banco de dados. Você deve usar esse método se seu banco de dados estiver em uma sub-rede inacessível em uma rede virtual.

3. Adicione o endereço IP **52.0.2.4** ao seu firewall de nível de servidor para permitir conexões de entrada para sua instância de SQL data warehouse do Fivetran.

   Para obter mais informações, consulte [criar uma regra de firewall no nível de servidor](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Configurar as credenciais do usuário

1. Conecte-se ao SQL Data Warehouse do Azure usando SQL Server Management Studio ou a ferramenta que você preferir. Entre como um usuário administrador do servidor. Em seguida, execute os seguintes comandos SQL para criar um usuário para Fivetran:
    - No banco de dados mestre: 
    
      ```
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - No banco de dados SQL Data Warehouse:

      ```
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Conceda ao usuário Fivetran as seguintes permissões para seu depósito:

    ```
    GRANT CONTROL to fivetran;
    ```

    A permissão CONTROL é necessária para criar credenciais no escopo do banco de dados que são usadas quando um usuário carrega arquivos do armazenamento de BLOBs do Azure usando o polybase.

3. Adicione uma classe de recurso adequada ao usuário Fivetran. A classe de recurso usada depende da memória necessária para criar um índice columnstore. Por exemplo, as integrações com produtos como Marketo e Salesforce exigem uma classe de recursos mais alta devido ao grande número de colunas e ao maior volume de dados que os produtos usam. Uma classe de recurso superior requer mais memória para criar índices columnstore.

    Recomendamos que você use classes de recursos estáticos. Você pode começar com a classe de recurso `staticrc20`. A classe de recurso `staticrc20` aloca 200 MB para cada usuário, independentemente do nível de desempenho que você usar. Se a indexação columnstore falhar no nível de classe de recurso inicial, aumente a classe de recurso.

    ```
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Para obter mais informações, leia sobre [limites de memória e de simultaneidade](memory-concurrency-limits.md) e [classes de recursos](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory).


## <a name="sign-in-to-fivetran"></a>Entrar no Fivetran

Para entrar no Fivetran, insira as credenciais que você usa para acessar SQL Data Warehouse: 

* Host (o nome do servidor).
* Porto.
* Base de dados.
* Usuário (o nome de usuário deve ser **fivetran\@_server_name_**  em que *server_name* faz parte do URI do host do Azure: ***server_name *. Database. Windows. net**).
* La.
