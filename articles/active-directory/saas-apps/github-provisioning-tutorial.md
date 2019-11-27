---
title: 'Tutorial: provisionamento de usuário para GitHub-Azure AD'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário no GitHub.
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
ms.openlocfilehash: 02bae60622ca8f0e660c8fafbd21189d8e368ba9
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276612"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Tutorial: configurar o GitHub para provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que você precisa executar no GitHub e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o GitHub.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Um locatário do Azure Active Directory
* Uma organização do GitHub criada no [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise), que requer o [plano de cobrança do GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)
* Uma conta de usuário no GitHub com permissões de administrador para a organização

> [!NOTE]
> A integração de provisionamento do Azure AD depende da [API scim do GitHub](https://developer.github.com/v3/scim/), que está disponível para os clientes do [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) no [plano de cobrança do GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations).

## <a name="assigning-users-to-github"></a>Atribuindo usuários ao GitHub

Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e grupos que foram "atribuídos" a um aplicativo no Azure AD são sincronizados. 

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao seu aplicativo GitHub. Depois de decidir, você pode atribuir esses usuários ao seu aplicativo GitHub seguindo estas instruções:

[Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Dicas importantes para atribuir usuários ao GitHub

* É recomendável que um único usuário do Azure AD seja atribuído ao GitHub para testar a configuração de provisionamento. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao GitHub, você deve selecionar a função de **usuário** ou outra função válida específica do aplicativo (se disponível) na caixa de diálogo de atribuição. A função de **acesso padrão** não funciona para provisionamento e esses usuários são ignorados.

## <a name="configuring-user-provisioning-to-github"></a>Configurando o provisionamento de usuário para o GitHub

Esta seção orienta você pela conexão do Azure AD com a API de provisionamento de conta de usuário do GitHub e pela configuração do serviço de provisionamento para criar, atualizar e desabilitar contas de usuário atribuídas no GitHub com base na atribuição de usuário e de grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o GitHub, seguindo as instruções fornecidas em [portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos se complementem.

### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Configurar o provisionamento automático de conta de usuário para o GitHub no Azure AD

1. Na [portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > aplicativos empresariais > todos os aplicativos** .

2. Se você já tiver configurado o GitHub para logon único, pesquise sua instância do GitHub usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **GitHub** na Galeria de aplicativos. Selecione GitHub nos resultados da pesquisa e adicione-o à lista de aplicativos.

3. Selecione sua instância do GitHub e, em seguida, selecione a guia **provisionamento** .

4. Defina o **modo de provisionamento** como **automático**.

    ![Provisionamento do GitHub](./media/github-provisioning-tutorial/GitHub1.png)

5. Na seção **credenciais de administrador** , clique em **autorizar**. Essa operação abre uma caixa de diálogo de autorização do GitHub em uma nova janela do navegador. 

6. Na nova janela, entre no GitHub usando sua conta de administrador. Na caixa de diálogo autorização resultante, selecione a equipe do GitHub para a qual você deseja habilitar o provisionamento e, em seguida, selecione **autorizar**. Depois de concluído, retorne ao portal do Azure para concluir a configuração de provisionamento.

    ![Caixa de diálogo de autorização](./media/github-provisioning-tutorial/GitHub2.png)

7. No portal do Azure, insira a **URL do locatário** e clique em **testar conexão** para garantir que o Azure ad possa se conectar ao seu aplicativo github. Se a conexão falhar, verifique se sua conta do GitHub tem permissões de administrador e se a **URL do locatário** foi inserida corretamente e tente a etapa "autorizar" novamente (você pode constituir a **URL do locatário** por regra: `https://api.github.com/scim/v2/organizations/<Organization_name>`, você pode encontrar suas organizações em sua conta do github: **configurações** > **organizações**).

    ![Caixa de diálogo de autorização](./media/github-provisioning-tutorial/GitHub3.png)

8. Insira o endereço de email de uma pessoa ou grupo que deve receber notificações de erro de provisionamento no campo **email de notificação** e marque a caixa de seleção "Enviar uma notificação por email quando ocorrer uma falha".

9. Clique em **Guardar**.

10. Na seção mapeamentos, selecione **sincronizar Azure Active Directory usuários para o GitHub**.

11. Na seção **mapeamentos de atributo** , examine os atributos de usuário que são sincronizados do Azure ad para o github. Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no GitHub para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

12. Para habilitar o serviço de provisionamento do Azure AD para o GitHub, altere o **status de provisionamento** para **ativado** na seção **configurações**

13. Clique em **Guardar**.

Esta operação inicia a sincronização inicial de todos os usuários e/ou grupos atribuídos ao GitHub na seção usuários e grupos. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço está em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para os logs de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento.

Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
