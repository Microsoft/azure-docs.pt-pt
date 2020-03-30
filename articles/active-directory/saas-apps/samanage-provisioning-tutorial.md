---
title: 'Tutorial: Configure Samanage para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer e desfornecer automaticamente contas de utilizador para samanage.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 988efc2087b3b30e6073bd7f6e2cf08f91fd397c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060507"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Tutorial: Configure Samanage para fornecimento automático de utilizadores

Este tutorial demonstra os passos a realizar no Samanage e azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer e desfornecer automaticamente utilizadores e grupos para Samanage.

> [!NOTE]
> Este tutorial descreve um conector que é construído em cima do serviço de fornecimento de utilizadores DaAzure AD. Para obter informações sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o [fornecimento e o desprovisionamento de utilizadores automate para aplicações de software como um serviço (SaaS) com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que tem:

* Um inquilino do Azure AD.
* Um [inquilino Samanage](https://www.samanage.com/pricing/) com o pacote Profissional.
* Uma conta de utilizador em Samanage com permissões de administração.

> [!NOTE]
> A integração de fornecimento de AD Azure baseia-se na [Samanage Rest API](https://www.samanage.com/api/). Esta API está disponível para desenvolvedores samanage para contas com o pacote Profissional.

## <a name="add-samanage-from-the-azure-marketplace"></a>Adicione Samanage do Azure Marketplace

Antes de configurar o Samanage para o fornecimento automático de utilizadores com a AD Azure, adicione samanage do Azure Marketplace à sua lista de aplicações SaaS geridas.

Para adicionar Samanage do Marketplace, siga estes passos.

1. No [portal Azure,](https://portal.azure.com)no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O ícone do Diretório Ativo Azure](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione **Nova aplicação** na parte superior da caixa de diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Samanage** e selecione **Samanage** a partir do painel de resultados. Para adicionar a aplicação, selecione **Adicionar**.

    ![Sagere na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Atribuir utilizadores ao Samanage

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores ou grupos que foram atribuídos a uma aplicação em Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, decida quais os utilizadores ou grupos em AD Azure que precisam de acesso ao Samanage. Para atribuir estes utilizadores ou grupos à Samanage, siga as instruções em [Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-samanage"></a>Dicas importantes para atribuir utilizadores à Samanage

*    Hoje em dia, as funções Samanage são automaticamente e dinamicamente povoadas no portal Azure UI. Antes de atribuir funções de Samanage aos utilizadores, certifique-se de que uma sincronização inicial é concluída contra a Samanage para recuperar as mais recentes funções no seu inquilino Samanage.

*    Recomendamos que atribua um único utilizador da AD Azure à Samanage para testar a configuração inicial de fornecimento automático do utilizador. Pode atribuir utilizadores e grupos adicionais mais tarde após o sucesso dos testes.

*    Quando atribuir um utilizador ao Samanage, selecione qualquer função específica de aplicação válida, se disponível, na caixa de diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Configure o fornecimento automático de utilizadores para samanage

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure. Utilize-o para criar, atualizar e desativar utilizadores ou grupos em Samanage com base em atribuições de utilizador ou grupo em Azure AD.

> [!TIP]
> Também pode ativar um único sinal baseado em SAML para samanage. Siga as instruções no tutorial de [assinatura único samanage](samanage-tutorial.md). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se complementem.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Configure o fornecimento automático de utilizadores para Samanage em Azure AD

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** > **Todas as aplicações** > **Samanage**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Samanage**.

    ![O link Samanage na lista de aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Prestação de samanage](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administrador,** insera o url do **inquilino** samanage e **o Token Secreto**. Clique na **ligação de teste** para garantir que o Azure AD pode ligar-se ao Samanage. Se a ligação falhar, certifique-se de que a sua conta Samanage tem permissões de administrador e tente novamente.

    ![Conexão de teste de samanage](./media/samanage-provisioning-tutorial/provisioning.png)

6. Na caixa de **e-mail de notificação,** insira o endereço de e-mail da pessoa ou grupo para receber as notificações de erro de fornecimento. Selecione a **notificação de e-mail enviar uma notificação de e-mail quando ocorrer uma falha** verificar a caixa.

    ![Samanage Notification Email](./media/samanage-provisioning-tutorial/EmailNotification.png)

7. Selecione **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Samanage**.

    ![Sagere a sincronização do utilizador](./media/samanage-provisioning-tutorial/UserMappings.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Samanage na secção **DeMapeamentos de Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Samanage para operações de atualização. Para guardar quaisquer alterações, selecione **Guardar**.

    ![Sagere atributos de utilizador correspondentes](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

10. Para permitir mapeamentos de grupo, sob a secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups para Samanage**.

    ![Sincronização de grupo sagere](./media/samanage-provisioning-tutorial/GroupMappings.png)

11. Conjunto **habilitado** a **Sim** para sincronizar grupos. Reveja os atributos do grupo que são sincronizados de Azure AD para Samanage na secção **DeMapeamentos de Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Samanage para operações de atualização. Para guardar quaisquer alterações, selecione **Guardar**.

    ![Samanage atributos de grupo correspondentes](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Para configurar filtros de deteção, siga as instruções no tutorial do [filtro de deteção](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento de AD Azure para Samanage, na secção **Definições,** altere o **Estado de Provisionamento** para **On**.

    ![Estatuto de Provisionamento De Samanage](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

14. Defina os utilizadores ou grupos que pretende fornecer à Samanage. Na secção **Definições,** selecione os valores que deseja no **Âmbito**. Quando selecionar a opção **Sync todos os utilizadores e grupos,** considere as limitações descritas na seguinte secção "Limitações do Conector".

    ![Âmbito de samanage](./media/samanage-provisioning-tutorial/ScopeSync.png)

15. Quando estiver pronto para fornecer, selecione **Guardar**.

    ![Samanage Save](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Esta operação inicia a sincronização inicial de todos os utilizadores ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a executar do que as sincronizações posteriores. Ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento da AD Azure seja executado. 

Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir as ligações ao relatório de atividades de provisionamento. O relatório descreve todas as ações realizadas pelo serviço de provisionamento da AD Azure em Samanage.

Para obter informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático de conta](../app-provisioning/check-status-user-account-provisioning.md)de utilizador .

## <a name="connector-limitations"></a>Limitações do conector

Se selecionar a opção **Sync todos os utilizadores e grupos** e configurar um valor para as **funções** Samanage atributo, o valor sob o valor Predefinido se a caixa **nula (é opcional)** deve ser expressa no seguinte formato:

- {"displayName":"role"}, onde a função é o valor padrão que deseja.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)


## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
