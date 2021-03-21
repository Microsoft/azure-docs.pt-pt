---
title: Configurar os parâmetros do servidor do motor Postgres para o seu grupo de servidor de hiperescala PostgreSQL em Azure Arc
titleSuffix: Azure Arc enabled data services
description: Configurar os parâmetros do servidor do motor Postgres para o seu grupo de servidor de hiperescala PostgreSQL em Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: cdbddfc84b3f71576cfd0299f2babec859b4ef1f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92311050"
---
# <a name="set-the-database-engine-settings-for-azure-arc-enabled-postgresql-hyperscale"></a>Estabelecer as definições do motor de base de dados para o PostgreSQL Hyperscale preparado para o Azure Arc

Este documento descreve os passos para configurar as definições do motor de base de dados do grupo de servidores do PostgreSQL Hyperscale para valores personalizados (não predefinidos). Para obter mais informações sobre quais os parâmetros do motor de base de dados que podem ser definidos e qual é o seu valor padrão, consulte a documentação postgreSQL [aqui](https://www.postgresql.org/docs/current/runtime-config.html).

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!NOTE]
> A pré-visualização não suporta a definição dos seguintes parâmetros: 
>
> - `archive_command`
> - `archive_timeout`
> - `log_directory`
> - `log_file_mode`
> - `log_filename`
> - `restore_command`
> - `shared_preload_libraries`
> - `synchronous_commit`
> - `ssl`
> - `wal_level`

## <a name="syntax"></a>Syntax

O formato geral do comando para configurar as definições do motor da base de dados é:

```console
azdata arc postgres server edit -n <server group name>, [{--engine-settings, -e}] [{--replace-engine-settings, --re}] {'<parameter name>=<parameter value>, ...'}
```

## <a name="show-current-custom-values"></a>Mostrar valores personalizados atuais

### <a name="with-azure-data-cli-azdata-command"></a>Com [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] comando

```console
azdata arc postgres server show -n <server group name>
```

Por exemplo:

```console
azdata arc postgres server show -n postgres01
```

Este comando devolve a especificação do grupo de servidores em que veria os parâmetros definidos. Se não houver secção de definições de motor, significa que todos os parâmetros estão a funcionar no seu valor predefinido:

```console
 "
...
engine": {
      "settings": {
        "default": {
          "autovacuum_vacuum_threshold": "65"
        }
      }
    },
...
```

### <a name="with-kubectl-command"></a>Com comando kubectl

Siga os passos abaixo.

1. Recupere o tipo de definição de recurso personalizado para o seu grupo de servidor

   Executar:

   ```console
   azdata arc postgres server show -n <server group name>
   ```

   Por exemplo:

   ```console
   azdata arc postgres server show -n postgres01
   ```

   Este comando devolve a especificação do grupo de servidores em que veria os parâmetros definidos. Se não houver secção de definições de motor, significa que todos os parâmetros estão a funcionar no seu valor predefinido:

   ```output
   > {
     >"apiVersion": "arcdata.microsoft.com/v1alpha1",
     >"**kind**": "**postgresql-12**",
     >"metadata": {
       >"creationTimestamp": "2020-08-25T14:32:23Z",
       >"generation": 1,
       >"name": "postgres01",
       >"namespace": "arc",  
   ```

   Nos resultados de saída, procure o campo `kind` e reserve o valor, por exemplo: `postgresql-12` .

2. Descreva o recurso personalizado Kubernetes correspondente ao seu grupo de servidor 

   O formato geral do comando é:

   ```console
   kubectl describe <kind of the custom resource> <server group name> -n <namespace name>
   ```

   Por exemplo:

   ```console
   kubectl describe postgresql-12 postgres01
   ```

   Se existirem valores personalizados definidos para as definições do motor, retorna-os. Por exemplo:

   ```output
   Engine:
   ...
    Settings:
      Default:
        autovacuum_vacuum_threshold:  65
   ```

   Se não tiver definido valores personalizados para nenhuma das definições do motor, a secção de Definições do Motor da `resultset` 1 será vazia como:

   ```output
   Engine:
   ...
       Settings:
         Default:
   ```

## <a name="set-custom-values-for-engine-settings"></a>Definir valores personalizados para configurações do motor

Os comandos abaixo definem os parâmetros do nó Coordenador e dos nós do Trabalhador da sua Hiperescala PostgreSQL para os mesmos valores. Ainda não é possível definir parâmetros por função no seu grupo de servidor. Ou seja, ainda não é possível configurar um determinado parâmetro para um nó coordenador específico e para outro valor para os nós do Trabalhador.

### <a name="set-a-single-parameter"></a>Definir um único parâmetro

```console
azdata arc server edit -n <server group name> -e <parameter name>=<parameter value>
```

Por exemplo:

```console
azdata arc postgres server edit -n postgres01 -e shared_buffers=8MB
```

### <a name="set-multiple-parameters-with-a-single-command"></a>Definir vários parâmetros com um único comando

```console
azdata arc postgres server edit -n <server group name> -e '<parameter name>=<parameter value>, <parameter name>=<parameter value>,...'
```

Por exemplo:

```console
azdata arc postgres server edit -n postgres01 -e 'shared_buffers=8MB, max_connections=50'
```

### <a name="reset-a-parameter-to-its-default-value"></a>Redefinir um parâmetro para o seu valor padrão

Para redefinir um parâmetro ao seu valor predefinido, desajuste-o sem indicar um valor. 

Por exemplo:

```console
azdata arc postgres server edit -n postgres01 -e shared_buffers=
```

### <a name="reset-all-parameters-to-their-default-values"></a>Redefinir todos os parâmetros para os seus valores predefinidos

```console
azdata arc postgres server edit -n <server group name> -e '' -re
```

Por exemplo:

```console
azdata arc postgres server edit -n postgres01 -e '' -re
```

## <a name="special-considerations"></a>Considerações especiais

### <a name="set-a-parameter-which-value-contains-a-comma-space-or-special-character"></a>Desa ajuste um parâmetro que o valor contém uma vírgula, espaço ou caráter especial

```console
azdata arc postgres server edit -n <server group name> -e '<parameter name>="<parameter value>"'
```

Por exemplo:

```console
azdata arc postgres server edit -n postgres01 -e 'custom_variable_classes = "plpgsql,plperl"'
```

### <a name="pass-an-environment-variable-in-a-parameter-value"></a>Passe uma variável de ambiente num valor de parâmetro

A variável ambiental deve ser embrulhada dentro de "'" para que não seja resolvida antes de ser definida.

Por exemplo: 

```console
azdata arc postgres server edit -n postgres01 -e 'search_path = "$user"'
```

## <a name="next-steps"></a>Passos seguintes
- Leia sobre [a escala (adicionar nós de trabalhador)](scale-out-postgresql-hyperscale-server-group.md) o seu grupo de servidor
- Leia sobre [o escalonamento para cima ou para baixo (aumentando/diminuindo a memória/vcores)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) o seu grupo de servidor
