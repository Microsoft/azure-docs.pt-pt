---
title: O que é o Azure Active Directory B2C?
description: Saiba como pode utilizar o Azure Ative Directory B2C para apoiar identidades externas nas suas aplicações, incluindo login social com facebook, Google e outros fornecedores de identidade.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 29b34041042f00d72b8a9fe86e31bd80f8c77dc2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94953955"
---
# <a name="what-is-azure-active-directory-b2c"></a>O que é o Azure Active Directory B2C?

O Azure Ative Directory B2C fornece a identidade entre empresas e clientes como um serviço. Os seus clientes utilizam as suas identidades sociais, empresariais ou locais preferidas para obter um único acesso de acesso às suas aplicações e APIs.

![Infográfico de fornecedores de identidade Azure AD B2C e aplicações a jusante](./media/overview/azureadb2c-overview.png)

O Azure Active Directory B2C (AAD B2C) é uma solução de gestão de acesso de identidade do cliente (CIAM) capaz de suportar milhões de utilizadores e milhares de milhões de autenticações por dia. Trata da escala e segurança da plataforma de autenticação, monitorizando e manuseando automaticamente ameaças como negação de serviço, spray de senha ou ataques de força bruta.

## <a name="custom-branded-identity-solution"></a>Solução de identidade de marca personalizada

Azure AD B2C é uma solução de autenticação com rótulo branco. Pode personalizar toda a experiência do utilizador com a sua marca para que se misture perfeitamente com as suas aplicações web e móveis.

Personalize todas as páginas apresentadas pelo Azure AD B2C quando os seus utilizadores se inscrevem, inscrevam e modificam as suas informações de perfil. Personalize o HTML, CSS e JavaScript nas suas viagens de utilizador para que a experiência AZure AD B2C pareça e sinta que é uma parte nativa da sua aplicação.

![Inscrições personalizadas e páginas de inscrição e imagem de fundo](./media/overview/sign-in-small.png)

## <a name="single-sign-on-access-with-a-user-provided-identity"></a>Acesso único de acesso com identidade fornecida pelo utilizador

O Azure AD B2C utiliza protocolos de autenticação baseados em padrões, incluindo OpenID Connect, OAuth 2.0 e SAML. Integra-se com a maioria das aplicações modernas e software comercial off-the-shelf.

![Diagrama de identidades de terceiros federando a Azure AD B2C](./media/overview/scenario-singlesignon.png)

Ao servir como autoridade central de autenticação para as suas aplicações web, aplicações móveis e APIs, o Azure AD B2C permite-lhe construir uma única solução de sinalização (SSO) para todas elas. Centralize a recolha de informações de perfil de utilizador e preferências e capture análises detalhadas sobre o comportamento de inscrição e conversão de inscrição.

## <a name="integrate-with-external-user-stores"></a>Integrar-se com lojas de utilizadores externos

O Azure AD B2C fornece um diretório que pode conter 100 atributos personalizados por utilizador. No entanto, também pode integrar-se com sistemas externos. Por exemplo, utilize o Azure AD B2C para autenticação, mas delegar numa gestão externa da relação com o cliente (CRM) ou na base de dados de fidelização do cliente como fonte de verdade para os dados do cliente.

Outro cenário externo da loja de utilizadores é ter o Azure AD B2C a tratar da autenticação da sua aplicação, mas integrar-se com um sistema externo que armazena o perfil do utilizador ou dados pessoais. Por exemplo, para satisfazer os requisitos de residência de dados, como políticas regionais ou de armazenamento de dados no local.

![Um diagrama lógico de Azure AD B2C comunicando com uma loja de utilizadores externa](./media/overview/scenario-remoteprofile.png)

O Azure AD B2C pode facilitar a recolha da informação do utilizador durante o registo ou edição de perfis e, em seguida, entregar esses dados para o sistema externo. Em seguida, durante futuras autenticações, o Azure AD B2C pode recuperar os dados do sistema externo e, se necessário, incluí-los como parte da resposta simbólica de autenticação que envia para a sua aplicação.

## <a name="progressive-profiling"></a>Perfis progressivos

Outra opção de viagem de utilizador inclui perfis progressivos. O perfil progressivo permite que os seus clientes completem rapidamente a sua primeira transação recolhendo uma quantidade mínima de informação. Em seguida, gradualmente, recolher mais dados de perfil do cliente em futuras insessão.

![Uma representação visual do perfil progressivo](./media/overview/scenario-progressive.png)

## <a name="third-party-identity-verification-and-proofing"></a>Verificação e comprovação de identidade de terceiros

Utilize o Azure AD B2C para facilitar a verificação de identidade e a revisão através da recolha de dados dos utilizadores e, em seguida, passe-os para um sistema de terceiros para efetuar validação, pontuação de confiança e aprovação para a criação de conta de utilizador.

![Um diagrama que mostra o fluxo do utilizador para a comprovação de identidade de terceiros](./media/overview/scenario-idproofing.png)

Estas são apenas algumas das coisas que você pode fazer com Azure AD B2C como sua plataforma de identidade entre negócios e cliente. As seguintes secções desta visão geral acompanham-no através de uma aplicação de demonstração que utiliza o Azure AD B2C. Você também é bem-vindo para passar diretamente para uma visão técnica mais aprofundada [de Azure AD B2C](technical-overview.md).

## <a name="example-woodgrove-groceries"></a>Exemplo: Mercearias WoodGrove

