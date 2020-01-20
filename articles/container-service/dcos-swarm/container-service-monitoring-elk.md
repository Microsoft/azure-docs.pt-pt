---
title: PRETERIDO Monitorar um cluster de DC/so do Azure-pilha ELK
description: Monitore um cluster DC/OS no cluster do serviço de contêiner do Azure com ELK (Elasticsearch, Logstash e Kibana).
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 3d34ebe22344be8acc6ec3cc974071639293e2b3
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277765"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-elk"></a>PRETERIDO Monitorar um cluster do serviço de contêiner do Azure com o ELK

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Neste artigo, demonstraremos como implantar a pilha ELK (Elasticsearch, Logstash, Kibana) em um cluster DC/OS no serviço de contêiner do Azure. 

## <a name="prerequisites"></a>Pré-requisitos
[Implante](container-service-deployment.md) e [Conecte](../container-service-connect.md) um cluster de DC/so configurado pelo serviço de contêiner do Azure. Explore o painel do DC/so e os serviços marathons [aqui](container-service-mesos-marathon-ui.md). Instale também o [Load Balancer Marathon](container-service-load-balancing.md).


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Elasticsearch, Logstash, Kibana)
ELK Stack é uma combinação de Elasticsearch, Logstash e Kibana que fornece uma pilha de ponta a ponta que pode ser usada para monitorar e analisar logs em seu cluster.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Configurar a pilha ELK em um cluster de DC/so
Acesse sua interface do usuário do DC/OS via [http://localhost:80/](http://localhost:80/) uma vez na interface do usuário do DC/so, navegue até **universo**. Pesquise e instale o Elasticsearch, o Logstash e o Kibana do universo DC/OS e nessa ordem específica. Você pode saber mais sobre a configuração se for para o link de **instalação avançada** .

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

Depois que os contêineres ELK e estiverem em execução, você precisará habilitar o Kibana a ser acessado por meio do Marathon-LB. Navegue até **serviços** > **kibana**e clique em **Editar** , conforme mostrado abaixo.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


Alterne para o **modo JSON** e role para baixo até a seção rótulos.
Você precisa adicionar uma entrada de `"HAPROXY_GROUP": "external"` aqui, conforme mostrado abaixo.
Depois de clicar em **implantar alterações**, seu contêiner será reiniciado.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Se você quiser verificar se o Kibana está registrado como um serviço no painel do HAPROXY, você precisa abrir a porta 9090 no cluster do agente, pois o HAPROXY é executado na porta 9090.
Por padrão, abrimos as portas 80, 8080 e 443 no cluster do agente DC/so.
As instruções para abrir uma porta e fornecer a avaliação pública são fornecidas [aqui](container-service-enable-public-access.md).

Para acessar o painel do HAPROXY, abra a interface de administração Marathon-LB em: `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`.
Depois de navegar até a URL, você deverá ver o painel do HAPROXY, conforme mostrado abaixo, e verá uma entrada de serviço para Kibana.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


Para acessar o painel do Kibana, que é implantado na porta 5601, você precisa abrir a porta 5601. Siga as instruções [aqui](container-service-enable-public-access.md). Em seguida, abra o painel do Kibana em: `http://localhost:5601`.

## <a name="next-steps"></a>Passos seguintes

* Para o encaminhamento e a configuração de log do sistema e do aplicativo, consulte [Gerenciamento de log no DC/os com Elk](https://docs.mesosphere.com/1.8/administration/logging/elk/).

* Para filtrar logs, consulte [Filtrar logs com Elk](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/). 

 

