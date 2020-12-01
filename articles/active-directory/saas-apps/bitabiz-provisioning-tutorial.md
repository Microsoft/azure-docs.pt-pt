---
title: 'Tutorial: Configurar o BitaBIZ para o provisionamento automático do utilizador com o Azure Ative Directory / Microsoft Docs'
description: Aprenda a configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores ao BitaBIZ.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 8eccc3be7da201ee1e2af046c6b515871ef05adc
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350794"
---
# <a name="tutorial-configure-bitabiz-for-automatic-user-provisioning"></a>Tutorial: Configurar BitaBIZ para o provisionamento automático do utilizador

O objetivo deste tutorial é demonstrar os passos a serem realizados no BitaBIZ e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desavisionamento automática de utilizadores e/ou grupos à BitaBIZ.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino bitaBIZ.](https://bitabiz.dk/en/price/)
* Uma conta de utilizador em BitaBIZ com permissões de Administração.

## <a name="assigning-users-to-bitabiz"></a>Atribuir utilizadores ao BitaBIZ

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao BitaBIZ. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos à BitaBIZ seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-bitabiz"></a>Dicas importantes para atribuir utilizadores ao BitaBIZ

* Recomenda-se que um único utilizador Azure AD seja designado para o BitaBIZ para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao BitaBIZ, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="setup-bitabiz-for-provisioning"></a>Configuração BitaBIZ para provisionamento

Antes de configurar o BitaBIZ para o fornecimento automático de utilizadores com Azure AD, terá de ativar o fornecimento scim no BitaBIZ.

1. Inscreva-se na sua [Consola de Administração BitaBIZ.](https://www.bitabiz.com/login?lang=en) Clique em **SETUP ADMIN**.

    :::image type="content" source="media/bitabiz-provisioning-tutorial/setup-admin.png" alt-text="Screenshot da Consola de Administração BitaBIZ, com o administrador de configuração em destaque." border="false":::

2.  Navegar para a **INTEGRAÇÃO.**

    :::image type="content" source="media/bitabiz-provisioning-tutorial/integration.png" alt-text="Screenshot da Consola de Administração BitaBIZ, com Integração em destaque." border="false":::

2.  Navegue para o **Microsoft Azure AD Provisioning**.  Selecione **Ativado** no fornecimento automático do utilizador. Copie os valores para **SCIM Provisioning ENDpoint URL** e  **Bearer Token**. Estes valores serão introduzidos nos campos URL e Secret Token no separador Provisioning da sua aplicação BitaBIZ no portal Azure.

    ![BitaBIZ Add SCIM](media/bitabiz-provisioning-tutorial/authentication.png)


## <a name="add-bitabiz-from-the-gallery"></a>Adicione BitaBIZ da galeria

Para configurar o BitaBIZ para o fornecimento automático de utilizadores com Azure AD, é necessário adicionar o BitaBIZ da galeria de aplicações AD AD Azure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar BitaBIZ da galeria de aplicações AZure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **BitaBIZ,** selecione **BitaBIZ** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![BitaBIZ na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-bitabiz"></a>Configurar o fornecimento automático de utilizadores ao BitaBIZ 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em BitaBIZ com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar o sign-on único baseado em SAML para o BitaBIZ, seguindo as instruções fornecidas no [tutorial de assinatura único BitaBIZ](BitaBIZ-tutorial.md). O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente

### <a name="to-configure-automatic-user-provisioning-for-bitabiz-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o BitaBIZ em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **BitaBIZ**.

    ![O link BitaBIZ na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção credenciais de administração, insira os valores URL e **Token do bearer** **de provisão SCIM** recuperados anteriormente em URL de inquilino e Token Secreto, respectivamente. Clique em **Testar a Ligação** para garantir que o Azure AD pode ligar-se ao BitaBIZ. Se a ligação falhar, certifique-se de que a sua conta BitaBIZ tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para BitaBIZ**.

    ![Mapeamentos de utilizadores bitaBIZ](media/bitabiz-provisioning-tutorial/usermapping.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD a BitaBIZ na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no BitaBIZ para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do utilizador BitaBIZ](media/bitabiz-provisioning-tutorial/user-attribute.png)


10. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de prestação de Ad Azure para o BitaBIZ, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja prestar ao BitaBIZ, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento AZure AD no BitaBIZ.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O BitaBIZ requer **userName**, **email**, **firstName** e **lastName** como atributos obrigatórios. 
* O BitaBIZ não suporta eliminações duras atualmente.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).
