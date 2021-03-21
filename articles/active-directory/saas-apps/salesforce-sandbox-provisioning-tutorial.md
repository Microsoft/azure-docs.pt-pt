---
title: 'Tutorial: Configurar a Caixa de Areia Salesforce para o fornecimento automático de utilizadores com o Azure Ative Directory| Microsoft Docs'
description: Conheça os passos necessários para realizar na Salesforce Sandbox e Azure AD para obter e desavisar automaticamente as contas de utilizadores de Azure AD para Salesforce Sandbox.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 7e3f8e5e975468b468712ae8907cdca0e80a5f9f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94352613"
---
# <a name="tutorial-configure-salesforce-sandbox-for-automatic-user-provisioning"></a>Tutorial: Configurar a Caixa de Areia Salesforce para o provisionamento automático do utilizador

O objetivo deste tutorial é mostrar-lhe os passos que precisa de executar na Salesforce Sandbox e AZure AD para fornecimento e desavisionamento automática de contas de utilizadores do Azure AD para a Salesforce Sandbox.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que já tem os seguintes itens:

*   Um inquilino de diretório Azure Ative.
*   Um inquilino válido para Salesforce Sandbox para trabalho ou Salesforce Sandbox para educação. Pode utilizar uma conta de teste gratuita para qualquer um dos serviços.
*   Uma conta de utilizador na Salesforce Sandbox com permissões team Admin.

## <a name="assigning-users-to-salesforce-sandbox"></a>Atribuir utilizadores à Salesforce Sandbox

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático da conta de utilizador, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação em AZure AD são sincronizados.

Antes de configurar e ativar o serviço de fornecimento, tem de decidir quais os utilizadores ou grupos em Azure AD que precisam de acesso à sua aplicação Salesforce Sandbox. Depois de tomar esta decisão, pode atribuir estes utilizadores à sua app Salesforce Sandbox seguindo as instruções em [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Dicas importantes para atribuir utilizadores à Salesforce Sandbox

* Recomenda-se que um único utilizador Azure AD seja designado para a Salesforce Sandbox para testar a configuração de provisionamento. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador à Salesforce Sandbox, tem de selecionar uma função de utilizador válida. A função "Acesso Predefinido" não funciona para o provisionamento.

> [!NOTE]
> Esta aplicação importa funções personalizadas da Salesforce Sandbox como parte do processo de provisionamento, que o cliente pode querer selecionar ao atribuir os utilizadores.

## <a name="enable-automated-user-provisioning"></a>Permitir o fornecimento automatizado de utilizadores

Esta secção guia-o através da ligação do seu AD AZure à conta de utilizador da Salesforce Sandbox que fornece API, e configura o serviço de fornecimento para criar, atualizar e desativar as contas de utilizador atribuídas na Salesforce Sandbox com base na atribuição de utilizadores e grupos no Azure AD.

>[!Tip]
>Pode também optar por ativar Sign-On única baseada em SAML para a Salesforce Sandbox, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal pode ser configurado independentemente do fornecimento automático, embora estas duas características se elogiem mutuamente.

### <a name="configure-automatic-user-account-provisioning"></a>Configurar o provisionamento automático da conta de utilizador

O objetivo desta secção é delinear como permitir o fornecimento de contas de utilizador do Ative Directory à Salesforce Sandbox.

1. No [portal Azure,](https://portal.azure.com)consulte a secção **Azure Ative Directory > Enterprise Apps > Todas as aplicações.**

1. Se já configurar a Salesforce Sandbox para um único sign-on, procure a sua instância de Salesforce Sandbox utilizando o campo de pesquisa. Caso contrário, **selecione Add** e procure por **Salesforce Sandbox** na galeria de aplicações. Selecione Salesforce Sandbox a partir dos resultados da pesquisa e adicione-o à sua lista de aplicações.

1. Selecione a sua instância de Salesforce Sandbox e, em seguida, selecione o **separador Provisioning.**

1. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![O Screenshot mostra a página de Provisionamento de Sandbox Salesforce, com o modo de provisionamento definido para Automático e outros valores que pode definir.](./media/salesforce-sandbox-provisioning-tutorial/provisioning.png)

1. Na secção **Credenciais de Administração,** forneça as seguintes definições de configuração:
   
    a. Na caixa de texto do **nome de utilizador Admin,** digite um nome de conta Salesforce Sandbox que tenha o perfil **de Administrador do Sistema** em Salesforce.com atribuído.
   
    b. Na caixa de texto da **Palavra-passe Admin,** digite a palavra-passe para esta conta.

1. Para obter o seu token de segurança Salesforce Sandbox, abra um novo separador e inscreva-se na mesma conta de administração salesforce Sandbox. No canto superior direito da página, clique no seu nome e, em seguida, clique em **Definições**.

     ![A screenshot mostra o link Definições selecionado.](./media/salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "Permitir o fornecimento automático do utilizador")

1. No painel de navegação à esquerda, clique em **My Personal Information** para expandir a secção relacionada e, em seguida, clique em Redefinir o **Token de Segurança**.
  
    ![A screenshot mostra reset My Security Token selecionado a partir de My Personal Information.](./media/salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "Permitir o fornecimento automático do utilizador")

1. Na página **Reset Security Token,** clique no botão **Reset Security Token.**

    ![O Screenshot mostra a página de Token de Segurança de Repouso, com texto explicativo e a opção de redefinir o token de segurança](./media/salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "Permitir o fornecimento automático do utilizador")

1. Verifique a caixa de entrada de e-mail associada a esta conta de administração. Procure um e-mail da Salesforce Sandbox.com que contenha o novo símbolo de segurança.

1. Copie o token, vá à sua janela AD Azure e cole-a no campo **Secret Token.**

1. No portal Azure, clique em **Test Connection** para garantir que o Azure AD pode ligar-se à sua aplicação Salesforce Sandbox.

1. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de provisionamento e verifique a caixa de verificação.

1. Clique **em Guardar.**  
    
1.  Na secção Mappings, selecione **Synchronize Azure Ative Directory Users to Salesforce Sandbox.**

1. Na secção **De mapeamentos de atributos,** reveja os atributos do utilizador que são sincronizados de Azure AD para Salesforce Sandbox. Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador na Salesforce Sandbox para operações de atualização. Selecione o botão Guardar para confirmar as alterações.

1. Para ativar o serviço de prestação de Ad Azure para a Salesforce Sandbox, altere o **Estado de Provisionamento** para **On** na secção Definições

1. Clique **em Guardar.**

Inicia a sincronização inicial de quaisquer utilizadores e/ou grupos atribuídos à Salesforce Sandbox na secção Utilizadores e Grupos. A sincronização inicial demora mais tempo a ser efetuada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em funcionamento. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o provisionamento de registos de atividades, que descrevem todas as ações realizadas pelo serviço de fornecimento na app Salesforce Sandbox.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar o único sign-on](./salesforce-sandbox-tutorial.md)