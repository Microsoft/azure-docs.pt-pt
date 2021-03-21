---
title: Obtenha registos para resolver problemas Serviços de dados do Azure Arc
description: Saiba como obter ficheiros de registo de um controlador de dados para resolver problemas A azure Arc permitiu serviços de dados.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0c4cff7583f08fe27649cee464fcef802cddd88f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93234053"
---
# <a name="get-logs-to-troubleshoot-azure-arc-enabled-data-services"></a>Obtenha registos para resolver problemas Serviços de dados do Azure Arc

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de prosseguir, precisa:

* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]. Para obter mais informações, consulte [instalar ferramentas de cliente para implantar e gerir os serviços de dados do Azure Arc.](./install-client-tools.md)
* Uma conta de administrador para iniciar seduca no Azure Arc permitiu o tratamento de dados.

## <a name="get-log-files"></a>Obtenha ficheiros de registo

Pode obter registos de serviço em todas as cápsulas ou cápsulas específicas para efeitos de resolução de problemas. Uma maneira é usar ferramentas padrão de Kubernetes, como o `kubectl logs` comando. Neste artigo, utilizará a ferramenta, o [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] que facilita a obtê-la de uma só vez.

1. Inscreva-se no controlador de dados com uma conta de administrador.

   ```console
   azdata login
   ```

2. Executar o seguinte comando para despejar os registos:

   ```console
   azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress
   ```

   Por exemplo:

   ```console
   #azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
   ```

O controlador de dados cria os ficheiros de registo no diretório de trabalho atual numa subdiretório chamada `logs` . 

## <a name="options"></a>Opções

O `azdata arc dc debug copy-logs` comando fornece as seguintes opções para gerir a saída:

* Desada os ficheiros de registo para um diretório diferente utilizando o `--target-folder` parâmetro.
* Comprima os ficheiros omitindo o `--skip-compress` parâmetro.
* Despoletem e inclua despejos de memória omitindo `--exclude-dumps` . Não recomendamos este método a menos que o Microsoft Support tenha solicitado os despejos de memória. Obter uma lixeira de memória requer que a definição do controlador de dados `allowDumps` seja definida para quando o controlador de `true` dados é criado.
* Filtrar para recolher registos apenas para uma vagem específica `--pod` () ou recipiente `--container` () pelo nome.
* Filtrar para recolher registos para um recurso personalizado específico, passando os `--resource-kind` parâmetros e `--resource-name` parâmetros. O `resource-kind` valor do parâmetro deve ser um dos nomes de definição de recursos personalizados. Pode recuperar esses nomes utilizando o `kubectl get customresourcedefinition` comando.

Com estes parâmetros, pode substituir o `<parameters>` seguinte exemplo: 

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>
```

Por exemplo:

```console
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

A seguinte hierarquia de pastas é um exemplo. É organizado pelo nome da cápsula, depois pelo contentor, e depois pela hierarquia do diretório dentro do contentor.

```output
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

## <a name="next-steps"></a>Passos seguintes

[azdata arc dc depurg copy-logs](/sql/azdata/reference/reference-azdata-arc-dc-debug#azdata-arc-dc-debug-copy-logs?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json)
