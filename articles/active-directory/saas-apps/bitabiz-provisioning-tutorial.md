---
title: 'Tutorial: Configure BitaBIZ para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo azure para fornecer e desfornecer automaticamente contas de utilizador ao BitaBIZ.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d0d38abe-c041-482a-9d3f-ca340678c226
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: ad9176614c4a5235e5138444d4197286204a747f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77059244"
---
# <a name="tutorial-configure-bitabiz-for-automatic-user-provisioning"></a>Tutorial: Configure bitaBIZ para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no BitaBIZ e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecer automaticamente e desfornecer utilizadores e/ou grupos para o BitaBIZ.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino bitaBIZ.](https://bitabiz.dk/en/price/)
* Uma conta de utilizador no BitaBIZ com permissões admin.

## <a name="assigning-users-to-bitabiz"></a>Atribuir utilizadores ao BitaBIZ

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao BitaBIZ. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao BitaBIZ seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-bitabiz"></a>Dicas importantes para atribuir utilizadores ao BitaBIZ

* Recomenda-se que um único utilizador da AD Azure seja atribuído ao BitaBIZ para testar a configuração automática de fornecimento do utilizador. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador ao BitaBIZ, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="setup-bitabiz-for-provisioning"></a>Configurar bitaBIZ para provisionamento

Antes de configurar o BitaBIZ para o fornecimento automático de utilizadores com a AD Azure, terá de ativar o fornecimento de SCIM no BitaBIZ.

1. Inscreva-se na consola [BitaBIZ Admin](https://www.bitabiz.com/login?lang=en). Clique em **CONFIGURAUP ADMIN**.

    ![Consola de administrador bitaBIZ](media/bitabiz-provisioning-tutorial/setup-admin.png)

2.  Navegar para **integração.**

    ![Consola de administrador bitaBIZ](media/bitabiz-provisioning-tutorial/integration.png)

2.  Navegue para o **Microsoft Azure AD Provisioning**.  Selecione **Ativado** no fornecimento automático de utilizadores. Copie os valores para URL final de ponto final de **fornecimento de SCIM** e **token**bearer . Estes valores serão inseridos nos campos URL do Tenant e Secret Token no separador de provisionamento da sua aplicação BitaBIZ no portal Azure.

    ![BitaBIZ Adicionar SCIM](media/bitabiz-provisioning-tutorial/authentication.png)


## <a name="add-bitabiz-from-the-gallery"></a>Adicione bitaBIZ da galeria

Para configurar o BitaBIZ para o fornecimento automático de utilizadores com a AD Azure, é necessário adicionar o BitaBIZ da galeria de aplicações Azure AD à sua lista de aplicações saaS geridas.

**Para adicionar bitaBIZ da galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza o **BitaBIZ,** selecione **BitaBIZ** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![BitaBIZ na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-bitabiz"></a>Configurar o fornecimento automático de utilizadores ao BitaBIZ 

Esta secção orienta-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no BitaBIZ com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para o BitaBIZ, seguindo as instruções fornecidas no [tutorial de inscrição single BitaBIZ](BitaBIZ-tutorial.md). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático de utilizadores, embora estas duas funcionalidades se elogiem mutuamente

### <a name="to-configure-automatic-user-provisioning-for-bitabiz-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para bitaBIZ em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **BitaBIZ**.

    ![O link BitaBIZ na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção de Credenciais de Administrador, insera o URL final do Ponto Final do **SCIM** e os valores do **Token do Portador** recuperados anteriormente em URL de Inquilino e Token Secreto, respectivamente. Clique em **Ligação de Teste** para garantir que o Azure AD pode ligar-se ao BitaBIZ. Se a ligação falhar, certifique-se de que a sua conta BitaBIZ tem permissões de Administrador e tente novamente.

    ![URL do inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to BitaBIZ**.

    ![Mapeamento de utilizadores bitaBIZ](media/bitabiz-provisioning-tutorial/usermapping.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para BitaBIZ na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no BitaBIZ para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos de utilizador bitaBIZ](media/bitabiz-provisioning-tutorial/user-attribute.png)


10. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de provisionamento de AD Azure para o BitaBIZ, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que gostaria de fornecer ao BitaBIZ, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure no BitaBIZ.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="connector-limitations"></a>Limitações do conector

* BitaBIZ requer **nome de utilizador**, **e-mail,** primeiro **nome** e **apelido** como atributos obrigatórios. 
* A BitaBIZ não suporta aeliminações duras atualmente.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento de contas de utilizador para aplicações empresariais.](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Aprenda a rever os registos e obtenha relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).
