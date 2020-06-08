---
title: Automatize a implementação e lançamento das suas aplicações móveis com o Visual Studio App Center e os serviços Azure
description: Conheça os serviços, como o App Center, que ajudam a configurar o pipeline de entrega contínua para as suas aplicações móveis.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/05/2020
ms.author: masoucou
ms.openlocfilehash: ed268bf2be69aece8a5f7e51c25ef0ff539e8c7e
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/07/2020
ms.locfileid: "84483130"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>Automatizar a implantação e libertação das suas aplicações móveis com serviços de entrega contínua

Como desenvolvedores, você escreve código e verifica-o no repositório de código, mas os compromissos verificados no repo podem nem sempre ser consistentes. Quando vários desenvolvedores trabalham no mesmo projeto, as questões podem surgir com integração. As equipas podem encontrar situações em que as coisas não funcionam, os insetos acumulam-se e o desenvolvimento do projeto é adiado. Os desenvolvedores têm que esperar até que todo o código de software seja construído e testado para verificar se há erros, o que torna o processo lento e menos iterativo.

Com a entrega contínua, automatiza a implementação e libertação das suas aplicações móveis. Não importa se está a distribuir a aplicação a um grupo de testadores ou funcionários da empresa (para testes beta) ou a uma loja de aplicações (para produção). A entrega contínua torna as implementações menos arriscadas e encoraja iterações rápidas. Também pode lançar novas alterações aos seus clientes de forma contínua.

## <a name="distribute-application-binaries-to-beta-testers"></a>Distribuir binários de aplicação a testadores beta
O teste beta da sua aplicação móvel é um dos passos críticos durante o processo de desenvolvimento da aplicação. Ajuda a encontrar bugs e problemas na sua aplicação desde o início. O feedback melhora a qualidade da sua aplicação quando está a preparem-no para uso de produção.

Utilize os seguintes serviços para ativar um gasoduto de entrega contínua nas suas aplicações móveis.

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Distribute](/appcenter/distribution/) é uma ferramenta para os desenvolvedores lançarem rapidamente as construções para os dispositivos. Com uma experiência completa no portal de instalação, o App Center Distribute é uma solução poderosa para a distribuição de testes de aplicações beta. É também uma alternativa conveniente à distribuição através de lojas de aplicações públicas. Os desenvolvedores podem automatizar ainda mais o seu fluxo de trabalho de distribuição com a App Center Build e integrações de lojas de aplicações públicas.

**Principais funcionalidades**
- Distribua a sua aplicação a testadores e utilizadores beta e certifique-se de que todos os seus testadores estão na versão mais recente da sua aplicação.
- Notifique os testadores de novos lançamentos sem que os testadores voltem a passar pelo fluxo de descarregamento.
- Gerencie os grupos de distribuição para diferentes versões da sua aplicação.
- Distribuir nas lojas: 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- Ganhe suporte à plataforma para iOS, Android, macOS, tvOS, Xamarin, React Native, Unity e Cordova.
- Registe automaticamente os dispositivos iOS no seu perfil de provisionamento.

**Referências**
- [Inscreva-se no Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Começar com app Center Distribute](/appcenter/build/)

### <a name="azure-pipelines"></a>Pipelines do Azure

[A Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) é um serviço de integração contínua (CI) e entrega contínua (CD) que funciona com o seu fornecedor de Git preferido. Os Oleodutos Azure podem ser implantados na maioria dos principais serviços em nuvem, como os serviços Azure. Pode começar com o seu código no GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud ou Azure Repos. Em seguida, pode automatizar a construção, teste e implementação do seu código para o Microsoft Azure, Google Cloud Platform ou Amazon Web Services (AWS).

