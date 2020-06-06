---
title: Construa uma aplicação móvel sem servidor com funções Azure e outros serviços
description: Conheça os serviços de computação utilizados para construir uma aplicação móvel sólida e sem servidor.
author: codemillmatt
ms.service: mobile-services
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 06eb757955f403b91ae38602f4dc8ccc3fa52f26
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450977"
---
# <a name="build-mobile-back-end-components-with-compute-services"></a>Construa componentes móveis de back-end com serviços de computação
Todas as aplicações móveis precisam de uma parte traseira responsável pelo armazenamento de dados, lógica de negócios e segurança. Gerir a infraestrutura para hospedar e executar código back-end requer que você dimensione, provisões e escala vários servidores. Também tem de gerir as atualizações de SO e o hardware envolvido e aplicar patches de segurança. Em seguida, você precisa monitorizar todos estes componentes de infraestrutura para desempenho, disponibilidade e tolerância a falhas. 

A arquitetura sem servidores vem a calhar para este tipo de cenário porque não tem servidores para gerir e nenhum sistema operativo ou atualizações de hardware relacionadas para gerir. A arquitetura sem servidores poupa tempo e custo ao desenvolvedor, o que significa tempo mais rápido para comercializar e energia focada na construção de aplicações.

## <a name="benefits-of-compute"></a>Benefícios do cálculo
- A abstração de servidores significa que não há necessidade de se preocupar em hospedar, remendar e segurança, o que permite concentrar-se exclusivamente no código.
- A escala instantânea e eficiente garante que os recursos são a provisionados automaticamente ou a pedido em qualquer escala que você precisa.
- Alta disponibilidade e tolerância a falhas.
- A micro-faturação garante que só é cobrado quando o seu código estiver realmente em funcionamento.
- O código corre na nuvem escrita na linguagem à sua escolha.

Utilize os seguintes serviços para ativar as capacidades de computação sem servidor nas suas aplicações móveis.

