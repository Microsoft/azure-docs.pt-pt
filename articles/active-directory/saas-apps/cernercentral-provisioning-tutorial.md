---
title: 'Tutorial: Configurar Cerner Central para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar automaticamente os utilizadores para uma lista de participantes Cerner Central.
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
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: dda84d30124eca1526f227ffec134f48451c9cb0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58102572"
---
# <a name="tutorial-configure-cerner-central-for-automatic-user-provisioning"></a>Tutorial: Configurar Cerner Central para aprovisionamento automático de utilizadores

O objetivo deste tutorial é mostrar a os passos que necessários para executar em Cerner Central e o Azure AD para aprovisionar e desaprovisionar contas de utilizador do Azure AD com uma lista de participantes de utilizador na Central de Cerner automaticamente. 


## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes itens:

*   Um inquilino do Azure Active Directory
*   Um inquilino Cerner Central 

> [!NOTE]
> O Azure Active Directory integra-se com a Central de Cerner utilizando o [SCIM](http://www.simplecloud.info/) protocolo.

## <a name="assigning-users-to-cerner-central"></a>Atribuir utilizadores a Cerner Central

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento de contas de utilizadores automático, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação no Azure AD são sincronizados. 

Antes de configurar e ativar o serviço de aprovisionamento, deve decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder ao Cerner Central. Depois de decidir, pode atribuir estes utilizadores a Central de Cerner ao seguir as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Dicas importantes para atribuir utilizadores a Cerner Central

*   Recomenda-se que um único utilizador do Azure AD ser atribuídos a Central de Cerner para testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

* Assim que o teste inicial estiver concluída para um único utilizador, Cerner Central recomenda a atribuição de toda a lista de utilizadores que se destina a aceder a qualquer solução de Cerner (não apenas Cerner Central) a ser aprovisionada para a lista de participantes do Cerner utilizador.  Outras soluções de Cerner tirar partido desta lista de utilizadores na lista de participantes do utilizador.

*   Ao atribuir um utilizador ao Cerner Central, tem de selecionar o **utilizador** função na caixa de diálogo atribuição. Os utilizadores com a função de "Acesso predefinido" são excluídos desde o aprovisionamento.


## <a name="configuring-user-provisioning-to-cerner-central"></a>Configurar o aprovisionamento para Cerner Central

Esta secção orienta-o ao longo da ligação do Azure AD para a lista de participantes do usuário do Cerner Central utilizando a conta de utilizador do Cerner SCIM API de aprovisionamento e configurar o serviço de aprovisionamento para criar, atualizar e desativar o utilizador atribuído contas no Cerner Central com base em atribuição de utilizadores e grupos no Azure AD.

> [!TIP]
> Também pode optar por ativada baseado em SAML início de sessão único para Cerner Central, seguindo as instruções fornecidas [portal do Azure](https://portal.azure.com). Início de sessão único a pode ser configurada independentemente do serviço de aprovisionamento automático, embora esses dois recursos complementam uma à outra. Para obter mais informações, consulte a [Cerner Central único início de sessão tutorial](cernercentral-tutorial.md).


### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>Para configurar o aprovisionamento automático de utilizadores conta ao Cerner Central no Azure AD:


Para aprovisionar contas de utilizador ao Cerner Central, terá de pedir uma conta de sistema Cerner Central da Cerner e gerar um token de portador do OAuth do Azure AD pode utilizar para ligar ao ponto final SCIM do Cerner. Também é recomendável que a integração a ser realizada num ambiente de sandbox Cerner antes da implantação em produção.

1. A primeira etapa é garantir que as pessoas que gerenciam o Cerner e integração do Azure AD tiver uma conta de CernerCare, que é necessária para acessar a documentação necessária para concluir as instruções. Se necessário, utilize os URLs abaixo para criar contas de CernerCare em cada ambiente aplicável.

   * Área de segurança:  https://sandboxcernercare.com/accounts/create

   * Produção:  https://cernercare.com/accounts/create  

2. Em seguida, uma conta do sistema tem de ser criada para o Azure AD. Utilize as instruções abaixo para pedir uma conta de sistema para os seus ambientes de área de segurança e de produção.

   * Instruções:  https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * Área de segurança: https://sandboxcernercentral.com/system-accounts/

   * Produção:  https://cernercentral.com/system-accounts/

3. Em seguida, gere um token de portador do OAuth para cada uma das suas contas do sistema. Para tal, siga as instruções abaixo.

   * Instruções:  https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * Área de segurança: https://sandboxcernercentral.com/system-accounts/

   * Produção:  https://cernercentral.com/system-accounts/

4. Por fim, terá de adquirir os IDs de Realm de lista de participantes de usuário para ambientes de área de segurança e de produção no Cerner para concluir a configuração. Para obter informações sobre como adquirir o isso, consulte: https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIM. 

5. Agora pode configurar o Azure AD para aprovisionar contas de utilizador para Cerner. Entrar para o [portal do Azure](https://portal.azure.com)e navegue para o **Azure Active Directory > aplicações empresariais > todos os aplicativos** secção.

6. Se já tiver configurado Cerner Central para início de sessão único, procure a sua instância do Cerner Central usando o campo de pesquisa. Caso contrário, selecione **Add** e procure **Cerner Central** na Galeria de aplicações. Selecione Cerner Central resultados da pesquisa e adicioná-lo à sua lista de aplicações.

7. Selecione a sua instância do Cerner Central, em seguida, selecione o **aprovisionamento** separador.

8. Definir o **modo de aprovisionamento** ao **automática**.

   ![Central de Cerner aprovisionamento](./media/cernercentral-provisioning-tutorial/Cerner.PNG)

9. Preencha os campos seguintes sob **credenciais de administrador**:

   * Na **URL de inquilino** campo, introduza um URL no formato abaixo, substituindo "User-lista de participantes-Realm-ID" com o ID de realm que obteve no passo 4 de #.

> Área de segurança: https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 
> 
> Produção: https://user-roster-api.cernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

   * Na **segredo de Token** campo, introduza o token de portador do OAuth geradas no passo 3 de # e clique em **Testar ligação**.

   * Deverá ver uma notificação de êxito no lado upperright do seu portal.

1. Introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento no **notificação por E-Mail** campo e marque a caixa de verificação abaixo.

1. Clique em **Guardar**. 

1. Na **mapeamentos de atributos** secção, reveja os atributos de utilizador e grupo a ser sincronizados a partir do Azure AD ao Cerner Central. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador e grupos no Cerner Central para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

1. Para ativar o Azure AD para Cerner Central do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção

1. Clique em **Guardar**. 

Esta ação inicia a sincronização inicial de todos os utilizadores e/ou grupos atribuídos ao Cerner Central na secção utilizadores e grupos. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e seguir links para os registos de atividades, que descrevem a todas as ações executadas pelo serviço de aprovisionamento na sua aplicação Cerner Central de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Central de Cerner: Publicação de dados de identidade com o Azure AD](https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+Azure+AD)
* [Tutorial: Configurar Cerner Central para início de sessão único com o Azure Active Directory](cernercentral-tutorial.md)
* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes
* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).
