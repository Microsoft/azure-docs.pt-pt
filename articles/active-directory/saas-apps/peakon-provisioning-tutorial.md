---
title: 'Tutorial: Configurar o fornecimento automático de utilizadores da Peakon com o Azure Ative Directory / Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores à Peakon .
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: b16ca0c396e2c406677af351cf6b1de2a3499353
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543678"
---
# <a name="tutorial-configure-peakon-for-automatic-user-provisioning"></a>Tutorial: Configurar Peakon para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados em Peakon e Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecimento e desavisagem automática de utilizadores e/ou grupos para Peakon.

> [!NOTE]
>  Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações saaS com diretório Azure Ative.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-Visualização. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos

* Um inquilino do Azure AD.
* [Um inquilino de Peakon.](https://peakon.com/us/pricing/)
* Uma conta de utilizador em Peakon com permissões de administração.

## <a name="assigning-users-to-peakon"></a>Atribuir utilizadores a Peakon

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Peakon. Uma vez decididos, pode atribuir estes utilizadores e/ou grupos a Peakon seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-peakon"></a>Dicas importantes para atribuir utilizadores a Peakon 

* Recomenda-se que um único utilizador AD Azure seja designado para Peakon para testar a configuração automática de provisionamento do utilizador. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador a Peakon, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="set-up-peakon-for-provisioning"></a>Configurar Peakon para provisão

1.  Inscreva-se na sua [Consola de Administração Peakon.](https://app.Peakon.com/login) Clique na **Configuração**. 

    ![Consola Peakon Admin](media/Peakon-provisioning-tutorial/Peakon-admin-configuration.png)

2.  Selecione **Integrações**.
    
    ![Provisão de empregados de Peakon](media/Peakon-provisioning-tutorial/Peakon-select-integration.png)

3.  Permitir o **Provisionamento de Empregados**.

    ![Provisão de empregados de Peakon](media/Peakon-provisioning-tutorial/peakon05.png)

4.  Copie os valores para **SCIM 2.0 URL** e **OAuth Bearer Token**. Estes valores serão introduzidos no campo URL do **Inquilino** e No Campo **Secreto token** no separador Provisioning da sua aplicação Peakon no portal Azure.

    ![Peakon Criar Token](media/Peakon-provisioning-tutorial/peakon04.png)

## <a name="add-peakon-from-the-gallery"></a>Adicione Peakon da galeria

Para configurar o Peakon para o fornecimento automático de utilizadores com Azure AD, é necessário adicionar peakon da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Peakon,** selecione **Peakon** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Peakon na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-peakon"></a>Configurar o fornecimento automático de utilizadores a Peakon 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Peakon com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar o sign-on único baseado em SAML para Peakon, seguindo as instruções fornecidas no [tutorial de assinatura Peakon Single](peakon-tutorial.md). O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-peakon--in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para Peakon em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Peakon**.

    ![O link Peakon na lista de Aplicações](common/all-applications.png)

3. Selecione o **separador Provisioning.**

    ![Separador de provisionamento](common/provisioning.png)

4. Desa ajuste o **modo de provisionamento** para **automático**.

    ![Separador de provisionamento](common/provisioning-automatic.png)

5. Sob a secção **credenciais de administração,** insira os valores **SCIM 2.0 URL** e **OAuth Bearer Token** recuperados anteriormente em **URL** de inquilino e **Token Secreto,** respectivamente. Clique em **Testar a Ligação** para garantir que o Azure AD pode ligar-se a Peakon. Se a ligação falhar, certifique-se de que a sua conta Peakon tem permissões de administração e tente novamente.

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)

7. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de notificação](common/provisioning-notification-email.png)

8. Clique em **Guardar**.

9. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Peakon**.

    ![Mapeamentos de utilizadores de Peakon](media/Peakon-provisioning-tutorial/Peakon-user-mappings.png)

10. Reveja os atributos do utilizador que são sincronizados de AD AD a Peakon na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador em Peakon para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

    ![Atributos do utilizador Peakon](media/Peakon-provisioning-tutorial/Peakon-user-attributes.png)

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do       [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
    
    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para a provisão, clique em **Guardar**.

    ![Configuração de provisionamento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD Azure em Peakon.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* Todos os atributos personalizados do utilizador em Peakon têm de ser estendidos a partir da extensão personalizada do utilizador SCIM da Peakon de `urn:ietf:params:scim:schemas:extension:peakon:2.0:User` .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)