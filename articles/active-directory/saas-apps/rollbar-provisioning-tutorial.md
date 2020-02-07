---
title: 'Tutorial: Configure Rollbar para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer automaticamente e desfornecer contas de utilizador ao Rollbar.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d737aa16-8ab4-4c0c-a68b-2911623b41eb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: d9720ca769eab8cf0e4ee763c720f6ba12ebb1d9
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063310"
---
# <a name="tutorial-configure-rollbar-for-automatic-user-provisioning"></a>Tutorial: Configure rollbar para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Rollbar e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer automaticamente e desfornecer utilizadores e/ou grupos para Rollbar.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino rollbar](https://rollbar.com/pricing/) que tem um plano de empresa.
* Uma conta de utilizador em Rollbar com permissões de administrador.

## <a name="assigning-users-to-rollbar"></a>Atribuir utilizadores a Rollbar

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Rollbar. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao Rollbar seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-rollbar"></a>Dicas importantes para atribuir utilizadores a Rollbar

* Recomenda-se que um único utilizador da AD Azure seja atribuído ao Rollbar para testar a configuração automática de fornecimento do utilizador. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um utilizador ao Rollbar, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="setup-rollbar-for-provisioning"></a>Configuração rollbar para provisionamento

Antes de configurar o Rollbar para o fornecimento automático de utilizadores com a AD Azure, terá de ativar o fornecimento de SCIM no Rollbar.

1. Inscreva-se na consola [De Administrador rollbar](https://rollbar.com/login/). Clique nas **Definições de Conta**.

    ![Consola de administrador rollbar](media/rollbar-provisioning-tutorial/image00.png)

2. Navegue para o seu **Nome de Inquilino Rollbar > Fichas**de Acesso à Conta .

    ![Consola de administrador rollbar](media/rollbar-provisioning-tutorial/account.png)

3. Copiar o valor do **SCIM.** Este valor será inserido no campo Secret Token no separador de provisionamento da sua aplicação Rollbar no portal Azure.

    ![Consola de administrador rollbar](media/rollbar-provisioning-tutorial/scim.png)

## <a name="add-rollbar-from-the-gallery"></a>Adicione Rollbar da galeria

Para configurar o Rollbar para o fornecimento automático de utilizadores com o Azure AD, é necessário adicionar rollbar à galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar Rollbar da galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **rollbar,** selecione **Rollbar** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Rollbar na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-rollbar"></a>Configurar o fornecimento automático de utilizadores ao Rollbar 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Rollbar com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para rollbar, seguindo as instruções fornecidas no tutorial de [inscrição rollbar Single](rollbar-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

### <a name="to-configure-automatic-user-provisioning-for-rollbar-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para rollbar em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Rollbar**.

    ![O link Rollbar na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção **de Credenciais de Administrador,** insere o valor do **Token** de Acesso à Conta recuperado anteriormente em **Ficha Secreta**. Clique na **ligação de teste** para garantir que o Azure AD pode ligar-se ao Rollbar. Se a ligação falhar, certifique-se de que a sua conta Rollbar tem permissões de administrador e tente novamente.

    ![Consola de administrador rollbar](media/rollbar-provisioning-tutorial/admin.png)

6. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Rollbar**.

    ![Mapeamento de utilizador rollbar](media/rollbar-provisioning-tutorial/usermapping.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Rollbar na secção De Mapeamento do **Atributo.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Rollbar para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos de utilizador rollbar](media/rollbar-provisioning-tutorial/userattribute.png)

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Rollbar**.

    ![Mapeamentos do grupo Rollbar](media/rollbar-provisioning-tutorial/groupmapping.png)

11. Reveja os atributos do grupo que são sincronizados de Azure AD para Rollbar na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar os grupos em Rollbar para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos do Grupo Rollbar](media/rollbar-provisioning-tutorial/groupattribute.png)

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento de AD Azure para rollbar, altere o Estado de **Provisionamento** para **Ligado** na secção **Definições.**

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que gostaria de fornecer ao Rollbar, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

    Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure em Rollbar.

    Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [Relatórios sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md)
    
## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
