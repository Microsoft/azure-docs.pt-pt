---
title: 'Tutorial: Configure Promapp para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer e desfornecer automaticamente contas de utilizadores à Promapp.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 41190ba0-9032-4725-9d2c-b3dd9c7786e6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2019
ms.author: Zhchia
ms.openlocfilehash: 1a4a27846196e7eb134b834647b2a2d65672f385
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77061018"
---
# <a name="tutorial-configure-promapp-for-automatic-user-provisioning"></a>Tutorial: Configure Promapp para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados na Promapp e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecer e desfornecer automaticamente utilizadores e/ou grupos para a Promapp.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD Azure
* [Um inquilino promapp](https://www.promapp.com/licensing/)
* Uma conta de utilizador em Promapp com permissões de administrador.

## <a name="assigning-users-to-promapp"></a>Atribuir utilizadores à Promapp

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Promapp. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos à Promapp seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-promapp"></a>Dicas importantes para atribuir utilizadores à Promapp

* Recomenda-se que um único utilizador da AD Azure seja designado para a Promapp para testar a configuração automática de fornecimento de utilizadores. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador ao Promapp, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="setup-promapp-for-provisioning"></a>Configuração Promapp para provisionamento

1. Inscreva-se na sua [Consola de Admin Promapp.](https://freetrial.promapp.com/axelerate/Login.aspx) Sob o nome de utilizador navegar para **O Meu Perfil**.

    ![Consola de administrador a promapp](media/promapp-provisioning-tutorial/admin.png)

2.  Em **Tokens** de acesso clique no botão **Criar Token.**

    ![Promapp Adicionar SCIM](media/promapp-provisioning-tutorial/addtoken.png)

3.  Forneça qualquer nome no campo **Descrição** e selecione **Scim** a partir do menu de dropdown **scope.** Clique no ícone de guardar.

    ![Promapp Adicionar Nome](media/promapp-provisioning-tutorial/addname.png)

4.  Copie o sinal de acesso e guarde-o pois esta será a única altura em que poderá vê-lo. Este valor será inserido no campo Secret Token no separador de fornecimento da sua aplicação Promapp no portal Azure.

    ![Promapp Criar Token](media/promapp-provisioning-tutorial/token.png)

## <a name="add-promapp-from-the-gallery"></a>Adicione Promapp da galeria

Antes de configurar o Promapp para o fornecimento automático de utilizadores com o Azure AD, precisa de adicionar o Promapp à galeria de aplicações Azure AD à sua lista de aplicações saaS geridas.

**Para adicionar promapp da galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza o **Promapp,** selecione **Promapp** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Promapp na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-promapp"></a>Configurar o fornecimento automático de utilizadores à Promapp 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Promapp com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Você também pode optar por ativar um único sign-on baseado em SAML para promapp seguindo as instruções fornecidas no tutorial de [inscrição single promapp](https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-tutorial). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se complementem.

### <a name="to-configure-automatic-user-provisioning-for-promapp-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para promapp em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Promapp**.

    ![O link Promapp na lista de aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção de `https://api.promapp.com/api/scim` **Credenciais de Administrador,** insere-se no URL do **Arrendatário**. Insera o valor token de **autenticação SCIM** recuperado anteriormente em **Ficha Secreta**. Clique em **Ligação** de Teste para garantir que o Azure AD pode ligar-se ao Promapp. Se a ligação falhar, certifique-se de que a sua conta Promapp tem permissões de administrador e tente novamente.

    ![URL do inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Promapp**.

    ![Mapeamento de utilizadores de promapp](media/promapp-provisioning-tutorial/usermappings.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Promapp na secção De Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no Promapp para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos de utilizador promapp](media/promapp-provisioning-tutorial/userattributes.png)

11. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para ativar o serviço de provisionamento de AD Azure para promapp, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

13. Defina os utilizadores e/ou grupos que gostaria de fornecer à Promapp, escolhendo os valores desejados no **Scope** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Você pode usar a secção **Detalhes de Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividade de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure na Promapp.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

