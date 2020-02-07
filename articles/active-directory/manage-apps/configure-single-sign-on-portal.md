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
ms.openlocfilehash: a415ac749d0d322bc2f71f64d4bec6e32ad1f12e
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063497"
---
# <a name="how-to-configure-saml-based-single-sign-on"></a>Como configurar o único sign-on baseado em SAML

Depois de ter adicionado uma aplicação às suas Aplicações Empresariais AD Azure, configura as definições de inscrição única. Este artigo descreve como configurar um único sign-on baseado em SAML para uma aplicação não-galeria. 

> [!NOTE]
> Adicionar uma aplicação de galeria? Encontre instruções passo a passo na [lista de tutoriais de aplicações SaaS](../saas-apps/tutorial-list.md)

Para configurar um único sinal para uma aplicação *não-galeria sem código*de escrita, precisa de ter uma subscrição ou Azure AD Premium e a aplicação deve suportar o SAML 2.0. Para mais informações sobre as versões Azure AD, visite [o preço da AD Azure.](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="before-you-begin"></a>Antes de começar

- Se a aplicação não tiver sido adicionada ao seu inquilino DaD Azure, consulte [Adicionar uma aplicação](add-gallery-app.md) de galeria ou [adicionar uma aplicação não-galeria](add-non-gallery-app.md).
- Contacte o seu fornecedor de aplicações para obter as informações corretas para as seguintes definições:

    | Definição básica de configuração SAML | Iniciado pelo SP | Iniciado pelo idP | Descrição |
    |:--|:--|:--|:--|
    | Identificador (ID de Entidade) | Necessário para algumas aplicações | Necessário para algumas aplicações | Identifica exclusivamente a aplicação para a qual o início de sessão único está a ser configurado. A Azure AD envia o identificador para a aplicação como parâmetro do público do símbolo SAML. Espera-se que a aplicação o valide. Este valor também aparece como o ID da Entidade nos metadados SAML que a aplicação fornece. *Pode encontrar este valor como elemento **Emitente** no **Pedido AuthnRequest** (pedido SAML) enviado pelo pedido.* |
    | URL de Resposta | Opcional | Necessário | Especifica onde é que a aplicação espera receber o token SAML. O URL de resposta também é denominado URL do Serviço de Consumidor de Asserções (ACS). |
    | URL de início de sessão | Necessário | Não especifique | Quando um utilizador abre este URL, o fornecedor de serviços redireciona para o Azure AD para autenticar e iniciar a sessão do utilizador. A Azure AD utiliza o URL para iniciar a aplicação a partir do Office 365 ou do Painel de Acesso AD Azure. Quando em branco, a Azure AD conta com o fornecedor de identidade para iniciar um único sinal quando um utilizador lançar a aplicação.|
    | Estado de Reencaminhamento | Opcional | Opcional | Especifica à aplicação para onde deve redirecionar o utilizador após a conclusão da autenticação. Tipicamente, o valor é um URL válido para a aplicação. No entanto, algumas aplicações utilizam este campo de forma diferente. Para obter mais informações, contacte o fornecedor da aplicação.
    | URL de fim de sessão | Opcional | Opcional | Usado para enviar as respostas de logout SAML de volta para a aplicação.

## <a name="step-1-edit-the-basic-saml-configuration"></a>Passo 1. Editar a configuração Básica sAML

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador de aplicação em nuvem ou administrador de candidatura para o seu inquilino Azure AD.

1. Navegue para **as aplicações** de **Diretório Ativo** > Enterprise e selecione a aplicação da lista. 
   
   - Para procurar a aplicação, no menu **Tipo de Aplicação,** selecione **Todas as aplicações,** e depois selecione **Aplicar**. Introduza o nome da aplicação na caixa de pesquisa e, em seguida, selecione a aplicação a partir dos resultados.

1. Na secção **Gerir,** selecione **single sign-on**. 

1. Selecione **SAML**. Aparece a **configuração do único sign-on com a página SAML - Pré-visualização.**

1. Para editar as opções básicas de configuração do SAML, selecione o ícone **Editar** (um lápis) no canto superior direito da secção **de Configuração SAML Básica.**

     ![Configurar os certificados](media/configure-single-sign-on-portal/basic-saml-configuration-edit-icon.png)

1. Nos campos apropriados, introduza as informações descritas na secção [Antes de iniciar.](#before-you-begin)

1. No topo da página, selecione **Guardar**.

## <a name="step-2-configure-user-attributes-and-claims"></a>Passo 2. Configure os atributos e reclamações do Utilizador 

Uma aplicação pode exigir atributos ou reclamações específicas do utilizador no token SAML que recebe da AD Azure quando um utilizador faz o sinal de entrada. Por exemplo, uris de reclamação específicas ou valores de reclamação podem ser necessários, ou **o Nome** pode ter de ser algo diferente do nome de utilizador armazenado na plataforma de identidade da Microsoft. Os requisitos para aplicações de galerias são descritos nos [tutoriais específicos da aplicação,](../saas-apps/tutorial-list.md)ou pode perguntar ao fornecedor de aplicações. Os passos gerais para configurar os atributos e reclamações dos utilizadores são descritos abaixo.

1. Na secção **Atributos e Reivindicações** do Utilizador, selecione o ícone **Editar** (um lápis) no canto superior direito.

1. Verifique o valor do **identificador**de nome . O valor predefinido é *user.mainname*. O identificador de utilizador identifica exclusivamente cada utilizador da aplicação. Por exemplo, se o endereço de e-mail for o nome de utilizador e o identificador exclusivo, defina o valor como *user.mail*.

1. Para modificar o valor do **identificador**de nome, selecione o ícone **Editar** (um lápis) para o campo **Name Identifier Value.** Faça as alterações adequadas ao formato e fonte do identificador, conforme necessário. Para mais detalhes, consulte [Editar NameId](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#editing-nameid). Guarde as mudanças quando terminar. 
 
1. Para configurar as reivindicações de grupo, selecione o ícone **Editar** para os **Grupos devolvidos no campo de reclamação.** Para mais detalhes, consulte [as alegações do grupo Configure](../hybrid/how-to-connect-fed-group-claims.md).

3. Para adicionar uma reclamação, selecione **Adicionar nova reclamação** no topo da página. Introduza o **Nome** e selecione a fonte adequada. Se selecionar a fonte **do Atributo,** terá de escolher o **atributo Fonte** que pretende utilizar. Se selecionar a fonte de **Tradução,** terá de escolher a **Transformação** e **o Parâmetro 1** que pretende utilizar. Para mais detalhes, consulte [adicionar reclamações específicas da aplicação.](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#adding-application-specific-claims) Guarde as mudanças quando terminar. 

4. Selecione **Guardar**. A nova reivindicação aparece na mesa.

   > [!NOTE]
   > Para obter formas adicionais de personalizar o token SAML da Azure AD à sua aplicação, consulte os seguintes recursos.
   >- Para criar papéis personalizados através do portal Azure, consulte as alegações de [função Configure.](../develop/active-directory-enterprise-app-role-management.md)
   >- Para personalizar as reclamações via PowerShell, consulte [Personalizar reclamações - PowerShell](../develop/active-directory-claims-mapping.md).
   >- Para modificar o manifesto de aplicação para configurar reclamações opcionais para a sua aplicação, consulte [as reclamações opcionais do Configure](../develop/active-directory-optional-claims.md).
   >- Para definir políticas de vida simbólicas para tokens de atualização, fichas de acesso, fichas de sessão e fichas de identificação, consulte a configuração de [token lifetimes](../develop/active-directory-configurable-token-lifetimes.md). Ou, para restringir as sessões de autenticação via Acesso Condicional Azure AD, consulte as capacidades de gestão da sessão de [autenticação.](https://go.microsoft.com/fwlink/?linkid=2083106)

## <a name="step-3-manage-the-saml-signing-certificate"></a>Passo 3. Gerir o certificado de assinatura SAML

A Azure AD utiliza um certificado para assinar as fichas SAML que envia para o pedido. Na **configuração de um único sign-on com página SAML,** pode visualizar ou descarregar o certificado ativo. Também pode atualizar, criar ou importar um certificado. Para aplicações de galeria, detalhes sobre o formato do certificado estão disponíveis na documentação SAML da aplicação (ver [tutoriais específicos da aplicação).](../saas-apps/tutorial-list.md) 

1. Vá à secção **de Certificado de Assinatura SAML.** Dependendo do tipo de aplicação, você verá opções para baixar o certificado em formato Base64, formato raw ou Federation Metadata XML. O Azure AD também fornece o Url de **Metadados da Federação de Aplicações** onde pode aceder aos metadados específicos da aplicação no formato `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`.

1. Para gerir, criar ou importar um certificado, selecione o ícone **Editar** (um lápis) no canto superior direito da secção certificado de **assinatura SAML** e, em seguida, faça qualquer um dos seguintes:

   - Para criar um novo certificado, selecione **Novo Certificado,** selecione a **Data de Validade**e, em seguida, selecione **Guardar**. Para ativar o certificado, selecione o menu de contexto **(...)** e selecione **Tornar o certificado ativo**.
   - Para fazer upload de um certificado com chave privada e credenciais pfx, selecione Certificado de **Importação** e navegue até ao certificado. Introduza a **palavra-passe PFX**e, em seguida, selecione **Adicionar**.  
   - Para configurar opções avançadas de assinatura de certificado, utilize as seguintes opções. Para descrições destas opções, consulte o artigo opções de assinatura de [certificadoavançado.](certificate-signing-options.md)
      - Na lista de recusa da **Opção de Assinatura,** escolha **a resposta Sign SAML,** **a afirmação do Sign SAML,** ou **a resposta e afirmação do Sign SAML**.
      - Na lista de lançamento do **Algoritmo de Assinatura,** escolha **SHA-1** ou **SHA-256**.
   - Para notificar pessoas adicionais quando o certificado ativo estiver próximo da data de validade, insira os endereços de e-mail nos campos de endereços de **e-mail da Notificação.**

1. Selecione **Guardar** na parte superior da secção certificado de **assinatura SAML.** 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>Passo 4. Configurar a aplicação para utilizar a Azure AD

A **Configuração \<aplicaçãoName>** secção lista os valores que precisam de ser configurados na aplicação para que utilize a Azure AD como fornecedor de identidade SAML. Os valores exigidos variam de acordo com a aplicação. Para mais detalhes, consulte a documentação SAML da aplicação.

1. Desloque-se até à **configuração \<aplicaçãoName>** secção. 
2. Copie o valor de cada linha desta secção, conforme necessário, siga as instruções específicas da aplicação para adicionar o valor à aplicação. Para aplicações de galeria, pode ver a documentação selecionando **as instruções passo a passo**. 
   - Os valores de **URL de Login** e **Logout** resolvem-se no mesmo ponto final, que é o ponto final de tratamento de pedidos SAML para a sua instância de AD Azure. 
   - O **Identificador AD Azure** é o valor do **Emitente** no token SAML emitido para o pedido.
1. Quando colar todos os valores nos campos apropriados, selecione **Save**.

## <a name="step-5-validate-single-sign-on"></a>Passo 5. Validar um único sinal

Está pronto para testar as definições para ver se um único sinal funciona para si, o administrador.  

1. Abra as definições do início de sessão único para a sua aplicação. 
2. Desloque-se para o único sinal de **validação com <applicationName>** secção. Para este tutorial, esta secção **chama-se Configurar o teste GitHub**.
3. Selecione **Teste**. As opções do teste aparecem.
4. Selecione **Iniciar sessão como utilizador atual**. 

Se o inessão for bem sucedido, está pronto para atribuir utilizadores e grupos à sua aplicação SAML.
Se aparecer uma mensagem de erro, complete os seguintes passos:

1. Copie e cole os detalhes na caixa **Qual é o aspeto do erro?** .

    ![Use a caixa "Como é o erro" para obter orientação de resolução](media/configure-single-sign-on-portal/error-guidance.png)

1. Selecione **Obter orientação**de resolução . A causa principal e a orientação da resolução aparecem.  Neste exemplo, o utilizador não foi atribuído à aplicação.
1. Leia a orientação da resolução e, se possível, corrija o problema.
1. Execute o teste novamente, até que seja concluído sem erros.

## <a name="next-steps"></a>Passos seguintes

- [Atribuir utilizadores ou grupos à aplicação](methods-for-assigning-users-and-groups.md)
- [Configurar o fornecimento automático de conta de utilizador](../app-provisioning/configure-automatic-user-provisioning-portal.md)
