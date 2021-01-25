---
title: Publique a sua app na galeria de aplicações Azure Ative Directory
description: Saiba como listar uma aplicação que suporta um único s-on na galeria de aplicações Azure Ative Directory.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 12/14/2020
ms.author: kenwith
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 8f5324e967c5a67ee77559087a02c3421611d8f3
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98752864"
---
# <a name="publish-your-app-to-the-azure-ad-app-gallery"></a>Publique a sua app na galeria de aplicações AZure AD

Pode publicar a sua aplicação na galeria de aplicações Azure AD. Quando a sua aplicação for publicada, ela aparecerá como uma opção para os clientes quando estiverem a adicionar aplicações ao seu inquilino. 

Alguns dos benefícios de adicionar a sua app à galeria Azure AD incluem:

- Os clientes encontram a melhor experiência de sinsu máximo possível para a sua aplicação.
- A configuração da aplicação é simples e mínima.
- Uma pesquisa rápida encontra a sua aplicação na galeria.
- Os clientes AD Azure gratuitos, básicos e premium podem utilizar esta integração.
- Os clientes mútuos obtêm um tutorial de configuração passo a passo.

Além disso, existem muitos benefícios quando os seus clientes usam o Azure AD como fornecedor de identidade para a sua aplicação. Algumas delas incluem:

- Forneça um único sinal de inscrição para os seus utilizadores. Com o SSO reduz os custos de suporte, facilitando aos seus clientes uma única sina. Se o SSO de um clique estiver ativado, os Administradores de TI dos seus clientes não têm de aprender a configurar a sua aplicação para utilização na sua organização. Para saber mais sobre um único sign-on, veja [o que é um único sign-on?](../manage-apps/what-is-single-sign-on.md)
- A sua aplicação pode ser detetada na Microsoft 365 App Gallery, no Microsoft 365 App Launcher e no Microsoft Search on Office.com. 
- Gestão integrada de aplicativos. Para saber mais sobre a gestão de aplicações em Azure AD, veja [o que é a gestão de aplicações?](../manage-apps/what-is-application-management.md)
- A sua aplicação pode usar a [API do Gráfico](/graph/) para aceder aos dados que impulsionam a produtividade dos utilizadores no ecossistema da Microsoft.
- Documentação específica da aplicação coproduzida com a equipa Azure AD para os nossos clientes mútuos facilita a adoção.
- Fornece aos seus clientes a capacidade de gerir completamente a autenticação e autorização das identidades dos seus colaboradores e convidados.
- Colocar toda a gestão de conta e conformidade com o proprietário dessas identidades.
- Fornecendo capacidade para permitir ou desativar SSO para fornecedores de identidade específicos, grupos ou utilizadores para atender às suas necessidades de negócio.
- Aumenta a sua capacidade de comercialização e adobilidade. Muitas grandes organizações exigem que (ou aspiram) os seus colaboradores têm experiências SSO perfeitas em todas as aplicações. Tornar o SSO fácil é importante.
- Reduz o atrito do utilizador final, o que pode aumentar o uso do utilizador final e aumentar as suas receitas.
- Os clientes que utilizam o Sistema de Gestão de Identidade de Domínio Cruzado[(SCIM)](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)podem utilizar o provisionamento para a mesma aplicação.
- Adicione segurança e comodidade quando os utilizadores assinarem as aplicações utilizando o Azure AD SSO e eliminando a necessidade de credenciais separadas.

> [!TIP]
> Quando oferece o seu pedido de utilização por outras empresas através de uma compra ou subscrição, disponibiliza a sua aplicação aos clientes dentro dos seus próprios inquilinos Azure. Isto é conhecido como criar uma aplicação multi-inquilino. Para uma visão geral deste conceito, consulte [Aplicações Multitenant em Azure](../../dotnet-develop-multitenant-applications.md) e [Tenancy no Azure Ative Directory](single-and-multi-tenant-apps.md).

> [!IMPORTANT]
> Para publicar a sua aplicação na galeria Azure AD deve concordar com termos e condições específicos. Antes de começar, certifique-se de ler e concordar com os [termos e condições](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/).

Os passos para a publicação da sua aplicação na galeria de aplicações AZure AD são:
1. Escolha o padrão de inscrição único certo para a sua aplicação.
2. Implemente um único sinal de s-on na sua aplicação.
3. Crie o seu inquilino Azure e teste a sua aplicação.
4. Criar e publicar documentação.
5. Envie a sua aplicação.
6. Junte-se à rede de parceiros da Microsoft.

