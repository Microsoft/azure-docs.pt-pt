---
title: 'Tutorial: Configurar o GroupTalk para o fornecimento automático de utilizadores com o Azure Ative Directory / Microsoft Docs'
description: Saiba como provisão e desaparamento automaticamente de contas de utilizador do Azure AD ao GroupTalk.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: e537d393-2724-450f-9f5b-4611cdc9237c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: 107ce33f753b275f694558ec1fec2f07e2316b36
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96031405"
---
# <a name="tutorial-configure-grouptalk-for-automatic-user-provisioning"></a>Tutorial: Configurar o GroupTalk para o fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para executar tanto no GroupTalk como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, o Azure AD fornece automaticamente e desescvisões utilizadores e grupos para [o GroupTalk](https://www.grouptalk.com/) utilizando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Capacidades Suportadas
> [!div class="checklist"]
> * Criar utilizadores no GroupTalk
> * Remova os utilizadores no GroupTalk quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e GroupTalk
> * Grupos de provisão e membros do grupo no GroupTalk

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global). 
* Uma conta de utilizador no GroupTalk com permissões de Administração.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento
1. Saiba [como funciona o serviço de aprovisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determine quem vai estar no [âmbito do aprovisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais os dados a [mapear entre Azure AD e GroupTalk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-grouptalk-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure o GroupTalk para apoiar o provisionamento com a Azure AD

1. Contacte o Suporte GroupTalk support@grouptalk.com com o nome do **Inquilino** e **iD** que gostaria de integrar com a Azure AD.
2. Quando tiver sido notificado de que a configuração necessária para a sua integração AZure AD está pronta, faça login no GroupTalk Admin e navegue para a sua visão da Organização. 
3. Deve ser visível um item de configuração de integração AD Azure. Clique nele para verificar o nome do **inquilino** e **iD**  para obter um **JWT (Secret Token)**. 
4. O URL do Inquilino GroupTalk é `https://api.grouptalk.com/api/scim/` . O **URL do inquilino** e o **Token Secreto** recuperados no passo anterior serão inseridos no separador Provisioning da sua aplicação GroupTalk no portal Azure. 

## <a name="step-3-add-grouptalk-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione GroupTalk da galeria de aplicações AZure AD

Adicione **GroupTalk** da galeria de aplicações AZure AD para começar a gerir o fornecimento ao GroupTalk.

1. Clique no **botão 'Iniciar sê-lo' para** o botão GroupTalk, que o encaminhará para a aplicação administrativa GroupTalk.
2. Se já tiver iniciado sessão no GroupTalk, faça login para chegar ao ecrã de login. Selecione o separador AD Azure e clique no botão **Iniciar súbdido.**

    ![GroupTalk](media/grouptalk-provisioning-tutorial/login.png)

3. Faça login com a sua conta Administrativa AD e aceite os direitos de acesso da aplicação GroupTalk. Receberá uma mensagem de erro depois de esta ser feita indicando que o utilizador não está presente. Isto é esperado uma vez que o seu utilizador ainda não está a ser a provisionado para o GroupTalk, mas agora adicionou o GroupTalk ao seu inquilino.
4. Volte ao portal Azure e verifique se o **GroupTalk** está agora adicionado às suas Aplicações Empresariais.

Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento 

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos ao GroupTalk, tem de selecionar outra função que não o **Acesso Predefinido**. Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito do aprovisionamento está definido para os utilizadores e os grupos atribuídos, pode controlar isto ao atribuir um ou dois utilizadores ou grupos à aplicação. Quando o âmbito está definido para todos os utilizadores e grupos, pode especificar um [filtro de âmbito baseado em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-grouptalk"></a>Passo 5. Configure o fornecimento automático de utilizadores ao GroupTalk 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-grouptalk-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o GroupTalk em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **GroupTalk**.

    ![O link GroupTalk na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Separador Aprovisionamento](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Separador de provisionamento automático](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** insira o URL do seu Inquilino GroupTalk e o Token Secreto recuperados anteriormente do Passo 2. Clique em **'Test Connection'** para garantir que o Azure AD pode ligar-se ao GroupTalk. Se a ligação falhar, certifique-se de que a sua conta GroupTalk tem permissões de Administração e tente novamente. Pode sempre obter um novo Token Secreto, conforme descrito no Passo 2.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **E-mail de Notificação**, introduza o endereço de e-mail de uma pessoa ou um grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação **Enviar uma notificação de e-mail quando ocorre uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to GroupTalk**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD a GroupTalk na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador no GroupTalk para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que a API GroupTalk suporta utilizadores filtrantes com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|Suportado para filtragem|
   |---|---|---|
   |userName|String|&check;|
   |phoneNumbers[type eq "mobile"].value|String|&check;|
   |emails[type eq "work"].value|String|&check;|
   |active|Booleano|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|
   |urn:ietf:params:scim:schemas:extension:grouptalk:2.0:User:label1|String|
   |urn:ietf:params:scim:schemas:extension:grouptalk:2.0:User:label2|String|
   |urn:ietf:params:scim:schemas:extension:grouptalk:2.0:User:label3|String|
   |urn:ietf:params:scim:schemas:extension:grouptalk:2.0:User:label4|String|
   |urn:ietf:params:scim:schemas:extension:grouptalk:2.0:User:label5|String|


10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to GroupTalk**.

11. Reveja os atributos do grupo que são sincronizados de Azure AD a GroupTalk na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder aos grupos no GroupTalk para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

      |Atributo|Tipo|Suportado para filtragem|
      |---|---|---|
      |displayName|String|&check;|
      |membros|Referência|
      |externalId|String|      
      |urn:ietf:params:scim:schemas:extension:grouptalk:2.0:Group:description|String|

12. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para o GroupTalk, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar ao GroupTalk, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo de sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições**. O ciclo inicial leva mais tempo a ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o aprovisionamento, utilize os seguintes recursos para monitorizar a sua implementação:

1. Utilize os [registos de aprovisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram aprovisionados com ou sem êxito
2. Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de aprovisionamento e quão próximo está da conclusão
3. Se a configuração de aprovisionamento parecer estar num mau estado de funcionamento, a aplicação vai entrar em quarentena. Saiba mais sobre os estados de quarentena [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).
4. Pode contactar o GroupTalk Support para obter registos de provisionamento adicionais configurados como relatórios personalizados dentro do GroupTalk Admin. Estes podem dar pistas adicionais de que os utilizadores e grupos não prestam adequadamente.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)
