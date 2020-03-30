---
title: Automatizar a implementação e lançamento das suas aplicações móveis com o Visual Studio App Center e os serviços Azure
description: Conheça os serviços como o App Center que ajudam a configurar o gasoduto de entrega contínua para as suas aplicações móveis.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: ccfd0fc0450a6fbd34192dce7e375fe2de7f47c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235344"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>Automatizar a implementação e libertação das suas aplicações móveis com serviços de entrega contínua

Como desenvolvedores, escreve código e verifica-o no repositório de códigos, mas os compromissos verificados no repo podem nem sempre ser consistentes. Quando vários desenvolvedores trabalham no mesmo projeto, as questões podem surgir com a integração. As equipas podem encontrar situações em que as coisas não funcionam, os insetos acumulam-se e o desenvolvimento do projeto atrasa-se. Os desenvolvedores têm de esperar até que todo o código de software seja construído e testado para verificar se há erros, o que torna o processo lento e menos iterativo.

Com a entrega contínua, automatiza a implementação e libertação das suas aplicações móveis. Não importa se está a distribuir a aplicação a um grupo de testadores ou funcionários da empresa (para testes beta) ou a uma loja de aplicações (para produção). A entrega contínua torna as implementações menos arriscadas e encoraja as iterações rápidas. Também pode lançar novas alterações aos seus clientes de forma contínua.

## <a name="distribute-application-binaries-to-beta-testers"></a>Distribuir binários de aplicação para testadores beta
O teste beta da sua aplicação móvel é uma das etapas críticas durante o processo de desenvolvimento da aplicação. Ajuda a encontrar bugs e problemas na sua aplicação desde cedo. O feedback melhora a qualidade da sua aplicação quando está a prepará-la para uso de produção.

Utilize os seguintes serviços para permitir um gasoduto de entrega contínuo nas suas aplicações móveis.

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[O App Center Distribute](/appcenter/distribution/) é uma ferramenta para os desenvolvedores lançarem rapidamente as construções para dispositivos. Com uma experiência de portal de instalação completa, o App Center Distribute é uma solução poderosa para a distribuição de tester de aplicações beta. É também uma alternativa conveniente à distribuição através de lojas de aplicações públicas. Os desenvolvedores podem automatizar ainda mais o seu fluxo de trabalho de distribuição com a App Center Build e integrações de lojas de aplicações públicas.

**Principais funcionalidades**
- Distribua a sua aplicação a testadores beta e utilizadores e certifique-se de que todos os seus testadores estão na versão mais recente da sua aplicação.
- Notifique os testadores de novas versões sem que os testadores passem pelo fluxo de descarregamento novamente.
- Gerencie grupos de distribuição para diferentes versões da sua aplicação.
- Distribuir para lojas: 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- Ganhe suporte de plataforma para iOS, Android, macOS, tvOS, Xamarin, React Native, Unity e Cordova.
- Registe automaticamente os dispositivos iOS no seu perfil de provisionamento.

**Referências**
- [Inscreva-se no Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Começar com app center Distribute](/appcenter/build/)

### <a name="azure-pipelines"></a>Pipelines do Azure

[A Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) é um serviço de integração contínua (CI) e entrega contínua (CD) que trabalha com o seu fornecedor Git preferido. Os oleodutos Azure podem ser implantados na maioria dos principais serviços de nuvem, como os serviços Azure. Pode começar com o seu código no GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud ou Azure Repos. Em seguida, pode automatizar a construção, teste e implementação do seu código para Microsoft Azure, Google Cloud Platform ou Amazon Web Services (AWS).

**Principais funcionalidades**
- **Experiência simplificada baseada em tarefas para a criação de um servidor CI:** Criar um servidor CI para aplicações móveis nativas (Android, iOS e Windows) e cross-platform (Xamarin, Cordova e React Native).
- **Qualquer idioma, plataforma e nuvem:** Construir, testar e implementar aplicações Node.js, Python, Java, PHP, Ruby, Go, C/C++, C#, Android e iOS. Corra em paralelo em Linux, macOS e Windows. Implemente para fornecedores de nuvem como Azure, AWS e Google Cloud Platform. Distribua aplicações móveis através de canais beta e lojas de aplicações.
- **Suporte de contentores nativos:** Crie novos recipientes com facilidade e empurre-os para qualquer registo. Desloque os recipientes para anfitriões independentes ou Kubernetes.
- **Fluxos de trabalho avançados e características:** Criar facilmente cadeias de construção e construções multifases. Obtenha suporte para YAML, integração de testes, portas de lançamento, reportagens e muito mais.
- **Extensível:** Utilize uma gama de tarefas de construção, teste e implementação construídas pela comunidade, que inclui centenas de extensões de Slack a SonarCloud. Podeaté implantar-se noutros sistemas de CI, como o Jenkins. Os ganchos web e ASAP REST podem ajudá-lo a integrar.
- **Construções gratuitas hospedadas em nuvem:** Estas construções estão disponíveis para repositórios públicos e privados.
- **Suporte para implantação a outros fornecedores** de nuvem: Os fornecedores incluem AWS e Google Cloud Platform.

