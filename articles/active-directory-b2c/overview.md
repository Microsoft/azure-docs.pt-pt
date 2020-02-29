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
ms.openlocfilehash: 61b6d5ea903d00519c58556bc99da7065741a6e3
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184065"
---
# <a name="what-is-azure-active-directory-b2c"></a>O que é o Azure Active Directory B2C?

O Azure Ative Directory B2C fornece a identidade entre negócios e clientes como serviço. Os seus clientes utilizam as suas identidades sociais, empresariais ou locais preferenciais para obter acesso único às suas aplicações e APIs.

![Infográfico de fornecedores de identidade Azure AD B2C e aplicações a jusante](./media/overview/azureadb2c-overview.png)

O Azure Ative Directory B2C (Azure AD B2C) é uma solução de gestão de acesso à identidade do cliente (CIAM) capaz de suportar milhões de utilizadores e milhares de milhões de autenticações por dia. Trata da escala e segurança da plataforma de autenticação, monitorizando e manuseando automaticamente ameaças como negação de serviço, spray de senha ou ataques de força bruta.

## <a name="custom-branded-identity-solution"></a>Solução de identidade de marca personalizada

Azure AD B2C é uma solução de autenticação de rótulo branco. Pode personalizar toda a experiência do utilizador com a sua marca para que se misture perfeitamente com as suas aplicações web e móveis.

Personalize todas as páginas apresentadas pelo Azure AD B2C quando os seus utilizadores se inscreverem, iniciarem sessão e modificarem as suas informações de perfil. Personalize o HTML, CSS e JavaScript nas suas viagens de utilizador para que a experiência Azure AD AD B2C pareça e pareça uma parte nativa da sua aplicação.

![Páginas de inscrição personalizadae e de inscrição e imagem de fundo](./media/overview/sign-in-small.png)

## <a name="single-sign-on-access-with-a-user-provided-identity"></a>Acesso único de acesso ao acesso ao acesso com uma identidade fornecida pelo utilizador

O Azure AD B2C utiliza protocolos de autenticação baseados em padrões, incluindo OpenID Connect, OAuth 2.0 e SAML. Integra-se com a maioria das aplicações modernas e software comercial fora da prateleira.

![Diagrama de identidades de terceiros federando-se para Azure AD B2C](./media/overview/scenario-singlesignon.png)

Ao servir como a autoridade central de autenticação para as suas aplicações web, aplicações móveis e APIs, o Azure AD B2C permite-lhe construir uma única solução de inscrição (SSO) para todos eles. Centralize a recolha de informações sobre o perfil do utilizador e a preferência e capture análises detalhadas sobre o comportamento de inscrição e conversão de inscrições.

## <a name="integrate-with-external-user-stores"></a>Integrar com lojas de utilizadores externos

O Azure AD B2C fornece um diretório que pode conter 100 atributos personalizados por utilizador. No entanto, também pode integrar-se com sistemas externos. Por exemplo, utilize o Azure AD B2C para autenticação, mas delege uma gestão externa da relação com o cliente (CRM) ou uma base de dados de fidelização de clientes como fonte de verdade para os dados do cliente.

Outro cenário externo de loja de utilizadores é ter o Azure AD B2C a tratar da autenticação para a sua aplicação, mas integrar-se com um sistema externo que armazena o perfil do utilizador ou dados pessoais. Por exemplo, satisfazer os requisitos de residência de dados, como as políticas regionais ou no local de armazenamento de dados.

![Um diagrama lógico de Azure AD B2C comunicando com uma loja de utilizadores externo](./media/overview/scenario-remoteprofile.png)

O Azure AD B2C pode facilitar a recolha da informação do utilizador durante o registo ou a edição de perfis e, em seguida, entregar esses dados ao sistema externo. Em seguida, durante futuras autenticações, o Azure AD B2C pode recuperar os dados do sistema externo e, se necessário, incluí-lo como parte da resposta simbólica de autenticação que envia para a sua aplicação.

## <a name="progressive-profiling"></a>Perfis progressivos

Outra opção de viagem do utilizador inclui perfis progressivos. O perfil progressivo permite que os seus clientes completem rapidamente a sua primeira transação recolhendo uma quantidade mínima de informação. Em seguida, recolha gradualmente mais dados de perfil do cliente em futuras inscrições.

![Uma representação visual do perfil progressivo](./media/overview/scenario-progressive.png)

## <a name="third-party-identity-verification-and-proofing"></a>Verificação e revisão de identidade de terceiros

Utilize o Azure AD B2C para facilitar a verificação e a comprovação de identidade, recolhendo dados dos utilizadores, passando-os depois para um sistema de terceiros para efetuar validação, pontuação de confiança e aprovação para criação de conta de utilizador.

![Um diagrama que mostra o fluxo do utilizador para a impermeabilização de identidade de terceiros](./media/overview/scenario-idproofing.png)

Estas são apenas algumas das coisas que pode fazer com o Azure AD B2C como a sua plataforma de identidade negócio-cliente. As seguintes secções desta visão geral passam por si através de uma aplicação de demonstração que utiliza O Azure AD B2C. Também é bem-vindo a seguir diretamente para uma visão técnica mais aprofundada [do Azure AD B2C](technical-overview.md).

## <a name="example-woodgrove-groceries"></a>Exemplo: Mercearias WoodGrove

