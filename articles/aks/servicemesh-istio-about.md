---
title: Visão geral de Istio
description: Obtenha uma visão geral de Istio
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 8518e30a54c2486abf84cd9ac026cc4dccb3fa84
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593905"
---
# <a name="istio"></a>Istio

## <a name="overview"></a>Descrição geral

[Istio][istio] é uma malha de serviço totalmente em destaque, personalizável e extensível.

## <a name="architecture"></a>Arquitetura

Istio fornece um avião de dados que é composto por sidecars baseados no [enviado.][envoy-proxy] Estes proxies inteligentes controlam todo o tráfego de rede dentro e fora das suas aplicações e cargas de trabalho.

O plano de controlo gere a configuração, a política e a telemetria através dos [seguintes componentes:][what-is-istio]

- **Mixer** - Aplica as políticas de controlo de acesso e utilização. Recolhe telemetria dos proxies que são empurrados para [Prometeu.][prometheus]

- **Piloto** - Fornece a política/configuração de gestão de tráfego e deteção de serviços para os proxies.

- **A Cidadela** - Fornece capacidades de identidade e segurança que permitem o mTLS entre serviços.

- **Galé** - Resumos e fornece configuração aos componentes.

O diagrama de arquitetura seguinte demonstra como os vários componentes dentro do plano de dados e plano de controlo interagem.


![Visão geral dos componentes istio e arquitetura.](media/servicemesh/istio/about-architecture.png)


## <a name="selection-criteria"></a>Critérios de seleção

É importante compreender e considerar as seguintes áreas ao avaliar istio para as suas cargas de trabalho:

- [Objetivos de Design](#design-goals)
- [Capacidades](#capabilities)
- [Cenários](#scenarios)


### <a name="design-goals"></a>Objetivos de design

Os seguintes objetivos de design [guiam][design-goals] o projeto Istio:

- **Maximizar transparência** - Permitir a adoção com a quantidade mínima de trabalho para obter valor real do sistema.

- **Extensibility** - Deve ser capaz de crescer e adaptar-se com necessidades em mudança.

- **Portabilidade** - Corra facilmente em diferentes tipos de ambientes - nuvem, no local.

- **Uniformidade política** - Coerência na definição de políticas entre a variedade de recursos.


### <a name="capabilities"></a>Capacidades

Istio fornece o seguinte conjunto de capacidades:

- **Malha** – gateways (multi-cluster), máquinas virtuais (expansão da malha)

- **Gestão de Tráfego** – encaminhamento, divisão, intervalos, disjuntores, repetições, ingressos, saídas

- **Política** – controlo de acesso, limite de tarifas, quota, adaptadores de políticas personalizadas

- **Segurança** – autenticação (jwt), autorização, encriptação (mTLS), CA externa (Cofre HashiCorp)

- **Observabilidade** - métricas douradas, espelho, rastreio, adaptadores personalizados, prometheus, grafana

### <a name="scenarios"></a>Cenários

Istio é bem adequado e sugerido para os seguintes cenários:

- Requerem extebilidade e um conjunto rico de capacidades

- Expansão da malha para incluir cargas de trabalho baseadas em VM

- Malha de serviço multi-cluster

## <a name="next-steps"></a>Passos seguintes

A seguinte documentação descreve como pode instalar o Istio no Serviço Azure Kubernetes (AKS):

> [!div class="nextstepaction"]
> [Instalar istio no Serviço Azure Kubernetes (AKS)][istio-install]

Também pode explorar ainda mais conceitos Istio e modelos de implementação adicionais:

- [Conceitos Istio][what-is-istio]
- [Modelos de implantação istio][deployment-models]

<!-- LINKS - external -->
[istio]: https://istio.io
[what-is-istio]: https://istio.io/docs/concepts/what-is-istio/
[design-goals]: https://istio.io/docs/concepts/what-is-istio/#design-goals
[deployment-models]: https://istio.io/docs/concepts/deployment-models/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[istio-install]: ./servicemesh-istio-install.md
