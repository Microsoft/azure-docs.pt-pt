---
title: Ver registos e métricas usando Kibana e Grafana
description: Ver registos e métricas usando Kibana e Grafana
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 12/08/2020
ms.topic: how-to
ms.openlocfilehash: cb53aba300b933c78d9ac2f5fc5cf8054f3413e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "104670006"
---
# <a name="view-logs-and-metrics-using-kibana-and-grafana"></a>Ver registos e métricas usando Kibana e Grafana

Os dashboards Web do Kibana e do Grafana são disponibilizados para proporcionar informações e clareza quanto aos espaços de nomes do Kubernetes utilizados pelos serviços de dados preparados para Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


## <a name="monitor-azure-sql-managed-instances-on-azure-arc"></a>Monitor Azure SQL geriu instâncias em Azure Arc

Para aceder aos registos e monitores dos dashboards de monitorização do ArcL Geridos Instance, executar o seguinte `azdata` comando CLI

```bash

azdata arc sql endpoint list -n <name of SQL instance>

```
Os painéis de Grafana relevantes são:

* "Azure SQL geriu métricas de instância"
* "Métricas do Nó anfitrião"
* "Métricas de Pods hospedeiros"


> [!NOTE]
>  Quando solicitado para introduzir um nome de utilizador e senha, insira o nome de utilizador e a palavra-passe que forneceu no momento em que criou o controlador de dados Azure Arc.

> [!NOTE]
>  Será solicitado com um aviso de certificado porque os certificados utilizados na pré-visualização são certificados auto-assinados.


## <a name="monitor-azure-database-for-postgresql-hyperscale-on-azure-arc"></a>Monitor Azure Database para a hiperescala pós-SQL em Azure Arc

Para aceder aos registos e monitores dos dashboards de hiperescala PostgreSQL, executar o seguinte `azdata` comando CLI

```bash

azdata arc postgres endpoint list -n <name of postgreSQL instance>

```

Os painéis postgreSQL relevantes são:

* "Métricas postgres"
* "Métricas de Tabela postgres"
* "Métricas do Nó anfitrião"
* "Métricas de Pods hospedeiros"


## <a name="additional-firewall-configuration"></a>Configuração adicional de firewall

Dependendo do local onde o controlador de dados é implantado, poderá descobrir que precisa de abrir portas na sua firewall para aceder aos pontos finais de Kibana e Grafana.

Abaixo está um exemplo de como fazê-lo para um VM Azure. Terá de o fazer se tiver implantado Kubernetes utilizando o script.

Os passos abaixo destacam como criar uma regra NSG para os pontos finais de Kibana e Grafana:

### <a name="find-the-name-of-the-nsg"></a>Localize o nome do NSG

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

### <a name="add-the-nsg-rule"></a>Adicione a regra NSG

Uma vez que tenha o nome do NSG, pode adicionar uma regra usando o seguinte comando:

```azurecli
az network nsg rule create -n ports_30777 --nsg-name azurearcvmNSG --priority 600 -g azurearcvm-rg --access Allow --description 'Allow Kibana and Grafana ports' --destination-address-prefixes '*' --destination-port-ranges 30777 --direction Inbound --protocol Tcp --source-address-prefixes '*' --source-port-ranges '*'
```


## <a name="next-steps"></a>Passos seguintes
- Experimente [carregar métricas e registos para o Azure Monitor](upload-metrics-and-logs-to-azure-monitor.md)
- Leia sobre Grafana:
   - [Introdução](https://grafana.com/docs/grafana/latest/getting-started/getting-started)
   - [Fundamentos de Grafana](https://grafana.com/tutorials/grafana-fundamentals/#1)
   - [Tutoriais de Grafana](https://grafana.com/tutorials/grafana-fundamentals/#1)
- Leia sobre Kibana
   - [Introdução](https://www.elastic.co/webinars/getting-started-kibana?baymax=default&elektra=docs&storm=top-video)
   - [Guia kibana](https://www.elastic.co/guide/en/kibana/current/index.html)
   - [Introdução a brocas de tablier com visualizações de dados em Kibana](https://www.elastic.co/webinars/dashboard-drilldowns-with-data-visualizations-in-kibana/)
   - [Como construir painéis de Kibana](https://www.elastic.co/webinars/how-to-build-kibana-dashboards/)

