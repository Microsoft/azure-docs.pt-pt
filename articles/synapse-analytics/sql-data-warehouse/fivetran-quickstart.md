---
title: 'Quickstart: Fivetran e armazém de dados'
description: Começa com o Fivetran e um armazém de dados Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 8f164232a3b1782511758f93a9e9b8d17d3714d5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414276"
---
# <a name="quickstart-fivetran-with-data-warehouse"></a>Quickstart: Fivetran com armazém de dados 

Este quickstart descreve como configurar um novo utilizador fivetran para trabalhar com um armazém de dados Azure Synapse Analytics aprovisionado com um SQL Pool. O artigo assume que tem um armazém de dados existente.

## <a name="set-up-a-connection"></a>Configurar uma ligação

1. Encontre o nome do servidor e o nome de base de dados totalmente qualificados que utiliza para ligar ao seu armazém de dados.
    
    Se precisar de ajuda para encontrar esta informação, consulte [Connect to your data warehouse](../sql/connect-overview.md).

2. No assistente de configuração, escolha se liga a sua base de dados diretamente ou utilizando um túnel SSH.

   Se optar por ligar-se diretamente à sua base de dados, tem de criar uma regra de firewall para permitir o acesso. Este método é o método mais simples e seguro.

   Se optar por ligar-se utilizando um túnel SSH, o Fivetran liga-se a um servidor separado na sua rede. O servidor fornece um túnel SSH à sua base de dados. Deve utilizar este método se a sua base de dados estiver numa subrede inacessível numa rede virtual.

3. Adicione o endereço IP **52.0.2.4** à firewall do seu servidor para permitir a entrada de ligações à sua instância de armazém de dados a partir de Fivetran.

   Para mais informações, consulte [Criar uma regra de firewall ao nível do servidor](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Configurar credenciais de utilizador

1. Conecte-se ao seu armazém de dados utilizando o SQL Server Management Studio (SSMS) ou a ferramenta que prefere. Inscreva-se como um utilizador de administração do servidor. Em seguida, executar os seguintes comandos SQL para criar um utilizador para Fivetran:

    - Na base de dados principal: 
    
      ```sql
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - Na base de dados dos armazéns de dados:

      ```sql
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Conceda ao utilizador fivetran as seguintes permissões ao seu armazém de dados:

    ```sql
    GRANT CONTROL to fivetran;
    ```

    A permissão CONTROL é necessária para criar credenciais de base de dados que são usadas quando um utilizador carrega ficheiros do armazenamento Do Blob Azure utilizando a PolyBase.

3. Adicione uma classe de recursos adequada ao utilizador Fivetran. A classe de recursos que usa depende da memória necessária para criar um índice de loja de colunas. Por exemplo, as integrações com produtos como marketo e Salesforce requerem uma classe de recursos mais elevada devido ao grande número de colunas e ao maior volume de dados que os produtos utilizam. Uma classe de recursos mais elevado requer mais memória para criar índices de lojas de colunas.

    Recomendamos que utilize aulas de recursos estáticos. Podecomeçar com `staticrc20` a classe de recursos. A `staticrc20` classe de recursos atribui 200 MB para cada utilizador, independentemente do nível de desempenho que utilizar. Se a indexação da coluna falhar ao nível inicial da classe de recursos, aumente a classe de recursos.

    ```sql
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Para mais informações, leia sobre os limites de [memória e concurrency](memory-concurrency-limits.md) e classes de [recursos.](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory)


## <a name="connect-from-fivetran"></a>Ligar a partir de Fivetran

Para se ligar ao seu armazém de dados a partir da sua conta Fivetran, insira as credenciais que utiliza para aceder ao seu armazém de dados: 

* Anfitrião (nome do seu servidor).
* Porto, porto.
* Base de dados.
* Utilizador (o nome do utilizador deve ser **de cinco transerver_name\@** onde *server_name* faz parte do seu anfitrião Azure URI: nome do ** _servidor\__.database.windows.net**).
* Senha.
