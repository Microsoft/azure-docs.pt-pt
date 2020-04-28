---
title: Terminologia para malha de tecido de serviço Azure
description: Este artigo detalha a terminologia utilizada pela Malha de Tecido de Serviço Azure para ajudá-lo a entender melhor os termos usados na documentação.
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: 6df7a6b708bca81f1390ac879f24ea4c22c38bee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75351978"
---
# <a name="service-fabric-mesh-terminology"></a>Terminologia de malha de tecido de serviço

O Azure Service Fabric Mesh é um serviço totalmente gerido que permite aos programadores implementar aplicações de microsserviços sem gerir máquinas virtuais, armazenamento ou redes. Este artigo detalha a terminologia utilizada pela Malha de Tecido de Serviço Azure para ajudá-lo a entender melhor os termos usados na documentação.

## <a name="service-fabric"></a>Service Fabric

[O Service Fabric](/azure/service-fabric/) é uma plataforma de sistemas distribuídos de código aberto que facilita o pacote, implementação e gestão de microserviços escaláveis e fiáveis. Service Fabric é o orquestrador que alimenta a malha de tecido de serviço. O Service Fabric oferece opções para como pode construir e executar as suas aplicações de microserviços. Pode utilizar qualquer enquadramento para escrever os seus serviços e escolher onde executar a aplicação a partir de múltiplas opções ambientais.

## <a name="application-and-service-concepts"></a>Conceitos de aplicação e serviço

**Aplicação de malha**de tecido de serviço : As aplicações de malha de tecido de serviço são descritas pelo Modelo de [Recursos](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources) (ficheiros de recursos YAML e JSON) e podem ser implantadas em qualquer ambiente onde o Tecido de Serviço funciona.

**Aplicação nativa de tecido de serviço**: Aplicações nativas de tecido de serviço são descritas pelo Modelo de [Aplicação Nativa](/azure/service-fabric/service-fabric-application-model) (aplicação baseada em XML e manifestos de serviço).  Aplicações nativas de tecido de serviço não podem ser executadas em malha de tecido de serviço.

**Aplicação**: Uma aplicação de malha de tecido de serviço é a unidade de implantação, versão e vida útil de uma aplicação de malha. O ciclo de vida de cada instância de aplicação pode ser gerido de forma independente.  As aplicações são compostas por um ou mais pacotes e configurações de código de serviço. Uma aplicação é definida utilizando o esquema do Modelo de Recursos Azure (RM).  Os serviços são descritos como propriedades do recurso de aplicação num modelo RM.  As redes e volumes utilizados pela aplicação são referenciados pela aplicação.  Ao criar uma aplicação, a aplicação, serviço(s), rede e volume(s) são modelados utilizando o Modelo de Recursos de Tecido de Serviço.

**Serviço**: Um serviço numa aplicação representa um microserviço e desempenha uma função completa e autónoma. Cada serviço é composto por um, ou mais, pacotes de código que descrevem tudo o que é necessário para executar a imagem do recipiente associada ao pacote de código.  O número de réplicas de serviço numa aplicação pode ser dimensionado dentro e fora.

**Pacote de código**: Os pacotes de código descrevem tudo o que é necessário para executar a imagem do recipiente associada ao pacote de código, incluindo o seguinte:

* Nome, versão e registo do contentor
* CPU e recursos de memória necessários para cada recipiente
* Pontos finais da rede
* Volumes para montar no recipiente, fazendo referência a um recurso de volume separado.

## <a name="deployment-and-application-models"></a>Modelos de implementação e aplicação 

Para implementar os seus serviços, precisa descrever como devem funcionar. O Service Fabric suporta três modelos de implantação diferentes:

### <a name="resource-model"></a>Modelo de recursos
Recursos de Tecido de Serviço são tudo o que pode ser implantado individualmente para o Tecido de Serviço; incluindo aplicações, serviços, redes e volumes. Os recursos são definidos usando um ficheiro JSON, que pode ser implantado para um ponto final de cluster.  Para malha de tecido de serviço, é utilizado o esquema do Modelo de Recurso Azure. Um esquema de ficheiro YAML também pode ser usado para ficheiros de definição de autor mais facilmente. Os recursos podem ser implantados em qualquer lugar que o Tecido de Serviço saisse. O modelo de recurso é a forma mais simples de descrever as suas aplicações de Tecido de Serviço. O seu foco principal é a simples implantação e gestão de serviços contentorizados. Para saber mais, leia Introdução ao Modelo de Recursos de [Tecido de Serviço](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources).

