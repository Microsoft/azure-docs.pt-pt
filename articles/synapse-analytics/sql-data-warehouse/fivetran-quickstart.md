---
title: 'Quickstart: Fivetran e armazém de dados'
description: Começa com a Fivetran e um armazém de dados Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 96e679c0b284cc649dbde3fba1b640f4e09df05e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001852"
---
# <a name="quickstart-fivetran-with-data-warehouse"></a>Quickstart: Fivetran com armazém de dados 

Este quickstart descreve como configurar um novo utilizador fivetran para trabalhar com um armazém de dados Azure Synapse Analytics a provisionado com uma piscina SQL. O artigo assume que tem um armazém de dados existente.

## <a name="set-up-a-connection"></a>Configurar uma ligação

1. Encontre o nome e o nome de base de dados totalmente qualificados que utiliza para ligar ao seu armazém de dados.
    
    Se precisar de ajuda para encontrar esta informação, consulte [o Connect to your data warehouse](../sql/connect-overview.md).

2. No assistente de configuração, escolha se liga a sua base de dados diretamente ou utilizando um túnel SSH.

   Se optar por ligar diretamente à sua base de dados, tem de criar uma regra de firewall para permitir o acesso. Este método é o método mais simples e seguro.

   Se optar por ligar-se através de um túnel SSH, o Fivetran liga-se a um servidor separado da sua rede. O servidor fornece um túnel SSH para a sua base de dados. Tem de utilizar este método se a sua base de dados estiver numa sub-rede inacessível numa rede virtual.

3. Adicione o endereço IP **52.0.2.4** à firewall de nível do servidor para permitir a entrada de ligações à sua instância de armazém de dados a partir de Fivetran.

   Para obter mais informações, consulte [Criar uma regra de firewall ao nível do servidor](create-data-warehouse-portal.md#create-a-server-level-firewall-rule).

## <a name="set-up-user-credentials"></a>Configurar credenciais de utilizador

1. Conecte-se ao seu armazém de dados utilizando o SQL Server Management Studio (SSMS) ou a ferramenta que preferir. Inscreva-se como um utilizador de administração de servidor. Em seguida, executar os seguintes comandos SQL para criar um utilizador para Fivetran:

    - Na base de dados principal: 
    
      ```sql
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - Na base de dados do armazém de dados:

      ```sql
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. Conceda ao utilizador Fivetran as seguintes permissões para o seu armazém de dados:

    ```sql
    GRANT CONTROL to fivetran;
    ```

    A permissão DE CONTROLO é necessária para criar credenciais de âmbito de base de dados que são utilizadas quando um utilizador carrega ficheiros a partir do armazenamento do Azure Blob utilizando o PolyBase.

3. Adicione uma classe de recursos adequada ao utilizador Fivetran. A classe de recursos que utiliza depende da memória necessária para criar um índice de loja de colunas. Por exemplo, integrações com produtos como o Marketo e o Salesforce requerem uma classe de recursos mais elevada devido ao grande número de colunas e ao maior volume de dados que os produtos utilizam. Uma classe de recursos mais alta requer mais memória para criar índices de loja de colunas.

    Recomendamos que utilize classes de recursos estáticos. Pode começar com a `staticrc20` classe de recursos. A `staticrc20` classe de recursos atribui 200 MB a cada utilizador, independentemente do nível de desempenho que utiliza. Se a indexação da loja de colunas falhar ao nível inicial da classe de recursos, aumente a classe de recursos.

    ```sql
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    Para mais informações, leia sobre [os limites de memória e concência](memory-concurrency-limits.md) e [classes de recursos.](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory)


## <a name="connect-from-fivetran"></a>Ligar a partir de Fivetran

Para se ligar ao seu armazém de dados a partir da sua conta Fivetran, insira as credenciais que utiliza para aceder ao seu armazém de dados: 

* Anfitrião (nome do seu servidor).
* Porto, porto.
* Base de dados.
* Utilizador (o nome de utilizador deve ser **server_name cincotran \@ _server_name_** onde *server_name* faz parte do seu Azure host URI: nome do **_servidor \__.database.windows.net**).
* A palavra-passe.
