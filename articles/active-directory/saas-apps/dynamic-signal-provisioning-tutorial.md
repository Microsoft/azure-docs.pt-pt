---
title: 'Tutorial: Configure Dynamic Signal para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Aprenda a configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores ao Dynamic Signal.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 2ec91d42dff8f3a1fc4b036aa1c3ec77faf6a0fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77058047"
---
# <a name="tutorial-configure-dynamic-signal-for-automatic-user-provisioning"></a>Tutorial: Configure o Sinal Dinâmico para o provisionamento automático do utilizador

O objetivo deste tutorial é demonstrar os passos a serem realizados no Dynamic Signal e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desavisagem automática de utilizadores e/ou grupos ao Sinal Dinâmico.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações saaS com diretório Azure Ative.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD AZure
* [Um inquilino de Sinal Dinâmico](https://dynamicsignal.com/)
* Uma conta de utilizador em Sinal Dinâmico com permissões de Administração.

## <a name="add-dynamic-signal-from-the-gallery"></a>Adicione sinal dinâmico da galeria

Antes de configurar o Sinal Dinâmico para o fornecimento automático de utilizadores com Azure AD, tem de adicionar o Sinal Dinâmico da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Sinal Dinâmico da galeria de aplicações AZure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **o Sinal Dinâmico,** selecione **Sinal Dinâmico** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Sinal Dinâmico na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-dynamic-signal"></a>Atribuir utilizadores ao Sinal Dinâmico

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Sinal Dinâmico. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos ao Sinal Dinâmico seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dynamic-signal"></a>Dicas importantes para atribuir utilizadores ao Sinal Dinâmico

* Recomenda-se que um único utilizador AD Azure seja designado para o Sinal Dinâmico para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao Sinal Dinâmico, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-dynamic-signal"></a>Configurar o fornecimento automático do utilizador ao Sinal Dinâmico 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Sinal Dinâmico com base nas atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar o único sinal de sinalização dinâmico baseado em SAML, seguindo as instruções fornecidas no [tutorial de assinatura único do Sinal Dinâmico](dynamicsignal-tutorial.md). O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-dynamic-signal-in-azure-ad"></a>Para configurar o fornecimento automático do utilizador para o Sinal Dinâmico em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Sinal Dinâmico**.

    ![O link de sinal dinâmico na lista de Aplicações](common/all-applications.png)

3. Selecione o **separador Provisioning.**

    ![Separador de provisionamento](common/provisioning.png)

4. Desa ajuste o **modo de provisionamento** para **automático**.

    ![Separador de provisionamento](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** insira o URL do **inquilino** e **o Token Secreto** da conta do seu Sinal Dinâmico, conforme descrito no Passo 6.

6. Na consola de administração De Sinal Dinâmico, navegue para **a API > de > Avançada de >**.

    ![Provisão de sinal dinâmico](./media/dynamic-signal-provisioning-tutorial/secret-token-1.png)

    Copie o **URL da API SCIM** para o **URL do inquilino.** Clique em **Gerar Novo Token** para gerar um **Token portador** e copiar o valor para Secret **Token**.

    ![Provisão de sinal dinâmico](./media/dynamic-signal-provisioning-tutorial/secret-token-2.png)

7. Ao povoar os campos mostrados no Passo 5, clique em **Test Connection** para garantir que o Azure AD pode ligar-se ao Sinal Dinâmico. Se a ligação falhar, certifique-se de que a sua conta De Sinal Dinâmico tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

8. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Dynamic Signal**.

    ![Mapeamentos dinâmicos do utilizador do sinal](media/dynamic-signal-provisioning-tutorial/user-mappings.png)

11. Reveja os atributos do utilizador que são sincronizados de AD AD a Sinal Dinâmico na secção **De Mapeamento** de Atributos. Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder às contas do utilizador no Sinal Dinâmico para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos dinâmicos do utilizador do sinal](media/dynamic-signal-provisioning-tutorial/user-mapping-attributes.png)

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para o Sinal Dinâmico, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de Provisionamento Toggled On](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar ao Sinal Dinâmico, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para a provisão, clique em **Guardar**.

    ![Configuração de provisionamento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD Azure no Sinal Dinâmico.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* O Sinal Dinâmico não suporta exclusões permanentes do Azure AD. Para eliminar permanentemente um utilizador no Sinal Dinâmico, a operação tem de ser feita através da UI da consola de administração Dynamic Signal. 
* O Sinal Dinâmico não suporta atualmente grupos.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Próximos passos

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

