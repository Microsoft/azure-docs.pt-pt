---
title: Criar back-end de aplicativo móvel sem servidor com Azure Functions e outros serviços
description: Saiba mais sobre os serviços de computação para criar um back-end de aplicativo móvel sem servidor e sólido.
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: b8378be675b86ada10b8442c40a54b635ee115a9
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795864"
---
# <a name="build-mobile-backend-components-with-compute-services"></a>Criar componentes de back-end móvel com serviços de computação
Cada aplicativo móvel precisa de um back-end responsável pelo armazenamento de dados, pela lógica de negócios e pela segurança. O gerenciamento da infraestrutura para hospedar e executar o código de back-end exige que você dimensione, provisione e dimensione vários servidores, gerencie atualizações do sistema operacional e hardware envolvidos, aplique patches de segurança e, em seguida, monitore todos esses componentes de infraestrutura para desempenho, disponibilidade e tolerância a falhas. Isso ocorre quando a arquitetura sem servidor é útil, pois os desenvolvedores não têm servidores para gerenciar, nenhum sistema operacional ou atualizações de software/hardware relacionadas a serem gerenciadas. Ele economiza muito tempo de desenvolvedor e custo, o que significa tempo de colocação no mercado mais rápido e energia concentrada na criação de aplicativos.

## <a name="benefits-of-compute"></a>Benefícios da computação
- Abstração de servidores: não há necessidade de se preocupar com hospedagem, aplicação de patches e segurança, permitindo que os desenvolvedores se concentrem apenas no código.
- O dimensionamento imediato e eficiente garante que os recursos sejam provisionados automaticamente ou sob demanda em qualquer escala necessária.
- Alta disponibilidade e tolerância a falhas.
- O micro billing garante que você seja cobrado apenas quando seu código estiver realmente em execução.
- Escreva o código que é executado na nuvem no idioma de sua escolha.

Use os seguintes serviços para habilitar recursos de computação sem servidor em seus aplicativos móveis.

