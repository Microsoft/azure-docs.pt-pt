---
title: Visão geral do Consul
description: Obtenha uma visão geral do Consul
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 7ffbd2386cd0e50cec8976e6bdd6c5778aa014d7
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928433"
---
# <a name="consul"></a>Consul

## <a name="overview"></a>Descrição geral

[Consul][consul] é uma solução de rede de serviço com reconhecimento de vários data center para se conectar e proteger serviços em plataformas de tempo de execução. [Connect][consul-features] é o componente que fornece recursos de malha de serviço.

## <a name="architecture"></a>Arquitetura

O Consul fornece um plano de dados composto de [sidecars][consul-sidecar] baseados em [Envoy][envoy-proxy]por padrão. Consul tem uma arquitetura de proxy conectável. Esses proxies inteligentes controlam todo o tráfego de rede dentro e fora de seus aplicativos e cargas de trabalho em malha.

O plano de controle gerencia a configuração e a política por meio dos seguintes [componentes][consul-architecture]:

- **Servidor** -um agente Consul em execução no modo de servidor que mantém o estado do cluster Consul.

- **Cliente** – um agente Consul em execução no modo de cliente leve. Cada nó de computação deve ter um agente cliente em execução. Essa configuração de agentes de cliente e a política entre as cargas de trabalho e a configuração Consul. 

O diagrama de arquitetura a seguir demonstra como os vários componentes dentro do plano de dados e do plano de controle interagem.

![Visão geral dos componentes e da arquitetura do Consul.](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>Critérios de seleção

É importante entender e considerar as seguintes áreas ao avaliar Consul para suas cargas de trabalho:

- [Princípios de Consul](#consul-principles)
- [Técnicas](#capabilities)
- [Cenários](#scenarios)


### <a name="consul-principles"></a>Princípios de Consul

Os princípios a seguir [guiam][consul-principles] o projeto Consul:

- **Controlado por API** -codificar todas as configurações e políticas.

- **Execute e conecte** -se a cargas de trabalho de conexão em qualquer lugar em plataformas de tempo de execução (kubernetes, VMS, sem servidor).

- **Estender e integrar** – Conecte cargas de trabalho com segurança na infraestrutura.


### <a name="capabilities"></a>Capacidades

O Consul fornece o seguinte conjunto de recursos:

- **Malha** – gateway (vários data center), máquinas virtuais (fora dos nós de cluster), sincronização de serviço, opção de depuração interna

- **Proxies** – Envoy, proxy interno, conectável, proxy L4 disponíveis para cargas de trabalho do Windows

- **Gerenciamento de tráfego** – roteamento, divisão, resolução

- **Política** – intenções, ACLs

- **Segurança** – autorização, autenticação, criptografia, identidades baseadas em SPIFFE, AC externa (cofre), gerenciamento de certificados e rotação

- **Observação** – métricas, painel de interface do usuário, Prometheus, grafana


### <a name="scenarios"></a>Cenários

O Consul é adequado e sugerido para os seguintes cenários:

- Estendendo cargas de trabalho conectadas Consul existentes

- Requisitos de conformidade em relação ao gerenciamento de certificados

- Malha de serviço de vários clusters

- Cargas de trabalho baseadas em VM a serem incluídas na malha de serviço



## <a name="next-steps"></a>Passos seguintes

A documentação a seguir descreve como você pode instalar o Consul no AKS (serviço kubernetes do Azure):

> [!div class="nextstepaction"]
> [Instalar o Consul no serviço kubernetes do Azure (AKS)][consul-install]

Você também pode explorar ainda mais os recursos e a arquitetura do Consul:

- [Recursos do Consul][consul-features]
- [Arquitetura do Consul][consul-architecture]
- [Consul-como o Connect funciona][consul-how-connect-works]

<!-- LINKS - external -->
[consul]: https://www.consul.io/mesh.html
[consul-features]: https://www.consul.io/docs/connect/index.html
[consul-architecture]: https://www.consul.io/docs/internals/architecture.html
[consul-sidecar]: https://www.consul.io/docs/connect/proxies.html
[consul-how-connect-works]: https://www.consul.io/docs/connect/connect-internals.html
[consul-principles]: https://www.consul.io/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[consul-install]: ./servicemesh-consul-install.md
