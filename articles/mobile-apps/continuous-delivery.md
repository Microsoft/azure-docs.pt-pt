---
title: Automatize a implantação e a versão de seus aplicativos móveis com Visual Studio App Center e serviços do Azure
description: Saiba mais sobre os serviços como App Center que ajudam a configurar o pipeline de entrega contínua para seus aplicativos móveis.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 3b8305d02345abe98b674a8ed61626eaf6b5528d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795604"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>Automatize a implantação e o lançamento de seus aplicativos móveis com serviços de entrega contínua

Como desenvolvedores, você escreve o código e o verifica no repositório de código, mas as confirmações verificadas no repositório podem não ser sempre consistentes. Com vários desenvolvedores trabalhando no mesmo projeto, os problemas surgem com a integração e a equipe estaria em situações em que as coisas não estão funcionando, os bugs continuam empilhandondo e o desenvolvimento do projeto é atrasado. Os desenvolvedores precisam esperar até que todo o código de software seja criado e testado para verificar se há erros e isso torna o processo lento e menos iterativo.

Com a **entrega contínua**, você automatiza a implantação e a liberação de seus aplicativos móveis, independentemente de você estar distribuindo o aplicativo para um grupo de testadores ou funcionários da empresa (para teste beta) ou a loja de aplicativos (para produção). Ele torna as implantações menos arriscadas, incentiva iterações rápidas e permite que você libere novas alterações para seus clientes de maneira contínua.

## <a name="distribute-application-binaries-to-beta-testers"></a>Distribuir binários de aplicativo para testadores beta
O teste beta de seu aplicativo móvel é uma das etapas críticas durante o processo de desenvolvimento de aplicativos. Ele ajuda a encontrar bugs e problemas no seu aplicativo no início e nos comentários que melhoram a qualidade do aplicativo, preparando-o para uso em produção.

Use os seguintes serviços para habilitar o pipeline de entrega contínua em seus aplicativos móveis.

### <a name="visual-studio-app-center"></a>Visual Studio App Center
A [distribuição app Center](/appcenter/distribution/) é uma ferramenta para que os desenvolvedores liberem rapidamente compilações para dispositivos de usuários finais. Com uma experiência completa do portal de instalação, a distribuição não é apenas uma solução poderosa para distribuição de testador de aplicativos beta, mas também uma alternativa conveniente à distribuição por meio de lojas de aplicativos públicos. Os desenvolvedores podem automatizar ainda mais o fluxo de trabalho de distribuição com App Center Build e integrações de loja de aplicativos públicas.

