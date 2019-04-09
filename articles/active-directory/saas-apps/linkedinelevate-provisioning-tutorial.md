---
title: 'Tutorial: Configurar o LinkedIn elevar para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador para elevar o LinkedIn.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: daveba
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: asmalser-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: e95fae67d3e7fd97cf4be1642f41b64a07fd0145
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59259614"
---
# <a name="tutorial-configure-linkedin-elevate-for-automatic-user-provisioning"></a>Tutorial: Configurar o LinkedIn elevar para aprovisionamento automático de utilizadores

O objetivo deste tutorial é mostrar a os passos que necessários para executar no LinkedIn elevar e o Azure AD, aprovisionar e desaprovisionar contas de utilizador do Azure AD para elevar o LinkedIn.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes itens:

* Um inquilino do Azure Active Directory
* Um inquilino de elevar o LinkedIn
* Uma conta de administrador no LinkedIn elevar com acesso ao centro de contas do LinkedIn

> [!NOTE]
> O Azure Active Directory se integra com o LinkedIn elevar a utilizar o [SCIM](http://www.simplecloud.info/) protocolo.

## <a name="assigning-users-to-linkedin-elevate"></a>Atribuir utilizadores a elevar o LinkedIn

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento de contas de utilizadores automático, serão sincronizados apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação no Azure AD.

Antes de configurar e ativar o serviço de aprovisionamento, terá de decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder para elevar o LinkedIn. Depois de decidir, pode atribuir esses usuários para elevar o LinkedIn, seguindo as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-elevate"></a>Dicas importantes para atribuir utilizadores a elevar o LinkedIn

* Recomenda-se que um único utilizador do Azure AD ser atribuídos a elevar o LinkedIn para testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um utilizador para elevar o LinkedIn, tem de selecionar o **utilizador** função na caixa de diálogo atribuição. A função de "Acesso predefinido" não funciona para o aprovisionamento.

## <a name="configuring-user-provisioning-to-linkedin-elevate"></a>Configurar o aprovisionamento para elevar o LinkedIn

Esta secção orienta-o ao longo da ligação do Azure AD da elevar o LinkedIn SCIM conta de utilizador API de aprovisionamento e configurar o serviço de aprovisionamento para criar, atualizar e desativar atribuídos a contas de utilizador no LinkedIn, efetuar a elevação com base no utilizador e a atribuição de grupo no Azure AD.

**Sugestão:** Também pode optar por ativada baseado em SAML início de sessão único para elevar o LinkedIn, seguindo as instruções fornecidas [portal do Azure](https://portal.azure.com). Início de sessão único a pode ser configurada independentemente do serviço de aprovisionamento automático, embora esses dois recursos complementam uma à outra.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-elevate-in-azure-ad"></a>Para configurar o aprovisionamento automático de utilizadores conta LinkedIn elevar no Azure AD:

A primeira etapa é obter o token de acesso do LinkedIn. Se for um administrador empresarial, pode Self-aprovisionar um token de acesso. No seu centro de contas, aceda a **configurações &gt; definições globais** e abra o **SCIM configuração** painel.

> [!NOTE]
> Se estiver a aceder ao centro de contas diretamente em vez de através de uma ligação, pode aceder ao mesmo através dos seguintes passos.

1. Inicie sessão no Centro de contas.

2. Selecione **administrador &gt; definições de administração** .

3. Clique em **integrações avançadas** na barra lateral esquerda. Será direcionado para o Centro de contas.

4. Clique em **+ Adicionar nova configuração de SCIM** e siga o procedimento ao preencher cada campo.

    > [!NOTE]
    > Quando autoassign licenças não estiver ativada, significa que apenas os dados de utilizador estão sincronizados.

    ![LinkedIn elevar o aprovisionamento](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate1.PNG)

    > [!NOTE]
    > Quando a atribuição de autolicense está ativada, tem de observar a instância da aplicação e o tipo de licença. As licenças são atribuídas numa primeira vêm, primeiro servir base até que todas as licenças são executadas.

    ![LinkedIn elevar o aprovisionamento](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate2.PNG)

5. Clique em **gerar token**. Deverá ver sua exibição de token de acesso sob o **token de acesso** campo.

6. Guarde o token de acesso à sua área de transferência ou o computador antes de sair da página.

7. Em seguida, inicie sessão para o [portal do Azure](https://portal.azure.com)e navegue para o **Azure Active Directory > aplicações empresariais > todos os aplicativos** secção.

8. Se já tiver configurado o LinkedIn elevar para início de sessão único, procure a sua instância do LinkedIn, efetuar a elevação utilizando o campo de pesquisa. Caso contrário, selecione **Add** e procure **LinkedIn elevar** na Galeria de aplicações. Selecione o LinkedIn elevar os resultados da pesquisa e adicioná-lo à sua lista de aplicações.

9. Selecione a sua instância de elevar o LinkedIn, em seguida, selecione o **aprovisionamento** separador.

10. Definir o **modo de aprovisionamento** ao **automática**.

    ![LinkedIn elevar o aprovisionamento](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate3.PNG)

11. Preencha os campos seguintes sob **credenciais de administrador** :

    * Na **URL de inquilino** , insira `https://api.linkedin.com`.

    * Na **segredo de Token** campo, introduza o token de acesso que gerou no passo 1 e clique em **Testar ligação** .

    * Deverá ver uma notificação de êxito no lado upperright do seu portal.

12. Introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento no **notificação por E-Mail** campo e marque a caixa de verificação abaixo.

13. Clique em **Guardar**.

14. Na **mapeamentos de atributos** secção, reveja os atributos de utilizadores e grupos que serão sincronizados do Azure AD para elevar o LinkedIn. Tenha em atenção que os atributos selecionados como **correspondência** propriedades serão utilizadas para corresponder as contas de utilizador e grupos no LinkedIn elevar para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

    ![LinkedIn elevar o aprovisionamento](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate4.PNG)

15. Para ativar o Azure AD para elevar o LinkedIn, o serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção

16. Clique em **Guardar**.

Isso iniciará a sincronização inicial de todos os utilizadores e/ou grupos atribuídos para elevar o LinkedIn na secção utilizadores e grupos. Tenha em atenção que a sincronização inicial demora mais tempo a executar que sincroniza subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e seguir links para aprovisionamento de registos de atividade, que descrevem a todas as ações executadas pelo serviço de aprovisionamento na sua aplicação de elevar o LinkedIn.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos Adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
