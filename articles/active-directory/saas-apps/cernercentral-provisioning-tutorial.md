---
title: 'Tutorial: provisionamento de usuário para o Cerner central – Azure AD'
description: Saiba como configurar Azure Active Directory para provisionar automaticamente os usuários em uma lista no Cerner central.
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
ms.openlocfilehash: 2e80373fa28f1ea24d6a2d5fc2c147bf81b2b279
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276518"
---
# <a name="tutorial-configure-cerner-central-for-automatic-user-provisioning"></a>Tutorial: configurar o Cerner central para provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que você precisa executar no Cerner central e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD para uma lista de usuários no Cerner central.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Um inquilino do Azure Active Directory
* Um locatário do Cerner central

> [!NOTE]
> O Azure Active Directory integra-se com o Cerner central usando o protocolo [scim](http://www.simplecloud.info/) .

## <a name="assigning-users-to-cerner-central"></a>Atribuindo usuários ao Cerner central

Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e grupos que foram "atribuídos" a um aplicativo no Azure AD são sincronizados. 

Antes de configurar e habilitar o serviço de provisionamento, você deve decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao Cerner central. Depois de decidir, você pode atribuir esses usuários ao Cerner central seguindo estas instruções:

[Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Dicas importantes para atribuir usuários ao Cerner central

* É recomendável que um único usuário do Azure AD seja atribuído ao Cerner central para testar a configuração de provisionamento. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Depois que o teste inicial for concluído para um único usuário, o Cerner central recomendará a atribuição de toda a lista de usuários destinada a acessar qualquer solução de Cerner (não apenas a Cerner central) a ser provisionada na lista de usuários do Cerner.  Outras soluções de Cerner aproveitam essa lista de usuários na lista de usuários.

* Ao atribuir um usuário ao Cerner central, você deve selecionar a função de **usuário** na caixa de diálogo de atribuição. Os usuários com a função de "acesso padrão" são excluídos do provisionamento.

## <a name="configuring-user-provisioning-to-cerner-central"></a>Configurando o provisionamento de usuário para o Cerner central

Esta seção orienta você pela conexão do Azure AD à lista de usuários do Cerner central usando a API de provisionamento de conta de usuário SCIM do Cerner e Configurando o serviço de provisionamento para criar, atualizar e desabilitar contas de usuário atribuídas no Cerner central com base em atribuição de usuário e grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Cerner central, seguindo as instruções fornecidas em [portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos se complementem. Para obter mais informações, consulte o [tutorial de logon único do Cerner central](cernercentral-tutorial.md).

### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>Para configurar o provisionamento automático de conta de usuário para o Cerner central no Azure AD:

Para provisionar contas de usuário no Cerner central, você precisará solicitar uma conta de sistema do Cerner central do Cerner e gerar um token de portador OAuth que o Azure AD possa usar para se conectar ao ponto de extremidade SCIM do Cerner. Também é recomendável que a integração seja executada em um ambiente de área restrita do Cerner antes de implantar na produção.

1. A primeira etapa é garantir que as pessoas que gerenciam o Cerner e a integração do Azure AD tenham uma conta do CernerCare, que é necessária para acessar a documentação necessária para concluir as instruções. Se necessário, use as URLs abaixo para criar contas do CernerCare em cada ambiente aplicável.

   * Área restrita: https://sandboxcernercare.com/accounts/create

   * Produção: https://cernercare.com/accounts/create  

2. Em seguida, uma conta do sistema deve ser criada para o Azure AD. Use as instruções abaixo para solicitar uma conta de sistema para seus ambientes de área de ti e de produção.

   * Instruções: https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * Área restrita: https://sandboxcernercentral.com/system-accounts/

   * Produção: https://cernercentral.com/system-accounts/

3. Em seguida, gere um token de portador OAuth para cada uma de suas contas de sistema. Para fazer isso, siga as instruções abaixo.

   * Instruções: https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * Área restrita: https://sandboxcernercentral.com/system-accounts/

   * Produção: https://cernercentral.com/system-accounts/

4. Por fim, você precisa adquirir IDs de realm de lista de usuários para ambientes de área restrita e de produção no Cerner para concluir a configuração. Para obter informações sobre como adquirir isso, consulte: https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIM. 

5. Agora você pode configurar o Azure AD para provisionar contas de usuário para o Cerner. Entre no [portal do Azure](https://portal.azure.com)e navegue até a seção **Azure Active Directory > aplicativos empresariais > todos os aplicativos** .

6. Se você já tiver configurado o Cerner central para logon único, pesquise sua instância do Cerner central usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise o **Cerner central** na Galeria de aplicativos. Selecione Cerner central nos resultados da pesquisa e adicione-o à lista de aplicativos.

7. Selecione sua instância do Cerner central e, em seguida, selecione a guia **provisionamento** .

8. Defina o **modo de provisionamento** como **automático**.

   ![Provisionamento central do Cerner](./media/cernercentral-provisioning-tutorial/Cerner.PNG)

9. Preencha os campos a seguir em **credenciais de administrador**:

   * No campo **URL do locatário** , insira uma URL no formato abaixo, substituindo "User-Ship-Realm-ID" pela ID de realm que você adquiriu na etapa #4.

    > Área restrita: https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 
    > 
    > Produção: https://user-roster-api.cernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

   * No campo **token secreto** , insira o token de portador OAuth gerado na etapa #3 e clique em **testar conexão**.

   * Você deverá ver uma notificação de êxito no lado do upperright do seu portal.

1. Insira o endereço de email de uma pessoa ou grupo que deve receber notificações de erro de provisionamento no campo **email de notificação** e marque a caixa de seleção abaixo.

1. Clique em **Guardar**.

1. Na seção **mapeamentos de atributo** , examine os atributos de usuário e grupo a serem sincronizados do Azure ad para o Cerner central. Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário e grupos no Cerner central para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

1. Para habilitar o serviço de provisionamento do Azure AD para o Cerner central, altere o **status de provisionamento** para **ativado** na seção **configurações**

1. Clique em **Guardar**.

Isso inicia a sincronização inicial de todos os usuários e/ou grupos atribuídos ao Cerner central na seção usuários e grupos. A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para os logs de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento em seu aplicativo Cerner central.

Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Cerner central: Publicando dados de identidade usando o Azure AD](https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+Azure+AD)
* [Tutorial: Configurando o Cerner central para logon único com o Azure Active Directory](cernercentral-tutorial.md)
* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).
