---
title: Terminologia da malha de Service Fabric do Azure | Microsoft Docs
description: Este artigo detalha a terminologia usada pela malha de Service Fabric do Azure para ajudá-lo a entender melhor os termos usados na documentação.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: cacf3a7419982e8c0e8d55700c903e3414cfcbb4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73663015"
---
# <a name="service-fabric-mesh-terminology"></a>Terminologia de malha de Service Fabric

O Azure Service Fabric Mesh é um serviço totalmente gerido que permite aos programadores implementar aplicações de microsserviços sem gerir máquinas virtuais, armazenamento ou redes. Este artigo detalha a terminologia usada pela malha de Service Fabric do Azure para ajudá-lo a entender melhor os termos usados na documentação.

## <a name="service-fabric"></a>Service Fabric

O [Service Fabric](/azure/service-fabric/) é uma plataforma de sistemas distribuídos de software livre que torna mais fácil empacotar, implantar e gerenciar microservices escalonáveis e confiáveis. Service Fabric é o orquestrador que capacita Service Fabric malha. Service Fabric fornece opções de como você pode criar e executar seus aplicativos de microserviço. Você pode usar qualquer estrutura para escrever seus serviços e escolher onde executar o aplicativo de várias opções de ambiente.

## <a name="application-and-service-concepts"></a>Conceitos de aplicativos e serviços

**Service Fabric aplicativo de malha**: Service Fabric aplicativos de malha são descritos pelo [modelo de recursos](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources) (YAML e arquivos de recurso JSON) e podem ser implantados em qualquer ambiente onde Service Fabric é executado.

**Service Fabric aplicativo nativo**: Service Fabric aplicativos nativos são descritos pelo [modelo de aplicativo nativo](/azure/service-fabric/service-fabric-application-model) (manifestos de aplicativo e serviço baseados em XML).  Service Fabric aplicativos nativos não podem ser executados em Service Fabric malha.

**Aplicativo**: um aplicativo de malha Service Fabric é a unidade de implantação, controle de versão e tempo de vida de um aplicativo de malha. O ciclo de vida de cada instância do aplicativo pode ser gerenciado de forma independente.  Os aplicativos são compostos de um ou mais pacotes e configurações de código de serviço. Um aplicativo é definido usando o esquema do modelo de recursos do Azure (RM).  Os serviços são descritos como propriedades do recurso de aplicativo em um modelo RM.  Redes e volumes usados pelo aplicativo são referenciados pelo aplicativo.  Ao criar um aplicativo, o aplicativo, serviço (s), rede e volume (s) são modelados usando o modelo de recurso Service Fabric.

**Serviço**: um serviço em um aplicativo representa um microserviço e executa uma função completa e autônoma. Cada serviço é composto por um ou mais pacotes de código que descrevem tudo o que é necessário para executar a imagem de contêiner associada ao pacote de códigos.  O número de réplicas de serviço em um aplicativo pode ser expandido para dentro e para fora.

**Pacote de códigos**: pacotes de código descrevem tudo o que é necessário para executar a imagem de contêiner associada ao pacote de códigos, incluindo o seguinte:

* Nome do contêiner, versão e registro
* Recursos de CPU e memória necessários para cada contêiner
* Pontos de extremidade de rede
* Volumes a serem montados no contêiner, fazendo referência a um recurso de volume separado.

## <a name="deployment-and-application-models"></a>Implantação e modelos de aplicativo 

Para implantar seus serviços, você precisa descrever como eles devem ser executados. O Service Fabric dá suporte a três modelos de implantação diferentes:

### <a name="resource-model"></a>Modelo de recursos
Service Fabric recursos são tudo o que pode ser implantado individualmente para Service Fabric; incluindo aplicativos, serviços, redes e volumes. Os recursos são definidos usando um arquivo JSON, que pode ser implantado em um ponto de extremidade do cluster.  Para Service Fabric malha, o esquema de modelo de recurso do Azure é usado. Um esquema de arquivo YAML também pode ser usado para criar arquivos de definição com mais facilidade. Os recursos podem ser implantados em qualquer lugar Service Fabric executado. O modelo de recurso é a maneira mais simples de descrever seus Service Fabric aplicativos. Seu foco principal é a implantação simples e o gerenciamento de serviços em contêineres. Para saber mais, leia [introdução ao modelo de recurso Service Fabric](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources).

