---
title: 'Tutorial: Configurar DocuSign para fornecimento automático de utilizadores com Diretório Ativo Azure| Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o DocuSign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: jeedes
ms.openlocfilehash: 71f95b08584a46fccb0975cd9285150573ac02d4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218524"
---
# <a name="tutorial-configure-docusign-for-automatic-user-provisioning"></a>Tutorial: Configurar docuSign para o fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos que precisa de executar em DocuSign e AZure AD para provisões automáticas e desavisionamento de contas de utilizadores de Azure AD a DocuSign.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que já tem os seguintes itens:

*   Um inquilino de diretório Azure Ative.
*   Uma assinatura ativada por docuSign.
*   Uma conta de utilizador em DocuSign com permissões de Administração da Equipa.

## <a name="assigning-users-to-docusign"></a>Atribuir utilizadores ao DocuSign

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático da conta de utilizador, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação em AZure AD são sincronizados.

Antes de configurar e ativar o serviço de fornecimento, tem de decidir quais os utilizadores e/ou grupos em Azure AD que representam os utilizadores que precisam de acesso à sua aplicação DocuSign. Uma vez decididos, pode atribuir estes utilizadores à sua aplicação DocuSign seguindo as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-docusign"></a>Dicas importantes para atribuir utilizadores ao DocuSign

*   Recomenda-se que um único utilizador Azure AD seja designado para o DocuSign para testar a configuração de provisionamento. Utilizadores adicionais podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador ao DocuSign, tem de selecionar uma função de utilizador válida. A função "Acesso Predefinido" não funciona para o provisionamento.

> [!NOTE]
> A Azure AD não suporta o provisionamento do grupo com a aplicação Docusign, só os utilizadores podem ser a provisionados.

## <a name="enable-user-provisioning"></a>Ativar o fornecimento de utilizadores

Esta secção guia-o através da ligação do seu AZure AD à conta de utilizador da DocuSign que fornece a API e configura o serviço de fornecimento para criar, atualizar e desativar as contas de utilizador atribuídas no DocuSign com base na atribuição de utilizadores e grupos em Azure AD.

> [!Tip]
> Pode também optar por ativar Sign-On única baseada em SAML para DocuSign, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal pode ser configurado independentemente do fornecimento automático, embora estas duas características se elogiem mutuamente.

### <a name="to-configure-user-account-provisioning"></a>Para configurar o provisionamento da conta de utilizador:

O objetivo desta secção é delinear como permitir o fornecimento de contas de utilizador do Ative Directory ao DocuSign.

1. No [portal Azure,](https://portal.azure.com)consulte a secção **Azure Ative Directory > Enterprise Apps > Todas as aplicações.**

1. Se já configurar o DocuSign para um único sinal de acesso, procure a sua instância de DocuSign utilizando o campo de pesquisa. Caso contrário, **selecione Add** e procure **docuSign** na galeria de aplicações. Selecione DocuSign a partir dos resultados da pesquisa e adicione-o à sua lista de aplicações.

1. Selecione a sua instância de DocuSign e, em seguida, selecione o **separador Provisioning.**

1. Defina o **Modo de Aprovisionamento** como **Automático**. 

    ![Screenshot do separador Provisioning para DocuSign no portal Azure. O modo de provisionamento está definido para nome de utilizador automático e admin, palavra-passe e ligação de teste são destacados.](./media/docusign-provisioning-tutorial/provisioning.png)

1. Na secção **Credenciais de Administração,** forneça as seguintes definições de configuração:
   
    a. Na caixa de texto **do Nome do Utilizador Admin,** digite um nome de conta DocuSign que tenha o perfil de Administrador do **Sistema** em DocuSign.com atribuído.
   
    b. Na caixa de texto da **Palavra-passe Admin,** digite a palavra-passe para esta conta.

> [!NOTE]
> Se o fornecimento de SSO e do utilizador for configurado, as credenciais de autorização utilizadas para o provisionamento devem ser configuradas para funcionar tanto com o SSO como com o Username/Password.

1. No portal Azure, clique em **Test Connection** para garantir que o Azure AD pode ligar-se à sua aplicação DocuSign.

1. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de provisionamento e verifique a caixa de verificação.

1. Clique **em Guardar.**

1. Sob a secção Mappings, selecione **Synchronize Azure Ative Directory Users to DocuSign.**

1. Na secção **De mapeamentos de atributos,** reveja os atributos do utilizador que são sincronizados de Azure AD a DocuSign. Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder às contas do utilizador no DocuSign para operações de atualização. Selecione o botão Guardar para confirmar as alterações.

1. Para ativar o serviço de prestação de Ad Azure para docuSign, altere o **Estado de Provisionamento** para **On** na secção Definições

1. Clique **em Guardar.**

Inicia a sincronização inicial de quaisquer utilizadores atribuídos ao DocuSign na secção Utilizadores e Grupos. A sincronização inicial demora mais tempo a ser efetuada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em funcionamento. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o provisionamento de registos de atividades, que descrevem todas as ações realizadas pelo serviço de fornecimento na sua aplicação DocuSign.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="troubleshooting-tips"></a>Sugestões de Resolução de Problemas
* A disponibilização de um perfil de papel ou permissão para um utilizador em Docusign pode ser realizada utilizando uma expressão nos mapeamentos do seu atributo utilizando as funções [de comutador](../app-provisioning/functions-for-customizing-application-data.md#switch) e [singleAppRoleAssignment.](../app-provisioning/functions-for-customizing-application-data.md#singleapproleassignment) Por exemplo, a expressão abaixo irá prever o ID "8032066" quando um utilizador tem a função "DS Admin" atribuída em Azure AD. Não providenciará nenhum perfil de permissão se o utilizador não for atribuído a uma função do lado AD do Azure. O ID pode ser recuperado a partir do [portal](https://support.docusign.com/articles/Default-settings-for-out-of-the-box-DocuSign-Permission-Profiles)DocuSign .

Switch (SingleAppRoleAssignment ([appRoleAssignments])," "DS Admin", "8032066")


## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar o único sign-on](docusign-tutorial.md)
