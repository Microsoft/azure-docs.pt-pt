---
title: Visão geral da malha de Service Fabric do Azure
description: Saiba mais sobre o Azure Service Fabric Mesh. Com o Service Fabric Mesh, pode implementar e dimensionar a sua aplicação sem se preocupar com as necessidades de infraestrutura.
author: dkkapur
ms.author: dekapur
ms.date: 10/1/2018
ms.topic: overview
ms.openlocfilehash: d6522d417556104a1ece703c725f3fbeab49d683
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458980"
---
# <a name="what-is-service-fabric-mesh"></a>O que é o Service Fabric Mesh?

Este vídeo apresenta uma descrição geral do Service Fabric Mesh.
> [!VIDEO https://www.youtube.com/embed/7qWeVGzAid0]

O Azure Service Fabric Mesh é um serviço totalmente gerido que permite aos programadores implementar aplicações de microsserviços sem gerir máquinas virtuais, armazenamento ou redes. A execução e o dimensionamento das aplicações alojadas no Service Fabric Mesh são efetuados sem ter de se preocupar com a respetiva infraestrutura.  O Service Fabric Mesh é constituído por clusters de milhares de máquinas.  Todas as operações dos clusters são ocultadas do programador. Carregue seu código e especifique os recursos necessários, os requisitos de disponibilidade e os limites de recursos.  O Service Fabric Mesh aloca automaticamente a infraestrutura e lida com falhas de infraestrutura, ao garantir que as suas aplicações são de elevada disponibilidade. Apenas tem de se preocupar com o estado de funcionamento e a capacidade de resposta da aplicação e não com a infraestrutura.  

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Este artigo fornece uma descrição geral dos principais benefícios do Service Fabric Mesh.

## <a name="great-developer-experience"></a>Excelente experiência de programação

O Service Fabric Mesh suporta qualquer linguagem de programação ou estrutura que possa ser executada num contentor. O suporte a ferramentas do Visual Studio 2019 e Visual Studio Code fornece uma experiência de edição e depuração poderosa para aplicativos .NET e .NET Core. 

Com o Service Fabric Mesh pode:

- Fazer a migração "lift-and-shift" das aplicações existentes para contentores para modernizar e executar as aplicações atuais à escala.
- Criar e implementar novas aplicações de microsserviços à escala no Azure.  Integrar noutros serviços do Azure ou aplicações existentes em execução em contentores. Cada microserviço faz parte de um aplicativo isolado de rede e seguro. O Microservice tem políticas de governança de recursos definidas para núcleos de CPU, memória, espaço em disco e muito mais.
- Integrar e expandir as aplicações existentes sem fazer alterações às mesmas. Utilizar a sua própria rede virtual para ligar a aplicação existente à nova aplicação.  
- Modernizar as aplicações dos Serviços Cloud existentes ao migrar para o Service Fabric Mesh.  

## <a name="simple-operational-lifecycle"></a>Ciclo de vida operacional simples

Gerencie facilmente aplicativos em execução, monitorando aplicativos e Depurando em ambientes de produção. Esse gerenciamento inclui atualizações de aplicativos e controle de versão. Estas aplicações podem ser constituídas por um único ou múltiplos microsserviços isolados na sua própria rede. As aplicações são executadas com eficiência com rápidos tempos de implementação, posicionamento e ativação pós-falha.

Com o Service Fabric Mesh pode:

- Implementar e gerir aplicações sem ter de aprovisionar e gerir explicitamente a infraestrutura.  O Service Fabric Mesh aprovisiona, atualiza, corrige e mantém a infraestrutura subjacente por si.
- Configurar a integração contínua através das ferramentas integradas para empacotar e implementar facilmente as aplicações.
- Aproveite todos os recursos de Azure Resource Manager recursos. Exemplos desses recursos incluem a trilha de auditoria e o [controle de acesso baseado em função](/azure/role-based-access-control/overview)). Todos os recursos que você implanta no serviço de malha Service Fabric no Azure são Azure Resource Manager recursos. Esses recursos incluem aplicativos, serviços, segredos e assim por diante.
- Implementar e gerir os recursos com o [portal do Azure](https://portal.azure.com), modelos do Resource Manager ou bibliotecas da CLI do Azure/PowerShell.
- Configurar a monitorização operacional e alertas através do [Application Insights](/azure/application-insights/) (ou da ferramenta à sua escolha) para capturar rastreios operacionais e de diagnóstico a partir da plataforma.
- Aceder às informações de diagnóstico das aplicações emitidas a partir do modelo de aplicação com o [Application Insights](/azure/application-insights/) ou a ferramenta à sua escolha.
- Otimize o uso de recursos especificando regras de dimensionamento automático para os serviços na definição do aplicativo.

## <a name="mission-critical-platform-capabilities"></a>Capacidades da plataforma fundamentais

O Service Fabric Mesh cria uma coleção de clusters que abrange [Zonas de Disponibilidade do Azure](/azure/availability-zones/az-overview) e/ou fronteiras regionais geo-políticas. A malha Service Fabric descreve os aplicativos com um conjunto de tentativas, como escala, requisitos de hardware, requisitos de durabilidade e políticas de segurança.  Quando a aplicação é implementada, o Service Fabric Mesh encontra o local ideal para executá-la.

Com o Service Fabric Mesh pode:

- Tirar partido da elevada disponibilidade, aumento ou redução horizontal, capacidade de deteção, orquestração, encaminhamento de mensagens, mensagens fiáveis, atualizações sem tempo de indisponibilidade, gestão de segurança/segredos, recuperação após desastre, gestão de estado, gestão de configuração e transações distribuídas.
- Escolher de entre vários modelos de aplicação na criação de aplicações.
- Utilizar as capacidades da plataforma expostas por pontos finais REST ao consumir enlaces de específicos da linguagem gerados através do Swagger.
- Implementar aplicações em [Zonas de Disponibilidade](/azure/availability-zones/az-overview) e várias regiões para fiabilidade geográfica.
- Utilizar todas as funcionalidades de segurança e conformidade fornecidas pelo Azure.

## <a name="next-steps"></a>Passos seguintes

Demora apenas alguns passos a implementar um projeto de exemplo com o Visual Studio. Para obter mais informações, veja [Criar um site ASP.NET Core](service-fabric-mesh-quickstart-dotnet-core.md). 

Encontre respostas a [perguntas comuns](service-fabric-mesh-faq.md).


<!-- Links -->

[service-fabric-overview]: ../service-fabric/service-fabric-overview.md
