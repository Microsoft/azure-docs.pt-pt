---
title: Automatize a implantação e a versão de seus aplicativos móveis com Visual Studio App Center e serviços do Azure
description: Saiba mais sobre os serviços como App Center que ajudam a configurar o pipeline de entrega contínua para seus aplicativos móveis.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 6a8c8d9fc535d973c70eb2e477051dbd1dd1f6fb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454460"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>Automatize a implantação e o lançamento de seus aplicativos móveis com serviços de entrega contínua

Como desenvolvedores, você escreve o código e o verifica no repositório de código, mas as confirmações verificadas no repositório talvez nem sempre sejam consistentes. Quando vários desenvolvedores trabalham no mesmo projeto, os problemas podem surgir com a integração. As equipes podem se deparar com situações em que as coisas não funcionam, os bugs se acumulam e o desenvolvimento do projeto é atrasado. Os desenvolvedores precisam esperar até que todo o código de software seja criado e testado para verificar se há erros, o que torna o processo lento e menos iterativo.

Com a entrega contínua, você automatiza a implantação e a liberação de seus aplicativos móveis. Não importa se você está distribuindo o aplicativo para um grupo de testadores ou funcionários da empresa (para teste beta) ou para uma loja de aplicativos (para produção). A entrega contínua torna as implantações menos arriscadas e incentiva as iterações rápidas. Você também pode liberar novas alterações para seus clientes de maneira contínua.

## <a name="distribute-application-binaries-to-beta-testers"></a>Distribuir binários de aplicativo para testadores beta
O teste beta de seu aplicativo móvel é uma das etapas críticas durante o processo de desenvolvimento de aplicativos. Ele ajuda a encontrar bugs e problemas no seu aplicativo no início. Os comentários aprimoram a qualidade do aplicativo quando você o estiver preparando para uso em produção.

Use os seguintes serviços para habilitar um pipeline de entrega contínua em seus aplicativos móveis.

### <a name="visual-studio-app-center"></a>Visual Studio App Center
A [distribuição app Center](/appcenter/distribution/) é uma ferramenta para que os desenvolvedores liberem rapidamente compilações para dispositivos. Com uma experiência completa de portal de instalação, o App Center distribute é uma solução poderosa para distribuição de testador de aplicativos beta. Também é uma alternativa conveniente para a distribuição por meio de lojas de aplicativos públicos. Os desenvolvedores podem automatizar ainda mais o fluxo de trabalho de distribuição com App Center Build e integrações de loja de aplicativos públicas.

