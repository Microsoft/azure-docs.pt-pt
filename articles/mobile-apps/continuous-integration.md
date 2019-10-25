---
title: Automatize o ciclo de vida de seus aplicativos com Visual Studio App Center e serviços do Azure
description: Saiba mais sobre os serviços como App Center que ajudam a configurar a compilação e a integração contínuas para seus aplicativos móveis.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 07e8e0edc4638c6c0bf9acc205175aa731c0f8bc
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795552"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Automatize o ciclo de vida de seus aplicativos com compilação e integração contínuas

Como desenvolvedores, você escreve o código e o verifica no repositório de código, mas as confirmações verificadas no repositório talvez nem sempre sejam consistentes. Com vários desenvolvedores trabalhando no mesmo projeto, os problemas surgem com a integração e a equipe estaria em situações em que as coisas não estão funcionando, os bugs continuam empilhandondo e o desenvolvimento do projeto é atrasado. Os desenvolvedores precisam esperar até que todo o código de software seja criado e testado para verificar se há erros e isso torna o processo lento e menos iterativo. 

Com a **compilação e a integração contínuas**, os desenvolvedores podem simplificar a compilação e o teste de seu código, não apenas confirmando suas alterações no repositório de código-fonte, mas também colocando testes e verificações no ambiente de compilação para que estejam sempre em execução testes em seu código. Todas as alterações feitas no código-fonte são compiladas continuamente sempre que houver uma confirmação feita no repositório. Com cada check-in, o servidor de CI valida e executa qualquer teste criado pelo desenvolvedor. Se os testes não passarem, o código será devolvido para outras alterações. Isso permite que o desenvolvedor não interrompa as compilações que são criadas, bem como não executar todos os testes localmente em seu computador, o que, por sua vez, aumenta a produtividade do desenvolvedor. 

## <a name="key-benefits"></a>Principais vantagens
- Pipeline **automatizado** de compilação, teste e implantação.
- **Detecte bugs e corrija problemas** antes de garantir taxas de liberação mais rápidas.
- **Confirme o código** com mais frequência e crie aplicativos rapidamente.
- **Flexibilidade** para alterar o código rapidamente sem problemas.
- O **tempo de colocação no mercado mais rápido** garante que apenas um bom código de qualidade faça todo o caminho.
- **Alterações de código pequenas** , pois permitem integrar pequenas partes de código ao mesmo tempo.
- **Aumentar a transparência e a responsabilidade da equipe** permite que você obtenha **comentários contínuos** não apenas de seus clientes, mas também de sua equipe.

Use os seguintes serviços para habilitar o pipeline de integração contínua em seus aplicativos móveis.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center](/appcenter/build/) serviço de compilação ajuda a criar aplicativos nativos e entre plataformas em que sua equipe está trabalhando, usando uma infraestrutura de nuvem segura. Você pode conectar facilmente seu repositório no App Center e começar a criar seu aplicativo na nuvem em todas as confirmações sem precisar se preocupar com a configuração de servidores de compilação localmente, configurações complicadas e código que se baseia na máquina de um colega, mas não em seu.

Com o poder adicional dos serviços de App Center, você pode automatizar ainda mais seu fluxo de trabalho. Libere automaticamente compilações para testadores e lojas de aplicativos públicos com App Center distribuir ou executar testes de interface do usuário automatizados em milhares de configurações reais de dispositivos e de sistemas operacionais na nuvem com App Center teste.

**Principais recursos**
- **Configure a integração contínua** em minutos e crie aplicativos com mais frequência e rapidez.
- Integre-se ao **GitHub, BitBucket, Azure DevOps e GitLab**.
- **Compilações rápidas e seguras** em máquinas gerenciadas e hospedadas em nuvem.
- **Habilite suas compilações para "Iniciar teste"** e verifique se o aplicativo é compilado em dispositivos Android e Ios reais.
- **Suporte nativo e de plataforma cruzada** -Ios, Android, MacOS, Windows, Xamarin, reagir nativo.
- **Personalize suas compilações** adicionando scripts pós-clonar, pré-compilação e pós-compilação.

**Referências**
- [Inscreva-se com App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Introdução à compilação de App Center](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 [Azure pipelines](https://azure.microsoft.com/services/devops/pipelines/), um serviço no Azure DevOps, é um serviço de CI (integração contínua) e distribuição contínua (CD) que funciona com seu provedor de git preferido e pode ser implantado na maioria dos principais serviços de nuvem, incluindo o Azure. Você pode começar com seu código no GitHub, no GitHub Enterprise Server, no GitLab, no bitbucket Cloud ou no Azure Repos. Em seguida, você pode automatizar a compilação, os testes e a implantação de seu código para Microsoft Azure, Google Cloud Platform ou Amazon Web Services.

**Principais recursos**
- Experiência simplificada baseada em tarefas para configurar um servidor de CI para aplicativos móveis nativos (Android, iOS e Windows) e entre plataformas (Xamarin, Cordova e reagir nativos), além da Microsoft e de um servidor baseado em Microsoft (Node. js, Java) as.
- **Qualquer linguagem, plataforma e nuvem** – compilem, testem e implantem os aplicativos node. js, Python, Java, PHP, Ruby, goC++, C#C/,, Android e Ios. Execute em paralelo em Linux, macOS e Windows. Implante em provedores de nuvem como Azure, AWS e GCP. Distribua aplicativos móveis por meio de canais beta e lojas de aplicativos.
- **Suporte a contêiner nativo** -crie novos contêineres com facilidade e envie-os por push para qualquer registro. Implante contêineres em hosts independentes ou kubernetes.
- **Fluxos de trabalho avançados** – cadeia de Build fácil e compilações com várias fases. Suporte para YAML, integração de teste, lançamento de entradas, relatórios e muito mais.
- **Extensível** – use uma variedade de tarefas de compilação, teste e implantação criadas pela Comunidade – centenas de extensões da margem de atraso para SonarCloud. Você pode até mesmo implantar de outros sistemas de CI, como Jenkins, e usar WebHooks e APIs REST para ajudá-lo a se integrar
- **Compilações gratuitas hospedadas na nuvem** para repositórios públicos e privados.
- **Dá suporte à implantação em outros fornecedores de nuvem** , como Amazon Web Services, Google Cloud Platform etc.

**Referências**
- [Introdução ao guia de Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Introdução ao Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [Inícios rápidos](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

Para escolher o serviço certo para Builds do seu aplicativo, siga o artigo que compara [app Center Build vs. Azure pipelines](/appcenter/build/choose-between-services).
