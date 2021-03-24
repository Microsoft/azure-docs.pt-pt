---
title: 'Tutorial: Integração única de sign-on (SSO) do Azure Ative Directory com integração AD Kemp LoadMaster Azure | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a integração AD Kemp LoadMaster Azure.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: 91f6db79b7d18dc8b34ba1712d74a92000d63528
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953685"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-kemp-loadmaster-azure-ad-integration"></a>Tutorial: Integração SSO do Azure Ative Directory com integração de AD Kemp LoadMaster Azure

Neste tutorial, você vai aprender a integrar a integração de Kemp LoadMaster Azure AD com Azure Ative Directory (Azure AD). Quando integrar a integração do Kemp LoadMaster AD com a AD Azure, pode:

* Control em Azure AD que tem acesso à integração AD Kemp LoadMaster Azure.
* Capacitar os seus utilizadores a serem automaticamente inscritos na integração do AD Kemp LoadMaster Azure com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A integração única ativada pelo Kemp LoadMaster AD (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Integração AD kemp LoadMaster Azure suporta **IDP** iniciado SSO

## <a name="add-kemp-loadmaster-azure-ad-integration-from-the-gallery"></a>Adicione a integração de AD Kemp LoadMaster Azure da galeria

Para configurar a integração da integração do Kemp LoadMaster Azure AD no AZure AD, é necessário adicionar a integração AD do Kemp LoadMaster Azure da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **de galeria,** **digite a integração AD do Kemp LoadMaster** na caixa de pesquisa.
1. Selecione **a integração AD do Kemp LoadMaster Azure** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-kemp-loadmaster-azure-ad-integration"></a>Configure e teste Azure AD SSO para integração de AD Kemp LoadMaster Azure

Configure e teste Azure AD SSO com a integração AD Kemp LoadMaster Azure usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na integração AD kemp LoadMaster Azure.

Para configurar e testar o Azure AD SSO com a integração AD do Kemp LoadMaster Azure, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Kemp LoadMaster Azure AD integração SSO](#configure-kemp-loadmaster-azure-ad-integration-sso)** - para configurar as definições de inscrição única no lado da aplicação.

1. **[Servidor Web de Publicação](#publishing-web-server)**
    1. **[Criar um serviço virtual](#create-a-virtual-service)**
    1. **[Certificados e Segurança](#certificates-and-security)**
    1. **[Perfil SAML de integração AD Kemp LoadMaster AD](#kemp-loadmaster-azure-ad-integration-saml-profile)**
    1. **[Verificar as alterações](#verify-the-changes)**
1. **[Configuração da autenticação baseada em Kerberos](#configuring-kerberos-based-authentication)**
    1. **[Criar uma conta de delegação Kerberos para a integração do Kemp LoadMaster Azure AD](#create-a-kerberos-delegation-account-for-kemp-loadmaster-azure-ad-integration)**
    1. **[KCD de integração AD Kemp LoadMaster (Contas da Delegação Kerberos)](#kemp-loadmaster-azure-ad-integration-kcd-kerberos-delegation-accounts)**
    1. **[Kemp LoadMaster Azure AD integração ESP](#kemp-loadmaster-azure-ad-integration-esp)**
    1. **[Create Kemp LoadMaster Azure AD test user](#create-kemp-loadmaster-azure-ad-integration-test-user)** - para ter uma contraparte de B.Simon em integração AD Kemp LoadMaster Azure que está ligada à representação AD Ad Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **de integração AZure Ad do Kemp LoadMaster Azure,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier (Entity ID),** digite um URL: `https://<KEMP-CUSTOMER-DOMAIN>.com/`

    b. Na caixa de texto **URL de resposta,** digite um URL: `https://<KEMP-CUSTOMER-DOMAIN>.com/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte a equipa de suporte ao cliente de [integração da Kemp LoadMaster Azure](mailto:support@kemp.ax) para obter estes valores. Também pode consultar os padrões indicados na secção de Configuração BÁSICA SAML no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e **o Metadados da Federação XML,** selecione **Descarregue** para descarregar os ficheiros XML do certificado e da federação e guarde-o no seu computador.

    ![O link de descarregamento de certificado](./media/kemp-tutorial/certificate-base-64.png)

1. Na secção **de integração AD Do Kemp LoadMaster Azure,** copie os URL(s) apropriados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à integração AD Kemp LoadMaster Azure.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Kemp LoadMaster Azure AD integração**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-kemp-loadmaster-azure-ad-integration-sso"></a>Configure Kemp LoadMaster Azure AD integração SSO

## <a name="publishing-web-server"></a>Servidor Web de Publicação 

### <a name="create-a-virtual-service"></a>Criar um serviço virtual 

1. Vá a Kemp LoadMaster Ad integração AD LoadMaster Web UI > Serviços Virtuais > Add New.

1. Clique em Adicionar Novo.

1. Especificar os Parâmetros para o Serviço Virtual.

    ![Screenshot que mostra a página "Por favor especifique os parâmetros para o serviço virtual" com valores de exemplo nas caixas.](./media/kemp-tutorial/kemp-1.png)

    a. Endereço Virtual
    
    b. Porta
    
    c. Nome de serviço (Opcional)
    
    d. Protocolo 

1. Navegue para a secção Servidores Reais.

1. Clique em Adicionar Novo.

1. Especifique os parâmetros para o Servidor Real.
    
    ![Screenshot que mostra a página "Por favor especifique os parâmetros para o servidor real" com valores de exemplo nas caixas.](./media/kemp-tutorial/kemp-2.png)

    a. Selecione Permitir endereços remotos
    
    b. Digite no endereço do servidor real
    
    c. Porta
    
    d. Método de encaminhamento
    
    e. Peso
    
    f. Limite de ligação
    
    exemplo, Clique em Adicionar Este Servidor Real

## <a name="certificates-and-security"></a>Certificados e Segurança
 
### <a name="import-certificate-on-kemp-loadmaster-azure-ad-integration"></a>Certificado de importação na integração do AD Kemp LoadMaster Azure 
 
1. Aceda ao Portal web de integração Azure Ad Kemp LoadMaster > Certificados & Segurança > Certificados SSL.

1. Sob Gestão de Certificados > Configuração de Certificado.

1. Clique em Certificado de Importação.

1. Especifique o nome do ficheiro que contém o certificado. O ficheiro também pode conter a chave privada. Se o ficheiro não contiver a chave privada, o ficheiro que contém a chave privada também deve ser especificado. O certificado pode estar em qualquer um. PEM ou . Formato PFX (IIS).

1. Clique em Escolher o ficheiro no Arquivo de Certificados.

1. Clique no Ficheiro chave (opcional).

1. Clique em Guardar.

### <a name="ssl-acceleration"></a>Aceleração SSL
 
1. Vá a Kemp LoadMaster Web UI > Serviços Virtuais > Ver/Modificar Serviços.

1. Clique em Modificar em Funcionamento.

1. Clique em SSL Properties (que opera na Camada 7).
    
    ![Screenshot que mostra a secção "S S L Properties" com selecionado "S S L Acceleration - Enabled" e um certificado de exemplo selecionado.](./media/kemp-tutorial/kemp-3.png)
    
    a. Clique em Ativado na Aceleração SSL.
    
    b. Nos Certificados Disponíveis, selecione o certificado importado e clique no `>` símbolo.
    
    c. Assim que o certificado SSL pretendido aparecer nos Certificados Atribuídos, clique em **Certificados Definidos**.

    > [!NOTE]
    > Certifique-se de clicar nos **Certificados Conjuntos.**

## <a name="kemp-loadmaster-azure-ad-integration-saml-profile"></a>Perfil SAML de integração AD Kemp LoadMaster AD
 
### <a name="import-idp-certificate"></a>Certificado IdP de importação

Vá a Kemp LoadMaster Azure AD integração Web Console 

1. Clique em Certificados Intermédios em Certificados e Autoridade.

    ![Screenshot que mostra a secção "Certificados Intermédios atualmente instalados" com um certificado de exemplo selecionado.](./media/kemp-tutorial/kemp-6.png)

    a. Clique em escolher o ficheiro em Adicionar um novo Certificado Intermédio.
    
    b. Navegue para o ficheiro de certificados previamente descarregado a partir da Aplicação Empresarial Azure AD.
    
    c. Clique em Open.
    
    d. Fornecer um Nome em Nome do Certificado.
    
    e. Clique no Certificado de Adicionar.

### <a name="create-authentication-policy"></a>Criar Política de Autenticação 
 
Vá a Manage SSO em Serviços Virtuais.

   ![Screenshot que mostra a página "Gerir S O".](./media/kemp-tutorial/kemp-7.png)
   
   a. Clique em Adicionar em Adicionar nova configuração do lado do cliente depois de lhe dar um nome.

   b. Selecione SAML ao abrigo do Protocolo de Autenticação.

   c. Selecione o Ficheiro De Metadas em IdP Provisioning. 

   d. Clique no Ficheiro Escolha.

   e. Navegue para XML previamente descarregado do portal Azure.

   f. Clique em Abrir e clique no ficheiro Metada de IdP de importação.

   exemplo, Selecione o certificado intermédio do Certificado IdP.

   h. Desconfiem do ID da Entidade SP que deve corresponder à identidade criada no portal Azure. 

   i. Clique no Conjunto de Identificação da Entidade SP.

### <a name="set-authentication"></a>Autenticação definida  
 
Na consola web de integração azure Ad do Kemp LoadMaster Ad

1. Clique em Serviços Virtuais.

1. Clique em Ver/Modificar Serviços.

1. Clique em Modificar e navegar para opções ESP.
    
    ![Screenshot que mostra a página "Ver/Modificar Serviços", com as secções "OPÇÕES ESP" e "Servidores Reais" expandidas.](./media/kemp-tutorial/kemp-8.png)

    a. Clique em Enable ESP.
    
    b. Selecione SAML no Modo de Autenticação do Cliente.
    
    c. Selecione autenticação do lado do cliente previamente criada no domínio SSO.
    
    d. Digite o nome do anfitrião em Hostes Virtuais Permitidos e clique em Conjunto de Anfitriões Virtuais Permitidos.
    
    e. Digite /* em Diretórios Virtuais Permitidos (com base nos requisitos de acesso) e clique em Definir Diretórios Permitidos.

### <a name="verify-the-changes"></a>Verificar as alterações 
 
Navegue pelo URL da aplicação 

Você deve ver a sua página de login insediada em vez de acesso não autenticado anteriormente. 

![Screenshot que mostra a página "Iniciar sedem".](./media/kemp-tutorial/kemp-9.png)

## <a name="configuring-kerberos-based-authentication"></a>Configuração da autenticação baseada em Kerberos 
 
### <a name="create-a-kerberos-delegation-account-for-kemp-loadmaster-azure-ad-integration"></a>Criar uma conta de delegação Kerberos para a integração do Kemp LoadMaster Azure AD 

1. Criar uma Conta de utilizador (neste exemplo AppDelegation).
    
    ![Screenshot que mostra a janela "KCD user Properties" com o separador "Conta" selecionado.](./media/kemp-tutorial/kemp-10.png)


    a. Selecione o separador Attribute Editor (Editor de Atributos).

    b. Navegue para o serviçoPrincipalName. 

    c. Selecione o serviçoPrincipalName e clique em Editar.

    d. Digite http/kcduser no Valor para adicionar campo e clique em Adicionar. 

    e. Clique em Aplicar e OK. A janela deve fechar-se antes de a abrir novamente (para ver o novo separador delegação). 

1. Abra novamente a janela das propriedades do utilizador e o separador Delegação fica disponível. 

1. Selecione o separador Delegação.

    ![Screenshot que mostra a janela "KCD user Properties" com o separador "Delegação" selecionado.](./media/kemp-tutorial/kemp-11.png)

    a. Selecione Confiar neste utilizador para delegação apenas para serviços especificados.

    b. Selecione Utilizar qualquer protocolo de autenticação.

    c. Adicione os Servidores Reais e adicione http como o serviço. 
    
    d. Selecione a caixa de verificação expandida. 

    e. Pode ver todos os servidores com o nome de anfitrião e o FQDN.
    
    f. Clique em OK.

> [!NOTE]
> Desafine o SPN no Application/Website conforme aplicável. Para aceder à aplicação quando a identidade do pool de aplicação tiver sido definida. Para aceder à aplicação IIS utilizando o nome FQDN, aceda ao pedido de comando do Servidor Real e escreva SetSpn com os parâmetros necessários. Por exemplo, Setspn –S HTTP/sescoindc.sunehes.co.in suneshes\kdcuser 

### <a name="kemp-loadmaster-azure-ad-integration-kcd-kerberos-delegation-accounts"></a>KCD de integração AD Kemp LoadMaster (Contas da Delegação Kerberos) 

Vá a Kemp LoadMaster Azure Ad integração Web Consola > Serviços Virtuais > Gerir SSO.

![Screenshot que mostra a página "Gerir S O - Gerir o Domínio".](./media/kemp-tutorial/kemp-12.png)

a. Navegue para o sinal único do lado do servidor nas configurações.

b. Digite nome em Adicionar nova configuração Server-Side e clique em Adicionar.

c. Selecione Kerberos Delegação Restrita no Protocolo de Autenticação.

d. Tipo nome de domínio em Kerberos Realm.

e. Clique no reino de set Kerberos.

f. Endereço IP do controlador de domínio do tipo no Centro de Distribuição de Chaves Kerberos.

exemplo, Clique em set Kerberos KDC.

h. Digite o nome de utilizador KCD em Kerberos Trusted User Name.

i. Clique no nome de utilizador fidedigno do KDC.

j. Digite palavra-passe em Kerberos Palavra-passe de utilizador fidedigno.

k. Clique na palavra-passe do utilizador fidedigno do KCD.

### <a name="kemp-loadmaster-azure-ad-integration-esp"></a>Kemp LoadMaster Azure AD integração ESP 

Aceda aos Serviços > Serviços de Visualização/Modificação de Serviços virtuais.

![Kemp LoadMaster Azure AD integração webserver](./media/kemp-tutorial/kemp-13.png)

a. Clique em Modificar o Nome Nick do Serviço Virtual.
    
b. Clique em Opções ESP.
    
c. No modo de autenticação do servidor, selecione KCD.
        
d. Na configuração Server-Side, selecione o perfil do lado do servidor previamente criado.

### <a name="create-kemp-loadmaster-azure-ad-integration-test-user"></a>Criar utilizador de teste de integração AD Kemp LoadMaster Azure

Nesta secção, cria-se um utilizador chamado B.Simon na integração AD Kemp LoadMaster Azure. Trabalhar com [a equipa de suporte de integração azure Ad Kemp LoadMaster](mailto:support@kemp.ax) para adicionar os utilizadores na plataforma de integração AD Kemp LoadMaster Azure. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito na integração AD kemp LoadMaster Azure para a qual configura o SSO.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo de integração AD Kemp LoadMaster Azure nas Minhas Apps, deverá ser automaticamente inscrito na integração AD Kemp LoadMaster Azure para a qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurada a integração de AD Kemp LoadMaster Azure, pode impor o controlo de sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).