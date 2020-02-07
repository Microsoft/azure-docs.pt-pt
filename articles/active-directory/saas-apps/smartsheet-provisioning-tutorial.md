---
title: 'Tutorial: Configure smartsheet para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo do Azure para fornecer e desfornecer automaticamente contas de utilizador à Smartsheet.
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
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: 9fbdf8a1c4b1881fc6dfd9d7b95a4103761e9ce7
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063208"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Tutorial: Configure smartsheet para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Smartsheet e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer automaticamente e desfornecer utilizadores e/ou grupos para a Smartsheet.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino smartsheet](https://www.smartsheet.com/pricing)
* Uma conta de utilizador num plano Smartsheet Enterprise ou Enterprise Premier com permissões de Administrador de Sistema.

## <a name="assign-users-to-smartsheet"></a>Atribuir utilizadores ao Smartsheet

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Smartsheet. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos à Smartsheet seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Dicas importantes para atribuir utilizadores ao Smartsheet

* Recomenda-se que um único utilizador da AD Azure seja atribuído à Smartsheet para testar a configuração automática de fornecimento do utilizador. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um utilizador ao Smartsheet, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

* Para garantir a paridade nas atribuições de funções de utilizador entre o Smartsheet e o Azure AD, recomenda-se utilizar as mesmas atribuições de funções povoadas na lista completa de utilizadores da Smartsheet. Para recuperar esta lista de utilizadores do Smartsheet, navegue para **A Dispor de Conta > User Management > Mais Ações > Download User List (csv)** .

* Para aceder a determinadas funcionalidades na aplicação, o Smartsheet requer que um utilizador tenha várias funções. Para saber mais sobre os tipos e permissões dos utilizadores no Smartsheet, vá a [Tipos e Permissões](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions)de Utilizador .

*  Se um utilizador tiver várias funções atribuídas no Smartsheet, **deve** certificar-se de que estas atribuições de funções são replicadas em AD Azure para evitar um cenário em que os utilizadores possam perder acesso permanente aos objetos Smartsheet. Cada papel único no Smartsheet **MUST** será atribuído a um grupo diferente em Azure AD. Em seguida, o utilizador **DEVE** ser adicionado a cada um dos grupos correspondentes às funções desejadas. 

## <a name="set-up-smartsheet-for-provisioning"></a>Configurar o Smartsheet para o provisionamento

Antes de configurar o Smartsheet para o fornecimento automático de utilizadores com a AD Azure, terá de ativar o fornecimento de SCIM no Smartsheet.

1. Inscreva-se como **SysAdmin** no **[portal Smartsheet](https://app.smartsheet.com/b/home)** e navegue para **o Account Admin**.

    ![Administrador de conta de folha inteligente](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Vá a Controlos de **Segurança > Fornecimento automático do utilizador > Editar**.

    ![Controlos de Segurança smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Adicione e valide os domínios de e-mail para os utilizadores que planeia fornecer de Azure AD para Smartsheet. Escolha **não habilitado** para garantir que todas as ações de provisionamento apenas têm origem em Azure AD, e também para garantir que a sua lista de utilizadores da Smartsheet está sincronizada com as atribuições da AD Azure.

    ![Fornecimento de utilizadores de folha inteligente](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Uma vez concluída a validação, terá de ativar o domínio. 

    ![Domínio de ativação de folha inteligente](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Gere o **Token Secreto** necessário para configurar o fornecimento automático de utilizadores com a AD Azure navegando para **Apps e Integrações**.

    ![Instalação de folha seletiva](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Escolha **acesso API**. Clique **Gerar novo sinal de acesso**.

    ![Instalação de folha seletiva](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Defina o nome do Token de Acesso API. Clique em **OK**.

    ![Instalação de folha seletiva](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Copie o Token de Acesso API e guarde-o pois esta será a única altura em que poderá vê-lo. Isto é necessário no campo **Secreto token** em Azure D.D.

    ![Ficha de folha inteligente](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>Adicione smartsheet da galeria

Para configurar o Smartsheet para o fornecimento automático de utilizadores com a AD Azure, é necessário adicionar smartsheet da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, **introduza o Smartsheet,** selecione **Smartsheet** no painel de resultados. 

    ![Folha inteligente na lista de resultados](common/search-new-app.png)

5. Selecione o **'Sign-up' para** o botão Smartsheet, que irá redirecioná-lo para a página de login do Smartsheet. 

    ![Smartsheet OIDC Add](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Como o Smartsheet é uma aplicação OpenIDConnect, opte por iniciar sessão no Smartsheet utilizando a sua conta de trabalho da Microsoft.

    ![Login Smartsheet OIDC](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. Após uma autenticação bem sucedida, aceite o pedido de consentimento para a página de consentimento. A aplicação será adicionada automaticamente ao seu inquilino e será redirecionada para a sua conta Smartsheet.

    ![Consentimento smartsheet OIDc](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Configure o fornecimento automático de utilizadores à Smartsheet 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Smartsheet com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para smartsheet em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Smartsheet**.

    ![O link Smartsheet na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção **de Credenciais de Administrador,** a entrada `https://scim.smartsheet.com/v2/` no **URL do Arrendatário**. Insera o valor que recuperou e guardou mais cedo da Smartsheet em **Secret Token**. Clique em **Ligação de Teste** para garantir que o Azure AD pode ligar-se ao Smartsheet. Se a ligação falhar, certifique-se de que a sua conta Smartsheet tem permissões SysAdmin e tente novamente.

    ![Certificado de](common/provisioning-testconnection-tenanturltoken.png)

6. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Smartsheet**.

    ![Mapeamento de utilizador de folha inteligente](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Smartsheet na secção De Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no Smartsheet para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos de utilizador de folha inteligente](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de provisionamento de AD Azure para smartsheet, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja fornecer ao Smartsheet, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividadede provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure na Smartsheet.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="connector-limitations"></a>Limitações do conector

* A Smartsheet não suporta eliminações suaves. Quando o atributo **ativo** de um utilizador é definido para Falso, o Smartsheet elimina o utilizador permanentemente.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