### <a name="native-model"></a>Modelo nativo
O modelo de aplicação nativa fornece às suas aplicações acesso completo de baixo nível ao Tecido de Serviço. As aplicações e serviços são definidos como tipos registados em ficheiros manifestos XML.

O modelo nativo suporta o quadro de Serviços Fiáveis, que proporciona acesso às APIs de tempo de execução do Tecido de Serviço e APIs de gestão de clusters em C# e Java. O modelo nativo também suporta recipientes arbitrários e executáveis.

O modelo nativo não é suportado no ambiente de malha de tecido de serviço.  Para mais informações, consulte a visão geral do modelo de [programação.](/azure/service-fabric/service-fabric-choose-framework)

### <a name="docker-compose"></a>Docker Compor 
[Docker Compose](https://docs.docker.com/compose/) faz parte do projeto Docker. O Service Fabric fornece suporte limitado para a implementação de aplicações utilizando o modelo Docker Compose.

## <a name="environments"></a>Ambientes

O Service Fabric é uma tecnologia de plataforma de código aberto em que vários serviços e produtos diferentes se baseiam. A Microsoft fornece as seguintes opções:

 - **Malha de tecido de serviço**: Um serviço totalmente gerido para executar aplicações de tecido de serviço no Microsoft Azure.
 - **Azure Service Fabric**: O Azure acolheu a oferta de cluster de tecido de serviço. Proporciona integração entre o Tecido de Serviço e a infraestrutura Azure, juntamente com a gestão de upgrade e configuração dos clusters service Fabric.
 - **Serviço Tecido autónomo**: Um conjunto de ferramentas de instalação e configuração para [implantar clusters de tecido](/azure/service-fabric/service-fabric-deploy-anywhere) de serviço em qualquer lugar (no local ou em qualquer fornecedor de nuvem). Não gerido por Azure.
 - **Cluster**de desenvolvimento de tecido de serviço : Proporciona uma experiência de desenvolvimento local em Windows, Linux ou Mac para o desenvolvimento de aplicações de Tecido de Serviço.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Matriz de suporte ao modelo de ambiente, enquadramento e implementação
Ambientes diferentes têm diferentes níveis de apoio a quadros e modelos de implantação. A tabela seguinte descreve as combinações de modelos de estrutura e de implantação suportadas.

| Tipo de Aplicação | Descrito por | Malha de tecido de serviço azure | Clusters de tecido de serviço Azure (qualquer Os)| Aglomerado local | Cluster autónomo |
|---|---|---|---|---|---|
| Aplicações de malha de tecido de serviço | Modelo de Recursos (YAML & JSON) | Suportado |Não suportado | Suportado pelo Windows, Linux e Mac não suportados | Windows- não suportado |
|Aplicações nativas de tecido de serviço | Modelo de aplicação nativa (XML) | Não suportado| Suportado|Suportado|Suportado pelo Windows|

A tabela seguinte descreve os diferentes modelos de aplicação e a ferramenta que existe para eles contra o Tecido de Serviço.

| Tipo de Aplicação | Descrito por | Visual Studio | Eclipse | SFCTL | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Aplicações de malha de tecido de serviço | Modelo de Recursos (YAML & JSON) | VS 2017 |Não suportado |Não suportado | Suportado - Apenas ambiente de malha | Não suportado|
|Aplicações nativas de tecido de serviço | Modelo de aplicação nativa (XML) | VS 2017 e VS 2015| Suportado|Suportado|Suportado|Suportado|

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Service Fabric Mesh, consulte [Descrição geral do Service Fabric Mesh](service-fabric-mesh-overview.md).

Encontre respostas a [perguntas comuns](service-fabric-mesh-faq.md).
