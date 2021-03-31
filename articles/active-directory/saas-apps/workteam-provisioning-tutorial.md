---
title: 'Tutorial: Configurar a workteam para o fornecimento automático de utilizadores com o Azure Ative Directory | Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionamento de contas de utilizador à Workteam.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/17/2019
ms.author: Zhchia
ms.openlocfilehash: f465438a6be80a1b487a4df7d3ee8bd38be9c102
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94354233"
---
# <a name="tutorial-configure-workteam--for-automatic-user-provisioning"></a>Tutorial: Configure Workteam para o fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados na Workteam e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desa provisionamento automaticamente de utilizadores e/ou grupos à Workteam.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino da workteam](https://workte.am/pricing.html)
* Uma conta de utilizador em Workteam com permissões de Administração.

## <a name="assigning-users-to-workteam"></a>Atribuir utilizadores à Workteam 

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso à workteam. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos à Workteam seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-workteam"></a>Dicas importantes para atribuir utilizadores à Workteam 

* Recomenda-se que um único utilizador Azure AD seja designado para o Workteam para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador à Workteam, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="setup-workteam--for-provisioning"></a>Equipa de Trabalho de Configuração para provisionamento

Antes de configurar a workteam para o fornecimento automático de utilizadores com Azure AD, terá de permitir o fornecimento de SCIM na Workteam.

1. Faça login na [workteam](https://app.workte.am/account/signin). Clique **nas definições da Organização**  >  **Definições**.

    ![Screenshot da workteam U I com as definições da Organização e opções DE DEFINIÇÃO chamadas.](media/workteam-provisioning-tutorial/settings.png)

2. Percorra para baixo e ative as capacidades de provisionamento da Workteam.

    ![Screenshot da parte inferior da secção DEFINIÇÕES com o ícone de engrenagem de provisionamento do utilizador S C I M chamado.](media/workteam-provisioning-tutorial/icon.png)

3. Copie o **Url base** e **o Token do Portador.** Estes valores serão inseridos no campo **URL** e **Secret Token** no separador Provisioning da sua aplicação Workteam no portal Azure.

    ![Screenshot da caixa de diálogo S C I M Configurações com as caixas de texto BASE U R L e BEARER TOKEN chamadas.](media/workteam-provisioning-tutorial/scim.png)


## <a name="add-workteam--from-the-gallery"></a>Adicionar Workteam da galeria

Para configurar o Workteam para o fornecimento automático de utilizadores com Azure AD, é necessário adicionar workteam da galeria de aplicações AD AD do Azure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar workteam da galeria de aplicações AZure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **introduza workteam,** selecione **Workteam** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Workteam na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-workteam"></a>Configurar o fornecimento automático de utilizadores à Workteam  

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos na Workteam com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar o sign-on único baseado em SAML para workteam, seguindo as instruções fornecidas no [tutorial de inscrição única da workteam.](workteam-tutorial.md) O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente

### <a name="to-configure-automatic-user-provisioning-for-workteam--in-azure-ad"></a>Para configurar o provisionamento automático do utilizador para workteam em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Workteam**.

    ![O link Workteam na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção credenciais de administração, insira os valores **de BASE URL** e **Bearer Token** recuperados anteriormente em URL de **Inquilino** e **Token Secreto,** respectivamente. Clique em **Testar a Ligação** para garantir que o Azure AD pode ligar-se à equipa de trabalho. Se a ligação falhar, certifique-se de que a sua conta Workteam tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Workteam**.

    ![Mapeamentos de utilizadores de workteam](media/workteam-provisioning-tutorial/usermapping.png)

9. Reveja os atributos do utilizador que são sincronizados de AD AD a Workteam na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador na Workteam para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do utilizador workteam](media/workteam-provisioning-tutorial/userattribute.png)

11. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para ativar o serviço de prestação de Ad Azure para a equipa de trabalho, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

13. Defina os utilizadores e/ou grupos que deseja prestar à Workteam, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser efetua do que as sincronizações subsequentes. Para obter mais informações sobre o tempo que os utilizadores e/ou grupos demorarão a providenciar, consulte [quanto tempo demorará a providenciar aos utilizadores](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Pode utilizar a secção **Estado Atual** para monitorizar o progresso e seguir links para o seu relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de Ad Azure na workteam. Para obter mais informações, [consulte verifique o estado do fornecimento do utilizador](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os registos de provisionamento da AD Azure, consulte [reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