[WoodGrove Groceries][woodgrove] é uma aplicação web ao vivo criada pela Microsoft para demonstrar várias funcionalidades Azure AD B2C. As próximas secções analisam algumas das opções de autenticação fornecidas pelo Azure AD B2C ao site da WoodGrove.

### <a name="business-overview"></a>Visão geral do negócio

A WoodGrove é uma mercearia online que vende mercearias a consumidores individuais e clientes empresariais. Os seus clientes comerciais compram mantimentos em nome da sua empresa, ou negócios que gerem.

### <a name="sign-in-options"></a>Opções de início de sessão

A WoodGrove Mercearia oferece várias opções de inscrição com base na relação que os seus clientes têm com a loja:

* Os clientes **individuais** podem inscrever-se ou inscrever-se com contas individuais, como por exemplo com um fornecedor de identidade social ou um endereço de e-mail e senha.
* Os clientes **empresariais** podem inscrever-se ou inscrever-se com as suas credenciais empresariais.
* **Parceiros** e fornecedores são indivíduos que fornecem à mercearia produtos para vender. A identidade do parceiro é fornecida pela [Azure Ative Directory B2B](../active-directory/external-identities/what-is-b2b.md).

![Páginas individuais (B2C), negócios (B2C) e parceiro (B2B)](./media/overview/woodgrove-overview.png)

### <a name="authenticate-individual-customers"></a>Autenticar clientes individuais

Quando um cliente seleciona **Iniciar sessão com a sua conta pessoal,** são redirecionados para uma página de sessão personalizada hospedada pelo Azure AD B2C. Pode ver na imagem seguinte que personalizamos a interface de utilizador (UI) para parecer e sentir-se como o site woodgrove mercearias. Os clientes da WoodGrove devem desconhecer que a experiência de autenticação é hospedada e assegurada pela Azure AD B2C.

![Página de sessão personalizada WoodGrove hospedada por Azure AD B2C](./media/overview/sign-in.png)

A WoodGrove permite que os seus clientes se inscrevam e se inscrevam utilizando as suas contas Google, Facebook ou Microsoft como seu fornecedor de identidade. Ou podem inscrever-se usando o seu endereço de e-mail e uma palavra-passe para criar o que é chamado de *conta local.*

Quando um cliente **seleciona Inscrever-se na sua conta pessoal** e, em seguida, **inscrever-se agora,** eles são apresentados com uma página de inscrição personalizada.

![Página de inscrição personalizada woodGrove hospedada por Azure AD B2C](./media/overview/sign-up.png)

Depois de introduzir um endereço de e-mail e selecionar **Enviar código de verificação,** o Azure AD B2C envia-lhes o código. Assim que introduzirem o seu código, **selecione Verificar** código , e, em seguida, introduzir as outras informações no formulário, também devem concordar com os termos de serviço.

Clicar no botão **Criar** faz com que o Azure AD B2C redirecione o utilizador de volta para o website da WoodGrove. Quando redireciona, o Azure AD B2C passa um token de autenticação OpenID Connect para a aplicação web WoodGrove. O utilizador está agora inscrito e pronto para ir, o seu nome de exibição mostrado no canto superior direito para indicar que estão inscritos.

![Cabeçalho do site WoodGrove Mercearias mostrando o utilizador é assinado em](./media/overview/signed-in-individual.png)

### <a name="authenticate-business-customers"></a>Autenticar clientes empresariais

Quando um cliente seleciona uma das opções nos **clientes Business,** o website WoodGrove Mercearias invoca uma política Azure AD B2C diferente do que para os clientes individuais.

Esta política oferece ao utilizador a opção de utilizar as suas credenciais corporativas para inscrição e inscrição. No exemplo woodgrove, os utilizadores são solicitados a fazer sôms com qualquer trabalho ou conta escolar. Esta política utiliza uma [aplicação AD Azure multi-arrendatário](../active-directory/develop/howto-convert-app-to-be-multi-tenant.md) e o `/common` ponto final Azure AD para federar Azure AD B2C com qualquer cliente Microsoft 365 no mundo.

### <a name="authenticate-partners"></a>Autenticar parceiros

O **'Iniciar s-in' com o link** da sua conta de fornecedor utiliza a funcionalidade de colaboração do Azure Ative Directory B2B. Azure AD B2B é uma família de funcionalidades no Azure Ative Directory para gerir identidades de parceiros. Essas identidades podem ser federadas do Azure Ative Directory para acesso a aplicações protegidas aZURE AD B2C.

Saiba mais sobre o Azure AD B2B em [Qual é o acesso do utilizador convidado no Azure Ative Directory B2B?](../active-directory/external-identities/what-is-b2b.md)

<!-- UNCOMMENT WHEN REPO IS UPDATED WITH LATEST DEMO CODE
### Sample code

If you'd like to jump right into the code to see how the WoodGrove Groceries application is built, you can find the repository on GitHub:

[Azure-Samples/active-directory-external-identities-woodgrove-demo][woodgrove-repo] (GitHub)
-->

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma ideia do que é a Azure AD B2C e alguns dos cenários com que pode ajudar, aprofundar um pouco as suas características e aspetos técnicos.

> [!div class="nextstepaction"]
> [Visão geral técnica da Azure AD B2C >](technical-overview.md)

<!-- LINKS - External -->
[woodgrove]: https://aka.ms/ciamdemo
[woodgrove-repo]: https://github.com/Azure-Samples/active-directory-external-identities-woodgrove-demo