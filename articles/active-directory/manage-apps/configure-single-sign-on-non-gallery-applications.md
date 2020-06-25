---
title: SAML single sign-on - non gallery applications - Microsoft identity platform / Microsoft Docs
description: Configure um único sign-on (SSO) para aplicações não-galeria na plataforma de identidade microsoft (Azure AD)
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 06/08/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1604e132cb77fbb2a2a1033a1f23f70dd3e6b8b9
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85355969"
---
# <a name="configure-saml-based-single-sign-on-to-non-gallery-applications"></a>Configurar o início de sessão único baseado em SAML para aplicações que não estão na galeria

Quando [adiciona uma aplicação](add-gallery-app.md) de galeria ou uma [aplicação web não-galeria](add-non-gallery-app.md) às suas Aplicações empresariais AZure AD, uma das opções únicas de inscrição disponível para si é o [sign-on único baseado em SAML.](what-is-single-sign-on.md#saml-sso) Escolha a SAML sempre que possível para aplicações que autenticam utilizando um dos protocolos SAML. Com um único sinal de SATURA, o Azure AD autentica-se na aplicação utilizando a conta Azure AD do utilizador. A Azure AD comunica a informação de inscrição à aplicação através de um protocolo de ligação. Pode mapear os utilizadores para funções de aplicação específicas com base nas regras que define nas suas reclamações SAML. Este artigo descreve como configurar um único sign-on baseado em SAML para uma aplicação não-galeria. 

> [!NOTE]
> Adicionar uma aplicação de galeria? Encontre instruções de configuração passo a passo na [lista de tutoriais de aplicações saaS](../saas-apps/tutorial-list.md)

Para configurar o único sinal de INSCRIÇão da SAML para uma aplicação sem galeria sem código de escrita, é necessário ter uma subscrição AD Azure e a aplicação deve apoiar o SAML 2.0. Para mais informações sobre as versões AD do Azure, visite [os preços da AD Azure](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="before-you-begin"></a>Antes de começar

Se a aplicação não tiver sido adicionada ao seu inquilino Azure AD, consulte [uma aplicação não-galeria](add-non-gallery-app.md).

## <a name="step-1-edit-the-basic-saml-configuration"></a>Passo 1. Editar a Configuração Básica SAML

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador de aplicações em nuvem ou como administrador de aplicação para o seu inquilino AZure AD.

2. Navegue para aplicações **Azure Ative Directory**  >  **Enterprise** e selecione a aplicação da lista. 
   
   - Para procurar a aplicação, no menu **'Tipo de Aplicação',** selecione **Todas as aplicações**e, em seguida, selecione **Aplicar**. Introduza o nome da aplicação na caixa de pesquisa e, em seguida, selecione a aplicação a partir dos resultados.

3. Na secção **'Gerir',** selecione **'Único's sign-on'**. 

   - Note que existem alguns cenários em que a opção **de inscrição única** não estará presente. Por exemplo, se a aplicação foi registada usando **registos de Aplicações,** então a capacidade de inscrição única está configurada no portal **de registo da App** e não aparecerá na navegação sob **aplicações da Enterprise.** Outros cenários em que o único sinal de sessão estará ausente da navegação incluem quando uma aplicação é hospedada noutro inquilino ou se a sua conta não tiver as permissões necessárias (Administrador Global, Administrador de Aplicação **cloud,** Administrador de Aplicação ou proprietário do principal de serviço). As permissões também podem causar um cenário em que pode abrir **o single de sposição,** mas não será capaz de economizar. Para saber mais sobre as funções administrativas da Azure AD, consulte https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .

4. Selecione **SAML**. Aparece **a configuração de um único sign-on com SAML - página de pré-visualização.**

   ![Passo 1 Editar a Configuração Básica SAML](media/configure-single-sign-on-non-gallery-applications/step-one-basic-saml-config.png)

5. Para editar as opções básicas de configuração SAML, selecione o ícone **Editar** (um lápis) no canto superior direito da secção **de configuração BÁSICA SAML.**

1. Introduza as seguintes definições. Deve obter os valores do vendedor de aplicações. Pode introduzir manualmente os valores ou carregar um ficheiro de metadados para extrair o valor dos campos.

    | Definição básica de configuração SAML | Iniciado pelo SP | Iniciado pelo idP | Descrição |
    |:--|:--|:--|:--|
    | **Identificador (ID de Entidade)** | Necessário para algumas aplicações | Necessário para algumas aplicações | Identifica exclusivamente a aplicação. A Azure AD envia o identificador para a aplicação como parâmetro do Público do token SAML. Espera-se que o pedido o valide. Este valor também aparece como o ID da Entidade nos metadados SAML que a aplicação fornece. Introduza um URL que utilize o seguinte padrão: 'https:// <subdomain> .contoso.com' *Pode encontrar este valor como elemento **emitente** no **pedido AuthnRequest** (pedido SAML) enviado pela aplicação.* |
    | **URL de resposta** | Necessário | Necessário | Especifica onde é que a aplicação espera receber o token SAML. O URL de resposta também é denominado URL do Serviço de Consumidor de Asserções (ACS). Pode utilizar os campos URL de resposta adicionais para especificar URLs de resposta múltipla. Por exemplo, pode precisar de URLs de resposta adicionais para vários subdomínios. Ou, para efeitos de teste, pode especificar URLs de resposta múltipla (hospedeiro local e URLs públicos) de uma só vez. |
    | **URL de inscrição** | Necessário | Não especifique | Quando um utilizador abre este URL, o fornecedor de serviços redireciona para o Azure AD para autenticar e iniciar a sessão do utilizador. A Azure AD utiliza o URL para iniciar a aplicação a partir do Office 365 ou do Painel de Acesso AD Azure. Quando em branco, o Azure AD realiza o início de súp.|
    | **Estado de Reencaminhamento** | Opcional | Opcional | Especifica à aplicação para onde deve redirecionar o utilizador após a conclusão da autenticação. Normalmente, o valor é um URL válido para a aplicação. No entanto, algumas aplicações utilizam este campo de forma diferente. Para obter mais informações, contacte o fornecedor da aplicação.
    | **Logout URL** | Opcional | Opcional | Usado para enviar as respostas de logout SAML de volta para a aplicação.

Para obter mais informações, consulte [o protocolo SAML de assinatura única.](../develop/single-sign-on-saml-protocol.md)

## <a name="step-2-configure-user-attributes-and-claims"></a>Passo 2. Configure os atributos e reclamações do Utilizador 

Quando um utilizador autentica a aplicação, a Azure AD emite a aplicação um token SAML com informações (ou reclamações) sobre o utilizador que as identifica de forma única. Por predefinição, estas informações incluem o nome de utilizador do utilizador, endereço de e-mail, primeiro nome e apelido. Poderá ser necessário personalizar estas reclamações se, por exemplo, a aplicação necessitar de valores de reclamação específicos ou de um formato **Name** que não seja o nome de utilizador. Os requisitos para aplicações de galeria são descritos nos [tutoriais específicos da aplicação,](../saas-apps/tutorial-list.md)ou pode perguntar ao fornecedor de aplicações. Os passos gerais para configurar atributos e reclamações do utilizador são descritos abaixo.

1. Na secção **Atributos e Reclamações** do Utilizador, selecione o ícone **editar** (um lápis) no canto superior direito.

   ![Passo 2 Configurar atributos e reclamações do Utilizador](media/configure-single-sign-on-non-gallery-applications/step-two-user-attributes-claims.png)

2. Verifique o **valor do identificador de nome.** O valor predefinido é *user.principalname*. O identificador de utilizador identifica exclusivamente cada utilizador da aplicação. Por exemplo, se o endereço de e-mail for o nome de utilizador e o identificador exclusivo, defina o valor como *user.mail*.

3. Para modificar o **Valor do Identificador de Nome,** selecione o ícone **editar** (um lápis) para o campo **Valor identificador de nome.** Faça as alterações adequadas ao formato e origem do identificador, se necessário. Para mais detalhes, consulte [editar NameId](../develop/active-directory-saml-claims-customization.md#editing-nameid). Guarde as alterações quando terminar. 
 
4. Para configurar as reclamações de grupo, selecione o ícone **editar** para os **Grupos devolvidos no campo de reclamação.** Para mais informações, consulte [as reclamações do grupo Configure](../hybrid/how-to-connect-fed-group-claims.md).

5. Para adicionar uma reclamação, **selecione Adicionar nova reclamação** no topo da página. Introduza o **Nome** e selecione a fonte apropriada. Se selecionar a fonte **Atributo,** terá de escolher o **atributo Fonte** que pretende utilizar. Se selecionar a fonte **de Tradução,** terá de escolher a **Transformação** e o **Parâmetro 1** que pretende utilizar. Para mais informações, consulte [adicionar reclamações específicas da aplicação.](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims) Guarde as alterações quando terminar. 

6. Selecione **Guardar**. A nova alegação aparece na mesa.

   > [!NOTE]
   > Para obter formas adicionais de personalizar o token SAML da AZure AD para a sua aplicação, consulte os seguintes recursos.
   >- Para criar funções personalizadas através do portal Azure, consulte [as reivindicações de funções Configure](../develop/active-directory-enterprise-app-role-management.md).
   >- Para personalizar as reclamações através do PowerShell, consulte [Personalizar as reclamações - PowerShell](../develop/active-directory-claims-mapping.md).
   >- Para modificar o manifesto de aplicação para configurar pedidos opcionais para a sua aplicação, consulte [Configure pedidos opcionais](../develop/active-directory-optional-claims.md).
   >- Para definir políticas de vida útil para atualização de tokens, tokens de acesso, fichas de sessão e fichas de identificação, consulte [as vidas simbólicas de Configure](../develop/active-directory-configurable-token-lifetimes.md). Ou, para restringir as sessões de autenticação através do Acesso Condicionado Azure AD, consulte [as capacidades de gestão da sessão de autenticação](https://go.microsoft.com/fwlink/?linkid=2083106).

## <a name="step-3-manage-the-saml-signing-certificate"></a>Passo 3. Gerir o certificado de assinatura SAML

A Azure AD utiliza um certificado para assinar os tokens SAML que envia para o pedido. Precisa deste certificado para estabelecer a confiança entre a Azure AD e o pedido. Para mais informações sobre o formato do certificado, consulte a documentação SAML da aplicação. Para obter mais informações, consulte [Gerir os certificados para](manage-certificates-for-federated-single-sign-on.md) opções de assinatura única e de assinatura de [certificados avançados federados no token SAML](certificate-signing-options.md).

A partir do Azure AD, você pode baixar o certificado ativo em formato Base64 ou Raw diretamente a partir da página principal Configurar Único Sinal com página **SAML.** Em alternativa, pode obter o certificado ativo descarregando o ficheiro XML de metadados de aplicação ou utilizando o URL de metadados da federação de aplicações. Para visualizar, criar ou baixar os seus certificados (ativos ou inativos), siga estes passos.

1. Aceda à secção **de Certificado de Assinatura SAML.** 

   ![Passo 3 Gerir o certificado de assinatura SAML](./media/configure-single-sign-on-non-gallery-applications/step-three-certificate.png)

2. Verifique se o certificado tem:

   - *A data de validade desejada.* Pode configurar a data de validade até três anos no futuro.
   - *Um estatuto ativo para o certificado pretendido.* Se o estado for **Inativo, altere**o estado para **Ative**. Para alterar o estado, clique com o direito na linha do certificado pretendido e selecione **Faça o certificado ativo**.
   - *A opção de assinatura correta e algoritmo.*
   - *O endereço de e-mail de notificação correto(es).* Quando o certificado ativo está próximo da data de validade, a Azure AD envia uma notificação para o endereço de e-mail configurado neste campo.

2. Para descarregar o certificado, selecione uma das opções para o formato Base64, formato Raw ou Federação Metadados XML. O Azure AD também fornece o **Url de Metadados da Federação de Aplicações** onde pode aceder aos metadados específicos da aplicação no `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>` formato.

3. Para gerir, criar ou importar um certificado, selecione o ícone **Edit** (um lápis) no canto superior direito da secção certificado de **assinatura SAML.**

   ![Certificado de assinatura SAML](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)


   Tome qualquer uma das seguintes ações:

   - Para criar um novo certificado, selecione **Novo Certificado,** selecione a **Data de Validade**e, em seguida, selecione **Guardar**. Para ativar o certificado, selecione o menu de contexto **(...)** e selecione **Faça o certificado ativo**.
   - Para fazer o upload de um certificado com credenciais de chave privada e pfx, selecione **Certificado de Importação** e navegue no certificado. Introduza a **palavra-passe PFX**e, em seguida, selecione **Adicionar**.  
   - Para configurar opções avançadas de assinatura de certificado, utilize as seguintes opções. Para descrições destas opções, consulte o artigo [de opções avançadas de assinatura de certificado.](certificate-signing-options.md)
      - Na lista de drop-down **da Opção** de Assinatura, escolha **a resposta Do Sinal SAML,** **Assine a afirmação SAML,** ou **assine a resposta e afirmação do SAML**.
      - Na lista de drop-down do Algoritmo de **Assinatura,** escolha **SHA-1** ou **SHA-256**.
   - Para notificar pessoas adicionais quando o certificado ativo estiver próximo da data de validade, insira os endereços de e-mail nos campos de **endereços de e-mail de Notificação.**

4. Se escoar as alterações, **selecione Guardar** no topo da secção de Certificado de **Assinatura SAML.** 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>Passo 4. Configurar a aplicação para usar a Azure AD

A secção **Configuração \<applicationName> ** lista os valores que precisam de ser configurados na aplicação para que utilize a Azure AD como fornecedor de identidade SAML. Os valores exigidos variam de acordo com a aplicação. Para mais detalhes, consulte a documentação SAML da aplicação. Para encontrar a documentação, vá ao título **configurar \<application name> ** e selecione **Ver instruções passo a passo**. A documentação aparece na página de inscrição do **Configure.** Esta página guia-o para preencher os valores **de URL de login,** **identificador AD Ad**e **logout** no título **set up. \<application name> **

1. Desloque-se até à secção **de configuração. \<applicationName> ** 
   
   ![Passo 4 Configurar a aplicação](media/configure-single-sign-on-non-gallery-applications/step-four-app-config.png)

1. Copie o valor de cada linha nesta secção conforme necessário e siga as instruções específicas da aplicação para adicionar o valor à aplicação. Para aplicações de galeria, pode ver a documentação selecionando **instruções passo a passo.** 
   - Os valores **de URL** de login e **URL logout** resolvem ambos para o mesmo ponto final, que é o ponto final de tratamento de pedidos SAML para o seu exemplo de Azure AD. 
   - O **Identificador Azure AD** é o valor do **emitente** no token SAML emitido para o pedido.
2. Quando colar todos os valores nos campos apropriados, **selecione Save**.

## <a name="step-5-validate-single-sign-on"></a>Passo 5. Validar um único sinal

Uma vez configurado a sua aplicação para utilizar o Azure AD como fornecedor de identidade baseado em SAML, pode testar as definições para ver se um único sinal funciona para a sua conta. 

2. Percorra para a única **s placar <applicationName> validate com** a secção.

   ![Passo 5 Valide um único sinal de inscrição](media/configure-single-sign-on-non-gallery-applications/step-five-validate.png)

3. Selecione **Validar**. As opções do teste aparecem.

4. Selecione **Iniciar sposição como utilizador atual**. 

Se a sessão for bem sucedida, está pronta para atribuir utilizadores e grupos à sua aplicação SAML.
Se aparecer uma mensagem de erro, complete os seguintes passos:

1. Copie e cole os detalhes na caixa **Qual é o aspeto do erro?**.

    ![Obter orientações de resolução](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Selecione **Obter orientação de resolução**. A orientação da causa e da resolução aparecem.  Neste exemplo, o utilizador não foi designado para a aplicação.

3. Leia a orientação de resolução e, se possível, corrija o problema.

4. Execute o teste novamente, até que seja concluído sem erros.

Para obter mais informações, consulte [o único sign-on baseado em Debug SAML para aplicações no Azure Ative Directory](../azuread-dev/howto-v1-debug-saml-sso-issues.md).

## <a name="next-steps"></a>Passos seguintes

- [Atribuir utilizadores ou grupos à aplicação](methods-for-assigning-users-and-groups.md)
- [Configurar o provisionamento automático da conta de utilizador](../app-provisioning/configure-automatic-user-provisioning-portal.md)
