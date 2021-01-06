---
title: 'Tutorial: Configurar Zscaler Beta para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Aprenda a configurar o Azure Ative Directory para provisões automáticas e desavisagem de contas de utilizadores à Zscaler Beta.
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
ms.openlocfilehash: 0d4945ee97a46c78aac3c4ac508c5f89f5942296
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937166"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>Tutorial: Configurar Zscaler Beta para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Zscaler Beta e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desavisionamento automática de utilizadores e/ou grupos à Zscaler Beta.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>


## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem o seguinte:

* Um inquilino da AD AZure
* Um inquilino Beta Zscaler
* Uma conta de utilizador em Zscaler Beta com permissões de administração

> [!NOTE]
> A integração de provisionamento Azure AD depende da Zscaler Beta SCIM API, que está disponível para os desenvolvedores Beta da Zscaler para contas com o pacote Enterprise.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Adicionar Zscaler Beta da galeria

Antes de configurar a Versão Beta do Zscaler para o fornecimento automático de utilizadores com Azure AD, tem de adicionar zscaler Beta da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Zscaler Beta da galeria de aplicações AZure, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, tipo **Zscaler Beta**, selecione **Zscaler Beta** do painel de resultados e clique em **Adicionar** botão para adicionar a aplicação.

    ![Zscaler Beta na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>Atribuir utilizadores à Zscaler Beta

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram "atribuídos" a uma aplicação em AD Azure são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso à Zscaler Beta. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos à Zscaler Beta seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>Dicas importantes para atribuir utilizadores à Zscaler Beta

* Recomenda-se que um único utilizador AZure AD seja designado para a Zscaler Beta para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador à Zscaler Beta, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>Configurar o fornecimento automático de utilizadores à Zscaler Beta

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Zscaler Beta com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar o sign-on único baseado em SAML para Zscaler Beta, seguindo as instruções fornecidas no [tutorial de assinatura única Zscaler Beta.](zscaler-beta-tutorial.md) O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

> [!NOTE]
> Quando os utilizadores e grupos são provisionados ou des provisionados, recomendamos reiniciar periodicamente o provisionamento para garantir que os membros do grupo sejam corretamente atualizados. Fazer um reinício forçará o nosso serviço a reavaliar todos os grupos e a atualizar os membros.  

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para zscaler Beta em Ad AZure:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **Zscaler Beta**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Zscaler Beta**.

    ![O link Beta Zscaler na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Há uma lista de separadores dispostos em categorias, e intitulado ZScaler Beta - Provisioning /Enterprise Application. É selecionado o separador Provisão da categoria Gestão.](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![O modo Automático foi selecionado a partir da lista de abandono do modo de provisionamento. Existem campos para credenciais de administração, usados para ligar à API da Zscaler Beta, e há um botão de Ligação de Teste.](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. Sob a secção **de Credenciais de Administrador,** insira o URL do **inquilino** e **o Token Secreto** da sua conta Zscaler Beta, conforme descrito no Passo 6.

6. Para obter o URL do **inquilino** e **o Token Secreto,** navegue para **a Administração > Configurações de Autenticação** na interface do portal Zscaler Beta e clique em **SAML** sob **Tipo de Autenticação**.

    ![Nas Definições de Autenticação, no Perfil de Autenticação, o Tipo de Diretório selecionado é Apresentado DB, e o Tipo de Autenticação Selecionado é SAML.](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    Clique em **Configurar SAML** para abrir as opções **SAML de configuração.**

    ![No Configure SAML, são selecionadas as opções de Auto-Provisionamento de Ativação SAML e de Enable SCIM-Based Provisioning. As caixas de texto base URL e Bearer Token são destacadas.](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    **Selecione Ative SCIM-Based Provisioning** para recuperar **o URL base** e **o Token do portador** e, em seguida, guarde as definições. Copie o **URL base** para o URL **do inquilino,** e **o Bearer Token**  para **Secret Token** no portal Azure.

7. Ao povoar os campos mostrados no Passo 5, clique em **Test Connection** para garantir que a Azure AD pode ligar-se à Zscaler Beta. Se a ligação falhar, certifique-se de que a sua conta Beta Zscaler tem permissões de Administração e tente novamente.

    ![Nas credenciais de administrador, os campos URL e Secret Token têm valores, e o botão de Ligação de Teste é realçado.](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação **Enviar por e-mail uma notificação de e-mail quando ocorrer uma falha**.

    ![A caixa de texto de Email de Notificação está vazia e o Envio de uma notificação por e-mail quando ocorre uma falha na caixa de verificação é apurada.](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. Clique em **Guardar**.

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para Zscaler Beta**.

    ![O Synchronize Azure Ative Directory Users para ZScalerBeta está selecionado e habilitado.](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. Reveja os atributos do utilizador que são sincronizados de Azure AD a Zscaler Beta na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder às contas do utilizador em Zscaler Beta para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Na secção De mapeamentos de Atributos para atributos do utilizador, os atributos Ative Directory são apresentados ao lado dos atributos Zscalar Beta com os quais estão sincronizados. Um par de atributos é mostrado como Matching.](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to Zscaler Beta**.

    ![O Synchronize Azure Ative Directory Groups para ZScalerBeta está selecionado e habilitado.](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. Reveja os atributos do grupo que são sincronizados de Azure AD a Zscaler Beta na secção **De Mapeamentos de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder aos grupos em Zscaler Beta para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Na secção De mapeamentos de Atributos para atributos de grupo, os atributos Ative Directory são apresentados ao lado dos atributos Zscalar Beta com os quais estão sincronizados. Um par de atributos é mostrado como Matching.](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para ativar o serviço de fornecimento de Ad Azure para Zscaler Beta, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![O Estado de Provisionamento é mostrado e definido para On.](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. Defina os utilizadores e/ou grupos que deseja prestar à Zscaler Beta, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![A lista de drop-down scope é mostrada e o Sync apenas é selecionado por utilizadores e grupos designados. O outro valor disponível é Sync todos os utilizadores e grupos.](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Destaca-se o botão Guardar no topo da Zscaler Beta - Provisioning. Há também um botão Descartar.](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD Azure em Zscaler Beta.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png