---
title: Sobre malhas de serviço
description: Obtenha uma visão geral das malhas de serviço, sua arquitetura e capacidades, e que critérios deve considerar ao selecionar um para implementar.
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: eca49a3fac1ea0398ebe1d05bde20fbca3c81232
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "77594316"
---
# <a name="about-service-meshes"></a>Sobre malhas de serviço

Uma malha de serviço fornece capacidades como gestão de tráfego, resiliência, política, segurança, identidade forte e observabilidade às suas cargas de trabalho. A sua aplicação é dissociada a partir destas capacidades operacionais e a malha de serviço move-as para fora da camada de aplicação, e para a camada de infraestrutura.

## <a name="scenarios"></a>Cenários

Estes são alguns dos cenários que podem ser ativados para as suas cargas de trabalho quando utiliza uma malha de serviço:

- **Criptografe todo o tráfego em cluster** - Ative o TLS mútuo entre serviços especificados no cluster. Isto pode ser estendido a entrada e saída no perímetro da rede. Fornece uma opção segura por predefinição sem alterações necessárias para o código de aplicação e infraestrutura.

- **Roll-outs canários e faseados** - Especifique as condições para que um subconjunto de tráfego seja encaminhado para um conjunto de novos serviços no cluster. No teste bem sucedido da libertação de canários, remova o encaminhamento condicional e aumente gradualmente % de todo o tráfego para um novo serviço. Eventualmente, todo o tráfego será direcionado para novo serviço.

- **Gestão e manipulação de tráfego** - Crie uma política sobre um serviço que irá limitar todo o tráfego a uma versão de um serviço de origem específica. Ou uma política que aplique uma estratégia de recandidutenção a classes de falhas entre serviços especificados. Espelhar o tráfego ao vivo para novas versões de serviços durante uma migração ou para depurar problemas. Injetar falhas entre serviços num ambiente de teste para testar a resiliência.

- **Visibilidade** - Obtenha informações sobre como os seus serviços estão ligados ao tráfego que flui entre eles. Obtenha métricas, troncos e vestígios para todo o tráfego em aglomerado, e entrada/saída. Adicione habilidades de rastreio distribuídas às suas aplicações.

## <a name="architecture"></a>Arquitetura

Uma malha de serviço é tipicamente composta por um plano de controlo e o plano de dados.

O **avião de controlo** tem uma série de componentes que suportam a gestão da malha de serviço. Isto normalmente incluirá uma interface de gestão que pode ser uma UI ou uma API. Haverá também, tipicamente, componentes que gerem as definições de regra e política que definem como a malha de serviço deve implementar capacidades específicas. Há também componentes que gerem aspetos de segurança como identidade forte e certificados para mTLS. As malhas de serviço também terão normalmente uma métrica ou componente de observabilidade que recolhe e agrega métricas e telemetria a partir das cargas de trabalho.

O plano de **dados** normalmente consiste de um proxy que é injetado de forma transparente como um sidecar para as suas cargas de trabalho. Este proxy está configurado para controlar todo o tráfego de rede dentro e fora da cápsula que contém a sua carga de trabalho. Isto permite que o proxy seja configurado para proteger o tráfego através de mTLS, rota dinâmica tráfego, aplicar políticas ao tráfego e recolher métricas e informações de rastreio. 

![Arquitetura típica de malha de serviço](media/servicemesh/typical-architecture.png)

## <a name="capabilities"></a>Capacidades

Cada uma das malhas de serviço tem um ajuste natural e foco no suporte a cenários específicos, mas você normalmente vai descobrir que a maioria irá implementar uma série de, se não todos, das seguintes capacidades.

### <a name="traffic-management"></a>Gestão do tráfego 

- **Protocolo** – camada 7 (http, grpc)
- **Encaminhamento Dinâmico** - condicionante, ponderação, espelhamento
- **Resiliência** - intervalos, retrações, disjuntores
- **Política** – controlo de acessos, limites de tarifas, quotas
- **Teste** - injeção de falha

### <a name="security"></a>Segurança

