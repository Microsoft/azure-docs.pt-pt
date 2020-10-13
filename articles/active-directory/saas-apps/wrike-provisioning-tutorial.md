---
title: 'Tutorial: Configure Wrike para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Aprenda a configurar o Azure Ative Directory para provisá-lo e desprovisionar automaticamente as contas dos utilizadores à Wrike.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: e3ec25504decca0747c8a853bb88ab3e7a10966a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88546326"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>Tutorial: Configure Wrike para o fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos que executa no Wrike e no Azure Ative Directory (Azure AD) para configurar a Azure AD para provisão automática e desprovisionar utilizadores ou grupos para Wrike.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do serviço de fornecimento de utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [o fornecimento e desprovisionamento de utilizadores da Automamate para aplicações software-as-a-service (SaaS) com diretório Azure Ative.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD AZure
* [Um inquilino wrike](https://www.wrike.com/price/)
* Uma conta de utilizador em Wrike com permissões de administração

## <a name="assign-users-to-wrike"></a>Atribuir utilizadores à Wrike
O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, decida quais os utilizadores ou grupos em Azure AD que precisam de acesso à Wrike. Em seguida, atribua estes utilizadores ou grupos à Wrike seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>Dicas importantes para atribuir utilizadores à Wrike

* Recomendamos que atribua um único utilizador Azure AD à Wrike para testar a configuração automática de provisionamento do utilizador. Utilizadores ou grupos adicionais podem ser atribuídos mais tarde.

* Quando atribuir um utilizador à Wrike, deve selecionar qualquer função específica de aplicação válida (se disponível) na caixa de diálogo de atribuição. Os utilizadores com a função Acesso Predefinido estão excluídos do provisionamento.

## <a name="set-up-wrike-for-provisioning"></a>Criar Wrike para provisionamento

Antes de configurar a Wrike para o fornecimento automático de utilizadores com Azure AD, tem de ativar o fornecimento do Sistema de Gestão de Identidade de Domínio Cruzado (SCIM) na Wrike.

1. Inscreva-se na sua [consola de administração Wrike.](https://www.Wrike.com/login/) Vá para a sua identificação de inquilino. Selecione **Apps & Integrações**.

    ![Apps & Integrações](media/Wrike-provisioning-tutorial/admin.png)

2.  Vá ao **Azure AD** e selecione-o.

    ![Azure AD](media/Wrike-provisioning-tutorial/Capture01.png)

3.  Selecione SCIM. Copie o **URL base**.

    ![URL Base](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. Selecione **API**  >  **Azure SCIM**.

    ![Azure SCIM](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  Abre-se um pop-up. Introduza a mesma senha que criou anteriormente para criar uma conta.

    ![Wrike Criar ficha](media/Wrike-provisioning-tutorial/password.png)

6.  Copie o **Token Secreto**e cole-o em Azure AD. **Selecione Guardar** para terminar a configuração de provisionamento no Wrike.

    ![Ficha de acesso permanente](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>Adicione Wrike da galeria

Antes de configurar a Wrike para o fornecimento automático de utilizadores com a Azure AD, adicione a Wrike da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

Para adicionar Wrike da galeria de aplicações AZure AD, siga estes passos.

1. No [portal Azure,](https://portal.azure.com)no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Wrike,** selecione **Wrike** no painel de resultados e, em seguida, selecione **Adicionar** para adicionar a aplicação.

    ![Wrike na lista de resultados](common/search-new-app.png)


## <a name="configure-automatic-user-provisioning-to-wrike"></a>Configure o fornecimento automático de utilizadores à Wrike 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores ou grupos em Wrike com base em atribuições de utilizador ou grupo em Azure AD.

> [!TIP]
> Para ativar o único sinal de SEM para wrike, siga as instruções no [tutorial de assinatura único wrike](wrike-tutorial.md). O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se complementem.

### <a name="configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>Configure o fornecimento automático de utilizadores para wrike em Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais**  >  **Todas as aplicações**.

    ![Todas as aplicações](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Wrike**.

    ![O link Wrike na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Separador Aprovisionamento](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Modo de Provisionamento definido para Automático](common/provisioning-automatic.png)

5. Sob a secção Credenciais de Administração, insira os valores **de símbolo de** base URL e de acesso **permanente** recuperados anteriormente em URL de **Inquilino** e **Token Secreto,** respectivamente. Selecione **a Ligação de Teste** para garantir que o Azure AD pode ligar-se ao Wrike. Se a ligação falhar, certifique-se de que a sua conta Wrike tem permissões de administração e tente novamente.

    ![USA do inquilino + token](common/provisioning-testconnection-tenanturltoken.png)

7. Na caixa de **e-mail de notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento. Selecione a **notificação Enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![E-mail de notificação](common/provisioning-notification-email.png)

8. Selecione **Guardar**.

9. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Wrike**.

    ![Mapeamentos de usuário wrike](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. Reveja os atributos do utilizador que são sincronizados de Azure AD a Wrike na secção **De Mapeamentos de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador em Wrike para operações de atualização. **Selecione Guardar** para escoar quaisquer alterações.

    ![Wrike atributos do utilizador](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. Para configurar filtros de deteção, siga as instruções no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para ativar o serviço de prestação de Ad Azure para a Wrike, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de Provisionamento alternado](common/provisioning-toggle-on.png)

13. Defina os utilizadores ou grupos que pretende prever à Wrike, escolhendo os valores pretendidos no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para a provisão, **selecione Save**.

    ![Configuração de provisão de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser efetua do que as sincronizações subsequentes. Para obter mais informações sobre o tempo que os utilizadores ou grupos demoram a providenciar, consulte [quanto tempo demorará a providenciar aos utilizadores?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

Pode utilizar a secção **Estado Presente** para monitorizar o progresso e seguir links para o seu relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de Ad AD Azure em Wrike. Para obter mais informações, [consulte verifique o estado do fornecimento do utilizador](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os registos de provisionamento da AD Azure, consulte [reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento da conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
