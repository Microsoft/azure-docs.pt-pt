---
title: 'Tutorial: Configure Cornerstone OnDemand para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo azure para fornecer e desfornecer automaticamente contas de utilizadores à Cornerstone OnDemand.
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
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6212e74ecbf8327d3939138de2e92868f29b0f1a
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77058448"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Tutorial: Configure Cornerstone OnDemand para fornecimento automático de utilizadores

Este tutorial demonstra os passos a executar na Cornerstone OnDemand e azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer e desfornecer automaticamente utilizadores ou grupos para a Cornerstone OnDemand.

> [!NOTE]
> Este tutorial descreve um conector que é construído em cima do serviço de fornecimento de utilizadores DaAzure AD. Para obter informações sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o [fornecimento e o desprovisionamento de utilizadores automate para aplicações de software como um serviço (SaaS) com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que tem:

* Um inquilino do Azure AD.
* Um inquilino da Cornerstone OnDemand.
* Uma conta de utilizador na Cornerstone OnDemand com permissões de administração.

> [!NOTE]
> A integração de fornecimento de Anúncios Azure baseia-se no [serviço web Cornerstone OnDemand.](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf) Este serviço está disponível para as equipas Dana Angular OnDemand.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Adicione a Cornerstone OnDemand do Azure Marketplace

Antes de configurar a Cornerstone OnDemand para o fornecimento automático de utilizadores com o Azure AD, adicione a Cornerstone OnDemand do Marketplace à sua lista de aplicações SaaS geridas.

Para adicionar a Cornerstone OnDemand do Marketplace, siga estes passos.

1. No [portal Azure,](https://portal.azure.com)no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O ícone do Diretório Ativo Azure](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione **Nova aplicação** na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza a **Cornerstone OnDemand** e selecione **Cornerstone OnDemand** a partir do painel de resultados. Para adicionar a aplicação, selecione **Adicionar**.

    ![Cornerstone OnDemand na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Atribuir utilizadores à Cornerstone OnDemand

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores ou grupos que foram atribuídos a uma aplicação em Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, decida quais os utilizadores ou grupos em AD Azure que precisam de acesso à Cornerstone OnDemand. Para atribuir estes utilizadores ou grupos à Cornerstone OnDemand, siga as instruções em [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Dicas importantes para atribuir utilizadores à Cornerstone OnDemand

* Recomendamos que atribua um único utilizador da AD Azure à Cornerstone OnDemand para testar a configuração automática de fornecimento de utilizadores. Mais tarde, pode atribuir utilizadores ou grupos adicionais.

* Quando atribuir um utilizador à Cornerstone OnDemand, selecione qualquer função específica de aplicação válida, se disponível, na caixa de diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Configure o fornecimento automático de utilizadores à Cornerstone OnDemand

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure. Utilize-o para criar, atualizar e desativar utilizadores ou grupos na Cornerstone OnDemand com base em atribuições de utilizadores ou grupos em Azure AD.

Para configurar o fornecimento automático de utilizadores para a Cornerstone OnDemand em Azure AD, siga estes passos.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações da Enterprise** > **Todas as aplicações** > **Cornerstone OnDemand**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Cornerstone OnDemand**.

    ![O link Cornerstone OnDemand na lista de aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Provisão ondemand da pedra angular](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Definir **o modo de provisionamento** para **automático**.

    ![Modo de provisionamento ondemand da pedra angular](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Na secção **de Credenciais de Administrador,** introduza o nome de utilizador, a palavra-passe de administrador e o domínio da conta da Cornerstone OnDemand:

    * Na caixa De Nome de **Utilizador Da Admin,** preencha o domínio ou o nome de utilizador da conta de administrador no seu inquilino Cornerstone OnDemand. Um exemplo é contoso\admin.

    * Na caixa **de palavra-passe do Administrador,** preencha a palavra-passe correspondente ao nome de utilizador do administrador.

    * Na caixa **de Domínio,** preencha o URL do serviço web do inquilino Cornerstone OnDemand. Por exemplo, o serviço está localizado em `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`, e para Contoso o domínio é `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`. Para obter mais informações sobre como recuperar o URL do serviço web, consulte [este pdf](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. Depois de preencher as caixas mostradas no Passo 5, selecione **Test Connection** para se certificar de que o Azure AD pode ligar-se à Cornerstone OnDemand. Se a ligação falhar, certifique-se de que a sua conta Cornerstone OnDemand tem permissões de administração e tente novamente.

    ![Ligação de teste a pedido de pedra angular](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. Na caixa de **e-mail de notificação,** insira o endereço de e-mail da pessoa ou grupo para receber as notificações de erro de fornecimento. Selecione a **notificação de e-mail enviar uma notificação de e-mail quando ocorrer uma falha** verificar a caixa.

    ![E-mail de notificação ondemand da cornerstone](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Selecione **Guardar**.

9. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Cornerstone OnDemand**.

    ![Sincronização onDemand da Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Reveja os atributos do utilizador que são sincronizados de Azure AD para Cornerstone OnDemand na secção **DeMapeamentos de Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Cornerstone OnDemand para operações de atualização. Para guardar quaisquer alterações, selecione **Guardar**.

    ![Mapeamento de atributos ondemand da Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Para configurar filtros de deteção, siga as instruções no tutorial do [filtro de deteção](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para ativar o serviço de provisionamento de AD Azure para a Cornerstone OnDemand, na secção **Definições,** altere o **Estado de Provisionamento** para **On**.

    ![Estatuto de Provisionamento onDemand de pedra angular](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Defina os utilizadores ou grupos que pretende fornecer à Cornerstone OnDemand. Na secção **Definições,** selecione os valores que deseja no **Âmbito**.

    ![Mira de base sobre a procura](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Quando estiver pronto para fornecer, selecione **Guardar**.

    ![Poupança OnDemand da Cornerstone](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a executar do que as sincronizações posteriores. Ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento da AD Azure seja executado. 

Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir as ligações ao relatório de atividades de provisionamento. O relatório descreve todas as ações realizadas pelo serviço de provisionamento da AD Azure na Cornerstone OnDemand.

Para obter informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático de conta](../app-provisioning/check-status-user-account-provisioning.md)de utilizador .

## <a name="connector-limitations"></a>Limitações do conector

O atributo da **Posição** OnDemand da Cornerstone espera um valor que corresponda às funções no portal Cornerstone OnDemand. Para obter uma lista de valores de **Posição** válidos, vá a **Editar User Record > Organization Structure > Position** no portal Cornerstone OnDemand.

![Cornerstone OnDemand Provisioning Editar Registo de Utilizadores](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Posição de provisionamento sobre pedidos de base de pedra](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Lista de posições de provisionamento ondemand da Cornerstone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