- **Encriptação** – mTLS, gestão de certificados, CA externo
- **Identidade Forte** – SPIFFE ou similar
- **Auth** – autenticação, autorização

### <a name="observability"></a>Observabilidade

- **Métricas** - métricas douradas, prometeu, grafana
- **Rastreio** - vestígios em cargas de trabalho
- **Tráfego** – cluster, entrada/saída

### <a name="mesh"></a>Malha

- **Computação suportada** - Kubernetes, máquinas virtuais
- **Multi-cluster** - gateways, federação

## <a name="selection-criteria"></a>Critérios de seleção

Antes de selecionar uma malha de serviço, certifique-se de que compreende os seus requisitos e as razões para instalar uma malha de serviço. Tente fazer as seguintes perguntas.

- **Um Controlador ingress é suficiente para as minhas necessidades?** - Por vezes, ter uma capacidade como testar a/b ou separar o tráfego na entrada é suficiente para suportar o cenário necessário. Não adicione complexidade ao seu ambiente sem vantagem.

- **As minhas cargas de trabalho e o meu ambiente podem tolerar as despesas adicionais?** - Todos os componentes adicionais necessários para suportar a malha de serviço requerem recursos adicionais como cpu e memória. Além disso, todos os proxies e os seus controlos de política associados adicionam latência ao seu tráfego. Se tiver cargas de trabalho muito sensíveis à latência ou não puder fornecer os recursos adicionais para cobrir os componentes da malha de serviço, então reencamquia.

- **Isto adiciona complexidade adicional desnecessariamente?** - Se a razão para instalar uma malha de serviço é ganhar uma capacidade que não seja necessariamente crítica para o negócio ou equipas operacionais, então considere se a complexidade adicional da instalação, manutenção e configuração vale a pena.

- **Isto pode ser adotado numa abordagem incremental?** - Algumas das malhas de serviço que fornecem muitas capacidades podem ser adotadas numa abordagem mais incremental. Instale apenas os componentes necessários para garantir o seu sucesso. Uma vez mais confiante e são necessárias capacidades adicionais, então explore-as. Resista à vontade de instalar *tudo* desde o início.

Se, após cuidadosa consideração, decidir que precisa de uma malha de serviço para fornecer as capacidades necessárias, então a sua próxima decisão é *qual malha de serviço?*

Considere as seguintes áreas e quais delas estão mais alinhadas com as suas necessidades. Isto irá guiá-lo para o melhor ajuste para o seu ambiente e cargas de trabalho. A secção [de passos seguintes](#next-steps) irá levá-lo para mais informações detalhadas sobre malhas de serviço específicas e como eles mapeiam para estas áreas.

- **Técnico** - gestão do tráfego, política, segurança, observabilidade

- **Negócios** - apoio comercial, fundação (CNCF), licença OSS, governação

- **Operacional** – instalação/upgrades, requisitos de recursos, requisitos de desempenho, integrações (métricas, telemetria, dashboards, ferramentas, SMI), cargas de trabalho mistas (piscinas de nó de Linux e Windows), computação (Kubernetes, máquinas virtuais), multi-cluster

- **Segurança** - auth, identidade, gestão e rotação de certificados, CA externo pluggable


## <a name="next-steps"></a>Passos seguintes

A documentação que se segue fornece mais informações sobre malhas de serviço que pode experimentar no Serviço Azure Kubernetes (AKS):

> [!div class="nextstepaction"]
> [Saiba mais sobre Istio...][istio-about]

> [!div class="nextstepaction"]
> [Saiba mais sobre Linkerd...][linkerd-about]

> [!div class="nextstepaction"]
> [Saiba mais sobre o Cônsul...][consul-about]

Também pode querer explorar a Interface de Malha de Serviço (SMI), uma interface padrão para malhas de serviço em Kubernetes:

- [Interface de malha de serviço (SMI)][smi]


<!-- LINKS - external -->
[smi]: https://smi-spec.io/

<!-- LINKS - internal -->
[istio-about]: ./servicemesh-istio-about.md
[linkerd-about]: ./servicemesh-linkerd-about.md
[consul-about]: ./servicemesh-consul-about.md