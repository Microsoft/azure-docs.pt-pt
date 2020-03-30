---
title: 'Tutorial: Configure Dynamic Signal para o fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer automaticamente e desfornecer contas de utilizador ao Dynamic Signal.
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
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 2ec91d42dff8f3a1fc4b036aa1c3ec77faf6a0fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058047"
---
# <a name="tutorial-configure-dynamic-signal-for-automatic-user-provisioning"></a>Tutorial: Configure O Sinal Dinâmico para o fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Dynamic Signal e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer automaticamente e desfornecer utilizadores e/ou grupos para o Dynamic Signal.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD Azure
* [Um inquilino de Sinal Dinâmico](https://dynamicsignal.com/)
* Uma conta de utilizador em Dynamic Signal com permissões de Administrador.

## <a name="add-dynamic-signal-from-the-gallery"></a>Adicione o Sinal Dinâmico da galeria

Antes de configurar o Dynamic Signal para o fornecimento automático de utilizadores com a AD Azure, é necessário adicionar o Dynamic Signal da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar O Sinal Dinâmico da galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza o **Dynamic Signal,** selecione **Dynamic Signal** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Sinal dinâmico na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-dynamic-signal"></a>Atribuir utilizadores ao Dynamic Signal

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Sinal Dinâmico. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao Dynamic Signal seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dynamic-signal"></a>Dicas importantes para atribuir utilizadores ao Dynamic Signal

* Recomenda-se que um único utilizador da AD Azure seja atribuído ao Dynamic Signal para testar a configuração automática de fornecimento do utilizador. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador ao Dynamic Signal, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="configuring-automatic-user-provisioning-to-dynamic-signal"></a>Configurar o fornecimento automático de utilizadores ao Sinal Dinâmico 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Sinal Dinâmico com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para o Sinal Dinâmico, seguindo as instruções fornecidas no tutorial de [sinal único do Sinal Dinâmico](dynamicsignal-tutorial.md). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-dynamic-signal-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o Sinal Dinâmico em Azure AD:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Dynamic Signal**.

    ![O link De Sinal Dinâmico na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. De acordo com a secção **de Credenciais de Administrador,** insera o **URL** do Inquilino e o **Token Secreto** da conta do seu Sinal Dinâmico, conforme descrito no Passo 6.

6. Na consola de administração Dynamic Signal, navegue para **admin > Advanced > API**.

    ![Fornecimento dinâmico de sinais](./media/dynamic-signal-provisioning-tutorial/secret-token-1.png)

    Copie o **URL DaCim API** para **o URL do Inquilino**. Clique em **Generate New Token** para gerar um **Token Bearer** e copiar o valor para Secret **Token**.

    ![Fornecimento dinâmico de sinais](./media/dynamic-signal-provisioning-tutorial/secret-token-2.png)

7. Ao povoar os campos mostrados no Passo 5, clique em **Ligação** de Teste para garantir que o Azure AD pode ligar-se ao Sinal Dinâmico. Se a ligação falhar, certifique-se de que a sua conta Dynamic Signal tem permissões de Administrador e tente novamente.

    ![URL do inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

8. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Dynamic Signal**.

    ![Mapeamento dinâmico do utilizador do sinal](media/dynamic-signal-provisioning-tutorial/user-mappings.png)

11. Reveja os atributos do utilizador que são sincronizados de Azure AD para Dynamic Signal na secção de Mapeamento do **Atributo.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas do utilizador no Dynamic Signal para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos dinâmicos do utilizador do sinal](media/dynamic-signal-provisioning-tutorial/user-mapping-attributes.png)

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento de AD Azure para o Sinal Dinâmico, altere o Estado de **Provisionamento** para **Ligado** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja fornecer ao Dynamic Signal escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure no Dynamic Signal.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="connector-limitations"></a>Limitações do conector

* O Dynamic Signal não suporta a eliminação permanente do utilizador a partir de Azure AD. Para eliminar um utilizador permanentemente no Dynamic Signal, a operação tem de ser efetuada através da consola de administração Dynamic Signal UI. 
* O Dynamic Signal não apoia atualmente grupos.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

