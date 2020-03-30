---
title: Sobre malshes de serviço
description: Obtenha uma visão geral das malshes de serviço, da sua arquitetura e capacidades, e que critérios deve considerar ao selecionar um para implementar.
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: eca49a3fac1ea0398ebe1d05bde20fbca3c81232
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594316"
---
# <a name="about-service-meshes"></a>Sobre malshes de serviço

Uma malha de serviço fornece capacidades como gestão de tráfego, resiliência, política, segurança, identidade forte e observabilidade às suas cargas de trabalho. A sua aplicação é dissociada destas capacidades operacionais e a malha de serviço move-as para fora da camada de aplicação, e para baixo para a camada de infraestrutura.

## <a name="scenarios"></a>Cenários

Estes são alguns dos cenários que podem ser ativados para as suas cargas de trabalho quando utiliza uma malha de serviço:

- **Criptografe todo o tráfego em cluster** - Ative TLS mútuo entre serviços especificados no cluster. Isto pode ser estendido à entrada e à saída no perímetro da rede. Fornece uma opção segura por padrão sem necessidade de alterações para código de aplicação e infraestrutura.

- **Saídas canárias e faseadas** - Especifique as condições para que um subconjunto de tráfego seja encaminhado para um conjunto de novos serviços no cluster. No teste bem sucedido da libertação de canários, remova o encaminhamento condicional e a fase aumente gradualmente % de todo o tráfego para um novo serviço. Eventualmente todo o tráfego será direcionado para um novo serviço.

- **Gestão e manipulação** de tráfego - Crie uma política sobre um serviço que limite todo o tráfego a uma versão de um serviço de origem específica. Ou uma política que aplica uma estratégia de retenção às classes de falhas entre serviços especificados. Mirror tráfego ao vivo para novas versões de serviços durante uma migração ou para depurar problemas. Injetar falhas entre serviços num ambiente de teste para testar a resiliência.

- **Observabilidade** - Obtenha informações sobre como os seus serviços estão conectados o tráfego que flui entre eles. Obtenha métricas, registos e vestígios para todo o tráfego em cluster, e ingress/egress. Adicione habilidades de rastreio distribuídas às suas aplicações.

## <a name="architecture"></a>Arquitetura

Uma malha de serviço é tipicamente composta por um plano de controlo e o plano de dados.

O plano de **controlo** tem uma série de componentes que suportam a gestão da malha de serviço. Isto incluirá normalmente uma interface de gestão que pode ser uma UI ou uma API. Normalmente, haverá componentes que gerem a regra e definições políticas que definem como a malha de serviço deve implementar capacidades específicas. Existem também componentes que gerem aspetos de segurança como identidade forte e certificados para mTLS. As malsheas de serviço também terão normalmente uma componente métrica ou observabilidade que recolhe e agrega métricas e telemetria das cargas de trabalho.

O plano de **dados** consiste tipicamente de um proxy que é injetado de forma transparente como um sidecar para as suas cargas de trabalho. Este proxy está configurado para controlar todo o tráfego de rede dentro e fora da cápsula que contém a sua carga de trabalho. Isto permite que o representante seja configurado para proteger o tráfego através do mTLS, tráfego de rotas dinâmicas, aplicar políticas ao tráfego e recolher métricas e rastrear informações. 

![Arquitetura típica de malha de serviço](media/servicemesh/typical-architecture.png)

## <a name="capabilities"></a>Capacidades

Cada uma das malshes de serviço tem um ajuste natural e foco em apoiar cenários específicos, mas você normalmente vai descobrir que a maioria implementará uma série de, se não todas, das seguintes capacidades.

### <a name="traffic-management"></a>Gestão de tráfego 

- **Protocolo** – camada 7 (http, grpc)
- **Encaminhamento Dinâmico** - condicional, ponderação, espelhamento
- **Resiliência** - intervalos, repetições, disjuntores
- **Política** – controlo de acessos, limites de taxas, quotas
- **Teste** - injeção de falhas

### <a name="security"></a>Segurança