## <a name="what-is-the-azure-ad-application-gallery"></a>O que é a galeria de aplicações AD AZure?

- Os clientes encontram a melhor experiência possível de inscrição única.
- A configuração da aplicação é simples e mínima.
- Uma pesquisa rápida encontra a sua aplicação na galeria.
- Os clientes AD Azure gratuitos, básicos e premium podem utilizar esta integração.
- Os clientes mútuos obtêm um tutorial de configuração passo a passo.
- Os clientes que utilizam o Sistema de Gestão de Identidade de Domínio Cruzado[(SCIM)](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)podem utilizar o provisionamento para a mesma aplicação.

## <a name="prerequisites"></a>Pré-requisitos

Precisa de uma conta permanente para testes com pelo menos dois utilizadores registados.

- Para aplicações federadas (Open ID e SAML/WS-Fed), a aplicação deve apoiar o modelo software-as-a-service (SaaS) para ser listado na galeria de aplicações AD Azure. As aplicações da galeria da empresa devem suportar múltiplas configurações de clientes e não qualquer cliente específico.
- Para o Open ID Connect, a aplicação deve ser multitendida e o [quadro de consentimento Azure AD](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) deve ser devidamente implementado para a aplicação. O utilizador pode enviar o pedido de inscrição para um ponto final comum para que qualquer cliente possa fornecer consentimento para a aplicação. Pode controlar o acesso do utilizador com base no ID do inquilino e na UPN do utilizador recebida no token.
- Para a SAML 2.0/WS-Fed, a sua aplicação deve ter a capacidade de fazer a integração SSO SAML/WS-Fed no modo SP ou IDP. Certifique-se de que esta capacidade está a funcionar corretamente antes de submeter o pedido.
- Para a palavra-passe SSO, certifique-se de que a sua aplicação suporta a autenticação do formulário para que a abóbada de senha possa ser feita para obter um único sinal de acesso ao funcionamento como esperado.
- Precisa de uma conta permanente para testes com pelo menos dois utilizadores registados.

**Como obter Azure AD para desenvolvedores?**

Você pode obter uma conta de teste gratuita com todas as funcionalidades premium AZure AD - 90 dias grátis e pode ser estendida desde que faça dev trabalhar com ele: https://docs.microsoft.com/office/developer-program/office-365-developer-program

## <a name="step-1---choose-the-right-single-sign-on-standard-for-your-app"></a>Passo 1 - Escolha o padrão de inscrição único certo para a sua aplicação

Para listar uma aplicação na galeria de aplicações AZure AD, é necessário implementar pelo menos uma das opções de inscrição única suportadas. Para compreender as opções de inscrição única, e como os clientes as configurarão em AD Azure, consulte [as opções SSO](../manage-apps/sso-options.md).

A tabela seguinte compara os principais padrões: Open Authentication 2.0 (OAuth 2.0) com OpenID Connect (OIDC), Security Assertion Markup Language (SAML) e Web Services Federation (WS-Fed).

| Funcionalidade| OAuth / OIDC| SAML / WS-Fed |
| - |-|-|
| Sign-on único baseado na Web| √| √ |
| Sign-out único baseado na Web| √| √ |
| Sign-on único baseado em dispositivos móveis| √| √* |
| Sign-out único baseado em dispositivos móveis| √| √* |
| Políticas de acesso condicional para aplicações móveis| √| √* |
| Experiência de MFA sem emenda para aplicações móveis| √| √* |
| Provisão SCIM| √| √ |
| Aceder ao Gráfico da Microsoft| √| X |

*Possível, mas a Microsoft não fornece amostras ou orientação.

