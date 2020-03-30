---
title: 'Tutorial: Fornecimento de utilizadores para LinkedIn Elevate - Azure AD'
description: Aprenda a configurar o Diretório Ativo do Azure para fornecer e desfornecer automaticamente contas de utilizadores ao LinkedIn Elevate.
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
ms.openlocfilehash: fa0a26eaeac431ed2c78c5bd938bbbe7dff14e0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057418"
---
# <a name="tutorial-configure-linkedin-elevate-for-automatic-user-provisioning"></a>Tutorial: Configure LinkedIn Elevate para fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos necessários para realizar no LinkedIn Elevate e Azure AD para fornecer e desfornecer automaticamente contas de utilizadores de Azure AD para LinkedIn Elevate.

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes itens:

* Um inquilino do Azure Active Directory
* Um inquilino LinkedIn Elevate
* Uma conta de administrador no LinkedIn Elevate com acesso ao LinkedIn Account Center

> [!NOTE]
> O Azure Ative Directory integra-se com o LinkedIn Elevate utilizando o protocolo [SCIM.](http://www.simplecloud.info/)

## <a name="assigning-users-to-linkedin-elevate"></a>Atribuir utilizadores ao LinkedIn Elevate

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de conta de utilizador, apenas os utilizadores e grupos que tenham sido "atribuídos" a uma aplicação em Azure AD serão sincronizados.

Antes de configurar e ativar o serviço de provisionamento, terá de decidir quais os utilizadores e/ou grupos em Azure AD que representam os utilizadores que precisam de acesso ao LinkedIn Elevate. Uma vez decidido, pode atribuir estes utilizadores ao LinkedIn Elevate seguindo as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-elevate"></a>Dicas importantes para atribuir utilizadores ao LinkedIn Elevate

* Recomenda-se que um único utilizador da AD Azure seja atribuído ao LinkedIn Elevate para testar a configuração de provisionamento. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador ao LinkedIn Elevate, deve selecionar a função **utilizador** no diálogo de atribuição. A função "Acesso Predefinido" não funciona para o provisionamento.

## <a name="configuring-user-provisioning-to-linkedin-elevate"></a>Configurar o fornecimento de utilizadores ao LinkedIn Elevate

Esta secção guia-o através da ligação do seu AD Azure à conta de utilizador SCIM do LinkedIn Elevate, e configurando o serviço de provisionamento para criar, atualizar e desativar as contas de utilizador atribuídas no LinkedIn Elevate com base na atribuição de utilizador e grupo em Azure AD.

**Dica:** Também pode optar por ativar o Single Sign-On baseado em SAML para linkedIn Elevate, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal instável pode ser configurado independentemente do fornecimento automático, embora estas duas funcionalidades se complementem.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-elevate-in-azure-ad"></a>Para configurar o fornecimento automático de conta de utilizador ao LinkedIn Elevate em Azure AD:

O primeiro passo é recuperar o seu token de acesso LinkedIn. Se for administrador da Enterprise, pode auto-fornecer um sinal de acesso. No seu centro de contas, vá a **Definições Globais &gt; ** de Definições e abra o painel de **configuração SCIM.**

> [!NOTE]
> Se estiver a aceder diretamente ao centro de contas e não através de um link, pode alcançá-lo utilizando os seguintes passos.

1. Inscreva-se no Centro de Contas.

2. Selecione **Configurações &gt; ** de Administrador .

3. Clique em **Integrações Avançadas** na barra lateral esquerda. Está direcionado para o centro de contas.

4. Clique **+ Adicione nova configuração SCIM** e siga o procedimento preenchendo cada campo.

    > [!NOTE]
    > Quando as licenças de autoatribuição não estão ativadas, significa que apenas os dados do utilizador são sincronizados.

    ![Fornecimento de elevação linkedIn](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate1.PNG)

    > [!NOTE]
    > Quando a atribuição de licença automática estiver ativada, é necessário observar a instância de aplicação e o tipo de licença. As licenças são atribuídas numa primeira chegada, primeiro servir a base até que todas as licenças sejam tomadas.

    ![Fornecimento de elevação linkedIn](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate2.PNG)

5. Clique **em Gerar ficha.** Deve ver o seu ecrã de acesso sob **o** campo de acesso.

6. Guarde o seu sinal de acesso à sua área de redação ou computador antes de sair da página.

7. Em seguida, inscreva-se no [portal Azure](https://portal.azure.com), e navegue no **Diretório Ativo do Azure > Aplicações Empresariais > Todas as aplicações.**

8. Se já configurou o LinkedIn Elevate para uma única inscrição, procure a sua instância de LinkedIn Elevate utilizando o campo de pesquisa. Caso contrário, selecione **Adicionar** e procurar **linkedIn Elevate** na galeria de aplicações. Selecione LinkedIn Elevate a partir dos resultados da pesquisa e adicione-o à sua lista de aplicações.

9. Selecione a sua instância de LinkedIn Elevate e, em seguida, selecione o separador **Provisioning.**

10. Detete o **modo de provisionamento** para **automático**.

    ![Fornecimento de elevação linkedIn](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate3.PNG)

11. Preencha os seguintes campos sob **credenciais de administrador:**

    * No campo URL do `https://api.linkedin.com` **Inquilino,** introduza .

    * No campo **Secret Token,** introduza o sinal de acesso gerado no passo 1 e clique em **Ligação de Teste** .

    * Deve ver uma notificação de sucesso no lado superior direito do seu portal.

12. Insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro no campo de email de **notificação** e verifique a caixa de verificação abaixo.

13. Clique em **Guardar**.

14. Na secção **DeAtributom Mappings,** reveja os atributos do utilizador e do grupo que serão sincronizados de Azure AD para LinkedIn Elevate. Note que os atributos selecionados como propriedades **correspondentes** serão usados para corresponder às contas e grupos de utilizadores no LinkedIn Elevate para operações de atualização. Selecione o botão Guardar para elegiro qualquer alteração.

    ![Fornecimento de elevação linkedIn](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate4.PNG)

15. Para ativar o serviço de provisionamento de AD Azure para linkedIn Elevate, altere o Estado de **Provisionamento** para **On** na secção **Definições**

16. Clique em **Guardar**.

Isto iniciará a sincronização inicial de quaisquer utilizadores e/ou grupos atribuídos ao LinkedIn Elevate na secção Utilizadores e Grupos. Note que a sincronização inicial demorará mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações aos registos de atividades de provisionamento, que descrevem todas as ações realizadas pelo serviço de provisionamento na sua aplicação LinkedIn Elevate.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos Adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