**Principais recursos**
- **Distribua seu aplicativo para testadores e usuários beta** e garanta que todos os testadores estejam na versão mais recente do seu aplicativo.
- **Notifique os testadores de novas versões** sem que os testadores passem pelo fluxo de download novamente.
- **Gerencie grupos de distribuição** para versões diferentes do seu aplicativo.
- **Distribuição para armazenamentos** 
    - [Laranja](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- **Suporte a plataforma** -Ios, Android, MacOS, TvOS, Xamarin, reagir nativo, Unity, Cordova.
- Registrar automaticamente os dispositivos iOS no seu perfil de provisionamento.

**Referências**
- [Inscreva-se com App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Introdução à distribuição de App Center](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

O [Azure pipelines](https://azure.microsoft.com/services/devops/pipelines/) é um serviço de CI (integração contínua) e distribuição contínua (CD) que funciona com seu provedor de git preferido e pode ser implantado na maioria dos principais serviços de nuvem, incluindo os serviços do Azure. Você pode começar com seu código no GitHub, no GitHub Enterprise Server, no GitLab, no bitbucket Cloud ou no Azure Repos. Em seguida, você pode automatizar a compilação, os testes e a implantação de seu código para Microsoft Azure, Google Cloud Platform ou Amazon Web Services.

**Principais recursos**
- Experiência simplificada baseada em tarefas para configurar um servidor de CI para **aplicativos móveis nativos (Android, Ios e Windows) e entre plataformas (Xamarin, Cordova e reagir nativos)** .
- **Qualquer linguagem, plataforma e nuvem** -Compile, teste e implante os aplicativos node. js, Python, Java, PHP, Ruby, go, C/C++, C#, Android e Ios. Execute em paralelo em Linux, macOS e Windows. Implante em provedores de nuvem como Azure, AWS e GCP. Distribua aplicativos móveis por meio de canais beta e lojas de aplicativos.
- **Suporte a contêiner nativo** -crie novos contêineres com facilidade e envie-os por push para qualquer registro. Implante contêineres em hosts independentes ou kubernetes.
- **Fluxos de trabalho avançados e recursos** – criação de encadeamento de compilação e compilações com várias fases. Suporte para YAML, integração de teste, lançamento de entradas, relatórios e muito mais.
- **Extensível** – use uma variedade de tarefas de compilação, teste e implantação criadas pela Comunidade – centenas de extensões da margem de atraso para SonarCloud. Você pode até mesmo implantar de outros sistemas de CI, como Jenkins. WebHooks e APIs REST ajudam você a integrar
- **Compilações gratuitas hospedadas na nuvem** para repositórios públicos e privados.
- **Dá suporte à implantação em outros fornecedores de nuvem** , como AWS, GCP etc.

**Referências**
- [Introdução ao guia de Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Introdução ao Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Distribua seu aplicativo diretamente para lojas de aplicativos
Quando seu aplicativo estiver pronto para uso em produção e você quiser que ele seja usado publicamente, ele precisará ser enviado para lojas de aplicativos, onde ele pode ser baixado pelos clientes. Há várias maneiras de distribuir seu aplicativo diretamente para lojas de aplicativos. 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center](/appcenter/distribution/stores/) serviço de distribuição permite publicar seus aplicativos móveis diretamente nas lojas de aplicativos. Depois que o aplicativo estiver pronto para ser baixado pelos usuários finais, você poderá publicar os binários do aplicativo diretamente do portal de App Center.  

Você pode distribuir diretamente para:
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Google Play Store](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
A App Store desenvolvida e mantida pela Apple permite que os usuários naveguem e baixem aplicativos desenvolvidos para dispositivos iOS, MacOS, WatchOS e tvOS. Os desenvolvedores precisam enviar seus aplicativos iOS para a Apple Store para uso público.

### <a name="google-play"></a>Google Play

Google Play é a App Store oficial para o sistema operacional Android, permitindo que os usuários naveguem e baixem aplicativos desenvolvidos para dispositivos Android e publicados por meio do Google.

### <a name="intune"></a>Intune

[Microsoft Intune](/intune/app-management) é um serviço baseado em nuvem no espaço de gerenciamento de mobilidade empresarial (EMM) que ajuda a permitir que sua força de negócios seja produtiva enquanto mantém seus dados corporativos protegidos. Com o Intune, você pode 
- Gerencie os dispositivos móveis e os computadores que sua força de equipe usa para acessar os dados da empresa.
- Gerencie os aplicativos móveis que sua força de uso usa.
- Proteja as informações da sua empresa controlando o modo como sua força de equipe acessa e compartilha.
- Verifique se os dispositivos e os aplicativos estão em conformidade com os requisitos de segurança da empresa.
    
## <a name="deploy-updates-directly-to-users-devices"></a>Implantar atualizações diretamente nos dispositivos dos usuários

### <a name="codepush"></a>CodePush
O serviço [CodePush](/appcenter/distribution/codepush/) no App Center permite Apache Cordova e reagir aos desenvolvedores nativos para implantar atualizações de aplicativos móveis diretamente nos dispositivos dos usuários. Ele funciona agindo como um repositório central no qual os desenvolvedores podem publicar determinadas atualizações (por exemplo, JS, HTML, CSS e alterações de imagem). Em seguida, os aplicativos podem consultar atualizações do repositório usando os SDKs de cliente fornecidos. Isso permite que você tenha um modelo de envolvimento mais determinístico e direto com seus usuários finais, ao mesmo tempo em que aborda bugs ou adiciona recursos pequenos sem a necessidade de recriar um binário ou redistribuí-lo por meio de qualquer loja de aplicativos públicos.

**Principais recursos**
- Permite que o Cordova e reaja os desenvolvedores nativos implantem atualizações de aplicativos móveis diretamente nos dispositivos dos usuários sem a liberação em um repositório.
- Útil para corrigir bugs ou adicionar/remover recursos pequenos que não exigem a recriação de binários e redistribuição por meio de respectivos repositórios.

**Referências**
- [Inscreva-se com App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Introdução ao push de código no App Center](/appcenter/distribution/codepush/)
- [CLI do CodePush](/appcenter/distribution/codepush/cli)