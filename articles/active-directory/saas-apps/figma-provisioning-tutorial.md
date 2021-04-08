---
title: 'Tutorial: Configurar o fornecimento automático de utilizadores da Figma com o Azure Ative Directory | Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores à Figma.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: ec509cc04db87602b3e29230253d9b363a5e30d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96353923"
---
# <a name="tutorial-configure-figma-for-automatic-user-provisioning"></a>Tutorial: Configure a Figma para o fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados na Figma e na Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desavisagem automática de utilizadores e/ou grupos à Figma.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino de Figma.](https://www.figma.com/pricing/)
* Uma conta de utilizador em Figma com permissões de Administração.

## <a name="assign-users-to-figma"></a>Atribua utilizadores à Figma.
O Azure Ative Directory utiliza um conceito chamado atribuições para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso à Figma. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos à Figma seguindo as instruções aqui:
 
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)
## <a name="important-tips-for-assigning-users-to-figma"></a>Dicas importantes para atribuir utilizadores à Figma

 * Recomenda-se que um único utilizador AZure AD seja atribuído à Figma para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador à Figma, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função Acesso Predefinido estão excluídos do provisionamento.

## <a name="set-up-figma-for-provisioning"></a>Configurar a Figma para o provisionamento

Antes de configurar a Figma para o fornecimento automático do utilizador com a Azure AD, terá de obter algumas informações de fornecimento da Figma.

1. Inscreva-se na consola [de administração figma.](https://www.Figma.com/) Clique no ícone de engrenagem ao lado do seu inquilino.

    :::image type="content" source="media/Figma-provisioning-tutorial/image0.png" alt-text="Screenshot da consola de administração figma. Um inquilino chamado A D Scim Test é visível. Ao lado do inquilino, destaca-se um ícone de engrenagem." border="false":::

2. Navegue para **configurações de início de > de atualização geral**.

    :::image type="content" source="media/Figma-provisioning-tutorial/figma03.png" alt-text="Screenshot do separador geral da consola de administração Figma. Em Início de Sessão e provisionamento, é realçada a atualização das definições de login." border="false":::

3. Copie a identificação do **inquilino.** Este valor será utilizado para construir o URL de ponta final SCIM a ser inserido no campo URL do **inquilino** no separador Provisioning da sua aplicação Figma no portal Azure.

    :::image type="content" source="media/Figma-provisioning-tutorial/figma-tenantid.png" alt-text="Screenshot da secção S A M L S S O na consola de administração figma. Uma etiqueta de identificação do inquilino e um link adjacente que diz Copy são destacados." border="false":::

4. Desloque-se para baixo e clique em **Gerar Token API**.

    :::image type="content" source="media/Figma-provisioning-tutorial/token.png" alt-text="Screenshot da secção de provisionamento S C I M na consola de administração Figma. Destaca-se um link com a etiqueta Generate A P I." border="false":::

5. Copie o valor token da **API.** Este valor será introduzido no campo **Secret Token** no separador Provisioning da sua aplicação Figma no portal Azure. 

    :::image type="content" source="media/Figma-provisioning-tutorial/figma04.png" alt-text="Screenshot de uma página na consola de administração figma. Sob o seu provisionamento A P I token, um espaço reservado para o token é realçado." border="false":::

## <a name="add-figma-from-the-gallery"></a>Adicione Figma da galeria

Para configurar a Figma para o fornecimento automático de utilizadores com Azure AD, é necessário adicionar a Figma da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Figma,** selecione **Figma** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Figma na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-figma"></a>Configurar o fornecimento automático de utilizadores à Figma 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Figma com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar o sign-on único baseado em SAML para a Figma, seguindo as instruções fornecidas no [tutorial de inscrição single figma](figma-tutorial.md). O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-figma--in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para a Figma em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Figma.**

    ![O link Figma na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais Admin,** entrada `https://www.figma.com/scim/v2/<TenantID>` em URL de **inquilino** onde **TenantID** é o valor que você recuperou de Figma anteriormente. Insira o valor **token** da API em **Secret Token**. Clique em **Testar a Ligação** para garantir que o Azure AD pode ligar-se ao Figma. Se a ligação falhar, certifique-se de que a sua conta Figma tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

8. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para Figma**.

    ![Mapeamentos do utilizador de Figma](media/Figma-provisioning-tutorial/figma05.png)

11. Reveja os atributos do utilizador que são sincronizados de Azure AD a Figma na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador em Figma para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do utilizador de Figma](media/Figma-provisioning-tutorial/figma06.png)

12. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para a Figma, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar à Figma, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de Ad AD Azure em Figma.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)