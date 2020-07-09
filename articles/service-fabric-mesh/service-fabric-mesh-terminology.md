---
title: Terminologia para malha de tecido de serviço Azure
description: Este artigo detalha a terminologia utilizada pela Malha de Tecido de Serviço Azure para ajudá-lo a entender melhor os termos utilizados na documentação.
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: 6df7a6b708bca81f1390ac879f24ea4c22c38bee
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75351978"
---
# <a name="service-fabric-mesh-terminology"></a>Terminologia da malha de tecido de serviço

O Azure Service Fabric Mesh é um serviço totalmente gerido que permite aos programadores implementar aplicações de microsserviços sem gerir máquinas virtuais, armazenamento ou redes. Este artigo detalha a terminologia utilizada pela Malha de Tecido de Serviço Azure para ajudá-lo a entender melhor os termos utilizados na documentação.

## <a name="service-fabric"></a>Service Fabric

[O Service Fabric](/azure/service-fabric/) é uma plataforma de sistemas distribuídos de código aberto que facilita a embalagem, implantação e gestão de microserviços escaláveis e fiáveis. Service Fabric é o orquestrador que alimenta a malha de tecido de serviço. O Service Fabric oferece opções para como pode construir e executar as suas aplicações de microserviços. Pode utilizar qualquer enquadramento para escrever os seus serviços e escolher onde executar a aplicação a partir de múltiplas opções ambientais.

## <a name="application-and-service-concepts"></a>Conceitos de aplicação e serviço

**Aplicação de malha de tecido de serviço**: As aplicações de malha de tecido de serviço são descritas pelo Modelo de [Recurso](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources) (ficheiros de recursos YAML e JSON) e podem ser implantadas em qualquer ambiente onde o Tecido de Serviço funciona.

**Aplicação nativa do tecido de**serviço : Aplicações nativas do tecido de serviço são descritas pelo Modelo de [Aplicação Nativa](/azure/service-fabric/service-fabric-application-model) (manifestos de aplicação e serviço baseados em XML).  As aplicações nativas do tecido de serviço não podem funcionar na malha de tecido de serviço.

**Aplicação**: Uma aplicação de malha de tecido de serviço é a unidade de implantação, versão e vida útil de uma aplicação de malha. O ciclo de vida de cada instância de aplicação pode ser gerido de forma independente.  As aplicações são compostas por um ou mais pacotes de código de serviço e configurações. Uma aplicação é definida usando o esquema do Modelo de Recursos Azure (RM).  Os serviços são descritos como propriedades do recurso de aplicação num modelo de RM.  As redes e volumes utilizados pela aplicação são referenciados pela aplicação.  Ao criar uma aplicação, a aplicação, serviços, rede e volume(s) são modelados usando o Modelo de Recursos de Tecido de Serviço.

**Serviço**: Um serviço numa aplicação representa um microserviço e executa uma função completa e autónoma. Cada serviço é composto por um, ou mais, pacotes de código que descrevem tudo o que é necessário para executar a imagem do recipiente associada ao pacote de código.  O número de réplicas de serviço numa aplicação pode ser dimensionado para dentro e para fora.

**Pacote de código**: Os pacotes de código descrevem tudo o que é necessário para executar a imagem do recipiente associada ao pacote de código, incluindo o seguinte:

* Nome do recipiente, versão e registo
* CPU e recursos de memória necessários para cada contentor
* Pontos finais da rede
* Volumes a montar no recipiente, referindo um recurso de volume separado.

## <a name="deployment-and-application-models"></a>Modelos de implementação e aplicação 

Para implementar os seus serviços, é necessário descrever como devem funcionar. O Service Fabric suporta três modelos de implementação diferentes:

