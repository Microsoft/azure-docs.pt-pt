---
title: Visão geral do Cônsul
description: Obtenha uma visão geral do Cônsul
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: c518985b360fa3264bd5ac1e3fe76d61b2810b9b
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594214"
---
# <a name="consul"></a>Cônsul

## <a name="overview"></a>Descrição geral

[O Cônsul][consul] é uma solução de rede de serviços consciente multi data center para conectar e proteger serviços através de plataformas de tempo de execução. [O connect][consul-features] é o componente que fornece capacidades de malha de serviço.

## <a name="architecture"></a>Arquitetura

O cônsul fornece um plano de dados que é composto por [sidecars][consul-sidecar] baseados em [enviado][envoy-proxy]por padrão. O cônsul tem uma arquitetura de procuração pluggable. Estes proxies inteligentes controlam todo o tráfego de rede dentro e fora das suas aplicações e cargas de trabalho.

O plano de controlo gere a configuração e a política através dos [seguintes componentes:][consul-architecture]

- **Servidor** - Um agente cônsul em modo Servidor que mantém o estado de cluster do Cônsul.

- **Cliente** - Um Agente Cônsul a funcionar em modo cliente leve. Cada nó computacional deve ter um agente cliente a funcionar. Esta configuração e política de corretores de clientes entre as cargas de trabalho e a configuração do Cônsul. 

O diagrama de arquitetura seguinte demonstra como os vários componentes dentro do plano de dados e plano de controlo interagem.

![Visão geral dos componentes do Cônsul e arquitetura.](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>Critérios de seleção

É importante compreender e considerar as seguintes áreas ao avaliar o Cônsul para as suas cargas de trabalho:

- [Princípios do Cônsul](#consul-principles)
- [Capacidades](#capabilities)
- [Cenários](#scenarios)


### <a name="consul-principles"></a>Princípios do cônsul

Os seguintes princípios [orientam][consul-principles] o projeto cônsul:

- **API-Driven** - Codificar toda a configuração e política.

- **Executar e Ligar qualquer lugar** - Conecte cargas de trabalho através de plataformas de tempo de execução (Kubernetes, VMs, Serverless).

- **Estender e Integrar** - Ligue de forma segura as cargas de trabalho através da infraestrutura.


### <a name="capabilities"></a>Capacidades

O Cônsul fornece o seguinte conjunto de capacidades:

- **Malha** – gateway (centro de dados multi), máquinas virtuais (fora de nós de cluster), sincronização de serviço, construída em opção de depuração

- **Proxies** – Enviado, proxy incorporado, pluggable, l4 proxy disponível para cargas de trabalho do Windows

- **Gestão de Tráfego** - encaminhamento, divisão, resolução

- **Política** – intenções, ACLs

- **Segurança** – autorização, autenticação, encriptação, identidades baseadas em SPIFFE, CA externa (Cofre), gestão de certificados e rotação

- **Observabilidade** - métricas, painel de instrumentos ui, prometheus, grafana


### <a name="scenarios"></a>Cenários

O cônsul é adequado e sugerido para os seguintes cenários:

- Alargar as cargas de trabalho existentes do Cônsul

- Requisitos de conformidade em torno da gestão de certificados

- Malha de serviço multicluster

- Cargas de trabalho baseadas em VM a incluir na malha de serviço



## <a name="next-steps"></a>Passos seguintes

A seguinte documentação descreve como pode instalar o Cônsul no Serviço Azure Kubernetes (AKS):

> [!div class="nextstepaction"]
> [Instalar cônsul no Serviço Azure Kubernetes (AKS)][consul-install]

Você também pode explorar ainda mais as características do Cônsul e arquitetura:

- [Características do cônsul][consul-features]
- [Arquitetura do Cônsul][consul-architecture]
- [Cônsul - Como funciona a Ligação][consul-how-connect-works]

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
