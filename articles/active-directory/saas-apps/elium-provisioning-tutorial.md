---
title: 'Tutorial: Configure Elium para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer automaticamente e desfornecer contas de utilizador ao Elium.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: Zhchia
ms.openlocfilehash: a3e1c936d46e0d9b0ad3b98dafeda7df0b4a23db
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77058496"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>Tutorial: Configure Elium para fornecimento automático de utilizadores

Este tutorial mostra como configurar o Elium e o Azure Ative Directory (Azure AD) para fornecer e desfornecer automaticamente utilizadores ou grupos à Elium.

> [!NOTE]
> Este tutorial descreve um conector que é construído em cima do serviço de provisionamento de utilizadores da AD Azure. Para detalhes importantes sobre o que este serviço faz e como funciona, e para perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores da Automatização para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector está atualmente em pré-visualização. Para os termos gerais de utilização das funcionalidades Do Azure na pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial assume que já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino de Elium](https://www.elium.com/pricing/)
* Uma conta de utilizador em Elium, com permissões de administração

## <a name="assigning-users-to-elium"></a>Atribuir utilizadores ao Elium

A Azure AD utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que recebem acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e grupos que foram atribuídos a uma aplicação em Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, decida quais os utilizadores e grupos em Azure AD que precisam de acesso ao Elium. Em seguida, atribua esses utilizadores e grupos ao Elium seguindo os passos em [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-elium"></a>Dicas importantes para atribuir utilizadores ao Elium 

Recomendamos que atribua um único utilizador da AD Azure ao Elium para testar a configuração automática de fornecimento de utilizadores. Mais utilizadores e grupos podem ser atribuídos mais tarde.

Ao atribuir um utilizador ao Elium, deve selecionar uma função válida e específica da aplicação (se houver) na caixa de diálogo de atribuição. Os utilizadores que tenham a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="set-up-elium-for-provisioning"></a>Configurar Elium para o provisionamento

Antes de configurar o Elium para o fornecimento automático de utilizadores com a AD Azure, deve ativar o fornecimento de Sistema de Gestão de Identidade de Domínio Transversal (SCIM) no Elium. Siga estes passos.

1. Inscreva-se no Elium e vá ao **My Profile** > **Definições**.

    ![Itens de menu de definições em Elium](media/Elium-provisioning-tutorial/setting.png)

1. No canto inferior esquerdo, em **ADVANCED,** selecione **Security**.

    ![Ligação de segurança em Elium](media/Elium-provisioning-tutorial/security.png)

1. Copie o URL do **Inquilino** e valores **secretos.** Utilizará estes valores mais tarde, em campos correspondentes no separador de **fornecimento** da sua aplicação Elium no portal Azure.

    ![URL do inquilino e campos de fichas secretas em Elium](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>Adicione Elium da galeria

Para configurar o Elium para o fornecimento automático de utilizadores com a AD Azure, deve também adicionar elium da galeria de aplicações Azure AD à sua lista de aplicações geridas de software como serviço (SaaS). Siga estes passos.

1. No [portal Azure,](https://portal.azure.com)no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![Item do menu Azure Ative Diretório](common/select-azuread.png)

1. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

     ![Lâmina de aplicações azure AD Enterprise](common/enterprise-applications.png)

1. Para adicionar uma nova aplicação, selecione **Nova aplicação** na parte superior do painel.

    ![Nova ligação de aplicação](common/add-new-app.png)

1. Na caixa de pesquisa, digite **Elium,** selecione **Elium** na lista de resultados e, em seguida, selecione **Adicionar** para adicionar a aplicação.

    ![Caixa de pesquisa de galeria](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>Configure o fornecimento automático de utilizadores ao Elium

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e grupos em Elium, com base em atribuições de utilizadores e grupos em Azure AD.

> [!TIP]
> Você também pode optar por ativar um único sign-on para Elium com base na Linguagem de Marcação de Afirmação de Segurança (SAML) seguindo as instruções no tutorial de [inscrição única elium](Elium-tutorial.md). Pode configurar um único sinal de inscrição independentemente do fornecimento automático do utilizador, embora as duas funcionalidades se complementem.

Para configurar o fornecimento automático de utilizadores para elium em Azure AD, siga estes passos:

1. Inscreva-se no [portal Azure,](https://portal.azure.com)selecione **aplicações Enterprise,** e depois selecione **Todas as aplicações**.

    ![Lâmina de aplicações azure AD Enterprise](common/enterprise-applications.png)

1. Na lista de candidaturas, selecione **Elium**.

    ![Lista de aplicações na lâmina de aplicações da Enterprise](common/all-applications.png)

1. Selecione o separador **Provisioning.**

    ![Separador de provisionamento na lâmina de aplicações da Enterprise](common/provisioning.png)

1. Detete o **modo de provisionamento** para **automático**.

    ![Regulação automática para modo de provisionamento](common/provisioning-automatic.png)

1. Na secção **credenciais de administrador,** **escreva\<\>/scim/v2** no campo URL do **Arrendatário.** (O **tenantURL** é o valor recuperado anteriormente da consola de administração Elium.) Escreva também o valor simbólico do Elium Secret no campo **Secreto Token.** Por fim, selecione **Test Connection** para verificar se o Azure AD pode ligar-se ao Elium. Se a ligação falhar, certifique-se de que a sua conta Elium tem permissões de administração e tente novamente.

    ![URL do inquilino e campos secretos de token em credenciais de administrador](common/provisioning-testconnection-tenanturltoken.png)

1. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que receberá as notificações de erro de fornecimento. Em seguida, selecione a **notificação de e-mail enviar uma notificação de e-mail quando ocorrer uma falha** verificar a caixa.

    ![Email de notificação](common/provisioning-notification-email.png)

1. Clique em **Guardar**.

1. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Elium**.

    ![Sincronizar link para mapear utilizadores da AD Azure para Elium](media/Elium-provisioning-tutorial/usermapping.png)

1. Reveja os atributos do utilizador que são sincronizados de Azure AD para Elium na secção **DeMapeamentos de Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Elium para operações de atualização. Selecione **Guardar** para cometer quaisquer alterações.

    ![Mapeamento de atributos entre Azure AD e Elium](media/Elium-provisioning-tutorial/userattribute.png)

1. Para configurar filtros de deteção, siga as instruções no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para ativar o serviço de provisionamento de AD Azure para o Elium, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estado de provisionamento definido para on](common/provisioning-toggle-on.png)

1. Defina os utilizadores e grupos que deseja fornecer ao Elium selecionando os valores que deseja na caixa de listas de drop-down scope na secção **Definições.**

    ![Caixa de lista de âmbito de provisionamento](common/provisioning-scope.png)

1. Quando estiver pronto para fornecer, selecione **Guardar**.

    ![Botão de guardar para configuração de fornecimento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** Este processo de sincronização inicial demora mais tempo do que as sincronizações posteriores. Para obter mais informações sobre o tempo necessário para o fornecimento, veja [quanto tempo demorará a fornecer aos utilizadores?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

Utilize a secção **Estado Atual** para monitorizar o progresso e seguir as ligações ao seu relatório de atividade de provisionamento. O relatório de atividade de provisionamento descreve todas as ações realizadas pelo serviço de provisionamento da AD Azure no Elium. Para mais informações, [consulte Verifique o estado do fornecimento do utilizador](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento de contas de utilizador para aplicações empresariais.](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
