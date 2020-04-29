---
title: 'Tutorial: Configure MerchLogix para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer e desfornecer automaticamente contas de utilizador à MerchLogix.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 9df4c7c5-9a58-478e-93b7-2f77aae12807
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4aa60fb565552961a3c85346c39c318a90c8adc0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77061338"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>Tutorial: Configure MerchLogix para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no MerchLogix e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer e desfornecer automaticamente utilizadores e/ou grupos à MerchLogix.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD Azure
* Um inquilino merchLogix
* Um contacto técnico na MerchLogix que pode fornecer o URL final do Ponto SCIM e ficha secreta necessária para o fornecimento do utilizador

## <a name="adding-merchlogix-from-the-gallery"></a>Adicionando MerchLogix da galeria

Antes de configurar o MerchLogix para o fornecimento automático de utilizadores com o Azure AD, é necessário adicionar merchLogix da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar MerchLogix à galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone do **Diretório Ativo Azure.** 

    ![O botão Azure Ative Directory][1]

2. Navegar para **aplicações** > da Enterprise**Todas as aplicações**.

    ![Secção de aplicações da Enterprise][2]

3. Para adicionar MerchLogix, clique no novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação][3]

4. Na caixa de pesquisa, escreva **MerchLogix**.

5. No painel de resultados, selecione **MerchLogix**, e, em seguida, clique no botão **Adicionar** para adicionar MerchLogix à sua lista de aplicações SaaS.

    ![Provisionamento MerchLogix][4]

## <a name="assigning-users-to-merchlogix"></a>Atribuir utilizadores a MerchLogix

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido "atribuídos" a uma aplicação em Azure AD são sincronizados. 

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao MerchLogix. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos à MerchLogix seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>Dicas importantes para atribuir utilizadores a MerchLogix

* Recomenda-se que um único utilizador da AD Azure seja atribuído à MerchLogix para testar a configuração inicial de fornecimento automático do utilizador. Os utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde, uma vez que os testes sejam bem sucedidos.

* Ao atribuir um utilizador ao MerchLogix, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>Configurar o fornecimento automático de utilizadores à MerchLogix 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em MerchLogix com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para merchLogix, seguindo as instruções fornecidas no [tutorial de inscrição única MerchLogix](merchlogix-tutorial.md). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para MerchLogix em Azure AD:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue nas **aplicações do Azure Ative Directory > Enterprise > todas as aplicações.**

2. Selecione MerchLogix na sua lista de aplicações SaaS.

3. Selecione o separador **Provisioning.**

4. Detete o **modo de provisionamento** para **automático**.

    ![Provisionamento MerchLogix](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. No âmbito da secção **de Credenciais de Administrador:**

    * No campo URL do **Inquilino,** introduza o URL final do Ponto Final do SCIM fornecido pelo seu contacto técnico MerchLogix.

    * No campo **Secret Token,** insira o símbolo secreto fornecido pelo seu contacto técnico MerchLogix.

6. Ao povoar os campos mostrados no Passo 5, clique em **Ligação** de Teste para garantir que o Azure AD pode ligar-se a MerchLogix. Se a ligação falhar, certifique-se de que a sua conta MerchLogix tem permissões de administrador e tente novamente.

7. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

8. Clique em **Guardar**.

9. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to MerchLogix**.

10. Reveja os atributos do utilizador sincronizados de Azure AD a MerchLogix na secção de Mapeamento do **Atributo.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar as contas de utilizador em MerchLogix para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

11. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to MerchLogix**.

12. Reveja os atributos do grupo que são sincronizados de Azure AD a MerchLogix na secção de Mapeamento do **Atributo.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar os grupos em MerchLogix para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

13. Para ativar o serviço de provisionamento de AD Azure para merchLogix, altere o Estado de **Provisionamento** para **Ligar** na secção **Definições.**

14. Quando estiver pronto para fornecer, clique em **Guardar**.

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividadede provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure em MerchLogix.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
