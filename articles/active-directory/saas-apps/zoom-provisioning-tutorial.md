---
title: 'Tutorial: Configure Zoom para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer automaticamente e desfornecer contas de utilizador ao Zoom.
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
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: cd832a9dfec4680222d2c985f49aba499a56aaac
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062787"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Tutorial: Configure zoom para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Zoom e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer automaticamente e desfornecer utilizadores e/ou grupos para zoom.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino zoom](https://zoom.us/pricing)
* Uma conta de utilizador em Zoom com permissões de Administrador

## <a name="add-zoom-from-the-gallery"></a>Adicione Zoom da galeria

Antes de configurar o Zoom para o fornecimento automático de utilizadores com o Azure AD, tem de adicionar zoom da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar Zoom da galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Zoom,** **selecione Zoom** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Zoom na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-zoom"></a>Atribuir utilizadores ao Zoom

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Zoom. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao Zoom seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zoom"></a>Dicas importantes para atribuir utilizadores ao Zoom

* Recomenda-se que um único utilizador da AD Azure seja designado para o Zoom para testar a configuração automática de fornecimento do utilizador. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um utilizador ao Zoom, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="configure-automatic-user-provisioning-to-zoom"></a>Configure o fornecimento automático de utilizadores ao Zoom 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores ou grupos em Zoom com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para zoom, seguindo as instruções fornecidas no tutorial de [inscrição individual zoom](zoom-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

### <a name="configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Configure o fornecimento automático de utilizadores para Zoom em Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, **selecione Zoom**.

    ![O link Zoom na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na secção **credenciais de administrador,** insira `https://api.zoom.us/scim` no URL do **Arrendatário**. Para recuperar o **Token Secreto** da sua conta Zoom, siga a passagem como descrito no Passo 6.

6. Inscreva-se na consola [de administrador de zoom](https://zoom.us/signin). Navegue para **Advanced > Zoom para Desenvolvedores** no painel de navegação esquerdo.

    ![Integrações de Zoom](media/zoom-provisioning-tutorial/zoom01.png)

    Navegue para **gerir** no canto superior direito da página. 

    ![Instalação de zoom](media/zoom-provisioning-tutorial/zoom02.png)

    Navegue para a sua aplicação Azure AD criada. 
    
    ![Zoom App](media/zoom-provisioning-tutorial/zoom03.png)

    Selecione Credenciais de **aplicação** no painel de navegação esquerdo.

    ![Zoom App](media/zoom-provisioning-tutorial/zoom04.png)

    Recupere o valor JWT Token mostrado abaixo e insera-o no campo **Secret Token** em Azure AD. Se precisar de um novo token sem validade, terá de reconfigurar o tempo de validade que irá gerar um novo token. 

    ![Instalação de zoom](media/zoom-provisioning-tutorial/zoom05.png)

7. Ao povoar os campos mostrados no Passo 5, clique em **Test Connection** para garantir que o Azure AD pode ligar-se ao Zoom. Se a ligação falhar, certifique-se de que a sua conta Zoom tem permissões de Administrador e tente novamente.

    ![Certificado de](common/provisioning-testconnection-tenanturltoken.png)

8. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Zoom**.

    ![Mapeamento de utilizador de zoom](media/zoom-provisioning-tutorial/zoom-user-mapping.png)

11. Reveja os atributos do utilizador que são sincronizados de Azure AD para Zoom na secção de Mapeamento do **Atributo.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas do utilizador no Zoom para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.
    
     ![Mapeamento de utilizador de zoom](media/zoom-provisioning-tutorial/zoom-user-attributes.png)

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento de AD Azure para zoom, altere o Estado de **Provisionamento** para **Ligado** na secção **Definições.**
    
    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja fornecer ao Zoom, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure no Zoom.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="connector-limitations"></a>Limitações do conector

* O Zoom não apoia o provisionamento de grupos.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
