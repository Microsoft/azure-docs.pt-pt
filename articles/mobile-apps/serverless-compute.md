---
title: Construa uma aplicação móvel sem servidores com funções azure e outros serviços
description: Conheça os serviços de computação utilizados para construir uma aplicação móvel sólida e sem servidores.
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: d6a2bbc984a8808d2cd8a9b292e55d80f43f2e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240137"
---
# <a name="build-mobile-back-end-components-with-compute-services"></a>Construir componentes móveis de back-end com serviços de computação
Todas as aplicações móveis precisam de um back end que seja responsável pelo armazenamento de dados, lógica empresarial e segurança. Gerir a infraestrutura para hospedar e executar código back-end requer que você dimensione, provisão e escala vários servidores. Também tem de gerir as atualizações do OS e o hardware envolvido e aplicar patches de segurança. Depois, é necessário monitorizar todos estes componentes de infraestrutura para o desempenho, disponibilidade e tolerância a falhas. 

A arquitetura sem servidores vem a calhar para este tipo de cenário porque não tem servidores para gerir e não há sistemas operativos ou atualizações de hardware relacionadas para gerir. A arquitetura sem servidores poupa tempo e custo ao desenvolvedor, o que significa tempo mais rápido para comercializar e energia focada na construção de aplicações.

## <a name="benefits-of-compute"></a>Benefícios da computação
- A abstração dos servidores significa que não há necessidade de se preocupar com hospedagem, remendos e segurança, o que lhe permite concentrar-se apenas no código.
- A escala instantânea e eficiente garante que os recursos são aprovisionados automaticamente ou a pedido em qualquer escala que necessite.
- Alta disponibilidade e tolerância à culpa.
- A micro-facturação garante que só é cobrado quando o seu código estiver realmente em funcionamento.
- O código corre na nuvem escrita na linguagem da sua escolha.

Utilize os seguintes serviços para ativar capacidades de computação sem servidores nas suas aplicações móveis.

