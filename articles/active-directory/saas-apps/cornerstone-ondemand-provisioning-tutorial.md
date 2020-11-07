---
title: 'Tutorial: Configurar a Cornerstone OnDemand para fornecimento automático de utilizadores com diretório Azure Ative / Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisá-lo e desprovisionar automaticamente as contas dos utilizadores à Cornerstone OnDemand.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 59c599167089d222324ed880c18e68d763f5e468
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358461"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Tutorial: Configurar a Cornerstone OnDemand para o fornecimento automático de utilizadores

Este tutorial demonstra os passos a executar em Cornerstone OnDemand e Azure Ative Directory (Azure AD) para configurar a Azure AD para provisão automática de utilizadores ou grupos de desprovisionamento para a Cornerstone OnDemand.

> [!NOTE]
> Este tutorial descreve um conector que é construído em cima do serviço de fornecimento de utilizadores AZure AD. Para obter informações sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações de software-as-a-service (SaaS) com diretório Azure Ative.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que você tem:

* Um inquilino do Azure AD.
* Um inquilino da Cornerstone OnDemand.
* Uma conta de utilizador em Cornerstone OnDemand com permissões de administração.

> [!NOTE]
> A integração de provisionamento azure AD depende do [serviço web Cornerstone OnDemand](https://www.cornerstoneondemand.com/). Este serviço está disponível para as equipas da Cornerstone OnDemand.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Adicione a Cornerstone OnDemand do Mercado Azure

Antes de configurar a Cornerstone OnDemand para fornecimento automático de utilizadores com Azure AD, adicione a Cornerstone OnDemand do Marketplace à sua lista de aplicações geridas pelo SaaS.

Para adicionar a Cornerstone OnDemand do Marketplace, siga estes passos.

1. No [portal Azure,](https://portal.azure.com)no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O ícone do Diretório Ativo Azure](common/select-azuread.png)

2. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione **Nova aplicação** na parte superior da caixa de diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **a Cornerstone OnDemand** e selecione **Cornerstone OnDemand** a partir do painel de resultados. Para adicionar a aplicação, **selecione Adicionar**.

    ![Stonestone OnDemand na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Atribuir utilizadores à Cornerstone OnDemand

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, decida quais os utilizadores ou grupos em Azure AD que precisam de acesso à Cornerstone OnDemand. Para atribuir estes utilizadores ou grupos à Cornerstone OnDemand, siga as instruções em [Atribuir um utilizador ou grupo a uma aplicação da empresa.](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Dicas importantes para atribuir utilizadores à Cornerstone OnDemand

* Recomendamos que atribua um único utilizador Azure AD à Cornerstone OnDemand para testar a configuração automática de provisionamento do utilizador. Pode atribuir utilizadores ou grupos adicionais mais tarde.

* Quando atribuir um utilizador à Cornerstone OnDemand, selecione qualquer função específica de aplicação válida, se disponível, na caixa de diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Configure o fornecimento automático de utilizadores à Cornerstone OnDemand

Esta secção guia-o através dos passos para configurar o serviço de fornecimento AZure AD. Utilize-o para criar, atualizar e desativar utilizadores ou grupos em Cornerstone OnDemand com base em atribuições de utilizador ou grupo em Azure AD.

Para configurar o fornecimento automático de utilizadores para a Cornerstone OnDemand em Azure AD, siga estes passos.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **aplicações da Empresa**  >  **Todas as aplicações**  >  **Cornerstone OnDemand**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Cornerstone OnDemand**.

    ![O link Stone OnDemand na lista de aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Stonestone OnDemand Provisioning](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Desa ajuste **o modo de provisionamento** para **automático**.

    ![Modo de Provisionamento stonestone onDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Na secção **Credenciais de Administração,** insira o nome de utilizador de administração, a palavra-passe de administrador e o domínio da conta da Sua Pedra Angular OnDemand:

    * Na caixa **de nome de utilizador Admin,** preencha o domínio ou o nome de utilizador da conta de administração no seu inquilino Cornerstone OnDemand. Um exemplo é contoso\admin.

    * Na caixa **de palavra-passe Admin,** preencha a palavra-passe que corresponde ao nome de utilizador de administrador.

    * Na caixa **de Domínio,** preencha o URL de serviço web do inquilino Cornerstone OnDemand. Por exemplo, o serviço está localizado em `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx` , e para Contoso o domínio é `https://ws-contoso.csod.com/feed30/clientdataservice.asmx` . Para obter mais informações sobre como recuperar o URL do serviço web, consulte [este pdf](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. Depois de preencher as caixas mostradas no Passo 5, selecione **a Ligação de Teste** para se certificar de que o Azure AD pode ligar-se à Cornerstone OnDemand. Se a ligação falhar, certifique-se de que a sua conta Cornerstone OnDemand tem permissões de administração e tente novamente.

    ![Ligação de teste de Stonestone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. Na caixa de **e-mail de notificação,** insira o endereço de e-mail da pessoa ou grupo para receber as notificações de erro de provisionamento. Selecione a **notificação Enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![E-mail de notificação de StoneStone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Selecione **Save** (Guardar).

9. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Cornerstone OnDemand**.

    ![Sincronização de Stonestone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Reveja os atributos do utilizador que são sincronizados de Azure AD a Cornerstone OnDemand na secção **De mapeamentos de atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder às contas de utilizador em Cornerstone OnDemand para operações de atualização. Para guardar quaisquer alterações, **selecione Guardar**.

    ![Mapeamentos de atributos stonea onDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Para configurar filtros de deteção, siga as instruções no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para ativar o serviço de prestação de AD Azure para a Cornerstone OnDemand, na secção **Definições,** altere o **Estado de Provisionamento** para **On**.

    ![Estado de provisionamento da Pedra Angular OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Defina os utilizadores ou grupos que pretende prever à Cornerstone OnDemand. Na secção **Definições,** selecione os valores que pretende no **Âmbito**.

    ![Extensão Stone OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Quando estiver pronto para a provisão, **selecione Save**.

    ![Cornerstone OnDemand Save](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser praticada do que as sincronizações posteriores. Ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD funciona. 

Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o relatório de atividades de provisionamento. O relatório descreve todas as ações realizadas pelo serviço de fornecimento de Azure AD em Cornerstone OnDemand.

Para obter informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

O atributo Stone OnDemand **Position** espera um valor que corresponda aos papéis no portal Cornerstone OnDemand. Para obter uma lista de valores de **posição** válidos, vá a **Editar o Registo de Utilizador > Estrutura de Organização > posição** no portal Cornerstone OnDemand.

![Stonestone OnDemand Provisioning Editing User Record](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Posição de provisionamento de Pedra Angular OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Lista de posições de provisionamento da Pedra Angular OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento da conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
