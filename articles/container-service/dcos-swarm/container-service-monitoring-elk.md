---
title: (DEPRECIADO) Monitorize um cluster Azure DC/OS - Stack ELK
description: Monitorize um cluster DC/OS no cluster do Serviço de Contentores Azure com ELK (Elasticsearch, Logstash e Kibana).
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 586b8d25a9f391487640e9b1f8adb3be0e4be6db
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "82166177"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-elk"></a>(DEPRECIADO) Monitorize um cluster de serviço de contentores Azure com ELK

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Neste artigo, demonstramos como implantar a pilha ELK (Elasticsearch, Logstash, Kibana) num cluster DC/OS no Serviço de Contentores Azure. 

## <a name="prerequisites"></a>Pré-requisitos
[Implementar](container-service-deployment.md) e [ligar](../container-service-connect.md) um cluster DC/OS configurado pelo Serviço de Contentores Azure. Explore [aqui](container-service-mesos-marathon-ui.md)os serviços de tablier DC/OS e Marathon . Instale também o [Balancer de Carga da Maratona.](container-service-load-balancing.md)


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Pesquisa Elástica, Logstash, Kibana)
A pilha elk é uma combinação de Elasticsearch, Logstash e Kibana que fornece uma pilha de fim a ponta que pode ser usada para monitorizar e analisar registos no seu cluster.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Configure a pilha ELK num cluster DC/OS
Aceda ao seu DC/OS UI via `http://localhost:80/` Once in the DC/OS UI navigate to **Universe**. Procure e instale Elasticsearch, Logstash e Kibana do Universo DC/OS e nessa ordem específica. Pode saber mais sobre a configuração se for ao link **De Instalação Avançada.**

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

Uma vez que os recipientes ELK e estejam em funcionamento, você precisa permitir que Kibana seja acedido através da Marathon-LB. Navegue para **os Serviços** > **kibana,** clique em **Editar** como mostrado abaixo.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


Alterne para o **modo JSON** e desloque-se para a secção de etiquetas.
Você precisa adicionar `"HAPROXY_GROUP": "external"` uma entrada aqui como mostrado abaixo.
Assim que clicar em alterar as **alterações,** o seu recipiente reinicia.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Se quiser verificar se a Kibana está registada como um serviço no painel HAPROXY, precisa de abrir a porta 9090 no cluster do agente, uma vez que o HAPROXY funciona na porta 9090.
Por padrão, abrimos as portas 80, 8080 e 443 no cluster de agentes DC/OS.
As instruções para abrir um porto e fornecer avaliação pública são fornecidas [aqui.](container-service-enable-public-access.md)

Para aceder ao painel HAPROXY, abra a interface `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`de administração Marathon-LB em: .
Uma vez navegado para o URL, deve ver o painel HAPROXY como mostrado abaixo e deve ver uma entrada de serviço para Kibana.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


Para aceder ao tablier Kibana, que está implantado no porto 5601, é necessário abrir o porto 5601. Siga as instruções [aqui](container-service-enable-public-access.md). Em seguida, abra o `http://localhost:5601`painel kibana em: .

## <a name="next-steps"></a>Passos seguintes

* Para o encaminhamento e configuração de log de sistema e aplicação, consulte [A Gestão de Registos em DC/OS com ELK](https://docs.mesosphere.com/1.8/administration/logging/elk/).

* Para filtrar os registos, consulte [registos de filtragem com ELK](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/). 

 

