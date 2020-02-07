---
title: 'Tutorial: Configure Workgrid para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer e desfornecer automaticamente contas de utilizadores à Workgrid.
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
ms.date: 08/17/2019
ms.author: Zhchia
ms.openlocfilehash: 94d70447117c73a309959ddf66972c921aa5e687
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062817"
---
# <a name="tutorial-configure-workgrid--for-automatic-user-provisioning"></a>Tutorial: Configure workgrid para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Workgrid e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer e desfornecer automaticamente utilizadores e/ou grupos à Workgrid.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino de Workgrid](https://www.workgrid.com/)
* Uma conta de utilizador em Workgrid com permissões de administrador.

## <a name="assigning-users-to-workgrid"></a>Atribuir utilizadores à Workgrid 

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso à Workgrid. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos à Workgrid seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-workgrid"></a>Dicas importantes para atribuir utilizadores à Workgrid 

* Recomenda-se que um único utilizador da AD Azure seja atribuído à Workgrid para testar a configuração automática de fornecimento do utilizador. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um utilizador à Workgrid, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="set-up-workgrid-for-provisioning"></a>Configurar a Workgrid para o provisionamento

Antes de configurar a Workgrid para o fornecimento automático de utilizadores com a AD Azure, terá de ativar o fornecimento de SCIM na Workgrid.

1. Inicie sessão na Workgrid. Navegar para **Utilizadores > Fornecimento de Utilizadores**.

    ![Workgrid](media/Workgrid-provisioning-tutorial/user.png)

2. No âmbito da API de **Gestão de Conta,** clique em **Criar Credenciais.**

    ![Workgrid](media/Workgrid-provisioning-tutorial/scim.png)

3. Copiar os valores do Ponto final do **SCIM** e do Token de **Acesso.** Estes serão inseridos no **campo URL** do Tenant e **secret Token** no separador de provisionamento da sua aplicação Workgrid no portal Azure.

    ![Workgrid](media/Workgrid-provisioning-tutorial/token.png)


## <a name="add-workgrid--from-the-gallery"></a>Adicione Workgrid da galeria

Para configurar a Workgrid para o fornecimento automático de utilizadores com a AD Azure, é necessário adicionar a Workgrid da galeria de aplicações Azure AD à sua lista de aplicações saaS geridas.

**Para adicionar Workgrid da galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Workgrid**, selecione **Workgrid** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Workgrid na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-workgrid"></a>Configurar o fornecimento automático de utilizadores à Workgrid  

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Workgrid com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para workgrid, seguindo as instruções fornecidas no tutorial de [sinalização Workgrid Single](Workgrid-tutorial.md). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático de utilizadores, embora estas duas funcionalidades se elogiem mutuamente

### <a name="to-configure-automatic-user-provisioning-for-workgrid--in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para workgrid em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Workgrid**.

    ![O link Workgrid na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção de Credenciais de Administrador, insere os valores **SCIM Endpoint** e **Access Token** recuperados anteriormente em URL de **Inquilino** e **Token Secreto,** respectivamente. Clique na **ligação de teste** para garantir que o Azure AD pode ligar-se à Workgrid. Se a ligação falhar, certifique-se de que a sua conta Workgrid tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Workgrid**.

    ![Mapeamento de utilizadores de workgrid](media/Workgrid-provisioning-tutorial/usermapping.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Workgrid na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Workgrid para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos de utilizador da workgrid](media/Workgrid-provisioning-tutorial/userattribute.png)

10. Sob a secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Workgrid**

    ![Mapeamento de utilizadores de workgrid](media/Workgrid-provisioning-tutorial/groupmapping.png)

12. Reveja os atributos do grupo que são sincronizados de Azure AD para Workgrid na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Workgrid para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Mapeamento de utilizadores de workgrid](media/Workgrid-provisioning-tutorial/groupattribute.png)

13. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para ativar o serviço de provisionamento de AD Azure para workgrid, altere o Estado de **Provisionamento** para **Ligado** na secção **Definições.**

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

15. Defina os utilizadores e/ou grupos que deseja fornecer à Workgrid, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

16. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a executar do que as sincronizações subsequentes. Para obter mais informações sobre quanto tempo demorará os utilizadores e/ou grupos a fornecer, veja [quanto tempo demorará a fornecer aos utilizadores](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Pode utilizar a secção **Estado Atual** para monitorizar o progresso e seguir ligações ao seu relatório de atividade de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento da AD Azure na Workgrid. Para mais informações, [consulte Verifique o estado do fornecimento do utilizador](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