**Principais funcionalidades**
- **Experiência simplificada baseada em tarefas para configurar um servidor CI:** Crie um servidor CI para aplicações móveis nativas (Android, iOS e Windows) e cross-platform (Xamarin, Cordova e React Native).
- **Qualquer idioma, plataforma e nuvem:** Construa, teste e implemente aplicações Node.js, Python, Java, PHP, Ruby, Go, C/C++, C#, Android e iOS. Corra em paralelo em Linux, macOS e Windows. Implemente para fornecedores de nuvem como Azure, AWS e Google Cloud Platform. Distribuir aplicações móveis através de canais beta e lojas de aplicações.
- **Suporte a contentores nativos:** Crie novos recipientes com facilidade e empurre-os para qualquer registo. Desdobre recipientes para hospedeiros independentes ou Kubernetes.
- **Fluxos de trabalho avançados e características:** Criar facilmente cadeias de construção e construções multifascadas. Obtenha apoio para YAML, integração de testes, portas de libertação, relatórios e muito mais.
- **Extensível:** Use uma gama de tarefas de construção, teste e implementação construídas pela comunidade, que inclui centenas de extensões de Slack para SonarCloud. Podes até implantar-te noutros sistemas de TI, como o Jenkins. Os ganchos web e as APIs REST podem ajudá-lo a integrar-se.
- **Construções gratuitas com nuvens:** Estas construções estão disponíveis para repositórios públicos e privados.
- **Suporte para implantação a outros fornecedores de nuvem:** Os fornecedores incluem AWS e Google Cloud Platform.

**Referências**
- [Começa com o guia Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Introdução ao Azure DevOps](https://app.vsaex.visualstudio.com/signup/)
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Distribua a sua aplicação diretamente para Lojas de Aplicações
Depois de a sua aplicação estar pronta para uso de produção e pretender que seja usada publicamente, tem de ser submetida a lojas de aplicações onde pode ser descarregada pelos clientes. Existem várias formas de distribuir a sua aplicação diretamente nas lojas de aplicações. 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
Com [o App Center Distribute,](/appcenter/distribution/stores/)pode publicar as suas aplicações móveis diretamente nas lojas de aplicações. Depois de a sua aplicação estar pronta para ser descarregada pelos utilizadores, pode publicar os binários da sua aplicação diretamente a partir do portal Visual Studio App Center. 

Pode distribuir diretamente para:
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Google Play Store](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
Na loja de aplicações desenvolvida e mantida pela Apple, os utilizadores podem navegar e descarregar aplicações desenvolvidas para dispositivos iOS, MacOS, WatchOS e tvOS. Os desenvolvedores precisam submeter as suas aplicações iOS à Apple App Store para uso público.

### <a name="google-play"></a>Google Play

O Google Play é a loja oficial de aplicações para o Android OS, onde os utilizadores podem navegar e descarregar aplicações desenvolvidas para dispositivos Android que são publicadas através da Google.

### <a name="intune"></a>Intune

[O Microsoft Intune](/intune/app-management) é um serviço baseado na nuvem no espaço de gestão da mobilidade empresarial que ajuda a permitir que a sua força de trabalho seja produtiva, mantendo os seus dados corporativos protegidos. Com o Intune, pode:
- Gerencie os dispositivos móveis e os computadores que a sua força de trabalho utiliza para aceder aos dados da empresa.
- Gerir as aplicações móveis que a sua força de trabalho utiliza.
- Proteja as informações da sua empresa controlando a forma como a sua força de trabalho acede e partilha.
- Certifique-se de que os dispositivos e aplicações estão em conformidade com os requisitos de segurança da empresa.
    
## <a name="deploy-updates-directly-to-users-devices"></a>Implementar atualizações diretamente para os dispositivos dos utilizadores

### <a name="codepush"></a>CodePush
Com [o CodePush](/appcenter/distribution/codepush/) no App Center, os desenvolvedores Apache Cordova e React Native podem implementar atualizações de aplicações móveis diretamente para os dispositivos dos seus utilizadores. Funciona como um repositório central para que os desenvolvedores possam publicar certas atualizações, tais como JavaScript, HTML, CSS e alterações de imagem. Em seguida, as aplicações podem consultar para atualizações a partir do repositório utilizando os SDKs do cliente fornecidos. Desta forma, pode ter um modelo de noivado mais determinístico e direto com os seus utilizadores enquanto aborda bugs ou adiciona pequenas funcionalidades. Não é obrigado a reconstruir um binário ou a redistribuí-lo através de quaisquer lojas de aplicações públicas.

**Principais funcionalidades**
- Os desenvolvedores da Cordova e React Native podem implementar atualizações de aplicações móveis diretamente para os dispositivos dos seus utilizadores sem ser lançados numa loja.
- Útil para corrigir bugs ou adicionar e remover pequenas funcionalidades que não requerem que você reconstrua binário e redistribua-lo através das respetivas lojas.

**Referências**
- [Inscreva-se no Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Começar com codePush no App Center](/appcenter/distribution/codepush/)
- [CodePush CLI](/appcenter/distribution/codepush/cli)