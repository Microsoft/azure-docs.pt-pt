---
title: 'Tutorial: Configure Consola de Administrador netskope para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo do Azure para fornecer e desfornecer automaticamente contas de utilizadores à Consola de Administradornetskope.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: e99f0e0f-28d0-43c6-a52b-df873fb9d551
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: eaee8e3305572d696e52c3879be2e2b9924bc93f
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77061298"
---
# <a name="tutorial-configure-netskope-administrator-console-for-automatic-user-provisioning"></a>Tutorial: Configure Consola de Administrador netskope para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados na Consola de Administrador netskope e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer e desfornecer automaticamente utilizadores e/ou grupos à Consola de Administrador netskope.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino de consola de administrador netskope](https://www.netskope.com/)
* Uma conta de utilizador na Consola de Administrador netskope com permissões de administrador.

## <a name="assigning-users-to-netskope-administrator-console"></a>Atribuir utilizadores à Consola de Administrador netskope

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso à Consola de Administradornetskope. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos à Consola de Administrador netskope seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-administrator-console"></a>Dicas importantes para atribuir utilizadores à Consola de Administrador netskope

* Recomenda-se que um único utilizador da AD Azure seja atribuído à Consola de Administrador netskope para testar a configuração automática de fornecimento do utilizador. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um utilizador à Consola de Administrador netskope, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="set-up-netskope-administrator-console-for-provisioning"></a>Configurar a Consola de Administrador netskope para o fornecimento

1. Inscreva-se na consola de [administrador netskope](https://netskope.goskope.com/). Navegue para **Casa > Configurações**.

    ![Consola de administrador netskope](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  Navegar para **ferramentas.** No menu **Tools,** navegue para **Ferramentas de Diretório > INTEGRAÇÃO SCIM**.

    ![Ferramentas de consola de administrador netskope](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Consola administradora netskope adicionar SCIM](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. Desloque-se para baixo e clique no botão **Adicionar Token.** Na caixa de diálogo **Add OAuth Client Name** forneça um NOME DE **CLIENTE** e clique no botão **Guardar.**

    ![Consola de administrador netskope adicionar token](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Nome CLient da consola do administrador netskope](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  Copie o URL do **Servidor SCIM** e o **TOKEN**. Estes valores serão inscritos nos campos URL do Tenant e Secret Token, respectivamente, no separador de fornecimento da sua aplicação de consola de administrador netskope no portal Azure.

    ![Consola administradora netskope criar token](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-administrator-console-from-the-gallery"></a>Adicione a consola de administrador netskope da galeria

Antes de configurar a Consola de Administrador netskope para o fornecimento automático de utilizadores com a AD Azure, tem de adicionar a Consola de Administrador netskope da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar a Consola de Administrador netskope da galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza a Consola de **Administrador netskope,** selecione **A Consola de Administrador netskope** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Consola administradora netskope na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-administrator-console"></a>Configurar o fornecimento automático de utilizadores à Consola de Administrador netskope 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos na Consola de Administrador netskope com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para a Consola de Administrador netskope seguindo as instruções fornecidas no tutorial de assinatura single do [administrador netskope](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se complementem.

> [!NOTE]
> Para saber mais sobre o ponto final do SCIM da Consola de Administrador netskope, consulte [isto](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon).

### <a name="to-configure-automatic-user-provisioning-for-netskope-administrator-console-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para a consola de administrador netskope em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione Consola de **Administrador netskope**.

    ![O link de consola do administrador netskope na lista de aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na secção **credenciais de administrador,** insere o valor URL do **Servidor SCIM** recuperado anteriormente no URL do **Inquilino**. Insera o valor **TOKEN** recuperado anteriormente em **Secret Token**. Clique em **Ligação de Teste** para garantir que o Azure AD pode ligar-se à Consola do Administrador netskope. Se a ligação falhar, certifique-se de que a sua conta de Consola de Administrador netskope tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users para a Consola de Administrador netskope**.

    ![Mapeamento de utilizadores de consola de administrador netskope](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para A Consola de Administrador netskope na secção **Demapeamento do Atributo.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador na Consola de Administrador netskope para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos de utilizador de consola de administrador netskope](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups para a Consola de Administrador netskope**.

    ![Mapeamentos do grupo de consolas administradorde Netskope](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. Reveja os atributos do grupo que são sincronizados de Azure AD para A Consola de Administrador netskope na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar os grupos na Consola de Administrador netskope para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos do Grupo de Consolas administradora netskope](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento de AD Azure para a Consola de Administrador netskope, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que gostaria de fornecer à Consola de Administrador netskope, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividadede provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de Anúncios Azure na Consola de Administrador de Netskope.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

