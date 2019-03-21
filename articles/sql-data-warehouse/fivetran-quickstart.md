---
title: Início rápido de Fivetran para o Azure SQL Data Warehouse | Documentos da Microsoft
description: Comece rapidamente com Fivetran e Azure SQL Data Warehouse.
services: sql-data-warehouse
author: hirokib
manager: jrj
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 10/12/2018
ms.author: elbutter
ms.reviewer: craigg
ms.openlocfilehash: 1d8b6d2df21fe0aabe85754e061db4deb129b32e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57895707"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>Comece a trabalhar rapidamente com Fivetran e o SQL Data Warehouse

Este início rápido descreve como configurar um novo utilizador Fivetran para trabalhar com o Azure SQL Data Warehouse. O artigo parte do princípio de que tem uma instância existente do SQL Data Warehouse.

## <a name="set-up-a-connection"></a>Configurar uma ligação

1. Encontre o nome de servidor completamente qualificado e o nome de base de dados que utiliza para ligar ao SQL Data Warehouse.
    
    Se precisar de ajuda para localizar estas informações, consulte [ligar ao Azure SQL Data Warehouse](sql-data-warehouse-connect-overview.md).

2. No Assistente de configuração, escolha se pretende ligar a sua base de dados diretamente ou através de um túnel SSH.

   Se optar por ligar diretamente à sua base de dados, tem de criar uma regra de firewall para permitir o acesso. Este método é o método mais simples e mais seguro.

   Se optar por ligar através de um túnel SSH, Fivetran liga-se para um servidor separado na sua rede. O servidor fornece um túnel SSH para a base de dados. Tem de utilizar este método se a base de dados estiver numa sub-rede acessível numa rede virtual.

3. Adicionar o endereço IP **52.0.2.4** à firewall ao nível do servidor para permitir ligações de entrada para a sua instância do SQL Data Warehouse de Fivetran.

   Para obter mais informações, consulte [criar uma regra de firewall ao nível do servidor](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Configurar as credenciais de utilizador

1. Ligar para o Azure SQL Data Warehouse com o SQL Server Management Studio ou a ferramenta de sua preferência. Inicie sessão como um utilizador de administrador do servidor. Em seguida, execute os seguintes comandos SQL para criar um utilizador para Fivetran:
    - Na base de dados mestra: 
    
      ```
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - Na base de dados do armazém de dados SQL:

      ```
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Conceda ao utilizador de Fivetran as seguintes permissões ao seu armazém:

    ```
    GRANT CONTROL to fivetran;
    ```

    É necessária a permissão de controlo para criar as credenciais no âmbito da base de dados que são utilizadas quando um utilizador carrega ficheiros de armazenamento de Blobs do Azure com o PolyBase.

3. Adicione uma classe de recursos adequados para o usuário Fivetran. A classe de recursos que utilizar depende da memória necessária para criar um índice columnstore. Por exemplo, integrações com produtos, como o Marketo e o Salesforce necessitam de uma classe de recursos maior devido ao elevado número de colunas e o maior volume de dados os produtos de usar. Uma classe de recursos maior exige mais memória para criar índices columnstore.

    Recomendamos que utilize classes estáticas. Pode começar com o `staticrc20` classe de recursos. O `staticrc20` classe de recursos aloca 200 MB para cada usuário, independentemente do nível de desempenho que utilizar. Se a indexação columnstore falhar no nível da classe de recursos inicial, aumente a classe de recursos.

    ```
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Para obter mais informações, leia sobre [limites de memória e simultaneidade](memory-and-concurrency-limits.md) e [classes de recursos](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory).


## <a name="sign-in-to-fivetran"></a>Inicie sessão no Fivetran

Para iniciar sessão no Fivetran, introduza as credenciais que utiliza para aceder ao SQL Data Warehouse: 

* Anfitrião (o nome do servidor).
* Porta.
* Base de dados.
* Utilizador (o nome de utilizador deve estar **fivetran\@_server_name_**  em que *server_name* faz parte do seu URI de anfitrião do Azure: ***server_name *. Database.Windows.NET**).
* Palavra-passe.
