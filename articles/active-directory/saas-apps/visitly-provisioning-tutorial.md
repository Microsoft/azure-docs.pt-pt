---
title: 'Tutorial: Configurar Visita visitly para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desprovisionar as contas dos utilizadores ao Visitly.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 15031a3e139265410179baa13db9ed3f1b41cf17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88531808"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>Tutorial: Configurar Visita visitly para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos que executa no Diretório Ativo visitly e Azure (AD) para configurar a Azure AD para provisão automática e deprovisionamento de utilizadores ou grupos para o Visitly.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do serviço de fornecimento de utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [o fornecimento e desprovisionamento de utilizadores da Automamate para aplicações software-as-a-service (SaaS) com diretório Azure Ative.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD AZure
* [Um inquilino visitante](https://www.visitly.io/pricing/)
* Uma conta de utilizador em Visitly com permissões de administração

## <a name="assign-users-to-visitly"></a>Atribuir utilizadores ao Visitly 

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, decida quais os utilizadores ou grupos em Azure AD que precisam de acesso ao Visitly. Em seguida, atribua estes utilizadores ou grupos ao Visitly seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>Dicas importantes para atribuir utilizadores ao Visitly 

* Recomendamos que atribua um único utilizador Azure AD ao Visitly para testar a configuração automática de provisionamento do utilizador. Utilizadores ou grupos adicionais podem ser atribuídos mais tarde.

* Quando atribui um utilizador ao Visitly, tem de selecionar qualquer função específica de aplicação válida (se disponível) na caixa de diálogo de atribuição. Os utilizadores com a função Acesso Predefinido estão excluídos do provisionamento.

## <a name="set-up-visitly-for-provisioning"></a>Configurar Visitly para provisionamento

Antes de configurar o Visitly para o fornecimento automático de utilizadores com Azure AD, precisa de ativar o fornecimento do Sistema de Gestão de Identidade de Domínio Cruzado (SCIM) no Visitly.

1. Inscreva-se no [Visitly.](https://app.visitly.io/login) Selecione **Integrações**  >  **Host Synchronization**.

    ![Sincronização do anfitrião](media/Visitly-provisioning-tutorial/login.png)

2. Selecione a secção **AD Azure.**

    ![Seção AD de Azure](media/Visitly-provisioning-tutorial/integration.png)

3. Copie a **chave API**. Estes valores são introduzidos na caixa **Secret Token** no **separador Provisioning** da sua aplicação Visitly no portal Azure.

    ![Chave de API](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>Adicione Visitly da galeria

Para configurar o Pedido de Utilização Automática de Utilizadores com Azure AD, adicione Visitly da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

Para adicionar Visitly da galeria de aplicações Azure AD, siga estes passos.

1. No [portal Azure,](https://portal.azure.com)no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Visitly,** selecione **Visitly** no painel de resultados e, em seguida, selecione **Adicionar** para adicionar a aplicação.

    ![Visita na lista de resultados](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-visitly"></a>Configure o fornecimento automático de utilizadores ao Visitly 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores ou grupos em Visitly com base em atribuições de utilizador ou grupo em Azure AD.

> [!TIP]
> Para ativar o sign-on único baseado em SAML para o Visitly, siga as instruções no [tutorial de inscrição única visitly](Visitly-tutorial.md). O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se complementem.

### <a name="configure-automatic-user-provisioning-for-visitly-in-azure-ad"></a>Configure o fornecimento automático de utilizadores para o Visitly em Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais**  >  **Todas as aplicações**.

    ![Todas as aplicações](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Visitly**.

    ![O link Visitly na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Separador Aprovisionamento](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Modo de Provisionamento definido para Automático](common/provisioning-automatic.png)

5. Sob a secção Credenciais de Administração, insira os `https://api.visitly.io/v1/usersync/SCIM` valores chave e **API** recuperados anteriormente em URL de **Inquilino** e **Token Secreto,** respectivamente. Selecione **a Ligação de Teste** para garantir que o Azure AD pode ligar-se ao Visitly. Se a ligação falhar, certifique-se de que a sua conta Visitly tem permissões de administração e tente novamente.

    ![USA do inquilino + token](common/provisioning-testconnection-tenanturltoken.png)

6. Na caixa de **e-mail de notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento. Selecione a **notificação Enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![E-mail de notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para Visitar.**

    ![Mapeamentos de utilizadores visitados](media/visitly-provisioning-tutorial/usermapping.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD a Visitly na secção **De Mapeamentos de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador no Visitly para operações de atualização. **Selecione Guardar** para escoar quaisquer alterações.

    ![Atributos de utilizador visitário](media/visitly-provisioning-tutorial/userattribute.png)

10. Para configurar filtros de deteção, siga as instruções no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de prestação de Ad Azure para o Visitly, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de Provisionamento alternado](common/provisioning-toggle-on.png)

12. Defina os utilizadores ou grupos que pretende prever ao Visitly, escolhendo os valores pretendidos no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para a provisão, **selecione Save**.

    ![Configuração de provisão de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser efetua do que as sincronizações subsequentes. Para obter mais informações sobre o tempo que os utilizadores ou grupos demoram a providenciar, consulte [quanto tempo demorará a providenciar aos utilizadores?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

Pode utilizar a secção **Estado Atual** para monitorizar o progresso e seguir links para o seu relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de Ad Azure em Visitly. Para obter mais informações, [consulte verifique o estado do fornecimento do utilizador](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os registos de provisionamento da AD Azure, consulte [reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

Visitly não suporta eliminações duras. Tudo é macio apenas apagar.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento da conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
