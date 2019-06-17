---
title: 'Tutorial: Configurar o fundamento OnDemand para o aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para automaticamente provisionar e desprovisionar contas de utilizador com base OnDemand.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85ddcf3aff7d15c946230cedb0da190bca6aeab7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721933"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Tutorial: Configurar o fundamento OnDemand para o aprovisionamento automático de utilizadores

Este tutorial demonstra os passos para executar no OnDemand de base e Azure Active Directory (Azure AD) para configurar o Azure AD para aprovisionar e desaprovisionar utilizadores ou grupos para fundamento OnDemand automaticamente.

> [!NOTE]
> Este tutorial descreve um conector que é criado sobre o serviço de aprovisionamento de utilizador do Azure AD. Para obter informações sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações de software-como-serviço (SaaS) com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que tem:

* Um inquilino do Azure AD.
* Um inquilino de alicerce OnDemand.
* Uma conta de utilizador na base OnDemand com permissões de administrador.

> [!NOTE]
> O Azure AD aprovisionamento integração depende da [serviço da web de Fundação OnDemand](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf). Este serviço está disponível para as equipes de alicerce OnDemand.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Adicionar a peça fundamental OnDemand no Azure Marketplace

Antes de configurar o fundamento OnDemand para automático de utilizadores de aprovisionamento com o Azure AD, adicione fundamento OnDemand do Marketplace à sua lista de aplicações de SaaS geridas.

Para adicionar a peça fundamental OnDemand do Marketplace, siga estes passos.

1. Na [portal do Azure](https://portal.azure.com), no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O ícone do Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione **nova aplicação** na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **fundamento OnDemand** e selecione **fundamento OnDemand** do painel de resultados. Para adicionar a aplicação, selecione **adicionar**.

    ![Alicerce OnDemand na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Atribuir utilizadores à base OnDemand

O Azure Active Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas os utilizadores ou grupos que foram atribuídos a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o aprovisionamento automático de utilizadores, decida quais os utilizadores ou grupos no Azure AD precisam de acesso a fundamento OnDemand. Para atribuir estes utilizadores ou grupos a fundamento OnDemand, siga as instruções em [atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Dicas importantes para atribuir utilizadores a fundamento OnDemand

* Recomendamos que atribua um único utilizador do Azure AD para OnDemand de alicerce para testar o configuração de aprovisionamento automático de utilizadores. Pode atribuir utilizadores adicionais ou grupos mais tarde.

* Quando atribui um utilizador a fundamento OnDemand, selecione qualquer função específicas da aplicação válida, se estiver disponível, na caixa de diálogo de atribuição. Os utilizadores com o **acesso predefinido** função são excluídas desde o aprovisionamento.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Configurar o aprovisionamento automático de utilizadores para OnDemand de base

Esta secção orienta-o pelos passos para configurar o serviço de aprovisionamento do AD do Azure. Utilize-o para criar, atualizar e desativar os utilizadores ou grupos no OnDemand base com base em atribuições de utilizador ou grupo no Azure AD.

Para configurar o aprovisionamento automático de utilizadores para OnDemand de fundamento no Azure AD, siga estes passos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações empresariais** > **todas as aplicações** > **fundamento OnDemand**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **fundamento OnDemand**.

    ![A ligação de base OnDemand na lista de aplicações](common/all-applications.png)

3. Selecione o **aprovisionamento** separador.

    ![Aprovisionamento de OnDemand base](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Definir **modo de aprovisionamento** ao **automática**.

    ![Modo de aprovisionamento de OnDemand de base](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Sob o **credenciais de administrador** , digite o nome de utilizador administrador, a palavra-passe de administrador e o domínio da conta de sua OnDemand de base:

    * Na **nome de utilizador administrador** caixa, preencha o domínio ou nome de utilizador da conta de administrador do seu inquilino de alicerce OnDemand. Um exemplo é contoso\admin.

    * Na **palavra-passe de administrador** caixa, preencha a palavra-passe que corresponde ao nome de utilizador administrador.

    * Na **domínio** caixa, preencha o URL do serviço web do inquilino fundamento OnDemand. Por exemplo, o serviço está localizado em `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`, e para a Contoso é o domínio `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`. Para obter mais informações sobre como obter o URL do serviço web, consulte [este pdf](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. Depois de preencher as caixas de mostrado no passo 5, selecione **Testar ligação** para se certificar de que esse Azure AD pode ligar à base OnDemand. Se a ligação falhar, certifique-se de que a conta de base OnDemand tem permissões de administrador e tente novamente.

    ![Ligação de teste de OnDemand de base](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. Na **notificação por E-Mail** caixa, introduza o endereço de e-mail da pessoa ou grupo para receber as notificações de erro de aprovisionamento. Selecione o **enviar uma notificação por e-mail quando ocorre uma falha** caixa de verificação.

    ![E-Mail de notificação de OnDemand de base](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Selecione **Guardar**.

9. Sob o **mapeamentos** secção, selecione **sincronizar do Azure Active Directory Users para fundamento OnDemand**.

    ![Sincronização de OnDemand de base](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Reveja os atributos de utilizador que são sincronizados a partir do Azure AD para OnDemand de fundamento no **mapeamentos de atributos** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador na base OnDemand para operações de atualização. Para guardar as alterações, selecione **guardar**.

    ![Mapeamentos de atributos de OnDemand de base](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Para configurar filtros de âmbito, siga as instruções a [tutorial âmbito do filtro](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para ativar o Azure AD para OnDemand fundamento, do serviço de aprovisionamento no **configurações** secção, altere **estado de aprovisionamento** para **no**.

    ![Estado de aprovisionamento de OnDemand de base](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Defina os utilizadores ou grupos que pretende fazer o aprovisionamento fundamento OnDemand. Na **definições** secção, selecione os valores que pretende na **âmbito**.

    ![Alicerce OnDemand âmbito](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Quando estiver pronto para aprovisionar, selecione **guardar**.

    ![Alicerce OnDemand guardar](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores ou grupos definidos na **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações posteriores. Que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure é executado. 

Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade de aprovisionamento. O relatório descreve todas as ações executadas pelo Azure AD em OnDemand de base do serviço de aprovisionamento.

Para obter informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações de conector

O OnDemand fundamento **posição** atributo espera um valor que corresponde às funções no portal de Fundação OnDemand. Para obter uma lista de válido **posição** valores, acedas à **editar registo de utilizador > estrutura da organização > posição** no portal de Fundação OnDemand.

![Base de aprovisionamento de OnDemand editar o registo de utilizador](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Posição de aprovisionamento de OnDemand de base](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Lista de posição de aprovisionamento de OnDemand base](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
