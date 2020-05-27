---
title: 'Tutorial: Azure Ative Directory integração individual (SSO) com software Maxient Conduct Manager [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Maxient Conduct Manager Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85e71b76-cac3-4ce6-a35f-796d2cb7bdb5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec59aa830fe314332d17091f17ef81d4a1d65470
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83833409"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maxient-conduct-manager-software"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com software maxient Conduct Manager

Neste tutorial, você vai aprender a integrar o Software Maxient Conduct Manager com o Azure Ative Directory (Azure AD). Quando integrar o Software Maxient Conduct Manager com a Azure AD, pode:

* Utilize a AD Azure para autenticar os seus utilizadores para o Software Maxient Conduct Manager
* Ative que os seus utilizadores sejam automaticamente inscritos no Software Maxient Conduct Manager com as suas contas Azure AD.


Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Maxient Conduct Manager Software uma subscrição única ativada por si.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, irá configurar o seu Anúncio Azure para utilização com o Software Maxient Conduct Manager.


* Maxient Conduct Manager Software suporta **SP e IDP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.

## <a name="adding-maxient-conduct-manager-software-from-the-gallery"></a>Adicionar software maxient conduct manager da galeria

Para configurar a integração do Software Maxient Conduct Manager em Azure AD, precisa adicionar software maxient Conduct Manager da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **de galeria,** digite o **Software Maxient Conduct Manager** na caixa de pesquisa.
1. **Selecione Software Maxient Conduct Manager** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-maxient-conduct-manager-software"></a>Configure e teste Azure AD single sign-on for Maxient Conduct Manager Software

Configure e teste Azure AD SSO com software maxient Conduct Manager. Para que o SSO funcione, é necessário estabelecer uma ligação entre o Azure AD e o Software Maxient Conduct Manager.

Para configurar e testar o Azure AD SSO com o Software Maxient Conduct Manager, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores autentiquem para utilização com o Software Maxient Conduct Manager
    1. **[Atribua a todos os utilizadores a utilização da Maxient](#assign-all-users-to-be-able-to-authenticate-for-the-Maxient-Conduct-Manager-Software)** - para permitir que todos na sua instituição possam autenticar.
1. **[TestAzure AD Setup Com Maxient](#test-with-maxient)** - para verificar se a configuração funciona, e os atributos corretos estão sendo lançados

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações do **Maxient Conduct Manager Software,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica saml,**   a aplicação está pré-configurada no modo iniciado **idp**   e os URLs necessários já estão pré-povoados com o Azure. O utilizador precisa de guardar a **Save**configuração clicando no   botão Guardar.

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://cm.maxient.com/<SCHOOLCODE>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de sign-on real. Trabalhe com o seu representante maxient implementação/suporte para obter o valor.

1. No **set single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.  Terá de fornecer ao seu representante maxient implementação/suporte este URL.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

### <a name="assign-all-users-to-be-able-to-authenticate-for-the-maxient-conduct-manager-software"></a>Atribuir a todos os utilizadores para serem capazes de autenticar para o Software Maxient Conduct Manager

Nesta secção, você concederá acesso para todas as contas para autenticar usando o sistema Azure para o Software Maxient Conduct Manager.  É importante notar que este passo é **necessário** para que maxient funcione corretamente.  A Maxient aproveita o seu sistema Azure AD para *autenticar* os utilizadores. A *autorização* dos utilizadores é realizada dentro do sistema Maxient para a função particular que estão a tentar desempenhar. A Maxient não usa atributos do seu diretório para tomar essas decisões.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Maxient Conduct Manager Software**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione todos os utilizadores (ou os grupos apropriados) e **atribua-os** para poderem autenticar com a Maxient.

## <a name="test-with-maxient"></a>Teste com Maxient 

Se um bilhete de apoio ainda não tiver sido aberto com um representante de Implementação/Suporte Maxient, envie um e-mail [support@maxient.com](mailto:support@maxient.com) com o tema "Aautenticação Baseada no Campus/Configuração Azure - \< \< Nome \> \> escolar". No corpo do e-mail, forneça o Url de **Metadados da Federação de Aplicações**. O pessoal da Maxient responderá com uma ligação de teste para verificar se os atributos adequados estão a ser libertados.  
    
## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente software maxient Conduct Manager com Azure AD](https://aad.portal.azure.com/)

