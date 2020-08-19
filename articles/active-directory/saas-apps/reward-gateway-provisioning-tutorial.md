---
title: 'Tutorial: Configure Reward Gateway para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores ao Reward Gateway.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 298ca69a659bec2d5262b344c274fac7371390f1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88534358"
---
# <a name="tutorial-configure-reward-gateway-for-automatic-user-provisioning"></a>Tutorial: Configure Reward Gateway para o fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Reward Gateway e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desavisagem automática de utilizadores e/ou grupos para Reward Gateway.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações saaS com diretório Azure Ative.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
>
> Este conector encontra-se atualmente em visualização pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* Um [inquilino reward Gateway.](https://www.rewardgateway.com/)
* Uma conta de utilizador em Reward Gateway com permissões de Administração.

## <a name="assigning-users-to-reward-gateway"></a>Atribuir utilizadores ao Reward Gateway 

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Reward Gateway. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos ao Reward Gateway seguindo as instruções em [Atribuir um utilizador ou grupo a uma aplicação empresarial.](../manage-apps/assign-user-or-group-access-portal.md)


## <a name="important-tips-for-assigning-users-to-reward-gateway"></a>Dicas importantes para atribuir utilizadores ao Reward Gateway 

* Recomenda-se que um único utilizador AZure AD seja designado para o Reward Gateway para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao Reward Gateway, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="setup-reward-gateway--for-provisioning"></a>Gateway de recompensa de configuração para provisionamento
Antes de configurar o Reward Gateway para o fornecimento automático de utilizadores com Azure AD, terá de ativar o fornecimento scim no Reward Gateway.

1. Inscreva-se na sua [Consola de Administração Reward Gateway.](https://rewardgateway.photoshelter.com/login/) Clique **em Integrações**.

    ![Consola de administração Reward Gateway](media/reward-gateway-provisioning-tutorial/image00.png)

2.  Selecione **A Minha Integração**.

    ![Consola de administração Reward Gateway](media/reward-gateway-provisioning-tutorial/image001.png)

3.  Copie os valores do **URL SCIM (v2)** e **do Token do Portador da OAuth**. Estes valores serão introduzidos no campo URL do Inquilino e Token Secreto no separador Provisioning da sua aplicação Reward Gateway no portal Azure.

    ![Consola de administração Reward Gateway](media/reward-gateway-provisioning-tutorial/image03.png)

## <a name="add-reward-gateway-from-the-gallery"></a>Adicione Reward Gateway da galeria

Para configurar o Reward Gateway para o fornecimento automático de utilizadores com Azure AD, é necessário adicionar o Reward Gateway da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Reward Gateway da galeria de aplicações AZure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **o Reward Gateway,** selecione **Reward Gateway** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Reward Gateway na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-reward-gateway"></a>Configurar o fornecimento automático de utilizadores ao Reward Gateway  

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Reward Gateway com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar um único sinal de entrada baseado em SAML para o Reward Gateway, seguindo as instruções fornecidas no [tutorial de assinatura Single Gateway Reward](reward-gateway-tutorial.md)Gateway . O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-reward-gateway-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para Reward Gateway em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Reward Gateway**.

    ![O link Reward Gateway na lista de Aplicações](common/all-applications.png)

3. Selecione o **separador Provisioning.**

    ![Separador de provisionamento](common/provisioning.png)

4. Desa ajuste o **modo de provisionamento** para **automático**.

    ![Separador de provisionamento](common/provisioning-automatic.png)

5. Sob a secção **credenciais de administração,** insira os valores **DE URL SCIM (v2)** e **OAuth Bearer Token** recuperados anteriormente em URL de **inquilino** e **Token Secreto,** respectivamente. Clique em **Testar A Ligação** para garantir que o Azure AD pode ligar-se ao gateway de recompensas. Se a ligação falhar, certifique-se de que a sua conta de gateway de recompensas tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Reward Gateway**.

    ![Consola de administração Reward Gateway](media/reward-gateway-provisioning-tutorial/user-mappings.png)

9. Reveja os atributos do utilizador que são sincronizados de AZure AD a Reward Gateway na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador no Reward Gateway para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Consola de administração Reward Gateway](media/reward-gateway-provisioning-tutorial/user-attributes.png)

10. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de prestação de Ad Azure para Gateway de Recompensa, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de Provisionamento Toggled On](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja prestar ao Reward Gateway, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para a provisão, clique em **Guardar**.

    ![Configuração de provisionamento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD Azure no Reward Gateway.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

A Reward Gateway não suporta o fornecimento de grupos atualmente.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

[Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
