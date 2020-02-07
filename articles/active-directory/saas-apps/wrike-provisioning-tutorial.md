---
title: 'Tutorial: Configure Wrike para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer e desfornecer automaticamente contas de utilizadores à Wrike.
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
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: 5dd4f5ac6152c22b5e2a84ecc0774672bcd5590b
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064197"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>Tutorial: Configure Wrike para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos que executa em Wrike e Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer e desfornecer automaticamente utilizadores ou grupos para Wrike.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do serviço de fornecimento de utilizadores DaAzure AD. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o [fornecimento e o desprovisionamento de utilizadores automate para aplicações de software como um serviço (SaaS) com o Diretório Ativo Azure.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os termos suplementares de utilização para as pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino de Wrike](https://www.wrike.com/price/)
* Uma conta de utilizador em Wrike com permissões de administração

## <a name="assign-users-to-wrike"></a>Atribuir utilizadores a Wrike
O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores ou grupos que foram atribuídos a uma aplicação em Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, decida quais os utilizadores ou grupos em AD Azure que precisam de acesso ao Wrike. Em seguida, atribua estes utilizadores ou grupos a Wrike seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>Dicas importantes para atribuir utilizadores a Wrike

* Recomendamos que atribua um único utilizador da AD Azure à Wrike para testar a configuração automática de fornecimento de utilizadores. Usuários ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um utilizador ao Wrike, deve selecionar qualquer função específica de aplicação válida (se disponível) na caixa de diálogo de atribuição. Os utilizadores com a função de Acesso Predefinido estão excluídos do fornecimento.

## <a name="set-up-wrike-for-provisioning"></a>Configurar Wrike para o provisionamento

Antes de configurar o Wrike para o fornecimento automático de utilizadores com a AD Azure, tem de ativar o fornecimento do Sistema de Gestão de Identidade de Domínio Cruzado (SCIM) em Wrike.

1. Inscreva-se na sua [consola de administração Wrike.](https://www.Wrike.com/login/) Vá para a sua identificação do inquilino. Selecione **Apps & Integrações.**

    ![Apps & Integrações](media/Wrike-provisioning-tutorial/admin.png)

2.  Vá ao **Azure AD** e selecione-o.

    ![Azure AD](media/Wrike-provisioning-tutorial/Capture01.png)

3.  Selecione SCIM. Copiar o **URL base**.

    ![Base URL](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. Selecione **API** > **Azure SCIM**.

    ![Azure SCIM](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  Abre-se um pop-up. Introduza a mesma senha que criou anteriormente para criar uma conta.

    ![Wrike Criar símbolo](media/Wrike-provisioning-tutorial/password.png)

6.  Copie o **Símbolo Secreto**e cole-o em Azure AD. Selecione **Guardar** para terminar a configuração de provisionamento em Wrike.

    ![Ficha de acesso permanente](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>Adicione Wrike da galeria

Antes de configurar wrike para o fornecimento automático de utilizadores com AD Azure, adicione Wrike da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

Para adicionar Wrike da galeria de aplicações da AD Azure, siga estes passos.

1. No [portal Azure,](https://portal.azure.com)no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Wrike**, selecione **Wrike** no painel de resultados e, em seguida, selecione **Adicionar** para adicionar a aplicação.

    ![Wrike na lista de resultados](common/search-new-app.png)


## <a name="configure-automatic-user-provisioning-to-wrike"></a>Configure o fornecimento automático de utilizadores a Wrike 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores ou grupos em Wrike com base em atribuições de utilizador ou grupo em Azure AD.

> [!TIP]
> Para ativar o único sinal baseado em SAML para Wrike, siga as instruções no tutorial de [inscrição única de Wrike](wrike-tutorial.md). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se complementem.

### <a name="configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>Configure o fornecimento automático de utilizadores para Wrike em Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais** > **Todas as aplicações**.

    ![Todas as aplicações](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Wrike**.

    ![O link Wrike na lista de aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Modo de provisionamento definido para automático](common/provisioning-automatic.png)

5. No âmbito da secção de Credenciais de Administrador, insere o **URL base** e os valores **acessos de acesso permanente** recuperados anteriormente em URL de **Inquilino** e **Token Secreto,** respectivamente. Selecione **Ligação** de Teste para garantir que o Azure AD pode ligar-se a Wrike. Se a ligação falhar, certifique-se de que a sua conta Wrike tem permissões de administração e tente novamente.

    ![URL do inquilino + símbolo](common/provisioning-testconnection-tenanturltoken.png)

7. Na caixa de **e-mail de notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento. Selecione a **notificação de e-mail enviar uma notificação de e-mail quando ocorrer uma falha** verificar a caixa.

    ![E-mail de notificação](common/provisioning-notification-email.png)

8. Selecione **Guardar**.

9. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Wrike**.

    ![Mapeamento de utilizadores wrike](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. Reveja os atributos do utilizador que são sincronizados de Azure AD para Wrike na secção **DeMapeamentos de Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Wrike para operações de atualização. Selecione **Guardar** para cometer quaisquer alterações.

    ![Atributos de utilizador wrike](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. Para configurar filtros de deteção, siga as instruções no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para ativar o serviço de provisionamento de AD Azure para wrike, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estado de provisionamento alternado](common/provisioning-toggle-on.png)

13. Defina os utilizadores ou grupos que pretende fornecer ao Wrike, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para fornecer, selecione **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a executar do que as sincronizações subsequentes. Para obter mais informações sobre o tempo que os utilizadores ou grupos demoram a fornecer, veja [quanto tempo demorará a fornecer utilizadores?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

Pode utilizar a secção **Current Status** para monitorizar o progresso e seguir ligações ao seu relatório de atividade de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure em Wrike. Para mais informações, [consulte Verifique o estado do fornecimento do utilizador](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
