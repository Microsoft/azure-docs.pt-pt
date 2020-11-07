---
title: 'Tutorial: Configurar Druva para fornecimento automático de utilizadores com Diretório Ativo Azure Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores à Druva.
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
ms.openlocfilehash: 5579a9d96828caa1453547e7c2e11b8f0d717d2a
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359311"
---
# <a name="tutorial-configure-druva-for-automatic-user-provisioning"></a>Tutorial: Configurar Druva para o fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Diretório Ativo de Druva e Azure (Ad) para configurar a Azure AD para fornecimento e desavisagem automática de utilizadores e/ou grupos à Druva.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino druva.](https://www.druva.com/products/pricing-plans/)
* Uma conta de utilizador em Druva com permissões de Administração.

## <a name="assigning-users-to-druva"></a>Atribuir utilizadores à Druva

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso à Druva. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos à Druva seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-druva"></a>Dicas importantes para atribuir utilizadores à Druva

* Recomenda-se que um único utilizador AZure AD seja atribuído à Druva para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador à Druva, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="setup-druva-for-provisioning"></a>Configuração Druva para provisionamento

Antes de configurar a Druva para o fornecimento automático de utilizadores com Azure AD, terá de permitir o fornecimento scim em Druva.

1. Inscreva-se na sua [Consola De Administração Druva.](https://console.druva.com) Navegue até **Druva > inSync.**

    ![Consola Druva Admin](media/druva-provisioning-tutorial/menubar.png)

2. Navegue para **gerir**  >  **utilizadores de implementações**  >  **Users**.

    :::image type="content" source="media/druva-provisioning-tutorial/manage.png" alt-text="Screenshot da consola de administração Druva. A gestão é realçada e o menu Gerir é visível. Neste menu, em Implementações, destaca-se o Utilizadores." border="false":::

3.  Navegar para **definições**. Clique **em gerar token**.

    :::image type="content" source="media/druva-provisioning-tutorial/settings.png" alt-text="Screenshot de uma página na consola de administração Druva. As definições são realçadas e o separador Definições está aberto. O botão De símbolo gerar é realçado." border="false":::

4.  Copie o valor **simbólico de Auth.** Este valor será introduzido no campo **Secret Token** no separador Provisioning da sua aplicação Druva no portal Azure.
    
    :::image type="content" source="media/druva-provisioning-tutorial/auth.png" alt-text="Screenshot da página de token Create na consola de administração Druva. Um link marcado Copy Token está disponível para copiar o valor simbólico de Auth." border="false":::

## <a name="add-druva-from-the-gallery"></a>Adicione Druva da galeria

Para configurar a Druva para o fornecimento automático de utilizadores com Azure AD, é necessário adicionar a Druva da galeria de aplicações AD AD do Azure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Druva da galeria de aplicações AD Azure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Druva,** selecione **Druva** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Druva na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-druva"></a>Configurar o fornecimento automático de utilizadores à Druva 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Druva com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar o sign-on único baseado em SAML para a Druva, seguindo as instruções fornecidas no [tutorial de inscrição única druva.](druva-tutorial.md) O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-druva-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para a Druva em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Druva.**

    ![O link Druva na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5.  Sob a secção de Credenciais de Administração, entrada `https://apis.druva.com/insync/scim` na **URL do inquilino.** Insira o valor **simbólico Auth** em **Secret Token**. Clique em **Testar a Ligação** para garantir que o Azure AD pode ligar-se ao Druva. Se a ligação falhar, certifique-se de que a sua conta Druva tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e selecione **Enviar uma notificação por e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para Druva**.

    ![Mapeamentos de utilizadores druva](media/druva-provisioning-tutorial/usermapping.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD a Druva na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador em Druva para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do utilizador Druva](media/druva-provisioning-tutorial/userattribute.png)


10. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de prestação de AD Azure para a Druva, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja prestar à Druva, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

    Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD Azure em Druva.

    Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).
    
## <a name="connector-limitations"></a>Limitações do conector

* Druva requer **e-mail** como um atributo obrigatório. 

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).
