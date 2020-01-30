---
title: 'Tutorial: Configure floco de neve para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer e desfornecer automaticamente contas de utilizador ao Snowflake.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f9ce85f4-0992-4bc6-8276-4b2efbce8dcb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2aaf1d2b377abc0b10b0b14de03d01c7f6fae5b7
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76767771"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Tutorial: Configure floco de neve para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Snowflake e azure Ative Directory (Azure AD) para configurar a Azure AD para fornecer e desfornecer automaticamente utilizadores e/ou grupos para Snowflake.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino de Floco de Neve.](https://www.Snowflake.com/pricing/)
* Uma conta de utilizador em Snowflake com permissões de administrador.

## <a name="assigning-users-to-snowflake"></a>Atribuir utilizadores a Snowflake

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Floco de Neve. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao Floco de Neve seguindo as instruções aqui:
* [Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-snowflake"></a>Dicas importantes para atribuir utilizadores a Snowflake

* Recomenda-se que um único utilizador da AD Azure seja atribuído ao Snowflake para testar a configuração automática de fornecimento do utilizador. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um utilizador ao Snowflake, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os usuários com a função de **acesso padrão** são excluídos do provisionamento.

## <a name="setup-snowflake-for-provisioning"></a>Configurar floco de neve para o provisionamento

Antes de configurar o Snowflake para o fornecimento automático de utilizadores com a AD Azure, terá de ativar o fornecimento de SCIM no Snowflake.

1. Inscreva-se na sua Consola de Administração Snowflake. Introduza a consulta mostrada abaixo na folha de cálculo realçada e clique em **Executar**.

    ![Consola de administrador de floco de neve](media/Snowflake-provisioning-tutorial/image00.png)

2.  Um Token de Acesso SCIM será gerado para o seu inquilino snowflake. Para recuperá-lo, clique no link realçado abaixo.

    ![Floco de neve Adicionar SCIM](media/Snowflake-provisioning-tutorial/image01.png)

3. Copie o valor do token gerado e clique **em Done**. Este valor será inserido no campo **Secret Token** no separador de provisionamento da sua aplicação Snowflake no portal Azure.

    ![Floco de neve Adicionar SCIM](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="add-snowflake-from-the-gallery"></a>Adicione Floco de Neve da galeria

Para configurar o Floco de Neve para o fornecimento automático de utilizadores com a AD Azure, é necessário adicionar snowflake da galeria de aplicações Azure AD à sua lista de aplicações saaS geridas.

**Para adicionar Floco de Neve da galeria de aplicações azure AD, execute os seguintes passos:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **snowflake,** selecione **Snowflake** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Floco de neve na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-snowflake"></a>Configurar o fornecimento automático de utilizadores ao Floco de Neve 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Snowflake com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para snowflake, seguindo as instruções fornecidas no tutorial de [assinatura Snowflake Single](Snowflake-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para Floco de Neve em Azure AD:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Snowflake**.

    ![O link do floco de neve na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **provisionamento** .

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção de Credenciais de Administrador, a entrada `https://<Snowflake Account URL>/scim/v2` no URL do inquilino. Um exemplo da URL do inquilino: `https://acme.snowflakecomputing.com/scim/v2`

6. Insera o valor token de **autenticação SCIM** recuperado anteriormente em **Ficha Secreta**. Clique em **Ligação de Teste** para garantir que o Azure AD pode ligar-se ao Floco de Neve. Se a ligação falhar, certifique-se de que a sua conta Snowflake tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

7. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção- **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

8. Clique em **Guardar**.

9. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Snowflake**.

    ![Mapeamento de utilizador de flocos de neve](media/Snowflake-provisioning-tutorial/user-mapping.png)

10. Reveja os atributos do utilizador que são sincronizados de Azure AD para Snowflake na secção de Mapeamento do **Atributo.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Snowflake para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Atributos do utilizador do floco de neve](media/Snowflake-provisioning-tutorial/user-attribute.png)

11. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Snowflake**.

    ![Mapeamentodo do grupo Snowflake](media/Snowflake-provisioning-tutorial/group-mapping.png)

12. Reveja os atributos do grupo que são sincronizados de Azure AD para Snowflake na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar os grupos em Snowflake para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Atributos do Grupo Snowflake](media/Snowflake-provisioning-tutorial/group-attribute.png)

13. Para configurar filtros de escopo, consulte as instruções a seguir fornecidas no [tutorial de filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para ativar o serviço de provisionamento de AD Azure para floco de neve, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

15. Defina os utilizadores e/ou grupos que deseja fornecer ao Floco de Neve escolhendo os valores desejados no **Âmbito** na secção **Definições.** Se esta opção não estiver disponível, configure os campos necessários sob credenciais de administrador, clique em **Guardar** e refresque a página. 

    ![Escopo de provisionamento](common/provisioning-scope.png)

16. Quando estiver pronto para provisionar, clique em **salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

    Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **escopo** na seção **configurações** . A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento da AD Azure em Snowflake.

    Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [Relatórios sobre o provisionamento automático da conta de utilizador](../manage-apps/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Limitações do conector

* Os tokens scim gerados pelo floco de neve expiram em 6 meses. Esteja ciente de que estes precisam de ser atualizados antes de expirarem para permitir que as sincronizações de provisionamento continuem a funcionar. 

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento de contas de utilizador para aplicações empresariais.](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes
* [Aprenda a rever os registos e obtenha relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md).
