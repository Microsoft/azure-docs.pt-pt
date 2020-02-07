---
title: 'Tutorial: Configure Robin para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo azure para fornecer automaticamente e desfornecer contas de utilizador à Robin Powered.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 178ca5b3-4155-43ab-84f5-650d25c5a74a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2019
ms.author: Zhchia
ms.openlocfilehash: fabd8a1953bedf6c3db6da443903a6dbd965b01e
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77061011"
---
# <a name="tutorial-configure-robin-for-automatic-user-provisioning"></a>Tutorial: Configure Robin para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados na Robin e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer automaticamente e desfornecer utilizadores e/ou grupos à Robin.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino Robin](https://robinpowered.com/pricing/)
* Uma conta de utilizador em Robin com permissões de administrador.

## <a name="assigning-users-to-robin"></a>Atribuir utilizadores à Robin

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso à Robin. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos à Robin seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-robin"></a>Dicas importantes para atribuir utilizadores à Robin

* Recomenda-se que um único utilizador da AD Azure seja atribuído à Robin para testar a configuração automática de fornecimento do utilizador. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um utilizador à Robin, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="set-up-robin-for-provisioning"></a>Configurar robin para o provisionamento

1. Inscreva-se na sua [Consola Robin Admin](https://dashboard.robinpowered.com/login). Navegar para **Gerir > Integrações > SCIM > Gerir**.

    ![consola de administração robin powered](media/robin-provisioning-tutorial/robin-admin.png)

2.  Gerar um novo símbolo da organização. Se perder este símbolo, pode sempre fazer um novo sem afetar os utilizadores existentes.

    ![Robin alimentado Add SCIM](media/robin-provisioning-tutorial/robin-token.png)

3.  Copiar o Símbolo de **Autenticação SCIM**. Este valor será inserido no campo Secret Token no separador de provisionamento da sua aplicação Robin no portal Azure.



## <a name="add-robin-from-the-gallery"></a>Adicione Robin da galeria

Antes de configurar a Robin para o fornecimento automático de utilizadores com o Azure AD, precisa de adicionar a Robin da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar Robin da galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Robin,** selecione **Robin** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Robin na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-robin"></a>Configurar o fornecimento automático de utilizadores à Robin 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Robin com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para a Robin, seguindo as instruções fornecidas no tutorial de [assinatura Robin Single](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático de utilizadores, embora estas duas funcionalidades se elogiem mutuamente

### <a name="to-configure-automatic-user-provisioning-for-robin-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para robin em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Robin**.

    ![O elo robin alimentado na lista de aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção **de Credenciais de Administrador,** a entrada `https://api.robinpowered.com/v1.0/scim-2` no **URL do Arrendatário**. Insera o valor token de **autenticação SCIM** recuperado anteriormente em **Ficha Secreta**. Clique em **Ligação de Teste** para garantir que o Azure AD pode ligar-se à Robin. Se a ligação falhar, certifique-se de que a sua conta Robin tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Robin**.

    ![Mapeamentos de utilizador movidos a robin](media/robin-provisioning-tutorial/robin-user-mapping.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Robin na secção De Mapeamento do **Atributo.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Robin para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![atributos de utilizador movidos a robin](media/robin-provisioning-tutorial/robin-user-attribute-mapping.png)

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Robin**.

    ![Mapeamentos de grupo movidos a robin](media/robin-provisioning-tutorial/robin-group-mapping.png)

11. Reveja os atributos do grupo que são sincronizados de Azure AD para Robin na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar os grupos em Robin para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![atributos de grupo movidos a robin](media/robin-provisioning-tutorial/robin-group-attribute-mapping.png)

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento de AD Azure para a Robin, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que gostaria de fornecer à Robin, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Você pode usar a secção **Detalhes de Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividadede provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure na Robin.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .



## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