### <a name="oauth-20-and-openid-connect"></a>OAuth 2.0 e OpenID Connect
OAuth 2.0 é um protocolo [padrão da indústria](https://oauth.net/2/) para autorização. OpenID Connect (OIDC) é uma camada de autenticação de identidade [padrão](https://openid.net/connect/) da indústria construída em cima do protocolo OAuth 2.0. 

**Razões para escolher OAuth/OIDC**
- A autorização inerente a estes protocolos permite que a sua aplicação aceda e integre com dados de utilizador e organizacionais ricos através da API do Gráfico da Microsoft.
- Simplifica a experiência do utilizador final dos seus clientes ao adotar sSO para a sua aplicação. Pode definir facilmente os conjuntos de permissão necessários, que são então automaticamente representados ao administrador ou ao consentimento do utilizador final.
- A utilização destes protocolos permite que os seus clientes utilizem políticas de Acesso Condicional e Autenticação Multi-Factor (MFA) para controlar o acesso às aplicações. 
- A Microsoft fornece bibliotecas e [amostras de código em várias plataformas tecnológicas](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) para ajudar no seu desenvolvimento.  

**Algumas coisas a considerar**
- Se já implementou um único sinal de saturação baseado em SAML para a sua aplicação, então talvez não queira implementar um novo padrão para obter a sua aplicação na galeria.

### <a name="saml-20-or-ws-fed"></a>SAML 2.0 ou WS-Fed

O SAML é um [padrão de inscrição único](https://www.oasis-open.org/standards#samlv2.0) maduro e amplamente adotado para aplicações web. Para saber mais sobre como a Azure utiliza o SAML, veja [como o Azure utiliza o protocolo SAML](active-directory-saml-protocol-reference.md). 

A Web Services Federation (WS-Fed) é um [padrão](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) da indústria geralmente utilizado para aplicações web que são desenvolvidas usando a plataforma .NET.

**Razões para escolher o SAML**
- SAML 2.0 é um padrão maduro e a maioria das plataformas tecnológicas suportam bibliotecas de código aberto para SAML 2.0. 
- Pode fornecer aos seus clientes uma interface de administração para configurar a SAML SSO. Podem configurar o SAML SSO para o Microsoft Azure AD e qualquer outro fornecedor de identidade que suporte a SAML.

**Algumas coisas a considerar**
- Ao utilizar protocolos SAML 2.0 ou WSFed para aplicações móveis, certas políticas de Acesso Condicional, incluindo a Autenticação Multi-factor (MFA) terão uma experiência degradada.
- Se quiser aceder ao Gráfico do Microsoft, terá de implementar a autorização através da OAuth 2.0 para gerar fichas necessárias. 

### <a name="password-based"></a>Baseada em palavra-passe
O SSO baseado em palavras-passe, também referido como cofre de palavras-passe, permite-lhe gerir o acesso ao utilizador e palavras-passe a aplicações web que não suportam a federação de identidade. Também é útil para cenários em que vários utilizadores precisam de partilhar uma única conta, como é o caso das contas de aplicações da sua organização nas redes sociais.


## <a name="step-2---implement-single-sign-on-in-your-app"></a>Passo 2 - Implementar um único sinal de inscrição na sua app
Todas as aplicações na galeria devem implementar uma das opções de inscrição única suportadas. Para saber mais sobre as opções suportadas, consulte as [opções SSO](../manage-apps/sso-options.md).

Para o OAuth e o OIDC, consulte [orientações sobre padrões de autenticação](v2-app-types.md) e [amostras de código de diretório ativo Azure](sample-v2-code.md).

Para a SAML e a WS-Fed, a sua aplicação deve ter a capacidade de fazer integração SSO no modo SP ou IDP. Certifique-se de que esta capacidade está a funcionar corretamente antes de submeter o pedido.

Para saber mais sobre a autenticação, veja [o que é a autenticação?](../azuread-dev/v1-authentication-scenarios.md)

> [!IMPORTANT]
> Para aplicações federadas (OpenID e SAML/WS-Fed), a aplicação deve apoiar o Software como modelo de Serviço (SaaS). As aplicações de galeria AZure AD devem suportar várias configurações de clientes e não devem ser específicas de qualquer cliente.

### <a name="implement-oauth-20-and-openid-connect"></a>Implementar OAuth 2.0 e OpenID Connect

Para o OpenID Connect, a aplicação deve ser multi-arrendada e o quadro de [consentimento Azure AD](consent-framework.md) deve ser devidamente implementado para a aplicação. O utilizador pode enviar o pedido de inscrição para um ponto final comum para que qualquer cliente possa fornecer consentimento para a aplicação. Pode controlar o acesso do utilizador com base no ID do inquilino e na UPN do utilizador recebida no token.

Para rever exemplos específicos, consulte as amostras de código da plataforma de identidade da [Microsoft.](sample-v2-code.md) 

Para rever exemplos específicos móveis, consulte: 
* [Android](quickstart-v2-android.md)
* [iOS](quickstart-v2-ios.md)
* [Plataforma Universal do Windows](quickstart-v2-uwp.md)

### <a name="implement-saml-20"></a>Implementar SAML 2.0

Se a sua aplicação suportar SAML 2.0, pode integrá-la diretamente com um inquilino AZure AD. Para saber mais sobre a configuração SAML com Azure AD, consulte o [sign-on único baseado em Configure SAML](../manage-apps/configure-saml-single-sign-on.md).

A Microsoft não fornece, nem recomenda, bibliotecas para implementações SAML. Há muitas bibliotecas de código aberto disponíveis.

### <a name="implement-ws-fed"></a>Implementar WS-Fed
Para saber mais sobre WS-Fed no ASP.NET Core, consulte [os utilizadores autenticados com WS-Federation no ASP.NET Core](/aspnet/core/security/authentication/ws-federation).

### <a name="implement-password-vaulting"></a>Implementar abóbada de senha

Crie uma aplicação web que tenha uma página de sessão html. Certifique-se de que a sua aplicação suporta a autenticação do formulário para que a abóbada de senha possa ser feita para obter um único sinal de acesso ao trabalho como esperado.

## <a name="step-3---implement-scim-user-provisioning-in-your-app"></a>Passo 3 - Implementar o provisionamento do utilizador SCIM na sua app
Apoiar o fornecimento [scim](https://aka.ms/scimoverview) é um passo opcional, mas altamente recomendado, na construção da sua aplicação. O suporte ao padrão SCIM é fácil de fazer e permite que os clientes criem e atualizem automaticamente as contas dos utilizadores na sua aplicação, sem depender de processos manuais como o upload de ficheiros CSV. Além disso, os clientes podem automatizar a remoção dos utilizadores e manter os membros do grupo em sincronização, o que não pode ser realizado com uma solução como a SAML JIT. 

### <a name="learn-about-scim"></a>Saiba mais sobre o SCIM
Para saber mais sobre os padrões e benefícios do SCIM para os seus clientes, consulte [o fornecimento com o SCIM - a começar.](https://aka.ms/scimoverview)

### <a name="understand-the-azure-ad-scim-implementation"></a>Compreender a implementação do Azure AD SCIM
Para saber mais sobre a implementação do Azure AD SCIM, consulte [a construção de um ponto final SCIM e configuure o fornecimento do utilizador com a Ad AZure](../app-provisioning/use-scim-to-provision-users-and-groups.md).

### <a name="implement-scim"></a>Implementar SCIM
A Azure AD fornece código de [referência](https://aka.ms/scimoverview) para ajudá-lo a construir um ponto final SCIM. Há também muitas bibliotecas /referências de terceiros que você pode encontrar no GitHub.  

## <a name="step-4---create-your-azure-tenant-and-test-your-app"></a>Passo 4 - Crie o seu inquilino Azure e teste a sua app

Você precisará de um inquilino AZure AD para testar a sua app. Para configurar o seu ambiente de desenvolvimento, consulte [Quickstart: Crie um inquilino.](quickstart-create-new-tenant.md)

Em alternativa, um inquilino AZure AD vem com cada subscrição da Microsoft 365. Para configurar um ambiente de desenvolvimento gratuito do Microsoft 365, consulte [o Programa de Desenvolvimento microsoft 365](/office/developer-program/microsoft-365-developer-program).

Uma vez que você tem um inquilino, você precisa testar um único sinal e [provisionamento](../app-provisioning/use-scim-to-provision-users-and-groups.md#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client). 

**Para aplicações OIDC ou Juramento,** [Registe a sua candidatura](quickstart-register-app.md) como uma aplicação multi-arrendatário. Selecione as Contas em qualquer diretório organizacional e opção de contas pessoais da Microsoft em tipos de Conta Suportada.

**Para aplicações baseadas em SAML e WS-Fed,** você configura aplicações [únicas baseadas em SAML](../manage-apps/configure-saml-single-sign-on.md) usando um modelo de SAML genérico em Azure AD.

Você também pode [converter um pedido de inquilino único para multi-inquilino,](howto-convert-app-to-be-multi-tenant.md) se necessário.


## <a name="step-5---create-and-publish-documentation"></a>Passo 5 - Criar e publicar documentação

### <a name="documentation-on-your-site"></a>Documentação no seu site

A facilidade de adoção é um fator significativo nas decisões de software da empresa. A documentação clara e fácil de seguir suporta os seus clientes na sua viagem de adoção e reduz os custos de suporte. Trabalhando com milhares de fornecedores de software, a Microsoft viu o que funciona.

Recomendamos que a sua documentação no seu site inclua, no mínimo, os seguintes itens.

* Introdução à sua funcionalidade SSO
  * Protocolos suportados
  * Versão e SKU
  * Lista de Fornecedores de Identidade Suportados com links de documentação
* Informações de licenciamento para o seu pedido
* Controlo de acesso baseado em funções para configurar sSO
* Passos de configuração SSO
  * Elementos de configuração de UI para SAML com valores esperados do fornecedor
  * Informações do prestador de serviços a passar aos fornecedores de identidade
* Se OIDC/OAuth
  * Lista de permissões necessárias para consentimento com justificações empresariais
* Etapas de teste para utilizadores-piloto
* Informações de resolução de problemas, incluindo códigos de erro e mensagens
* Mecanismos de apoio aos clientes
* Detalhes sobre o seu ponto final SCIM, incluindo os recursos e atributos suportados

### <a name="documentation-on-the-microsoft-site"></a>Documentação no Site da Microsoft

Ao listar a sua aplicação na Azure Ative Directory Application Gallery, que também publica a sua aplicação no Azure Marketplace, a Microsoft gerará documentação para os nossos clientes mútuos explicando o processo passo a passo. Pode ver um exemplo [aqui.](../saas-apps/tutorial-list.md) Esta documentação é criada com base na sua submissão à galeria, e pode atualizá-la facilmente se estoissar a sua aplicação utilizando a sua conta GitHub.


## <a name="step-6---submit-your-app"></a>Passo 6 - Submeter a sua app

Depois de ter testado que a integração da sua aplicação funciona com a Azure AD, submeta o seu pedido de candidatura no [portal da Rede de Aplicações](https://microsoft.sharepoint.com/teams/apponboarding/Apps)da Microsoft .

A primeira vez que tentar entrar no portal será apresentado com um de dois ecrãs. 

Se receber a mensagem "Isso não funcionou" então terá de contactar a Equipa de [Integração SSO da Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Forneça a conta de e-mail que pretende utilizar para submeter o pedido. Um endereço de e-mail de negócios como `name@yourbusiness.com` é preferível. A equipa Azure AD adicionará a conta no portal da Rede de Aplicações da Microsoft.

Se vir uma página de "Acesso a Pedidos", preencha a justificação do negócio e selecione **'Acesso ao Pedido'.**

Após a adição da conta, pode iniciar sômissão no portal da Rede de Aplicações da Microsoft e submeter o pedido selecionando o azulejo do **Pedido de Submissão (ISV)** na página inicial.

![Enviar azulejos do Pedido (ISV) na página inicial](./media/howto-app-gallery-listing/homepage.png)

### <a name="issues-on-logging-into-portal"></a>Problemas no login no portal

Se estiver a ver este erro durante o início de sessão, então aqui estão os detalhes sobre o assunto e é assim que pode corrigi-lo.

* Se o seu sinal de sposição foi bloqueado como mostrado abaixo:

  ![questões que resolvem aplicação na galeria](./media/howto-app-gallery-listing/blocked.png)

**O que está a acontecer:**

O utilizador convidado é federado a um inquilino doméstico que também é um AD Azure. O utilizador convidado está em alto risco. A Microsoft não permite que utilizadores de alto risco acedam aos seus recursos. Todos os utilizadores de alto risco (empregados ou hóspedes/fornecedores) devem remediar/fechar o seu risco de aceder aos recursos da Microsoft. Para os utilizadores convidados, este risco de utilizador provém do arrendatário doméstico e a política provém do inquilino de recursos (Microsoft neste caso).
 
**Soluções seguras:**

* Os utilizadores de hóspedes registados no MFA remediam o seu próprio risco de utilizador. Isto pode ser feito pelo utilizador convidado que executa uma alteração de senha garantida ou reset https://aka.ms/sspr) (no seu inquilino de casa (isto precisa de MFA e SSPR no inquilino doméstico). A alteração ou reset de palavra-passe garantido deve ser iniciada no Ad Azure e não no pré-pré.

* Os utilizadores convidados têm os seus administradores a remediar o seu risco. Neste caso, o administrador realizará um reset de palavra-passe (geração temporária de palavra-passe). Isto não precisa de proteção de identidade. O administrador do utilizador convidado pode ir e clicar em 'Redefinir a https://aka.ms/RiskyUsers palavra-passe'.

* Os utilizadores convidados têm os seus administradores próximos /dispensam o seu risco. Mais uma vez, isto não precisa de proteção de identidade. O administrador pode ir https://aka.ms/RiskyUsers e clicar em 'Dispensar o risco do utilizador'. No entanto, a administração deve fazer as devidas diligências para garantir que se tratava de uma falsa avaliação de risco positivo antes de fechar o risco do utilizador. Caso contrário, estão a colocar os seus recursos e os recursos da Microsoft em risco, suprimindo uma avaliação de risco sem investigação.

> [!NOTE]
> Se tiver algum problema de acesso, contacte a [Equipa de Integração SSO da Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

### <a name="implementation-specific-options"></a>Opções específicas de implementação
Se pretender adicionar a sua aplicação à lista na galeria utilizando o OpenID Connect, selecione **OpenID Connect & OAuth 2.0** como mostrado.

![Listagem de uma aplicação OpenID Connect na galeria](./media/howto-app-gallery-listing/openid.png)

Se pretender adicionar a sua candidatura à lista na galeria utilizando **SAML 2.0** ou **WS-Fed,** selecione **SAML 2.0/WS-Fed** como mostrado.

![Listando um pedido DE SAML 2.0 ou WS-Fed na galeria](./media/howto-app-gallery-listing/saml.png)

Se pretender adicionar a sua aplicação à lista na galeria utilizando a palavra-passe SSO, selecione **Password SSO (UserName & Password)** como mostrado.

![Listagem de uma aplicação SSO de palavra-passe na galeria](./media/howto-app-gallery-listing/passwordsso.png)

Se estiver a implementar um ponto final [SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) 2.0 para o provisionamento do utilizador, selecione a opção como mostrado. Ao fornecer o esquema no pedido de embarque, siga as instruções [aqui](../app-provisioning/export-import-provisioning-configuration.md) para descarregar o seu esquema. Usaremos o esquema que configuraste ao testar a aplicação não-galeria para construir a aplicação da galeria. 

   ![Pedido de provisionamento de utilizadores](./media/howto-app-gallery-listing/user-provisioning.png)

### <a name="update-or-remove-an-existing-listing"></a>Atualizar ou remover uma listagem existente

Pode atualizar ou remover uma aplicação de galeria existente no portal da [Rede de Aplicações](https://microsoft.sharepoint.com/teams/apponboarding/Apps)da Microsoft.

![Listagem de uma aplicação SAML na galeria](./media/howto-app-gallery-listing/updateorremove.png)

> [!NOTE]
> Se tiver algum problema de acesso, consulte a secção anterior sobre a criação da sua conta. Se isso não funcionar, contacte a [Equipa de Integração SSO da Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

### <a name="list-requests-by-customers"></a>Pedidos de lista por clientes

Os clientes podem submeter um pedido de lista de uma aplicação selecionando **pedidos de App por Clientes**  >  **Enviar novo pedido.**

![Mostra o azulejo de aplicações solicitado pelo cliente](./media/howto-app-gallery-listing/customer-submit-request.png)

Aqui está o fluxo de aplicações solicitadas pelo cliente.

![Mostra o fluxo de aplicações solicitadas pelo cliente](./media/howto-app-gallery-listing/customer-request-2.png)


### <a name="timelines"></a>Linhas cronológicas

A cronologia do processo de listagem de um pedido DE SAML 2.0 ou WS-Fed na galeria é de 7 a 10 dias úteis.

![Cronologia para listar uma aplicação SAML na galeria](./media/howto-app-gallery-listing/timeline.png)

A cronologia do processo de listagem de uma aplicação OpenID Connect na galeria é de 2 a 5 dias úteis.

![Linha do tempo para listar uma aplicação OpenID Connect na galeria](./media/howto-app-gallery-listing/timeline2.png)

### <a name="escalations"></a>Escaladas

Para qualquer escalada, envie um e-mail para a [Equipa de Integração Azure AD SSO](mailto:SaaSApplicationIntegrations@service.microsoft.com), e responderemos o mais rapidamente possível.


## <a name="step-7---join-the-microsoft-partner-network"></a>Passo 7 - Junte-se à rede de parceiros da Microsoft
A Microsoft Partner Network fornece acesso instantâneo a recursos exclusivos, programas, ferramentas e conexões. Para aderir à rede e criar o seu plano de mercado, consulte [os clientes comerciais Reach.](https://partner.microsoft.com/explore/commercial#gtm)


## <a name="next-steps"></a>Próximos passos
* [Construa um ponto final SCIM e configuure o fornecimento de utilizadores](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Cenários de autenticação para Azure AD](authentication-flows-app-scenarios.md)