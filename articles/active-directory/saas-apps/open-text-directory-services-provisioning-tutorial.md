---
title: 'Tutorial: Configurar Serviços de Diretório OpenText para fornecimento automático de utilizadores com Diretório Ativo Azure Microsoft Docs'
description: Saiba como providenciar e desa provisionar automaticamente as contas de utilizadores do Azure AD para os Serviços de Diretório OpenText.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: ad55ba5f-c56c-4ed0-bdfd-163d2883ed80
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: c023d9b79b81257419814c3087b78f65e1cb6dd5
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94355996"
---
# <a name="tutorial-configure-opentext-directory-services-for-automatic-user-provisioning"></a>Tutorial: Configurar serviços de diretório OpenText para fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para realizar tanto nos Serviços de Diretório OpenText como no Diretório Azure Ative (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, a Azure AD fornece automaticamente e desescvisões utilizadores e grupos para Serviços de Diretório OpenText utilizando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores em Serviços de Diretório OpenText
> * Remova os utilizadores nos Serviços de Diretório OpenText quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre o Azure AD e os Serviços de Diretório OpenText
> * Grupos de fornecimento e membros do grupo nos Serviços de Diretório OpenText
> * [Único sinal de abertura](https://docs.microsoft.com/azure/active-directory/saas-apps/opentext-directory-services-tutorial) para serviços de diretório OpenText (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global). 
* Uma instalação OTDS acessível pela Azure AD.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento
1. Saiba [como funciona o serviço de aprovisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determine quem vai estar no [âmbito do aprovisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais os dados a [mapear entre o Azure AD e os Serviços de Diretório OpenText](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-opentext-directory-services-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure os Serviços de Diretório OpenText para apoiar o fornecimento com a Azure AD

> [!NOTE]
> Os passos abaixo aplicam-se a uma instalação de Serviços de Diretório OpenText. Não se candidatam a inquilinos OpenText CoreShare ou OpenText OT2.

1. Crie um cliente confidencial dedicado **da OAuth.**
2. Desema estada uma longa **vida útil do símbolo de acesso**.

      ![Access Token Life](media/open-text-directory-services-provisioning-tutorial/token-life.png)

3. Não especifique quaisquer URLs de redirecionamento. Não são necessários. 
4. O OTDS gerará e exibirá o segredo do **cliente.** Guarde a **identificação** do cliente e **o segredo** do cliente num local seguro.

      ![Segredo do Cliente](media/open-text-directory-services-provisioning-tutorial/client-secret.png)

5. Crie uma divisória para que os utilizadores e grupos sejam sincronizados a partir do Azure AD.

      ![Página de partição](media/open-text-directory-services-provisioning-tutorial/partition.png)

6. Conceda direitos administrativos ao cliente OAuth que criou na partição que utilizará para os utilizadores e grupos AD Azure que estão a ser sincronizados.    
      * Partição -> Ações -Administradores de Edição >

      ![Página do administrador](media/open-text-directory-services-provisioning-tutorial/administrator.png)

5. Um símbolo secreto deve ser recuperado e configurado em Azure AD. Qualquer aplicação do cliente HTTP pode ser utilizada para este problema. Abaixo estão os passos para recuperar usando a aplicação Swagger API incluída na OTDS.
      * Num navegador web, vá a {OTDS URL}/otdsws/oauth2
      * Vá a /token e clique no ícone de bloqueio na parte superior direita. Insira o ID do cliente OAuth e o segredo recuperado anteriormente como nome de utilizador e senha, respectivamente. Clique em Autorizor.

      ![Botão de autorização](media/open-text-directory-services-provisioning-tutorial/authorization.png)

6. Selecione **client_credentials** para o grant_type e clique em **Executar**.

      ![Executar buton](media/open-text-directory-services-provisioning-tutorial/execute.png)

7. O token de acesso na resposta deve ser usado no campo **Secret Token** em Azure AD.

      ![Token de Acesso](media/open-text-directory-services-provisioning-tutorial/access-token.png)

## <a name="step-3-add-opentext-directory-services-from-the-azure-ad-application-gallery"></a>Passo 3. Adicionar Serviços de Diretório OpenText da galeria de aplicações Azure AD

Adicione serviços de diretório OpenText da galeria de aplicações Azure AD para começar a gerir o fornecimento aos Serviços de Diretório OpenText. Se já configurar serviços de diretório OpenText para SSO, pode utilizar a mesma aplicação. No entanto, é recomendável criar uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento 

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos aos Serviços de Diretório OpenText, tem de selecionar outra função que não o **Acesso Predefinido**. Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito do aprovisionamento está definido para os utilizadores e os grupos atribuídos, pode controlar isto ao atribuir um ou dois utilizadores ou grupos à aplicação. Quando o âmbito está definido para todos os utilizadores e grupos, pode especificar um [filtro de âmbito baseado em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-opentext-directory-services"></a>Passo 5. Configure o fornecimento automático de utilizadores aos Serviços de Diretório OpenText 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-opentext-directory-services-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para os Serviços de Diretório OpenText em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **OpenText Directory Services**.

    ![O link OpenText Directory Services na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Separador Aprovisionamento](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Separador de provisionamento automático](common/provisioning-automatic.png)

5. Na secção **Credenciais de Administração,** insira o URL do seu Diretório De Serviços OpenText
   * Url de inquilino não-specifc : {OTDS URL}/scim/{partitionName}
   * Url de inquilino específico : {OTDS URL}/otdstenant/{tenantID}/scim/{partitionName}

6. Insira o Token Secreto recuperado do passo 2. Clique em **'Test Connection'** para garantir que o Azure AD pode ligar-se aos Serviços de Diretório OpenText. Se a ligação falhar, certifique-se de que a sua conta OpenText Directory Services tem permissões de administração e tente novamente.

      ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **E-mail de Notificação** , introduza o endereço de e-mail de uma pessoa ou um grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação **Enviar uma notificação de e-mail quando ocorre uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para OpenText Directory Services**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Serviços de Diretório OpenText na secção **De mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder às contas do utilizador nos Serviços de Diretório OpenText para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que a API suporta a filtragem dos utilizadores com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|
   |---|---|
   |userName|String|
   |active|Booleano|
   |displayName|String|
   |título|String|
   |emails[type eq "work"].value|String|
   |preferiuLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |nome.formatado|String|
   |endereços[tipo eq "work"].formatado|String|
   |endereços[tipo eq "work"].streetAddress|String|
   |endereços[tipo eq "work"].localidade|String|
   |endereços[tipo eq "work"].região|String|
   |endereços[tipo eq "work"].postalCode|String|
   |endereços[tipo eq "work"].país|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |números de telefone[tipo eq "fax"].valor|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referência| 

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups para OpenText Directory Services**.

11. Reveja os atributos do grupo que são sincronizados de Azure AD para OpenText Directory Services na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder aos grupos nos Serviços de Diretório OpenText para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

      |Atributo|Tipo|
      |---|---|
      |displayName|String|
      |externalId|String|
      |membros|Referência|

12. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de prestação de Ad Azure para serviços de diretório OpenText, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar aos Serviços de Diretório OpenText, escolhendo os valores pretendidos no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo de sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições**. O ciclo inicial leva mais tempo a ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o aprovisionamento, utilize os seguintes recursos para monitorizar a sua implementação:

1. Utilize os [registos de aprovisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram aprovisionados com ou sem êxito
2. Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de aprovisionamento e quão próximo está da conclusão
3. Se a configuração de aprovisionamento parecer estar num mau estado de funcionamento, a aplicação vai entrar em quarentena. Saiba mais sobre os estados de quarentena [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)