- **Encriptação** - mTLS, gestão de certificados, CA externa
- **Identidade Forte** – SPIFFE ou similar
- **Auth** - autenticação, autorização

### <a name="observability"></a>Observabilidade

- **Métricas** - métricas douradas, prometheus, grafana
- **Rastreio** - vestígios em cargas de trabalho
- **Tráfego** – cluster, ingress/egress

### <a name="mesh"></a>Malha

- **Computação Suportada** - Kubernetes, máquinas virtuais
- **Multi-cluster** - gateways, federação

## <a name="selection-criteria"></a>Critérios de seleção

Antes de selecionar uma malha de serviço, certifique-se de que compreende os seus requisitos e as razões para instalar uma malha de serviço. Tente fazer as seguintes perguntas.

- **Um Controlador de Ingress é suficiente para as minhas necessidades?** - Por vezes, ter uma capacidade como testes a/b ou divisão de tráfego na entrada é suficiente para suportar o cenário necessário. Não adicione complexidade ao seu ambiente sem nenhum lado positivo.

- **As minhas cargas de trabalho e o meu ambiente podem tolerar as despesas adicionais?** - Todos os componentes adicionais necessários para suportar a malha de serviço requerem recursos adicionais como cpu e memória. Além disso, todos os proxies e os seus controlos políticos associados adicionam latência ao seu tráfego. Se tiver cargas de trabalho muito sensíveis à latência ou não puder fornecer os recursos adicionais para cobrir os componentes da malha de serviço, então reconsidere.

- **Isto é adicionar complexidade adicional desnecessariamente?** - Se a razão para instalar uma malha de serviço é para ganhar uma capacidade que não é necessariamente crítica para as equipas de negócio ou operacionais, então considere se a complexidade adicional de instalação, manutenção e configuração vale a pena.

- **Isto pode ser adotado numa abordagem incremental?** - Algumas das malshes de serviço que fornecem muitas capacidades podem ser adotadas numa abordagem mais incremental. Instale apenas os componentes necessários para garantir o seu sucesso. Uma vez mais confiante e são necessárias capacidades adicionais, então explore-as. Resista à vontade de instalar *tudo* desde o início.

Se, após cuidadosa consideração, decidir que precisa de uma malha de serviço para fornecer as capacidades necessárias, então a sua próxima decisão é qual a malha de *serviço?*

Considere as seguintes áreas e quais delas estão mais alinhadas com os seus requisitos. Isto irá guiá-lo para o melhor para o seu ambiente e cargas de trabalho. A secção [de passos seguintes](#next-steps) irá levá-lo a mais informações detalhadas sobre malshes de serviço específicos e como eles mapeiam para estas áreas.

- **Técnica** - gestão do tráfego, política, segurança, observabilidade

- **Negócios** - apoio comercial, fundação (CNCF), licença OSS, governação

- **Operacional** – instalação/upgrades, requisitos de recursos, requisitos de desempenho, integrações (métricas, telemetria, dashboards, ferramentas, SMI), cargas de trabalho mistas (piscinas de nólino e windows), computação (Kubernetes, máquinas virtuais), multi-cluster

- **Segurança** - auth, identidade, gestão de certificados e rotação, AC externo pluggable


## <a name="next-steps"></a>Passos seguintes

A seguinte documentação fornece mais informações sobre maltratas de serviço que pode experimentar no Serviço Azure Kubernetes (AKS):

> [!div class="nextstepaction"]
> [Saiba mais sobre Istio...][istio-about]

> [!div class="nextstepaction"]
> [Saiba mais sobre Linkerd ...][linkerd-about]

> [!div class="nextstepaction"]
> [Saiba mais sobre o Cônsul...][consul-about]

Você também pode querer explorar service Mesh Interface (SMI), uma interface padrão para malhas de serviço em Kubernetes:

- [Interface de malha de serviço (SMI)][smi]


<!-- LINKS - external -->
[smi]: https://smi-spec.io/

<!-- LINKS - internal -->
[istio-about]: ./servicemesh-istio-about.md
[linkerd-about]: ./servicemesh-linkerd-about.md
[consul-about]: ./servicemesh-consul-about.md