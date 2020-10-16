---
title: 'Tutorial: Configurar bónus para o fornecimento automático de utilizadores com diretório Azure Ative / Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores à Bonusly.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 1db1ef3a8fa7de557444c25650410b4e84422be4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91849315"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>Tutorial: Configurar bónus para o provisionamento automático do utilizador

O objetivo deste tutorial é demonstrar os passos a serem realizados no Bónus e no Azure Ative Directory (Azure AD) para configurar a Azure AD para automaticamente a prestação e desavisagem de utilizadores e/ou grupos para bónus.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem o seguinte:

* Um inquilino da AD AZure
* Um [inquilino bónus](https://bonus.ly/pricing)
* Uma conta de utilizador em Bónus com permissões de Administração

> [!NOTE]
> A integração de provisionamento Azure AD depende da [API de Repouso Bónus,](https://konghq.com/solutions/gateway/)que está disponível para os desenvolvedores da Bonusly.

## <a name="adding-bonusly-from-the-gallery"></a>Adicionar bónus da galeria

Antes de configurar bónus para o fornecimento automático de utilizadores com Azure AD, tem de adicionar Bonusly da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Bonusly da galeria de aplicações AZure, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva Bonusly**, selecione **Bonusly** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Bónus na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>Atribuir utilizadores a Bónus

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram "atribuídos" a uma aplicação em AD Azure são sincronizados. 

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em AZure AD que precisam de acesso ao Bónus. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos a Bónus seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>Dicas importantes para atribuir utilizadores à Bónus

* Recomenda-se que um único utilizador AZure AD seja designado para a Bonusly para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador a Bónus, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Configurar o fornecimento automático do utilizador para bónus

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Bónus com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar o único sinal de Bónus baseado em SAML, seguindo as instruções fornecidas no [tutorial de inscrição único bonusly](bonus-tutorial.md). O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para bónus em Ad Azure:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Bonusly**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Bonusly**.

    ![O link bónus na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningTab.png" alt-text="Screenshot do separador Bonusly - Provisioning. Em Gestão, Provisioning é destacado." border="false":::

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png" alt-text="Screenshot do separador Bonusly - Provisioning. Em Gestão, Provisioning é destacado." border="false":::

5. Sob a secção **Credenciais de Administração,** insira o **Token Secreto** da sua conta Bónus, conforme descrito no Passo 6.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/secrettoken.png" alt-text="Screenshot do separador Bonusly - Provisioning. Em Gestão, Provisioning é destacado." border="false":::

6. O **Token Secreto** para a sua conta Bonusly está localizado na **Empresa > Admin > Integrações.** Na secção **Se pretender codificar,** clique em **API > Criar Novo Token de Acesso API** para criar um novo Token Secreto.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png" alt-text="Screenshot do separador Bonusly - Provisioning. Em Gestão, Provisioning é destacado." border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/BonsulyRestApi.png" alt-text="Screenshot do separador Bonusly - Provisioning. Em Gestão, Provisioning é destacado." border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/CreateToken.png" alt-text="Screenshot do separador Bonusly - Provisioning. Em Gestão, Provisioning é destacado." border="false":::

7. No ecrã seguinte, digite um nome para o token de acesso na caixa de texto fornecida e, em seguida, prima **Create Api Key**. O novo token de acesso aparecerá por alguns segundos num pop-up.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/Token01.png" alt-text="Screenshot do separador Bonusly - Provisioning. Em Gestão, Provisioning é destacado." border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/Token02.png" alt-text="Screenshot do separador Bonusly - Provisioning. Em Gestão, Provisioning é destacado." border="false":::

8. Ao povoar os campos mostrados no Passo 5, clique em **Test Connection** para garantir que a Azure AD pode ligar-se ao Bónus. Se a ligação falhar, certifique-se de que a sua conta Bonusly tem permissões de Administração e tente novamente.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/TestConnection.png" alt-text="Screenshot do separador Bonusly - Provisioning. Em Gestão, Provisioning é destacado." border="false":::

9. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação **Enviar por e-mail uma notificação de e-mail quando ocorrer uma falha**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/EmailNotification.png" alt-text="Screenshot do separador Bonusly - Provisioning. Em Gestão, Provisioning é destacado." border="false":::

10. Clique em **Guardar**.

11. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para bónus**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/UserMappings.png" alt-text="Screenshot do separador Bonusly - Provisioning. Em Gestão, Provisioning é destacado." border="false":::

12. Reveja os atributos do utilizador que são sincronizados de AD AD a Bonusly na secção **De Mapeamento** de Atributos. Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador em Bonusly para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/UserAttributeMapping.png" alt-text="Screenshot do separador Bonusly - Provisioning. Em Gestão, Provisioning é destacado." border="false":::

13. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para ativar o serviço de prestação de Ad Azure para bónus, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningStatus.png" alt-text="Screenshot do separador Bonusly - Provisioning. Em Gestão, Provisioning é destacado." border="false":::

15. Defina os utilizadores e/ou grupos que deseja prever bónus, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ScopeSync.png" alt-text="Screenshot do separador Bonusly - Provisioning. Em Gestão, Provisioning é destacado." border="false":::

16. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/SaveProvisioning.png" alt-text="Screenshot do separador Bonusly - Provisioning. Em Gestão, Provisioning é destacado." border="false":::

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD Azure em Bónus.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png
