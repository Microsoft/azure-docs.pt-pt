---
title: Obtenha registos para resolução de problemas Azure Arc controlador de dados habilitado
description: Obtenha registos de serviço para resolver problemas O controlador de dados ativado pelo Azure Arc.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 71c84b35c001be7fafdc2df53014050ae21dec63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939117"
---
# <a name="get-azure-arc-enabled-data-services-logs"></a>Obtenha registos de serviços de dados habilitados para a Azure Arc

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para recuperar os registos de serviços de dados ativados do Arco Azure, necessitará da ferramenta Azure Data CLI. [Instruções de instalação](./install-client-tools.md)

Terá de ser capaz de iniciar sessão no serviço de tratamento de serviços de dados ativado pelo Azure Arc como administrador.

## <a name="get-azure-arc-enabled-data-services-logs"></a>Obtenha registos de serviços de dados habilitados para a Azure Arc

Pode obter os registos de serviços de dados ativados pelo Azure Arc em todas as cápsulas ou cápsulas específicas para efeitos de resolução de problemas.  Pode fazê-lo utilizando ferramentas padrão de Kubernetes, como o `kubectl logs` comando ou neste artigo, estará a utilizar a ferramenta CLI de dados Azure, o que facilita a obtê-lo de uma só vez.

Primeiro, certifique-se de que está registado no controlador de dados.

```console
azdata login
```

Em seguida, executar o seguinte comando para despejar os registos:
```console
azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress

#Example:
#azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
```

Os ficheiros de registo serão criados no diretório de trabalho atual por padrão numa subdirectória chamada 'logs'.  Pode desausar os ficheiros de registo para um diretório diferente utilizando o `--target-folder` parâmetro.

Pode optar por comprimir os ficheiros omitindo o `--skip-compress` parâmetro.

Pode ativar e incluir despejos de memória omitindo o `--exclude-dumps` , mas isso não é recomendado a menos que o Microsoft Support tenha solicitado o despejo de memória.  A tomada de uma lixeira de memória requer que a definição do controlador de dados `allowDumps` seja definida para o tempo da `true` criação do controlador de dados.

Pode optar opcionalmente por filtrar para recolher registos apenas para uma vagem específica `--pod` () ou recipiente `--container` () pelo nome.

Também pode optar por filtrar para recolher registos para um recurso personalizado específico, passando o `--resource-kind` e `--resource-name` paramater.  O `resource-kind` valor do parâmetro deve ser um dos nomes de definição de recursos personalizados que podem ser recuperados pelo comando `kubectl get customresourcedefinition` .

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>

#Example
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

Exemplo da hierarquia da pasta.  Note que a hierarquia da pasta é organizada pelo nome do pod e, em seguida, por contentor e, em seguida, pela hierarquia do diretório dentro do recipiente.

```console
<export directory>
├───debuglogs-arc-20200827-180403
│   ├───bootstrapper-vl8j2
│   │   └───bootstrapper
│   │       ├───apt
│   │       └───fsck
│   ├───control-j2dm5
│   │   ├───controller
│   │   │   └───controller
│   │   │       ├───2020-08-27
│   │   │       └───2020-08-28
│   │   └───fluentbit
│   │       ├───agent
│   │       ├───fluentbit
│   │       └───supervisor
│   │           └───log
│   ├───controldb-0
│   │   ├───fluentbit
│   │   │   ├───agent
│   │   │   ├───fluentbit
│   │   │   └───supervisor
│   │   │       └───log
│   │   └───mssql-server
│   │       ├───agent
│   │       ├───mssql
│   │       ├───mssql-server
│   │       └───supervisor
│   │           └───log
│   ├───controlwd-ln6j8
│   │   └───controlwatchdog
│   │       └───controlwatchdog
│   ├───logsdb-0
│   │   └───elasticsearch
│   │       ├───agent
│   │       ├───elasticsearch
│   │       ├───provisioner
│   │       └───supervisor
│   │           └───log
│   ├───logsui-7gg2d
│   │   └───kibana
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───kibana
│   │       └───supervisor
│   │           └───log
│   ├───metricsdb-0
│   │   └───influxdb
│   │       ├───agent
│   │       ├───influxdb
│   │       └───supervisor
│   │           └───log
│   ├───metricsdc-2f62t
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-jznd2
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-n5vnx
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsui-h748h
│   │   └───grafana
│   │       ├───agent
│   │       ├───grafana
│   │       └───supervisor
│   │           └───log
│   └───mgmtproxy-r5zxs
│       ├───fluentbit
│       │   ├───agent
│       │   ├───fluentbit
│       │   └───supervisor
│       │       └───log
│       └───service-proxy
│           ├───agent
│           ├───nginx
│           └───supervisor
│               └───log
└───debuglogs-kube-system-20200827-180431
    ├───coredns-8bbb65c89-kklt7
    │   └───coredns
    ├───coredns-8bbb65c89-z2vvr
    │   └───coredns
    ├───coredns-autoscaler-5585bf8c9f-g52nt
    │   └───autoscaler
    ├───kube-proxy-5c9s2
    │   └───kube-proxy
    ├───kube-proxy-h6x56
    │   └───kube-proxy
    ├───kube-proxy-nd2b7
    │   └───kube-proxy
    ├───metrics-server-5f54b8994-vpm5r
    │   └───metrics-server
    └───tunnelfront-db87f4cd8-5xwxv
        ├───tunnel-front
        │   ├───apt
        │   └───journal
        └───tunnel-probe
            ├───apt
            ├───journal
            └───openvpn
```