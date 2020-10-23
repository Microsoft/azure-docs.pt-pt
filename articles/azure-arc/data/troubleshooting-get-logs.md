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
ms.openlocfilehash: 625092e0557d40051e1ffd538a496c20edc0222f
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320210"
---
# <a name="get-azure-arc-enabled-data-services-logs"></a>Obtenha registos de serviços de dados habilitados para a Azure Arc

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de prosseguir, precisa:

* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]. [Instruções de instalação](./install-client-tools.md).
* Uma conta de administrador para iniciar seduca no Azure Arc permitiu o tratamento de serviços de dados.

## <a name="get-azure-arc-enabled-data-services-logs"></a>Obtenha registos de serviços de dados habilitados para a Azure Arc

Pode obter os registos de serviços de dados ativados pelo Azure Arc em todas as cápsulas ou cápsulas específicas para efeitos de resolução de problemas. Pode fazê-lo utilizando ferramentas padrão de Kubernetes, como o `kubectl logs` comando ou neste artigo, que irá utilizar a ferramenta, o [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] que facilita a obtê-lo de uma só vez.

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

`azdata arc dc debug copy-logs` fornece as seguintes opções para gerir a saída.

* Desada os ficheiros de registo para um diretório diferente utilizando o `--target-folder` parâmetro.
* Comprima os ficheiros omitindo o `--skip-compress` parâmetro.
* Despoletem e incluam despejos de memória omitindo o `--exclude-dumps` . Este método não é recomendado a menos que o Microsoft Support tenha solicitado o despejo de memória. A tomada de uma lixeira de memória requer que a definição do controlador de dados `allowDumps` seja definida para o tempo da `true` criação do controlador de dados.
* Filtrar para recolher registos apenas para uma vagem específica `--pod` () ou recipiente `--container` () pelo nome.
* Filtrar para recolher registos para um recurso personalizado específico, passando o `--resource-kind` parâmetro e `--resource-name` parâmetro. O `resource-kind` valor do parâmetro deve ser um dos nomes de definição de recursos personalizados, que podem ser recuperados pelo comando `kubectl get customresourcedefinition` .

Com estes parâmetros, pode substituir o `<parameters>` exemplo seguinte. 

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>
```

Por exemplo

```console
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

Exemplo da hierarquia da pasta. A hierarquia da pasta é organizada pelo nome do casulo, depois pelo contentor e, em seguida, pela hierarquia do diretório dentro do contentor.

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