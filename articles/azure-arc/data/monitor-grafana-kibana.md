---
title: Ver registos e métricas usando Kibana e Grafana
description: Ver registos e métricas usando Kibana e Grafana
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3693c30a34601512770f5d9071f5d786410fb00e
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92360382"
---
# <a name="view-logs-and-metrics-using-kibana-and-grafana"></a>Ver registos e métricas usando Kibana e Grafana

Os dashboards Web do Kibana e do Grafana são disponibilizados para proporcionar informações e clareza quanto aos espaços de nomes do Kubernetes utilizados pelos serviços de dados preparados para Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="retrieve-the-ip-address-of-your-cluster"></a>Recupere o endereço IP do seu cluster

Para aceder aos dashboards, terá de recuperar o endereço IP do seu cluster. O método para recuperar o endereço IP correto varia consoante a forma como escolheu implementar Kubernetes. Adiem as opções abaixo para encontrar a certa para si.

### <a name="azure-virtual-machine"></a>Máquina virtual do Azure

Utilize o seguinte comando para obter o endereço IP público:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

### <a name="kubeadm-cluster"></a>Aglomerado de Kubeadm

Para recuperar o endereço IP cluster, utilize o seguinte comando:

```console
kubectl cluster-info
```


### <a name="aks-or-other-load-balanced-cluster"></a>AKS ou outro cluster equilibrado de carga

Para monitorizar o seu ambiente em AKS ou outro cluster equilibrado de carga, precisa de obter o endereço IP do serviço de procuração de gestão. Utilize este comando para recuperar o endereço **ip externo:**

```console
kubectl get svc mgmtproxy-svc-external -n <namespace>

#Example:
#kubectl get svc mgmtproxy-svc-external -n arc
NAME                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)           AGE
mgmtproxy-svc-external   LoadBalancer   10.0.186.28   52.152.148.25   30777:30849/TCP   19h
```

## <a name="additional-firewall-configuration"></a>Configuração adicional de firewall

Pode descobrir que precisa de abrir portas na sua firewall para aceder aos pontos finais de Kibana e Grafana.

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

## <a name="monitor-azure-sql-managed-instances-on-azure-arc"></a>Monitor Azure SQL geriu instâncias em Azure Arc

Agora que tem o endereço IP e expôs as portas, deverá poder aceder a Grafana e Kibana.

> [!NOTE]
>  Quando solicitado para introduzir um nome de utilizador e senha, insira o nome de utilizador e a palavra-passe que forneceu no momento em que criou o controlador de dados Azure Arc.

> [!NOTE]
>  Será solicitado com um aviso de certificado porque os certificados utilizados na pré-visualização são certificados auto-assinados.

Utilize o seguinte padrão de URL para aceder aos dashboards de registo e monitorização para a instância gerida Azure SQL:

```html
https://<external-ip-from-above>:30777/grafana
https://<external-ip-from-above>:30777/kibana
```

Os dashboards relevantes são:

* "Azure SQL geriu métricas de instância"
* "Métricas do Nó anfitrião"
* "Métricas de Pods hospedeiros"

## <a name="monitor-azure-database-for-postgresql-hyperscale---azure-arc"></a>Monitor Azure Database para a hiperescala pós-SQL - Arco Azure

Utilize o seguinte padrão de URL para aceder aos painéis de registo e monitorização da hiperescala postgresql:

```html
https://<external-ip-from-above>:30777/grafana
https://<external-ip-from-above>:30777/kibana
```

Os dashboards relevantes são:

* "Métricas postgres"
* "Métricas de Tabela postgres"
* "Métricas do Nó anfitrião"
* "Métricas de Pods hospedeiros"

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

