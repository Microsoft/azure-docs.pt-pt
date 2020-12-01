---
title: 'Tutorial: Configurar Meta Networks Connector para fornecimento automático de utilizadores com Diretório Ativo Azure Microsoft Docs'
description: Aprenda a configurar o Azure Ative Directory para provisões automáticas e desa provisionar contas de utilizadores ao Meta Networks Connector.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: b6a8f192cd26639431cc9fcb6b43e1bc5e8e2843
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353634"
---
# <a name="tutorial-configure-meta-networks-connector-for-automatic-user-provisioning"></a>Tutorial: Configurar meta-redes connector para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Meta Networks Connector e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desavisagem automática de utilizadores e/ou grupos para o Meta Networks Connector.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD AZure
* [Um inquilino meta networks connector](https://www.metanetworks.com/)
* Uma conta de utilizador no Conector Meta Networks com permissões de Administração.

## <a name="assigning-users-to-meta-networks-connector"></a>Atribuir utilizadores ao Conector meta redes

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Meta Networks Connector. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos ao Conector Meta Networks seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-meta-networks-connector"></a>Dicas importantes para atribuir utilizadores ao Conector meta redes

* Recomenda-se que um único utilizador AZure AD seja designado para o Meta Networks Connector para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao Meta Networks Connector, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="setup-meta-networks-connector-for-provisioning"></a>Conector de redes meta de configuração para provisionamento

1. Inscreva-se na consola [de admin](https://login.metanetworks.com/login/) meta redes connector usando o nome da sua organização. Navegue para a **Administração > Chaves API**.

    ![Meta Networks Connector Admin Consola](media/meta-networks-connector-provisioning-tutorial/apikey.png)

2.  Clique no sinal de mais no lado superior direito do ecrã para criar uma nova **chave API**.

    ![Meta Networks Connector plus ícone](media/meta-networks-connector-provisioning-tutorial/plusicon.png)

3.  Desa estatudo o **nome da chave API** e **a descrição da chave API**.

    :::image type="content" source="media/meta-networks-connector-provisioning-tutorial/keyname.png" alt-text="Screenshot da Consola de Admin Meta Networks Connector com nome chave P I realçado e valores de descrição chave de Azure A D e A P I." border="false":::

4.  Ligue os privilégios **de escrita** para **grupos** e **utilizadores.**

    ![Privilégios de conector meta redes](media/meta-networks-connector-provisioning-tutorial/privileges.png)

5.  Clique em **Adicionar**. Copie o **SECRET** e guarde-o pois esta será a única altura em que poderá vê-lo. Este valor será introduzido no campo Secret Token no separador Provisioning da sua aplicação Meta Networks Connector no portal Azure.

    :::image type="content" source="media/meta-networks-connector-provisioning-tutorial/token.png" alt-text="Screenshot de uma janela a dizer aos utilizadores que a chave A P I foi adicionada. A caixa Secreta contém um valor indecifrável e é realçada." border="false":::

6.  Adicione um IdP navegando para **a Administração > Configurações > IdP > Criar Novo**.

    ![Meta Networks Connector Add IdP](media/meta-networks-connector-provisioning-tutorial/newidp.png)

7.  Na página **de Configuração IdP** pode **nomear** a sua configuração IdP e escolher um **Ícone**.

    ![Nome do IdP do conector de redes meta networks](media/meta-networks-connector-provisioning-tutorial/idpname.png)

    ![Ícone de IdP do conector de redes meta networks](media/meta-networks-connector-provisioning-tutorial/icon.png)

8.  Em **Configurar SCIM** selecione o nome chave API criado nos passos anteriores. Clique em **Guardar**.

    ![Meta Networks Connector configurar SCIM](media/meta-networks-connector-provisioning-tutorial/configure.png)

9.  Navegue para **a administração > Configurações > separador IdP**. Clique no nome da configuração IdP criada nos passos anteriores para ver o **IDP ID**. Este **ID** é adicionado ao final do URL do **inquilino** enquanto introduz o valor no campo URL do **inquilino** no separador Provisioning da sua aplicação Meta Networks Connector no portal Azure.

    ![ID do conector meta redes](media/meta-networks-connector-provisioning-tutorial/idpid.png)

## <a name="add-meta-networks-connector-from-the-gallery"></a>Adicione o Conector Meta Networks da galeria

Antes de configurar o Conector Meta Networks para o fornecimento automático de utilizadores com Azure AD, tem de adicionar o Conector Meta Networks da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

**Para adicionar o Conector Meta Networks da galeria de aplicações AZure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira o **Conector Meta Networks**, selecione **Meta Networks Connector** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Meta Networks Connector na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-meta-networks-connector"></a>Configurar o fornecimento automático do utilizador ao Conector meta redes 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no Meta Networks Connector com base nas atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar o único sinal de inscrição baseado em SAML para o Conector meta redes, seguindo as instruções fornecidas no tutorial de assinatura único do [Conector meta networks](./metanetworksconnector-tutorial.md). O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente

### <a name="to-configure-automatic-user-provisioning-for-meta-networks-connector-in-azure-ad"></a>Para configurar o fornecimento automático do utilizador para o Conector meta redes em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Meta Networks Connector**.

    ![O conector meta redes na lista de aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** entrada `https://api.metanetworks.com/v1/scim/<IdP ID>` na URL do **inquilino.** Insira o valor **de token de autenticação SCIM** recuperado anteriormente em **Secret Token**. Clique em **'Testar' Ligação** para garantir que o Azure AD pode ligar-se ao Conector Meta Networks. Se a ligação falhar, certifique-se de que a sua conta Meta Networks Connector tem permissões de Administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Meta Networks Connector**.

    ![Mapeamentos do utilizador do conector meta networks](media/meta-networks-connector-provisioning-tutorial/usermappings.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD a Meta Networks Connector na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador no Conector Meta Networks para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Meta Networks Connector User Atributos](media/meta-networks-connector-provisioning-tutorial/userattributes.png)

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to Meta Networks Connector**.

    ![Mapeamentos do grupo de conector de redes meta](media/meta-networks-connector-provisioning-tutorial/groupmappings.png)

11. Reveja os atributos do grupo que são sincronizados de Azure AD a Meta Networks Connector na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar com os grupos no Conector Meta Networks para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do grupo de conector meta networks](media/meta-networks-connector-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de AD Azure para o Conector meta redes, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar ao Meta Networks Connector, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD Azure no Conector meta networks.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)