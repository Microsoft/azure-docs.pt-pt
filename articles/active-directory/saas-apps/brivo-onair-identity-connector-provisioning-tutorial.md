---
title: 'Tutorial: Configurar o Conector de Identidade Brivo Onair para o fornecimento automático de utilizadores com o Azure Ative Directory | Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automáticamente e desavisigativar contas de utilizador ao Conector de Identidade Brivo Onair.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: 4f230a70ae87c7c0ec9db347e7cf975c0252bb18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95998094"
---
# <a name="tutorial-configure-brivo-onair-identity-connector-for-automatic-user-provisioning"></a>Tutorial: Configurar o Conector de Identidade Brivo Onair brivo para o fornecimento automático do utilizador

O objetivo deste tutorial é demonstrar os passos a serem realizados no Brivo Onair Identity Connector e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desavisagem automática de utilizadores e/ou grupos para Brivo Onair Identity Connector.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD AZure
* [Um inquilino do Conector de Identidade Brivo Onair](https://www.brivo.com/lp/quote)
* Uma conta de utilizador no Conector de Identidade Brivo Onair com permissões de Administrador Sénior.

## <a name="assigning-users-to-brivo-onair-identity-connector"></a>Atribuir utilizadores ao Conector de Identidade Brivo Onair

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deverá decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Conector de Identidade Brivo Onair. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos ao Conector de Identidade Brivo Onair seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-brivo-onair-identity-connector"></a>Dicas importantes para atribuir utilizadores ao Conector de Identidade Brivo Onair

* Recomenda-se que um único utilizador Azure AD seja designado para brivo Onair Identity Connector para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao Brivo Onair Identity Connector, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="setup-brivo-onair-identity-connector-for-provisioning"></a>Configuração Brivo Onair Identity Connector para provisionamento

1. Inscreva-se na sua [Consola de Admin de Identificação Brivo Onair.](https://acs.brivo.com/login/) Navegar para **as Definições de Conta > Conta**.

   ![Consola de admin de conector de identidade Brivo Onair](media/brivo-onair-identity-connector-provisioning-tutorial/admin.png)

2. Clique no separador **Azure AD.** Na página de detalhes da **AZure AD** reintrodir a palavra-passe da sua conta de administrador sénior. Clique em **Enviar**.

   ![Conector de identidade Brivo Onair azul](media/brivo-onair-identity-connector-provisioning-tutorial/azuread.png)

3. Clique no botão **Copy Token** e guarde o **Token Secreto.** Este valor será introduzido no campo Secret Token no separador Provisioning da sua aplicação Brivo Onair Identity Connector no portal Azure.

   ![Ficha do conector de identidade Brivo Onair](media/brivo-onair-identity-connector-provisioning-tutorial/token.png)

## <a name="add-brivo-onair-identity-connector-from-the-gallery"></a>Adicione o Conector de Identidade Brivo Onair da galeria

Antes de configurar o Conector de Identidade Brivo Onair para o fornecimento automático de utilizadores com Azure AD, é necessário adicionar o Conector de Identidade Brivo Onair da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar o Conector de Identidade Brivo Onair da galeria de aplicações AZure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira o **Conector de Identidade Brivo Onair**, selecione **Brivo Onair Identity Connector** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Conector de identidade Brivo Onair na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-brivo-onair-identity-connector"></a>Configurar o fornecimento automático do utilizador ao Conector de Identidade Brivo Onair 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no Brivo Onair Identity Connector com base nas atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-brivo-onair-identity-connector-in-azure-ad"></a>Para configurar o fornecimento automático do utilizador para o Conector de Identidade Brivo Onair em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Brivo Onair Identity Connector**.

    ![O link brivo onair identity connector na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** entrada `https://scim.brivo.com/ActiveDirectory/v2/` na URL do **inquilino.** Insira o valor **de token de autenticação SCIM** recuperado anteriormente em **Secret Token**. Clique em **Testar a Ligação** para garantir que o Azure AD pode ligar-se ao Conector de Identidade Brivo Onair. Se a ligação falhar, certifique-se de que a sua conta Brivo Onair Identity Connector tem permissões de administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para Brivo Onair Identity Connector**.

    ![Mapeamentos do utilizador do conector de identidade Brivo Onair](media/brivo-onair-identity-connector-provisioning-tutorial/user-mappings.png )

9. Reveja os atributos do utilizador que são sincronizados de Azure AD a Brivo Onair Identity Connector na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador no Conector de Identidade Brivo Onair para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do utilizador do conector de identidade Brivo Onair](media/brivo-onair-identity-connector-provisioning-tutorial/user-attributes.png)

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups para Brivo Onair Identity Connector**.

    ![Mapeamentos do grupo de conector de identidade Brivo Onair](media/brivo-onair-identity-connector-provisioning-tutorial/group-mappings.png)

11. Reveja os atributos do grupo que são sincronizados de Azure AD a Brivo Onair Identity Connector na secção **De mapeamento** de atributos. Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar com os grupos no Conector de Identidade Brivo Onair para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do Grupo de Conector de Identidade Brivo Onair](media/brivo-onair-identity-connector-provisioning-tutorial/group-attributes.png)

12. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para o Conector de Identidade Brivo Onair, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar ao Conector de Identidade Brivo Onair, escolhendo os valores pretendidos no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de Azure AD no Brivo Onair Identity Connector.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)

