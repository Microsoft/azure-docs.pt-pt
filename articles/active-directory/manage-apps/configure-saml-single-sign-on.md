---
title: Compreenda o sign-on único baseado em SAML (SSO) para aplicações no Azure Ative Directory
description: Compreenda o sign-on único baseado em SAML (SSO) para aplicações no Azure Ative Directory
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/28/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: f6422c3f751a7aaa430b93ee68ca1a3520ac915f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257697"
---
# <a name="understand-saml-based-single-sign-on"></a>Compreenda o sign-on único baseado em SAML

Na [série quickstart](view-applications-portal.md) sobre gestão de aplicações, aprendeu a usar a Azure AD como Fornecedor de Identidade (IdP) para uma aplicação. Este artigo entra em mais detalhes sobre a opção baseada em SAML para um único sign-on. 


## <a name="before-you-begin"></a>Antes de começar

A utilização do Azure AD como seu Fornecedor de Identidade (IdP) e configurar um único sign-on (SSO) pode ser simples ou complexo dependendo da aplicação que está a ser utilizada. Algumas aplicações podem ser configuradas com apenas algumas ações. Outros requerem uma configuração aprofundada. Para aumentar rapidamente o conhecimento, caminhe pela [série quickstart](view-applications-portal.md) sobre gestão de aplicações. Se a aplicação que está a adicionar é simples, então provavelmente não precisa ler este artigo. Se a aplicação que está a adicionar requer uma configuração personalizada para SSO baseado em SAML, então este artigo é para si.

Na [série quickstart,](add-application-portal-setup-sso.md)há um artigo sobre configurar um único sign-on. Nele, aprende-se a aceder à página de configuração SAML para uma aplicação. A página de configuração SAML inclui cinco secções. Estas secções são discutidas em detalhe neste artigo.

> [!IMPORTANT] 
> Existem alguns cenários em que a opção **de inscrição única** não estará presente na navegação para uma aplicação em **aplicações da Enterprise**. 
>
> Se a aplicação foi registada usando **registos de Aplicações,** então a capacidade de inscrição única é configurada para utilizar o OIDC OAuth por padrão. Neste caso, a opção **de inscrição única** não será apresentada na navegação ao abrigo das **aplicações da Enterprise.** Quando utiliza **as inscrições da App** para adicionar a sua aplicação personalizada, configura as opções no ficheiro manifesto. Para saber mais sobre o ficheiro manifesto, consulte o [manifesto da aplicação Azure Ative Directory](../develop/reference-app-manifest.md). Para saber mais sobre os padrões SSO, consulte [autenticação e autorização utilizando a plataforma de identidade da Microsoft.](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform) 
>
> Outros cenários em que o único sinal de sessão estará ausente da navegação incluem quando uma aplicação é hospedada noutro inquilino ou se a sua conta não tiver as permissões necessárias (Administrador Global, Administrador de Aplicação **cloud,** Administrador de Aplicação ou proprietário do principal de serviço). As permissões também podem causar um cenário em que pode abrir **o single de sposição,** mas não será capaz de economizar. Para saber mais sobre as funções administrativas da Azure AD, consulte https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .


## <a name="basic-saml-configuration"></a>Configuração básica do SAML

Deve obter os valores do vendedor de aplicações. Pode introduzir manualmente os valores ou carregar um ficheiro de metadados para extrair o valor dos campos.

> [!TIP]
> Muitas aplicações já foram pré-configuradas para trabalhar com a Azure AD. Estas aplicações estão listadas na galeria de aplicações que pode navegar quando adiciona uma aplicação ao seu inquilino AZure AD. A [série quickstart](add-application-portal-setup-sso.md) acompanha-o através do processo. Para as aplicações na galeria encontrará instruções detalhadas, passo a passo. Para aceder aos passos pode clicar no link na página de configuração SAML para a aplicação descrita na série quickstart ou pode navegar numa lista de todos os tutoriais de configuração de aplicações em tutoriais de configuração de [aplicações SaaS.](../saas-apps/tutorial-list.md)

