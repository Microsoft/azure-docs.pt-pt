---
title: Terminologia do malha de recursos de infraestrutura do serviço do Azure | Documentos da Microsoft
description: Conheça os termos comuns para o modo de malha do Azure Service Fabric.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 25e7ba20ea6054439d72b1827f32b8470e2b6a91
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58106632"
---
# <a name="service-fabric-mesh-terminology"></a>Terminologia de malha do Service Fabric

O Azure Service Fabric Mesh é um serviço totalmente gerido que permite aos programadores implementar aplicações de microsserviços sem gerir máquinas virtuais, armazenamento ou redes. Este artigo fornece detalhes sobre a terminologia utilizada pelo Azure Service Fabric em malha para compreender os termos usados na documentação.

## <a name="service-fabric"></a>Service Fabric

[Service Fabric](/azure/service-fabric/) é uma plataforma de sistemas distribuídos de código-fonte aberto que facilita o empacotamento, implementação e a gestão de microsserviços dimensionáveis e fiáveis. O Service Fabric é o orquestrador que capacita a malha de recursos de infraestrutura do serviço. O Service Fabric fornece opções para como criar e executar as suas aplicações de microsserviços. Pode utilizar qualquer estrutura para escrever seus serviços e escolha onde pretende executar a aplicação a partir de várias opções de ambiente.

## <a name="application-and-service-concepts"></a>Aplicação e conceitos do serviço

**Aplicação de Service Fabric malha**: Aplicações de Mesh do Service Fabric são descritas pela [modelo de recursos](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources) (arquivos de recursos YAML e JSON) e podem ser implementados em qualquer ambiente em que executa a Service Fabric.

**Aplicação de Service Fabric nativo**: Aplicativos nativos do Service Fabric são descritos pela [modelo de aplicativo nativo](/azure/service-fabric/service-fabric-application-model) (aplicativo baseado em XML e manifestos de serviço).  Aplicativos nativos do Service Fabric não é possível executar na malha de recursos de infraestrutura do serviço.

**Aplicação**: Um aplicativo de malha do Service Fabric é a unidade de implementação, o controle de versão e o tempo de vida de um aplicativo de malha. O ciclo de vida de cada instância da aplicação pode ser gerido de forma independente.  As aplicações são compostas por um ou mais pacotes de código de serviço e as definições. Um aplicativo é definido usando o esquema do modelo de recursos do Azure (RM).  Os serviços são descritos como propriedades do recurso de aplicação num modelo RM.  Redes e volumes utilizados pela aplicação são referenciados pela aplicação.  Ao criar uma aplicação, a aplicação, serviço ou serviços, rede e volumes são modelados com o modelo de recursos do Service Fabric.

**Serviço**: Um serviço num aplicativo representa um microsserviço e executa uma função completa e autónoma. Cada serviço é composto por um ou mais, pacotes de código que descrevem tudo necessários para executar a imagem de contentor associada ao pacote de código.  O número de réplicas do serviço num aplicativo pode ser dimensionado de entrada e saída.

**Pacote do código**: Pacotes do código descrevem tudo necessários para executar a imagem de contentor associada ao pacote de código, incluindo o seguinte:

* Nome do contentor, versão e do Registro
* Recursos de CPU e memória necessários para cada contentor
* Pontos finais de rede
* Volumes para montar no contentor, fazendo referência um recurso de volume separado.

## <a name="deployment-and-application-models"></a>Modelos de implementação e aplicação 

Para implementar os seus serviços, terá de descrever como deve ser executado. O Service Fabric suporta três modelos de implementação diferentes:

### <a name="resource-model"></a>Modelo de recursos
Recursos de infraestrutura do serviço são tudo o que podem ser implementados individualmente para o Service Fabric; incluindo aplicações, serviços, redes e volumes. Recursos são definidos através de um ficheiro JSON, que pode ser implementado para um ponto de extremidade do cluster.  Para a malha de recursos de infraestrutura de serviço, é utilizado o esquema do modelo de recursos do Azure. Um esquema de ficheiro YAML também pode ser usado para arquivos de definição para criar mais facilmente. Podem ser implementados recursos em qualquer lugar que executa a Service Fabric. O modelo de recursos é a forma mais simples para descrever as suas aplicações do Service Fabric. Seu foco principal é sobre a implantação simple e gestão de serviços em contentores. Para saber mais, leia [introdução para o modelo de recursos do Service Fabric](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources).

