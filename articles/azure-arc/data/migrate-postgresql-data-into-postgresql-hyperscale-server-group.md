---
title: Migrar dados de uma base de dados PostgreSQL para um grupo de servidores de hiperescala pós-escala do Arco Azure
titleSuffix: Azure Arc enabled database services
description: Migrar dados de uma base de dados PostgreSQL para um grupo de servidores de hiperescala pós-escala do Arco Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d9cbfc30b10373ad2a4f4304987dac426b5dcabe
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101643580"
---
# <a name="migrate-postgresql-database-to-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Migrar a base de dados PostgreSQL para O Arco Azure ativado grupo de servidores de hiperescala pós-SQL

Este documento descreve os passos para obter a sua base de dados PostgreSQL existente (uma que não foi hospedada em Azure Arc ativou serviços de dados) no seu grupo de servidores de hiperescala PósgreSQL ativado por Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="considerations"></a>Considerações

O grupo de servidores pós-escala pós-escala Azure Arc é a versão comunitária do PostgreSQL e funciona com a extensão CitusData ativada. Assim, qualquer ferramenta que funcione em PostgreSQL fora do Azure Arc deve trabalhar com o grupo de servidores pós-escala pós-SQL ativado pelo Arco Azure.


Como tal, com o conjunto de ferramentas que utiliza hoje para Postgres, deverá ser capaz de:
1. Faça backup da sua base de dados Postgres a partir do seu caso hospedado fora de Azure Arc
2. Restaure-o no seu Azure Arc ativado pelo grupo de servidores de hiperescala PostgreSQL

O que lhe restará fazer é:
- redefinir os parâmetros do servidor
- redefinir os contextos de segurança: recriar utilizadores, funções e repor permissões...

Para fazer esta operação de backup/restauro, pode utilizar qualquer ferramenta capaz de fazer backup/restauro para Postgres. Por exemplo:
- Azure Data Studio e a sua extensão Postgres
- `pgcli`
- `pgAdmin`
- `pg_dump`
- `pg_restore`
- `psql`
- ...

## <a name="example"></a>Exemplo
Vamos ilustrar os passos usando a `pgAdmin` ferramenta.
Considere a seguinte configuração:
- **Fonte:**  
    Um servidor Postgres a funcionar nas instalações de um servidor de metal nu e chamado JEANYDSRV. É da versão 12 e acolhe uma base de dados chamada MyOnPremPostgresDB que tem uma tabela T1 que tem 1 linha :::image type="content" source="media/postgres-hyperscale/migrate-pg-source.jpg" alt-text="de origem migrate":::

- **Destino:**  
    Um servidor Postgres em execução em um ambiente Azure Arc e nomeado postgres01. É da versão 12. Não tem nenhuma base de dados, exceto a base de dados padrão dos Postgres.  
    :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination.jpg" alt-text="Destino migratório":::


### <a name="take-a-backup-of-the-source-database-on-premises"></a>Faça uma cópia de segurança da base de dados de origem nas instalações

:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup.jpg" alt-text="Migrar-source-backup":::

Configure-o:
1. Dê-lhe um nome de ficheiro: **MySourceBackup**
2. Desafie o formato para  
 :::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup2.jpg" alt-text="Migração Personalizada-fonte-configuração de backup":::

A cópia de segurança completa com sucesso:  
:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup3.jpg" alt-text="Migração-fonte-backup-completa":::

### <a name="create-an-empty-database-on-the-destination-system-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Crie uma base de dados vazia no sistema de destino no seu grupo de servidores de hiperescala pós-escala do Arco Azure

> [!NOTE]
> Para registar uma instância postgres na ferramenta, precisa de utilizar o `pgAdmin` IP público do seu caso no seu cluster Kubernetes e definir o contexto de porta e segurança adequadamente. Encontrará estes detalhes na `psql` linha de ponta depois de executar o seguinte comando:

```console
azdata arc postgres endpoint list -n postgres01
```
Que devolve uma saída como:
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

Vamos nomear a base de dados de destino **RESTORED_MyOnPremPostgresDB.**

:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg" alt-text="Migração-destino-db-create" lightbox="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg":::

### <a name="restore-the-database-in-your-arc-setup"></a>Restaurar a base de dados na sua configuração Arc

:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore.jpg" alt-text="Migratre-db-restore":::

Configure a restauração:
1. Aponte para o ficheiro que contém a cópia de segurança para restaurar: **MySourceBackup**
2. Mantenha o formato definido para **Personalizado ou alcatrão** 
    :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore2.jpg" alt-text="Migrar-db-restaurar-configure":::

