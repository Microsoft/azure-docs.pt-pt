---
title: Visão geral de Linkerd
description: Obtenha uma visão geral de Linkerd
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 3181be62a14ec1b3450bd181172b5323ca176427
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77593772"
---
# <a name="linkerd"></a>Linkerd

## <a name="overview"></a>Descrição geral

[Linkerd][linkerd] é uma malha de serviço fácil de usar e leve.

## <a name="architecture"></a>Arquitetura

Linkerd fornece um plano de dados composto por carros laterais de procuração especializados em ultraleve [Linkerd.][linkerd-proxy] Estes proxies inteligentes controlam todo o tráfego de rede dentro e fora das suas apps e cargas de trabalho. Os proxies também expõem métricas através de pontos finais de métricas [de Prometeu.][prometheus]

O plano de controlo gere a configuração e a telemetria agregada através dos [seguintes componentes:][linkerd-architecture]

- **Controlador** - Fornece api que conduz o Linkerd CLI e o Dashboard. Fornece configuração para proxies.

- **Tap** - Estabelecer relógios em tempo real em pedidos e respostas.

- **Identidade** - Fornece capacidades de identidade e segurança que permitem mTLS entre serviços.

- **Web** - Fornece o dashboard Linkerd.


O seguinte diagrama de arquitetura demonstra como os vários componentes dentro do plano de dados e do plano de controlo interagem.


![Visão geral dos componentes e arquitetura linkerd.](media/servicemesh/linkerd/about-architecture.png)


## <a name="selection-criteria"></a>Critérios de seleção

É importante compreender e considerar as seguintes áreas ao avaliar o Linkerd para as suas cargas de trabalho:

- [Princípios de Design](#design-principles)
- [Capacidades](#capabilities)
- [Cenários](#scenarios)


### <a name="design-principles"></a>Princípios de conceção

Os seguintes princípios de design [guiam][design-principles] o projeto Linkerd:

- **Mantenha-o simples** - Deve ser fácil de usar e entender.

- **Minimizar os requisitos de recursos** - Impor o mínimo de desempenho e custo de recursos.

- **Just Work** - Não quebre as aplicações existentes e não exija uma configuração complexa.


### <a name="capabilities"></a>Capacidades

Linkerd fornece o seguinte conjunto de capacidades:

- **Malha** – construída em opção de depuragem

- **Gestão de Tráfego** - separação, intervalos, retrações, entrasss

- **Segurança** – encriptação (mTLS), certificados autonotados a cada 24 horas

- **Visibilidade** – métricas douradas, torneira, rastreio, perfis de serviço e por métricas de rota, painel web com gráficos de topologia, prometheus, grafana


### <a name="scenarios"></a>Cenários

Linkerd é adequado e sugerido para os seguintes cenários:

- Simples de usar apenas com o conjunto essencial de requisitos de capacidade

- Baixa latência, sobrecarga baixa, com foco na observabilidade e gestão simples do tráfego


## <a name="next-steps"></a>Passos seguintes

A documentação que se segue descreve como pode instalar o Linkerd no Serviço Azure Kubernetes (AKS):

> [!div class="nextstepaction"]
> [Instalar Linkerd no Serviço Azure Kubernetes (AKS)][linkerd-install]

Você também pode explorar ainda as características e arquitetura linkerd:

- [Funcionalidades linkerd][linkerd-features]
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