---
title: 'Tutorial: Configure iProva para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer automaticamente e desfornecer contas de utilizador para iProva.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 4edba747-242d-4795-9539-649f33af4c13
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: bb730bad2837616aee0ebfa2da04015542782d9a
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057509"
---
# <a name="tutorial-configure-iprova-for-automatic-user-provisioning"></a>Tutorial: Configure iProva para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no iProva e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer automaticamente e desfornecer utilizadores e/ou grupos para iProva.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino iProva](https://www.iProva.com/)
* Uma conta de utilizador no iProva com permissões admin.

## <a name="assigning-users-to-iprova"></a>Atribuir utilizadores ao iProva

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao iProva. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos à iProva seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-iprova"></a>Dicas importantes para atribuir utilizadores ao iProva

* Recomenda-se que um único utilizador da AD Azure seja designado para o iProva para testar a configuração automática de fornecimento do utilizador. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um utilizador ao iProva, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="set-up-iprova-for-provisioning"></a>Configurar iProva para o provisionamento

1. Inscreva-se na consola [iProva Admin](https://www.iProva.com/). Navegue para **ir para > Gestão de Aplicações.**

    ![consola iProva Admin](media/iprova-provisioning-tutorial/admin.png)

2.  Clique na **gestão externa do utilizador.**

    ![iProva Adicionar SCIM](media/iprova-provisioning-tutorial/external.png)

3. Para adicionar um novo fornecedor, Clique no ícone **plus.** Na nova caixa de diálogo **do fornecedor Add,** forneça um **Título**. Pode optar por adicionar **restrição de acesso baseada em IP.** Clique no botão **OK.**

    ![iProva adicionar novo](media/iprova-provisioning-tutorial/add.png)

    ![iProva adicionar fornecedor](media/iprova-provisioning-tutorial/addprovider.png)

4.  Clique no botão **de ficha permanente.** Copie o **símbolo Permanente** e guarde-o pois esta será a única altura em que poderá vê-lo. Este valor será inserido no campo Secret Token no separador provisionamento da sua aplicação iProva no portal Azure.

    ![iProva Criar Token](media/iprova-provisioning-tutorial/token.png)

## <a name="add-iprova-from-the-gallery"></a>Adicione iProva da galeria

Antes de configurar o iProva para o fornecimento automático de utilizadores com a AD Azure, é necessário adicionar iProva da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar iProva na galeria de aplicações da AD Azure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **iProva,** selecione **iProva** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![iProva na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-iprova"></a>Configurar o fornecimento automático de utilizadores ao iProva 

Esta secção orienta-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no iProva com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para iProva seguindo as instruções fornecidas no tutorial de [inscrição single iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iProva-tutorial). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se complementem.

### <a name="to-configure-automatic-user-provisioning-for-iprova-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para iProva em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **iProva**.

    ![O link iProva na lista de Candidaturas](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção **de Credenciais de Administrador,** a entrada `https://identitymanagement.services.iProva.nl/scim` no **URL do Arrendatário**. Insera o valor **do símbolo permanente** recuperado anteriormente em Secret **Token**. Clique em **Ligação** de Teste para garantir que o Azure AD pode ligar-se ao iProva. Se a ligação falhar, certifique-se de que a sua conta iProva tem permissões de Administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to iProva**.

    ![Mapeamento de utilizadores iProva](media/iprova-provisioning-tutorial/usermappings.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para iProva na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para corresponder às contas do utilizador no iProva para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos de utilizador iProva](media/iprova-provisioning-tutorial/userattributes.png)

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups para iProva**.

    ![Mapeamentos do grupo iProva](media/iprova-provisioning-tutorial/groupmappings.png)

11. Reveja os atributos do grupo que são sincronizados de Azure AD para iProva na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar os grupos em iProva para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos do Grupo iProva](media/iprova-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento de AD Azure para iProva, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja fornecer ao iProva, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento da AD Azure no iProva.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

