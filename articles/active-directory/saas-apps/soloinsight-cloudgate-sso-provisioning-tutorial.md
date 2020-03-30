---
title: 'Tutorial: Configure Soloinsight-CloudGate SSO para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer e desfornecer automaticamente contas de utilizadores para Soloinsight-CloudGate SSO.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 07558ceb-d406-40e7-90b8-1b40fdc829e7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 6ab90a6aea262d5c7067f9f41b9ddfc090b7371d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063200"
---
# <a name="tutorial-configure-soloinsight-cloudgate-sso-for-automatic-user-provisioning"></a>Tutorial: Configure Soloinsight-CloudGate SSO para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no SSO Soloinsight-CloudGate e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer e desfornecer automaticamente utilizadores e/ou grupos para Soloinsight-CloudGate SSO.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD Azure
* [Um inquilino SSO Soloinsight-CloudGate](https://www.soloinsight.com/)
* Uma conta de utilizador em Soloinsight-CloudGate SSO com permissões de administrador.

## <a name="assigning-users-to-soloinsight-cloudgate-sso"></a>Atribuir utilizadores a Soloinsight-CloudGate SSO

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao SSO Soloinsight-CloudGate. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos a Soloinsight-CloudGate SSO seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-soloinsight-cloudgate-sso"></a>Dicas importantes para atribuir utilizadores a Soloinsight-CloudGate SSO

* Recomenda-se que um único utilizador da AD Azure seja atribuído ao Soloinsight-CloudGate SSO para testar a configuração automática de fornecimento de utilizadores. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador ao Soloinsight-CloudGate SSO, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="set-up-soloinsight-cloudgate-sso-for-provisioning"></a>Configurar SSO Soloinsight-CloudGate para o fornecimento

1. Inscreva-se na consola [De administrador Soloinsight-CloudGate SSO](https://soloinsight.sigateway.com/login). Navegar para **a Administração > Definições**do Sistema .

    ![Consola de administrador Soloinsight-CloudGate SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/admin.png)

2.  Navegue para **o General.**

    ![Sioninsight-CloudGate SSO Adicionar SCIM](media/soloinsight-cloudgate-sso-provisioning-tutorial/config.png)

3.  Percorra até ao final da página para obter o URL do **Inquilino** e **O Token Secreto**. Copie o **Símbolo Secreto.** Este valor será inserido no campo Secret Token no separador Provisioning da sua aplicação Soloinsight-CloudGate SSO no portal Azure.

    ![Soloinsight-CloudGate SSO Criar Token](media/soloinsight-cloudgate-sso-provisioning-tutorial/token.png)

## <a name="add-soloinsight-cloudgate-sso-from-the-gallery"></a>Adicione Soloinsight-CloudGate SSO da galeria

Antes de configurar o SSO Soloinsight-CloudGate para o fornecimento automático de utilizadores com a AD Azure, é necessário adicionar O SSO Soloinsight-CloudGate da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar Soloinsight-CloudGate SSO da galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Soloinsight-CloudGate SSO,** selecione **Soloinsight-CloudGate SSO** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Soloinsight-CloudGate SSO na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-soloinsight-cloudgate-sso"></a>Configurar o fornecimento automático de utilizadores para Soloinsight-CloudGate SSO 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Soloinsight-CloudGate SSO com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sign-on baseado em SAML para Soloinsight-CloudGate SSO, seguindo as instruções fornecidas no [tutorial de inscrição individual Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático de utilizadores, embora estas duas funcionalidades se elogiem mutuamente

### <a name="to-configure-automatic-user-provisioning-for-soloinsight-cloudgate-sso-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para Soloinsight-CloudGate SSO em Azure AD:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Soloinsight-CloudGate SSO**.

    ![O link Soloinsight-CloudGate SSO na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção de `https://sigateway.com/scim/v2/sync/serviceproviderconfig` **Credenciais de Administrador,** insere-se no URL do **Arrendatário**. Insera o valor token de **autenticação SCIM** recuperado anteriormente em **Ficha Secreta**. Clique em **Ligação de Teste** para garantir que o Azure AD pode ligar-se ao SSO Soloinsight-CloudGate. Se a ligação falhar, certifique-se de que a sua conta SSO Soloinsight-CloudGate tem permissões de administrador e tente novamente.

    ![URL do inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Soloinsight-CloudGate SSO**.

    ![Mapeamento soo do SSO Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/usermappings.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Soloinsight-CloudGate SSO na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Soloinsight-CloudGate SSO para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos de utilizador SSO Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/userattributes.png)

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Soloinsight-CloudGate SSO**.

    ![Mapeamentos do grupo SSO Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupmappings.png)

11. Reveja os atributos do grupo que são sincronizados de Azure AD para Soloinsight-CloudGate SSO na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar os grupos em Soloinsight-CloudGate SSO para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos do grupo SSO Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento de AD Azure para Soloinsight-CloudGate SSO, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja fornecer ao SSO Soloinsight-CloudGate, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividadede provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure no SSO Soloinsight-CloudGate.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

