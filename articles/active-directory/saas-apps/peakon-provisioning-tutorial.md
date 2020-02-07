---
title: 'Tutorial: Configure Peakon fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer e desfornecer automaticamente contas de utilizadores a Peakon .
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
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 0a67dc8069ee71305a47bd5d2a724a61cec234a0
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063405"
---
# <a name="tutorial-configure-peakon-for-automatic-user-provisioning"></a>Tutorial: Configure Peakon para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados em Peakon e Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer e desfornecer automaticamente utilizadores e/ou grupos para Peakon.

> [!NOTE]
>  Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .
## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos

* Um inquilino do Azure AD.
* [Um inquilino de Peakon.](https://peakon.com/us/pricing/)
* Uma conta de utilizador em Peakon com permissões de administrador.

## <a name="assigning-users-to-peakon"></a>Atribuir utilizadores a Peakon

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Peakon. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos a Peakon seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-peakon"></a>Dicas importantes para atribuir utilizadores a Peakon 

* Recomenda-se que um único utilizador da AD Azure seja atribuído a Peakon para testar a configuração automática de fornecimento do utilizador. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um utilizador a Peakon, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="set-up-peakon-for-provisioning"></a>Configurar Peakon para o provisionamento

1.  Inscreva-se na consola [De Administração Peakon](https://app.Peakon.com/login). Clique na **Configuração**. 

    ![Consola de Administração Peakon](media/Peakon-provisioning-tutorial/Peakon-admin-configuration.png)

2.  Selecione **Integrações**.
    
    ![Oferta de funcionários peakon](media/Peakon-provisioning-tutorial/Peakon-select-integration.png)

3.  Ativar **o fornecimento de funcionários**.

    ![Oferta de funcionários peakon](media/Peakon-provisioning-tutorial/peakon05.png)

4.  Copie os valores para **SCIM 2.0 URL** e **OAuth Bearer Token**. Estes valores serão inseridos no **campo URL** do Tenant e no campo **Secret Token** no separador de provisionamento da sua aplicação Peakon no portal Azure.

    ![Peakon Criar Token](media/Peakon-provisioning-tutorial/peakon04.png)

## <a name="add-peakon-from-the-gallery"></a>Adicione Peakon da galeria

Para configurar o Peakon para o fornecimento automático de utilizadores com a AD Azure, é necessário adicionar peakon da galeria de aplicações Azure AD à sua lista de aplicações saaS geridas.

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Peakon**, selecione **Peakon** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Peakon na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-peakon"></a>Configurar o fornecimento automático de utilizadores a Peakon 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Peakon com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para Peakon, seguindo as instruções fornecidas no tutorial de [sinalização Peakon Single](peakon-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

### <a name="to-configure-automatic-user-provisioning-for-peakon--in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para Peakon em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Peakon**.

    ![A ligação Peakon na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. De acordo com a secção **de Credenciais de Administrador,** insere os valores **de URL SCIM 2.0** e **OAuth Bearer Token** recuperados anteriormente em **URL** de Inquilino e **Token Secreto,** respectivamente. Clique em **Ligação de Teste** para garantir que o Azure AD pode ligar-se a Peakon. Se a ligação falhar, certifique-se de que a sua conta Peakon tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

7. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

8. Clique em **Guardar**.

9. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Peakon**.

    ![Mapmapeamento de utilizadores de Peakon](media/Peakon-provisioning-tutorial/Peakon-user-mappings.png)

10. Reveja os atributos do utilizador que são sincronizados de Azure AD para Peakon na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Peakon para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos de utilizador de Peakon](media/Peakon-provisioning-tutorial/Peakon-user-attributes.png)

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
    
    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure em Peakon.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="connector-limitations"></a>Limitações do conector

* Todos os atributos personalizados do utilizador em Peakon têm de ser estendidos a partir da extensão personalizada de utilizador SCIM da Peakon de `urn:ietf:params:scim:schemas:extension:peakon:2.0:User`.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)