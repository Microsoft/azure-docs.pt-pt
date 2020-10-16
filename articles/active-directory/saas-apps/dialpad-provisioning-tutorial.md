---
title: 'Tutorial: Configurar o Dialport para o provisionamento automático do utilizador com o Azure Ative Directory / Microsoft Docs'
description: Aprenda a configurar o Azure Ative Directory para provisões automáticas e desavisagem de contas de utilizador para o Dialpad.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 7475ebc6dd51bbc1c30ee22623c7cbda1a5237ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91848964"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Tutorial: Configure o Dialport para o fornecimento automático do utilizador

O objetivo deste tutorial é demonstrar os passos a serem realizados no Dialpad e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desavisagem automática de utilizadores e/ou grupos para o Dialpad.

> [!NOTE]
>  Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).

> Este conector encontra-se atualmente em Pré-Visualização. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino de dialporto.](https://www.dialpad.com/pricing/)
* Uma conta de utilizador no Dialpad com permissões de Administração.

## <a name="assign-users-to-dialpad"></a>Atribuir utilizadores ao Dialpad
O Azure Ative Directory utiliza um conceito chamado atribuições para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Dialpad. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos ao Dialpad seguindo as instruções aqui:
 
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Dicas importantes para atribuir utilizadores ao Dialpad

 * Recomenda-se que um único utilizador Azure AD seja designado para o Dialpad para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao Dialpad, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função Acesso Predefinido estão excluídos do provisionamento.

## <a name="setup-dialpad-for-provisioning"></a>Configurar o marcador para o provisionamento

Antes de configurar o Dialpad para o fornecimento automático do utilizador com a Azure AD, terá de obter algumas informações de fornecimento do Dialpad.

1. Inscreva-se na consola [de admin do Dialport e](https://dialpadbeta.com/login) selecione **as definições de Administração**. Certifique-se de que **a My Company** é selecionada a partir do dropdown. Navegue para **autenticação > teclas API**.

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad01.png" alt-text="Screenshot da consola de administração Dialpad, com o ícone de definições, As teclas My Company, Authentication e A P I em destaque, e a Minha Empresa selecionada." border="false":::

2. Gere uma nova tecla clicando **Em adicionar uma chave** e configurar as propriedades do seu token secreto.

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad02.png" alt-text="Screenshot da consola de administração Dialpad, com o ícone de definições, As teclas My Company, Authentication e A P I em destaque, e a Minha Empresa selecionada." border="false":::

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad03.png" alt-text="Screenshot da consola de administração Dialpad, com o ícone de definições, As teclas My Company, Authentication e A P I em destaque, e a Minha Empresa selecionada." border="false":::

3. Clique no botão **Click para mostrar o botão de valor** para a sua tecla API recentemente criada e copie o valor mostrado. Este valor será introduzido no campo **Secret Token** no separador Provisioning da sua aplicação Dialpad no portal Azure. 

    ![Dialpad Criar Token](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Adicione o Dialpad da galeria

Para configurar o Dialpad para o fornecimento automático de utilizadores com Azure AD, é necessário adicionar o Dialpad da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Dialpad da galeria de aplicações AZure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **insira o Dialpad,** selecione **Dialpad** no painel de resultados.
    ![Dialpad na lista de resultados](common/search-new-app.png)

5. Navegue para o **URL** realçado abaixo num navegador separado. 

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad05.png" alt-text="Screenshot da consola de administração Dialpad, com o ícone de definições, As teclas My Company, Authentication e A P I em destaque, e a Minha Empresa selecionada." border="false":::

6. No canto superior direito, selecione **Iniciar > Use o Dialpad online**.

    :::image type="content" source="media/dialpad-provisioning-tutorial/dialpad06.png" alt-text="Screenshot da consola de administração Dialpad, com o ícone de definições, As teclas My Company, Authentication e A P I em destaque, e a Minha Empresa selecionada." border="false":::

7. Como o Dialpad é uma aplicação OpenIDConnect, opte por iniciar sessão no Dialpad utilizando a sua conta de trabalho da Microsoft.

    :::image type="content" source="media/dialpad-provisioning-tutorial/loginpage.png" alt-text="Screenshot da consola de administração Dialpad, com o ícone de definições, As teclas My Company, Authentication e A P I em destaque, e a Minha Empresa selecionada." border="false":::

8. Após uma autenticação bem sucedida, aceite o pedido de consentimento para a página de consentimento. A aplicação será então automaticamente adicionada ao seu inquilino e será redirecionado para a sua conta Dialpad.

    :::image type="content" source="media/dialpad-provisioning-tutorial/redirect.png" alt-text="Screenshot da consola de administração Dialpad, com o ícone de definições, As teclas My Company, Authentication e A P I em destaque, e a Minha Empresa selecionada." border="false":::

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Configure o fornecimento automático do utilizador ao Dialpad

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no Dialpad com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Para configurar o fornecimento automático do utilizador para o Dialpad em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Dialpad**.

    ![O link Dialpad na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** entrada `https://dialpad.com/scim` na URL do **inquilino.** Insira o valor que recuperou e guardou mais cedo do Dialpad em **Secret Token**. Clique em **Testar a Ligação** para garantir que o Azure AD pode ligar-se ao Dialpad. Se a ligação falhar, certifique-se de que a sua conta Dialpad tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para o Dialpad**.

    ![Mapeamentos de utilizadores de dialportos](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD a Dialpad na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador no Dialpad para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do utilizador do dialporto](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de prestação de Ad Azure para o Dialpad, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja providenciar ao Dialpad, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento AD Azure no Dialpad.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>Limitações do conector
* O Dialpad não suporta renomes de grupo hoje. Isto significa que quaisquer alterações ao nome de **exibição** de um grupo em Azure AD não serão atualizadas e refletidas no Dialpad.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
