---
title: 'Tutorial: Configurar StarLeaf para fornecimento automático de utilizadores com Diretório Ativo Azure Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores ao StarLeaf.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: zhchia
ms.openlocfilehash: 07c476c0de644ac63c577d466f4691b5cf415334
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357944"
---
# <a name="tutorial-configure-starleaf-for-automatic-user-provisioning"></a>Tutorial: Configurar StarLeaf para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no StarLeaf e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desavisionamento automática de utilizadores e/ou grupos ao StarLeaf.

> [!NOTE]
>  Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em Pré-Visualização. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino starLeaf.](https://starleaf.com/)
* Uma conta de utilizador no StarLeaf com permissões de Administração.

## <a name="assign-users-to-starleaf"></a>Atribuir utilizadores ao StarLeaf
O Azure Ative Directory utiliza um conceito chamado atribuições para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e grupos em Azure AD que precisam de acesso ao StarLeaf. Em seguida, pode atribuir os utilizadores e grupos ao StarLeaf seguindo [estas instruções](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-starleaf"></a>Dicas importantes para atribuir utilizadores ao StarLeaf

* Recomenda-se que um único utilizador AZure AD seja designado para o StarLeaf para testar a configuração automática de provisionamento do utilizador. Utilizadores e grupos adicionais podem ser atribuídos mais tarde.

* Quando atribuir um utilizador ao StarLeaf, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função Acesso Predefinido estão excluídos do provisionamento.

## <a name="set-up-starleaf-for-provisioning"></a>Configurar StarLeaf para provisionamento

Antes de configurar o StarLeaf para o fornecimento automático de utilizadores com Azure AD, terá de configurar o fornecimento scim no StarLeaf:

1. Inscreva-se na sua [Consola de Administração StarLeaf](https://portal.starleaf.com/#page=login). Navegar para **integrações**  >  **Adicionar integração.**

    ![Screenshot da Consola de Admin StarLeaf com as opções de integração e adicionar opções de integração chamadas.](media/starleaf-provisioning-tutorial/image00.png)

2. Selecione o **Tipo** para ser o Microsoft Azure Ative Directory. Insira um nome adequado no **Nome**. Clique em **Aplicar**.

    ![Screenshot da caixa de diálogo de integração Add com as caixas de texto Tipo e Nome chamadas.](media/starleaf-provisioning-tutorial/image01.png)

3.  Os valores **de base SCIM URL** e Access **token** serão então apresentados. Estes valores serão introduzidos nos campos **URL** e **Secret Token** no separador Provisioning da sua aplicação StarLeaf no portal Azure. 

    ![Screenshot da caixa de diálogo de integração editar com as caixas de texto URL de base Tipo, Nome e SCIM chamadas.](media/starleaf-provisioning-tutorial/image02.png)

## <a name="add-starleaf-from-the-gallery"></a>Adicione StarLeaf da galeria

Para configurar o StarLeaf para o fornecimento automático de utilizadores com Azure AD, é necessário adicionar o StarLeaf da galeria de aplicações AD AD do Azure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar StarLeaf da galeria de aplicações AZure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **o StarLeaf,** selecione **StarLeaf** no painel de resultados.
    ![StarLeaf na lista de resultados](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-starleaf"></a>Configure o fornecimento automático de utilizadores ao StarLeaf

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em StarLeaf com base em atribuições de utilizador e/ou grupo em Azure AD.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **StarLeaf**.

    ![O link StarLeaf na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção credenciais de administração, insira os valores **URL base SCIM** e **Access Token** recuperados anteriormente em URL de **Inquilino** e **Token Secreto,** respectivamente. Clique em **Testar A Ligação** para garantir que o Azure AD pode ligar-se ao StarLeaf. Se a ligação falhar, certifique-se de que a sua conta StarLeaf tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a **notificação enviar uma notificação por e-mail quando ocorrer uma falha** na caixa.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to StarLeaf**.

    ![Screenshot da secção Mappings mostrando os Utilizadores do Diretório Ativo Synchronize para a opção StarLeaf.](media/starleaf-provisioning-tutorial/usermapping.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD a StarLeaf na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no StarLeaf para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Screenshot da secção De mapeamentos de atributos mostrando nove mapeamentos exibidos.](media/starleaf-provisioning-tutorial/userattribute.png)


10. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


11. Para ativar o serviço de prestação de Ad Azure para o StarLeaf, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja prestar ao StarLeaf, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD Azure no StarLeaf.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Limitações do conector

* O StarLeaf não suporta atualmente o fornecimento de grupos. 
* O StarLeaf exige que os valores **de email** e nome **de utilizador** tenham o mesmo valor de origem.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento da conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* Saiba como [rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).