3. Clique **em Restaurar**.  

   A restauração é um sucesso.  
   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore3.jpg" alt-text="Migrar-db-restauro-concluído":::

### <a name="verify-that-the-database-was-successfully-restored-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Verifique se a base de dados foi restaurada com sucesso no seu grupo de servidores de hiperescala PostgreSQL ativado

Utilizar qualquer um dos seguintes métodos:

**A partir `pgAdmin` de:**  

Expanda a instância postgres hospedada na sua configuração Azure Arc. Verá a tabela na base de dados que restaurou e quando selecionar os dados mostra a mesma linha que tem no local:

   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestoreverif.jpg" alt-text="Migração-db-restauração-verificação":::

**De dentro da `psql` sua configuração Azure Arc:**  

Dentro da sua configuração Arc pode utilizar `psql` para ligar à sua instância Postgres, definir o contexto da base de dados e consultar os `RESTORED_MyOnPremPostgresDB` dados:

1. Enuda os pontos finais para ajudar na sua cadeia de `psql` ligação:

   ```console
   azdata arc postgres endpoint list -n postgres01
   [
     {
       "Description": "PostgreSQL Instance",
       "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
     },
     {
       "Description": "Log Search Dashboard",
       "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
     },
     {
       "Description": "Metrics Dashboard",
       "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
     }
   ]
   ```

1. A partir da sua `psql` cadeia de ligação utilize o `-d` parâmetro para indicar o nome da base de dados. Com o comando abaixo, será solicitado para a senha:

   ```console
   psql -d RESTORED_MyOnPremPostgresDB -U postgres -h 10.0.0.4 -p 32639
   ```

   `psql` liga.

   ```output
   Password for user postgres:
   psql (10.12 (Ubuntu 10.12-0ubuntu0.18.04.1), server 12.3 (Debian 12.3-1.pgdg100+1))
   WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
   SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
   Type "help" for help.

   RESTORED_MyOnPremPostgresDB=#   
   ```

1. Selecione a tabela e verá os dados que restaurou a partir da instância postgres no local:

   ```console
   RESTORED_MyOnPremPostgresDB=# select * from t1;
   ```

   ```output
    col1 |    col2
   ------+-------------
       1 | BobbyIsADog
   (1 row)
   ```

> [!NOTE]
> - Não verá tantos benefícios de desempenho de correr em Azure Arc ativado Em Escala de Hiperescala PósgreSQL até que se escalone e você fragmento/distribuir os dados através dos nós de trabalhador do seu grupo de servidores de hiperescala PostgreSQL. Ver [os próximos passos](#next-steps).
>
> - Hoje em dia não é possível "embarcar em Azure Arc" uma instância postgres existente que iria funcionar em instalações ou em qualquer outra nuvem. Por outras palavras, não é possível instalar algum tipo de "agente Azure Arc" na sua instância postgres existente para torná-lo uma configuração postgres ativada por Azure Arc. Em vez disso, precisa criar uma nova instância postgres e transferir dados para o mesmo. Pode utilizar a técnica acima apresentada para o fazer ou poderá utilizar qualquer ferramenta ETL à sua escolha.

## <a name="next-steps"></a>Passos seguintes

- Leia os conceitos e guias de como fazer a Base de Dados Azure para a Hiperescala Pós-SQL para distribuir os seus dados através de vários nós de hiperescala postgreSQL e para beneficiar de toda a potência da Base de Dados Azure para a Hiperescala Pós-SQL:
    * [Nós e tabelas](../../postgresql/concepts-hyperscale-nodes.md)
    * [Determinar o tipo de aplicação](../../postgresql/concepts-hyperscale-app-type.md)
    * [Escolher uma coluna de distribuição](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Colocalização de tabela](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuir e modificar tabelas](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Desenhe uma base de dados multi-inquilinos](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Desenhe um painel de análise em tempo real](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> *Nestes documentos, ignore as secções **Iniciar sessão no portal Azure** e **Criar uma Base de Dados Azure para Postgres - Hiperescala (Citus)**. Implemente os passos restantes na sua implantação do Arco Azure. Estas secções são específicas da Base de Dados Azure para a Hiperescala Pós-SQL (Citus) oferecida como um serviço PaaS na nuvem Azure, mas as outras partes dos documentos são diretamente aplicáveis à sua Hiperescala Pós-Altura pós-SQL ativada pelo Arco Azure.

- [Aumentar horizontalmente o grupo de servidores de Hyperscale da Base de Dados do Azure para PostgreSQL](scale-out-postgresql-hyperscale-server-group.md)
