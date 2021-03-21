---
title: 'Tutorial: Configurar zscaler para fornecimento automático de utilizadores com Azure Ative Directory | Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores à Zscaler.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 9f368a4aebc4d5de38ebbab800241366650633e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936588"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Tutorial: Configurar Zscaler para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Diretório Ativo Zscaler e Azure (Azure AD) para configurar a Azure AD para fornecimento e desavisionamento automática de utilizadores e/ou grupos à Zscaler.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem o seguinte:

* Um inquilino do Azure AD.
* Um inquilino de Zscaler.
* Uma conta de utilizador em Zscaler com permissões de Administração.

> [!NOTE]
> A integração de provisionamento Azure AD depende da Zscaler SCIM API, que está disponível para os desenvolvedores da Zscaler para contas com o pacote Enterprise.

## <a name="adding-zscaler-from-the-gallery"></a>Adicionando Zscaler da galeria

Antes de configurar o Zscaler para o fornecimento automático de utilizadores com Azure AD, tem de adicionar zscaler da galeria de aplicações AD AD do Azure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Zscaler da galeria de aplicações AZure, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, tipo **Zscaler**, selecione **Zscaler** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Zscaler na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Atribuir utilizadores à Zscaler

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram "atribuídos" a uma aplicação em AD Azure são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Zscaler. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos à Zscaler seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Dicas importantes para atribuir utilizadores à Zscaler

* Recomenda-se que um único utilizador AZure AD seja atribuído à Zscaler para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador a Zscaler, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Configurar o fornecimento automático do utilizador à Zscaler

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Zscaler com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar o sign-on único baseado em SAML para zscaler, seguindo as instruções fornecidas no [tutorial de inscrição única Zscaler](zscaler-tutorial.md). O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

> [!NOTE]
> Quando os utilizadores e grupos são provisionados ou des provisionados, recomendamos reiniciar periodicamente o provisionamento para garantir que os membros do grupo sejam corretamente atualizados. Fazer um reinício forçará o nosso serviço a reavaliar todos os grupos e a atualizar os membros. 

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para zscaler em Azure AD:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **Zscaler**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Zscaler**.

    ![O link Zscaler na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot da barra lateral Zscaler - Provisioning Enterprise Application com a opção Provisioning realçada.](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da página de provisionamento com o modo de provisionamento definido para Automático.](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. Sob a secção **de Credenciais de Administrador,** insira o URL do **inquilino** e **o Token Secreto** da sua conta Zscaler, conforme descrito no Passo 6.

6. Para obter o URL do **inquilino** e **o Token Secreto,** navegue para **a Administração > Configurações de Autenticação** na interface de utilizador do portal Zscaler e clique em **SAML** em **Tipo de Autenticação**.

    ![Screenshot da página Definições de Autenticação.](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    Clique em **Configurar SAML** para abrir opções **SAML de configuração.**

    ![Screenshot da caixa de diálogo Configure S A M L com as caixas de texto Base U R L e Bearer Token chamadas.](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    **Selecione Ative SCIM-Based Provisioning** para recuperar **o URL base** e **o Token do portador** e, em seguida, guarde as definições. Copie o **URL base** para o URL **do inquilino,** e **o Bearer Token**  para **Secret Token** no portal Azure.

7. Ao povoar os campos mostrados no Passo 5, clique em **Test Connection** para garantir que o Azure AD pode ligar-se ao Zscaler. Se a ligação falhar, certifique-se de que a sua conta Zscaler tem permissões de Administração e tente novamente.

    ![Screenshot da secção credenciais de administrador com a opção de ligação de teste chamada.](./media/zscaler-provisioning-tutorial/test-connection.png)

8. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação **Enviar por e-mail uma notificação de e-mail quando ocorrer uma falha**.

    ![Screenshot da caixa de texto de e-mail de notificação.](./media/zscaler-provisioning-tutorial/notification.png)

9. Clique em **Guardar**.

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para Zscaler**.

    ![Screenshot da secção Mappings com a opção Synchronize Azure Ative Directory Users to Zscaler.](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. Reveja os atributos do utilizador que são sincronizados de Azure AD a Zscaler na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador em Zscaler para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Screenshot da secção De mapeamentos de atributos com sete mapeamentos exibidos.](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to Zscaler**.

    ![Screenshot da secção Mappings com a opção Synchronize Azure Ative Directory Groups to Zscaler.](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. Reveja os atributos do grupo que são sincronizados de Azure AD a Zscaler na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar com os grupos em Zscaler para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Screenshot da secção De mapeamentos de atributos com três mapeamentos exibidos.](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para ativar o serviço de prestação de Ad Azure para zscaler, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Screenshot da opção Estado de Provisionamento definida para On.](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. Defina os utilizadores e/ou grupos que deseja prestar à Zscaler, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Screenshot da definição de Âmbito com a opção sync apenas atribuída a utilizadores e grupos destacados.](./media/zscaler-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Screenshot do Zscaler - Provisioning Enterprise Application sidebar com a opção Save chamada.](./media/zscaler-provisioning-tutorial/save-provisioning.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD Azure em Zscaler.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png