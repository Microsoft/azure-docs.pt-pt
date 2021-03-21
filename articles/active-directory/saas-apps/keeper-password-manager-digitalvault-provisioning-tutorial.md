---
title: 'Tutorial: Configure Keeper Password Manager & Cofre Digital para fornecimento automático de utilizadores com Azure Ative Directory | Microsoft Docs'
description: Aprenda a configurar o Azure Ative Directory para provisões automáticas e desavisagem de contas de utilizador ao Keeper Password Manager & Digital Vault.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 2670dc0cb56805a2afa966bee1d2aa52b6c8e46a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94358988"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Tutorial: Configure Keeper Password Manager & Cofre Digital para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Keeper Password Manager & Digital Vault e Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e/ou grupos de fornecimento e/ou grupos de keeper para Keeper Password Manager & Digital Vault.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD AZure
* [Um gestor de passwords do guardião & inquilino do cofre digital](https://keepersecurity.com/pricing.html?t=e)
* Uma conta de utilizador no Keeper Password Manager & Digital Vault com permissões de administração.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Adicione o Gestor de Passwords do Guardião & Cofre Digital da galeria

Antes de configurar o Keeper Password Manager & Cofre Digital para fornecimento automático de utilizadores com Azure AD, tem de adicionar o Keeper Password Manager & Digital Vault da galeria de aplicações AD Azure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Keeper Password Manager & Cofre Digital da galeria de aplicações AZure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira o **Keeper Password Manager & Digital Vault**, selecione Keeper Password Manager & Digital **Vault** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Keeper Password Manager & Digital Vault na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Atribuir utilizadores ao Keeper Password Manager & Cofre Digital

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deverá decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Keeper Password Manager & Digital Vault. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos ao Keeper Password Manager & Digital Vault seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Dicas importantes para atribuir utilizadores ao Keeper Password Manager & Cofre Digital

* Recomenda-se que um único utilizador Azure AD seja designado para Keeper Password Manager & Digital Vault para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao Keeper Password Manager & Digital Vault, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Configurar o fornecimento automático de utilizadores ao Keeper Password Manager & Cofre Digital 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no Keeper Password Manager & Digital Vault com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar o sign-on único baseado em SAML para Keeper Password Manager & Digital Vault, seguindo as instruções fornecidas no Tutorial de [Assinatura do Keeper & Digital Vault.](keeperpasswordmanager-tutorial.md) O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para Keeper Password Manager & Digital Vault em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Keeper Password Manager & Digital Vault**.

    ![O Gestor de Passwords do Guardião & link digital Vault na lista de aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** insira o URL do **inquilino** e **o Token Secreto** do seu Keeper Password Manager & conta digital Vault conforme descrito no Passo 6.

6. Inscreva-se na sua [Consola de Administração Keeper](https://keepersecurity.com/console/#login). Clique em **Administração** e selecione um nó existente ou crie um novo. Navegue no **separador Provisioning** e selecione **Add Method**.

    ![Consola de Administrador Guardião](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    Selecione **SCIM (Sistema de Gestão de Identidade de Domínio Cruzado**.

    ![Keeper Add SCIM](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    Clique **em Criar Ficha de Provisionamento**.

    ![Keeper Criar Ponto final](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Copie os valores para **URL** e **Token** e cole-os em **URL de inquilino** e **token secreto** em Azure AD. Clique **em Guardar** para completar a configuração de provisionamento no Keeper.

    ![Keeper Create Token](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. Ao povoar os campos mostrados no Passo 5, clique em **Test Connection** para garantir que o Azure AD pode ligar-se ao Keeper Password Manager & Digital Vault. Se a ligação falhar, certifique-se de que o seu Keeper Password Manager & conta Digital Vault tem permissões de administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

8. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Na secção **De Mapeamentos,** selecione **Synchronize Azure Ative Directory Users to Keeper Password Manager & Digital Vault**.

    ![Mapeamentos do utilizador do guardião](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Reveja os atributos do utilizador que são sincronizados do AD AD do Azure para keeper Password Manager & Digital Vault na secção **De Mapeamento** de Atributos. Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder às contas do utilizador no Keeper Password Manager & Digital Vault para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do utilizador do guardião](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to Keeper Password Manager & Digital Vault**.

    ![Mapeamentos do grupo keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Reveja os atributos do grupo que são sincronizados de Azure AD para Keeper Password Manager & Digital Vault na secção **De Mapeamento** de Atributos. Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder aos grupos do Keeper Password Manager & Digital Vault para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do Grupo Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para ativar o serviço de fornecimento de Ad Azure para Keeper Password Manager & Digital Vault, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

16. Defina os utilizadores e/ou grupos que pretende prever ao Keeper Password Manager & Digital Vault, escolhendo os valores pretendidos no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

17. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD Azure no Keeper Password Manager & Digital Vault.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* Keeper Password Manager & Digital Vault requer **que os e-mails** e **o nome do utilizador** tenham o mesmo valor de origem, uma vez que quaisquer atualizações de ambos os atributos irão modificar o outro valor.
* Keeper Password Manager & Digital Vault não suporta exclusões de utilizador, apenas desativar. Os utilizadores desativado aparecerão como bloqueados na UI da Consola de Administrador do Guardião.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)

