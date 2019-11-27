---
title: 'Tutorial: provisionamento de usuário para LinkedIn Elevate – Azure AD'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o LinkedIn Elevate.
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
ms.openlocfilehash: ea6f9923062d960f18203b081702e69a30dd3c9e
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276850"
---
# <a name="tutorial-configure-linkedin-elevate-for-automatic-user-provisioning"></a>Tutorial: configurar o LinkedIn Elevate para o provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que você precisa executar no LinkedIn Elevate e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para o LinkedIn Elevate.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Um inquilino do Azure Active Directory
* Um locatário do LinkedIn Elevate
* Uma conta de administrador no LinkedIn Elevate com acesso ao centro de contas do LinkedIn

> [!NOTE]
> O Azure Active Directory integra-se com o LinkedIn Elevate usando o protocolo [scim](http://www.simplecloud.info/) .

## <a name="assigning-users-to-linkedin-elevate"></a>Atribuindo usuários ao LinkedIn Elevate

Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e grupos que foram "atribuídos" a um aplicativo no Azure AD serão sincronizados.

Antes de configurar e habilitar o serviço de provisionamento, você precisará decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao LinkedIn Elevate. Depois de decidir, você pode atribuir esses usuários ao LinkedIn Elevate seguindo as instruções aqui:

[Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-elevate"></a>Dicas importantes para atribuir usuários ao LinkedIn Elevate

* É recomendável que um único usuário do Azure AD seja atribuído ao LinkedIn Elevate para testar a configuração de provisionamento. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao LinkedIn Elevate, você deve selecionar a função de **usuário** na caixa de diálogo de atribuição. A função de "acesso padrão" não funciona para provisionamento.

## <a name="configuring-user-provisioning-to-linkedin-elevate"></a>Configurando o provisionamento de usuário para o LinkedIn Elevate

Esta seção orienta você pela conexão do Azure AD à API de provisionamento de conta de usuário do SCIM do LinkedIn Elevate e na configuração do serviço de provisionamento para criar, atualizar e desabilitar contas de usuário atribuídas no LinkedIn Elevate com base na atribuição de usuário e de grupo no Azure AD.

**Dica:** Você também pode optar por habilitar o logon único baseado em SAML para o LinkedIn Elevate, seguindo as instruções fornecidas em [portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos se complementem.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-elevate-in-azure-ad"></a>Para configurar o provisionamento automático de conta de usuário para o LinkedIn Elevate no Azure AD:

A primeira etapa é recuperar seu token de acesso do LinkedIn. Se você for um administrador corporativo, poderá autoprovisionar um token de acesso. Em seu centro de contas, vá para **configurações &gt; configurações globais** e abra o painel de **configuração do scim** .

> [!NOTE]
> Se você estiver acessando o centro de contas diretamente em vez de por meio de um link, poderá acessá-lo usando as etapas a seguir.

1. Entre no centro de contas.

2. Selecione **admin &gt; configurações de administrador** .

3. Clique em **integrações avançadas** na barra lateral esquerda. Você será direcionado para o centro de contas.

4. Clique em **+ Adicionar nova configuração do scim** e siga o procedimento preenchendo cada campo.

    > [!NOTE]
    > Quando a atribuição de licenças de autoatribuir não está habilitada, isso significa que apenas os dados do usuário são sincronizados.

    ![Provisionamento do LinkedIn Elevate](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate1.PNG)

    > [!NOTE]
    > Quando a atribuição de licença autolicenciada estiver habilitada, você precisará anotar a instância do aplicativo e o tipo de licença. As licenças são atribuídas em primeiro lugar, servem de base até que todas as licenças sejam executadas.

    ![Provisionamento do LinkedIn Elevate](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate2.PNG)

5. Clique em **gerar token**. Você deve ver a exibição do token de acesso no campo **token de acesso** .

6. Salve seu token de acesso na área de transferência ou no computador antes de sair da página.

7. Em seguida, entre no [portal do Azure](https://portal.azure.com)e navegue até a seção **Azure Active Directory > aplicativos empresariais > todos os aplicativos** .

8. Se você já tiver configurado o LinkedIn Elevate para logon único, pesquise sua instância do LinkedIn Elevate usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise por **LinkedIn Elevate** na Galeria de aplicativos. Selecione LinkedIn Elevate nos resultados da pesquisa e adicione-o à lista de aplicativos.

9. Selecione sua instância do LinkedIn Elevate e, em seguida, selecione a guia **provisionamento** .

10. Defina o **modo de provisionamento** como **automático**.

    ![Provisionamento do LinkedIn Elevate](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate3.PNG)

11. Preencha os campos a seguir em **credenciais de administrador** :

    * No campo **URL do locatário** , digite `https://api.linkedin.com`.

    * No campo **token secreto** , insira o token de acesso gerado na etapa 1 e clique em **testar conexão** .

    * Você deverá ver uma notificação de êxito no lado do upperright do seu portal.

12. Insira o endereço de email de uma pessoa ou grupo que deve receber notificações de erro de provisionamento no campo **email de notificação** e marque a caixa de seleção abaixo.

13. Clique em **Guardar**.

14. Na seção **mapeamentos de atributo** , examine os atributos de usuário e grupo que serão sincronizados do Azure ad para o LinkedIn Elevate. Observe que os atributos selecionados como propriedades **correspondentes** serão usados para corresponder as contas de usuário e grupos no LinkedIn Elevate para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

    ![Provisionamento do LinkedIn Elevate](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate4.PNG)

15. Para habilitar o serviço de provisionamento do Azure AD para o LinkedIn Elevate, altere o **status de provisionamento** para **ativado** na seção **configurações**

16. Clique em **Guardar**.

Isso iniciará a sincronização inicial de todos os usuários e/ou grupos atribuídos ao LinkedIn Elevate na seção usuários e grupos. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrerão aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para os logs de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento em seu aplicativo LinkedIn Elevate.

Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos Adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
