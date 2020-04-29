---
title: 'Tutorial: Configure Reward Gateway para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer automaticamente e desfornecer contas de utilizador para Reward Gateway.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 928d48907e43de5e65ca5604ff878bfb83d5e95b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77061008"
---
# <a name="tutorial-configure-reward-gateway-for-automatic-user-provisioning"></a>Tutorial: Configure Reward Gateway para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Reward Gateway e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer e desfornecer automaticamente utilizadores e/ou grupos para recompensar gateway.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
>
> Este conector encontra-se atualmente em pré-visualização pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* Um [inquilino reward Gateway.](https://www.rewardgateway.com/)
* Uma conta de utilizador em Reward Gateway com permissões de Administrador.

## <a name="assigning-users-to-reward-gateway"></a>Atribuir utilizadores ao Reward Gateway 

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Reward Gateway. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao Reward Gateway seguindo as instruções em [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md).


## <a name="important-tips-for-assigning-users-to-reward-gateway"></a>Dicas importantes para atribuir utilizadores ao Reward Gateway 

* Recomenda-se que um único utilizador da AD Azure seja atribuído ao Reward Gateway para testar a configuração automática de fornecimento do utilizador. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador ao Reward Gateway, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="setup-reward-gateway--for-provisioning"></a>Configurar Gateway de Recompensa para o provisionamento
Antes de configurar o Reward Gateway para o fornecimento automático de utilizadores com o Azure AD, terá de ativar o fornecimento de SCIM no Reward Gateway.

1. Inscreva-se na consola [Reward Gateway Admin](https://rewardgateway.photoshelter.com/login/). Clique em **Integrações**.

    ![Consola de administrador a gateway recompensa](media/reward-gateway-provisioning-tutorial/image00.png)

2.  Selecione **A Minha Integração.**

    ![Consola de administrador a gateway recompensa](media/reward-gateway-provisioning-tutorial/image001.png)

3.  Copie os valores do **URL SCIM (v2)** e **o Token OAuth Bearer**. Estes valores serão inseridos no campo URL do Tenant e no campo Secret Token no separador de fornecimento da sua aplicação Reward Gateway no portal Azure.

    ![Consola de administrador a gateway recompensa](media/reward-gateway-provisioning-tutorial/image03.png)

## <a name="add-reward-gateway-from-the-gallery"></a>Adicione Reward Gateway da galeria

Para configurar o Reward Gateway para o fornecimento automático de utilizadores com o Azure AD, é necessário adicionar o Reward Gateway da galeria de aplicações Azure AD à sua lista de aplicações saaS geridas.

**Para adicionar Reward Gateway da galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Reward Gateway,** selecione **Reward Gateway** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Gateway recompensa na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-reward-gateway"></a>Configurar o fornecimento automático de utilizadores para Reward Gateway  

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Reward Gateway com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sign-on baseado em SAML para Reward Gateway, seguindo as instruções fornecidas no tutorial de [inscrição single reward Gateway](reward-gateway-tutorial.md). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-reward-gateway-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para Reward Gateway em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Reward Gateway**.

    ![O link Reward Gateway na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. De acordo com a secção **de Credenciais de Administrador,** insere os valores **de URL SCIM (v2)** e **OAuth Bearer Token** recuperados anteriormente em **URL** de Inquilino e **Token Secreto,** respectivamente. Clique em **Ligação de Teste** para garantir que o Azure AD pode ligar-se ao gateway de recompensa. Se a ligação falhar, certifique-se de que a sua conta de gateway de recompensa tem permissões de administrador e tente novamente.

    ![URL do inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Reward Gateway**.

    ![Consola de administrador a gateway recompensa](media/reward-gateway-provisioning-tutorial/user-mappings.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Reward Gateway na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no Reward Gateway para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Consola de administrador a gateway recompensa](media/reward-gateway-provisioning-tutorial/user-attributes.png)

10. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de provisionamento de AD Azure para Gateway reward, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja fornecer ao Reward Gateway, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de Anúncios Azure no Reward Gateway.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="connector-limitations"></a>Limitações do conector

A Reward Gateway não suporta o fornecimento de grupos atualmente.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

[Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
