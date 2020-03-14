---
title: SAML single sign-on - aplicações não-galeria - plataforma de identidade Microsoft  Microsoft Docs
description: Configure um único sign-on (SSO) para aplicações não-gallery na plataforma de identidade da Microsoft (Azure AD)
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
ms.openlocfilehash: ad27ad5e34d9f44fe7d7be80e05e33dd6fb5e7b1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79244215"
---
# <a name="configure-saml-based-single-sign-on-to-non-gallery-applications"></a>Configure a inscrição única baseada em SAML em aplicações não-galerias

Quando [adiciona uma aplicação](add-gallery-app.md) de galeria ou uma [aplicação web não-galeria](add-non-gallery-app.md) às suas Aplicações Empresariais AD Azure, uma das opções únicas de inscrição disponível para si é a [única inscrição baseada na SAML](what-is-single-sign-on.md#saml-sso). Escolha o SAML sempre que possível para aplicações que autenticam utilizando um dos protocolos SAML. Com o único sinal da SAML, a Azure AD autentica a aplicação utilizando a conta Azure AD do utilizador. O Azure AD comunica as informações de início de sessão para a aplicação através de um protocolo de ligação. Pode mapear os utilizadores para funções específicas de aplicação com base em regras que define nas suas reivindicações SAML. Este artigo descreve como configurar um único sign-on baseado em SAML para uma aplicação não-galeria. 

> [!NOTE]
> Adicionar uma aplicação de galeria? Encontre instruções passo a passo na [lista de tutoriais de aplicações SaaS](../saas-apps/tutorial-list.md)

Para configurar o único sign-on da SAML para uma aplicação não-galeria sem código de escrita, precisa de ter uma subscrição juntamente com uma licença Azure AD Premium e a aplicação deve suportar o SAML 2.0. Para mais informações sobre as versões Azure AD, visite [o preço da AD Azure.](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="before-you-begin"></a>Antes de começar

Se a aplicação não tiver sido adicionada ao seu inquilino DaD Azure, consulte [Adicionar uma aplicação não-galeria](add-non-gallery-app.md).

## <a name="step-1-edit-the-basic-saml-configuration"></a>Passo 1. Editar a configuração Básica sAML

1. Inscreva-se no [portal Azure](https://portal.azure.com) como administrador de aplicação em nuvem ou administrador de candidatura para o seu inquilino Azure AD.

2. Navegue para **as aplicações** de **Diretório Ativo** > Enterprise e selecione a aplicação da lista. 
   
   - Para procurar a aplicação, no menu **Tipo de Aplicação,** selecione **Todas as aplicações,** e depois selecione **Aplicar**. Introduza o nome da aplicação na caixa de pesquisa e, em seguida, selecione a aplicação a partir dos resultados.

3. Na secção **Gerir,** selecione **single sign-on**. 

4. Selecione **SAML**. Aparece a **configuração do único sign-on com a página SAML - Pré-visualização.**

   ![Passo 1 Editar a configuração Básica do SAML](media/configure-single-sign-on-non-gallery-applications/step-one-basic-saml-config.png)

5. Para editar as opções básicas de configuração do SAML, selecione o ícone **Editar** (um lápis) no canto superior direito da secção **de Configuração SAML Básica.**

1. Introduza as seguintes definições. Deve obter os valores do vendedor de aplicações. Pode introduzir manualmente os valores ou carregar um ficheiro de metadados para extrair o valor dos campos.

    | Definição básica de configuração SAML | Iniciado pelo SP | Iniciado pelo idP | Descrição |
    |:--|:--|:--|:--|
    | **Identificador (ID da entidade)** | Necessário para algumas aplicações | Necessário para algumas aplicações | Identifica exclusivamente a aplicação. A Azure AD envia o identificador para a aplicação como parâmetro do público do símbolo SAML. Espera-se que a aplicação o valide. Este valor também aparece como o ID da Entidade nos metadados SAML que a aplicação fornece. Introduza um URL que utilize o seguinte padrão: 'https://<subdomain>.contoso.com' *Pode encontrar este valor como elemento **emitente** no **Pedido AuthnRequest** (pedido SAML) enviado pelo pedido.* |
    | **URL de resposta** | Necessário | Necessário | Especifica onde é que a aplicação espera receber o token SAML. O URL de resposta também é denominado URL do Serviço de Consumidor de Asserções (ACS). Pode utilizar os campos de URL de resposta adicional para especificar URLs de resposta múltipla. Por exemplo, pode necessitar de URLs de resposta adicional para vários subdomínios. Ou, para efeitos de teste, pode especificar urLs de resposta múltipla (hospedeiro local e URLs públicos) de uma só vez. |
    | **URL de início de sessão** | Necessário | Não especifique | Quando um utilizador abre este URL, o fornecedor de serviços redireciona para o Azure AD para autenticar e iniciar a sessão do utilizador. A Azure AD utiliza o URL para iniciar a aplicação a partir do Office 365 ou do Painel de Acesso AD Azure. Quando em branco, a Azure AD executa o sinal iniciado pelo IdP quando um utilizador lança a aplicação a partir do Office 365, do Painel de Acesso AD Azure ou do URL Azure AD SSO.|
    | **Estado de Retransmissão** | Opcional | Opcional | Especifica à aplicação para onde deve redirecionar o utilizador após a conclusão da autenticação. Tipicamente, o valor é um URL válido para a aplicação. No entanto, algumas aplicações utilizam este campo de forma diferente. Para obter mais informações, contacte o fornecedor da aplicação.
    | **Logout URL** | Opcional | Opcional | Usado para enviar as respostas de logout SAML de volta para a aplicação.

Para mais informações, consulte [o protocolo SAML de inscrição única](../develop/single-sign-on-saml-protocol.md).

## <a name="step-2-configure-user-attributes-and-claims"></a>Passo 2. Configure os atributos e reclamações do Utilizador 

Quando um utilizador autentica a aplicação, a Azure AD emite na aplicação um token SAML com informações (ou reclamações) sobre o utilizador que os identifica exclusivamente. Por predefinição, estas informações incluem o nome de utilizador, endereço de e-mail, primeiro nome e apelido. Poderá ter de personalizar estas reclamações se, por exemplo, a aplicação necessitar de valores de reclamação específicos ou de um formato **nome** diferente do nome de utilizador. Os requisitos para aplicações de galerias são descritos nos [tutoriais específicos da aplicação,](../saas-apps/tutorial-list.md)ou pode perguntar ao fornecedor de aplicações. Os passos gerais para configurar os atributos e reclamações dos utilizadores são descritos abaixo.

1. Na secção **Atributos e Reivindicações** do Utilizador, selecione o ícone **Editar** (um lápis) no canto superior direito.

   ![Passo 2 Configurar atributos e reclamações do utilizador](media/configure-single-sign-on-non-gallery-applications/step-two-user-attributes-claims.png)

2. Verifique o valor do **identificador**de nome . O valor predefinido é *user.mainname*. O identificador de utilizador identifica exclusivamente cada utilizador da aplicação. Por exemplo, se o endereço de e-mail for o nome de utilizador e o identificador exclusivo, defina o valor como *user.mail*.

3. Para modificar o valor do **identificador**de nome, selecione o ícone **Editar** (um lápis) para o campo **Name Identifier Value.** Faça as alterações adequadas ao formato e fonte do identificador, conforme necessário. Para mais detalhes, consulte [Editar NameId](../develop/active-directory-saml-claims-customization.md#editing-nameid). Guarde as mudanças quando terminar. 
 
4. Para configurar as reivindicações de grupo, selecione o ícone **Editar** para os **Grupos devolvidos no campo de reclamação.** Para mais detalhes, consulte [as alegações do grupo Configure](../hybrid/how-to-connect-fed-group-claims.md).

5. Para adicionar uma reclamação, selecione **Adicionar nova reclamação** no topo da página. Introduza o **Nome** e selecione a fonte adequada. Se selecionar a fonte **do Atributo,** terá de escolher o **atributo Fonte** que pretende utilizar. Se selecionar a fonte de **Tradução,** terá de escolher a **Transformação** e **o Parâmetro 1** que pretende utilizar. Para mais detalhes, consulte [adicionar reclamações específicas da aplicação.](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims) Guarde as mudanças quando terminar. 

6. Selecione **Guardar**. A nova reivindicação aparece na mesa.

   > [!NOTE]
   > Para obter formas adicionais de personalizar o token SAML da Azure AD à sua aplicação, consulte os seguintes recursos.
   >- Para criar papéis personalizados através do portal Azure, consulte as alegações de [função Configure.](../develop/active-directory-enterprise-app-role-management.md)
   >- Para personalizar as reclamações via PowerShell, consulte [Personalizar reclamações - PowerShell](../develop/active-directory-claims-mapping.md).
   >- Para modificar o manifesto de aplicação para configurar reclamações opcionais para a sua aplicação, consulte [as reclamações opcionais do Configure](../develop/active-directory-optional-claims.md).
   >- Para definir políticas de vida simbólicas para tokens de atualização, fichas de acesso, fichas de sessão e fichas de identificação, consulte a configuração de [token lifetimes](../develop/active-directory-configurable-token-lifetimes.md). Ou, para restringir as sessões de autenticação via Acesso Condicional Azure AD, consulte as capacidades de gestão da sessão de [autenticação.](https://go.microsoft.com/fwlink/?linkid=2083106)

## <a name="step-3-manage-the-saml-signing-certificate"></a>Passo 3. Gerir o certificado de assinatura SAML

A Azure AD utiliza um certificado para assinar as fichas SAML que envia para o pedido. Precisa deste certificado para configurar a confiança entre a Azure AD e a aplicação. Para obter mais informações sobre o formato do certificado, consulte a documentação SAML da aplicação. Para obter mais informações, consulte Gerir certificados para opções [de assinatura de certificados individuais federados](manage-certificates-for-federated-single-sign-on.md) e [de certificado avançado no token SAML](certificate-signing-options.md).

A partir do Azure AD, pode descarregar o certificado ativo em formato Base64 ou Raw diretamente a partir do conjunto principal De Acesso Único com página **SAML.** Em alternativa, pode obter o certificado ativo descarregando o ficheiro XML dos metadados da aplicação ou utilizando o URL de metadados da Federação da App. Para visualizar, criar ou descarregar os seus certificados (ativos ou inativos), siga estes passos.

1. Vá à secção **de Certificado de Assinatura SAML.** 

   ![Passo 3 Gerir o certificado de assinatura SAML](./media/configure-single-sign-on-non-gallery-applications/step-three-certificate.png)

2. Verifique se o certificado tem:

   - *A data de validade desejada.* Pode configurar a data de validade até três anos no futuro.
   - *Um estado ativo para o certificado pretendido.* Se o estado estiver **inativo,** altere o estado para **Ativo**. Para alterar o estado, clique à direita na linha do certificado pretendido e selecione **Tornar o certificado ativo**.
   - *A opção de assinatura correta e algoritmo.*
   - *O endereço de e-mail de notificação correto(es).* Quando o certificado ativo se encontra próximo da data de validade, a Azure AD envia uma notificação para o endereço de e-mail configurado neste campo.

2. Para descarregar o certificado, selecione uma das opções para o formato Base64, formato Raw ou Federação de Metadados XML. O Azure AD também fornece o Url de **Metadados da Federação de Aplicações** onde pode aceder aos metadados específicos da aplicação no formato `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`.

3. Para gerir, criar ou importar um certificado, selecione o ícone **Editar** (um lápis) no canto superior direito da secção certificado de **assinatura SAML.**

   ![Certificado de assinatura SAML](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)


   Tome qualquer uma das seguintes ações:

   - Para criar um novo certificado, selecione **Novo Certificado,** selecione a **Data de Validade**e, em seguida, selecione **Guardar**. Para ativar o certificado, selecione o menu de contexto **(...)** e selecione **Tornar o certificado ativo**.
   - Para fazer upload de um certificado com chave privada e credenciais pfx, selecione Certificado de **Importação** e navegue até ao certificado. Introduza a **palavra-passe PFX**e, em seguida, selecione **Adicionar**.  
   - Para configurar opções avançadas de assinatura de certificado, utilize as seguintes opções. Para descrições destas opções, consulte o artigo opções de assinatura de [certificadoavançado.](certificate-signing-options.md)
      - Na lista de recusa da **Opção de Assinatura,** escolha **a resposta Sign SAML,** **a afirmação do Sign SAML,** ou **a resposta e afirmação do Sign SAML**.
      - Na lista de lançamento do **Algoritmo de Assinatura,** escolha **SHA-1** ou **SHA-256**.
   - Para notificar pessoas adicionais quando o certificado ativo estiver próximo da data de validade, insira os endereços de e-mail nos campos de endereços de **e-mail da Notificação.**

4. Se tiver feito alterações, selecione **Guardar** na parte superior da secção certificado de **assinatura SAML.** 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>Passo 4. Configurar a aplicação para utilizar a Azure AD

A **Configuração \<aplicaçãoName>** secção lista os valores que precisam de ser configurados na aplicação para que utilize a Azure AD como fornecedor de identidade SAML. Os valores exigidos variam de acordo com a aplicação. Para mais detalhes, consulte a documentação SAML da aplicação. Para encontrar a documentação, vá ao **Nome de aplicação \<>** título e selecione **Ver instruções passo a passo**. A documentação aparece na página de inscrição do **Configure.** Esta página guia-o no preenchimento do URL de **Login,** **do Identificador AD Azure**e dos valores de URL de **Logout** no Configurar \<nome de **aplicação>** título.

1. Desloque-se até à **configuração \<aplicaçãoName>** secção. 
   
   ![Passo 4 Configurar a aplicação](media/configure-single-sign-on-non-gallery-applications/step-four-app-config.png)

1. Copie o valor de cada linha desta secção, conforme necessário, siga as instruções específicas da aplicação para adicionar o valor à aplicação. Para aplicações de galeria, pode ver a documentação selecionando **as instruções passo a passo**. 
   - Os valores de **URL de Login** e **Logout** resolvem-se no mesmo ponto final, que é o ponto final de tratamento de pedidos SAML para a sua instância de AD Azure. 
   - O **Identificador AD Azure** é o valor do **Emitente** no token SAML emitido para o pedido.
2. Quando colar todos os valores nos campos apropriados, selecione **Save**.

## <a name="step-5-validate-single-sign-on"></a>Passo 5. Validar um único sinal

Depois de configurar a sua aplicação para utilizar o Azure AD como fornecedor de identidade baseado em SAML, pode testar as definições para ver se o único sinal funciona para a sua conta. 

2. Desloque-se para o único sinal de **validação com <applicationName>** secção.

   ![Passo 5 Validar um único sinal](media/configure-single-sign-on-non-gallery-applications/step-five-validate.png)

3. Selecione **Validar**. As opções do teste aparecem.

4. Selecione **Iniciar sessão como utilizador atual**. 

Se o inessão for bem sucedido, está pronto para atribuir utilizadores e grupos à sua aplicação SAML.
Se aparecer uma mensagem de erro, complete os seguintes passos:

1. Copie e cole os detalhes na caixa **Qual é o aspeto do erro?** .

    ![Obter orientações de resolução](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Selecione **Obter orientação**de resolução . A causa principal e a orientação da resolução aparecem.  Neste exemplo, o utilizador não foi atribuído à aplicação.

3. Leia a orientação da resolução e, se possível, corrija o problema.

4. Execute o teste novamente, até que seja concluído sem erros.

Para mais informações, consulte o [único sign-on baseado em Debug SAML para aplicações no Diretório Ativo Azure](../azuread-dev/howto-v1-debug-saml-sso-issues.md).

## <a name="next-steps"></a>Passos seguintes

- [Atribuir utilizadores ou grupos à aplicação](methods-for-assigning-users-and-groups.md)
- [Configurar o fornecimento automático de conta de utilizador](../app-provisioning/configure-automatic-user-provisioning-portal.md)
