---
title: 'Tutorial: Fornecimento de utilizadores para Cerner Central - Azure AD'
description: Saiba como configurar o Azure Ative Directory para providenciar automaticamente aos utilizadores uma lista em Cerner Central.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: arvinh
ms.openlocfilehash: 1f82cab1172e7293e2a5910d35280eefb30ed49e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94357458"
---
# <a name="tutorial-configure-cerner-central-for-automatic-user-provisioning"></a>Tutorial: Configurar Cerner Central para o fornecimento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos que precisa de executar na Cerner Central e AZure AD para provisões automáticas e desavisagem de contas de utilizadores do Azure AD para uma lista de utilizadores em Cerner Central.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que já tem os seguintes itens:

* Um inquilino do Azure Active Directory
* Um inquilino central da Cerner

> [!NOTE]
> O Azure Ative Directory integra-se com a Cerner Central utilizando o protocolo [SCIM.](http://www.simplecloud.info/)

## <a name="assigning-users-to-cerner-central"></a>Atribuir utilizadores à Cerner Central

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático da conta de utilizador, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação em AZure AD são sincronizados. 

Antes de configurar e ativar o serviço de fornecimento, deve decidir quais os utilizadores e/ou grupos em Azure AD que representam os utilizadores que precisam de acesso à Cerner Central. Uma vez decididos, pode atribuir estes utilizadores à Cerner Central seguindo as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Dicas importantes para a atribuição de utilizadores à Cerner Central

* Recomenda-se que um único utilizador AD Azure seja designado à Cerner Central para testar a configuração de provisionamento. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Uma vez concluído o teste inicial para um único utilizador, a Cerner Central recomenda a atribuição de toda a lista de utilizadores destinados a aceder a qualquer solução Cerner (e não apenas cerner Central) a ser alistada na lista de utilizadores da Cerner.  Outras soluções Cerner aproveitam esta lista de utilizadores na lista de utilizadores.

* Ao atribuir um utilizador à Cerner Central, deve selecionar a função **Utilizador** no diálogo de atribuição. Os utilizadores com a função "Acesso Predefinido" estão excluídos do provisionamento.

## <a name="configuring-user-provisioning-to-cerner-central"></a>Configurar o fornecimento do utilizador à Cerner Central

Esta secção guia-o através da ligação do seu AZure AD à Lista de Utilizadores da Cerner Central utilizando a conta de utilizador SCIM da Cerner que fornece a API, e configurando o serviço de fornecimento para criar, atualizar e desativar as contas de utilizador atribuídas na Cerner Central com base na atribuição de utilizadores e grupos em Azure AD.

> [!TIP]
> Pode também optar por ativar Sign-On individuais baseadas em SAML para a Cerner Central, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal pode ser configurado independentemente do fornecimento automático, embora estas duas características se complementem. Para mais informações, consulte o tutorial de assinatura único da [Cerner Central.](cernercentral-tutorial.md)

### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>Para configurar o fornecimento automático de conta de utilizador à Cerner Central em Azure AD:

Para providenciar contas de utilizador à Cerner Central, terá de solicitar uma conta de sistema Cerner Central da Cerner e gerar um sinal de portador da OAuth que o Azure AD pode utilizar para ligar ao ponto final scim da Cerner. Recomenda-se igualmente que a integração seja realizada num ambiente de caixa de areia cerner antes de ser implantada na produção.

1. O primeiro passo é garantir que as pessoas que gerem a integração cerner e AD Azure tenham uma conta CernerCare, que é necessária para aceder à documentação necessária para completar as instruções. Se necessário, utilize os URLs abaixo para criar contas CernerCare em cada ambiente aplicável.

   * Caixa de areia:  https://sandboxcernercare.com/accounts/create

   * Produção:  https://cernercare.com/accounts/create  

2. Em seguida, deve ser criada uma conta de sistema para o Azure AD. Utilize as instruções abaixo para solicitar uma Conta do Sistema para a sua caixa de areia e ambientes de produção.

   * Instruções:  https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * Caixa de areia: https://sandboxcernercentral.com/system-accounts/

   * Produção:  https://cernercentral.com/system-accounts/

3. Em seguida, gere um símbolo portador de OAuth para cada uma das suas contas do sistema. Para isso, siga as instruções abaixo.

   * Instruções:  https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * Caixa de areia: https://sandboxcernercentral.com/system-accounts/

   * Produção:  https://cernercentral.com/system-accounts/

4. Por fim, é necessário adquirir IDs de Lista de Utilizadores para os ambientes de areia e produção em Cerner para completar a configuração. Para obter informações sobre como adquiri-lo, consulte: https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIM . 

5. Agora pode configurar a Azure AD para a disponibilização de contas de utilizador à Cerner. Inscreva-se no [portal Azure](https://portal.azure.com)e navegue na secção **Azure Ative Directory > Aplicações Empresariais > Todas as aplicações.**

6. Se já tiver configurado cerner Central para um único sinal, procure a sua instância de Cerner Central utilizando o campo de pesquisa. Caso contrário, **selecione Add** e procure pela **Cerner Central** na galeria de aplicações. Selecione Cerner Central a partir dos resultados da pesquisa e adicione-o à sua lista de aplicações.

7. Selecione a sua instância de Cerner Central e, em seguida, selecione o **separador Provisioning.**

8. Defina o **Modo de Aprovisionamento** como **Automático**.

   ![Provisionamento Central cerner](./media/cernercentral-provisioning-tutorial/Cerner.PNG)

9. Preencha os seguintes campos sob **as credenciais de administração:**

   * No campo URL do **inquilino,** introduza um URL no formato abaixo, substituindo "User-Roster-Realm-ID" pelo ID do reino que adquiriu em #4 passo.

    > Caixa de areia: https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 
    > 
    > Produção: https://user-roster-api.cernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

   * No campo **Token Secreto,** insira o token portador da OAuth que gerou na #3 passo e clique em **Test Connection**.

   * Deverá ver uma notificação de sucesso no lado superior do seu portal.

1. Insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de provisionamento no campo **de e-mail de notificação** e verifique a caixa de verificação abaixo.

1. Clique em **Guardar**.

1. Na secção **De mapeamentos de atributos,** reveja os atributos do utilizador e do grupo para serem sincronizados de Azure AD para Cerner Central. Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas e grupos de utilizador no Cerner Central para operações de atualização. Selecione o botão Guardar para confirmar as alterações.

1. Para ativar o serviço de prestação de Ad Azure para a Cerner Central, altere o **Estado de Provisionamento** para **On** na secção **Definições**

1. Clique em **Guardar**.

Isto inicia a sincronização inicial de quaisquer utilizadores e/ou grupos atribuídos à Cerner Central na secção Utilizadores e Grupos. A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para registos de atividade de provisionamento, que descrevem todas as ações realizadas pelo serviço de fornecimento na sua aplicação Cerner Central.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Cerner Central: Publicar dados de identidade utilizando a Azure AD](https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+Azure+AD)
* [Tutorial: Configurar cerner Central para um único sign-on com Azure Ative Directory](cernercentral-tutorial.md)
* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).