**Referências**
- [Começar com o guia azure pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Introdução ao Azure DevOps](https://app.vsaex.visualstudio.com/signup/)
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Distribua a sua aplicação diretamente para as Lojas de Aplicações
Depois de a sua aplicação estar pronta para uso de produção e pretender que seja usada publicamente, tem de ser submetida a lojas de aplicações onde pode ser descarregada pelos clientes. Existem várias formas de distribuir a sua aplicação diretamente para lojas de aplicações. 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
Com o [App Center Distribute,](/appcenter/distribution/stores/)pode publicar as suas aplicações móveis diretamente nas lojas de aplicações. Depois de a sua aplicação estar pronta para ser descarregada pelos utilizadores, pode publicar os binários da sua aplicação diretamente a partir do portal Visual Studio App Center. 

Pode distribuir diretamente para:
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Google Play Store](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
Na loja de aplicações desenvolvida e mantida pela Apple, os utilizadores podem navegar e descarregar aplicações desenvolvidas para dispositivos iOS, MacOS, WatchOS e tvOS. Os desenvolvedores precisam de submeter as suas aplicações iOS à Apple App Store para uso público.

### <a name="google-play"></a>Google Play

O Google Play é a loja oficial de aplicações para O Sistema Operativo Android, onde os utilizadores podem navegar e descarregar aplicações desenvolvidas para dispositivos Android que são publicadas através da Google.

### <a name="intune"></a>Intune

O [Microsoft Intune](/intune/app-management) é um serviço baseado na nuvem no espaço de gestão da mobilidade empresarial que ajuda a permitir que a sua força de trabalho seja produtiva, mantendo os seus dados corporativos protegidos. Com o Intune, pode:
- Gerencie os dispositivos móveis e computadores que a sua força de trabalho utiliza para aceder aos dados da empresa.
- Gerencie as aplicações móveis que a sua força de trabalho utiliza.
- Proteja as informações da sua empresa controlando a forma como a sua força de trabalho acede e partilha.
- Certifique-se de que os dispositivos e aplicações estão em conformidade com os requisitos de segurança da empresa.
    
## <a name="deploy-updates-directly-to-users-devices"></a>Implementar atualizações diretamente para os dispositivos dos utilizadores

### <a name="codepush"></a>CodePush
Com o [CodePush](/appcenter/distribution/codepush/) no App Center, os desenvolvedores Apache Cordova e React Native podem implementar atualizações de aplicações móveis diretamente para os dispositivos dos seus utilizadores. Funciona como um repositório central para o quais os desenvolvedores podem publicar determinadas atualizações, tais como JavaScript, HTML, CSS e alterações de imagem. Em seguida, as aplicações podem consultar atualizações do repositório utilizando os SDKs fornecidos pelo cliente. Desta forma, pode ter um modelo de envolvimento mais determinista e direto com os seus utilizadores enquanto aborda bugs ou adiciona pequenas funcionalidades. Não é obrigado a reconstruir um binário ou a redistribuí-lo através de nenhuma loja pública de aplicações.

**Principais funcionalidades**
- Os desenvolvedores da Cordova e da React Native podem implementar atualizações de aplicações móveis diretamente para os dispositivos dos seus utilizadores sem lançar em uma loja.
- Útil para corrigir bugs ou adicionar e remover pequenas funcionalidades que não exijam que reconstruíre binário e redistribua-o através das respetivas lojas.

**Referências**
- [Inscreva-se no Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Começar com codePush no App Center](/appcenter/distribution/codepush/)
- [CodePush CLI](/appcenter/distribution/codepush/cli)