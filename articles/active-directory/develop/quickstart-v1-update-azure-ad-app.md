---
title: Integrar aplicações com o Azure Active Directory
description: Saiba como atualizar uma aplicação no Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: bee16ed8205453546702946628c98c73b0f34b15
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58103813"
---
# <a name="quickstart-update-an-application-in-azure-active-directory"></a>Início rápido: Atualizar uma aplicação no Azure Active Directory

Os programadores empresariais e os fornecedores de software como serviço (SaaS) que tenham registado aplicações no Azure Active Directory (Azure AD) poderão ter de configurar as aplicações para acederem a outros recursos, como APIs Web, as disponibilizar noutras organizações, etc.

Neste início rápido, vai aprender as várias formas através das quais pode configurar ou atualizar a sua aplicação, de modo a que cumpra os requisitos ou as necessidades da sua organização ou de outras organizações.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, confirme que concluiu os seguintes passos:

* Leu a descrição geral da [arquitetura de consentimento do Azure AD](consent-framework.md), que é importante compreender se estiver a criar aplicações que têm de ser utilizadas por outros utilizadores ou aplicações.
* Tem um inquilino do Azure AD que tenha aplicações registadas.
  * Se ainda não tiver um inquilino, [saiba como obter um](quickstart-create-new-tenant.md).
  * Se não tiver aplicações registadas no seu inquilino, [saiba como adicionar uma aplicação ao Azure AD](quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="configure-a-client-application-to-access-web-apis"></a>Configurar uma aplicação cliente para aceder a APIs Web.

Para que uma aplicação cliente Web/confidencial possa participar num fluxo de concessão de autorização que precise de autenticação (e obter um token de acesso), essa aplicação tem de estabelecer credenciais seguras. O método de autenticação predefinido que o portal do Azure suporta é ID de cliente + chave secreta. Esta secção abrange os passos de configuração necessários para fornecer a chave secreta com as credenciais do cliente.

Antes de um cliente poder aceder a uma API Web exposta por uma aplicação de recurso (como a Microsoft Graph API), a arquitetura de consentimento garante que o cliente obtém a concessão de permissão necessária, com base nas permissões pedidas. Por predefinição, todas as aplicações podem escolher as permissões do**Azure Active Directory** (Graph API) e do modelo de implementação clássica do Azure. A [permissão “Iniciar e ler perfil de utilizador” da Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes#PermissionScopeDetails) também está selecionada por predefinição. Se o seu cliente estiver a ser registado num inquilino que tenha contas subscritas no Office 365, estarão disponíveis para seleção APIs Web e permissão para o SharePoint e o Exchange Online. Pode escolher de entre dois tipos de permissões para cada API Web pretendida:

- Permissões de aplicação: A aplicação cliente precisa acessar a API web diretamente em nome próprio (nenhum contexto de usuário). Este tipo de permissão requer consentimento do administrador e também não está disponível para aplicações cliente nativas.
- Permissões delegadas: A aplicação cliente precisa de aceder a API web, como o utilizador com sessão iniciada, mas com acesso limitado pela permissão selecionada. Este tipo de permissão pode ser concedido por um utilizador, a não ser que exija consentimento do administrador.

  > [!NOTE]
  > Adicionar uma permissão delegada a uma aplicação não concede automaticamente consentimento aos utilizadores no inquilino. Os utilizadores continuam a ter de consentir manualmente as permissões delegadas adicionadas no runtime, a não ser que o administrador conceda acesso em nome de todos eles.

### <a name="add-application-credentials-or-permissions-to-access-web-apis"></a>Adicionar credenciais de aplicação ou permissões para aceder a APIs Web

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Se a sua conta permitir aceder a mais de um, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
3. No painel de navegação do lado esquerdo, selecione o serviço **Azure Active Directory**, selecione **Registos de aplicações** e localize/selecione a aplicação que pretende configurar.

   ![Atualizar o registo de uma aplicação](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

4. É encaminhado para a página de registo principal da aplicação, a qual inclui a página **Definições** daquela. Para adicionar uma credencial à aplicação Web:
   1. Selecione a secção **Chaves** na página **Definições**.
   1. Para adicionar um certificado:
      - Selecione **Carregar Chave Pública**.
      - Selecione o ficheiro que pretende carregar. Tem de ser do tipo .cer, .pem ou .crt.
   1. Para adicionar uma palavra-passe:
      - Adicione uma descrição da chave.
      - Selecione uma duração.
      - Selecione **Guardar**. A coluna mais à direita inclui o valor da chave, depois de guardar as alterações à configuração. **Certifique-se de que copia a chave** para utilizar no código da sua aplicação cliente, pois aquela deixa de estar acessível quando sair desta página.

5. Para adicionar uma ou mais permissões para aceder às APIs do recurso a partir do cliente:
   1. Selecione a secção **Permissões necessárias**, na página **Definições** e selecione **Adicionar**.
   1. Selecione **Selecionar uma API** para escolher os tipos de recursos de entre os quais quer escolher.
   1. Navegue pela lista de APIs disponíveis ou utilize a caixa de pesquisa para selecionar a partir das aplicações de recursos disponíveis no seu diretório que expõem APIs Web. Selecione o recurso em que está interessado e clique em **Selecionar**.
   1. Na página **Permitir Acesso**, selecione as permissões da aplicação e/ou as permissões delegadas de que a aplicação precisa quando aceder à API.
   
   ![Atualizar o registo de uma aplicação - permissões API](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-api.png)

   ![Atualizar o registo de uma aplicação - permissões](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-perms.png)

6. Quando terminar, selecione o botão **Selecionar**, na página **Permitir Acesso**, e o botão  **Concluído**, na página **Adicionar acesso da API**. É reencaminhado para a página **Permissões necessárias**, onde o recurso novo é adicionado à lista de APIs.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Configurar uma aplicação de recurso para expor APIs Web

Pode programar uma API Web e expor [âmbitos](developer-glossary.md#scopes) e [funções](developer-glossary.md#roles) de acesso para a disponibilizar às aplicações cliente. As APIs Web configuradas corretamente são disponibilizadas tal e qual as outras APIs Web da Microsoft, incluindo a Graph API e as APIs do Office 365. Os âmbitos e as funções de acesso são expostos através do [manifesto da aplicação](developer-glossary.md#application-manifest), que é um ficheiro JSON que representa a configuração de identidade da aplicação.

A secção seguinte mostra-lhe como expor os âmbitos de acesso mediante a modificação do manifesto da aplicação do recurso.

### <a name="add-access-scopes-to-your-resource-application"></a>Adicionar âmbitos de acesso à aplicação do recurso

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Se a sua conta permitir aceder a mais de um, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
3. No painel de navegação do lado esquerdo, selecione o serviço **Azure Active Directory > Registos de aplicações**  e localize/selecione a aplicação que pretende configurar.

   ![Atualizar o registo de uma aplicação](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

4. É encaminhado para a página de registo principal da aplicação, a qual abre a página **Definições** daquela. Clique em **Manifesto**, na página de registo da aplicação, para mudar para a página **Editar manifesto**. É aberto um editor de manifesto baseado na Web, que lhe permite **Editar** o manifesto no portal. Opcionalmente, pode clicar em **Transferir** e editar localmente e, em seguida, utilizar **Carregar** para o reaplicar à aplicação.
5. Neste exemplo, vamos expor um âmbito novo denominado `Employees.Read.All` no nosso recurso/API mediante a adição do seguinte elemento JSON à coleção `oauth2Permissions`. O âmbito `user_impersonation` já existente é fornecido por predefinição durante o registo. `user_impersonation` permite a uma aplicação cliente pedir permissão para aceder ao recurso com a identidade do utilizador com sessão iniciada. Confirme que adiciona a vírgula a seguir ao elemento de âmbito `user_impersonation` já existente e altere os valores da propriedade de acordo com as necessidades do seu recurso. 

   ```json
   {
    "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
    "adminConsentDisplayName": "Read-only access to Employee records",
    "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
    "isEnabled": true,
    "type": "User",
    "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
    "userConsentDisplayName": "Read-only access to your Employee records",
    "value": "Employees.Read.All"
   }
   ```

   > [!NOTE]
   > O `id` valor tem de ser gerado por meio de programação ou usando uma GUID geração ferramenta, como [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx). `id` representa um identificador exclusivo para o âmbito conforme é exposto pela API Web. Após um cliente ser devidamente configurado com permissões para aceder à sua API Web, o Azure AD emite um token de acesso OAuth2.0 ao mesmo. Quando o cliente chamar a API Web, apresenta o token de acesso que tem a afirmação de âmbito (scp) definida como as permissões pedidas no registo da aplicação.
   >
   > Se necessário, pode expor âmbitos adicionais mais tarde. Considere que a API Web poderá expor vários âmbitos associados a diversas funções diferentes. O seu recurso pode controlar o acesso à API Web no runtime ao avaliar a afirmação ou afirmações do âmbito (`scp`) no token de acesso OAuth 2.0 recebido.

6. Quando terminar, clique em **Guardar**. A sua API Web está agora configurada para ser utilizada por outras aplicações no seu diretório.

   ![Atualizar o registo de uma aplicação](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-manifest.png)

### <a name="verify-the-web-api-is-exposed-to-other-applications-in-your-tenant"></a>Verificar se a API Web está exposta a outras aplicações no inquilino

1. Regresse ao seu inquilino do Azure AD, selecione novamente **Registos de aplicações** e localize/selecione a aplicação cliente que pretende configurar.

   ![Atualizar o registo de uma aplicação](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

2. Repita o passo 5, conforme fez em [Configurar uma aplicação cliente para aceder a APIs Web](#configure-a-client-application-to-access-web-apis). Quando chegar ao passo **Selecionar uma API**, introduza o nome da aplicação no campo de pesquisa e clique em **Selecionar** para procurar o seu recurso.

3. Na página **Permitir Acesso**, deverá ver o âmbito novo disponível para pedidos de permissão de cliente.

   ![São mostradas permissões novas](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-perms-newscopes.png)

### <a name="more-on-the-application-manifest"></a>Mais sobre o manifesto da aplicação

O manifesto de aplicação funciona como um mecanismo para atualizar a entidade Aplicação, que define todos os atributos de configuração de identidade de uma aplicação do Azure AD, incluindo os âmbitos de acesso da API que já abordámos. Para obter mais informações sobre a entidade Aplicação e respetivo esquema, veja a documentação [Entidade de Aplicação da Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). O artigo contém informações de referência completas sobre os membros da entidade Aplicação utilizados para especificar permissões para a sua API, incluindo:  

- O membro appRoles, que é uma coleção de entidades [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type), utilizado para definir as [permissões da aplicação](developer-glossary.md#permissions) de uma API Web. 
- O membro oauth2Permissions, que é uma coleção de entidades [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type), utilizado para definir as [permissões delegadas](developer-glossary.md#permissions) de uma API Web.

Para obter mais informações sobre os conceitos do manifesto de aplicação em geral, veja [Azure AD app manifest](reference-app-manifest.md) (Manifesto de aplicação do Azure AD).

## <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Aceder ao Azure AD Graph e ao Office 365 através das Microsoft Graph APIs  

Tal como foi dito anteriormente, para além de expor/aceder às APIs para as suas próprias aplicações, pode registar a aplicação cliente para aceder às APIs que são expostas pelos recursos da Microsoft. A Microsoft Graph API, referida como “Microsoft Graph” na lista de recursos/API do portal, está disponível para todas as aplicações registadas no Azure AD. Se estiver a registar a sua aplicação cliente num inquilino que tenha contas inscritas numa subscrição do Office 365, também pode aceder aos âmbitos expostos pelos vários recursos do Office 365.

Para ver uma descrição completa dos âmbitos que a Microsoft Graph API expõe, veja o artigo [Microsoft Graph permissions reference](https://docs.microsoft.com/graph/permissions-reference) (Referência às permissões do Microsoft Graph).

> [!NOTE]
> Devido a uma limitação atual, as aplicações cliente nativas só podem chamar a Azure AD Graph API se utilizarem a permissão “Aceder ao diretório da sua organização”. Esta restrição não se aplica às aplicações Web.

## <a name="configuring-multi-tenant-applications"></a>Configurar aplicações multi-inquilino

Quando registar uma aplicação no Azure AD, poderá querer que a mesma só esteja acessível pelos utilizadores da sua organização. Em alternativa, pode querer que a aplicação esteja acessível apenas por utilizadores de organizações externas. Estes dois tipos de aplicações são denominados “aplicação de inquilino único” e “aplicação multi-inquilinos”. Esta secção mostra como modificar a configuração de uma aplicação de inquilino único para a tornar uma aplicação multi-inquilinos.

É importante ter em conta as diferenças entre estes dois tipos de aplicações:  

- Uma aplicação de inquilino único destina-se a ser utilizada numa organização. Regra geral, é uma aplicação de linha de negócio (LoB) escrita por um programador de uma empresa. Só pode ser acedida pelos utilizadores com contas no mesmo inquilino do registo da aplicação. Como resultado, basta que seja aprovisionada num único diretório.
- Uma aplicação multi-inquilinos destina-se a ser utilizada em muitas organizações. Conhecida como aplicação Web de software como serviço (SaaS), é, geralmente,escrita por um fornecedor de software independente (ISV). As aplicações multi-inquilinos têm de ser aprovisionadas em todos os inquilinos nos quais os utilizadores precisam de acesso. Para as registar, é necessário o consentimento do utilizador ou do administrador nos inquilinos que não aquele em que as aplicações estão registadas. Tenha em conta que as aplicações cliente nativas são multi-inquilinos por predefinição, pois são instaladas no dispositivo do proprietário do recurso. Consulte a descrição geral da anterior da secção de estrutura de consentimento para obter detalhes sobre a estrutura de consentimento.

Para que uma aplicação seja multi-inquilinos, é preciso fazer alterações ao registo da aplicação e à própria aplicação Web. As secções abaixo abordam essas duas alterações: 

### <a name="changing-the-application-registration-to-support-multi-tenant"></a>Alterar o registo da aplicação para suportar vários inquilinos

Se estiver a escrever uma aplicação e pretende disponibilizá-la aos seus clientes ou parceiros fora da sua organização, tem de atualizar a definição da mesma no portal do Azure.

> [!IMPORTANT]
> O Azure AD precisa que o URI do ID da Aplicação das aplicações multi-inquilinos seja globalmente exclusivo. O URI do ID da Aplicação é uma das formas através das quais as aplicações são identificadas nas mensagens de protocolo. Relativamente às aplicações de inquilino único, basta que o URI do ID da Aplicação seja exclusivo nesse inquilino. Nas aplicações multi-inquilinos, tem de ser globalmente exclusivo, para que o Azure AD consiga encontrar a aplicação em todos os inquilinos.
> Para aplicar a exclusividade global, o URI do ID da App tem de ter um nome de anfitrião que corresponda a um domínio verificado do inquilino do Azure AD. Por exemplo, se o nome do inquilino for contoso.onmicrosoft.com, https://contoso.onmicrosoft.com/myapp seria um URI de ID da Aplicação válido. Se o seu inquilino tiver o domínio verificado contoso.com, https://contoso.com/myapp também seria um URI de ID da Aplicação válido. Se o URI não seguir este padrão, a definição da aplicação como multi-inquilinos falha.

Para dar aos utilizadores externos a possibilidade de acederem à aplicação:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Se a sua conta permitir aceder a mais de uma, clique na sua conta no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
3. No painel de navegação do lado esquerdo, clique no serviço **Azure Active Directory** , clique em **Registos de aplicações** e localize/selecione a aplicação que pretende configurar. É encaminhado para a página de registo principal da aplicação, a qual abre a página **Definições** daquela.
4. Na página **Definições**, clique em **Propriedades** e altere o comutador **Multi-inquilinos** para **Sim**.

Depois de fazer as alterações, os utilizadores e administradores das outras organizações podem conceder aos respetivos utilizadores a capacidade de iniciarem sessão na sua aplicação, permitindo que esta aceda aos recursos protegidos pelos inquilinos deles.

### <a name="changing-the-application-to-support-multi-tenant"></a>Alterar a aplicação para suportar vários inquilinos

O suporte de aplicações multi-inquilinos depende muito da arquitetura de consentimento do Azure AD. O consentimento é o mecanismo através do qual um utilizador de outro inquilino pode conceder à aplicação acesso aos recursos protegidos pelo inquilino desse utilizador. A esta experiência dá-se o nome “consentimento do utilizador”.

A sua aplicação Web pode também oferecer:

- A capacidade de “inscrever a minha empresa” aos administradores. Esta experiência, conhecida como “consentimento do administrador” dá aos administradores a capacidade de concederem o consentimento em nome de *todos os utilizadores* nas organizações deles. Só um utilizador que se autentique numa conta que pertença à função Administrador Global pode conceder o consentimento do administrador; os outros utilizadores recebem um erro.
- Uma experiência de inscrição para os utilizadores. Espera-se que seja disponibilizado ao utilizador um botão de “inscrição” que redirecionará o browser para o ponto final `/authorize` de OAuth2.0 do Azure AD ou para um ponto final `/userinfo` do OpenID Connect. Estes pontos finais permitem à aplicação inspecionar o id_token e, assim, obter as informações do utilizador novo. Após a fase de inscrição, é apresentada ao utilizador com um prompt de consentimento, semelhante à mostrada na descrição geral da secção de estrutura de consentimento.

Para obter mais informações sobre as alterações à aplicação que são necessárias para suportar as experiências de acesso multi-inquilinos e de início de sessão/inscrição, veja:

- [Como iniciar sessão de qualquer utilizador do Azure Active Directory (AD) utilizando o padrão de aplicação multi-inquilino](howto-convert-app-to-be-multi-tenant.md)
- A lista de [exemplos de código multi-inquilinos](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant).
- [Quickstart: Adicionar imagem corporativa à sua página de início de sessão no Azure AD](../fundamentals/customize-branding.md)

## <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>Permitir a concessão implícita de OAuth 2.0 para aplicações de página única.

Geralmente, as aplicações de página única (SPAs) são estruturadas com um front-end altamente baseado em JavaScript que é executado no browser e que chama o back-end da API Web da aplicação para realizar a respetiva lógica de negócio. Relativamente às SPAs alojadas no Azure AD, é utilizada a Concessão Implícita de OAuth 2.0 para autenticar o utilizador no Azure AD e obter um token que pode ser utilizado para proteger as chamadas do cliente JavaScript da aplicação para a respetiva API Web de back-end.

Depois de o utilizador conceder o consentimento, este mesmo protocolo de autenticação pode ser utilizado para obter tokens para proteger as chamadas entre o cliente e os outros recursos da API Web configurados para a aplicação. Para saber mais sobre a concessão da autorização implícita e decidir se é adequada para o cenário da sua aplicação, veja [Understanding the OAuth2 implicit grant flow in Azure Active Directory](v1-oauth2-implicit-grant-flow.md) (Compreender o fluxo de concessão implícita de OAuth 2.0 no Azure Active Directory).

Por predefinição, a concessão implícita de OAuth 2.0 está desativada nas aplicações. Pode definir o valor `oauth2AllowImplicitFlow` no [manifesto de aplicação](reference-app-manifest.md) da sua aplicação para permitir a concessão implícita de OAuth 2.0 na mesma.

### <a name="to-enable-oauth-20-implicit-grant"></a>Para permitir a concessão implícita de OAuth 2.0

> [!NOTE]
> Para obter detalhes sobre como editar o manifesto de aplicação, comece por rever a secção [Configurar uma aplicação de recurso para expor APIs Web](#configuring-a-resource-application-to-expose-web-apis) anterior.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Se a sua conta permitir aceder a mais de uma, clique na sua conta no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
3. No painel de navegação do lado esquerdo, clique no serviço **Azure Active Directory** , clique em **Registos de aplicações** e localize/selecione a aplicação que pretende configurar. É encaminhado para a página de registo principal da aplicação, a qual abre a página **Definições** daquela.
4. Clique em **Manifesto**, na página de registo da aplicação, para mudar para a página **Editar manifesto**. É aberto um editor de manifesto baseado na Web, que lhe permite **Editar** o manifesto no portal. Localize e defina o valor "oauth2AllowImplicitFlow" como "true." Por predefinição, está definido como “false”.
   
   ```json
   "oauth2AllowImplicitFlow": true,
   ```
5. Guarde o manifesto atualizado. Depois de guardado, a API Web passa a estar configurada para utilizar a concessão implícita de OAuth 2.0 para autenticar os utilizadores.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre outros inícios rápidos de gestão de aplicações relacionados que utilizam o ponto final do Azure AD v1.0:
- [Adicionar uma aplicação ao Azure AD](quickstart-v1-integrate-apps-with-azure-ad.md)
- [Remover uma aplicação do Azure AD](quickstart-v1-remove-azure-ad-app.md)

Para saber mais sobre os dois objetos do Azure AD que representam uma aplicação registada e o relacionamento entre os mesmos, veja [Application objects and service principal objects](app-objects-and-service-principals.md) (Objetos da aplicação e objetos do principal de serviço).

Para saber mais sobre as diretrizes de imagem corporativa que deve seguir quando desenvolver aplicações com o Azure Active Directory, veja [Diretrizes de imagem corporativa para aplicações](howto-add-branding-in-azure-ad-apps.md).
