---
title: 'Tutorial: Integração do Active Directory do Azure com o SAP NetWeaver | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o SAP NetWeaver e o Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5a1034a55bc4d516244e11d035f17fcc3b94929
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57855644"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-netweaver"></a>Tutorial: Integração do Active Directory do Azure com o SAP NetWeaver

Neste tutorial, saiba como integrar o SAP NetWeaver no Azure Active Directory (Azure AD).
Integrar o SAP NetWeaver no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao SAP NetWeaver.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o SAP NetWeaver (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o SAP NetWeaver, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* SAP NetWeaver logon único habilitado subscrição
* SAP NetWeaver V7.20 necessária, pelo menos

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta o SAP NetWeaver **SP** iniciada SSO

## <a name="adding-sap-netweaver-from-the-gallery"></a>Adicionando o SAP NetWeaver da Galeria

Para configurar a integração do SAP NetWeaver no Azure AD, terá de adicionar o SAP NetWeaver partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o SAP NetWeaver partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **SAP NetWeaver**, selecione **SAP NetWeaver** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![SAP NetWeaver na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o SAP NetWeaver, com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado em SAP NetWeaver deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o SAP NetWeaver, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o SAP NetWeaver início de sessão único](#configure-sap-netweaver-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de SAP NetWeaver](#create-sap-netweaver-test-user)**  - para ter um equivalente da Eduarda Almeida no SAP NetWeaver, que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o SAP NetWeaver, execute os seguintes passos:

1. Abra uma nova janela do browser web e de registo no site da sua empresa SAP NetWeaver, como administrador

2. Certifique-se de que **http** e **https** serviços estão ativos e portas apropriadas são atribuídas no **SMICM** código T.

3. Inicie sessão para empresas do cliente do sistema de SAP (T01), onde é necessário o SSO e ativar a gestão de sessão de segurança de HTTP.

    a. Aceda ao código da transação **SICF_SESSIONS**. Apresenta todos os parâmetros de perfil relevantes com os valores atuais. Eles ser semelhante ao seguinte:-
    ```
    login/create_sso2_ticket = 2
    login/accept_sso2_ticket = 1
    login/ticketcache_entries_max = 1000
    login/ticketcache_off = 0  login/ticket_only_by_https = 0 
    icf/set_HTTPonly_flag_on_cookies = 3
    icf/user_recheck = 0  http/security_session_timeout = 1800
    http/security_context_cache_size = 2500
    rdisp/plugin_auto_logout = 1800
    rdisp/autothtime = 60
    ```
    >[!NOTE]
    > Ajustar acima parâmetros de acordo com os requisitos da organização, acima de parâmetros são indicados aqui como indicação apenas.

    b. Se for necessário ajustar os parâmetros, no perfil de instância/predefinido do sistema SAP e reinicie o sistema SAP.

    c. Faça duplo clique num cliente relevante para ativar a sessão de segurança HTTP.

    ![O link de download de certificado](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. Ative abaixo serviços SICF:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
4. Aceda ao código da transação **SAML2** em empresas do cliente do sistema SAP [T01/122]. Isso abrirá uma interface do usuário num navegador. Neste exemplo, assumimos que 122 como cliente de negócios SAP.

    ![O link de download de certificado](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

5. Forneça o seu nome de utilizador e palavra-passe para o introduza na interface do usuário e clique em **editar**.

    ![O link de download de certificado](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

6. Substitua **nome do fornecedor** partir T01122 para `http://T01122` e clique em **guardar**.

    > [!NOTE]
    > Pelo nome do fornecedor predefinida ser <sid> <client> formato, do Azure AD, mas espera o nome no formato <protocol>://<name>, recomendando para manter o nome do fornecedor como https://<sid> <client> para permitir que vários SAP Mecanismos de NetWeaver ABAP configurar no Azure AD.

    ![O link de download de certificado](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

7. **Geração de metadados do fornecedor de serviço**: – assim que fizemos com a configuração a **fornecedor Local** e **fornecedores fidedignos** definições na Interface do usuário de SAML 2.0, a próxima etapa seria Gere ficheiro de metadados do fornecedor de serviços (que iria conter todas as definições, os contextos de autenticação e outras configurações no SAP). Assim que esse arquivo é gerado, precisamos carregá-lo no Azure AD.

    ![O link de download de certificado](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Aceda a **separador de fornecedor Local**.

    b. Clique em **metadados**.

    c. Guardar o gerado **arquivo XML de metadados** no seu computador e carregá-la na **configuração básica de SAML** secção para preencher o automática a **identificador** e  **URL de resposta** valores no portal do Azure.

8. Na [portal do Azure](https://portal.azure.com/), na **SAP NetWeaver** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

9. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

10. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

11. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    a. Clique em **carregamento de ficheiro de metadados** para carregar o **ficheiro de metadados do fornecedor de serviços** que obteve anteriormente.

    ![Carregar ficheiro de metadados](common/upload-metadata.png)

    b. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

    ![Escolha o ficheiro de metadados](common/browse-upload-metadata.png)

    c. Depois do ficheiro de metadados é carregado com êxito, o **identificador** e **URL de resposta** valores ficam automaticamente povoada na **configuração básica de SAML** secção caixa de texto, como mostra a abaixo:

    ![SAP NetWeaver domínio e URLs únicas início de sessão em informações](common/sp-identifier-reply.png)

    d. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<your company instance of SAP NetWeaver>`

    > [!NOTE]
    > Vimos alguns clientes reportar um erro do URL de resposta incorreto configurado às respetivas instâncias. Se receber qualquer erro desse tipo, pode utilizar após o script do PowerShell como um trabalho em torno para definir o URL de resposta correta para a sua instância.:
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > ID de objeto ServicePrincipal está seja definido por mesmo pela primeira vez ou pode passá-lo também aqui.

12. SAP NetWeaver aplicativo espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

13. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, configurar o atributo de token de SAML conforme mostrado na imagem acima e execute os seguintes passos:

    a. Clique em **ícone de edição** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![image](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. Partir do **transformação** lista, selecione **ExtractMailPrefix()**.

    c. Partir do **parâmetro 1** lista, selecione **user.userprinicipalname**.

    d. Clique em **Guardar**.

14. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

15. Sobre o **configurar o SAP NetWeaver** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-sap-netweaver-single-sign-on"></a>Configurar o SAP NetWeaver Single Sign-On

1. Para o sistema SAP de início de sessão e vá para o código de transação SAML2. Nova janela do browser é aberto o ecrã de configuração de SAML.

2. Para configurar pontos finais de identidade fidedignos fornecedor (Azure AD), aceda a **fornecedores fidedignos** separador.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. Prima **Add** e selecione **carregar o ficheiro de metadados** no menu de contexto.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. Carregar ficheiro de metadados, que transferiu do portal do Azure.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. No ecrã seguinte, escreva o nome de Alias. Por exemplo, aadsts e prima **seguinte** para continuar.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. Certifique-se de que sua **algoritmo de texto implícita** deve ser **SHA-256** e não necessitam de quaisquer alterações e pressione **seguinte**.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. No **pontos finais de início de sessão único**, utilize **HTTP POST** e clique em **seguinte** para continuar.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. No **pontos de extremidade de fim de sessão único** selecionar **HTTPRedirect** e clique em **seguinte** para continuar.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. No **pontos de extremidade do artefacto**, prima **próxima** para continuar.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. No **requisitos de autenticação**, clique em **concluir**.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. Aceda ao separador **Trusted Provider** > **Federação de identidades** (da parte inferior do ecrã). Clique em **Editar**.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. Clique em **Add** sob a **Federação de identidades** separador (janela na parte inferior).

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. Na janela de pop-up, selecione **não especificado** da **formatos suportados NameID** e clique em OK.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

14. Tenha em atenção que **ID de origem do utilizador** e **modo de mapeamento de id de utilizador** valores determinam a ligação entre o utilizador SAP e a afirmação do Azure AD.  

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Cenário: Utilizador SAP para o mapeamento de utilizador do Azure AD.

    a. NameID a captura de ecrã de detalhes do SAP.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Captura de ecrã mencionar as declarações necessárias do Azure AD.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Cenário: Selecione o id de utilizador SAP com base no endereço de correio eletrónico configurado no SU01. Neste caso o id de e-mail deve ser configurada no su01 para cada usuário que requer o SSO.

    a.  NameID a captura de ecrã de detalhes do SAP.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. Captura de ecrã mencionar as declarações necessárias do Azure AD.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/claimsaad2.png)

15. Clique em **salvar** e, em seguida, clique em **ativar** para ativar o fornecedor de identidade.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/configuration1.png)

16. Clique em **OK** quando lhe for pedido.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/configuration2.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para SAP NetWeaver.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **SAP NetWeaver**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **SAP NetWeaver**.

    ![A ligação do SAP NetWeaver na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-sap-netweaver-test-user"></a>Criar utilizador de teste de SAP NetWeaver

Nesta secção, vai criar um usuário chamado Eduarda Almeida no SAP NetWeaver. Inicie funcionam à sua equipa de especialistas de SAP internamente ou trabalhar com o seu parceiro SAP da organização para adicionar os utilizadores na plataforma do SAP NetWeaver.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

1. Assim que o fornecedor de identidade do Azure AD foi ativado, tente aceder ao URL para verificar o SSO abaixo (não haverá nenhuma linha de comandos para o nome de utilizador e palavra-passe)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (ou) a utilizar o URL abaixo

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Substitua sapurl com o nome do anfitrião real SAP.

2. O URL acima deverá demorar para abaixo ecrã mencionado. Se for capaz de alcançar até a página, a configuração do SSO do Azure AD é efetuada com êxito.

    ![Configurar o início de sessão único](./media/sapnetweaver-tutorial/testingsso.png)

3. Se ocorrer a linha de comandos de nome de utilizador e palavra-passe, tente diagnosticar o problema por ativar o rastreio com o URL abaixo

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