[WoodGrove Mercearias][woodgrove] é uma aplicação web ao vivo criada pela Microsoft para demonstrar várias funcionalidades Azure AD B2C. As próximas secções analisam algumas das opções de autenticação fornecidas pelo Azure AD B2C ao website da WoodGrove.

### <a name="business-overview"></a>Visão geral do negócio

A WoodGrove é uma mercearia online que vende mantimentos tanto a consumidores individuais como a clientes empresariais. Os seus clientes comerciais compram mantimentos em nome da sua empresa, ou empresas que gerem.

### <a name="sign-in-options"></a>Opções de início de sessão

A WoodGrove Mercearias oferece várias opções de inscrição com base na relação que os seus clientes têm com a loja:

* Os clientes **individuais** podem inscrever-se ou inscrever-se em contas individuais, como por exemplo com um fornecedor de identidade social ou um endereço de e-mail e senha.
* Os clientes **empresariais** podem inscrever-se ou inscrever-se com as suas credenciais empresariais.
* **Parceiros** e fornecedores são indivíduos que fornecem à mercearia produtos para vender. A identidade do parceiro é fornecida pelo [Azure Ative Directory B2B](../active-directory/b2b/what-is-b2b.md).

![Páginas de inscrição individuais (B2C), empresas (B2C) e parceira (B2B)](./media/overview/woodgrove-overview.png)

### <a name="authenticate-individual-customers"></a>Autenticar clientes individuais

Quando um cliente seleciona Iniciar sessão com a **sua conta pessoal,** são redirecionados para uma página de entrada personalizada hospedada pelo Azure AD B2C. Pode ver na seguinte imagem que personalizamos a interface de utilizador (UI) para parecer e sentir-se como o site da Mercearia WoodGrove. Os clientes da WoodGrove deverão desconhecer que a experiência de autenticação é hospedada e protegida pelo Azure AD B2C.

![Página de inscrição personalizada do WoodGrove hospedada pelo Azure AD B2C](./media/overview/sign-in.png)

A WoodGrove permite que os seus clientes se inscrevam e inscrevam-se utilizando as suas contas Google, Facebook ou Microsoft como fornecedorde identidade. Ou podem inscrever-se usando o seu endereço de e-mail e uma palavra-passe para criar o que é chamado de *conta local*.

Quando um cliente seleciona **Inscreva-se na sua conta pessoal** e, em seguida, **inscreva-se agora,** é apresentado com uma página de inscrição personalizada.

![Página de inscrição personalizada do WoodGrove hospedada pelo Azure AD B2C](./media/overview/sign-up.png)

Depois de introduzir um endereço de e-mail e selecionar **Enviar código de verificação,** o Azure AD B2C envia-lhes o código. Assim que introduzirem o seu código, selecione **'Verificar código'** e, em seguida, introduzir as outras informações no formulário, também devem concordar com os termos de serviço.

Clicar no botão **Criar** faz com que o Azure AD B2C redirecione o utilizador de volta para o website das mercearias WoodGrove. Quando redireciona, o Azure AD B2C passa um símbolo de autenticação OpenID Connect para a aplicação web WoodGrove. O utilizador está agora inscrito e pronto para partir, o seu nome de exibição mostrado no canto superior direito para indicar que está inscrito.

![Cabeçalho do site da WoodGrove Mercearias mostrando o utilizador está assinado em](./media/overview/signed-in-individual.png)

### <a name="authenticate-business-customers"></a>Autenticar clientes empresariais

Quando um cliente seleciona uma das opções sob **clientes empresariais,** o site da WoodGrove Mercearias invoca uma política Azure AD B2C diferente da que faz para os clientes individuais.

Esta política apresenta ao utilizador a opção de utilizar as suas credenciais corporativas para o inscrição e inscrição. No exemplo do WoodGrove, os utilizadores são solicitados a iniciar sessão com qualquer conta ad 365 ou Azure. Esta política utiliza uma [aplicação Azure AD multi-inquilino](../active-directory/develop/howto-convert-app-to-be-multi-tenant.md) e o ponto final da `/common` Azure AD para federar o Azure AD B2C com qualquer cliente do Office 365 no mundo.

### <a name="authenticate-partners"></a>Parceiros autenticados

O **Signin** e o link de conta do fornecedor utilizam a funcionalidade de colaboração do Azure Ative Directory B2B. O Azure AD B2B é uma família de funcionalidades no Azure Ative Directory para gerir identidades de parceiros. Essas identidades podem ser federadas a partir do Diretório Ativo Azure para acesso a aplicações protegidas por Azure AD B2C.

Saiba mais sobre o Azure AD B2B no [Que é o acesso dos utilizadores convidados no Azure Ative Directory B2B?](../active-directory/b2b/what-is-b2b.md)

<!-- UNCOMMENT WHEN REPO IS UPDATED WITH LATEST DEMO CODE
### Sample code

If you'd like to jump right into the code to see how the WoodGrove Groceries application is built, you can find the repository on GitHub:

[Azure-Samples/active-directory-external-identities-woodgrove-demo][woodgrove-repo] (GitHub)
-->

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma ideia do que é o Azure AD B2C e alguns dos cenários com que pode ajudar, aprofundar um pouco mais as suas características e aspetos técnicos.

> [!div class="nextstepaction"]
> [Visão geral técnica Azure AD B2C >](technical-overview.md)

<!-- LINKS - External -->
[woodgrove]: https://aka.ms/ciamdemo
[woodgrove-repo]: https://github.com/Azure-Samples/active-directory-external-identities-woodgrove-demo