### <a name="native-model"></a>Modelo nativo
O modelo de aplicação nativa fornece seus aplicativos com acesso completo de nível baixo para o Service Fabric. Aplicações e serviços são definidos como tipos registados nos ficheiros de manifestos XML.

O modelo nativo suporta o framework de Reliable Services, que fornece acesso a APIs de runtime do Service Fabric e gestão de clusters de APIs em c# e Java. O modelo nativo também suporta arbitrários contentores e executáveis.

O modelo nativo não é suportado no ambiente de malha do Service Fabric.  Para obter mais informações, consulte [descrição geral do modelo de programação](/azure/service-fabric/service-fabric-choose-framework).

### <a name="docker-compose"></a>Docker Compose 
[Docker Compose](https://docs.docker.com/compose/) faz parte do projeto Docker. O Service Fabric fornece suporte limitado para implantar aplicativos usando o modelo de Docker Compose.

## <a name="environments"></a>Ambientes

Service Fabric é uma tecnologia de plataforma de código aberto baseados em vários produtos e serviços diferentes. A Microsoft fornece as seguintes opções:

 - **Malha de recursos de infraestrutura do serviço**: Um serviço totalmente gerido para executar aplicações do Service Fabric no Microsoft Azure.
 - **Azure Service Fabric**: O Azure alojado oferta de cluster do Service Fabric. Ele fornece integração entre o Service Fabric e a infraestrutura do Azure, juntamente com a gestão de atualização e configuração de clusters do Service Fabric.
 - **Autónomo do Service Fabric**: Um conjunto de ferramentas de instalação e configuração para [implementar clusters do Service Fabric em qualquer lugar](/azure/service-fabric/service-fabric-deploy-anywhere) (no local ou em qualquer fornecedor de cloud). Não é gerido pelo Azure.
 - **Cluster de desenvolvimento do Service Fabric**: Fornece uma experiência de desenvolvimento local no Windows, Linux ou Mac para desenvolvimento de aplicações do Service Fabric.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Matriz de suporte de ambiente, o framework e o modelo de implementação
Ambientes diferentes tem vários níveis de suporte de estruturas e modelos de implementação. A tabela seguinte descreve a estrutura de suporte e combinações de modelo de implementação.

| Tipo de aplicação | Descrito por | Malha de recursos de infraestrutura do serviço do Azure | Clusters de recursos de infraestrutura do serviço do Azure (qualquer sistema operacional)| Local cluster | Cluster autónomo |
|---|---|---|---|---|---|
| Aplicações de malha do Service Fabric | Modelo de recursos (YAML & JSON) | Suportadas |Não suportado | Windows – suportado, Linux e Mac não suportado | Windows e não suportados |
|Aplicativos nativos do Service Fabric | Modelo de aplicativo nativo (XML) | Não suportado| Suportadas|Suportadas|Windows – suportado|

A tabela seguinte descreve os modelos de aplicação diferente e as ferramentas existentes para os mesmos em relação a Service Fabric.

| Tipo de aplicação | Descrito por | Visual Studio | Eclipse | SFCTL | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Aplicações de malha do Service Fabric | Modelo de recursos (YAML & JSON) | VS 2017 |Não suportado |Não suportado | Suportado - apenas ambiente de malha | Não suportado|
|Aplicativos nativos do Service Fabric | Modelo de aplicativo nativo (XML) | VS 2017 e VS 2015| Suportadas|Suportadas|Suportadas|Suportadas|

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre o Service Fabric Mesh, consulte [Descrição geral do Service Fabric Mesh](service-fabric-mesh-overview.md).

Encontre respostas a [perguntas comuns](service-fabric-mesh-faq.md).
