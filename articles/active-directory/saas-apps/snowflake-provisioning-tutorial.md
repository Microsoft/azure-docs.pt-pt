---
title: 'Tutorial: Configure floco de neve para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
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
ms.openlocfilehash: 2c5d91894ba35233f3fbebffdff9104edcfdd27b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063166"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Tutorial: Configure floco de neve para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Snowflake e azure Ative Directory (Azure AD) para configurar a Azure AD para fornecer e desfornecer automaticamente utilizadores e/ou grupos para Snowflake.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino de Floco de Neve.](https://www.Snowflake.com/pricing/)
* Uma conta de utilizador em Snowflake com permissões de administrador.

## <a name="assigning-users-to-snowflake"></a>Atribuir utilizadores a Snowflake

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Floco de Neve. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao Floco de Neve seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-snowflake"></a>Dicas importantes para atribuir utilizadores a Snowflake

* Recomenda-se que um único utilizador da AD Azure seja atribuído ao Snowflake para testar a configuração automática de fornecimento do utilizador. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador ao Snowflake, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

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

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **snowflake,** selecione **Snowflake** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Floco de neve na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-snowflake"></a>Configurar o fornecimento automático de utilizadores ao Floco de Neve 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Snowflake com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para snowflake, seguindo as instruções fornecidas no tutorial de [assinatura Snowflake Single](Snowflake-tutorial.md). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para Floco de Neve em Azure AD:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Snowflake**.

    ![A ligação snowflake na lista de aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Sob a secção de Credenciais de Administrador, input `https://<Snowflake Account URL>/scim/v2` intenant URL. Um exemplo da URL do inquilino:`https://acme.snowflakecomputing.com/scim/v2`

6. Insera o valor token de **autenticação SCIM** recuperado anteriormente em **Ficha Secreta**. Clique em **Ligação de Teste** para garantir que o Azure AD pode ligar-se ao Floco de Neve. Se a ligação falhar, certifique-se de que a sua conta Snowflake tem permissões de administrador e tente novamente.

    ![URL do inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

7. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

8. Clique em **Guardar**.

9. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Snowflake**.

    ![Mapeamento de utilizador de flocos de neve](media/Snowflake-provisioning-tutorial/user-mapping.png)

10. Reveja os atributos do utilizador que são sincronizados de Azure AD para Snowflake na secção de Mapeamento do **Atributo.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Snowflake para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos do utilizador do floco de neve](media/Snowflake-provisioning-tutorial/user-attribute.png)

11. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Snowflake**.

    ![Mapeamentodo do grupo Snowflake](media/Snowflake-provisioning-tutorial/group-mapping.png)

12. Reveja os atributos do grupo que são sincronizados de Azure AD para Snowflake na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar os grupos em Snowflake para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos do Grupo Snowflake](media/Snowflake-provisioning-tutorial/group-attribute.png)

13. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para ativar o serviço de provisionamento de AD Azure para floco de neve, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

15. Defina os utilizadores e/ou grupos que deseja fornecer ao Floco de Neve escolhendo os valores desejados no **Âmbito** na secção **Definições.** Se esta opção não estiver disponível, configure os campos necessários sob credenciais de administrador, clique em **Guardar** e refresque a página. 

    ![Âmbito de provisionamento](common/provisioning-scope.png)

16. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

    Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento da AD Azure em Snowflake.

    Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [Relatórios sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Limitações do conector

* Os tokens scim gerados pelo floco de neve expiram em 6 meses. Esteja ciente de que estes precisam de ser atualizados antes de expirarem para permitir que as sincronizações de provisionamento continuem a funcionar. 

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento de contas de utilizador para aplicações empresariais.](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes
* [Aprenda a rever os registos e obtenha relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).
