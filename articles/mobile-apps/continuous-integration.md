---
title: Automatizar o ciclo de vida das suas apps com o Visual Studio App Center e os serviços Azure
description: Conheça os serviços como o App Center que ajudam a configurar a construção e integração contínuas para as suas aplicações móveis.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 0560f47b832ec2965d9b567e1aeff78baa9c247c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80240930"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Automatizar o ciclo de vida das suas apps com construção e integração contínuas

Como desenvolvedores, escreve código e verifica-o no repositório de códigos, mas os compromissos verificados no repo podem nem sempre ser consistentes. Quando vários desenvolvedores trabalham no mesmo projeto, as questões podem surgir com a integração. As equipas podem encontrar situações em que as coisas não funcionam, os insetos acumulam-se e o desenvolvimento do projeto atrasa-se. Os desenvolvedores têm de esperar até que todo o código de software seja construído e testado para verificar se há erros, o que torna o processo lento e menos iterativo. 

Com a construção e integração contínuas, os desenvolvedores podem simplificar as construções e testar o seu código, comprometendo as suas alterações ao repositório de código fonte e colocando testes e verificações no ambiente de construção. Desta forma, estão sempre a fazer testes contra o código. Todas as alterações feitas ao código fonte são construídas continuamente sempre que há um compromisso feito para o repositório. A cada check-in, o servidor de integração contínua (CI) valida e executa qualquer teste que o desenvolvedor criou. Se os testes não passarem, o código é enviado para novas alterações. Desta forma, os desenvolvedores não quebram as construções que são criadas. Também não têm de fazer todos os testes localmente nos seus computadores, o que aumenta a produtividade dos desenvolvedores. 

## <a name="key-benefits"></a>Principais vantagens
- Automatizar as suas construções, testes e implantações para oleodutos.
- Detete bugs e corrija problemas mais cedo para garantir taxas de libertação mais rápidas.
- Comprometa código com mais frequência e construa aplicações rapidamente.
- Obtenha flexibilidade para mudar de código rapidamente sem qualquer problema.
- Ganhe tempo-a-mercado mais rápido para que apenas um código de boa qualidade o faça até ao fim.
- Faça pequenas alterações de código de forma mais eficiente porque pequenos pedaços de código são integrados ao mesmo tempo.
- Aumente a transparência e a prestação de contas da equipa para que obtenha feedback contínuo dos seus clientes e da sua equipa.

Utilize os seguintes serviços para permitir um pipeline de integração contínua nas suas aplicações móveis.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[O App Center Build](/appcenter/build/) ajuda-o a construir aplicações nativas e transplataformas em que a sua equipa está a trabalhar utilizando uma infraestrutura de nuvem segura. Pode ligar facilmente o seu repo no Visual Studio App Center e começar a construir a sua aplicação na nuvem em cada compromisso. Não tem que se preocupar em configurar servidores de construção localmente, configurações complicadas e código que se baseia na máquina de um colega de trabalho, mas não na sua.

Com o poder adicional dos serviços do Visual Studio App Center, pode automatizar ainda mais o seu fluxo de trabalho. Pode lançar automaticamente construções para testadores e lojas de aplicações públicas com O App Center Distribute. Também pode executar testes automatizados de UI em milhares de configurações de dispositivos reais e SNa na nuvem com App Center Test.

**Principais funcionalidades**
- Crie uma integração contínua em minutos e construa aplicações com mais frequência e mais rápido.
- Integre com gitHub, BitBucket, Azure DevOps e GitLab.
- Crie construções rápidas e seguras em máquinas geridas e hospedadas em nuvem.
- Ative as suas construções para lançar o teste e verifique se a aplicação se constrói em dispositivos iOS e Android no mundo real.
- Ganhe suporte nativo e cross-platform para iOS, Android, macOS, Windows, Xamarin e React Native.
- Personalize as suas construções adicionando scripts pós-clone, pré-construção e pós-construção.

**Referências**
- [Inscreva-se no Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Começar com app center build](/appcenter/build/)

## <a name="azure-pipelines"></a>Pipelines do Azure
 [A Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/), um serviço em Azure DevOps, é um serviço de integração contínua e entrega contínua (CD) que trabalha com o seu fornecedor Git preferido. Pode implantar-se na maioria dos principais serviços de nuvem, que inclui o Azure. Pode começar com o seu código no GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud ou Azure Repos. Em seguida, pode automatizar a construção, teste e implementação do seu código para Microsoft Azure, Google Cloud Platform ou Amazon Web Services (AWS).

**Principais funcionalidades**
- **Experiência simplificada baseada em tarefas para a criação de um servidor CI:** Criar um servidor CI para aplicações móveis nativas (Android, iOS e Windows) e cross-platform (Xamarin, Cordova e React Native), além das tecnologias de servidores baseadas na Microsoft e não-Microsoft (Node.js, Java).
- **Qualquer idioma, plataforma e nuvem:** Construir, testar e implementar aplicações Node.js, Python, Java, PHP, Ruby, Go, C/C++, C#, Android e iOS. Corra em paralelo em Linux, macOS e Windows. Implemente para fornecedores de nuvem como Azure, AWS e Google Cloud Platform. Distribua aplicações móveis através de canais beta e lojas de aplicações.
- **Suporte de contentores nativos:** Crie novos recipientes com facilidade e empurre-os para qualquer registo. Desloque os recipientes para anfitriões independentes ou Kubernetes.
- **Fluxos de trabalho avançados:** Criar facilmente cadeias de construção e construções multifases. Obtenha suporte para YAML, integração de testes, portas de lançamento, reportagens e muito mais.
- **Extensível:** Utilize uma gama de tarefas de construção, teste e implementação construídas pela comunidade, que inclui centenas de extensões de Slack a SonarCloud. Podeaté implantar-se noutros sistemas de CI, como o Jenkins. Os ganchos web e ASAP REST podem ajudá-lo a integrar.
- **Construções gratuitas hospedadas em nuvem:** Estas construções estão disponíveis para repositórios públicos e privados.
- **Suporte para implantação a outros fornecedores** de nuvem: Os fornecedores incluem AWS e Google Cloud Platform.

**Referências**
- [Começar com o guia azure pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Introdução ao Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [Guias de Início Rápido](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

Para ajudá-lo a escolher o serviço certo para as construções da sua aplicação, consulte o artigo que compara o [App Center Build vs. Azure Pipelines](/appcenter/build/choose-between-services).
