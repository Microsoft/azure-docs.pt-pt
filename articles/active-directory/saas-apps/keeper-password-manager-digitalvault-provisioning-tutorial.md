---
title: 'Tutorial: Configure Keeper Password Manager & Digital Vault para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo do Azure para fornecer e desfornecer automaticamente contas de utilizador ao Keeper Password Manager & Digital Vault.
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
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 236527a9889879f872ef8c3867a7ec3c1b1ba0a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057532"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Tutorial: Configure Keeper Password Manager & Cofre Digital para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Keeper Password Manager & Digital Vault e Azure Ative Directory (Azure AD) para configurar o Azure AD para fornecer e desfornecer automaticamente utilizadores e/ou grupos para Keeper Password Manager & Digital Vault.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD Azure
* [Um gestor de passwords de guarda & inquilino do Cofre Digital](https://keepersecurity.com/pricing.html?t=e)
* Uma conta de utilizador no Keeper Password Manager & Digital Vault com permissões de Administrador.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Adicione o Gestor de Passwords do Guardião & Cofre Digital da galeria

Antes de configurar o Keeper Password Manager & Digital Vault para o fornecimento automático de utilizadores com a AD Azure, tem de adicionar o Gestor de Passwords do Guardião & Cofre Digital da galeria de aplicações Azure AD à sua lista de aplicações geridas do SaaS.

**Para adicionar o Gestor de Passwords keeper & Cofre Digital da galeria de aplicações Da AD Azure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza o **Gestor de Passwords do Guardião & Cofre Digital,** selecione **Keeper Password Manager & Digital Vault** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Gestor de passwords do Guardião & Cofre Digital na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Atribuir utilizadores ao Gestor de Passwords keeper & Cofre Digital

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Gestor de Passwords do Guardião & Cofre Digital. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao Keeper Password Manager & Digital Vault seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Dicas importantes para atribuir utilizadores ao Keeper Password Manager & Cofre Digital

* Recomenda-se que um único utilizador da AD Azure seja atribuído ao Keeper Password Manager & Digital Vault para testar a configuração automática de fornecimento de utilizadores. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador ao Gestor de Passwords do Guardião & Cofre Digital, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Configurar o fornecimento automático de utilizadores ao Gestor de Passwords keeper & Cofre Digital 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no Keeper Password Manager & Digital Vault com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para o Gestor de Passwords do Guardião & Cofre Digital, seguindo as instruções fornecidas no [Keeper Password Manager & tutorial de inscrição individual do Cofre Digital](keeperpasswordmanager-tutorial.md). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para keeper Password Manager & Digital Vault in Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Keeper Password Manager & Digital Vault**.

    ![O Gestor de Passwords keeper & ligação digital vault na lista de aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. De acordo com a secção **de Credenciais de Administrador,** insera o **URL** do Inquilino e o **Token Secreto** do seu Gestor de Passwords do Guardião & conta do Cofre Digital, conforme descrito no Passo 6.

6. Inscreva-se na consola [Keeper Admin](https://keepersecurity.com/console/#login). Clique em **Admin** e selecione um nó existente ou crie um novo. Navegue para o separador **Provisioning** e selecione **Adicionar Método**.

    ![Consola De Guardião Admin](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    Selecione **SCIM (Sistema de Gestão de Identidade de Domínio Transversal**.

    ![Guardião Adicionar SCIM](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    Clique em **Criar Token**de Provisionamento .

    ![Guardião Criar Ponto Final](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Copie os valores para **URL** e **Token** e cole-os em **URL do Inquilino** e **Token Secreto** em Azure AD. Clique em **Guardar** para completar a configuração de provisionamento no Keeper.

    ![Guardião Criar Token](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. Ao povoar os campos mostrados no Passo 5, clique em **Test Connection** para garantir que o Azure AD pode ligar-se ao Keeper Password Manager & Digital Vault. Se a ligação falhar, certifique-se de que o gestor de passwords do Guardião & conta Digital Vault tem permissões de administrador e tente novamente.

    ![URL do inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

8. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Keeper Password Manager & Digital Vault**.

    ![Mapeamento de utilizador de guardião](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Reveja os atributos do utilizador que são sincronizados de Azure AD para Keeper Password Manager & Digital Vault na secção De mapeamento de **atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no Keeper Password Manager & Digital Vault para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos do Utilizador do Guardião](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Keeper Password Manager & Digital Vault**.

    ![Mapeamentodo do Grupo Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Reveja os atributos do grupo que são sincronizados de Azure AD para Keeper Password Manager & Digital Vault na secção De mapeamento de **atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar os grupos em Keeper Password Manager & Digital Vault para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos do Grupo Keeper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para ativar o serviço de provisionamento de Anúncios Azure para o Gestor de Passwords do Guardião & Cofre Digital, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

16. Defina os utilizadores e/ou grupos que deseja fornecer ao Keeper Password Manager & Digital Vault, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

17. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividadede provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de Anúncios Azure no Keeper Password Manager & Digital Vault.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="connector-limitations"></a>Limitações do conector

* O Gestor de Passwords do Guardião & Digital Vault requer **que os e-mails** e o **userName** tenham o mesmo valor de origem, uma vez que quaisquer atualizações para qualquer um dos atributos modificarão o outro valor.
* O Gestor de Passwords do Guardião & o Cofre Digital não suporta que o utilizador apague, apenas desative. Os utilizadores com deficiência aparecerão como bloqueados na Consola Keeper Admin UI.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

