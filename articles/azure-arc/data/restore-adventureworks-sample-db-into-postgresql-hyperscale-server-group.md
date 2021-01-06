---
title: Restaurar a base de dados de amostras AdventureWorks para Azure Arc ativada em Hiperescala PósgreSQL
description: Restaurar a base de dados de amostras AdventureWorks para Azure Arc ativada em Hiperescala PósgreSQL
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: b1ee779be118fcafd0efa2bd2718ece1c34c50d1
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97954333"
---
# <a name="restore-the-adventureworks-sample-database-to-azure-arc-enabled-postgresql-hyperscale"></a>Restaurar a base de dados de amostras AdventureWorks para Azure Arc ativada em Hiperescala PósgreSQL

[AdventureWorks](/sql/samples/adventureworks-install-configure) é uma base de dados de amostras contendo uma base de dados OLTP usada em tutoriais e exemplos. É fornecido e mantido pela Microsoft como parte das amostras do [sql Server GitHub repositório](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases).

Um projeto de código aberto converteu a base de dados AdventureWorks para ser compatível com a hiperescala postgreSQL ativada.
- [Projeto original](https://github.com/lorint/AdventureWorks-for-Postgres)
- [Acompanhe o projeto que pré-converte os ficheiros CSV para serem compatíveis com o PostgreSQL](https://github.com/NorfolkDataSci/adventure-works-postgres)

Este documento descreve um processo simples para obter a base de dados de amostras AdventureWorks restaurada no seu grupo de servidores de hiperescala PostgreSQL.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="download-the-adventureworks-backup-file"></a>Descarregue o ficheiro de backup adventureWorks

Descarregue o ficheiro adventureWorks .sql no seu recipiente do grupo de servidores PostgreSQL Hyperscale. Neste exemplo, usaremos o `kubectl exec` comando para executar remotamente um comando no contentor do grupo de servidores PostgreSQL Hyperscale para descarregar o ficheiro no recipiente. Pode descarregar este ficheiro a partir de qualquer local acessível por `curl` . Utilize este mesmo método se tiver outros ficheiros de back-up de base de dados que pretende puxar no recipiente do grupo de servidores de hiperescala PostgreSQL. Uma vez que está no recipiente do grupo de servidores de hiperescala PostgreSQL, é fácil criar a base de dados, o esquema e povoar os dados.

Executar um comando como este para descarregar os ficheiros substitua o valor do nome do pod e nome do espaço antes de o executar:

> [!NOTE]
>  O seu contentor terá de ter conectividade com a Internet acima do 443 para descarregar o ficheiro do GitHub.

> [!NOTE]
>  Utilize o nome do nó de pod do nó coordenador do grupo de servidores Postgres Hyperscale. O seu nome é <server group name> -0.  Se não tiver certeza do nome da cápsula, executar o comando `kubectl get pod`

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres  -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/main/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"

#Example:
#kubectl exec postgres02-0 -n arc -c postgres -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/main/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"
```

## <a name="step-2-restore-the-adventureworks-database"></a>Passo 2: Restaurar a base de dados AdventureWorks

Da mesma forma, pode executar um comando executivo de kubectl para utilizar a ferramenta psql CLI que está incluída nos recipientes do grupo de servidores de hiperescala PostgreSQL para criar e carregar a base de dados.

Executar um comando como este para criar a base de dados vazia substituindo primeiro o valor do nome do pod e o nome do espaço antes de executá-lo.

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'
```

Em seguida, executar um comando como este para restaurar a base de dados substituindo o valor do nome do pod e o nome do espaço antes de executá-lo.

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql
```


> **Nota: Não verá tantos benefícios de desempenho de correr em Azure Arc ativado Em Escala pós-SQL até que se escalone e distribua os dados/tabelas através dos nós de trabalhadores do seu grupo de servidores postgreSQL Hyperscale. Ver [Passos Sugeridos.](#suggested-next-steps)**

## <a name="suggested-next-steps"></a>Passos próximos sugeridos
- Leia os conceitos e guias de como fazer a Base de Dados Azure para a Hiperescala Pós-SQL para distribuir os seus dados através de vários nós de hiperescala postgreSQL e para beneficiar de toda a potência da Base de Dados Azure para a Escala de Hiperescala Pós-SQL. :
    * [Nós e tabelas](../../postgresql/concepts-hyperscale-nodes.md)
    * [Determinar o tipo de aplicação](../../postgresql/concepts-hyperscale-app-type.md)
    * [Escolher uma coluna de distribuição](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Colocalização de tabela](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuir e modificar tabelas](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Desenhe uma base de dados multi-inquilinos](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Desenhe um painel de análise em tempo real](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

   > \* Nos documentos acima, salte as secções **Iniciar sessão No portal Azure,**& **Criar uma Base de Dados Azure para PostgreSQL - Hiperescala (Citus)**. Implemente os passos restantes na sua implantação do Arco Azure. Estas secções são específicas da Base de Dados Azure para a Hiperescala Pós-SQL (Citus) oferecida como um serviço PaaS na nuvem Azure, mas as outras partes dos documentos são diretamente aplicáveis à sua Hiperescala Pós-Altura pós-SQL ativada pelo Arco Azure.

- [Aumentar horizontalmente o grupo de servidores de Hyperscale da Base de Dados do Azure para PostgreSQL](scale-out-postgresql-hyperscale-server-group.md)
