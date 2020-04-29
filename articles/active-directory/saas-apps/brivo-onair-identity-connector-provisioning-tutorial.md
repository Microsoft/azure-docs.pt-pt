---
title: 'Tutorial: Configure O Conector de Identidade Brivo Onair para o fornecimento automático de utilizadores com diretório ativo Azure [ ) Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo azure para fornecer e desfornecer automaticamente contas de utilizadores ao Brivo Onair Identity Connector.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 542ce04c-ef7d-4154-9b0e-7f68e1154f6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: f3c1e7337c0ce07b7fbebb9f954deeb75f0b9584
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80246658"
---
# <a name="tutorial-configure-brivo-onair-identity-connector-for-automatic-user-provisioning"></a>Tutorial: Configure o Conector de Identidade Brivo Onair para o fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Brivo Onair Identity Connector e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer e desfornecer automaticamente utilizadores e/ou grupos para o Conector de Identidade Brivo Onair.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD Azure
* [Um inquilino do Conector de Identidade Brivo Onair](https://www.brivo.com/lp/quote)
* Uma conta de utilizador no Conector de Identidade Brivo Onair com permissões do Administrador Sénior.

## <a name="assigning-users-to-brivo-onair-identity-connector"></a>Atribuir utilizadores ao Conector de Identidade Brivo Onair

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos da AD Azure que precisam de acesso ao Conector de Identidade Brivo Onair. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao Conector de Identidade Brivo Onair seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-brivo-onair-identity-connector"></a>Dicas importantes para atribuir utilizadores ao Conector de Identidade Brivo Onair

* Recomenda-se que um único utilizador da AD Azure seja atribuído ao Conector de Identidade Brivo Onair para testar a configuração automática de provisionamento do utilizador. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador ao Conector de Identidade Brivo Onair, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="setup-brivo-onair-identity-connector-for-provisioning"></a>Configurar o Conector de Identidade Brivo Onair para o fornecimento

1.    Inscreva-se na consola de administradora do [Conector de Identidade Brivo Onair](https://acs.brivo.com/login/). Navegar para **conta > Definições**de conta .

    ![Consola de administradora de conector de identidade Brivo Onair](media/brivo-onair-identity-connector-provisioning-tutorial/admin.png)

2.  Clique no separador **Azure AD.** Na página de detalhes da **AD Azure,** reintroduza a palavra-passe da sua conta de administrador sénior. Clique em **Submeter**.

    ![Brivo Onair Identity Connector azure](media/brivo-onair-identity-connector-provisioning-tutorial/azuread.png)

3.    Clique no botão **Copy Token** e guarde o **Token Secreto**. Este valor será inserido no campo Secret Token no separador de provisionamento da sua aplicação de Conector de Identidade Brivo Onair no portal Azure.

    ![Ficha de ficha de conector de identidade Brivo Onair](media/brivo-onair-identity-connector-provisioning-tutorial/token.png)

## <a name="add-brivo-onair-identity-connector-from-the-gallery"></a>Adicione o Conector de Identidade Brivo Onair da galeria

Antes de configurar o Conector de Identidade Brivo Onair para o fornecimento automático de utilizadores com a AD Azure, é necessário adicionar o Conector de Identidade Brivo Onair da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar o Conector de Identidade Brivo Onair da galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza o **Conector de Identidade Brivo Onair,** selecione o **Conector de Identidade Brivo Onair** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Conector de identidade Brivo Onair na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-brivo-onair-identity-connector"></a>Configurar o fornecimento automático de utilizadores ao Conector de Identidade Brivo Onair 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no Connector de Identidade Brivo Onair com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-brivo-onair-identity-connector-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o Conector de Identidade Brivo Onair em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Brivo Onair Identity Connector**.

    ![O link do Conector de Identidade Brivo Onair na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção de `https://scim.brivo.com/ActiveDirectory/v2/` **Credenciais de Administrador,** insere-se no URL do **Arrendatário**. Insera o valor token de **autenticação SCIM** recuperado anteriormente em **Ficha Secreta**. Clique na **ligação de teste** para garantir que o Azure AD pode ligar-se ao Conector de Identidade Brivo Onair. Se a ligação falhar, certifique-se de que a sua conta de Conector de Identidade Brivo Onair tem permissões de administrador e tente novamente.

    ![URL do inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Brivo Onair Identity Connector**.

    ![Mapeamento do utilizador do conector de identidade Brivo Onair](media/brivo-onair-identity-connector-provisioning-tutorial/user-mappings.png )

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Brivo Onair Identity Connector na secção **Demapeamento do Atributo.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no Conector de Identidade Brivo Onair para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos do utilizador do conector de identidade Brivo Onair](media/brivo-onair-identity-connector-provisioning-tutorial/user-attributes.png)

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups para Brivo Onair Identity Connector**.

    ![Mapeamentos do grupo de conector de identidade Brivo Onair](media/brivo-onair-identity-connector-provisioning-tutorial/group-mappings.png)

11. Reveja os atributos do grupo que são sincronizados de Azure AD para Brivo Onair Identity Connector na secção **Demapeamento do Atributo.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar os grupos no Conector de Identidade Brivo Onair para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Brivo Onair Identity Connector Group Atribui](media/brivo-onair-identity-connector-provisioning-tutorial/group-attributes.png)

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento de AD Azure para o Conector de Identidade Brivo Onair, altere o Estado de **Provisionamento** para **Ligar** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja fornecer ao Conector de Identidade Brivo Onair, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividadede provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento da AD Azure no Conector de Identidade Brivo Onair.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

