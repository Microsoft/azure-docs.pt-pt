---
title: Automatize o ciclo de vida das suas apps com o Visual Studio App Center e os serviços Azure
description: Conheça os serviços como o App Center que ajudam a criar e integração contínuas para as suas aplicações móveis.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 54103ad999d3e310d53e84f29d41aad733abfe93
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450879"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Automatizar o ciclo de vida das suas apps com construção e integração contínuas

Como desenvolvedores, você escreve código e verifica-o no repositório de código, mas os compromissos verificados no repo podem nem sempre ser consistentes. Quando vários desenvolvedores trabalham no mesmo projeto, as questões podem surgir com integração. As equipas podem encontrar situações em que as coisas não funcionam, os insetos acumulam-se e o desenvolvimento do projeto é adiado. Os desenvolvedores têm que esperar até que todo o código de software seja construído e testado para verificar se há erros, o que torna o processo lento e menos iterativo. 

Com a construção e integração contínuas, os desenvolvedores podem simplificar as construções e testar o seu código, comprometendo as suas alterações ao repositório de código fonte e colocando testes e verificações no ambiente de construção. Desta forma, estão sempre a fazer testes contra o código. Todas as alterações feitas ao código fonte são construídas continuamente sempre que há um compromisso feito com o repositório. Com cada check-in, o servidor de integração contínua (CI) valida e executa qualquer teste que o desenvolvedor criou. Se os testes não passarem, o código é enviado de volta para mais alterações. Desta forma, os desenvolvedores não quebram as construções que são criadas. Também não têm de fazer todos os testes localmente nos seus computadores, o que aumenta a produtividade dos desenvolvedores. 

## <a name="key-benefits"></a>Principais vantagens
- Automatize as suas construções, testes e implementações para oleodutos.
- Detete bugs e corrija problemas precocemente para garantir taxas de libertação mais rápidas.
- Comprometa o código com mais frequência e construa aplicações rapidamente.
- Obtenha flexibilidade para alterar código rapidamente sem qualquer problema.
- Ganhe tempo mais rápido para o mercado para que apenas um código de boa qualidade chegue até ao fim.
- Faça pequenas alterações de código de forma mais eficiente porque pequenos pedaços de código são integrados ao mesmo tempo.
- Aumente a transparência e a responsabilidade da equipa para obter feedback contínuo dos seus clientes e da sua equipa.

Utilize os seguintes serviços para permitir um gasoduto de integração contínua nas suas aplicações móveis.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[O App Center Build](/appcenter/build/) ajuda-o a construir aplicações nativas e inter-plataformas em que a sua equipa está a trabalhar utilizando uma infraestrutura de nuvem segura. Pode ligar facilmente o seu repo no Visual Studio App Center e começar a construir a sua aplicação na nuvem em cada compromisso. Não tens de te preocupar em configurar servidores de construção localmente, configurações complicadas e código que se baseia na máquina de um colega de trabalho, mas não no teu.

Com o poder adicional dos serviços do Visual Studio App Center, pode automatizar ainda mais o seu fluxo de trabalho. Pode lançar automaticamente edifícios para testadores e lojas de aplicações públicas com App Center Distribute. Também pode realizar testes automatizados de UI em milhares de configurações reais de dispositivos e SO na nuvem com o App Center Test.

**Principais funcionalidades**
- Configurar a integração contínua em minutos e construir aplicações com mais frequência e mais rápido.
- Integre com GitHub, BitBucket, Azure DevOps e GitLab.
- Crie construções rápidas e seguras em máquinas geridas e hospedadas em nuvem.
- Possibilite as suas construções para lançar o teste e verifique se a aplicação se constrói em dispositivos iOS e Android no mundo real.
- Ganhe suporte nativo e transversal para iOS, Android, macOS, Windows, Xamarin e React Native.
- Personalize as suas construções adicionando scripts pós-clones, pré-construídos e pós-construção.

**Referências**
- [Inscreva-se no Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Começar com a Criação do Centro de Aplicações](/appcenter/build/)

## <a name="azure-pipelines"></a>Pipelines do Azure
 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/), um serviço em Azure DevOps, é um serviço de integração contínua e entrega contínua (CD) totalmente apresentado que funciona com o seu fornecedor de Git preferido. Pode implantar-se na maioria dos principais serviços em nuvem, que inclui o Azure. Pode começar com o seu código no GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud ou Azure Repos. Em seguida, pode automatizar a construção, teste e implementação do seu código para o Microsoft Azure, Google Cloud Platform ou Amazon Web Services (AWS).

**Principais funcionalidades**
- **Experiência simplificada baseada em tarefas para configurar um servidor CI:** Crie um servidor CI para aplicações móveis nativas (Android, iOS e Windows) e cross-platform (Xamarin, Cordova e React Native), além das tecnologias de servidores baseadas na Microsoft e não-Microsoft (Node.js, Java).
- **Qualquer idioma, plataforma e nuvem:** Construa, teste e implemente aplicações Node.js, Python, Java, PHP, Ruby, Go, C/C++, C#, Android e iOS. Corra em paralelo em Linux, macOS e Windows. Implemente para fornecedores de nuvem como Azure, AWS e Google Cloud Platform. Distribuir aplicações móveis através de canais beta e lojas de aplicações.
- **Suporte a contentores nativos:** Crie novos recipientes com facilidade e empurre-os para qualquer registo. Desdobre recipientes para hospedeiros independentes ou Kubernetes.
- **Fluxos de trabalho avançados:** Criar facilmente cadeias de construção e construções multifascadas. Obtenha apoio para YAML, integração de testes, portas de libertação, relatórios e muito mais.
- **Extensível:** Use uma gama de tarefas de construção, teste e implementação construídas pela comunidade, que inclui centenas de extensões de Slack para SonarCloud. Podes até implantar-te noutros sistemas de TI, como o Jenkins. Os ganchos web e as APIs REST podem ajudá-lo a integrar-se.
- **Construções gratuitas com nuvens:** Estas construções estão disponíveis para repositórios públicos e privados.
- **Suporte para implantação a outros fornecedores de nuvem:** Os fornecedores incluem AWS e Google Cloud Platform.

**Referências**
- [Começa com o guia Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Introdução ao Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [Guias de Início Rápido](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

Para ajudá-lo a escolher o serviço certo para as construções da sua aplicação, consulte o artigo que compara [o App Center Build vs. Azure Pipelines](/appcenter/build/choose-between-services).