### <a name="native-model"></a>Modelo nativo
O modelo de aplicativo nativo fornece aos seus aplicativos acesso completo de baixo nível a Service Fabric. Os aplicativos e serviços são definidos como tipos registrados em arquivos de manifesto XML.

O modelo nativo dá suporte à estrutura Reliable Services, que fornece acesso às APIs de tempo de execução Service Fabric e APIs C# de gerenciamento de cluster no e Java. O modelo nativo também dá suporte a contêineres e executáveis arbitrários.

Não há suporte para o modelo nativo no ambiente de malha Service Fabric.  Para obter mais informações, consulte [visão geral do modelo de programação](/azure/service-fabric/service-fabric-choose-framework).

### <a name="docker-compose"></a>Docker Compose 
[Docker Compose](https://docs.docker.com/compose/) faz parte do projeto do Docker. Service Fabric fornece suporte limitado para implantar aplicativos usando o modelo de Docker Compose.

## <a name="environments"></a>Ambientes

Service Fabric é uma tecnologia de plataforma de software livre na qual vários serviços e produtos são baseados. A Microsoft fornece as seguintes opções:

 - **Malha de Service Fabric**: um serviço totalmente gerenciado para executar Service Fabric aplicativos no Microsoft Azure.
 - **Service Fabric do Azure**: a oferta de cluster de Service Fabric hospedado do Azure. Ele fornece integração entre Service Fabric e a infraestrutura do Azure, juntamente com o gerenciamento de atualização e configuração de clusters de Service Fabric.
 - **Service Fabric autônomo**: um conjunto de ferramentas de instalação e configuração para [implantar Service Fabric clusters em qualquer lugar](/azure/service-fabric/service-fabric-deploy-anywhere) (local ou em qualquer provedor de nuvem). Não gerenciado pelo Azure.
 - **Service Fabric cluster de desenvolvimento**: fornece uma experiência de desenvolvimento local no Windows, Linux ou Mac para o desenvolvimento de aplicativos Service Fabric.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Matriz de suporte de ambiente, estrutura e modelo de implantação
Ambientes diferentes têm um nível diferente de suporte para estruturas e modelos de implantação. A tabela a seguir descreve as combinações de estrutura e modelo de implantação com suporte.

| Tipo de aplicativo | Descrito por | Malha de Service Fabric do Azure | Clusters Service Fabric do Azure (qualquer sistema operacional)| Cluster local | Cluster autónomo |
|---|---|---|---|---|---|
| Service Fabric aplicativos de malha | Modelo de recurso (YAML & JSON) | Suportado |Não suportado | Windows-com suporte, Linux e Mac-sem suporte | Windows-sem suporte |
|Service Fabric aplicativos nativos | Modelo de aplicativo nativo (XML) | Não Suportado| Suportado|Suportado|Windows-com suporte|

A tabela a seguir descreve os diferentes modelos de aplicativo e as ferramentas que existem para eles em relação ao Service Fabric.

| Tipo de aplicativo | Descrito por | Visual Studio | Eclipse | SFCTL | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Service Fabric aplicativos de malha | Modelo de recurso (YAML & JSON) | VS 2017 |Não suportado |Não suportado | Com suporte-somente ambiente de malha | Não Suportado|
|Service Fabric aplicativos nativos | Modelo de aplicativo nativo (XML) | VS 2017 e VS 2015| Suportado|Suportado|Suportado|Suportado|

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Service Fabric Mesh, consulte [Descrição geral do Service Fabric Mesh](service-fabric-mesh-overview.md).

Encontre respostas a [perguntas comuns](service-fabric-mesh-faq.md).