### <a name="resource-model"></a>Modelo de recursos
Recursos de Tecido de Serviço são tudo o que pode ser implementado individualmente para o Service Fabric; incluindo aplicações, serviços, redes e volumes. Os recursos são definidos usando um ficheiro JSON, que pode ser implantado num ponto final de cluster.  Para a malha de tecido de serviço, é utilizado o esquema do Modelo de Recurso Azure. Um esquema de ficheiro YAML também pode ser usado para mais facilmente os ficheiros de definição de autor. Os recursos podem ser implantados em qualquer lugar que o Service Fabric executa. O modelo de recursos é a forma mais simples de descrever as suas aplicações de Tecido de Serviço. O seu foco principal é a simples implantação e gestão de serviços contentorizados. Para saber mais, leia [Introdução ao Modelo de Recursos de Tecido de Serviço.](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources)

### <a name="native-model"></a>Modelo nativo
O modelo de aplicação nativa fornece às suas aplicações acesso total de baixo nível ao Service Fabric. As aplicações e serviços são definidos como tipos registados em ficheiros manifestos XML.

O modelo nativo suporta o quadro de Serviços Fiáveis, que fornece acesso às APIs de execução do Tecido de Serviço e APIs de gestão de clusters em C# e Java. O modelo nativo também suporta recipientes arbitrários e executáveis.

O modelo nativo não é suportado no ambiente de malha de tecido de serviço.  Para obter mais informações, consulte [a visão geral do modelo de programação.](/azure/service-fabric/service-fabric-choose-framework)

### <a name="docker-compose"></a>Docker Compor 
[Docker Compose](https://docs.docker.com/compose/) faz parte do projeto Docker. A Service Fabric oferece suporte limitado para a implementação de aplicações utilizando o modelo Docker Compose.

## <a name="environments"></a>Ambientes

A Service Fabric é uma tecnologia de plataforma de código aberto em que vários serviços e produtos diferentes são baseados. A Microsoft fornece as seguintes opções:

 - **Malha de tecido de serviço**: um serviço totalmente gerido para executar aplicações de Tecido de Serviço no Microsoft Azure.
 - **Azure Service Fabric**: O Azure acolheu a oferta de cluster de tecido de serviço. Proporciona integração entre o Service Fabric e a infraestrutura Azure, juntamente com a gestão de upgrade e configuração dos clusters de Tecidos de Serviço.
 - **Suporte autónomo do tecido**: Um conjunto de ferramentas de instalação e configuração para [implantar clusters de tecido de serviço em qualquer lugar](/azure/service-fabric/service-fabric-deploy-anywhere) (no local ou em qualquer fornecedor de nuvem). Não gerido por Azure.
 - **Cluster de desenvolvimento de tecidos de serviço**: Proporciona uma experiência de desenvolvimento local em Windows, Linux ou Mac para o desenvolvimento de aplicações de Tecido de Serviço.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Matriz de suporte de modelos de ambiente, enquadramento e implantação
Diferentes ambientes têm diferentes níveis de apoio para quadros e modelos de implantação. O quadro seguinte descreve as combinações de modelos de estrutura e implantação suportadas.

| Tipo de Aplicação | Descrito por | Malha de tecido de serviço Azure | Clusters de tecido de serviço Azure (qualquer SISTEMA)| Aglomerado local | Cluster autónomo |
|---|---|---|---|---|---|
| Aplicações de malha de tecido de serviço | Modelo de recursos (YAML & JSON) | Suportado |Não suportado | Windows- suportado, Linux e Mac- não suportados | Windows- não suportado |
|Aplicações nativas do tecido de serviço | Modelo de aplicação nativa (XML) | Não suportado| Suportado|Suportado|Windows- suportado|

A tabela a seguir descreve os diferentes modelos de aplicação e a ferramenta que existe para os mesmos contra o Tecido de Serviço.

| Tipo de Aplicação | Descrito por | Visual Studio | Eclipse | SFCTL | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Aplicações de malha de tecido de serviço | Modelo de recursos (YAML & JSON) | VS 2017 |Não suportado |Não suportado | Suportado - Ambiente de malha apenas | Não suportado|
|Aplicações nativas do tecido de serviço | Modelo de aplicação nativa (XML) | VS 2017 e VS 2015| Suportado|Suportado|Suportado|Suportado|

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre o Service Fabric Mesh, consulte [Descrição geral do Service Fabric Mesh](service-fabric-mesh-overview.md).

Encontre respostas a [perguntas comuns](service-fabric-mesh-faq.md).