## <a name="azure-functions"></a>Funções do Azure
[O Azure Functions](https://azure.microsoft.com/services/functions/) é uma experiência computacional orientada para eventos que pode utilizar para executar o seu código, escrito na linguagem de programação à sua escolha, sem se preocupar com servidores. Não é preciso gerir a aplicação ou a infraestrutura para a executar. As funções escalam a pedido, e você paga apenas pelo tempo que o seu código funciona. As funções azure são uma ótima maneira de implementar uma API para uma aplicação móvel. São fáceis de implementar e manter e são acessíveis através do HTTP.

**Principais funcionalidades**
- Orientado para eventos e escalável onde pode utilizar gatilhos e encadernações para definir quando uma função é invocada e para que dados se conecta.
- Traga as suas próprias dependências porque as Funções suportam NuGet e NPM, para que possa usar as suas bibliotecas favoritas.
- Segurança integrada para que possa proteger funções desencadeadas por HTTP com fornecedores da OAuth, tais como ODirecty Ativo, Facebook, Google, Twitter e Microsoft Account.
- Integração simplificada com diferentes ofertas de serviços e software [Azure](/azure/azure-functions/functions-overview) como um serviço (SaaS).
- Desenvolvimento flexível para que possa codificar as suas funções diretamente no portal Azure ou configurar uma integração contínua e implementar o seu código através do GitHub, Azure DevOps Services e outras ferramentas de desenvolvimento suportadas.
- O tempo de funcionamento das funções é de código aberto e está disponível no [GitHub](https://github.com/azure/azure-webjobs-sdk-script).
- Experiência de desenvolvimento melhorada onde pode codificar, testar e depurar localmente usando o seu editor preferido ou interface web fácil de usar com monitorização com ferramentas integradas e capacidades de DevOps incorporadas.
- Variedade de linguagens de programação e opções de hospedagem para desenvolvimento, tais como C#, Node.js, Java, JavaScript ou Python.
- Modelo de preços pay-per-use significa que você paga apenas pelo tempo gasto a executar o seu código.

**Referências**
- [Portal Azure](https://portal.azure.com)
- [Documentação das Funções do Azure](/azure/azure-functions/)
- [Guia do programador das Funções do Azure](/azure/azure-functions/functions-reference)
- [Arranques rápidos](/azure/azure-functions/functions-create-first-function-vs-code)
- [Amostras](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="azure-app-service"></a>App Service do Azure
Com o [Azure App Service,](https://azure.microsoft.com/services/app-service/)pode construir e hospedar aplicações web e APIs RESTful na linguagem de programação à sua escolha sem gerir infraestruturas. Oferece autoscalcificação e alta disponibilidade, suporta tanto o Windows como o Linux, e permite implementações automatizadas do GitHub, Azure DevOps ou qualquer repo Git.

**Principais funcionalidades**
- Múltiplas linguagens e suporte sinuoso para ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP ou Python. Também pode executar o PowerShell e outros scripts ou executáveis como serviços em segundo plano.
- Otimização de DevOps através da integração contínua e implantação com Azure DevOps, GitHub, BitBucket, Docker Hub ou Registo de Contentores Azure. Faça a gestão das suas aplicações no App Service utilizando o Azure PowerShell ou a interface de linha de comandos (CLI) de várias plataformas.
- Escala global com alta disponibilidade para escalar ou sair manualmente ou automaticamente.
- Ligações a plataformas SaaS e dados no local para escolher entre mais de 50 conectores para sistemas empresariais como o SAP, serviços SaaS como salesforce e serviços de internet como o Facebook. Acesso aos dados no local utilizando ligações híbridas e Redes Virtuais Azure.
- O Serviço de Aplicações Azure é compatível com ISO, SOC e PCI. Autenticar utilizadores com Diretório Ativo Azure ou com entrada para redes sociais como Google, Facebook, Twitter e Microsoft. Crie restrições de endereço IP e faça a gestão de identidades de serviço.
- Modelos de aplicação para escolher entre uma extensa lista de modelos de aplicação no Azure Marketplace, tais como WordPress, Joomla e Drupal.
- A integração do Estúdio Visual com ferramentas dedicadas no Visual Studio dinamiza o trabalho de criação, implantação e depuração.

**Referências**
- [Portal Azure](https://portal.azure.com/)
- [Documentação do Serviço de Aplicações Azure](/azure/app-service/)
- [Arranques rápidos](/azure/app-service/app-service-web-get-started-dotnet)
- [Amostras](/azure/app-service/samples-cli)
- [Tutoriais](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service
[O Serviço Azure Kubernetes (AKS)](https://azure.microsoft.com/services/kubernetes-service/) gere o seu ambiente kubernetes hospedado. A AKS torna-o rápido e fácil de implantar e gerir aplicações contentorizadas sem conhecimentos de orquestração de contentores. Elimina igualmente o peso das operações em curso e da manutenção. As provisões, atualizações e dimensionamentos de recursos da AKS, sem desativar as suas aplicações.

**Principais funcionalidades**
- Migrar facilmente as aplicações existentes para contentores e funcionar dentro do AKS.
- Simplificar a implantação e gestão de aplicações baseadas em microserviços.
- Secure DevOps para AKS alcançar o equilíbrio entre velocidade e segurança e entregar código mais rapidamente em escala.
- Escala com facilidade utilizando as instâncias de contentores AKS e Azure para fornecer cápsulas dentro de instâncias de contentores que começam em segundos.
- Implementar e gerir dispositivos IoT a pedido.
- Treine modelos de aprendizagem automática com a utilização de ferramentas como tensorFlow e KubeFlow.

**Referências**
- [Portal Azure](https://portal.azure.com/)
- [Documentação do Serviço Azure Kubernetes](/azure/aks/)
- [Arranques rápidos](/azure/aks/kubernetes-walkthrough-portal)
- [Tutoriais](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances
[As Instâncias de Contentores Azure](https://azure.microsoft.com/services/container-instances/) são uma ótima solução para qualquer cenário que possa operar em contentores isolados, tais como aplicações simples, automação de tarefas e construção de postos de trabalho. Desenvolver aplicações rapidamente sem gerir VMs.

**Principais funcionalidades**
- Tempos de arranque rápidos como Container Instances podem iniciar contentores em Azure em segundos, sem a necessidade de fornecer e gerir VMs.
- Conectividade IP pública e nome DNS personalizado.
- Segurança de nível hipervisor que garante que a sua aplicação está tão isolada num recipiente como num VM.
- Tamanhos personalizados para uma utilização ótima, permitindo especificações exatas de núcleos de CPU e memória. A faturação é ao segundo e consoante o que precisar, para que possa ajustar os gastos com base nas suas necessidades reais.
- Armazenamento persistente para recuperar e persistir estado. Container Instances oferece montagem direta de ações da Azure Files.
- Recipientes Linux e Windows agendados com a mesma API.

**Referências**
- [Portal Azure](https://portal.azure.com/)
- [Documentação do Azure Container Instances](/azure/container-instances/)
- [Arranques rápidos](/azure/container-instances/container-instances-quickstart-portal)
- [Amostras](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [Tutoriais](/azure/container-instances/container-instances-tutorial-prepare-app)