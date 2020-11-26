---
title: 'Tutorial: Configurar o Apple School Manager para o fornecimento automático de utilizadores com o Azure Ative Directory Microsoft Docs'
description: Saiba como oferecer e desa provisionar automaticamente as contas de utilizadores do Azure AD ao Apple School Manager.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: f006c177-7b35-4af1-84f2-db4a4e2bf96a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/08/2020
ms.author: Zhchia
ms.openlocfilehash: 00e3b40b0e3f4c799c54308b35ce3e810cde118c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181098"
---
# <a name="tutorial-configure-apple-school-manager-for-automatic-user-provisioning"></a>Tutorial: Configure Apple School Manager para o fornecimento automático de utilizadores



Este tutorial descreve os passos que precisa de executar tanto no Apple School Manager como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático de utilizadores. Quando configurado, o Azure AD fornece automaticamente e desescê-lo os utilizadores ao [Apple School Manager](https://school.apple.com/) utilizando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Capacidades Suportadas
> [!div class="checklist"]
> * Criar utilizadores no Apple School Manager
> * Remova os utilizadores no Apple School Manager quando já não necessitam de acesso
> * Mantenha os atributos específicos do utilizador sincronizados entre a Azure AD e o Apple School Manager

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](../develop/quickstart-create-new-tenant.md) 
* Uma conta de utilizador em Azure AD com [permissão](../roles/permissions-reference.md) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global). 
* Uma conta apple school manager com o papel de Administrador, Gestor de Sites ou Gestor de Pessoas.

> [!NOTE]
> A transferência de Token para a Azure AD e o estabelecimento de uma ligação bem sucedida tem de ser concluída em 4 dias de calendário ou o processo tem de ser reiniciado.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento
1. Saiba [como funciona o serviço de aprovisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem vai estar no [âmbito do aprovisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais os dados a [mapear entre Azure AD e Apple School Manager](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-apple-school-manager-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure o Apple School Manager para apoiar o provisionamento com a AZure AD

1. No Apple School Manager, inscreva-se com uma conta que tem o papel de Administrador, Gestor de Sites ou Gestor de Pessoas.
2. Clique em Definições na parte inferior da barra lateral clique em Data Source abaixo Das Definições da Organização e, em seguida, clique em Ligar à Fonte de Dados.
3. Clique em Ligar ao lado do SCIM, ler cuidadosamente o aviso, clicar em Copiar e clicar em Fechar.
[A janela Connect to SCIM, que fornece um token e um botão Copy debaixo dela.] Deixe esta janela aberta para copiar o URL do Inquilino da Apple Business Manager para a Azure AD, que é: https://federation.apple.com/feeds/school/scim '

    ![Gestor Escolar da Apple](media/appleschoolmanager-provisioning-tutorial/scim-token.png)

> [!NOTE]
> O símbolo secreto não deve ser partilhado com ninguém além do administrador da AD Azure.

## <a name="step-3-add-apple-school-manager-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione o Apple School Manager da galeria de aplicações AZure AD

Adicione o Apple School Manager da galeria de aplicações AZure AD para começar a gerir o fornecimento ao Apple School Manager. Se já configurar o Apple School Manager para SSO, pode utilizar a mesma aplicação. No entanto, é recomendável criar uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento 

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir os utilizadores ao Apple School Manager, tem de selecionar outra função que não o **Acesso Predefinido**. Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito do aprovisionamento está definido para os utilizadores e os grupos atribuídos, pode controlar isto ao atribuir um ou dois utilizadores ou grupos à aplicação. Quando o âmbito está definido para todos os utilizadores e grupos, pode especificar um [filtro de âmbito baseado em atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-apple-school-manager"></a>Passo 5. Configure o fornecimento automático de utilizadores ao Apple School Manager

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Apple School Manager**.

    ![O Gerente da Escola Apple na lista de aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Separador Aprovisionamento](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Separador de provisionamento automático](common/provisioning-automatic.png)

5. Sob a secção **credenciais de administração,** insira os valores **DE URL base SCIM 2.0 e Access Token** recuperados do Apple School Manager em **URL** de inquilino e **Token Secreto,** respectivamente. Clique em **Test Connection** para garantir que o Azure AD pode ligar-se ao Apple School Manager. Se a ligação falhar, certifique-se de que a sua conta Apple School Manager tem permissões de Administração e tente novamente.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

> [!NOTE]
>Se a ligação for bem sucedida, o Apple School Manager mostra a ligação SCIM como ativa. Este processo pode demorar até 60 segundos para o Apple School Manager refletir o estado mais recente da ligação.

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para Apple School Manager**.

9. Reveja os atributos do utilizador que são sincronizados do AD AD a Apple School Manager na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são usados para combinar as contas de utilizador no Apple School Manager para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

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
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

10. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de fornecimento de Ad Azure para o Apple School Manager, altere o **Estado de Provisionamento** para **On** na secção Definições.

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que gostaria de oferecer ao Apple School Manager, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução.

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação

Depois de configurar o aprovisionamento, utilize os seguintes recursos para monitorizar a sua implementação:

1. Utilize os [registos de aprovisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais os utilizadores que foram aprovisionados com ou sem êxito
2. Verifique a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o estado do ciclo de aprovisionamento e quão próximo está da conclusão
3. Se a configuração de aprovisionamento parecer estar num mau estado de funcionamento, a aplicação vai entrar em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Reveja os requisitos do SCIM para o Apple School Manager](https://support.apple.com/guide/apple-school-manager/apdd88331cd6)
* [Como um ID de pessoa é usado no Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd69e1e48e9)
* [Use o SCIM para importar utilizadores para o Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd3ec7b95ad)
* [Resolver conflitos na conta de utilizador scim no Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd313013d12)
* [Eliminar contas AD AZure que aparecem no Apple School Manager](https://support.apple.com/guide/apple-school-manager/apdaa5798fbe)
* [Ver atividade scim no Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd1bfd8dfde)
* [Gerir o token SCIM existente e as conexões no Apple School Manager](https://support.apple.com/guide/apple-school-manager/apdc9a823611)
* [Desligue a ligação SCIM no Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd609be3a61)
* [Resolução de problemas da ligação SCIM no Apple School Manager](https://support.apple.com/guide/apple-school-manager/apd403a0f3bd)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)