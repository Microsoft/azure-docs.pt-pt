---
title: (PRECADO) Monitorize um cluster Azure DC/OS - pilha de ELK
description: Monitorize um cluster DC/OS no cluster de serviço de contentores Azure com ELK (Elasticsearch, Logstash e Kibana).
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 586b8d25a9f391487640e9b1f8adb3be0e4be6db
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82166177"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-elk"></a>(PRECADO) Monitorize um cluster de serviço de contentores Azure com ELK

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Neste artigo, demonstramos como implantar a pilha ELK (Elasticsearch, Logstash, Kibana) num cluster DC/OS no Serviço de Contentores Azure. 

## <a name="prerequisites"></a>Pré-requisitos
[Implementar](container-service-deployment.md) e [ligar](../container-service-connect.md) um cluster DC/OS configurado pelo Serviço de Contentores Azure. Explore [aqui](container-service-mesos-marathon-ui.md)os serviços de tablier DC/OS e Maratona. Instale também o [Equilibrador de Carga da Maratona.](container-service-load-balancing.md)


## <a name="elk-elasticsearch-logstash-kibana"></a>Alce (Elasticsearch, Logstash, Kibana)
A pilha de ELK é uma combinação de Elasticsearch, Logstash e Kibana que fornece uma pilha de ponta a ponta que pode ser usada para monitorizar e analisar troncos no seu cluster.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Configure a pilha de ALCE num cluster DC/OS
Aceda ao seu UI DC/OS via `http://localhost:80/` Uma vez na UI DC/OS navegar para o **Universo.** Pesquise e instale Elasticsearch, Logstash e Kibana do Universo DC/OS e nessa ordem específica. Pode saber mais sobre a configuração se for ao link **de Instalação Avançada.**

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ALLC2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

Uma vez que os contentores ELK estão a funcionar, você precisa permitir que Kibana seja acedido através da Maratona-LB. Navegue para **serviços**  >  **kibana**, e clique em **Editar** como mostrado abaixo.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


Alterne para o **modo JSON** e desloque-se até à secção de etiquetas.
Tem de adicionar uma `"HAPROXY_GROUP": "external"` entrada aqui, como mostrado abaixo.
Assim que clicar **Em Implementar alterações,** o seu recipiente reinicia.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Se quiser verificar se o Kibana está registado como um serviço no painel HAPROXY, tem de abrir a porta 9090 no cluster do agente, uma vez que o HAPROXY funciona no porto 9090.
Por padrão, abrimos as portas 80, 8080 e 443 no cluster de agentes DC/OS.
As instruções para abrir um porto e fornecer avaliação pública são fornecidas [aqui](container-service-enable-public-access.md).

Para aceder ao painel HAPROXY, abra a interface de administração Marathon-LB em: `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats` .
Uma vez navegado para o URL, deverá ver o painel HAPROXY como mostrado abaixo e deverá ver uma entrada de serviço para Kibana.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


Para aceder ao tablier Kibana, que está implantado no porto 5601, é necessário abrir o porto 5601. Siga as instruções [aqui.](container-service-enable-public-access.md) Em seguida, abra o tablier Kibana em: `http://localhost:5601` .

## <a name="next-steps"></a>Passos seguintes

* Para encaminhamento e configuração de registo de sistemas e aplicações, consulte [Log Management em DC/OS com ELK](https://docs.mesosphere.com/1.8/administration/logging/elk/).

* Para filtrar registos, consulte [os registos de filtragem com ELK](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/). 

 

