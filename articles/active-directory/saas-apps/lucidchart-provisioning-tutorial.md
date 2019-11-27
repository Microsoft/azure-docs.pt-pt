---
title: 'Tutorial: provisionamento de usuário para LucidChart-Azure AD'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o LucidChart.
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
ms.openlocfilehash: 3791992586edbdc5188c3078b1f1bb108ce580d7
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276859"
---
# <a name="tutorial-configure-lucidchart-for-automatic-user-provisioning"></a>Tutorial: configurar o LucidChart para o provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que você precisa executar no LucidChart e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o LucidChart. 

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Um locatário do Azure Active Directory
* Um locatário do LucidChart com o [plano Enterprise](https://www.lucidchart.com/user/117598685#/subscriptionLevel) ou melhor habilitado
* Uma conta de usuário no LucidChart com permissões de administrador

## <a name="assigning-users-to-lucidchart"></a>Atribuindo usuários ao LucidChart

Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e grupos que foram "atribuídos" a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o serviço de provisionamento, você precisa decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao seu aplicativo LucidChart. Depois de decidir, você pode atribuir esses usuários ao seu aplicativo LucidChart seguindo estas instruções:

[Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-lucidchart"></a>Dicas importantes para atribuir usuários ao LucidChart

* É recomendável que um único usuário do Azure AD seja atribuído ao LucidChart para testar a configuração de provisionamento. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao LucidChart, você deve selecionar a função de **usuário** ou outra função válida específica do aplicativo (se disponível) na caixa de diálogo de atribuição. A função de **acesso padrão** não funciona para provisionamento e esses usuários são ignorados.

## <a name="configuring-user-provisioning-to-lucidchart"></a>Configurando o provisionamento de usuário para o LucidChart

Esta seção orienta você pela conexão do Azure AD com a API de provisionamento de conta de usuário do LucidChart e pela configuração do serviço de provisionamento para criar, atualizar e desabilitar contas de usuário atribuídas no LucidChart com base na atribuição de usuário e de grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o LucidChart, seguindo as instruções fornecidas em [portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos se complementem.

### <a name="configure-automatic-user-account-provisioning-to-lucidchart-in-azure-ad"></a>Configurar o provisionamento automático de conta de usuário para o LucidChart no Azure AD

1. Na [portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > aplicativos empresariais > todos os aplicativos** .

2. Se você já tiver configurado o LucidChart para logon único, pesquise sua instância do LucidChart usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise **LucidChart** na Galeria de aplicativos. Selecione LucidChart nos resultados da pesquisa e adicione-o à lista de aplicativos.

3. Selecione sua instância do LucidChart e, em seguida, selecione a guia **provisionamento** .

4. Defina o **modo de provisionamento** como **automático**.

    ![Provisionamento do LucidChart](./media/lucidchart-provisioning-tutorial/LucidChart1.png)

5. Na seção **credenciais de administrador** , insira o **token secreto** gerado pela conta do LucidChart (você pode encontrar o token em sua conta: **Team** > **app Integration** > **scim**).

    ![Provisionamento do LucidChart](./media/lucidchart-provisioning-tutorial/LucidChart2.png)

6. No portal do Azure, clique em **testar conexão** para garantir que o Azure ad possa se conectar ao seu aplicativo LucidChart. Se a conexão falhar, verifique se sua conta do LucidChart tem permissões de administrador e repita a etapa 5.

7. Insira o endereço de email de uma pessoa ou grupo que deve receber notificações de erro de provisionamento no campo **email de notificação** e marque a caixa de seleção "Enviar uma notificação por email quando ocorrer uma falha".

8. Clique em **Guardar**.

9. Na seção mapeamentos, selecione **sincronizar Azure Active Directory usuários para LucidChart**.

10. Na seção **mapeamentos de atributo** , examine os atributos de usuário que são sincronizados do Azure ad para o LucidChart. Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no LucidChart para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

11. Para habilitar o serviço de provisionamento do Azure AD para o LucidChart, altere o **status de provisionamento** para **ativado** na seção **configurações**

12. Clique em **Guardar**.

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos atribuídos ao LucidChart na seção usuários e grupos. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço está em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para os logs de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento.

Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
