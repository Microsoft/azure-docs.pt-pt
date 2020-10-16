---
title: 'Tutorial: Configurar o Apple Business Manager para o fornecimento automático de utilizadores com o Azure Ative Directory Microsoft Docs'
description: Saiba como oferecer e desa provisionar automaticamente as contas de utilizadores do Azure AD para o Apple Business Manager.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 4ad30031-9904-4ac3-a4d2-e8c28d44f319
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2020
ms.author: Zhchia
ms.openlocfilehash: ff017671496816f0142bd3b71c3df1a3769b274b
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2020
ms.locfileid: "91941097"
---
# <a name="tutorial-configure-apple-business-manager-for-automatic-user-provisioning"></a>Tutorial: Configurar o Apple Business Manager para o fornecimento automático de utilizadores



Este tutorial descreve os passos que precisa de executar tanto no Apple Business Manager como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático de utilizadores. Quando configurado, o Azure AD fornece automaticamente e desnegri os utilizadores ao [Apple Business Manager](https://business.apple.com/) utilizando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../manage-apps/user-provisioning.md). 

## <a name="capabilities-supported"></a>Capacidades Suportadas
> [!div class="checklist"]
> * Criar utilizadores no Apple Business Manager
> * Remova os utilizadores no Apple Business Manager quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre a Azure AD e o Apple Business Manager

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global).
* Uma conta Apple Business Manager com o papel de Administrador ou Gestor de Pessoas.

> [!NOTE]
> A transferência de Token para a Azure AD e o estabelecimento de uma ligação bem sucedida tem de ser concluída em 4 dias de calendário ou o processo tem de ser reiniciado.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento
1. Saiba [como funciona o serviço de aprovisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determine quem vai estar no [âmbito do aprovisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais os dados a [mapear entre a Azure AD e o Apple Business Manager](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-apple-business-manager-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure o Apple Business Manager para apoiar o provisionamento com a AZure AD

1. No Apple Business Manager, inscreva-se com uma conta que tem o papel de Administrador ou Gestor de Pessoas.
2. Clique em Definições na parte inferior da barra lateral clique em Data Source abaixo Das Definições da Organização e, em seguida, clique em Ligar à Fonte de Dados.
3. Clique em Ligar ao lado do SCIM, ler cuidadosamente o aviso, clicar em Copiar e clicar em Fechar.
[A janela Connect to SCIM, que fornece um token e um botão Copy debaixo dela.] Deixe esta janela aberta para copiar o URL do Inquilino da Apple Business Manager para a Azure AD, que é: https://federation.apple.com/feeds/business/scim '

    ![Apple Business Manager](media/applebusinessmanager-provisioning-tutorial/scim-token.png)

> [!NOTE]
> O símbolo secreto não deve ser partilhado com ninguém além do administrador da AD Azure.

## <a name="step-3-add-apple-business-manager-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione o Apple Business Manager da galeria de aplicações AZure AD

Adicione o Apple Business Manager da galeria de aplicações AZure AD para começar a gerir o fornecimento ao Apple Business Manager. Se já configurar o Apple Business Manager para SSO, pode utilizar a mesma aplicação. No entanto, é recomendável criar uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento 

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores ao Apple Business Manager, tem de selecionar outra função que não o **Acesso Padrão.** Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito do aprovisionamento está definido para os utilizadores e os grupos atribuídos, pode controlar isto ao atribuir um ou dois utilizadores ou grupos à aplicação. Quando o âmbito está definido para todos os utilizadores e grupos, pode especificar um [filtro de âmbito baseado em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

## <a name="step-5-configure-automatic-user-provisioning-to-apple-business-manager"></a>Passo 5. Configure o fornecimento automático de utilizadores ao Apple Business Manager

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Apple Business Manager**.

    ![O Gestor de Negócios da Apple na lista de aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Separador Aprovisionamento](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Separador de provisionamento automático](common/provisioning-automatic.png)

5. Sob a secção **Credenciais de Administração,** insira os valores **DE URL base SCIM 2.0 e Access Token** recuperados do Apple Business Manager em **URL** de inquilino e **Token Secreto,** respectivamente. Clique em **Test Connection** para garantir que o Azure AD pode ligar-se ao Apple Business Manager. Se a ligação falhar, certifique-se de que a sua conta Apple Business Manager tem permissões de Administração e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

> [!NOTE]
>Se a ligação for bem sucedida, o Apple Business Manager mostra a ligação SCIM como ativa. Este processo pode demorar até 60 segundos para o Apple Business Manager refletir o mais recente estado de ligação.

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para Apple Business Manager**.

9. Reveja os atributos do utilizador que são sincronizados do AD AD a Apple Business Manager na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de matching** são usados para combinar as contas de utilizador no Apple Business Manager para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|
   |---|---|
   |active|Booleano|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.givenName|String|
   |externalId|String|
   |região|String|
   |timezone|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|Cadeia|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|Cadeia|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|Cadeia|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Cadeia|

10. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de fornecimento de Ad Azure para o Apple Business Manager, altere o **Estado de Provisionamento** para **On** na secção Definições.

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que gostaria de oferecer ao Apple Business Manager, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução.

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o aprovisionamento, utilize os seguintes recursos para monitorizar a sua implementação:

1. Utilize os [registos de aprovisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram aprovisionados com ou sem êxito
2. Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de aprovisionamento e quão próximo está da conclusão
3. Se a configuração de aprovisionamento parecer estar num mau estado de funcionamento, a aplicação vai entrar em quarentena. Saiba mais sobre os estados de quarentena [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Reveja os requisitos do SCIM para o Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apdd88331cd6)
* [Como um ID de pessoa é usado no Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apd69e1e48e9)
* [Use o SCIM para importar utilizadores para o Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apd3ec7b95ad)
* [Resolver conflitos na conta de utilizador scim no Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apd313013d12)
* [Eliminar contas AD Azure que aparecem no Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apdaa5798fbe)
* [Ver atividade scim no Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apd1bfd8dfde)
* [Gerir o token SCIM existente e as conexões no Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apdc9a823611)
* [Desligue a ligação SCIM no Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apd609be3a61)
* [Gerir o token SCIM existente e as conexões no Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apdc9a8236e9)
* [Resolução de problemas da ligação SCIM no Apple Business Manager](https://support.apple.com/guide/apple-business-manager/apd403a0f3bd/web)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)

