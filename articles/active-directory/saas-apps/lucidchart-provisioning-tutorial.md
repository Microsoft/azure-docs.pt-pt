---
title: 'Tutorial: Fornecimento de utilizadores para LucidChart - Azure AD'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer automaticamente e desfornecer contas de utilizador à LucidChart.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5d946c6e257c7676178f9bc3c234f66ba6fe622
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77057333"
---
# <a name="tutorial-configure-lucidchart-for-automatic-user-provisioning"></a>Tutorial: Configure LucidChart para fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos necessários para executar em LucidChart e Azure AD para fornecer e desfornecer automaticamente contas de utilizadores de Azure AD para LucidChart. 

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes itens:

* Um inquilino de diretório Azure Ative
* Um inquilino LucidChart com o [plano Enterprise](https://www.lucidchart.com/user/117598685#/subscriptionLevel) ou melhor habilitado
* Uma conta de utilizador em LucidChart com permissões de administrador

## <a name="assigning-users-to-lucidchart"></a>Atribuir utilizadores à LucidChart

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de conta de utilizador, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação em Azure AD são sincronizados.

Antes de configurar e ativar o serviço de provisionamento, tem de decidir quais os utilizadores e/ou grupos em Azure AD que representam os utilizadores que precisam de acesso à sua aplicação LucidChart. Uma vez decidido, pode atribuir estes utilizadores à sua aplicação LucidChart seguindo as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-lucidchart"></a>Dicas importantes para atribuir utilizadores à LucidChart

* Recomenda-se que um único utilizador da AD Azure seja atribuído à LucidChart para testar a configuração de provisionamento. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador ao LucidChart, deve selecionar a função **utilizador,** ou outra função específica de aplicação válida (se disponível) no diálogo de atribuição. A função **De Acesso Predefinido** não funciona para o provisionamento, e estes utilizadores são ignorados.

## <a name="configuring-user-provisioning-to-lucidchart"></a>Configurar o fornecimento de utilizadores à LucidChart

Esta secção guia-o através da ligação do seu AD Azure à conta de utilizador da LucidChart que aprovisiona a API, e configurando o serviço de provisionamento para criar, atualizar e desativar as contas de utilizador atribuídas na LucidChart com base na atribuição de utilizador e grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar o Single Sign-On baseado em SAML para lucidchart, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="configure-automatic-user-account-provisioning-to-lucidchart-in-azure-ad"></a>Configure o fornecimento automático de conta de utilizador para lucidchart em AD Azure

1. No [portal Azure,](https://portal.azure.com)navegue até ao **Azure Ative Directory > Enterprise Apps > todas as aplicações.**

2. Se já configurou o LucidChart para um único sinal, procure a sua instância de LucidChart utilizando o campo de pesquisa. Caso contrário, selecione **Adicionar** e procurar **lucidChart** na galeria de aplicações. Selecione LucidChart a partir dos resultados da pesquisa e adicione-o à sua lista de aplicações.

3. Selecione a sua instância de LucidChart e, em seguida, selecione o separador **Provisioning.**

4. Detete o **modo de provisionamento** para **automático**.

    ![Provisionamento LucidChart](./media/lucidchart-provisioning-tutorial/LucidChart1.png)

5. Na secção **credenciais de administrador,** insere o **Token Secreto** gerado pela conta da LucidChart (pode encontrar o símbolo na sua conta: **Team** > **App Integration** > **SCIM**).

    ![Provisionamento LucidChart](./media/lucidchart-provisioning-tutorial/LucidChart2.png)

6. No portal Azure, clique em **Test Connection** para garantir que o Azure AD pode ligar-se à sua aplicação LucidChart. Se a ligação falhar, certifique-se de que a sua conta LucidChart tem permissões de administrador e tente novamente o passo 5.

7. Insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro no campo de email de **notificação** e consulte a caixa de verificação "Envie uma notificação de e-mail quando ocorrer uma falha".

8. Clique em **Guardar**.

9. Na secção Mapeamentos, **selecione Synchronize Azure Ative Directory Users to LucidChart**.

10. Na secção **DeMapeamentos** de Atributos, reveja os atributos do utilizador que são sincronizados de Azure AD para LucidChart. Os atributos selecionados como propriedades **correspondentes** são usados para combinar as contas de utilizador no LucidChart para operações de atualização. Selecione o botão Guardar para elegiro qualquer alteração.

11. Para ativar o serviço de provisionamento de AD Azure para lucidChart, altere o Estado de **Provisionamento** para **On** na secção **Definições**

12. Clique em **Guardar**.

Esta operação inicia a sincronização inicial de quaisquer utilizadores e/ou grupos atribuídos à LucidChart na secção Utilizadores e Grupos. A sincronização inicial demora mais tempo a realizar do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações aos registos de atividades de provisionamento, que descrevem todas as ações realizadas pelo serviço de provisionamento.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