| Definição básica de configuração SAML | Iniciado pelo SP | Iniciado pelo idP | Description |
|:--|:--|:--|:--|
| **Identificador (ID de Entidade)** | Necessário para algumas aplicações | Necessário para algumas aplicações | Identifica exclusivamente a aplicação. A Azure AD envia o identificador para a aplicação como parâmetro do Público do token SAML. Espera-se que o pedido o valide. Este valor também aparece como o ID da Entidade nos metadados SAML que a aplicação fornece. Introduza um URL que utilize o seguinte padrão: 'https:// <subdomain> .contoso.com' *Pode encontrar este valor como elemento **emitente** no **pedido AuthnRequest** (pedido SAML) enviado pela aplicação.* |
| **URL de resposta** | Necessário | Necessário | Especifica onde é que a aplicação espera receber o token SAML. O URL de resposta também é denominado URL do Serviço de Consumidor de Asserções (ACS). Pode utilizar os campos URL de resposta adicionais para especificar URLs de resposta múltipla. Por exemplo, pode precisar de URLs de resposta adicionais para vários subdomínios. Ou, para efeitos de teste, pode especificar URLs de resposta múltipla (hospedeiro local e URLs públicos) de uma só vez. |
| **URL de inscrição** | Necessário | Não especifique | Quando um utilizador abre este URL, o fornecedor de serviços redireciona para o Azure AD para autenticar e iniciar a sessão do utilizador. O Azure AD utiliza o URL para iniciar a aplicação a partir de Microsoft 365 ou Azure AD My Apps. Quando em branco, o Azure AD faz um teste de sação iniciado pelo IdP quando um utilizador lança a aplicação a partir do Microsoft 365, Azure AD My Apps ou o Azure AD SSO URL.|
| **Estado de Reencaminhamento** | Opcional | Opcional | Especifica à aplicação para onde deve redirecionar o utilizador após a conclusão da autenticação. Normalmente, o valor é um URL válido para a aplicação. No entanto, algumas aplicações utilizam este campo de forma diferente. Para obter mais informações, contacte o fornecedor da aplicação.
| **Logout URL** | Opcional | Opcional | Usado para enviar as respostas de logout SAML de volta para a aplicação.

## <a name="user-attributes-and-claims"></a>Atributos e reclamações do utilizador 

Quando um utilizador autentica a aplicação, a Azure AD emite a aplicação um token SAML com informações (ou reclamações) sobre o utilizador que as identifica de forma única. Por predefinição, estas informações incluem o nome de utilizador do utilizador, endereço de e-mail, primeiro nome e apelido. Poderá ser necessário personalizar estas reclamações se, por exemplo, a aplicação necessitar de valores de reclamação específicos ou de um formato **Name** que não seja o nome de utilizador. 

> [!IMPORTANT]
> Muitas aplicações já estão pré-configuradas e na galeria de aplicações e não precisa de se preocupar em configurar as reclamações do utilizador e do grupo. A [série quickstart](add-application-portal.md) acompanha-o através da adição e configuração de apps.


O valor **do identificador de identificação do utilizador único (Name ID)** é uma reivindicação necessária e é importante. O valor predefinido é *user.userprincipalname*. O identificador de utilizador identifica exclusivamente cada utilizador da aplicação. Por exemplo, se o endereço de e-mail for o nome de utilizador e o identificador exclusivo, defina o valor como *user.mail*.

Para saber mais sobre a personalização das reclamações da SAML, consulte [Como: personalizar as reclamações emitidas no token SAML para aplicações empresariais.](../develop/active-directory-saml-claims-customization.md)

Pode adicionar novas reclamações, para mais detalhes ver [adicionar reclamações específicas da aplicação](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims) ou adicionar reclamações de grupo, consulte [as reclamações do grupo Configure](../hybrid/how-to-connect-fed-group-claims.md).


> [!NOTE]
> Para obter formas adicionais de personalizar o token SAML da AZure AD para a sua aplicação, consulte os seguintes recursos.
>- Para criar funções personalizadas através do portal Azure, consulte [as reivindicações de funções Configure](../develop/active-directory-enterprise-app-role-management.md).
>- Para personalizar as reclamações através do PowerShell, consulte [Personalizar as reclamações - PowerShell](../develop/active-directory-claims-mapping.md).
>- Para modificar o manifesto de aplicação para configurar pedidos opcionais para a sua aplicação, consulte [Configure pedidos opcionais](../develop/active-directory-optional-claims.md).
>- Para definir políticas de vida útil para atualização de tokens, tokens de acesso, fichas de sessão e fichas de identificação, consulte [as vidas simbólicas de Configure](../develop/active-directory-configurable-token-lifetimes.md). Ou, para restringir as sessões de autenticação através do Acesso Condicionado Azure AD, consulte [as capacidades de gestão da sessão de autenticação](../conditional-access/howto-conditional-access-session-lifetime.md).

## <a name="saml-signing-certificate"></a>Certificado de assinatura SAML

A Azure AD utiliza um certificado para assinar os tokens SAML que envia para o pedido. Precisa deste certificado para configurar a confiança entre a Azure AD e o pedido. Para mais informações sobre o formato do certificado, consulte a documentação SAML da aplicação. Para obter mais informações, consulte [Gerir os certificados para](manage-certificates-for-federated-single-sign-on.md) opções de assinatura única e de assinatura de [certificados avançados federados no token SAML](certificate-signing-options.md).

> [!IMPORTANT]
> Muitas aplicações já estão pré-configuradas e na galeria de aplicações e você não precisa mergulhar em certificados. A [série quickstart](add-application-portal.md) acompanha-o através da adição e configuração de apps.

