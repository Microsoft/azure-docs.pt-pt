---
title: 'Tutorial: Configure De visita para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer e desfornecer automaticamente contas de utilizadores para visitar.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 73cc1a58689db7902843f222aa4874a5e188be44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063174"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>Tutorial: Configure de visita para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos que executa no Visitly and Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer e desfornecer automaticamente utilizadores ou grupos para visitar.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do serviço de fornecimento de utilizadores DaAzure AD. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o [fornecimento e o desprovisionamento de utilizadores automate para aplicações de software como um serviço (SaaS) com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os termos suplementares de utilização para as pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD Azure
* [Um inquilino visitante](https://www.visitly.io/pricing/)
* Uma conta de utilizador em Visitly com permissões de administração

## <a name="assign-users-to-visitly"></a>Atribuir utilizadores a Visitly 

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores ou grupos que foram atribuídos a uma aplicação em Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, decida quais os utilizadores ou grupos em AD Azure que precisam de ter acesso à Visitly. Em seguida, atribua estes utilizadores ou grupos à Visitly seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>Dicas importantes para atribuir utilizadores à Visitly 

* Recomendamos que designe um único utilizador da AD Azure à Visitly para testar a configuração automática de fornecimento do utilizador. Utilizadores ou grupos adicionais podem ser atribuídos mais tarde.

* Quando designar um utilizador para a Visitly, deve selecionar qualquer função específica de aplicação válida (se disponível) na caixa de diálogo de atribuição. Os utilizadores com a função de Acesso Predefinido estão excluídos do fornecimento.

## <a name="set-up-visitly-for-provisioning"></a>Configurar visitativamente para o provisionamento

Antes de configurar de visita para o fornecimento automático de utilizadores com a AD Azure, é necessário ativar o fornecimento do Sistema de Gestão de Identidade de Domínio Transversal (SCIM) na Visitly.

1. Inscreva-se na [Visitly](https://app.visitly.io/login). Selecione **Integrações** > **Sincronização anfitriã .**

    ![Sincronização do hospedeiro](media/Visitly-provisioning-tutorial/login.png)

2. Selecione a secção **Azure AD.**

    ![Secção Azure AD](media/Visitly-provisioning-tutorial/integration.png)

3. Copiar a **Tecla API**. Estes valores são inseridos na caixa **Token Secreta** no separador de **provisionamento** da sua aplicação Visitly no portal Azure.

    ![Chave da API](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>Adicione visitly a partir da galeria

Para configurar de visita para o fornecimento automático de utilizadores com a AD Azure, adicione visitly da galeria de aplicações Azure AD à sua lista de aplicações saaS geridas.

Para adicionar Visitly a partir da galeria de aplicações da AD Azure, siga estes passos.

1. No [portal Azure,](https://portal.azure.com)no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Visitly**, selecione **Visitly** no painel de resultados e, em seguida, selecione **Adicionar** para adicionar a aplicação.

    ![Visita na lista de resultados](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-visitly"></a>Configure o fornecimento automático de utilizadores à Visitly 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores ou grupos em Visitly com base em atribuições de utilizador ou grupo em Azure AD.

> [!TIP]
> Para ativar o único sign-on baseado em SAML para o Visitly, siga as instruções no tutorial de [inscrição única da Visitly](Visitly-tutorial.md). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se complementem.

### <a name="configure-automatic-user-provisioning-for-visitly-in-azure-ad"></a>Configure o fornecimento automático de utilizadores para visita em Azure AD

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** > **Todas as aplicações**.

    ![Todas as aplicações](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Visitly**.

    ![O link Visitly na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Modo de provisionamento definido para automático](common/provisioning-automatic.png)

5. De acordo com a secção `https://api.visitly.io/v1/usersync/SCIM` de Credenciais de Administrador, insera os valores chave e **Chave API** recuperados anteriormente em URL de **Inquilino** e **Token Secreto,** respectivamente. Selecione **Ligação** de Teste para garantir que o Azure AD pode ligar-se à Visitly. Se a ligação falhar, certifique-se de que a sua conta Visitly tem permissões de administração e tente novamente.

    ![URL do inquilino + símbolo](common/provisioning-testconnection-tenanturltoken.png)

6. Na caixa de **e-mail de notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento. Selecione a **notificação de e-mail enviar uma notificação de e-mail quando ocorrer uma falha** verificar a caixa.

    ![E-mail de notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users para visitar .**

    ![Mapeamento de utilizadores de visita](media/visitly-provisioning-tutorial/usermapping.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Visitly na secção **DeMapeamentos de Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas do utilizador em Visitly para operações de atualização. Selecione **Guardar** para cometer quaisquer alterações.

    ![Atributos de utilizador de visita](media/visitly-provisioning-tutorial/userattribute.png)

10. Para configurar filtros de deteção, siga as instruções no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de provisionamento de AD Azure para visita, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estado de provisionamento alternado](common/provisioning-toggle-on.png)

12. Defina os utilizadores ou grupos que pretende fornecer de visita, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para fornecer, selecione **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a executar do que as sincronizações subsequentes. Para obter mais informações sobre o tempo que os utilizadores ou grupos demoram a fornecer, veja [quanto tempo demorará a fornecer utilizadores?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

Pode utilizar a secção **Estado Atual** para monitorizar o progresso e seguir ligações ao seu relatório de atividade de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento da AD Azure em Visitly. Para mais informações, [consulte Verifique o estado do fornecimento do utilizador](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="connector-limitations"></a>Limitações do conector

A visita não suporta eliminações duras. Tudo é suave apenas apagar.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
