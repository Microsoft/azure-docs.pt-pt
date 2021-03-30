---
title: Visão geral de Istio
description: Obtenha uma visão geral de Istio
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 8518e30a54c2486abf84cd9ac026cc4dccb3fa84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "77593905"
---
# <a name="istio"></a>Istio

## <a name="overview"></a>Descrição Geral

[Istio][istio] é uma malha de serviço completa, personalizável e extensível.

## <a name="architecture"></a>Arquitetura

A Istio fornece um plano de dados composto por sidecars baseados em [enviados.][envoy-proxy] Estes proxies inteligentes controlam todo o tráfego de rede dentro e fora das suas apps e cargas de trabalho.

O plano de controlo gere a configuração, a política e a telemetria através dos [seguintes componentes:][what-is-istio]

- **Mixer** - Aplica as políticas de controlo de acesso e utilização. Recolhe telemetria dos proxies que são empurrados para [Prometeu.][prometheus]

- **Pilot** - Fornece a política/configuração de descoberta de serviços e gestão de tráfego para os proxies.

- **Cidadela** - Fornece capacidades de identidade e segurança que permitem mTLS entre serviços.

- **Galé** - Resumos e fornece configuração para componentes.

O seguinte diagrama de arquitetura demonstra como os vários componentes dentro do plano de dados e do plano de controlo interagem.


![Visão geral dos componentes e arquitetura istio.](media/servicemesh/istio/about-architecture.png)


## <a name="selection-criteria"></a>Critérios de seleção

É importante compreender e considerar as seguintes áreas ao avaliar a Istio para as suas cargas de trabalho:

- [Objetivos de Design](#design-goals)
- [Capacidades](#capabilities)
- [Cenários](#scenarios)


### <a name="design-goals"></a>Objetivos de design

Os seguintes objetivos de design [guiam][design-goals] o projeto Istio:

- **Maximizar a Transparência** - Permitir a adoção com a quantidade mínima de trabalho para obter valor real do sistema.

- **Extensibilidade** - Deve ser capaz de crescer e adaptar-se com necessidades em mudança.

- **Portabilidade** - Corra facilmente em diferentes tipos de ambientes - nuvem, no local.

- **Uniformidade política** - Coerência na definição de políticas entre variedades de recursos.


### <a name="capabilities"></a>Capacidades

A Istio fornece o seguinte conjunto de capacidades:

- **Malha** – gateways (multi-cluster), máquinas virtuais (expansão da malha)

- **Gestão de Tráfego** - encaminhamento, divisão, intervalos, disjuntores, retrações, entrasss, saídas

- **Política** – controlo de acesso, limite de tarifas, quota, adaptadores de políticas personalizados

- **Segurança** – autenticação (jwt), autorização, encriptação (mTLS), CA externa (HashiCorp Vault)

- **Visibilidade** – métricas douradas, espelho, rastreio, adaptadores personalizados, prometheus, grafana

### <a name="scenarios"></a>Cenários

Istio é bem adaptado e sugerido para os seguintes cenários:

- Requer extensibilidade e rico conjunto de capacidades

- Expansão da malha para incluir cargas de trabalho baseadas em VM

- Malha de serviço multi-cluster

## <a name="next-steps"></a>Passos seguintes

A documentação que se segue descreve como pode instalar o Istio no Serviço Azure Kubernetes (AKS):

> [!div class="nextstepaction"]
> [Instalar istio no serviço Azure Kubernetes (AKS)][istio-install]

Você também pode explorar ainda mais conceitos istio e modelos de implementação adicionais:

- [Conceitos Istio][what-is-istio]
- [Modelos de implantação de Istio][deployment-models]

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
