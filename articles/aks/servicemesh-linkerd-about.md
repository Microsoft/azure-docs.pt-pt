---
title: Visão geral de Linkerd
description: Obtenha uma visão geral de Linkerd
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 3181be62a14ec1b3450bd181172b5323ca176427
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593772"
---
# <a name="linkerd"></a>Linkerd

## <a name="overview"></a>Descrição geral

[Linkerd][linkerd] é uma malha de serviço fácil de usar e leve.

## <a name="architecture"></a>Arquitetura

A Linkerd fornece um plano de dados composto por carros de procuração especializados ultraleves [Linkerd.][linkerd-proxy] Estes proxies inteligentes controlam todo o tráfego de rede dentro e fora das suas aplicações e cargas de trabalho. Os proxies também expõem métricas através de pontos finais das métricas [de Prometeu.][prometheus]

O plano de controlo gere a configuração e a telemetria agregada através dos [seguintes componentes:][linkerd-architecture]

- **Controlador** - Fornece api que conduz o Linkerd CLI e o Dashboard. Fornece configuração para proxies.

- **Toque** - Estabeleça relógios em tempo real sobre pedidos e respostas.

- **Identidade** - Fornece capacidades de identidade e segurança que permitem o mTLS entre serviços.

- **Web** - Fornece o painel linkerd.


O diagrama de arquitetura seguinte demonstra como os vários componentes dentro do plano de dados e plano de controlo interagem.


![Visão geral dos componentes linkerd e arquitetura.](media/servicemesh/linkerd/about-architecture.png)


## <a name="selection-criteria"></a>Critérios de seleção

É importante compreender e considerar as seguintes áreas ao avaliar linkerd para as suas cargas de trabalho:

- [Princípios de Design](#design-principles)
- [Capacidades](#capabilities)
- [Cenários](#scenarios)


### <a name="design-principles"></a>Princípios de conceção

Os seguintes princípios de conceção [orientam][design-principles] o projeto Linkerd:

- **Mantê-lo simples** - Deve ser fácil de usar e entender.

- **Minimizar os Requisitos de Recursos** - Impor o mínimo de desempenho e custo de recursos.

- **Just Work** - Não quebre as aplicações existentes e não exija uma configuração complexa.


### <a name="capabilities"></a>Capacidades

A Linkerd fornece o seguinte conjunto de capacidades:

- **Malha** – construída em opção de depuração

- **Gestão de Tráfego** - divisão, intervalos, repetições, ingressos

- **Segurança** – encriptação (mTLS), certificados rodados automaticamente a cada 24 horas

- **Observabilidade** - métricas douradas, toque, rastreio, perfis de serviço e por métricas de rota, painel web com gráficos de topologia, prometheus, grafana


### <a name="scenarios"></a>Cenários

Linkerd é adequado e sugerido para os seguintes cenários:

- Simples de usar com apenas o conjunto essencial de requisitos de capacidade

- Baixa latência, baixa sobrecarga, com foco na observabilidade e gestão simples do tráfego


## <a name="next-steps"></a>Passos seguintes

A seguinte documentação descreve como pode instalar o Linkerd no Serviço Azure Kubernetes (AKS):

> [!div class="nextstepaction"]
> [Instalar linkerd no Serviço Azure Kubernetes (AKS)][linkerd-install]

Também pode explorar ainda mais as funcionalidades e arquitetura linkerd:

- [Funcionalidades de Linkerd][linkerd-features]
- [Arquitetura Linkerd][linkerd-architecture]

<!-- LINKS - external -->
[linkerd]: https://linkerd.io/2/overview/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-features]: https://linkerd.io/2/features/
[design-principles]: https://linkerd.io/2/design-principles/
[linkerd-proxy]: https://github.com/linkerd/linkerd2-proxy

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[linkerd-install]: ./servicemesh-linkerd-install.md