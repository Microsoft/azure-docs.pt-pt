---
title: 'Tutorial: Configure Meta Networks Connector para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer e desfornecer automaticamente contas de utilizador ao Meta Networks Connector.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 6e34e540-7761-4bdf-a2f9-8c12becc119d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: 03c2dc6253fba5c2c7d59f3aefc5c1c663ed8248
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061365"
---
# <a name="tutorial-configure-meta-networks-connector-for-automatic-user-provisioning"></a>Tutorial: Configure Meta Networks Connector para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Meta Networks Connector e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer e desfornecer automaticamente utilizadores e/ou grupos para o Conector meta redes.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD Azure
* [Um inquilino de Conector meta redes](https://www.metanetworks.com/)
* Uma conta de utilizador no Conector meta redes com permissões de administrador.

## <a name="assigning-users-to-meta-networks-connector"></a>Atribuir utilizadores ao Conector meta redes

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Conector meta redes. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao Conector meta redes seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-meta-networks-connector"></a>Dicas importantes para atribuir utilizadores ao Conector meta redes

* Recomenda-se que um único utilizador da AD Azure seja atribuído ao Conector meta redes para testar a configuração automática de fornecimento do utilizador. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador ao Conector Meta Networks, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="setup-meta-networks-connector-for-provisioning"></a>Configurar o Conector meta redes para o fornecimento

1. Inscreva-se na consola de administrador a [conector meta redes](https://login.metanetworks.com/login/) utilizando o nome da sua organização. Navegue para **a Administração > Chaves API**.

    ![Consola de administradora de conector de redes meta](media/meta-networks-connector-provisioning-tutorial/apikey.png)

2.  Clique no sinal de mais no lado superior direito do ecrã para criar uma nova **Chave API**.

    ![Conector de Meta Networks mais ícone](media/meta-networks-connector-provisioning-tutorial/plusicon.png)

3.  Detete o nome da **chave API** e **a descrição da chave API**.

    ![Conector de redes de meta criar token](media/meta-networks-connector-provisioning-tutorial/keyname.png)

4.  Ligue os privilégios **de Escrita** para **Grupos** e **Utilizadores**.

    ![Privilégios de Conector de Redes Meta](media/meta-networks-connector-provisioning-tutorial/privileges.png)

5.  Clique em **Adicionar**. Copie o **SECRET** e guarde-o pois esta será a única altura em que poderá vê-lo. Este valor será inserido no campo Secret Token no separador provisionamento da sua aplicação Meta Networks Connector no portal Azure.

    ![Conector de redes de meta criar token](media/meta-networks-connector-provisioning-tutorial/token.png)

6.  Adicione um IDP navegando para **as definições de > de administração > idp > criar novo**.

    ![Conector de redes de meta adicionar IDP](media/meta-networks-connector-provisioning-tutorial/newidp.png)

7.  Na página **de Configuração IDP** pode **nomear** a configuração idp e escolher um **Ícone**.

    ![Nome idP do conector de redes meta](media/meta-networks-connector-provisioning-tutorial/idpname.png)

    ![Ícone idp do conector de redes meta](media/meta-networks-connector-provisioning-tutorial/icon.png)

8.  Em **Configurar o SCIM, selecione** o nome chave API criado nos passos anteriores. Clique em **Guardar**.

    ![Rede de Meta Rede configurar SCIM](media/meta-networks-connector-provisioning-tutorial/configure.png)

9.  Navegue para **a Administração > Definições > separador IDP**. Clique no nome da configuração IDP criada nos passos anteriores para visualizar o **ID Id**. Este **ID** é adicionado ao final do **URL** do Inquilino ao introduzir o valor no campo URL do **Arrendatário** no separador provisionamento da sua aplicação Meta Networks Connector no portal Azure.

    ![Id id id id do conector de redes meta](media/meta-networks-connector-provisioning-tutorial/idpid.png)

## <a name="add-meta-networks-connector-from-the-gallery"></a>Adicione o Conector meta networks da galeria

Antes de configurar o Conector Meta Networks para o fornecimento automático de utilizadores com a AD Azure, tem de adicionar o Conector Meta Networks da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar meta networks Connector da galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza o **Conector meta redes,** selecione **Meta Networks Connector** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Conector meta redes na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-meta-networks-connector"></a>Configurar o fornecimento automático de utilizadores ao Conector meta redes 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no Conector meta redes com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para o Conector meta networks, seguindo as instruções fornecidas no tutorial de inscrição single do [Conector](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial)de Meta Networks . O único sinal de inscrição pode ser configurado independentemente do fornecimento automático de utilizadores, embora estas duas funcionalidades se elogiem mutuamente

### <a name="to-configure-automatic-user-provisioning-for-meta-networks-connector-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o Conector meta redes em Azure AD:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Meta Networks Connector**.

    ![O link de conector meta redes na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção de `https://api.metanetworks.com/v1/scim/<IdP ID>` **Credenciais de Administrador,** insere-se no URL do **Arrendatário**. Insera o valor token de **autenticação SCIM** recuperado anteriormente em **Ficha Secreta**. Clique na **ligação de teste** para garantir que o Azure AD pode ligar-se ao Conector de Redes Meta. Se a ligação falhar, certifique-se de que a sua conta Deconector Meta Networks tem permissões de administrador e tente novamente.

    ![URL do inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Meta Networks Connector**.

    ![Mapeamento de utilizadores de conector de redes de meta](media/meta-networks-connector-provisioning-tutorial/usermappings.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Meta Networks Connector na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no Conector meta redes para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos do utilizador do conector de redes meta](media/meta-networks-connector-provisioning-tutorial/userattributes.png)

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Meta Networks Connector**.

    ![Mapeamentos de grupo de conector de redes meta](media/meta-networks-connector-provisioning-tutorial/groupmappings.png)

11. Reveja os atributos do grupo que são sincronizados de Azure AD para Meta Networks Connector na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar os grupos no Conector meta redes para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos do Grupo de Conector de Redes Meta](media/meta-networks-connector-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento de AD Azure para o Conector de Redes Meta, altere o Estado de **Provisionamento** para **Ligar** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja fornecer ao Conector meta redes, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividadede provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure no Meta Networks Connector.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