A partir do Azure AD, você pode baixar o certificado ativo em formato Base64 ou Raw diretamente a partir do Sign-On único configurado com a página **SAML.** Além disso, pode obter o certificado ativo descarregando o ficheiro XML de metadados de aplicação ou utilizando o URL de metadados da federação de aplicações. Para visualizar, criar ou baixar os seus certificados (ativos ou inativos), siga estes passos.

Algumas coisas comuns para verificar para verificar um certificado incluem: 
   - *A data de validade correta.* Pode configurar a data de validade até três anos no futuro.
   - *Um estatuto de ativo para o certificado certo.* Se o estado for **Inativo, altere** o estado para **Ative**. Para alterar o estado, clique com o direito na linha do certificado e selecione **Faça o certificado ativo**.
   - *A opção de assinatura correta e algoritmo.*
   - *O endereço de e-mail de notificação correto(es).* Quando o certificado ativo está próximo da data de validade, a Azure AD envia uma notificação para o endereço de e-mail configurado neste campo.

Às vezes, pode precisar de baixar o certificado. Tenha cuidado onde o guarda! Para descarregar o certificado, selecione uma das opções para o formato Base64, formato Raw ou Federação Metadados XML. O Azure AD também fornece o **Url de Metadados da Federação de Aplicações** onde pode aceder aos metadados específicos da aplicação no `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>` formato.

> [!NOTE]
> A aplicação deve ser capaz de manusear o Byte Order Marker presente no XML prestado durante a utilização https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id} . A marca de encomenda byte é representada como um carácter ASCII não imprimível »¿ e no Hex é representada como EF BB BF ao rever os dados XML.

Para escoar as alterações de certificado, selecione o botão Editar. Há várias coisas que pode fazer na página do **Certificado de Assinatura SAML:**
   - Crie um novo certificado: selecione **Novo Certificado,** selecione a **Data de Validade** e, em seguida, selecione **Guardar**. Para ativar o certificado, selecione o menu de contexto **(...)** e selecione **Faça o certificado ativo**.
   - Faça o upload de um certificado com credenciais de chave privada e pfx: selecione **Certificado de Importação** e navegue no certificado. Introduza a **palavra-passe PFX** e, em seguida, selecione **Adicionar**.  
   - Configurar a assinatura de certificado avançado. Para obter mais informações sobre estas opções, consulte [as opções avançadas de assinatura de certificados.](certificate-signing-options.md)
   - Notifique pessoas adicionais quando o certificado ativo estiver próximo da data de validade: insira os endereços de e-mail nos campos de **endereços de e-mail de Notificação.**

## <a name="set-up-the-application-to-use-azure-ad"></a>Configurar a aplicação para usar a Azure AD

A secção **Configuração \<applicationName>** lista os valores que precisam de ser configurados na aplicação para que utilize a Azure AD como fornecedor de identidade SAML. Define os valores na página de configuração no site das aplicações. Por exemplo, se estiver a configurar o GitHub, então iria ao site github.com e definiria os valores. Se a aplicação já estiver pré-configurada e na galeria AD Azure, encontrará um link para **ver instruções passo a passo**. Caso contrário, terá de encontrar a documentação para a aplicação que está a configurar. 

Os valores url de **login** e **URL logout** resolvem ambos para o mesmo ponto final, que é o ponto final de tratamento de pedidos SAML para o inquilino AZure AD. 

O **Identificador Azure AD** é o valor do **emitente** no token SAML emitido para o pedido.

## <a name="test-single-sign-on"></a>Testar o início de sessão único

Uma vez configurado a sua aplicação para utilizar o Azure AD como fornecedor de identidade baseado em SAML, pode testar as definições para ver se um único sinal funciona para a sua conta. 

Selecione **Teste** e, em seguida, opte por testar com o utilizador atualmente assinado ou como outra pessoa. 

Se a sessão for bem sucedida, está pronta para atribuir utilizadores e grupos à sua aplicação SAML. Parabéns!

Se aparecer uma mensagem de erro, complete os seguintes passos:

1. Copie e cole os detalhes na caixa **Qual é o aspeto do erro?**.

    ![Obter orientações de resolução](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Selecione **Obter orientação de resolução**. A orientação da causa e da resolução aparecem.  Neste exemplo, o utilizador não foi designado para a aplicação.

3. Leia a orientação de resolução e tente corrigir o problema.

4. Execute o teste novamente, até que seja concluído sem erros.

Para obter mais informações, consulte [o único sign-on baseado em Debug SAML para aplicações no Azure Ative Directory](./debug-saml-sso-issues.md).


## <a name="next-steps"></a>Passos seguintes

- [Quickstart Series em Gestão de Aplicações](view-applications-portal.md)
- [Atribuir utilizadores ou grupos à aplicação](./assign-user-or-group-access-portal.md)
- [Configurar o provisionamento automático da conta de utilizador](../app-provisioning/configure-automatic-user-provisioning-portal.md)
- [Protocolo SAML de Início de Sessão Único](../develop/single-sign-on-saml-protocol.md)
