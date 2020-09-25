---
title: 'Tutorial: Configurar jive para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Aprenda os passos que necessita de realizar em Jive e Azure AD para obter e desavisar automaticamente as contas de utilizadores de Azure AD a Jive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: fa45ece76499b50694d366ed2dcdd57cc1e0c7dc
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91312101"
---
# <a name="tutorial-configure-jive-for-automatic-user-provisioning"></a>Tutorial: Configure Jive para o fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos que precisa de executar em Jive e AZure AD para automaticamente fornecendo e desresaviss contas de utilizadores de Azure AD a Jive.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que já tem os seguintes itens:

*   Um inquilino de diretório Azure Ative.
*   Um sinal único Jive na subscrição ativada.
*   Uma conta de utilizador em Jive com permissões de Administração da Equipa.

## <a name="assigning-users-to-jive"></a>Atribuir utilizadores à Jive

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático da conta de utilizador, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação em AZure AD são sincronizados.

Antes de configurar e ativar o serviço de fornecimento, tem de decidir quais os utilizadores e/ou grupos em Azure AD que representam os utilizadores que precisam de acesso à sua app Jive. Uma vez decididos, pode atribuir estes utilizadores à sua aplicação Jive seguindo as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-jive"></a>Dicas importantes para atribuir utilizadores à Jive

*   Recomenda-se que um único utilizador AD Azure seja designado para a Jive para testar a configuração de provisionamento. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador ao Jive, deve selecionar uma função de utilizador válida. A função "Acesso Predefinido" não funciona para o provisionamento.

## <a name="enable-user-provisioning"></a>Ativar o fornecimento de utilizadores

Esta secção guia-o através da ligação do seu AD Azure à conta de utilizador da Jive que fornece a API, e configura o serviço de fornecimento para criar, atualizar e desativar as contas de utilizador atribuídas em Jive com base na atribuição de utilizadores e grupos em Azure AD.

> [!TIP]
> Pode também optar por ativar um único sign-on baseado em SAML para Jive, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal pode ser configurado independentemente do fornecimento automático, embora estas duas características se elogiem mutuamente.

### <a name="to-configure-user-account-provisioning"></a>Para configurar o provisionamento da conta de utilizador:

O objetivo desta secção é delinear como permitir o fornecimento de contas de utilizador do Ative Directory à Jive.
Como parte deste procedimento, você é obrigado a fornecer um sinal de segurança do utilizador que você precisa solicitar a Jive.com.

1. No [portal Azure,](https://portal.azure.com)consulte a secção **Azure Ative Directory > Enterprise Apps > Todas as aplicações.**

1. Se já configurar o Jive para um único sinal, procure a sua instância de Jive utilizando o campo de pesquisa. Caso contrário, **selecione Add** e procure por **Jive** na galeria de aplicações. Selecione Jive a partir dos resultados da pesquisa e adicione-o à sua lista de aplicações.

1. Selecione a sua instância de Jive e, em seguida, selecione o **separador Provisioning.**

1. Defina o **Modo de Aprovisionamento** como **Automático**. 

    ![A screenshot mostra a página de Provisionamento Jive, com o modo de provisionamento definido para Automático e outros valores que pode definir.](./media/jive-provisioning-tutorial/provisioning.png)

1. Na secção **Credenciais de Administração,** forneça as seguintes definições de configuração:
   
    a. Na caixa de texto **Jive Admin User Name,** digite um nome de conta Jive que tenha o perfil **de Administrador do Sistema** em Jive.com atribuído.
   
    b. Na caixa de texto **Jive Admin Password,** digite a palavra-passe para esta conta.
   
    c. Na caixa de texto **Jive Tenant URL,** digite a URL do inquilino Jive.
      
      > [!NOTE]
      > O URL do inquilino Jive é URL que é usado pela sua organização para iniciar sessão em Jive.  
      > Normalmente, o URL tem o seguinte formato: **www. \<organization\> jive.com.**          

1. No portal Azure, clique em **Test Connection** para garantir que a Azure AD pode ligar-se à sua aplicação Jive.

1. Insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de provisionamento no campo **de e-mail de notificação** e verifique a caixa de verificação abaixo.

1. Clique **em Guardar.**

1. Na secção Mappings, selecione **Synchronize Azure Ative Directory Users para Jive.**

1. Na secção **De Mapeamentos de Atributos,** reveja os atributos do utilizador que são sincronizados de Azure AD a Jive. Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador em Jive para operações de atualização. Selecione o botão Guardar para confirmar as alterações.

1. Para ativar o serviço de prestação de Ad Azure para jive, altere o **Estado de Provisionamento** para **On** na secção Definições

1. Clique **em Guardar.**

Inicia a sincronização inicial de quaisquer utilizadores e/ou grupos atribuídos ao Jive na secção Utilizadores e Grupos. A sincronização inicial demora mais tempo a ser efetuada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em funcionamento. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para registos de atividade de provisionamento, que descrevem todas as ações realizadas pelo serviço de fornecimento na sua app Jive.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar o único sign-on](jive-tutorial.md)
