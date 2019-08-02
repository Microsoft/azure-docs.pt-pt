---
title: Configurar o início de sessão único - Azure Active Directory | Microsoft Docs
description: Este tutorial utiliza o portal do Azure para configurar o início de sessão único baseado em SAML para uma aplicação com o Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 04/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: c5e8ed4a78fccce4f3a5c631a99a8729114e5722
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422594"
---
# <a name="how-to-configure-saml-based-single-sign-on"></a>Como configurar o logon único baseado em SAML

Depois de adicionar um aplicativo a seus aplicativos empresariais do Azure AD, você configura as configurações de logon único. Este artigo descreve como configurar o logon único baseado em SAML para um aplicativo inexistente na galeria. 

> [!NOTE]
> Adicionando um aplicativo de galeria? Encontre instruções de instalação passo a passo na [lista de tutoriais do aplicativo SaaS](../saas-apps/tutorial-list.md)

Para configurar o logon único para um aplicativo que não seja da Galeria *sem escrever código*, você precisa ter uma assinatura ou Azure ad Premium e o aplicativo deve dar suporte a SAML 2,0. Para obter mais informações sobre versões do Azure AD, visite [preços do Azure ad](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="before-you-begin"></a>Antes de começar

- Se o aplicativo não tiver sido adicionado ao seu locatário do Azure AD, consulte [Adicionar um aplicativo da Galeria](add-gallery-app.md) ou [Adicionar um aplicativo](add-non-gallery-app.md)inexistente na galeria.
- Contate o fornecedor do aplicativo para obter as informações corretas para as seguintes configurações:

    | Definição de configuração básica do SAML | Iniciado pelo SP | Iniciado pelo idP | Descrição |
    |:--|:--|:--|:--|
    | Identificador (ID de Entidade) | Necessário para algumas aplicações | Necessário para algumas aplicações | Identifica exclusivamente a aplicação para a qual o início de sessão único está a ser configurado. O Azure AD envia o identificador para o aplicativo como o parâmetro Audience do token SAML. Espera-se que o aplicativo o valide. Este valor também aparece como o ID da Entidade nos metadados SAML que a aplicação fornece. *Você pode encontrar esse valor como o elemento **emissor** no **AuthnRequest** (solicitação SAML) enviado pelo aplicativo.* |
    | URL de Resposta | Opcional | Requerido | Especifica onde é que a aplicação espera receber o token SAML. O URL de resposta também é denominado URL do Serviço de Consumidor de Asserções (ACS). |
    | URL de início de sessão | Requerido | Não especificar | Quando um utilizador abre este URL, o fornecedor de serviços redireciona para o Azure AD para autenticar e iniciar a sessão do utilizador. O Azure AD usa a URL para iniciar o aplicativo do Office 365 ou do painel de acesso do Azure AD. Quando em branco, o AD do Azure depende do provedor de identidade para iniciar o logon único quando um usuário inicia o aplicativo.|
    | Estado de Reencaminhamento | Opcional | Opcional | Especifica à aplicação para onde deve redirecionar o utilizador após a conclusão da autenticação. Normalmente, o valor é uma URL válida para o aplicativo. No entanto, alguns aplicativos usam esse campo de forma diferente. Para obter mais informações, contacte o fornecedor da aplicação.
    | URL de fim de sessão | Opcional | Opcional | Usado para enviar as respostas de logout SAML de volta para o aplicativo.

## <a name="step-1-edit-the-basic-saml-configuration"></a>Passo 1. Editar a configuração básica do SAML

1. Entre no [portal do Azure](https://portal.azure.com) como um administrador de aplicativo de nuvem ou um administrador de aplicativo para seu locatário do Azure AD.

1. Navegue até **Azure Active Directory** > **aplicativos empresariais** e selecione o aplicativo na lista. 
   
   - Para pesquisar o aplicativo, no menu **tipo de aplicativo** , selecione **todos os aplicativos**e, em seguida, selecione **aplicar**. Insira o nome do aplicativo na caixa de pesquisa e, em seguida, selecione o aplicativo nos resultados.

1. Na seção **gerenciar** , selecione **logon único**. 

1. Selecione **SAML**. A página **Configurar logon único com SAML-Preview** é exibida.

1. Para editar as opções básicas de configuração do SAML, selecione o ícone **Editar** (um lápis) no canto superior direito da seção **configuração básica do SAML** .

     ![Configurar os certificados](media/configure-single-sign-on-portal/basic-saml-configuration-edit-icon.png)

1. Nos campos apropriados, insira as informações descritas na seção [antes de começar](#before-you-begin) .

1. Na parte superior da página, selecione **salvar**.

## <a name="step-2-configure-user-attributes-and-claims"></a>Passo 2. Configurar atributos e declarações de usuário 

Um aplicativo pode exigir atributos de usuário específicos ou declarações no token SAML que recebe do Azure AD quando um usuário faz logon. Por exemplo, URIs de declaração específicos ou valores de declaração podem ser necessários, ou o **nome** pode precisar ser algo diferente do nome de usuário armazenado na plataforma de identidade da Microsoft. Os requisitos para aplicativos da galeria são descritos nos [tutoriais específicos do aplicativo](../saas-apps/tutorial-list.md)ou você pode solicitar ao fornecedor do aplicativo. As etapas gerais para configurar atributos e declarações de usuário são descritas abaixo.

1. Na seção **atributos e declarações de usuário** , selecione o ícone de **edição** (um lápis) no canto superior direito.

1. Verifique o **valor do identificador de nome**. O valor padrão é *User. PrincipalName*. O identificador de utilizador identifica exclusivamente cada utilizador da aplicação. Por exemplo, se o endereço de e-mail for o nome de utilizador e o identificador exclusivo, defina o valor como *user.mail*.

1. Para modificar o **valor do identificador de nome**, selecione o ícone de **edição** (um lápis) para o campo valor do **identificador de nome** . Faça as alterações apropriadas para o formato e a origem do identificador, conforme necessário. Para obter detalhes, [](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#editing-nameid)consulte Editando NameID. Salve as alterações quando terminar. 
 
1. Para configurar as declarações de grupo, selecione o ícone **Editar** para os **grupos retornados no campo declaração** . Para obter detalhes, consulte [Configurar declarações de grupo](../hybrid/how-to-connect-fed-group-claims.md).

3. Para adicionar uma declaração, selecione **Adicionar nova declaração** na parte superior da página. Insira o **nome** e selecione a origem apropriada. Se você selecionar a origem do **atributo** , precisará escolher o **atributo de origem** que deseja usar. Se você selecionar a origem da **tradução** , precisará escolher a **transformação** e o **parâmetro 1** que deseja usar. Para obter detalhes, consulte [adicionando declarações específicas do aplicativo](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#adding-application-specific-claims). Salve as alterações quando terminar. 

4. Selecione **Guardar**. A nova declaração aparece na tabela.

   > [!NOTE]
   > Para obter outras maneiras de personalizar o token SAML do Azure AD para seu aplicativo, consulte os recursos a seguir.
   >- Para criar funções personalizadas por meio do portal do Azure, consulte [Configurar declarações de função](../develop/active-directory-enterprise-app-role-management.md).
   >- Para personalizar as declarações por meio do PowerShell, consulte [Personalizar declarações-PowerShell](../develop/active-directory-claims-mapping.md).
   >- Para modificar o manifesto do aplicativo para configurar declarações opcionais para seu aplicativo, consulte [Configurar declarações opcionais](../develop/active-directory-optional-claims.md).
   >- Para definir políticas de tempo de vida de token para tokens de atualização, tokens de acesso, tokens de sessão e tokens de ID, consulte [Configurar tempos de vida de token](../develop/active-directory-configurable-token-lifetimes.md) Ou, para restringir sessões de autenticação por meio do acesso condicional do Azure AD, consulte [recursos de gerenciamento de sessão de autenticação](https://go.microsoft.com/fwlink/?linkid=2083106).

## <a name="step-3-manage-the-saml-signing-certificate"></a>Passo 3: Gerenciar o certificado de autenticação SAML

O Azure AD usa um certificado para assinar os tokens SAML que ele envia para o aplicativo. Na página **Configurar logon único com SAML** , você pode exibir ou baixar o certificado ativo. Você também pode atualizar, criar ou importar um certificado. Para aplicativos da galeria, os detalhes sobre o formato do certificado estão disponíveis na documentação do SAML do aplicativo (consulte os [tutoriais específicos do aplicativo](../saas-apps/tutorial-list.md)). 

1. Vá para a seção **certificado de autenticação SAML** . Dependendo do tipo de aplicativo, você verá opções para baixar o certificado no formato base64, no formato bruto ou no XML de metadados de Federação. O AD do Azure também fornece a **URL de metadados de Federação do aplicativo** , na qual você pode acessar os metadados `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`específicos para o aplicativo no formato.

1. Para gerenciar, criar ou importar um certificado, selecione o ícone **Editar** (um lápis) no canto superior direito da seção **certificado de autenticação SAML** e, em seguida, faça o seguinte:

   - Para criar um novo certificado, selecione **novo certificado**, selecione a **data de validade**e, em seguida, selecione **salvar**. Para ativar o certificado, selecione o menu de contexto ( **...** ) e selecione **tornar o certificado ativo**.
   - Para carregar um certificado com as credenciais de chave privada e pfx, selecione **importar certificado** e navegue até o certificado. Insira a **senha pfx**e, em seguida, selecione **Adicionar**.  
   - Para configurar as opções avançadas de assinatura de certificado, use as opções a seguir. Para obter descrições dessas opções, consulte o artigo [Opções avançadas de assinatura de certificado](certificate-signing-options.md) .
      - Na lista **suspensa opção de assinatura** , escolha **assinar resposta SAML**, **assinar Asserção SAML**ou **assinar resposta e Asserção SAML**.
      - Na lista suspensa **algoritmo de assinatura** , escolha **SHA-1** ou **SHA-256**.
   - Para notificar outras pessoas quando o certificado ativo estiver próximo da data de validade, insira os endereços de email nos campos de **email de notificação** .

1. Selecione **salvar** na parte superior da seção **certificado de autenticação SAML** . 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>Passo 4: Configurar o aplicativo para usar o Azure AD

A seção **Configurar \<o ApplicationName >** lista os valores que precisam ser configurados no aplicativo para que ele use o Azure ad como um provedor de identidade SAML. Os valores necessários variam de acordo com o aplicativo. Para obter detalhes, consulte a documentação do SAML do aplicativo.

1. Role para baixo até a seção  **\<configurar o ApplicationName >** . 
2. Copie o valor de cada linha desta seção conforme necessário e siga as instruções específicas do aplicativo para adicionar o valor ao aplicativo. Para aplicativos da galeria, você pode exibir a documentação selecionando **exibir instruções**passo a passo. 
   - Os valores de URL de **logon** e **URL de logout** são resolvidos para o mesmo ponto de extremidade, que é o ponto de extremidade de tratamento de solicitação SAML para sua instância do Azure AD. 
   - O **identificador do Azure ad** é o valor do **emissor** no token SAML emitido para o aplicativo.
1. Depois de colar todos os valores nos campos apropriados, selecione **salvar**.

## <a name="step-5-validate-single-sign-on"></a>Passo 5: Validar logon único

Você está pronto para testar as configurações para ver se o logon único funciona para você, o administrador.  

1. Abra as definições do início de sessão único para a sua aplicação. 
2. Role até a seção **validar logon único com <applicationName>**  . Para este tutorial, esta seção é chamada **Configurar GitHub-Test**.
3. Selecione **testar**. As opções do teste aparecem.
4. Selecione **entrar como usuário atual**. 

Se o logon for bem-sucedido, você estará pronto para atribuir usuários e grupos ao aplicativo SAML.
Se uma mensagem de erro for exibida, conclua as seguintes etapas:

1. Copie e cole os detalhes na caixa **Qual é o aspeto do erro?** .

    ![Use a caixa "o que é o erro se parece" para obter diretrizes de resolução](media/configure-single-sign-on-portal/error-guidance.png)

1. Selecione **obter diretrizes de resolução**. As diretrizes de causa e resolução raiz são exibidas.  Neste exemplo, o usuário não foi atribuído ao aplicativo.
1. Leia as diretrizes de resolução e, se possível, corrija o problema.
1. Execute o teste novamente, até que seja concluído sem erros.

## <a name="next-steps"></a>Passos Seguintes

- [Atribuir usuários ou grupos ao aplicativo](methods-for-assigning-users-and-groups.md)
- [Configurar o provisionamento automático de conta de usuário](configure-automatic-user-provisioning-portal.md)