**Principais funcionalidades**
- Distribua seu aplicativo para testadores e usuários beta e verifique se todos os testadores estão na versão mais recente do seu aplicativo.
- Notifique os testadores de novas versões sem que os testadores passem pelo fluxo de download novamente.
- Gerencie grupos de distribuição para versões diferentes do seu aplicativo.
- Distribuir para lojas: 
    - [Laranja](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- Obter suporte de plataforma para iOS, Android, macOS, tvOS, Xamarin, reagir nativo, Unity e Cordova.
- Registrar automaticamente os dispositivos iOS no seu perfil de provisionamento.

**Referências**
- [Inscreva-se com Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Introdução à distribuição de App Center](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

O [Azure pipelines](https://azure.microsoft.com/services/devops/pipelines/) é um serviço de integração contínua (CI) e entrega contínua (CD) que funciona com seu provedor de git preferido. Azure Pipelines pode implantar na maioria dos principais serviços de nuvem, como os serviços do Azure. Você pode começar com seu código no GitHub, no GitHub Enterprise Server, no GitLab, no bitbucket Cloud ou no Azure Repos. Em seguida, você pode automatizar a compilação, o teste e a implantação do seu código para Microsoft Azure, Google Cloud Platform ou Amazon Web Services (AWS).

**Principais funcionalidades**
- **Experiência simplificada baseada em tarefas para configurar um servidor de CI:** Configure um servidor de CI para aplicativos móveis nativos (Android, iOS e Windows) e entre plataformas (Xamarin, Cordova e reaja nativo).
- **Qualquer linguagem, plataforma e nuvem:** Crie, teste e implante os aplicativos node. js, Python, Java, PHP, Ruby, go, CC++/ C#,, Android e Ios. Execute em paralelo em Linux, macOS e Windows. Implante em provedores de nuvem como Azure, AWS e Google Cloud Platform. Distribua aplicativos móveis por meio de canais beta e lojas de aplicativos.
- **Suporte a contêiner nativo:** Crie novos contêineres com facilidade e envie-os por push para qualquer registro. Implante contêineres em hosts independentes ou kubernetes.
- **Recursos e fluxos de trabalho avançados:** Crie facilmente cadeias de Build e compilações em fases. Obtenha suporte para YAML, integração de testes, liberar Gates, relatórios e muito mais.
- **Extensível:** Use uma variedade de tarefas de compilação, teste e implantação criadas pela Comunidade, que inclui centenas de extensões de margem de atraso para SonarCloud. Você pode até mesmo implantar de outros sistemas de CI, como Jenkins. Os ganchos da Web e as APIs REST podem ajudá-lo a integrar o.
- **Compilações gratuitas hospedadas na nuvem:** Essas compilações estão disponíveis para repositórios públicos e privados.
- **Suporte para implantação em outros fornecedores de nuvem:** Os fornecedores incluem AWS e Google Cloud Platform.

**Referências**
- [Introdução ao guia de Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Introdução ao Azure DevOps](https://app.vsaex.visualstudio.com/signup/)
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Distribua seu aplicativo diretamente para lojas de aplicativos
Depois que o aplicativo estiver pronto para uso em produção e você quiser usá-lo publicamente, ele precisará ser enviado para lojas de aplicativos, onde ele pode ser baixado pelos clientes. Há várias maneiras de distribuir seu aplicativo diretamente para lojas de aplicativos. 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
Com [app Center distribuir](/appcenter/distribution/stores/), você pode publicar seus aplicativos móveis diretamente nas lojas de aplicativos. Depois que o aplicativo estiver pronto para ser baixado pelos usuários, você poderá publicar os binários do aplicativo diretamente do portal de Visual Studio App Center. 

Você pode distribuir diretamente para:
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Google Play Store](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
Na App Store desenvolvida e mantida pela Apple, os usuários podem navegar e baixar aplicativos desenvolvidos para dispositivos iOS, MacOS, WatchOS e tvOS. Os desenvolvedores precisam enviar seus aplicativos iOS para a Apple App Store para uso público.

### <a name="google-play"></a>Google Play

Google Play é a App Store oficial para o sistema operacional Android, em que os usuários podem navegar e baixar aplicativos desenvolvidos para dispositivos Android que são publicados por meio do Google.

### <a name="intune"></a>Intune

O [Microsoft Intune](/intune/app-management) é um serviço baseado em nuvem no espaço de gerenciamento de mobilidade empresarial que ajuda a permitir que sua força de negócios seja produtiva enquanto mantém seus dados corporativos protegidos. Com o Intune, pode:
- Gerencie os dispositivos móveis e os computadores que sua força de equipe usa para acessar os dados da empresa.
- Gerencie os aplicativos móveis que sua força de uso usa.
- Proteja as informações da sua empresa controlando o modo como sua força de equipe acessa e compartilha.
- Verifique se os dispositivos e os aplicativos estão em conformidade com os requisitos de segurança da empresa.
    
## <a name="deploy-updates-directly-to-users-devices"></a>Implantar atualizações diretamente nos dispositivos dos usuários

### <a name="codepush"></a>CodePush
Com o [CodePush](/appcenter/distribution/codepush/) em App Center, Apache Cordova e reagir os desenvolvedores nativos podem implantar atualizações de aplicativos móveis diretamente nos dispositivos dos usuários. Ele atua como um repositório central no qual os desenvolvedores podem publicar determinadas atualizações, como JavaScript, HTML, CSS e alterações de imagem. Em seguida, os aplicativos podem consultar atualizações do repositório usando os SDKs de cliente fornecidos. Dessa forma, você pode ter um modelo de envolvimento mais determinístico e direto com seus usuários enquanto resolve bugs ou adiciona recursos pequenos. Não é necessário recriar um binário ou redistribuí-lo por meio de qualquer loja de aplicativos públicos.

**Principais funcionalidades**
- O Cordova e reagir aos desenvolvedores nativos podem implantar atualizações de aplicativos móveis diretamente nos dispositivos dos seus usuários, sem liberá-los em um repositório.
- Útil para corrigir bugs ou adicionar e remover recursos pequenos que não exigem que você reconstrua binário e redistribua-os por meio de respectivos repositórios.

**Referências**
- [Inscreva-se com Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Introdução ao CodePush no App Center](/appcenter/distribution/codepush/)
- [CLI do CodePush](/appcenter/distribution/codepush/cli)