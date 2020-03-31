---
title: 'Tutorial: Configure Blink para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo azure para fornecer automaticamente e desfornecer contas de utilizador para Blink.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 9ebcbf4a-0cf9-41c3-96af-d8ab6ab11639
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: 455036652836c6cfd2055e9a747f30b6dfe41295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77059158"
---
# <a name="tutorial-configure-blink-for-automatic-user-provisioning"></a>Tutorial: Configure Blink para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Blink e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer automaticamente e desfornecer utilizadores e/ou grupos para Blink.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD Azure
* [Um inquilino Blink](https://joinblink.com/pricing)
* Uma conta de utilizador em Blink com permissões de Administrador.

## <a name="assigning-users-to-blink"></a>Atribuir utilizadores a Blink

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em AD Azure que precisam de acesso ao Blink. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos a Blink seguindo as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-blink"></a>Dicas importantes para atribuir utilizadores ao Blink

* Recomenda-se que um único utilizador da AD Azure seja designado para blink para testar a configuração automática de fornecimento do utilizador. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador ao Blink, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="setup-blink-for-provisioning"></a>Configurar blink para o provisionamento

1. Inicie sessão num Caso support@joinblink.com de [Suporte](https://help.joinblink.com/hc/requests/new) ou por e-mail, **solicite** um token SCIM. .

2.  Copiar o Símbolo de **Autenticação SCIM**. Este valor será inserido no campo Secret Token no separador de provisionamento da sua aplicação Blink no portal Azure.

## <a name="add-blink-from-the-gallery"></a>Adicione Blink da galeria

Antes de configurar o Blink para o fornecimento automático de utilizadores com a AD Azure, é necessário adicionar Blink da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar Blink da galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Blink**, selecione **Blink** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Pisque na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-blink"></a>Configurar o fornecimento automático de utilizadores para Blink 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Blink com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para Blink, seguindo as instruções fornecidas no [tutorial de inscrição Blink Single](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático de utilizadores, embora estas duas funcionalidades se elogiem mutuamente

### <a name="to-configure-automatic-user-provisioning-for-blink-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para Blink em Azure AD:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Blink**.

    ![O link Blink na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção de `https://api.joinblink.com/scim` **Credenciais de Administrador,** insere-se no URL do **Arrendatário**. Insera o valor token de **autenticação SCIM** recuperado anteriormente em **Ficha Secreta**. Clique na **ligação de teste** para garantir que o Azure AD pode ligar-se ao Blink. Se a ligação falhar, certifique-se de que a sua conta Blink tem permissões de administrador e tente novamente.

    ![URL do inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Blink**.

    ![Blink User Mappings](media/blink-provisioning-tutorial/User_mappings.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Blink na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Blink para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos de utilizador intermitentes](media/blink-provisioning-tutorial/User_attributes.png)

10. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de provisionamento de AD Azure para Blink, altere o Estado de **Provisionamento** para **Ligado** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

12. Defina os utilizadores que gostaria de fornecer ao Blink escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure no Blink.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