## <a name="azure-functions"></a>Funções do Azure
[Azure Functions](https://azure.microsoft.com/services/functions/) é uma experiência de computação orientada por eventos que permite executar seu código, escrito na linguagem de programação de sua escolha, sem se preocupar com os servidores. Os desenvolvedores não precisam gerenciar o aplicativo nem a infraestrutura para executá-lo. O Functions é dimensionado sob demanda e você paga apenas pela hora em que seu código é executado. Azure Functions são uma ótima maneira de implementar uma API para um aplicativo móvel, pois elas são muito fáceis de implementar e manter e podem ser acessadas por meio de HTTP.

**Principais recursos**
- **Orientado a eventos e escalonável** , em que os desenvolvedores podem usar **gatilhos e associações** para definir quando uma função é invocada e para quais dados ele se conecta.
- **Traga as suas próprias dependências** - As funções são compatíveis com NuGet e NPM, pelo que pode utilizar as suas bibliotecas favoritas.
- A **segurança integrada** permite que você proteja funções disparadas por http com provedores OAuth, como Azure Active Directory, Facebook, Google, Twitter e conta da Microsoft.
- **Integração simplificada** com diferentes [Serviços do Azure](/azure/azure-functions/functions-overview#integrations) e ofertas de software como serviço (SaaS).
- O **desenvolvimento flexível** permite codificar suas funções diretamente no portal ou configurar a integração contínua e implantar seu código por meio do GitHub, Azure DevOps Services e outras ferramentas de desenvolvimento com suporte.
- O tempo de execução do Functions é de **código-fonte aberto** e está disponível no [GitHub](https://github.com/azure/azure-webjobs-sdk-script).
-  **Experiência de desenvolvimento aprimorada** em que os desenvolvedores podem codificar, testar e depurar localmente usando seu editor preferido ou uma interface da Web fácil de usar com monitoramento com ferramentas integradas e recursos internos de DevOps.
- **Variedade de linguagens de programação e opções** de hospedagem C#– desenvolva usando, Node. js, Java, JavaScript ou Python.
- **Modelo de preços de pagamento por utilização** - Pague apenas o tempo despendido a executar o seu código.

**Referências**
- [Portal do Azure](https://portal.azure.com)
- [Sobre](/azure/azure-functions/)
- [Guia do desenvolvedor do Azure Functions](/azure/azure-functions/functions-reference)
- [Inícios rápidos](/azure/azure-functions/functions-create-first-function-vs-code)
- [Amostras](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="app-service"></a>Serviço de Aplicações
[Serviço de Azure app](https://azure.microsoft.com/services/app-service/) Azure App serviço permite que você crie e hospede aplicativos Web e APIs RESTful na linguagem de programação de sua escolha sem gerenciar a infraestrutura. Este serviço garante dimensionamento automático e elevada disponibilidade, suporta Windows e Linux e permite implementações automáticas a partir do GitHub, DevOps do Azure ou de qualquer repositório de Git.

**Principais recursos**
- Suporte a **várias linguagens e estruturas** para ASP.NET, ASP.NET Core, Java, Ruby, Node. js, php ou Python. Você também pode executar o PowerShell e outros scripts ou executáveis como serviços em segundo plano.
- **Otimização de DevOps** – configure a integração e a implantação contínuas com o Azure DevOps, o GitHub, o BitBucket, o Hub do Docker ou o registro de contêiner do Azure. Gerencie seus aplicativos no serviço de aplicativo usando Azure PowerShell ou a CLI (interface de linha de comando) de plataforma cruzada.
- **Escala global com alta disponibilidade** para escalar verticalmente ou horizontalmente de forma manual ou automática.
- **Conexões com plataformas SaaS e dados locais** para escolher entre mais de 50 conectores para sistemas empresariais (como SAP), serviços de SaaS (como Salesforce) e serviços de Internet (como o Facebook). Acesse dados locais usando Conexões Híbridas e redes virtuais do Azure.
- **Segurança e conformidade** – o serviço de Azure app é compatível com ISO, SoC e PCI. Autentique usuários com Azure Active Directory ou com logon social (Google, Facebook, Twitter e Microsoft). Criar restrições de endereço IP e gerenciar identidades de serviço.
- **Modelos de aplicativos** a serem escolhidos em uma lista extensa de modelos de aplicativos no Azure Marketplace, como WordPress, Joomla e Drupal.
- A **integração do Visual Studio** com ferramentas dedicadas no Visual Studio simplifica o trabalho de criação, implantação e depuração.

**Referências**
- [Portal do Azure](https://portal.azure.com/)
- [Sobre](/azure/app-service/)
- [Inícios rápidos](/azure/app-service/app-service-web-get-started-dotnet)
- [Amostras](/azure/app-service/samples-cli)
- [Tutoriais](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service-aks"></a>Serviço Kubernetes do Azure (AKS)
O [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) faz a gestão do seu ambiente alojado do Kubernetes, permitindo implementar e gerir rápida e facilmente aplicações contentorizadas sem que sejam necessários conhecimentos em termos de orquestração de contentores. Também põe fim às tarefas de operações e manutenções contínuas ao aprovisionar, atualizar e dimensionar recursos a pedido, sem que as aplicações sejam colocadas offline. 

**Principais recursos**
- **Migre facilmente aplicativos existentes** para contêineres e execute em AKs.
- **Simplifique a implantação e o gerenciamento** de aplicativos baseados em microserviços.
- **Proteja o DevOps para AKs** a fim de obter o equilíbrio entre velocidade e segurança e fornecer código mais rápido em escala.
- **Dimensione com facilidade usando AKs e ACI** para provisionar pods dentro de ACI que começam em segundos.
- **Implantação e gerenciamento de dispositivos IOT** sob demanda.
- **Treinamento de modelo do Machine Learning** com uso de ferramentas como TensorFlow e KubeFlow.

**Referências**
- [Portal do Azure](https://portal.azure.com/)
- [Sobre](/azure/aks/)
- [Inícios rápidos](/azure/aks/kubernetes-walkthrough-portal)
- [Tutoriais](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances
As [ACI (instâncias de contêiner do Azure)](https://azure.microsoft.com/services/container-instances/) são uma ótima solução para qualquer cenário que possa operar em contêineres isolados, incluindo aplicativos simples, automação de tarefas e trabalhos de compilação. Desenvolva aplicativos rapidamente sem gerenciar VMs.

**Principais recursos**
- Os **tempos de inicialização rápidos** como ACI podem iniciar contêineres no Azure em segundos, sem a necessidade de provisionar e gerenciar VMS.
- **Conectividade IP pública e nome DNS personalizado**.
- A **segurança em nível de hipervisor** que garante que seu aplicativo seja isolada em um contêiner, como seria em uma VM.
- **Tamanhos personalizados** para a utilização ideal, permitindo especificações exatas de núcleos de CPU e memória. A faturação é ao segundo e consoante o que precisar, para que possa ajustar os gastos com base nas suas necessidades reais.
- **Armazenamento persistente** para recuperar e persistir o estado, o ACI oferece a montagem direta de compartilhamentos de arquivos do Azure.
- **Contêineres do Linux e do Windows** agendados com a mesma API.

**Referências**
- [Portal do Azure](https://portal.azure.com/)
- [Sobre](/azure/container-instances/)
- [Inícios rápidos](/azure/container-instances/container-instances-quickstart-portal)
- [Amostras](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [Tutoriais](/azure/container-instances/container-instances-tutorial-prepare-app)