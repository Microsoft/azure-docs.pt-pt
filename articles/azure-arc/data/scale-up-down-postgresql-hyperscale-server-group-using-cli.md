---
title: Dimensione para cima e para baixo uma base de dados de Azure para o grupo de servidores de hiperescala PostgreSQL usando CLI (azdata ou kubectl)
description: Dimensione para cima e para baixo uma base de dados de Azure para o grupo de servidores de hiperescala PostgreSQL usando CLI (azdata ou kubectl)
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 303a919cc0afc9b5db49918233f3e5718a896646
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148045"
---
# <a name="scale-up-and-down-an-azure-database-for-postgresql-hyperscale-server-group-using-cli-azdata-or-kubectl"></a>Dimensione para cima e para baixo uma base de dados de Azure para o grupo de servidores de hiperescala PostgreSQL usando CLI (azdata ou kubectl)



Há momentos em que poderá ter de alterar as características ou a definição de um grupo de servidores. Por exemplo:

- Escalar para cima ou para baixo o número de vCores que cada um dos nós coordenador ou operário usa
- Escalar para cima ou para baixo a memória que cada um dos nós coordenador ou operário usa

Este guia explica como escalar vCore e/ou memória.

Escalar para cima ou para baixo as definições vCore ou memória do seu grupo de servidor significa que tem a possibilidade de definir um mínimo e/ou um máximo para cada uma das definições vCore e memória. Se pretender configurar o seu grupo de servidor para utilizar um número específico de vCore ou uma quantidade específica de memória, definirá as definições de min iguais às definições máximas.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="show-the-current-definition-of-the-server-group"></a>Mostrar a definição atual do grupo de servidor

Para mostrar a definição atual do seu grupo de servidor e ver quais são as definições vCore e Memória atuais, execute qualquer um dos seguintes comandos:

### <a name="cli-with-azdata"></a>CLI com azdata

```console
azdata arc postgres server show -n <server group name>
```
### <a name="cli-with-kubectl"></a>CLI com kubectl

```console
kubectl describe postgresql-12/<server group name> [-n <namespace name>]
```
> [!NOTE]
> Se criou um grupo de servidores da versão 11 do PostgreSQL, execute `kubectl describe postgresql-11/<server group name>` em vez disso.

Devolve a configuração do seu grupo de servidor. Se criou o grupo de servidor com as definições predefinidos, deverá ver a definição da seguinte forma:

```console
"scheduling": {
      "default": {
        "resources": {
          "requests": {
            "memory": "256Mi"
          }
        }
      }
    },
```

## <a name="interpret-the-definition-of-the-server-group"></a>Interpretar a definição do grupo de servidor

Na definição de um grupo de servidor, a secção que transporta as definições de min/max vCore por nó e memória min/max por nó é a secção **"agendamento".** Nessa secção, as definições máximas serão persistiu numa subsecção chamada **"limites"** e as definições min são persistiu na subsecção denominada **"pedidos".**

Se definir definições min diferentes das definições máximas, a configuração garante que o seu grupo de servidor é alocado os recursos solicitados se precisar. Não excederá os limites definidos.

Os recursos (vCores e memória) que serão realmente utilizados pelo seu grupo de servidor estão até às definições máximas e dependem das cargas de trabalho e dos recursos disponíveis no cluster. Se não tapar as definições com um máximo, o seu grupo de servidor poderá utilizar até todos os recursos que o cluster Kubernetes atribui aos nós kubernetes em que o seu grupo de servidor está programado.

Estas definições vCore e memória aplicam-se a cada um dos nós de hiperescala PostgreSQL (nó coordenador e nós de trabalhador). Ainda não é apoiado para definir separadamente as definições do nó coordenador e dos nós dos trabalhadores.

Numa configuração predefinida, apenas a memória mínima é definida para 256Mi, uma vez que é a quantidade mínima de memória recomendada para executar a Escala de Hiperescala PostgreSQL.

> [!NOTE]
> Definir um mínimo não significa que o grupo de servidor utilizará necessariamente esse mínimo. Significa que, se o grupo de servidores precisar, é garantido que será atribuído pelo menos este mínimo. Por exemplo, vamos considerar que `--minCpu 2` definimos. Não significa que o grupo de servidores esteja sempre a utilizar pelo menos 2 vCores. Em vez disso, significa que o grupo de sever pode começar a usar menos de 2 vCores se não precisar de tanto e é garantido que será atribuído pelo menos 2 vCores se precisar deles mais tarde. Implica que o cluster Kubernetes aloca recursos a outras cargas de trabalho de forma a poder alocar 2 vCores ao grupo de servidores se alguma vez precisar deles.

