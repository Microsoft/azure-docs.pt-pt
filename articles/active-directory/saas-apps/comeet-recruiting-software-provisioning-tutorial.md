---
title: 'Tutorial: Configure Comeet Software de Recrutamento para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo do Azure para fornecer e desfornecer automaticamente contas de utilizadores ao Comeet Recruiting Software.
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
ms.openlocfilehash: f427fb75cfaeda79b037c327992e4ad482a7e689
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058342"
---
# <a name="tutorial-configure-comeet-recruiting-software-for-automatic-user-provisioning"></a>Tutorial: Configure Comeet Software de Recrutamento para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Comeet Recruiting Software e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer e desfornecer automaticamente utilizadores e/ou grupos para comeet Recruiting Software.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD Azure
* [Um inquilino de Software de Recrutamento Comeet](https://www.comeet.co/)
* Uma conta de utilizador em Comeet Recruiting Software com permissões de Administrador.

## <a name="add-comeet-recruiting-software-from-the-gallery"></a>Adicione software de recrutamento Comeet da galeria

Antes de configurar o Software de Recrutamento Comeet para o fornecimento automático de utilizadores com a AD Azure, precisa de adicionar software de recrutamento Comeet da galeria de aplicações Azure AD à sua lista de aplicações geridas do SaaS.

**Para adicionar software de recrutamento Comeet da galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza o Software de **Recrutamento Comeet,** selecione **Comeet Recruiting Software** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Comeet Recruiting Software na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-comeet-recruiting-software"></a>Atribuir utilizadores ao Software de Recrutamento Comeet

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Software de Recrutamento Comeet. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao Comeet Recruiting Software seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-comeet-recruiting-software"></a>Dicas importantes para atribuir utilizadores ao Software de Recrutamento Comeet

* Recomenda-se que um único utilizador da AD Azure seja atribuído ao Software de Recrutamento Comeet para testar a configuração automática de fornecimento de utilizadores. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador ao Software de Recrutamento Comeet, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="configuring-automatic-user-provisioning-to-comeet-recruiting-software"></a>Configurar o fornecimento automático de utilizadores ao Software de Recrutamento Comeet 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no Software de Recrutamento Comeet com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sign-on baseado em SAML para o Software de Recrutamento Comeet, seguindo as instruções fornecidas no [tutorial de inscrição individual](comeetrecruitingsoftware-tutorial.md)do Software de Recrutamento Comeet . O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-comeet-recruiting-software-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o Software de Recrutamento Comeet em Azure AD:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Comeet Recruiting Software**.

    ![O link comeet recruiting software na lista de aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. De acordo com a secção **de Credenciais de Administrador,** insera o **URL** do Inquilino e o **Token Secreto** da conta do software de recrutamento comeet como descrito no Passo 6.

6. Na [consola de administração comeet Recruiting Software,](https://app.comeet.co/)navegue para **Comeet > Definições > Autenticação > Microsoft Azure,** e copie o **Token Secreto para** o valor da sua empresa para o campo **Secret Token** em Azure AD.

    ![Comeet Recrutamento de Software Provisioning](./media/comeet-recruiting-software-provisioning-tutorial/secret-token-1.png)

7. Ao povoar os campos mostrados no Passo 5, clique em **Test Connection** para garantir que o Azure AD pode ligar-se ao Software de Recrutamento Comeet. Se a ligação falhar, certifique-se de que a sua conta comeet Recruiting Software tem permissões de Administrador e tente novamente.

    ![Certificado de](common/provisioning-testconnection-token.png)

8. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Comeet**.

    ![Comeet Recrutamento De Software Mapeamento de utilizadores](media/comeet-recruiting-software-provisioning-tutorial/user-mappings.png)

11. Reveja os atributos do utilizador que são sincronizados de Azure AD para Comeet Recruiting Software na secção De Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no Software de Recrutamento Comeet para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Comeet Recrutamento Software Group Atributos](media/comeet-recruiting-software-provisioning-tutorial/user-mapping-attributes.png)

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento de AD Azure para o Software de Recrutamento Comeet, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja fornecer ao Comeet Recruiting Software, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de Anúncios Azure no Software de Recrutamento Comeet.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="connector-limitations"></a>Limitações do conector

* O Comeet Recruiting Software não suporta atualmente grupos.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

