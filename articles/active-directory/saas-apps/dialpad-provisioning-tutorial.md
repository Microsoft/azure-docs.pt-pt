---
title: 'Tutorial: Configure Dialpad para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer automaticamente e desfornecer contas de utilizador ao Dialpad.
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
ms.openlocfilehash: 9f39277644547a625d87a39681f0c5520996cbd6
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77058389"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Tutorial: Configure dialpad para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Dialpad e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer automaticamente e desfornecer utilizadores e/ou grupos para dialpad.

> [!NOTE]
>  Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)

> Este conector encontra-se atualmente em Pré-visualização. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino dialpad.](https://www.dialpad.com/pricing/)
* Uma conta de utilizador no Dialpad com permissões de administrador.

## <a name="assign-users-to-dialpad"></a>Atribuir utilizadores ao Dialpad
Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Dialpad. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao Dialpad seguindo as instruções aqui:
 
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Dicas importantes para atribuir utilizadores ao Dialpad

 * Recomenda-se que um único utilizador da AD Azure seja atribuído ao Dialpad para testar a configuração automática de fornecimento do utilizador. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um utilizador ao Dialpad, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função de Acesso Predefinido estão excluídos do fornecimento.

## <a name="setup-dialpad-for-provisioning"></a>Configurar dialpad para provisionamento

Antes de configurar o Dialpad para o fornecimento automático de utilizadores com o Azure AD, terá de recuperar algumas informações de fornecimento a partir do Dialpad.

1. Inscreva-se na consola de administrador do [Dialpad](https://dialpadbeta.com/login) e selecione **as definições de administrador**. Certifique-se de que a **Minha Companhia** é selecionada a partir da queda. Navegar para **Autenticação > Chaves API**.

    ![Dialpad Adicionar SCIM](media/dialpad-provisioning-tutorial/dialpad01.png)

2. Gere uma nova tecla clicando **Em adicionar uma chave** e configurar as propriedades do seu símbolo secreto.

    ![Dialpad Adicionar SCIM](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![Dialpad Adicionar SCIM](media/dialpad-provisioning-tutorial/dialpad03.png)

3. Clique no **Click para mostrar** o botão de valor para a sua chave API recentemente criada e copiar o valor mostrado. Este valor será inserido no campo **Secret Token** no separador de fornecimento da sua aplicação Dialpad no portal Azure. 

    ![Dialpad Criar Token](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Adicione dialpad da galeria

Para configurar o Dialpad para o fornecimento automático de utilizadores com a AD Azure, é necessário adicionar o Dialpad da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar o Dialpad à galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **o Dialpad**, selecione **Dialpad** no painel de resultados.
    ![Dialpad na lista de resultados](common/search-new-app.png)

5. Navegue para o **URL** realçado abaixo num navegador separado. 

    ![Dialpad Adicionar SCIM](media/dialpad-provisioning-tutorial/dialpad05.png)

6. No canto superior direito, selecione **Log In > Use o Dialpad online**.

    ![Dialpad Adicionar SCIM](media/dialpad-provisioning-tutorial/dialpad06.png)

7. Como o Dialpad é uma aplicação OpenIDConnect, opte por iniciar sessão no Dialpad utilizando a sua conta de trabalho da Microsoft.

    ![Dialpad Adicionar SCIM](media/dialpad-provisioning-tutorial/loginpage.png)

8. Após uma autenticação bem sucedida, aceite o pedido de consentimento para a página de consentimento. A aplicação será adicionada automaticamente ao seu inquilino e será redirecionado para a sua conta Dialpad.

    ![Dialpad Adicionar SCIM](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Configure o fornecimento automático de utilizadores ao Dialpad

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no Dialpad com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para dialpad em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Dialpad**.

    ![O link Dialpad na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção **de Credenciais de Administrador,** a entrada `https://dialpad.com/scim` no **URL do Arrendatário**. Insera o valor que recuperou e guardou mais cedo do Dialpad em **Secret Token**. Clique na **ligação de teste** para garantir que o Azure AD pode ligar-se ao Dialpad. Se a ligação falhar, certifique-se de que a sua conta Dialpad tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Dialpad**.

    ![Mapeamento do utilizador dialpad](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Dialpad na secção De Mapeamento do **Atributo.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar as contas do utilizador no Dialpad para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos do utilizador do dialpad](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de provisionamento de AD Azure para o Dialpad, altere o Estado de **Provisionamento** para **Ligar** na secção **Definições.**

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja fornecer ao Dialpad, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividadede provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure no Dialpad.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [Relatórios sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>Limitações do conector
* O Dialpad não suporta renomeações de grupo hoje. Isto significa que quaisquer alterações ao nome de **exibição** de um grupo em Azure AD não serão atualizadas e refletidas no Dialpad.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
