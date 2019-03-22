---
title: Descrição geral de identidade para o Azure Stack | Documentos da Microsoft
description: Saiba mais sobre os sistemas de identidade que pode utilizar com o Azure Stack.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 665f8ac9a8b0738ed23649673c548bc6b1774d2d
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259963"
---
# <a name="overview-of-identity-for-azure-stack"></a>Descrição geral de identidade para o Azure Stack

O Azure Stack requer o Azure Active Directory (Azure AD) ou serviços de Federação do Active Directory (AD FS), apoiado pelo Active Directory como um fornecedor de identidade. A escolha de um provedor é uma decisão única que fizer quando implantar primeiro o Azure Stack. Os conceitos e detalhes de autorização neste artigo podem ajudá-lo a escolher entre fornecedores de identidade.

À sua escolha do Azure AD ou AD FS é determinada pelo modo no qual implementar o Azure Stack:

- Quando implementá-lo num modo ligado, pode utilizar o Azure AD ou AD FS.
- Quando implementar num modo desligado, sem uma ligação à internet, é suportado apenas do AD FS.

Para obter mais informações sobre as suas opções dependem do seu ambiente do Azure Stack, veja os artigos seguintes:

- Kit de implementação do Azure Stack: [Considerações sobre a identidade](azure-stack-datacenter-integration.md#identity-considerations).
- Sistemas integrados do Azure Stack: [Decisões para o Azure Stack de planejamento da implantação de sistemas integrados](azure-stack-deployment-decisions.md).

## <a name="common-concepts-for-identity"></a>Conceitos comuns para a identidade

As próximas seções falam sobre conceitos comuns sobre fornecedores de identidade e de seu uso no Azure Stack.

![Terminologia para fornecedores de identidade](media/azure-stack-identity-overview/terminology.png)

### <a name="directory-tenants-and-organizations"></a>Inquilinos de diretório e as organizações

Um diretório é um contentor que retém informações sobre *usuários*, *aplicativos*, *grupos*, e *principais de serviço*.

Um inquilino do diretório é um *organização*, tais como a Microsoft ou da sua empresa.

- O Azure AD suporta vários inquilinos, e ele pode oferecer suporte a várias organizações, cada um em seu próprio diretório. Se utilizar o Azure AD e tiver vários inquilinos, pode conceder a aplicações e os utilizadores de acesso de um inquilino de outros inquilinos do mesmo diretório.
- AD FS suporta apenas um único inquilino e, portanto, apenas uma única organização.

### <a name="users-and-groups"></a>Utilizadores e grupos

Contas de utilizador (identidades) são contas padrão que autenticam indivíduos com um ID de utilizador e palavra-passe. Os grupos podem incluir utilizadores ou outros grupos.

Como criar e gerir utilizadores e grupos depende a solução de identidade que utilizar.

No Azure Stack, contas de utilizador:

- São criados no *nome de utilizador\@domínio* formato. Embora o AD FS mapeia as contas de utilizador para uma instância do Active Directory, AD FS não suporta a utilização do  *\\ \<domínio >\\\<alias >* formato.
- Pode ser configurado para utilizar a autenticação multifator.
- São restritos para o diretório onde eles primeiro Registre-se, que é o diretório da sua organização.
- Podem ser importados a partir de seus diretórios no local. Para obter mais informações, consulte [integrar seus diretórios no local com o Azure Active Directory](/azure/active-directory/connect/active-directory-aadconnect).

Quando inicia sessão no portal de inquilinos da sua organização, utilize o *https:\//portal.local.azurestack.external* URL. Ao iniciar sessão no portal do Azure Stack de domínios que não seja utilizada para registar o Azure Stack, o nome de domínio utilizado para registar o Azure Stack deve ser acrescentado ao portal do url. Por exemplo, se o Azure Stack foi registado com fabrikam.onmicrosoft.com e a conta de utilizador iniciar sessão está admin@contoso.com, o url a utilizar para iniciar sessão no portal de utilizador seria: https:\//portal.local.azurestack.external/ Fabrikam.onmicrosoft.com.

### <a name="guest-users"></a>Utilizadores convidados

Os utilizadores convidados são contas de utilizador de outros inquilinos do diretório que foi concedidos acesso a recursos no seu diretório. Para suportar os utilizadores convidados, utilize o Azure AD e ativar o suporte para vários inquilinos. Quando o suporte está ativado, pode convidar utilizadores convidados para aceder a recursos no seu inquilino do diretório, que por sua vez, permite sua colaboração com organizações externas.

Convidar utilizadores convidados, os operadores da nuvem e os utilizadores podem utilizar [colaboração B2B do Azure AD](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Utilizadores convidados obtém acesso a documentos, recursos e aplicações a partir de seu diretório e mantém o controlo sobre os seus próprios recursos e dados. 

Como um utilizador convidado, podem iniciar sessão inquilino do diretório de outra organização. Para fazer isso, acrescentar o nome do diretório dessa organização para o portal de URL. Por exemplo, se pertence à organização Contoso e pretende iniciar sessão para o diretório da Fabrikam, utilizar o https:\//portal.local.azurestack.external/fabrikam.onmicrosoft.com.

### <a name="applications"></a>Aplicações

Pode registar aplicações para o Azure AD ou AD FS e, em seguida, oferecer as aplicações aos utilizadores na sua organização.

Aplicações incluem:

- **Aplicação Web**: Os exemplos incluem o portal do Azure e o Azure Resource Manager. Eles oferecem suporte a chamadas de Web API.
- **Cliente nativo**: Os exemplos incluem o Azure PowerShell, o Visual Studio e o CLI do Azure.

Aplicativos podem suportar dois tipos de inquilinos:

- **Inquilino único**: Suporta utilizadores e serviços apenas a partir do mesmo diretório em que a aplicação fica registada.

  > [!NOTE]
  > Como o AD FS suporta apenas um único diretório, aplicações que criar numa topologia do AD FS são, por design, aplicações de inquilino único.

- **Multi-inquilino**: Suporta a utilizar por utilizadores e serviços do diretório onde o aplicativo está registado e diretórios de inquilinos adicionais. Com aplicativos de multi-inquilino, os utilizadores de outro pode de diretório (noutro inquilino do Azure AD) do inquilino iniciar sessão para a sua aplicação. 

  Para obter mais informações sobre vários inquilinos, consulte [ativar multi-inquilinos](azure-stack-enable-multitenancy.md).

  Para obter mais informações sobre como desenvolver uma aplicação multi-inquilino, consulte [aplicações multi-inquilino](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Quando registar uma aplicação, vai criar dois objetos:

- **Objeto de aplicativo**: A representação global da aplicação em todos os inquilinos. Esta relação é um para um com o aplicativo de software e existe somente no diretório em que a aplicação é registada pela primeira vez.

- **Objeto principal do serviço**: Uma credencial criada para uma aplicação no diretório em que a aplicação é registada pela primeira vez. Também é criado um principal de serviço no diretório de cada inquilino adicional em que esse aplicativo é usado. Esta relação pode ser um-para-muitos com a aplicação de software.

Para saber mais sobre a aplicação e objetos de principal de serviço, veja [aplicativos e objetos de principal de serviço no Azure Active Directory](/azure/active-directory/develop/active-directory-application-objects).

### <a name="service-principals"></a>Principais de serviço

Um principal de serviço é um conjunto de *credenciais* para uma aplicação ou serviço que concedem acesso a recursos no Azure Stack. A utilização de um principal de serviço separa as permissões de aplicação das permissões do utilizador da aplicação.

É criado um principal de serviço em cada inquilino onde o aplicativo é usado. O principal de serviço estabelece uma identidade para início de sessão e acesso a recursos (por exemplo, utilizadores) que são protegidas nesse inquilino.

- Uma aplicação de inquilino único tem apenas um principal de serviço, no diretório em que é criado pela primeira vez. Este principal de serviço é criado e dá consentimento a que está a ser utilizada durante o registo da aplicação.
- Um aplicativo web do multi-inquilino ou a API com um principal de serviço que é criado em cada inquilino em que um utilizador de inquilino permitir a utilização da aplicação.

As credenciais de principais de serviço podem ser uma chave que é gerada pelo portal do Azure ou um certificado. A utilização de um certificado é adequada para a automatização, como certificados são considerados mais seguros do que as chaves. 

> [!NOTE]
> Quando utilizar o AD FS com o Azure Stack, apenas o administrador pode criar principais de serviço. Com o AD FS, os principais de serviço necessitam de certificados e são criados através do ponto de final com privilégios (PEP). Para obter mais informações, consulte [fornecer acesso de aplicações para o Azure Stack](azure-stack-create-service-principals.md).

Para saber mais sobre os principais de serviço para o Azure Stack, veja [criar principais de serviço](azure-stack-create-service-principals.md).

### <a name="services"></a>Serviços

Serviços no Azure Stack que interagem com o fornecedor de identidade estão registados como aplicações com o fornecedor de identidade. Assim como os aplicativos, registo, permite que um serviço autenticar com o sistema de identidade.

Todos os serviços do Azure utilizam [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code) protocolos e [JSON Web Tokens](/azure/active-directory/develop/active-directory-token-and-claims) para estabelecer a identidade. Porque o Azure AD e AD FS utilizam protocolos de forma consistente, pode usar [do Azure Active Directory Authentication Library](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) para autenticar no local ou para o Azure (num cenário conectado). Com a ADAL, também pode utilizar ferramentas como o Azure PowerShell e CLI do Azure para gestão de recursos entre Clouds e no local.

### <a name="identities-and-your-identity-system"></a>As identidades e o sistema de identidade

Identidades para o Azure Stack incluem contas de utilizador, grupos e principais de serviço.

Ao instalar o Azure Stack, diversos aplicativos internos e os serviços registados automaticamente com o seu fornecedor de identidade no inquilino do diretório. Alguns serviços registrados são utilizados para a administração. Outros serviços estão disponíveis para os utilizadores. Os registros de predefinição dar identidades de serviços de núcleos que podem interagir entre si e com identidades que adicionar mais tarde.

Se configurar o Azure AD com vários inquilinos, alguns aplicativos ser propagada para os diretórios de novo.

## <a name="authentication-and-authorization"></a>Autenticação e autorização

### <a name="authentication-by-applications-and-users"></a>Autenticação por aplicativos e usuários

![Identidade entre camadas do Azure Stack](media/azure-stack-identity-overview/identity-layers.png)

Para aplicações e utilizadores, a arquitetura do Azure Stack é descrita por quatro camadas. Interações entre cada uma dessas camadas podem utilizar diferentes tipos de autenticação.

|Camada    |Autenticação entre camadas  |
|---------|---------|
|Ferramentas e os clientes, tais como o portal de administração     | Para acessar ou modificar um recurso no Azure Stack, ferramentas e os clientes utilizam uma [JSON Web Token](/azure/active-directory/develop/active-directory-token-and-claims) para fazer uma chamada para o Azure Resource Manager. <br>O Azure Resource Manager valida o JSON Web Token e lê a *afirmações* no token emitido para estimar o nível de autorização, esse utilizador ou principal de serviço tem no Azure Stack. |
|O Azure Resource Manager e os respetivos serviços de núcleo     |O Azure Resource Manager comunica com os fornecedores de recursos para transferir a comunicação dos usuários. <br> Transfere o uso *imperativo direto* chamadas ou *declarativa* chama [modelos Azure Resource Manager](/azure/azure-stack/user/azure-stack-arm-templates).|
|Fornecedores de recursos     |Chamadas que são transmitidas para fornecedores de recursos são protegidas com a autenticação baseada em certificados. <br>O Azure Resource Manager e o fornecedor de recursos, em seguida, mantenha-se na comunicação através de uma API. Para cada chamada que é recebida do Azure Resource Manager, o fornecedor de recursos valida a chamada com esse certificado.|
|Infraestrutura e lógica de negócio     |Fornecedores de recursos se comunicar com a lógica de negócios e a infraestrutura através de um modo de autenticação de sua preferência. Os fornecedores de recursos predefinidos que vêm com o Azure Stack utilizam a autenticação do Windows para proteger estas comunicações.|

![Informações necessárias para autenticação](media/azure-stack-identity-overview/authentication.png)

### <a name="authenticate-to-azure-resource-manager"></a>Autenticar para o Azure Resource Manager

Para autenticar com o fornecedor de identidade e receber um JSON Web Token, tem de ter as seguintes informações:

1. **URL para o sistema de identidade (autoridade)**: O URL em que o seu fornecedor de identidade pode ser contatado. Por exemplo, *https:\//login.windows.net*.
2. **O URI de ID de aplicação para o Azure Resource Manager**: O identificador exclusivo para o Azure Resource Manager que está registada no fornecedor de identidade. Também é exclusivo para cada instalação do Azure Stack.
3. **credenciais**: A credencial que é utilizada para autenticar com o fornecedor de identidade.
4. **URL para o Azure Resource Manager**: O URL é a localização do serviço do Azure Resource Manager. Por exemplo, *https:\//management.azure.com* ou *https:\//management.local.azurestack.external*.

Quando uma entidade de segurança (um cliente, aplicativo ou usuário) faz um pedido de autenticação para aceder a um recurso, o pedido tem de incluir:

- Credenciais do principal.
- A URI de ID do recurso que o principal que pretenda ter acesso à aplicação.

As credenciais são validadas pelo fornecedor de identidade. O fornecedor de identidade também valida que a aplicação, é o URI de ID, para uma aplicação registada e que o principal tem os privilégios corretos para obter um token para esse recurso. Se o pedido for válido, é concedido uma JSON Web Token.

Em seguida, tem do token de passar no cabeçalho de um pedido para o Azure Resource Manager. O Gestor de recursos do Azure faz o seguinte, sem nenhuma ordem específica:

- Valida o *emissor* (iss) de afirmação confirmar que o token é do fornecedor de identidade correto.
- Valida o *público-alvo* afirmação (aud) para confirmar que o token foi emitido para o Azure Resource Manager.
- Valida que o JSON Web Token seja assinado com um certificado que é configurado através do OpenID é conhecido para o Azure Resource Manager.
- Reveja os *emitido no* (iat) e *expiração* (exp) as afirmações confirmar que o token está ativo e podem ser aceites.

Quando todas as validações forem concluídas, o Azure Resource Manager utiliza o *objeto* (oid) e o *grupos* afirmações fazer uma lista de recursos que pode aceder a entidade de segurança.

![Diagrama do protocolo de troca de tokens](media/azure-stack-identity-overview/token-exchange.png)

> [!NOTE]
> Após a implementação, a permissão de administrador global do Azure Active Directory não é necessário. No entanto, algumas operações podem exigir a credencial de administrador global. Por exemplo, um script de instalador de fornecedor de recursos ou um novo recurso que requerem uma permissão para ser concedida. Pode temporariamente instate novamente as permissões de administrador global da conta ou utilizar uma conta de administrador global separado que é proprietária dos *predefinido da subscrição do fornecedor*.

### <a name="use-role-based-access-control"></a>Utilizar o controlo de acesso baseado em funções

Com base em função de controlo de acesso (RBAC) no Azure Stack é consistente com a implementação no Microsoft Azure. Pode gerir o acesso aos recursos ao atribuir a função RBAC adequada para os utilizadores, grupos e aplicações.
Para obter informações sobre como utilizar o RBAC com o Azure Stack, veja os artigos seguintes:

- [Introdução ao controlo de acesso baseado em funções no portal do Azure](/azure/role-based-access-control/overview).
- [Utilizar o controlo de acesso baseado em funções para gerir o acesso aos recursos da sua subscrição do Azure](/azure/role-based-access-control/role-assignments-portal).
- [Criar funções personalizadas para controlo de acesso](/azure/role-based-access-control/custom-roles).
- [Gerir controlo de acesso baseado em funções](azure-stack-manage-permissions.md) no Azure Stack.

### <a name="authenticate-with-azure-powershell"></a>Autenticar com o Azure PowerShell

Detalhes sobre como utilizar o Azure PowerShell para autenticar com o Azure Stack podem ser encontrados em [configurar o ambiente do PowerShell do utilizador do Azure Stack](azure-stack-powershell-configure-user.md).

### <a name="authenticate-with-azure-cli"></a>Autenticar com a CLI do Azure

Para obter informações sobre como utilizar o Azure PowerShell para autenticar com o Azure Stack, veja [instalar e configurar a CLI do Azure para utilização com o Azure Stack](/azure/azure-stack/user/azure-stack-connect-cli).

## <a name="next-steps"></a>Passos Seguintes

- [Arquitetura de identidade](azure-stack-identity-architecture.md)
- [Integração do Centro de dados - identidade](azure-stack-integrate-identity.md)
