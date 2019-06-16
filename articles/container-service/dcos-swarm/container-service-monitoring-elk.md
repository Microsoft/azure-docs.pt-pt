---
title: (PRETERIDO) Monitorizar um cluster DC/OS do Azure - pilha ELK
description: Monitorize um cluster do DC/OS no cluster do Azure Container Service com o ELK (Elasticsearch, Logstash e Kibana).
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 342cf23db2df7d7c79a2b56df96d1a78d6ba215e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61467773"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-elk"></a>(PRETERIDO) Monitorizar um cluster do Azure Container Service com o ELK

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Neste artigo, vamos demonstrar como implementar a pilha ELK (Elasticsearch, Logstash, Kibana) num cluster DC/OS no Azure Container Service. 

## <a name="prerequisites"></a>Pré-requisitos
[Implementar](container-service-deployment.md) e [ligar](../container-service-connect.md) um cluster de DC/OS configurado pelo Azure Container Service. Explore o dashboard de DC/OS e os serviços do Marathon [aqui](container-service-mesos-marathon-ui.md). Também instalar o [Balanceador de carga Marathon](container-service-load-balancing.md).


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Elasticsearch, Logstash, Kibana)
Pilha ELK é uma combinação de Elasticsearch, Logstash e Kibana que fornece uma pilha de ponto a ponto que pode ser usada para monitorar e analisar registos do cluster.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Configurar a pilha ELK num cluster DC/OS
Aceder à IU do DC/OS através de [ http://localhost:80/ ](http://localhost:80/) uma vez na IU do DC/OS, navegue até **universo**. Procure e instale o Elasticsearch, Logstash e Kibana do DC/OS Universe e por essa ordem específica. Pode saber mais sobre a configuração, se for para o **Advanced instalação** ligação.

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

Uma vez os contentores ELK e estão em funcionamento, tem de ativar o Kibana ser acedido através do Marathon-LB. Navegue para **serviços** > **kibana**e clique em **editar** conforme mostrado abaixo.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


Alternar para **modo JSON** e desloque-se para baixo para a secção de etiquetas.
Precisa adicionar um `"HAPROXY_GROUP": "external"` entrada aqui conforme mostrado abaixo.
Assim que clicar em **implementar alterações**, os reinícios de contentor.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Se quiser Certifique-se de que o Kibana está registado como um serviço no dashboard do HAPROXY, terá de abrir a porta 9090 no cluster de agente, como HAPROXY é executado na porta 9090.
Por predefinição, podemos abrir as portas 80, 8080 e 443 do cluster do agente DC/OS.
São fornecidas instruções para abrir uma porta e fornecer público avaliar [aqui](container-service-enable-public-access.md).

Para aceder ao dashboard do HAPROXY, abra a interface de administração do Marathon-LB em: `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`.
Depois de navegar para o URL, deverá ver o dashboard HAPROXY conforme mostrado abaixo e deverá ver uma entrada de serviço do Kibana.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


Para aceder ao dashboard do Kibana, que é implementado na porta 5601, terá de abrir a porta 5601. Siga as instruções [aqui](container-service-enable-public-access.md). Em seguida, abra o dashboard do Kibana em: `http://localhost:5601`.

## <a name="next-steps"></a>Passos Seguintes

* Para o registo de sistema e aplicação de encaminhamento e a configuração, consulte [gestão de registos no DC/OS com o ELK](https://docs.mesosphere.com/1.8/administration/logging/elk/).

* Filtrar registos, consulte [registos de filtragem de mensagens em fila com o ELK](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/). 

 

