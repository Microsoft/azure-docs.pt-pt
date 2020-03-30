---
title: 'Tutorial: Configure 155 para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo azure para fornecer automaticamente e desfornecer contas de utilizador para 155.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: a276c004-9f71-4efc-8cca-1f615760249f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: f1f66a7b69048180bc41c8f2fa432598f00f7f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77059290"
---
# <a name="tutorial-configure-15five-for-automatic-user-provisioning"></a>Tutorial: Configure 155 para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados em 155 e azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer e desfornecer automaticamente utilizadores e/ou grupos para 155.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino 155.](https://www.15five.com/pricing/)
* Uma conta de utilizador em 155 com permissões de administrador.

## <a name="assigning-users-to-15five"></a>Atribuir utilizadores a 155

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao 155. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao 155, seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-15five"></a>Dicas importantes para atribuir utilizadores ao 155

* Recomenda-se que um único utilizador da AD Azure seja atribuído ao 155 para testar a configuração automática de fornecimento do utilizador. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador ao 155, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="setup-15five-for-provisioning"></a>Configuração 155 para o fornecimento

Antes de configurar 155 para o fornecimento automático de utilizadores com a AD Azure, terá de ativar o fornecimento de SCIM no 155.

1. Inscreva-se na sua Consola de [155 Admin](https://my.15five.com/). Navegue para **funcionalidades > integrações.**

    ![Consola de 155 administradores](media/15five-provisioning-tutorial/integration.png)

2.  Clique no **SCIM 2.0**.

    ![Consola de 155 administradores](media/15five-provisioning-tutorial/image00.png)

3.  Navegue para a **integração sCIM > Gerar ficha OAuth**.

    ![155 Adicionar SCIM](media/15five-provisioning-tutorial/image02.png)

4.  Copie os valores para **URL base SCIM 2.0** e **Token de Acesso**. Este valor será inserido no **campo URL** do Inquilino e no Campo **Secreto token** no separador provisionamento da sua aplicação 155 no portal Azure.
    
    ![155 Adicionar SCIM](media/15five-provisioning-tutorial/image03.png)

## <a name="add-15five-from-the-gallery"></a>Adicione 155 da galeria

Para configurar 15Five para o fornecimento automático de utilizadores com a AD Azure, é necessário adicionar 1555 da galeria de aplicações Azure AD à sua lista de aplicações saaS geridas.

**Para adicionar 155 da galeria de aplicações da AD Azure, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **155**, selecione **155** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![15Cinco na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-15five"></a>Configurar o fornecimento automático de utilizadores para 155 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em 155 com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para 15Five, seguindo as instruções fornecidas no tutorial de [inscrição 155 single](15five-tutorial.md). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-15five-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para 155 em Azure AD:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **155**.

    ![O link 155 na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5.  No âmbito da secção de Credenciais de Administrador, insere-se os valores de **URL base SCIM 2.0 e Access Token** recuperados anteriormente em URL de **Inquilino** e **Token Secreto,** respectivamente. Clique na **ligação de teste** para garantir que o Azure AD pode ligar-se ao 155. Se a ligação falhar, certifique-se de que a sua conta 155 tem permissões de administrador e tente novamente.

    ![URL do inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to 15Five**.

    ![15Cinco mapeamentos de utilizadores](media/15five-provisioning-tutorial/usermapping.png)

9. Reveja os atributos do utilizador sincronizados de Azure AD para 15Five na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas do utilizador em 15Five para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![15Cinco atributos de utilizador](media/15five-provisioning-tutorial/userattribute.png)

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to 15Five**.

    ![155 Mapeamentos de grupo](media/15five-provisioning-tutorial/groupmapping.png)

11. Reveja os atributos do grupo que são sincronizados de Azure AD para 15Five na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar os grupos em 15Five para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![155 Atributos de Grupo](media/15five-provisioning-tutorial/groupattribute.png)

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento AD Azure para 155, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja fornecer ao 155, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

    Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento da AD Azure no dia 155.

    Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [Relatórios sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md)
    
## <a name="connector-limitations"></a>Limitações do conector

* 155 não suporta eliminações duras para os utilizadores.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento de contas de utilizador para aplicações empresariais.](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Aprenda a rever os registos e obtenha relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).
