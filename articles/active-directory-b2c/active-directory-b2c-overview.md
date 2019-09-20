---
title: O que é o Azure Active Directory B2C?
description: Saiba como você pode usar Azure Active Directory B2C para dar suporte a identidades externas em seus aplicativos, incluindo o logon social com o Facebook, o Google e outros provedores de identidade.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/19/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 007b23f07afec6163c2158feb3f17ba71e44bdb5
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71120566"
---
# <a name="what-is-azure-active-directory-b2c"></a>O que é o Azure Active Directory B2C?

O Azure Active Directory B2C fornece a identidade do cliente como um serviço. Seus clientes usam suas identidades de conta social, corporativa ou local preferenciais para obter acesso de logon único para seus aplicativos e APIs.

![Infográfico de provedores de identidade Azure AD B2C e aplicativos downstream](media/active-directory-b2c-overview/azureadb2c-overview.png)

O Azure Active Directory B2C (Azure AD B2C) é uma solução de CIAM (gerenciamento de acesso de identidade do cliente) capaz de dar suporte a milhões de usuários e bilhões de autenticações por dia. Ele cuida do dimensionamento e da segurança da plataforma de autenticação, do monitoramento e do tratamento automático de ameaças, como negação de serviço, irrigação de senha ou ataques de força bruta.

## <a name="custom-branded-identity-solution"></a>Solução de identidade com marca personalizada

Azure AD B2C é uma solução de autenticação de rótulo branco. Você pode personalizar toda a experiência do usuário com sua marca para que ela se misture diretamente com seus aplicativos Web e móveis.

Personalize cada página exibida por Azure AD B2C quando os usuários se inscrevem, entram e modificam suas informações de perfil. Personalize o HTML, CSS e JavaScript em suas viagens de usuário para que a experiência de Azure AD B2C pareça e seja uma parte nativa do seu aplicativo.

![Páginas de inscrição e entrada personalizadas e a imagem de plano de fundo](media/active-directory-b2c-overview/sign-in-small.png)

## <a name="single-sign-on-access-with-a-user-provided-identity"></a>Acesso de logon único com uma identidade fornecida pelo usuário

O Azure AD B2C usa protocolos de autenticação baseados em padrões, incluindo OpenID Connect, OAuth 2,0 e SAML. Ele se integra aos aplicativos mais modernos e commercial off-the-shelf software.

![Diagrama de identidades de terceiros que se federa à Azure AD B2C](media/active-directory-b2c-overview/scenario-singlesignon.png)

Ao servir como a autoridade de autenticação central para seus aplicativos Web, aplicativos móveis e APIs, Azure AD B2C permite que você crie uma solução de SSO (logon único) para todos eles. Centralize a coleta de perfil do usuário e informações de preferência e Capture análises detalhadas sobre o comportamento de entrada e a conversão de inscrição.

## <a name="integrate-with-external-user-stores"></a>Integrar com armazenamentos de usuários externos

Azure AD B2C fornece um diretório que pode conter 100 atributos personalizados por usuário. No entanto, você também pode integrar com sistemas externos. Por exemplo, use Azure AD B2C para autenticação, mas delegar a um CRM (gerenciamento de relacionamento com o cliente) externo ou a um banco de dados de fidelidade do cliente como a fonte de verdade para o cliente.

Outro cenário de armazenamento de usuário externo é ter Azure AD B2C manipular a autenticação para seu aplicativo, mas integrá-lo a um sistema externo que armazena o perfil do usuário ou dados pessoais. Por exemplo, para satisfazer os requisitos de residência de dados como políticas regionais ou locais de armazenamento de dados.

![Um diagrama lógico de Azure AD B2C se comunicando com um armazenamento de usuário externo](media/active-directory-b2c-overview/scenario-remoteprofile.png)

Azure AD B2C pode facilitar a coleta de informações do usuário durante a edição do registro ou do perfil e, em seguida, a entrega desses dados para o sistema externo. Em seguida, durante as autenticações futuras, Azure AD B2C pode recuperar os dados do sistema externo e, se necessário, incluí-los como parte da resposta do token de autenticação que ele envia para seu aplicativo.

## <a name="progressive-profiling"></a>Criação de perfil progressiva

Outra opção de jornada do usuário inclui a criação de perfil progressiva. A criação de perfil progressiva permite que seus clientes concluam rapidamente sua primeira transação coletando uma quantidade mínima de informações. Em seguida, colete gradualmente mais dados de perfil do cliente em logons futuros.

![Uma representação visual da criação de perfil progressiva](media/active-directory-b2c-overview/scenario-progressive.png)

## <a name="third-party-identity-verification-and-proofing"></a>Verificação de identidade de terceiros e prova de ortografia

Use Azure AD B2C para facilitar a verificação de identidade e a prova coletando dados do usuário, passando-os para um sistema de terceiros para executar validação, pontuação de confiança e aprovação para a criação da conta de usuário.

![Um diagrama que mostra o fluxo do usuário para a verificação de identidade de terceiros](media/active-directory-b2c-overview/scenario-idproofing.png)

Essas são apenas algumas das coisas que você pode fazer com Azure AD B2C como sua plataforma de identidade entre clientes. As seções a seguir desta visão geral orientam você por um aplicativo de demonstração que usa Azure AD B2C. Você também é bem-vindo a passar diretamente para uma [visão geral técnica](technical-overview.md)mais aprofundada do Azure ad B2C.

## <a name="example-woodgrove-groceries"></a>Exemplo: Mercados do WoodGrove

