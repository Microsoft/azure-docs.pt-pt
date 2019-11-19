---
title: Logon único do SAML – aplicativos que não são da Galeria-plataforma de identidade da Microsoft | Microsoft Docs
description: Configurar o SSO (logon único) para aplicativos que não são da galeria na plataforma Microsoft Identity (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 07/19/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2dcc2d6fc252f288f15e2583012798b4d0e9cee6
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74169444"
---
# <a name="configure-saml-based-single-sign-on-to-non-gallery-applications"></a>Configurar o logon único baseado em SAML para aplicativos que não são da Galeria

Quando você [adiciona um aplicativo de galeria](add-gallery-app.md) ou um [aplicativo Web que não é da Galeria](add-non-gallery-app.md) a seus aplicativos empresariais do Azure AD, uma das opções de logon único disponíveis é o [logon único baseado em SAML](what-is-single-sign-on.md#saml-sso). Escolha SAML sempre que possível para aplicativos que se autenticam usando um dos protocolos SAML. Com o logon único do SAML, o Azure AD é autenticado no aplicativo usando a conta do Azure AD do usuário. O Azure AD comunica as informações de início de sessão para a aplicação através de um protocolo de ligação. Você pode mapear usuários para funções de aplicativo específicas com base nas regras que você define em suas declarações SAML. Este artigo descreve como configurar o logon único baseado em SAML para um aplicativo inexistente na galeria. 

> [!NOTE]
> Adicionando um aplicativo de galeria? Encontre instruções de instalação passo a passo na [lista de tutoriais do aplicativo SaaS](../saas-apps/tutorial-list.md)

Para configurar o logon único do SAML para um aplicativo que não seja da Galeria sem escrever código, você precisa ter uma assinatura ou Azure AD Premium e o aplicativo deve dar suporte a SAML 2,0. Para obter mais informações sobre versões do Azure AD, visite [preços do Azure ad](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="before-you-begin"></a>Antes de começar

Se o aplicativo não tiver sido adicionado ao seu locatário do Azure AD, consulte [Adicionar um aplicativo inexistente na Galeria](add-non-gallery-app.md).

## <a name="step-1-edit-the-basic-saml-configuration"></a>Passo 1. Editar a configuração básica do SAML

1. Entre no [portal do Azure](https://portal.azure.com) como um administrador de aplicativo de nuvem ou um administrador de aplicativo para seu locatário do Azure AD.

2. Navegue até **Azure Active Directory** > **aplicativos empresariais** e selecione o aplicativo na lista. 
   
   - Para pesquisar o aplicativo, no menu **tipo de aplicativo** , selecione **todos os aplicativos**e, em seguida, selecione **aplicar**. Insira o nome do aplicativo na caixa de pesquisa e, em seguida, selecione o aplicativo nos resultados.

3. Na seção **gerenciar** , selecione **logon único**. 

4. Selecione **SAML**. A página **Configurar logon único com SAML-Preview** é exibida.

   ![Etapa 1 editar a configuração básica do SAML](media/configure-single-sign-on-non-gallery-applications/step-one-basic-saml-config.png)

5. Para editar as opções básicas de configuração do SAML, selecione o ícone **Editar** (um lápis) no canto superior direito da seção **configuração básica do SAML** .

1. Introduza as seguintes definições. Você deve obter os valores do fornecedor do aplicativo. Você pode inserir manualmente os valores ou carregar um arquivo de metadados para extrair o valor dos campos.

    | Definição de configuração básica do SAML | Iniciado pelo SP | Iniciado pelo idP | Descrição |
    |:--|:--|:--|:--|
    | **Identificador (ID da entidade)** | Necessário para algumas aplicações | Necessário para algumas aplicações | Identifica exclusivamente o aplicativo. O Azure AD envia o identificador para o aplicativo como o parâmetro Audience do token SAML. Espera-se que o aplicativo o valide. Este valor também aparece como o ID da Entidade nos metadados SAML que a aplicação fornece. Insira uma URL que usa o seguinte padrão: ' https://<subdomain>. contoso.com ' *você pode encontrar esse valor como o elemento **emissor** no **AuthnRequest** (solicitação SAML) enviado pelo aplicativo.* |
    | **URL de resposta** | Necessário | Necessário | Especifica onde é que a aplicação espera receber o token SAML. O URL de resposta também é denominado URL do Serviço de Consumidor de Asserções (ACS). Você pode usar os campos de URL de resposta adicionais para especificar várias URLs de resposta. Por exemplo, você pode precisar de URLs de resposta adicionais para vários subdomínios. Ou, para fins de teste, você pode especificar várias URLs de resposta (host local e URLs públicas) ao mesmo tempo. |
    | **URL de início de sessão** | Necessário | Não especificar | Quando um utilizador abre este URL, o fornecedor de serviços redireciona para o Azure AD para autenticar e iniciar a sessão do utilizador. O Azure AD usa a URL para iniciar o aplicativo do Office 365 ou do painel de acesso do Azure AD. Quando em branco, o Azure AD executa o logon iniciado pelo IdP quando um usuário inicia o aplicativo do Office 365, do painel de acesso do Azure AD ou da URL de SSO do Azure AD.|
    | **Estado de retransmissão** | Opcional | Opcional | Especifica à aplicação para onde deve redirecionar o utilizador após a conclusão da autenticação. Normalmente, o valor é uma URL válida para o aplicativo. No entanto, alguns aplicativos usam esse campo de forma diferente. Para obter mais informações, contacte o fornecedor da aplicação.
    | **URL de logout** | Opcional | Opcional | Usado para enviar as respostas de logout SAML de volta para o aplicativo.

Para obter mais informações, consulte [protocolo SAML de logon único](../develop/single-sign-on-saml-protocol.md).

## <a name="step-2-configure-user-attributes-and-claims"></a>Passo 2. Configurar atributos e declarações de usuário 

Quando um usuário é autenticado no aplicativo, o Azure AD emite ao aplicativo um token SAML com informações (ou declarações) sobre o usuário que os identifica exclusivamente. Por padrão, essas informações incluem o nome do usuário, o endereço de email, o nome e o sobrenome. Talvez seja necessário personalizar essas declarações se, por exemplo, o aplicativo exigir valores de declaração específicos ou um formato de **nome** diferente de nome de usuário. Os requisitos para aplicativos da galeria são descritos nos [tutoriais específicos do aplicativo](../saas-apps/tutorial-list.md)ou você pode solicitar ao fornecedor do aplicativo. As etapas gerais para configurar atributos e declarações de usuário são descritas abaixo.

1. Na seção **atributos e declarações de usuário** , selecione o ícone de **edição** (um lápis) no canto superior direito.

   ![Etapa 2 Configurar atributos e declarações de usuário](media/configure-single-sign-on-non-gallery-applications/step-two-user-attributes-claims.png)

2. Verifique o **valor do identificador de nome**. O valor padrão é *User. PrincipalName*. O identificador de utilizador identifica exclusivamente cada utilizador da aplicação. Por exemplo, se o endereço de e-mail for o nome de utilizador e o identificador exclusivo, defina o valor como *user.mail*.

3. Para modificar o **valor do identificador de nome**, selecione o ícone de **edição** (um lápis) para o campo valor do **identificador de nome** . Faça as alterações apropriadas para o formato e a origem do identificador, conforme necessário. Para obter detalhes, consulte [editando NameID](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#editing-nameid). Salve as alterações quando terminar. 
 
4. Para configurar as declarações de grupo, selecione o ícone **Editar** para os **grupos retornados no campo declaração** . Para obter detalhes, consulte [Configurar declarações de grupo](../hybrid/how-to-connect-fed-group-claims.md).

5. Para adicionar uma declaração, selecione **Adicionar nova declaração** na parte superior da página. Insira o **nome** e selecione a origem apropriada. Se você selecionar a origem do **atributo** , precisará escolher o **atributo de origem** que deseja usar. Se você selecionar a origem da **tradução** , precisará escolher a **transformação** e o **parâmetro 1** que deseja usar. Para obter detalhes, consulte [adicionando declarações específicas do aplicativo](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#adding-application-specific-claims). Salve as alterações quando terminar. 

6. Selecione **Guardar**. A nova declaração aparece na tabela.

   > [!NOTE]
   > Para obter outras maneiras de personalizar o token SAML do Azure AD para seu aplicativo, consulte os recursos a seguir.
   >- Para criar funções personalizadas por meio do portal do Azure, consulte [Configurar declarações de função](../develop/active-directory-enterprise-app-role-management.md).
   >- Para personalizar as declarações por meio do PowerShell, consulte [Personalizar declarações-PowerShell](../develop/active-directory-claims-mapping.md).
   >- Para modificar o manifesto do aplicativo para configurar declarações opcionais para seu aplicativo, consulte [Configurar declarações opcionais](../develop/active-directory-optional-claims.md).
   >- Para definir políticas de tempo de vida de token para tokens de atualização, tokens de acesso, tokens de sessão e tokens de ID, consulte [Configurar tempos de vida de token](../develop/active-directory-configurable-token-lifetimes.md) Ou, para restringir sessões de autenticação por meio do acesso condicional do Azure AD, consulte [recursos de gerenciamento de sessão de autenticação](https://go.microsoft.com/fwlink/?linkid=2083106).

## <a name="step-3-manage-the-saml-signing-certificate"></a>Passo 3: Gerenciar o certificado de autenticação SAML

O Azure AD usa um certificado para assinar os tokens SAML que ele envia para o aplicativo. Você precisa desse certificado para configurar a relação de confiança entre o Azure AD e o aplicativo. Para obter detalhes sobre o formato do certificado, consulte a documentação do SAML do aplicativo. Para obter mais informações, consulte [gerenciar certificados para logon único federado](manage-certificates-for-federated-single-sign-on.md) e [Opções avançadas de assinatura de certificado no token SAML](certificate-signing-options.md).

No Azure AD, você pode baixar o certificado ativo no formato base64 ou RAW diretamente do principal **Configurar logon único com a página SAML** . Como alternativa, você pode obter o certificado ativo baixando o arquivo XML de metadados do aplicativo ou usando a URL de metadados de Federação do aplicativo. Para exibir, criar ou baixar seus certificados (ativos ou inativos), siga estas etapas.

1. Vá para a seção **certificado de autenticação SAML** . 

   ![Etapa 3 gerenciar o certificado de autenticação SAML](./media/configure-single-sign-on-non-gallery-applications/step-three-certificate.png)

2. Verifique se o certificado tem:

   - *A data de expiração desejada.* Você pode configurar a data de validade por até três anos no futuro.
   - *Um status ativo para o certificado desejado.* Se o status for **inativo**, altere o status para **ativo**. Para alterar o status, clique com o botão direito do mouse na linha do certificado desejado e selecione **tornar certificado ativo**.
   - *A opção de assinatura e o algoritmo corretos.*
   - *Os endereços de email de notificação corretos.* Quando o certificado ativo está próximo da data de validade, o Azure AD envia uma notificação para o endereço de email configurado neste campo.

2. Para baixar o certificado, selecione uma das opções de formato base64, formato bruto ou XML de metadados de Federação. O AD do Azure também fornece a **URL de metadados de Federação do aplicativo** , na qual você pode acessar os metadados específicos para o aplicativo no formato `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`.

3. Para gerenciar, criar ou importar um certificado, selecione o ícone de **edição** (um lápis) no canto superior direito da seção certificado de **autenticação SAML** .

   ![Certificado de autenticação SAML](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)


   Execute qualquer uma das seguintes ações:

   - Para criar um novo certificado, selecione **novo certificado**, selecione a **data de validade**e, em seguida, selecione **salvar**. Para ativar o certificado, selecione o menu de contexto ( **...** ) e selecione **tornar o certificado ativo**.
   - Para carregar um certificado com as credenciais de chave privada e pfx, selecione **importar certificado** e navegue até o certificado. Insira a **senha pfx**e, em seguida, selecione **Adicionar**.  
   - Para configurar as opções avançadas de assinatura de certificado, use as opções a seguir. Para obter descrições dessas opções, consulte o artigo [Opções avançadas de assinatura de certificado](certificate-signing-options.md) .
      - Na lista **suspensa opção de assinatura** , escolha **assinar resposta SAML**, **assinar Asserção SAML**ou **assinar resposta e Asserção SAML**.
      - Na lista suspensa **algoritmo de assinatura** , escolha **SHA-1** ou **SHA-256**.
   - Para notificar outras pessoas quando o certificado ativo estiver próximo da data de validade, insira os endereços de email nos campos de **email de notificação** .

4. Se você tiver feito alterações, selecione **salvar** na parte superior da seção **certificado de autenticação SAML** . 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>Passo 4: Configurar o aplicativo para usar o Azure AD

A seção **configurar \<applicationName >** lista os valores que precisam ser configurados no aplicativo para que ele use o Azure ad como um provedor de identidade SAML. Os valores necessários variam de acordo com o aplicativo. Para obter detalhes, consulte a documentação do SAML do aplicativo. Para encontrar a documentação, vá para o cabeçalho **configurar \<nome do aplicativo >** e selecione **exibir instruções passo a passo**. A documentação é exibida na página **Configurar logon** . Essa página o orienta a preencher a **URL de logon**, o **identificador do Azure ad**e os valores de URL de **logout** no título **Configurar \<aplicativo nome >** .

1. Role para baixo até a seção **configurar \<applicationName >** . 
   
   ![Etapa 4 configurar o aplicativo](media/configure-single-sign-on-non-gallery-applications/step-four-app-config.png)

1. Copie o valor de cada linha desta seção conforme necessário e siga as instruções específicas do aplicativo para adicionar o valor ao aplicativo. Para aplicativos da galeria, você pode exibir a documentação selecionando **exibir instruções**passo a passo. 
   - Os valores de URL de **logon** e **URL de logout** são resolvidos para o mesmo ponto de extremidade, que é o ponto de extremidade de tratamento de solicitação SAML para sua instância do Azure AD. 
   - O **identificador do Azure ad** é o valor do **emissor** no token SAML emitido para o aplicativo.
2. Depois de colar todos os valores nos campos apropriados, selecione **salvar**.

## <a name="step-5-validate-single-sign-on"></a>Passo 5: Validar logon único

Depois de configurar seu aplicativo para usar o Azure AD como um provedor de identidade baseado em SAML, você pode testar as configurações para ver se o logon único funciona para sua conta. 

2. Role até a seção **validar logon único com <applicationName>** .

   ![Etapa 5: validar o logon único](media/configure-single-sign-on-non-gallery-applications/step-five-validate.png)

3. Selecione **Validar**. As opções do teste aparecem.

4. Selecione **entrar como usuário atual**. 

Se o logon for bem-sucedido, você estará pronto para atribuir usuários e grupos ao aplicativo SAML.
Se uma mensagem de erro for exibida, conclua as seguintes etapas:

1. Copie e cole os detalhes na caixa **Qual é o aspeto do erro?** .

    ![Obter orientações de resolução](media/configure-single-sign-on-portal/error-guidance.png)

2. Selecione **obter diretrizes de resolução**. As diretrizes de causa e resolução raiz são exibidas.  Neste exemplo, o usuário não foi atribuído ao aplicativo.

3. Leia as diretrizes de resolução e, se possível, corrija o problema.

4. Execute o teste novamente, até que seja concluído sem erros.

Para obter mais informações, consulte [depurar o logon único baseado em SAML para aplicativos no Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md).

## <a name="next-steps"></a>Passos seguintes

- [Atribuir usuários ou grupos ao aplicativo](methods-for-assigning-users-and-groups.md)
- [Configurar o provisionamento automático de conta de usuário](configure-automatic-user-provisioning-portal.md)
