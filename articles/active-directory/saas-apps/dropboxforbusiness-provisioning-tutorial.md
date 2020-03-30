---
title: 'Tutorial: Configure Dropbox para Negócios para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo do Azure para fornecer e desfornecer automaticamente contas de utilizadores ao Dropbox for Business.
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
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 3acc2c271e590bddb13aaa01498f404da4340036
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058457"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Tutorial: Configure Dropbox para Negócios para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Dropbox para o Business e o Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecer e desfornecer automaticamente utilizadores e/ou grupos para o Dropbox for Business.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD Azure
* [Uma dropbox para inquilino de negócios](https://www.dropbox.com/business/pricing)
* Uma conta de utilizador no Dropbox para o Negócios com permissões de Administrador.

## <a name="add-dropbox-for-business-from-the-gallery"></a>Adicione dropbox para negócios a partir da galeria

Antes de configurar o Dropbox para o Negócio para o fornecimento automático de utilizadores com a AD Azure, tem de adicionar o Dropbox para negócios da galeria de aplicações Azure AD à sua lista de aplicações saaS geridas.

**Para adicionar dropbox para negócios a partir da galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **o Dropbox para negócios,** selecione **Dropbox para Negócios** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Dropbox para Negócios na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Atribuir utilizadores ao Dropbox para negócios

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Dropbox para negócios. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao Dropbox for Business seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Dicas importantes para atribuir utilizadores ao Dropbox para negócios

* Recomenda-se que um único utilizador da AD Azure seja atribuído ao Dropbox para o Negócios para testar a configuração automática de fornecimento de utilizadores. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador ao Dropbox for Business, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Configurar o fornecimento automático de utilizadores ao Dropbox para negócios 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no Dropbox para Negócios com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sign-on baseado em SAML para o Dropbox for Business, seguindo as instruções fornecidas no tutorial de [inscrição única dropbox para negócios](dropboxforbusiness-tutorial.md). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para dropbox para negócios em Azure AD:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Dropbox para Negócios**.

    ![O dropbox para ligação ao negócio na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na secção **credenciais de administrador,** clique **em Autorizar**. Abre um diálogo de login dropbox para negócios numa nova janela do navegador.

    ![Aprovisionamento ](common/provisioning-oauth.png)

6. No **Sign-in do Dropbox para o Negócios para ligar com** o diálogo Azure AD, iniciar sessão no seu Dropbox para inquilino de negócios e verificar a sua identidade.

    ![Dropbox para entrada de negócios](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. Ao completar os passos 5 e 6, clique em **Test Connection** para garantir que o Azure AD pode ligar-se ao Dropbox para negócios. Se a ligação falhar, certifique-se de que a sua conta Dropbox para Negócios tem permissões de Administrador e tente novamente.

    ![Certificado de](common/provisioning-testconnection-oauth.png)

8. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Dropbox**.

    ![Mapeamento de utilizador de caixa de gota](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. Reveja os atributos do utilizador que são sincronizados de Azure AD para Dropbox na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no Dropbox para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos de utilizador de caixa de lançamento](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Dropbox**.

    ![Mapeamentos de grupo dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. Reveja os atributos do grupo que são sincronizados de Azure AD para Dropbox na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar os grupos no Dropbox para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos do Grupo Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para ativar o serviço de provisionamento de AD Azure para o Dropbox, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

16. Defina os utilizadores e/ou grupos que deseja fornecer ao Dropbox, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

17. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividadede provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure no Dropbox.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="connector-limitations"></a>Limitações do conector
 
* O Dropbox não suporta a suspensão dos utilizadores convidados. Se um utilizador convidado for suspenso, esse utilizador será eliminado.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

