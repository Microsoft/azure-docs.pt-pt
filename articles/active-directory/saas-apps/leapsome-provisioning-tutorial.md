---
title: 'Tutorial: Configurar Leapsome para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores ao Leapsome.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 06/28/2019
ms.author: jeedes
ms.openlocfilehash: f0d240819f3690292c572a350f387cd025c24737
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88519313"
---
# <a name="tutorial-configure-leapsome-for-automatic-user-provisioning"></a>Tutorial: Configurar Leapsome para o fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Diretório Ativo Leapsome e Azure Ative (Azure AD) para configurar a Azure AD para automaticamente a prestação e desavisagem de utilizadores e/ou grupos para Leapsome.

> [!NOTE]
>  Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações saaS com diretório Azure Ative.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-Visualização. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* Um inquilino [saltitado.](https://www.Leapsome.com/en/pricing)
* Uma conta de utilizador em Leapsome com permissões de Administração.

## <a name="assigning-users-to-leapsome"></a>Atribuir utilizadores ao Leapsome

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em AZure AD que precisam de acesso ao Leapsome. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos ao Leapsome seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)


## <a name="important-tips-for-assigning-users-to-leapsome"></a>Dicas importantes para atribuir utilizadores ao Leapsome

* Recomenda-se que um único utilizador AZure AD seja designado para o Leapsome para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao Leapsome, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.


## <a name="setup-leapsome-for-provisioning"></a>Configuração Leapsome para provisionamento

1. Inscreva-se na sua [Consola de Administração Leapsome.](https://www.Leapsome.com/app/#/login) Navegue para **Definições > Definições de Administração**.

    ![Consola de administração leapsome](media/Leapsome-provisioning-tutorial/leapsome-admin-console.png)

2.  Navegue para **integrações > fornecimento de utilizadores SCIM**.

    ![Leapsome Add SCIM](media/Leapsome-provisioning-tutorial/leapsome-add-scim.png)

3.  Copie o **token de autenticação SCIM**. Este valor será introduzido no campo Secret Token no separador Provisioning da sua aplicação Leapsome no portal Azure.

    ![Leapsome Criar Token](media/Leapsome-provisioning-tutorial/leapsome-create-token.png)

## <a name="add-leapsome-from-the-gallery"></a>Adicione Leapsome da galeria

Antes de configurar o Leapsome para o fornecimento automático de utilizadores com Azure AD, tem de adicionar Leapsome da galeria de aplicações AD AD do Azure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Leapsome da galeria de aplicações AZure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Leapsome,** selecione **Leapsome** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Leapsome na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-leapsome"></a>Configurar o fornecimento automático do utilizador ao Leapsome 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Leapsome com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar o sign-on único baseado em SAML para Leapsome, seguindo as instruções fornecidas no [tutorial de inscrição leapsome Single](Leapsome-tutorial.md). O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente

### <a name="to-configure-automatic-user-provisioning-for-leapsome-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para Leapsome in AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Leapsome**.

    ![O link Leapsome na lista de Aplicações](common/all-applications.png)

3. Selecione o **separador Provisioning.**

    ![Separador de provisionamento](common/provisioning.png)

4. Desa ajuste o **modo de provisionamento** para **automático**.

    ![Separador de provisionamento](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** entrada `https://www.leapsome.com/api/scim` na URL do **inquilino.** Insira o valor **de token de autenticação SCIM** recuperado anteriormente em **Secret Token**. Clique em **Testar a Ligação** para garantir que o Azure AD pode ligar-se ao Leapsome. Se a ligação falhar, certifique-se de que a sua conta Leapsome tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Leapsome**.

    ![Mapeamentos de utilizadores leapsome](media/Leapsome-provisioning-tutorial/Leapsome-user-mappings.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD a Leapsome na secção **De Mapeamento** de Atributos. Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador em Leapsome para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos de utilizador leapsome](media/Leapsome-provisioning-tutorial/Leapsome-user-attributes.png)

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to Leapsome**.

    ![Mapeamentos de grupo leapsome](media/Leapsome-provisioning-tutorial/Leapsome-group-mappings.png)

11. Reveja os atributos do grupo que são sincronizados de Azure AD a Leapsome na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são usados para combinar os grupos em Leapsome para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos de grupo leapsome](media/Leapsome-provisioning-tutorial/Leapsome-group-attributes.png)

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para Leapsome, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de Provisionamento Toggled On](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja providenciar ao Leapsome, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para a provisão, clique em **Guardar**.

    ![Configuração de provisionamento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD Azure em Leapsome.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* Leapsome requer **que o nome do utilizador** seja único.
* Leapsome só permite guardar endereços de e-mail de trabalho.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