>[!NOTE]
>Antes de modificar a configuração do seu sistema, certifique-se de se familiarizar com o modelo de recursos Kubernetes [aqui](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

## <a name="scale-up-the-server-group"></a>Dimensione o grupo de servidores

As definições que está prestes a definir têm de ser consideradas dentro da configuração definida para o seu cluster Kubernetes. Certifique-se de que não está a definir valores que o seu cluster Kubernetes não será capaz de satisfazer. Isso pode levar a erros ou comportamentos imprevisíveis. Como exemplo, se o estado do seu grupo de servidor permanecer em _atualização_ de estado durante muito tempo após alterar a configuração, pode ser uma indicação de que define os parâmetros abaixo para valores que o seu cluster Kubernetes não pode satisfazer. Se for esse o caso, reverta a mudança ou leia o _troubleshooting_section.

Como exemplo, vamos supor que pretende aumentar a definição do seu grupo de servidor para:

- Min vCore = 2
- Max vCore = 4
- Memória min = 512Mb
- Memória Máxima = 1Gb

Usaria qualquer uma das seguintes abordagens:

### <a name="cli-with-azdata"></a>CLI com azdata

```console
azdata arc postgres server edit -n <name of your server group> --cores-request <# core-request>  --cores-limit <# core-limit>  --memory-request <# memory-request>Mi  --memory-limit <# memory-limit>Mi
```

> [!CAUTION]
> Abaixo está um exemplo fornecido para ilustrar como você poderia usar o comando. Antes de executar um comando de edição, certifique-se de definir os parâmetros para valores que o cluster Kubernetes pode honrar.

```console
azdata arc postgres server edit -n <name of your server group> --cores-request 2  --cores-limit 4  --memory-request 512Mi  --memory-limit 1024Mi
```

O comando executa com sucesso quando mostra:

```console
<name of your server group> is Ready
```

> [!NOTE]
> Para mais detalhes sobre estes parâmetros, `azdata arc postgres server edit --help` corra.

### <a name="cli-with-kubectl"></a>CLI com kubectl

```console
kubectl edit postgresql-12/<server group name> [-n <namespace name>]
```

Isto leva-o ao editor vi onde pode navegar e alterar a configuração. Utilize o seguinte para mapear a definição desejada para o nome do campo na especificação:

> [!CAUTION]
> Abaixo está um exemplo fornecido para ilustrar como poderia editar a configuração. Antes de atualizar a configuração, certifique-se de definir os parâmetros para valores que o cluster Kubernetes pode honrar.

Por exemplo:
- Min vCore = 2 -> agendamento\predefinido\recursos\pedidos\cpu
- Max vCore = 4 -> agendamento\predefinido\recursos\limites\cpu
- Memória min = 512Mb -> agendamento\predefinido\recursos\pedidos\cpu
- Memória Máxima = 1Gb -> agendamento\predefinido\recursos\limites\cpu

Se não estiver familiarizado com o editor vi, consulte uma descrição dos comandos que poderá necessitar [aqui:](https://www.computerhope.com/unix/uvi.htm)
- modo de edição: `i`
- mover-se com setas
- edição _stop: `esc`
- _exit sem poupar: `:qa!`
- _exit depois de salvar: `:qw!`


## <a name="show-the-scaled-up-definition-of-the-server-group"></a>Mostrar a definição de escala do grupo de servidor

Executar novamente o comando para mostrar a definição do grupo de servidor e verificar se está definido como deseja:

### <a name="cli-with-azdata"></a>CLI com azdata

```console
azdata arc postgres server show -n <the name of your server group>
```
### <a name="cli-with-kubectl"></a>CLI com kubectl

```console
kubectl describe postgresql-12/<server group name>  [-n <namespace name>]
```
> [!NOTE]
> Se criou um grupo de servidores da versão 11 do PostgreSQL, execute `kubectl describe postgresql-11/<server group name>` em vez disso.


Mostrará a nova definição do grupo de servidores:

```console
"scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "2",
            "memory": "512Mi"
          }
        }
      }
    },
```

## <a name="scale-down-the-server-group"></a>Reduza o grupo de servidores

Para reduzir o grupo de servidor executa o mesmo comando, mas define valores inferiores para as definições que pretende reduzir. Para remover os pedidos e/ou limites, especifique o seu valor como cadeia vazia.

## <a name="next-steps"></a>Passos seguintes

- [Aumentar horizontalmente o grupo de servidores de Hyperscale da Base de Dados do Azure para PostgreSQL](scale-out-postgresql-hyperscale-server-group.md)
- [Configuração de armazenamento e conceitos de armazenamento de Kubernetes](storage-configuration.md)
- [Modelo de recurso Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
