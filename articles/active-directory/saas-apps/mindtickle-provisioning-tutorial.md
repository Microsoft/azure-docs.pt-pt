---
title: 'Tutorial: Configure mindTickle para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer e desfornecer automaticamente contas de utilizador à MindTickle.
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
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: 6ae7b6ac9c6feb539e4da3627ebbabd3c5b628fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061243"
---
# <a name="tutorial-configure-mindtickle-for-automatic-user-provisioning"></a>Tutorial: Configure mindTickle para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no MindTickle e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer e desfornecer automaticamente utilizadores e/ou grupos à MindTickle.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD Azure
* [Um inquilino mindTickle](https://www.mindtickle.com/)
* Uma conta de utilizador em MindTickle com permissões de administrador.

## <a name="assigning-users-to-mindtickle"></a>Atribuir utilizadores ao MindTickle

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao MindTickle. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos à MindTickle seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-mindtickle"></a>Dicas importantes para atribuir utilizadores ao MindTickle

* Recomenda-se que um único utilizador da AD Azure seja atribuído à MindTickle para testar a configuração automática de fornecimento do utilizador. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador ao MindTickle, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="setup-mindtickle-for-provisioning"></a>Configurar mindTickle para provisionamento

Antes de configurar o MindTickle para o fornecimento automático de utilizadores com a AD Azure, terá de ativar o fornecimento de SCIM no MindTickle.


1.  Contacte a equipa de [apoio do MindTickle](mailto:help@mindtickle.com) para obter o símbolo JWT necessário para configurar o fornecimento de SCIM.


## <a name="add-mindtickle-from-the-gallery"></a>Adicione MindTickle da galeria

Para configurar o MindTickle para o fornecimento automático de utilizadores com o Azure AD, é necessário adicionar o MindTickle da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar mindTickle na galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **mindTickle,** selecione **MindTickle** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![MindTickle na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-mindtickle"></a>Configurar o fornecimento automático de utilizadores ao MindTickle 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em MindTickle com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sign-on baseado em SAML para mindTickle, seguindo as instruções fornecidas no tutorial de [inscrição MindTickle Single](mindtickle-tutorial.md). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático de utilizadores, embora estas duas funcionalidades se elogiem mutuamente

### <a name="to-configure-automatic-user-provisioning-for-mindtickle-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o MindTickle em Azure AD:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **MindTickle**.

    ![O link MindTickle na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção de `https://admin.mindtickle.com/scim` **Credenciais de Administrador,** insere-se no URL do **Arrendatário**. Insira o valor simbólico **JWT** recuperado anteriormente Em Caixa de Texto Secreta Token, insira o valor do **símbolo JWT** que foi dado pela equipa de suporte MindTickle. Clique em **Ligação** de Teste para garantir que o Azure AD pode ligar-se às minhas Políticas. Se a ligação falhar, certifique-se de que a sua conta MindTickle tem permissões de Administrador e tente novamente.

    ![URL do inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to MindTickle**.

    ![Mapeamento de utilizador mindTickle](media/mindtickle-provisioning-tutorial/usermapping.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para MindTickle na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no MindTickle para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Mapeamento de utilizador mindTickle](media/mindtickle-provisioning-tutorial/userattribute.png)

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento de AD Azure para mindTickle, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja fornecer ao MindTickle, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a executar do que as sincronizações subsequentes. Para obter mais informações sobre quanto tempo demorará os utilizadores e/ou grupos a fornecer, veja [quanto tempo demorará a fornecer aos utilizadores](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users). 

Pode utilizar a secção **Current Status** para monitorizar o progresso e seguir ligações ao seu relatório de atividade de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure no MindTickle. Para mais informações, [consulte Verifique o estado do fornecimento do utilizador](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
