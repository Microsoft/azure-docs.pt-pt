---
title: 'Tutorial: Configurar Workgrid para fornecimento automático de utilizadores com Diretório Ativo Azure Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desagregativa de contas de utilizador à Workgrid.
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
ms.openlocfilehash: ee656f147bd27ff782121f088a239a7337d81075
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359770"
---
# <a name="tutorial-configure-workgrid--for-automatic-user-provisioning"></a>Tutorial: Configurar workgrid para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Workgrid e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desagregamento automaticamente de utilizadores e/ou grupos à Workgrid.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino workgrid](https://www.workgrid.com/)
* Uma conta de utilizador em Workgrid com permissões de Administração.

## <a name="assigning-users-to-workgrid"></a>Atribuir utilizadores à Workgrid 

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Workgrid. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos à Workgrid seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-workgrid"></a>Dicas importantes para atribuir utilizadores à Workgrid 

* Recomenda-se que um único utilizador AZure AD seja designado para a Workgrid para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador à Workgrid, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="set-up-workgrid-for-provisioning"></a>Criar Workgrid para provisionamento

Antes de configurar o Workgrid para o fornecimento automático de utilizadores com Azure AD, terá de permitir o fornecimento scim no Workgrid.

1. Faça login no Workgrid. Navegue para **utilizadores > fornecimento de utilizadores.**

    ![Screenshot do Workgrid U I com as opções de Provisionamento de Utilizadores e Desempaçamento do Utilizador chamadas.](media/Workgrid-provisioning-tutorial/user.png)

2. Na **API de Gestão de Conta,** clique em **Criar Credenciais.**

    ![Screenshot da secção Gestão de Conta Um P I com a opção Criar Credenciais chamada.](media/Workgrid-provisioning-tutorial/scim.png)

3. Copie os **valores do ponto final scim** e **do token de acesso.** Estes serão inseridos no campo **URL** e **Secret Token** no separador Provisioning da sua aplicação Workgrid no portal Azure.

    ![Screenshot da gestão de conta Uma secção P I com S C I M Endpoint e Access Token chamados.](media/Workgrid-provisioning-tutorial/token.png)


## <a name="add-workgrid--from-the-gallery"></a>Adicione Workgrid da galeria

Para configurar workgrid para fornecimento automático de utilizadores com Azure AD, você precisa adicionar Workgrid da galeria de aplicações AD AD Azure à sua lista de aplicações geridas saaS.

**Para adicionar Workgrid da galeria de aplicações AZure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Workgrid,** selecione **Workgrid** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Workgrid na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-workgrid"></a>Configurar o fornecimento automático de utilizadores à Workgrid  

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Workgrid com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar o sign-on único baseado em SAML para workgrid, seguindo as instruções fornecidas no [tutorial de inscrição single workgrid](Workgrid-tutorial.md). O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente

### <a name="to-configure-automatic-user-provisioning-for-workgrid--in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para a Workgrid em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Workgrid**.

    ![O link Workgrid na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção credenciais de administração, insira os valores **scim Endpoint** e **Access Token** recuperados anteriormente em URL de **inquilino** e **Token Secreto,** respectivamente. Clique em **Testar a Ligação** para garantir que o Azure AD pode ligar-se ao Workgrid. Se a ligação falhar, certifique-se de que a sua conta Workgrid tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Workgrid**.

    ![Screenshot da secção Mappings com a opção Synchronize Azure Ative Directory Users to Workgrid chamada.](media/Workgrid-provisioning-tutorial/usermapping.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD a Workgrid na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no Workgrid para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do utilizador workgrid](media/Workgrid-provisioning-tutorial/userattribute.png)

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to Workgrid**

    ![Screenshot da secção Mappings com a opção Synchronize Azure Ative Directory Groups to Workgrid chamada.](media/Workgrid-provisioning-tutorial/groupmapping.png)

12. Reveja os atributos do grupo que são sincronizados de Azure AD a Workgrid na secção **De mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no Workgrid para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Screenshot da secção De mapeamentos de atributos com três mapeamentos exibidos.](media/Workgrid-provisioning-tutorial/groupattribute.png)

13. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para ativar o serviço de prestação de Ad Azure para workgrid, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

15. Defina os utilizadores e/ou grupos que deseja prestar à Workgrid, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

16. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser efetua do que as sincronizações subsequentes. Para obter mais informações sobre o tempo que os utilizadores e/ou grupos demorarão a providenciar, consulte [quanto tempo demorará a providenciar aos utilizadores](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Pode utilizar a secção **Estado Atual** para monitorizar o progresso e seguir links para o seu relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de Azure AD em Workgrid. Para obter mais informações, [consulte verifique o estado do fornecimento do utilizador](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os registos de provisionamento da AD Azure, consulte [reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
