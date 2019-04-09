---
title: 'Tutorial: Configurar o fundamento OnDemand para o aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador com base OnDemand.
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
ms.openlocfilehash: 9d17a3c81784d56c6fcad7c7608559abf732882a
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057936"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Tutorial: Configurar o fundamento OnDemand para o aprovisionamento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos para ser executada na base OnDemand e Azure Active Directory (Azure AD) para configurar o Azure AD para aprovisionar e desaprovisionar utilizadores e/ou grupos para fundamento OnDemand automaticamente.

> [!NOTE]
> Este tutorial descreve um conector assentes no serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD
* Um inquilino de alicerce OnDemand
* Uma conta de utilizador na base OnDemand com permissões de administrador

> [!NOTE]
> O Azure AD aprovisionamento integração depende a [fundamento OnDemand Webservice](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf), que está disponível para as equipes de alicerce OnDemand.

## <a name="adding-cornerstone-ondemand-from-the-gallery"></a>Adicionando o fundamento OnDemand da Galeria

Antes de configurar a peça fundamental OnDemand para aprovisionamento automático de utilizadores com o Azure AD, terá de adicionar OnDemand base a partir da Galeria de aplicações do Azure AD à sua lista de aplicações de SaaS geridas.

**Para adicionar o fundamento OnDemand a partir da Galeria de aplicações do Azure AD, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **fundamento OnDemand**, selecione **fundamento OnDemand** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Alicerce OnDemand na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-cornerstone-ondemand"></a>Atribuir utilizadores a fundamento OnDemand

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas a utilizadores e/ou grupos que foram "atribuídos" a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o aprovisionamento de utilizador automático, deve decidir o que os utilizadores e/ou grupos no Azure AD precisam de acesso a fundamento OnDemand. Depois de decidir, pode atribuir estes utilizadores e/ou grupos a fundamento OnDemand ao seguir as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Dicas importantes para atribuir utilizadores a fundamento OnDemand

* Recomenda-se que um único utilizador do Azure AD está atribuído a fundamento OnDemand para testar o configuração de aprovisionamento automático de utilizadores. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

* Quando atribuir um utilizador a fundamento OnDemand, tem de selecionar qualquer função de específicas da aplicação válida (se disponível) na caixa de diálogo atribuição. Os utilizadores com o **acesso predefinido** função são excluídas desde o aprovisionamento.

## <a name="configuring-automatic-user-provisioning-to-cornerstone-ondemand"></a>Configurar o aprovisionamento automático de utilizadores para OnDemand de base

Esta secção orienta-o pelos passos para configurar o Azure AD do serviço de aprovisionamento para criar, atualizar e desativar os utilizadores e/ou grupos no OnDemand base com base no utilizador e/ou atribuições de grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-cornerstone-ondemand-in-azure-ad"></a>Para configurar o aprovisionamento automático de utilizadores para OnDemand de fundamento no Azure AD:

1. Entrar para o [portal do Azure](https://portal.azure.com) e selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **fundamento OnDemand**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **fundamento OnDemand**.

    ![A ligação de base OnDemand na lista de aplicações](common/all-applications.png)

3. Selecione o **aprovisionamento** separador.

    ![Aprovisionamento de OnDemand base](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Aprovisionamento de OnDemand base](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Sob o **credenciais de administrador** secção, de entrada a **nome de utilizador administrador**, **palavra-passe de administrador**, e **domínio** de sua OnDemand de base conta.

    * Na **nome de utilizador administrador** campo, preencher o domínio \ nomedeutilizador da conta de administrador no seu inquilino de alicerce OnDemand. Exemplo: contoso\admin.

    * Na **palavra-passe de administrador** campo, preencher a palavra-passe correspondente ao nome de utilizador administrador.

    * Na **domínio** campo, preencher o URL do serviço Web do inquilino fundamento OnDemand. Exemplo: O serviço está localizado em `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`, para Contoso domínio é `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`. Para obter mais informações sobre como obter o URL do serviço Web, consulte [aqui](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. Após preencher os campos mostrados no passo 5, clique em **Testar ligação** para garantir que o Azure AD pode ligar à base OnDemand. Se a ligação falhar, certifique-se de que a conta de base OnDemand tem permissões de administrador e tente novamente.

    ![Aprovisionamento de OnDemand base](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. Na **notificação por E-Mail** campo, introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação - **enviar uma notificação por e-mail quando uma falha ocorre**.

    ![Aprovisionamento de OnDemand base](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Clique em **Guardar**.

9. Sob o **mapeamentos** secção, selecione **sincronizar do Azure Active Directory Users para fundamento OnDemand**.

    ![Aprovisionamento de OnDemand base](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Reveja os atributos de utilizador que são sincronizados a partir do Azure AD para OnDemand de fundamento no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador na base OnDemand para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Aprovisionamento de OnDemand base](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Para configurar filtros de âmbito, consulte as seguintes instruções fornecidas a [tutorial de filtro de Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para ativar o Azure AD para o fundamento OnDemand do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção.

    ![Aprovisionamento de OnDemand base](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Definir a utilizadores e/ou grupos que pretende fazer o aprovisionamento fundamento OnDemand escolhendo os valores pretendidos na **âmbito** no **definições** secção.

    ![Aprovisionamento de OnDemand base](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Quando estiver pronto para aprovisionar, clique em **guardar**.

    ![Aprovisionamento de OnDemand base](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade, que descreve todas as ações executadas pelo Azure AD em OnDemand de base do serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações de conector

* O OnDemand fundamento **posição** atributo espera um valor que corresponde às funções no portal de Fundação OnDemand. A lista de válido **posição** valores podem ser obtidos ao navegar até **editar registo de utilizador > estrutura da organização > posição** no portal de Fundação OnDemand.

    ![Base de aprovisionamento de OnDemand Editar utilizador](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png) ![OnDemand fundamento aprovisionamento posição](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png) ![OnDemand fundamento lista de posições de aprovisionamento](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
