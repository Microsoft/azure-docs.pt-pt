---
title: 'Tutorial: Fornecimento de utilizadores para MilEyes - Azure AD'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer e desfornecer automaticamente contas de utilizadores à ThousandEyes.
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
ms.date: 03/28/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: d87cffce636146eac3e557670ffc4fb2fc34ae38
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062885"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>Tutorial: Configure MilEyes para fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos necessários para realizar em ThousandEyes e Azure AD para fornecer e desfornecer automaticamente contas de utilizadores de Azure AD para ThousandEyes. 

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Um inquilino de diretório Azure Ative
* Um inquilino milEyes com o [plano Standard](https://www.thousandeyes.com/pricing) ou melhor habilitado 
* Uma conta de utilizador em ThousandEyes com permissões de Administrador 

> [!NOTE]
> A integração de provisionamento de AD Azure baseia-se na [API ThousandEyes SCIM,](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK)que está disponível para equipas milEyes no plano Standard ou melhor.

## <a name="assigning-users-to-thousandeyes"></a>Atribuir utilizadores a ThousandEyes

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de conta de utilizador, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação em Azure AD são sincronizados. 

Antes de configurar e ativar o serviço de provisionamento, precisa decidir quais os utilizadores e/ou grupos em Azure AD que representam os utilizadores que precisam de acesso à sua aplicação ThousandEyes. Uma vez decidido, pode atribuir estes utilizadores à sua aplicação ThousandEyes seguindo as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>Dicas importantes para atribuir utilizadores ao ThousandEyes

* Recomenda-se que um único utilizador da AD Azure seja atribuído à ThousandEyes para testar a configuração de provisionamento. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um utilizador ao ThousandEyes, deve selecionar a função **Utilizador** ou outra função específica de aplicação válida (se disponível) no diálogo de atribuição. A função **De Acesso Predefinido** não funciona para o provisionamento, e estes utilizadores são ignorados.

## <a name="configuring-user-provisioning-to-thousandeyes"></a>Configurar o fornecimento de utilizadores a ThousandEyes 

Esta secção guia-o através da ligação do seu AD Azure à conta de utilizador da ThousandEyes que aprovisiona a API, e configurando o serviço de provisionamento para criar, atualizar e desativar as contas de utilizador atribuídas em ThousandEyes com base na atribuição de utilizador e grupo em Azure AD .

> [!TIP]
> Também pode optar por ativar um único signo baseado em SAML para milolhos, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático, embora estas duas funcionalidades se elogiem mutuamente.

### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>Configure o fornecimento automático de conta de utilizador para MilEyes em Azure AD

1. No [portal Azure,](https://portal.azure.com)navegue até ao **Azure Ative Directory > Enterprise Apps > Todas as aplicações.**

2. Se já configurou o ThousandEyes para um único sinal, procure a sua instância de ThousandEyes utilizando o campo de pesquisa. Caso contrário, selecione **Adicionar** e procurar **MilEyes** na galeria de aplicações. Selecione ThousandEyes a partir dos resultados da pesquisa e adicione-os à sua lista de aplicações.

3. Selecione a sua instância de ThousandEyes e, em seguida, selecione o separador **Provisioning.**

4. Detete o **modo de provisionamento** para **automático**.

    ![Prestação milolhos](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)

5. Na secção **credenciais de administrador,** insere o **Token OAuth Bearer** gerado pela sua conta ThousandEyes (pode encontrar e ou gerar um token sob a sua secção de **Perfil** de conta MilEyes).

    ![Prestação milolhos](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. No portal Azure, clique em **Test Connection** para garantir que o Azure AD pode ligar-se à sua aplicação ThousandEyes. Se a ligação falhar, certifique-se de que a sua conta ThousandEyes tem permissões de Administrador e tente novamente o passo 5.

7. Insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro no campo de email de **notificação** e consulte a caixa de verificação "Envie uma notificação de e-mail quando ocorrer uma falha".

8. Clique em **Guardar**.

9. Na secção Mapeamentos, **selecione Synchronize Azure Ative Directory Users to ThousandEyes**.

10. Na secção **DeMapeamentos de Atributos,** reveja os atributos do utilizador que são sincronizados de Azure AD para ThousandEyes. Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em ThousandEyes para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

11. Para ativar o serviço de provisionamento de AD Azure para MilEyes, altere o Estado de **Provisionamento** para **On** na secção **Definições**

12. Clique em **Guardar**.

Esta operação inicia a sincronização inicial de quaisquer utilizadores e/ou grupos atribuídos ao ThousandEyes na secção Utilizadores e Grupos. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço está em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações aos registos de atividades de provisionamento, que descrevem todas as ações realizadas pelo serviço de provisionamento.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
