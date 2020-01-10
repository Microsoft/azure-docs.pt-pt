---
title: Automatize o ciclo de vida de seus aplicativos com Visual Studio App Center e serviços do Azure
description: Saiba mais sobre os serviços como App Center que ajudam a configurar a compilação e a integração contínuas para seus aplicativos móveis.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 9b0a9e10a88836ce83e636db20180c3692ab4429
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453196"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Automatize o ciclo de vida de seus aplicativos com compilação e integração contínuas

Como desenvolvedores, você escreve o código e o verifica no repositório de código, mas as confirmações verificadas no repositório talvez nem sempre sejam consistentes. Quando vários desenvolvedores trabalham no mesmo projeto, os problemas podem surgir com a integração. As equipes podem se deparar com situações em que as coisas não funcionam, os bugs se acumulam e o desenvolvimento do projeto é atrasado. Os desenvolvedores precisam esperar até que todo o código de software seja criado e testado para verificar se há erros, o que torna o processo lento e menos iterativo. 

Com a compilação e a integração contínuas, os desenvolvedores podem simplificar compilações e testar seu código, confirmando suas alterações no repositório de código-fonte e colocando testes e verificações no ambiente de compilação. Dessa forma, eles estão sempre executando testes em seu código. Todas as alterações feitas no código-fonte são criadas continuamente sempre que houver uma confirmação feita no repositório. Com cada check-in, o servidor de CI (integração contínua) valida e executa qualquer teste criado pelo desenvolvedor. Se os testes não passarem, o código será devolvido para outras alterações. Dessa forma, os desenvolvedores não rompem as compilações criadas. Eles também não precisam executar todos os testes localmente em seus computadores, o que aumenta a produtividade do desenvolvedor. 

## <a name="key-benefits"></a>Principais vantagens
- Automatize suas compilações, testes e implantações para pipelines.
- Detecte bugs e corrija problemas antes de garantir taxas de liberação mais rápidas.
- Confirme o código com mais frequência e crie aplicativos rapidamente.
- Obtenha flexibilidade para alterar o código rapidamente sem problemas.
- Tenha um tempo de colocação no mercado mais rápido para que apenas um bom código de qualidade faça todo o caminho.
- Faça pequenas alterações de código com mais eficiência porque pequenas partes de código são integradas ao mesmo tempo.
- Aumente a transparência e a responsabilidade da equipe para que você obtenha comentários contínuos de seus clientes e da sua equipe.

Use os seguintes serviços para habilitar um pipeline de integração contínua em seus aplicativos móveis.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Build](/appcenter/build/) ajuda você a criar aplicativos nativos e entre plataformas em que sua equipe está trabalhando usando uma infraestrutura de nuvem segura. Você pode conectar facilmente seu repositório no Visual Studio App Center e começar a criar seu aplicativo na nuvem em cada confirmação. Você não precisa se preocupar com a configuração de servidores de compilação localmente, configurações complicadas e código que se baseia na máquina de um colega de colaborador, mas não em seu.

Com o poder adicional dos serviços de Visual Studio App Center, você pode automatizar ainda mais seu fluxo de trabalho. Você pode liberar automaticamente compilações para testadores e lojas de aplicativos públicos com App Center distribuir. Você também pode executar testes de interface do usuário automatizados em milhares de configurações reais de dispositivo e de so na nuvem com App Center teste.

**Principais funcionalidades**
- Configure a integração contínua em minutos e crie aplicativos com mais frequência e rapidez.
- Integre-se ao GitHub, BitBucket, Azure DevOps e GitLab.
- Crie compilações rápidas e seguras em máquinas gerenciadas e hospedadas na nuvem.
- Habilite suas compilações para iniciar o teste e verifique se o aplicativo é compilado em dispositivos Android e iOS reais.
- Obter suporte nativo e entre plataformas para iOS, Android, macOS, Windows, Xamarin e reagir nativos.
- Personalize suas compilações adicionando scripts pós-clonar, pré-compilação e pós-compilação.

**Referências**
- [Inscreva-se com Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Introdução à compilação de App Center](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 [Azure pipelines](https://azure.microsoft.com/services/devops/pipelines/), um serviço no Azure DevOps, é um serviço de CD (integração contínua e entrega contínua) completo que funciona com seu provedor de git preferido. Ele pode implantar na maioria dos principais serviços de nuvem, o que inclui o Azure. Você pode começar com seu código no GitHub, no GitHub Enterprise Server, no GitLab, no bitbucket Cloud ou no Azure Repos. Em seguida, você pode automatizar a compilação, o teste e a implantação do seu código para Microsoft Azure, Google Cloud Platform ou Amazon Web Services (AWS).

**Principais funcionalidades**
- **Experiência simplificada baseada em tarefas para configurar um servidor de CI:** Configure um servidor de CI para aplicativos móveis nativos (Android, iOS e Windows) e entre plataformas (Xamarin, Cordova e reaja nativo), além de tecnologias de servidor baseadas em Microsoft e não Microsoft (Node. js, Java).
- **Qualquer linguagem, plataforma e nuvem:** Crie, teste e implante os aplicativos node. js, Python, Java, PHP, Ruby, go, CC++/ C#,, Android e Ios. Execute em paralelo em Linux, macOS e Windows. Implante em provedores de nuvem como Azure, AWS e Google Cloud Platform. Distribua aplicativos móveis por meio de canais beta e lojas de aplicativos.
- **Suporte a contêiner nativo:** Crie novos contêineres com facilidade e envie-os por push para qualquer registro. Implante contêineres em hosts independentes ou kubernetes.
- **Fluxos de trabalho avançados:** Crie facilmente cadeias de Build e compilações em fases. Obtenha suporte para YAML, integração de testes, liberar Gates, relatórios e muito mais.
- **Extensível:** Use uma variedade de tarefas de compilação, teste e implantação criadas pela Comunidade, que inclui centenas de extensões de margem de atraso para SonarCloud. Você pode até mesmo implantar de outros sistemas de CI, como Jenkins. Os ganchos da Web e as APIs REST podem ajudá-lo a integrar o.
- **Compilações gratuitas hospedadas na nuvem:** Essas compilações estão disponíveis para repositórios públicos e privados.
- **Suporte para implantação em outros fornecedores de nuvem:** Os fornecedores incluem AWS e Google Cloud Platform.

**Referências**
- [Introdução ao guia de Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Introdução ao Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [Inícios rápidos](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

Para ajudá-lo a escolher o serviço certo para as compilações de seu aplicativo, consulte o artigo que compara [app Center Build vs. Azure pipelines](/appcenter/build/choose-between-services).