Os [mercados do Woodgrove][woodgrove] são um aplicativo Web ao vivo criado pela Microsoft para demonstrar vários recursos de Azure ad B2C. As próximas seções examinam algumas das opções de autenticação fornecidas pelo Azure AD B2C para o site do WoodGrove.

### <a name="business-overview"></a>Visão geral dos negócios

O WoodGrove é um armazenamento online de mercados que vende mercados para consumidores individuais e clientes de negócios. Seus clientes de negócios compram mercados em nome de sua empresa ou empresas que eles gerenciam.

### <a name="sign-in-options"></a>Opções de início de sessão

Os mercados do WoodGrove oferecem várias opções de entrada com base na relação que seus clientes têm com a loja:

* Clientes **individuais** podem se inscrever ou entrar com contas individuais, como com um provedor de identidade social ou um endereço de email e senha.
* Os clientes de **negócios** podem se inscrever ou entrar com suas credenciais corporativas.
* **Parceiros** e fornecedores são indivíduos que fornecem a loja de supermercado com produtos para venda. A identidade do parceiro é fornecida por [Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md).

![Páginas de entrada individuais (B2C), Business (B2C) e parceiro (B2B)](media/active-directory-b2c-overview/woodgrove-overview.png)

### <a name="authenticate-individual-customers"></a>Autenticar clientes individuais

Quando um cliente seleciona **entrar com sua conta pessoal**, ele é redirecionado para uma página de entrada personalizada hospedada pelo Azure ad B2C. Você pode ver na imagem a seguir que personalizamos a interface do usuário (IU) para que ela seja parecida com o site de supermercado do WoodGrove. Os clientes do WoodGrove não devem estar cientes de que a experiência de autenticação é hospedada e protegida pelo Azure AD B2C.

![Página de entrada personalizada do WoodGrove hospedada por Azure AD B2C](media/active-directory-b2c-overview/sign-in.png)

O WoodGrove permite que seus clientes se inscrevam e entrem usando suas contas do Google, Facebook ou Microsoft como seu provedor de identidade. Ou, eles podem se inscrever usando seu endereço de email e uma senha para criar o que é chamado de *conta local*.

Quando um cliente seleciona **inscrever-se com sua conta pessoal** e, em seguida, **se inscrever agora**, é apresentada uma página de inscrição personalizada.

![Página de inscrição personalizada do WoodGrove hospedada por Azure AD B2C](media/active-directory-b2c-overview/sign-up.png)

Depois de inserir um endereço de email e selecionar **enviar código de verificação**, Azure ad B2C envia-lhes o código. Depois de inserir seu código, selecione **verificar código**e, em seguida, insira as outras informações no formulário, eles também devem concordar com os termos de serviço.

Clicar no botão **criar** faz Azure ad B2C redirecionar o usuário de volta para o site de supermercado do Woodgrove. Ao ser redirecionado, o Azure AD B2C passa um token de autenticação do OpenID Connect para o aplicativo Web do WoodGrove. O usuário agora está conectado e pronto para ser usado, o nome de exibição mostrado no canto superior direito para indicar que está conectado.

![Cabeçalho do site de supermercado do WoodGrove mostrando que o usuário está conectado](media/active-directory-b2c-overview/signed-in-individual.png)

### <a name="authenticate-business-customers"></a>Autenticar clientes comerciais

Quando um cliente seleciona uma das opções em **clientes comerciais**, o site de mercados do Woodgrove invoca uma política de Azure ad B2C diferente da que faz para clientes individuais.

Essa política apresenta ao usuário uma opção para usar suas credenciais corporativas para inscrição e entrada. No exemplo do WoodGrove, os usuários são solicitados a entrar com qualquer conta do Office 365 ou do Azure AD. Essa política usa um [aplicativo multilocatário do Azure ad](../active-directory/develop/howto-convert-app-to-be-multi-tenant.md) e o `/common` ponto de extremidade do Azure ad para federar Azure ad B2C com qualquer cliente do Office 365 no mundo.

### <a name="authenticate-partners"></a>Autenticar parceiros

O link **entrar com sua conta de fornecedor** usa Azure Active Directory funcionalidade de colaboração B2B's. O Azure AD B2B é uma família de recursos no Azure Active Directory para gerenciar identidades de parceiros. Essas identidades podem ser federadas a partir de Azure Active Directory para acesso a aplicativos protegidos por Azure AD B2C.

Saiba mais sobre o Azure AD B2B em [o que é acesso de usuário convidado em Azure Active Directory B2B?](../active-directory/b2b/what-is-b2b.md).

<!-- UNCOMMENT WHEN REPO IS UPDATED WITH LATEST DEMO CODE
### Sample code

If you'd like to jump right into the code to see how the WoodGrove Groceries application is built, you can find the repository on GitHub:

[Azure-Samples/active-directory-external-identities-woodgrove-demo][woodgrove-repo] (GitHub)
-->

## <a name="next-steps"></a>Passos seguintes

Agora que você tem uma ideia do que Azure AD B2C é e de alguns dos cenários com os quais pode ajudar, aprofunde-se em seus recursos e aspectos técnicos.

> [!div class="nextstepaction"]
> [Visão geral técnica de Azure AD B2C >](technical-overview.md)

<!-- LINKS - External -->
[woodgrove]: https://aka.ms/ciamdemo
[woodgrove-repo]: https://github.com/Azure-Samples/active-directory-external-identities-woodgrove-demo
