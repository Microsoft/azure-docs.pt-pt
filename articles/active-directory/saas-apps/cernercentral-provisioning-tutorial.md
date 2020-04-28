---
title: 'Tutorial: Fornecimento de utilizadores para Cerner Central - Azure AD'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer automaticamente aos utilizadores uma lista em Cerner Central.
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
ms.openlocfilehash: 5ed04d8fdcc2d79c66e2ebc53c737c78664e4621
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77058321"
---
# <a name="tutorial-configure-cerner-central-for-automatic-user-provisioning"></a>Tutorial: Configure Cerner Central para fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos necessários para realizar em Cerner Central e Azure AD para fornecer e desfornecer automaticamente contas de utilizadores de Azure AD para uma lista de utilizadores em Cerner Central.

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes itens:

* Um inquilino do Azure Active Directory
* Um inquilino central de Cerner

> [!NOTE]
> O Azure Ative Directory integra-se com a Cerner Central utilizando o protocolo [SCIM.](http://www.simplecloud.info/)

## <a name="assigning-users-to-cerner-central"></a>Atribuir utilizadores à Cerner Central

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de conta de utilizador, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação em Azure AD são sincronizados. 

Antes de configurar e ativar o serviço de provisionamento, deve decidir quais os utilizadores e/ou grupos em Azure AD que representam os utilizadores que precisam de acesso ao Cerner Central. Uma vez decidido, pode atribuir estes utilizadores à Cerner Central seguindo as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Dicas importantes para atribuir utilizadores à Cerner Central

* Recomenda-se que um único utilizador da AD Azure seja atribuído à Cerner Central para testar a configuração de provisionamento. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Uma vez que os testes iniciais estejam completos para um único utilizador, a Cerner Central recomenda a atribuição de toda a lista de utilizadores destinados a aceder a qualquer solução Cerner (e não apenas cerner central) para ser provisionado na lista de utilizadores de Cerner.  Outras soluções Cerner alavancam esta lista de utilizadores na lista de utilizadores.

* Ao atribuir um utilizador à Cerner Central, deve selecionar a função **utilizador** no diálogo de atribuição. Os utilizadores com a função "Acesso Predefinido" estão excluídos do fornecimento.

## <a name="configuring-user-provisioning-to-cerner-central"></a>Configurar o fornecimento de utilizadores à Cerner Central

Esta secção guia-o através da ligação do seu AD Azure à Lista de Utilizadores da Cerner Central utilizando a conta de utilizador SCIM da Cerner, e configurando o serviço de provisionamento para criar, atualizar e desativar as contas de utilizador atribuídas em Cerner Central com base na atribuição de utilizador e grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar o single sign-on baseado em SAML para a Cerner Central, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal instável pode ser configurado independentemente do fornecimento automático, embora estas duas funcionalidades se complementem. Para mais informações, consulte o tutorial de inscrição individual da [Cerner Central.](cernercentral-tutorial.md)

### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>Para configurar o fornecimento automático de conta de utilizador à Cerner Central em Azure AD:

Para fornecer contas de utilizador à Cerner Central, terá de solicitar uma conta do sistema Cerner Central a Cerner e gerar um token do portador da OAuth que a Azure AD pode usar para ligar ao ponto final do SCIM de Cerner. Recomenda-se também que a integração seja realizada num ambiente de caixa de areia Cerner antes de ser implantada para a produção.

1. O primeiro passo é garantir que as pessoas que gerem a integração da AD Cerner e Azure tenham uma conta CernerCare, que é necessária para aceder à documentação necessária para completar as instruções. Se necessário, utilize os URLs abaixo para criar contas CernerCare em cada ambiente aplicável.

   * Caixa de areia:https://sandboxcernercare.com/accounts/create

   * Produção:https://cernercare.com/accounts/create  

2. Em seguida, deve ser criada uma conta de sistema para a AD Azure. Utilize as instruções abaixo para solicitar uma Conta de Sistema para a sua caixa de areia e ambientes de produção.

   * Instruções:https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * Caixa de areia:https://sandboxcernercentral.com/system-accounts/

   * Produção:https://cernercentral.com/system-accounts/

3. Em seguida, gere um símbolo do portador da OAuth para cada uma das suas contas do sistema. Para isso, siga as instruções abaixo.

   * Instruções:https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * Caixa de areia:https://sandboxcernercentral.com/system-accounts/

   * Produção:https://cernercentral.com/system-accounts/

4. Por fim, é necessário adquirir IDs de Domínio da Lista de Utilizadores tanto para os ambientes de sandbox como para a produção em Cerner para completar a configuração. Para obter informações sobre como https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIMadquirir isto, consulte: . 

5. Agora pode configurar a AD Azure para fornecer contas de utilizador a Cerner. Inscreva-se no [portal Azure](https://portal.azure.com)e navegue no **Diretório Ativo do Azure > Aplicações empresariais > todas as aplicações.**

6. Se já configurou a Cerner Central para uma única inscrição, procure a sua instância de Cerner Central utilizando o campo de pesquisa. Caso contrário, selecione **Adicionar** e procurar **a Cerner Central** na galeria de aplicações. Selecione Cerner Central a partir dos resultados da pesquisa e adicione-o à sua lista de aplicações.

7. Selecione a sua instância de Cerner Central e, em seguida, selecione o separador **Provisioning.**

8. Detete o **modo de provisionamento** para **automático**.

   ![Provisionamento Central de Cerner](./media/cernercentral-provisioning-tutorial/Cerner.PNG)

9. Preencha os seguintes campos sob **credenciais de administrador:**

   * No campo URL do **Inquilino,** introduza um URL no formato abaixo, substituindo "User-Roster-Realm-ID" pelo ID do reino que adquiriu em passo #4.

    > Caixa de areia:https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 
    > 
    > Produção:https://user-roster-api.cernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

   * No campo **Secret Token,** introduza o símbolo do portador oAuth que gerou em passo #3 e clique em **Test Connection**.

   * Deve ver uma notificação de sucesso no lado superior direito do seu portal.

1. Insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro no campo de email de **notificação** e verifique a caixa de verificação abaixo.

1. Clique em **Guardar**.

1. Na secção **DeMapeamentos de Atributos,** reveja os atributos do utilizador e do grupo a serem sincronizados de Azure AD para Cerner Central. Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas e grupos de utilizadores em Cerner Central para operações de atualização. Selecione o botão Guardar para elegiro qualquer alteração.

1. Para ativar o serviço de provisionamento de AD Azure para a Cerner Central, altere o Estado de **Provisionamento** para **On** na secção **Definições**

1. Clique em **Guardar**.

Isto inicia a sincronização inicial de quaisquer utilizadores e/ou grupos atribuídos à Cerner Central na secção Utilizadores e Grupos. A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações aos registos de atividades de provisionamento, que descrevem todas as ações realizadas pelo serviço de provisionamento na sua aplicação Cerner Central.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Cerner Central: Publicar dados de identidade usando a AD Azure](https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+Azure+AD)
* [Tutorial: Configurar cerner central para um único sign-on com o Diretório Ativo Azure](cernercentral-tutorial.md)
* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Aprenda a rever os registos e obtenha relatórios sobre a atividade de provisionamento](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).