## <a name="azure-functions"></a>Funções do Azure
[A azure Functions](https://azure.microsoft.com/services/functions/) é uma experiência computacional orientada para o evento que pode usar para executar o seu código, escrito na linguagem de programação à sua escolha, sem se preocupar com servidores. Não é preciso gerir a aplicação ou a infraestrutura para a executar. As funções são de escala a pedido, e paga-se apenas pelo tempo que o seu código for executado. As funções Azure são uma ótima maneira de implementar uma API para uma aplicação móvel. São fáceis de implementar e manter e são acessíveis através de HTTP.

**Principais funcionalidades**
- Orientado para o evento e escalável onde pode usar gatilhos e encadernações para definir quando uma função é invocada e para que dados ele conecta.
- Traga as suas próprias dependências porque as funções suportam NuGet e NPM, para que possa utilizar as suas bibliotecas favoritas.
- Segurança integrada para que possa proteger funções desencadeadas por HTTP com fornecedores OAuth como Azure Ative Directory, Facebook, Google, Twitter e Microsoft Account.
- Integração simplificada com [diferentes serviços](/azure/azure-functions/functions-overview) e software Azure como ofertas de serviço (SaaS).
- Desenvolvimento flexível para que possa codificar as suas funções diretamente no portal Azure ou configurar a integração contínua e implementar o seu código através do GitHub, serviços Azure DevOps e outras ferramentas de desenvolvimento suportadas.
- O tempo de funcionamento das funções é de código aberto e está disponível no [GitHub](https://github.com/azure/azure-webjobs-sdk-script).
- Experiência de desenvolvimento melhorada onde pode codificar, testar e depurar localmente, utilizando o seu editor preferido ou interface web fácil de usar com monitorização com ferramentas integradas e capacidades de DevOps incorporadas.
- Variedade de linguagens de programação e opções de hospedagem para desenvolvimento, tais como C#, Node.js, Java, JavaScript ou Python.
- O modelo de preços pay-per-use significa que paga apenas pelo tempo gasto a executar o seu código.

**Referências**
- [Portal do Azure](https://portal.azure.com)
- [Documentação das Funções do Azure](/azure/azure-functions/)
- [Guia do programador das Funções do Azure](/azure/azure-functions/functions-reference)
- [Guias de Início Rápido](/azure/azure-functions/functions-create-first-function-vs-code)
- [Amostras](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="azure-app-service"></a>Serviço de Aplicações do Azure
Com [o Azure App Service,](https://azure.microsoft.com/services/app-service/)pode construir e hospedar aplicações web e APIs RESTful na linguagem de programação da sua escolha sem gerir infraestruturas. Oferece autoscaling e alta disponibilidade, suporta tanto o Windows como o Linux, e permite implementações automatizadas a partir de GitHub, Azure DevOps ou qualquer repo Git.

**Principais funcionalidades**
- Suporte linguístico e escamo múltipla para ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP ou Python. Também pode executar o PowerShell e outros scripts ou executáveis como serviços em segundo plano.
- Otimização de DevOps através de integração contínua e implementação com Azure DevOps, GitHub, BitBucket, Docker Hub ou Registo de Contentores Azure. Faça a gestão das suas aplicações no App Service utilizando o Azure PowerShell ou a interface de linha de comandos (CLI) de várias plataformas.
- Escala global com alta disponibilidade para escalar ou sair manualmente ou automaticamente.
- Ligações a plataformas SaaS e dados no local para escolher entre mais de 50 conectores para sistemas empresariais como SAP, serviços SaaS como Salesforce, e serviços de internet como o Facebook. Aceda aos dados no local utilizando ligações híbridas e redes virtuais Azure.
- O Azure App Service é compatível com ISO, SOC e PCI. Autenticar utilizadores com Diretório Ativo Azure ou com sedução para redes sociais como Google, Facebook, Twitter e Microsoft. Crie restrições de endereço IP e faça a gestão de identidades de serviço.
- Modelos de aplicação para escolher entre uma extensa lista de modelos de aplicação no Azure Marketplace, tais como WordPress, Joomla e Drupal.
- A integração do Visual Studio com ferramentas dedicadas no Visual Studio dinamiza o trabalho de criação, implantação e depuragem.

**Referências**
- [Portal do Azure](https://portal.azure.com/)
- [Documentação do Serviço de Aplicações do Azure](/azure/app-service/)
- [Guias de Início Rápido](/azure/app-service/app-service-web-get-started-dotnet)
- [Amostras](/azure/app-service/samples-cli)
- [Tutoriais](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service
[O Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) gere o ambiente de Kubernetes hospedado. A AKS torna-o rápido e fácil de implantar e gerir aplicações contentorizadas sem conhecimentos de orquestração de contentores. Elimina igualmente o fardo das operações e manutenção em curso. Provisões, atualizações e dimensionamento de recursos da AKS a pedido, sem desativar as suas aplicações.

**Principais funcionalidades**
- Migrar facilmente as aplicações existentes para contentores e funcionar dentro de AKS.
- Simplificar a implantação e gestão de aplicações baseadas em microserviços.
- Proteja os DevOps para a AKS para alcançar o equilíbrio entre velocidade e segurança e entregar o código mais rapidamente à escala.
- Dimensione com facilidade utilizando as instâncias do contentor AKS e Azure para o fornecimento de cápsulas dentro de caixas de contentores que começam em segundos.
- Implementar e gerir dispositivos IoT a pedido.
- Treina modelos de aprendizagem automática com o uso de ferramentas como TensorFlow e KubeFlow.

**Referências**
- [Portal do Azure](https://portal.azure.com/)
- [Documentação do Serviço Azure Kubernetes](/azure/aks/)
- [Guias de Início Rápido](/azure/aks/kubernetes-walkthrough-portal)
- [Tutoriais](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances
[Azure Container Instances](https://azure.microsoft.com/services/container-instances/) é uma ótima solução para qualquer cenário que possa operar em recipientes isolados, tais como aplicações simples, automação de tarefas e construção de empregos. Desenvolver aplicativos rapidamente sem gerir VMs.

**Principais funcionalidades**
- Tempos de arranque rápidos como Container Instances podem iniciar recipientes em Azure em segundos, sem a necessidade de provisões e gerenciamento de VMs.
- Conectividade IP pública e nome DNS personalizado.
- Segurança de nível hipervisor que garante que a sua aplicação está tão isolada num recipiente como seria num VM.
- Tamanhos personalizados para uma utilização ótima, permitindo especificações exatas de núcleos e memória de CPU. A faturação é ao segundo e consoante o que precisar, para que possa ajustar os gastos com base nas suas necessidades reais.
- Armazenamento persistente para recuperar e persistir estado. A Container Instances oferece a montagem direta das ações da Azure Files.
- Recipientes Linux e Windows programados com a mesma API.

**Referências**
- [Portal do Azure](https://portal.azure.com/)
- [Documentação do Azure Container Instances](/azure/container-instances/)
- [Guias de Início Rápido](/azure/container-instances/container-instances-quickstart-portal)
- [Amostras](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [Tutoriais](/azure/container-instances/container-instances-tutorial-prepare-app)