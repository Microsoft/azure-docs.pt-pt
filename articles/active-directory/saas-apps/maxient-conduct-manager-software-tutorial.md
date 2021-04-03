---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Maxient Conduct Manager Software | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Maxient Conduct Manager Software.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2019
ms.author: jeedes
ms.openlocfilehash: 4b57b3fbb338774eb8d66fc4d3c0c817e19aff4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458205"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maxient-conduct-manager-software"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Maxient Conduct Manager Software

Neste tutorial, você vai aprender a integrar o Maxient Conduct Manager Software com O Azure Ative Directory (Azure AD). Quando integrar o Software Maxient Conduct Manager com Azure AD, pode:

* Utilize o Azure AD para autenticar os seus utilizadores para o Software Maxient Conduct Manager
* Capacitar os seus utilizadores a serem automaticamente inscritos no Maxient Conduct Manager Software com as suas contas AD Azure.


Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Maxient Conduct Manager Software assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, irá configurar o seu AD Azure para uso com o Maxient Conduct Manager Software.


* Maxient Conduct Manager Software suporta **SP e IDP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-maxient-conduct-manager-software-from-the-gallery"></a>Adicionar Software Maxient Conduct Manager da galeria

Para configurar a integração do Maxient Conduct Manager Software em Azure AD, é necessário adicionar o Software Maxient Conduct Manager da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Maxient Conduct Manager Software** na caixa de pesquisa.
1. Selecione **o Maxient Conduct Manager Software** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-maxient-conduct-manager-software"></a>Configurar e testar a Azure AD um único sinal para o Maxient Conduct Manager Software

Configure e teste Azure AD SSO com Maxient Conduct Manager Software. Para que o SSO funcione, é necessário estabelecer uma ligação entre o Azure AD e o Maxient Conduct Manager Software.

Para configurar e testar o Azure AD SSO com o Maxient Conduct Manager Software, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores autentem para serem utilizados com o Software Maxient Conduct Manager
    1. **[Atribua a todos os utilizadores a utilização da Maxient](#assign-all-users-to-be-able-to-authenticate-for-the-maxient-conduct-manager-software)** - para permitir que todos na sua instituição possam autenticar.
1. **[Teste Ad Setup AD Com Maxient](#test-with-maxient)** - para verificar se a configuração funciona e os atributos corretos estão sendo lançados

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **do Maxient Conduct Manager Software,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** a aplicação está pré-configurada no modo iniciado pelo **IDP** e os URLs necessários já estão pré-povoados com Azure. O utilizador precisa de guardar a configuração clicando no botão **Guardar.**

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://cm.maxient.com/<SCHOOLCODE>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de inscrição real. Trabalhe com o seu representante de Implementação/Suporte Maxient para obter o valor.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** clique no botão de cópia para copiar o Url de **metadados da Federação de Aplicações** e guarde-o no seu computador.  Você precisará fornecer o seu representante de Implementação/Suporte Maxient com este URL.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

### <a name="assign-all-users-to-be-able-to-authenticate-for-the-maxient-conduct-manager-software"></a>Atribua a todos os utilizadores a sua adoção para o software maxient conduct manager

Nesta secção, você concederá acesso a todas as contas para autenticar usando o sistema Azure para o Software Maxient Conduct Manager.  É importante notar que este passo é **necessário** para que o Maxient funcione corretamente.  A Maxient aproveita o seu sistema Azure AD para *autenticar* os utilizadores. A *autorização* dos utilizadores é realizada dentro do sistema Maxient para a função específica que estão a tentar desempenhar. Maxient não usa atributos do seu diretório para tomar essas decisões.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Maxient Conduct Manager Software**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione todos os utilizadores (ou os grupos apropriados) e **atribua-os** para poderem autenticar com a Maxient.

## <a name="test-with-maxient"></a>Teste com Maxient 

Se um bilhete de apoio ainda não tiver sido aberto com um representante de Implementação/Suporte Maxient, envie um e-mail para [support@maxient.com](mailto:support@maxient.com) o assunto "Campus Based Authentication/Azure Setup - \<\<School Name\> \> ". No corpo do e-mail, forneça o **Url de Metadados da Federação de Aplicações.** A equipa Maxient responderá com uma ligação de teste para verificar se os atributos adequados estão a ser libertados.  
    
## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Software Maxient Conduct Manager com Azure AD](https://aad.portal.azure.com/)