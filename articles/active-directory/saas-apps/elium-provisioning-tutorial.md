---
title: 'Tutorial: Configure Elium para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores ao Elium.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/19/2019
ms.author: Zhchia
ms.openlocfilehash: e8f027ccc577df79e561fca7194c20b6cc7ef2c6
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94356778"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>Tutorial: Configure Elium para o fornecimento automático de utilizadores

Este tutorial mostra como configurar o Elium e o Azure Ative Directory (Azure AD) para provisão automática de utilizadores e grupos de desa provisionamento para Elium.

> [!NOTE]
> Este tutorial descreve um conector que é construído em cima do serviço de fornecimento de utilizadores AD Azure. Para obter detalhes importantes sobre o que este serviço faz e como funciona, e para perguntas frequentes, consulte [o fornecimento e desprovisionamento de utilizadores da Automamate para aplicações SaaS com o Azure Ative Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em pré-visualização. Para os termos gerais de utilização das funcionalidades Azure na pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD AZure
* [Um inquilino de Elium](https://www.elium.com/pricing/)
* Uma conta de utilizador em Elium, com permissões de administração

## <a name="assigning-users-to-elium"></a>Atribuir utilizadores a Elium

A Azure AD usa um conceito chamado *atribuições* para determinar quais os utilizadores que recebem acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, decida quais os utilizadores e grupos em Azure AD que precisam de acesso ao Elium. Em seguida, atribua esses utilizadores e grupos a Elium seguindo os passos na [Atribuição de um utilizador ou grupo a uma aplicação empresarial.](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-elium"></a>Dicas importantes para atribuir utilizadores ao Elium 

Recomendamos que atribua um único utilizador Azure AD ao Elium para testar a configuração automática de fornecimento do utilizador. Mais utilizadores e grupos podem ser atribuídos mais tarde.

Ao atribuir um utilizador a Elium, deve selecionar uma função válida e específica para a aplicação (se houver alguma disponível) na caixa de diálogo de atribuição. Os utilizadores que tenham a função **De Acesso Predefinido** estão excluídos do provisionamento.

## <a name="set-up-elium-for-provisioning"></a>Configurar Elium para provisionamento

Antes de configurar Elium para o fornecimento automático de utilizadores com Azure AD, deve ativar o fornecimento do Sistema de Gestão de Identidade de Domínio Cruzado (SCIM) em Elium. Siga estes passos:

1. Inscreva-se no Elium e vá às **Definições do Meu Perfil**  >  **Settings**.

    ![Item do menu de definições em Elium](media/Elium-provisioning-tutorial/setting.png)

1. No canto inferior esquerdo, em **ADVANCED,** **selecione Security**.

    ![Ligação de segurança em Elium](media/Elium-provisioning-tutorial/security.png)

1. Copie os valores url do **inquilino** e **símbolo secreto.** Utilizará estes valores mais tarde, em campos correspondentes no **separador Provisioning** da sua aplicação Elium no portal Azure.

    ![Url de inquilino e campos de token secretos em Elium](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>Adicione Elium da galeria

Para configurar o Elium para o fornecimento automático de utilizadores com Azure AD, também deve adicionar Elium da galeria de aplicações AD AZure à sua lista de aplicações geridas de software-as-a-service (SaaS). Siga estes passos:

1. No [portal Azure,](https://portal.azure.com)no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![Item do menu do Diretório Ativo Azure](common/select-azuread.png)

1. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.

     ![Lâmina de aplicações AD Enterprise Azure](common/enterprise-applications.png)

1. Para adicionar uma nova aplicação, selecione **Nova aplicação** na parte superior do painel.

    ![Novo link de aplicação](common/add-new-app.png)

1. Na caixa de pesquisa, **digite Elium,** selecione **Elium** na lista de resultados e, em seguida, selecione **Adicionar** para adicionar a aplicação.

    ![Caixa de pesquisa de galeria](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>Configure o fornecimento automático de utilizadores ao Elium

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e grupos em Elium, com base em atribuições de utilizador e grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar um único sinal de sílaba com base na Linguagem de Marcação de Afirmação de Segurança (SAML), seguindo as instruções no tutorial de inscrição única de [Elium.](Elium-tutorial.md) Pode configurar um único sinal de inscrição independentemente do fornecimento automático do utilizador, embora as duas funcionalidades se complementem.

Para configurar o fornecimento automático de utilizadores para Elium em Azure AD, siga estes passos:

1. Inscreva-se no [portal Azure,](https://portal.azure.com)selecione **aplicações Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações AD Enterprise Azure](common/enterprise-applications.png)

1. Na lista de candidaturas, selecione **Elium**.

    ![Lista de aplicações na lâmina de aplicações da Enterprise](common/all-applications.png)

1. Selecione o separador **Aprovisionamento**.

    ![Aba de provisionamento na lâmina de aplicações da Enterprise](common/provisioning.png)

1. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Definição automática para modo de provisionamento](common/provisioning-automatic.png)

1. Na secção **Credenciais Admin,** tipo **\<tenantURL\> /scim/v2** no campo URL do **inquilino.** (O **inquilinoURL** é o valor recuperado anteriormente da consola de administração Elium.) Também digite o valor simbólico elium **secreto** no campo **Secret Token.** Por fim, selecione **a Ligação de Teste** para verificar se o Azure AD pode ligar-se ao Elium. Se a ligação falhar, certifique-se de que a sua conta Elium tem permissões de administração e tente novamente.

    ![URL de inquilino e campos de token secreto em credenciais de administrador](common/provisioning-testconnection-tenanturltoken.png)

1. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que receberá as notificações de erro de provisionamento. Em seguida, selecione a **notificação enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

1. Clique em **Guardar**.

1. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Elium**.

    ![Synchronize link para mapear utilizadores AZure AD para Elium](media/Elium-provisioning-tutorial/usermapping.png)

1. Reveja os atributos do utilizador que são sincronizados de Azure AD a Elium na secção **De Mapeamentos de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador em Elium para operações de atualização. **Selecione Guardar** para escoar quaisquer alterações.

    ![Atribuir mapeamentos entre Azure AD e Elium](media/Elium-provisioning-tutorial/userattribute.png)

1. Para configurar filtros de deteção, siga as instruções no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para ativar o serviço de prestação de Ad Azure para Elium, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado de provisionamento definido para On](common/provisioning-toggle-on.png)

1. Defina os utilizadores e grupos que deseja prestar ao Elium selecionando os valores que pretende na caixa de lista de down-down **Scope** na secção **Definições.**

    ![Caixa de lista de âmbito de provisionamento](common/provisioning-scope.png)

1. Quando estiver pronto para a provisão, **selecione Save**.

    ![Guardar botão para a configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** Este processo de sincronização inicial demora mais do que sincronizações posteriores. Para obter mais informações sobre o tempo necessário para o provisionamento, consulte [quanto tempo demorará a providenciar aos utilizadores?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

Utilize a secção **Estado Atual** para monitorizar o progresso e seguir links para o seu relatório de atividade de provisionamento. O relatório de atividades de provisionamento descreve todas as ações realizadas pelo serviço de fornecimento de Ad Azure em Elium. Para obter mais informações, [consulte verifique o estado do fornecimento do utilizador](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os registos de provisionamento da AD Azure, consulte [reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
