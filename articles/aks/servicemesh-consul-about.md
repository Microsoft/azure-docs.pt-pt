---
title: Visão geral do Cônsul
description: Obtenha uma visão geral do Cônsul
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 77cb6a693c5a73d0498b0acf9bc4ad8cc9f8f32f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "83773995"
---
# <a name="consul"></a>Cônsul

## <a name="overview"></a>Descrição Geral

[O Cônsul][consul] é uma solução de rede de serviços de vários centros de dados para conectar e proteger serviços através de plataformas de tempo de funcionamento. [O Connect][consul-features] é o componente que fornece capacidades de malha de serviço.

## <a name="architecture"></a>Arquitetura

O Cônsul fornece um plano de dados composto por [padrão][envoy-proxy]de [sidecars baseados em enviados.][consul-sidecar] Cônsul tem uma arquitetura de procuração pluggável. Estes proxies inteligentes controlam todo o tráfego de rede dentro e fora das suas apps e cargas de trabalho.

O plano de controlo gere a configuração e a política através dos [seguintes componentes:][consul-architecture]

- **Servidor** - Um Agente Cônsul em execução no modo Servidor que mantém o estado do cluster do Cônsul.

- **Cliente** - Um Agente Cônsul em execução em modo cliente leve. Cada nó de cálculo deve ter um agente cliente a funcionar. Esta configuração e política de corretores de clientes entre as cargas de trabalho e a configuração do Cônsul. 

O seguinte diagrama de arquitetura demonstra como os vários componentes dentro do plano de dados e do plano de controlo interagem.

![Visão geral dos componentes do Cônsul e arquitetura.](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>Critérios de seleção

É importante compreender e considerar as seguintes áreas ao avaliar o Cônsul para as suas cargas de trabalho:

- [Princípios do Cônsul](#consul-principles)
- [Capacidades](#capabilities)
- [Cenários](#scenarios)


### <a name="consul-principles"></a>Princípios do Cônsul

Os seguintes princípios [orientam][consul-principles] o projeto Cônsul:

- **API-Driven** - Codificar todas as configurações e políticas.

- **Executar e Ligar Qualquer Lugar** - Conecte cargas de trabalho através de plataformas de tempo de execução (Kubernetes, VMs, Serverless).

- **Estender e Integrar** - Ligar de forma segura cargas de trabalho em toda a infraestrutura.


### <a name="capabilities"></a>Capacidades

O Cônsul fornece o seguinte conjunto de capacidades:

- **Malha** – gateway (centro de dados multi), máquinas virtuais (fora dos nóns de cluster), sincronização de serviço, construída em opção de depuração

- **Proxies** – Enviado, proxy incorporado, pluggable, proxy l4 disponível para cargas de trabalho do Windows

- **Gestão de Tráfego** - encaminhamento, divisão, resolução

- **Política** – intenções, ACLs

- **Segurança** – autorização, autenticação, encriptação, identidades baseadas em SPIFFE, CA externa (Cofre), gestão de certificados e rotação

- **Visibilidade** – métricas, painel ui, prometeu, grafana


### <a name="scenarios"></a>Cenários

O cônsul é adequado e sugerido para os seguintes cenários:

- Alargamento das cargas de trabalho conexas do Cônsul existentes

- Requisitos de conformidade em torno da gestão de certificados

- Malha de serviço de vários clusters

- Cargas de trabalho baseadas em VM a incluir na malha de serviço



## <a name="next-steps"></a>Passos seguintes

A documentação que se segue descreve como pode instalar o Cônsul no Serviço Azure Kubernetes (AKS):

> [!div class="nextstepaction"]
> [Instalar cônsul no Serviço Azure Kubernetes (AKS)][consul-install]

Você também pode explorar ainda mais as características do Cônsul e arquitetura:

- [Cônsul começando tutoriais][consul-getting-started]
- [Características do Cônsul][consul-features]
- [Arquitetura Cônsul][consul-architecture]
- [Cônsul - Como Se Conecta][consul-how-connect-works]

<!-- LINKS - external -->
[consul]: https://www.consul.io/mesh.html
[consul-features]: https://www.consul.io/docs/connect/index.html
[consul-architecture]: https://www.consul.io/docs/internals/architecture.html
[consul-sidecar]: https://www.consul.io/docs/connect/proxies.html
[consul-how-connect-works]: https://www.consul.io/docs/connect/connect-internals.html
[consul-principles]: https://www.consul.io/
[consul-getting-started]:https://learn.hashicorp.com/consul?track=gs-consul-service-mesh#gs-consul-service-mesh

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[consul-install]: ./servicemesh-consul-install.md
