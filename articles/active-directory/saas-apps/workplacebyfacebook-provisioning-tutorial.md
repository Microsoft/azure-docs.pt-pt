---
title: 'Tutorial: Configurar o Workplace pelo Facebook para o fornecimento automático de utilizadores com o Azure Ative Directory Microsoft Docs'
description: Conheça os passos necessários para realizar tanto no Workplace by Facebook como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático de utilizadores.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 04/28/2020
ms.author: jeedes
ms.openlocfilehash: c72ca7cb521c5c7e7ed33e9a0539de0df252ac92
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91321905"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Tutorial: Configurar o Local de Trabalho pelo Facebook para o fornecimento automático de utilizadores

Este tutorial descreve os passos que precisa de executar tanto no Workplace do Facebook como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático de utilizadores. Quando configurado, a Azure AD fornece automaticamente e desresesa os utilizadores e grupos para [o Local de Trabalho pelo Facebook](https://work.workplace.com/) utilizando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores no Workplace pelo Facebook
> * Remova os utilizadores no Workplace pelo Facebook quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre AZure AD e Workplace pelo Facebook
> * [Único sinal de trabalho](https://docs.microsoft.com/azure/active-directory/saas-apps/workplacebyfacebook-tutorial) por facebook (recomendado)

>[!VIDEO https://www.youtube.com/embed/oF7I0jjCfrY]

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global)
* Um local de trabalho pelo Facebook de um único sinal na subscrição ativada

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização de um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a não ser que seja necessário.
- Se não tiver um ambiente de experimentação da AD Azure, pode ter um julgamento de um mês [aqui.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento
1. Saiba [como funciona o serviço de aprovisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determine quem vai estar no [âmbito do aprovisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais os dados a [mapear entre Azure AD e Workplace pelo Facebook](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure Workplace by Facebook para apoiar o provisionamento com Azure AD

Antes de configurar e ativar o serviço de fornecimento, é necessário decidir quais os utilizadores e/ou grupos em Azure AD que representam os utilizadores que precisam de acesso ao seu Workplace através da aplicação facebook. Uma vez decididos, pode atribuir estes utilizadores ao seu Workplace pela aplicação facebook seguindo as instruções aqui:

*   Recomenda-se que um único utilizador AZure AD seja designado para o Workplace pelo Facebook para testar a configuração de provisionamento. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador ao Workplace pelo Facebook, tem de selecionar uma função de utilizador válida. A função "Acesso Predefinido" não funciona para o provisionamento.

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione Workplace pelo Facebook a partir da galeria de aplicações AZure AD

Adicione Workplace pelo Facebook a partir da galeria de aplicações AZure AD para começar a gerir o fornecimento ao Workplace pelo Facebook. Se já configurar workplace by Facebook for SSO pode utilizar a mesma aplicação. No entanto, é recomendável criar uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento 

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos ao Workplace pelo Facebook, tem de selecionar outra função que não o **Acesso Predefinido**. Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito do aprovisionamento está definido para os utilizadores e os grupos atribuídos, pode controlar isto ao atribuir um ou dois utilizadores ou grupos à aplicação. Quando o âmbito está definido para todos os utilizadores e grupos, pode especificar um [filtro de âmbito baseado em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Workplace by Facebook**.

    ![O link Workplace by Facebook na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Na secção **Credenciais de Administração,** clique em **Authorize**. Será redirecionado para o Local de Trabalho pela página de autorização do Facebook. Insira o seu Local de Trabalho pelo nome de utilizador do Facebook e clique no botão **Continuar.** Clique em **Testar A Ligação** para garantir que a Azure AD pode ligar-se ao Workplace pelo Facebook. Se a ligação falhar, certifique-se de que o seu Workplace by Facebook conta com permissões de Administração e tente novamente.

    ![A screenshot mostra caixa de diálogo de Admin Credentials com uma opção Authorize.](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![autorizar](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. No campo **E-mail de Notificação**, introduza o endereço de e-mail de uma pessoa ou um grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação **Enviar uma notificação de e-mail quando ocorre uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Workplace by Facebook**.

9. Reveja os atributos do utilizador que são sincronizados do AD AD do Azure para o Workplace pelo Facebook na secção **De mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no Workplace pelo Facebook para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que o Workplace by Facebook API suporta a filtragem dos utilizadores com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|
   |---|---|
   |userName|String|
   |displayName|String|
   |active|Booleano|
   |título|Booleano|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |nome.formatado|Cadeia|
   |endereços[tipo eq "work"].formatado|Cadeia|
   |endereços[tipo eq "work"].streetAddress|Cadeia|
   |endereços[tipo eq "work"].localidade|Cadeia|
   |endereços[tipo eq "work"].região|Cadeia|
   |endereços[tipo eq "work"].país|Cadeia|
   |endereços[tipo eq "work"].postalCode|Cadeia|
   |endereços[tipo eq "outros"].formatados|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |números de telefone[tipo eq "fax"].valor|Cadeia|
   |externalId|String|
   |preferiuLanguage|String|
   |urn:scim:schemas:extension:enterprise:1.0.manager|String|
   |urn:scim:schemas:extension:enterprise:1.0.department|String|
   |urn:scim:schemas:extension:enterprise:1.0.division|String|
   |urn:scim:schemas:extension:enterprise:1.0.organization|String|
   |urn:scim:schemas:extension:enterprise:1.0.costCenter|String|
   |urn:scim:schemas:extension:enterprise:1.0.employeeNumber|String|
   |urn:scim:schemas:extensão:facebook:auth_method:1.0:auth_method|Cadeia|
   |urn:scim:schemas:extension:facebook:frontline:1.0.is_frontline|Booleano|
   |urn:scim:schemas:extension:facebook:starttermdates:1.0.startDate|Número inteiro|


10. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de prestação de Ad Azure para o Local de Trabalho pelo Facebook, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja prestar ao Workplace pelo Facebook, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo de sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições**. O ciclo inicial leva mais tempo a ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do Azure AD esteja em execução. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o aprovisionamento, utilize os seguintes recursos para monitorizar a sua implementação:

1. Utilize os [registos de aprovisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram aprovisionados com ou sem êxito
2. Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de aprovisionamento e quão próximo está da conclusão
3. Se a configuração de aprovisionamento parecer estar num mau estado de funcionamento, a aplicação vai entrar em quarentena. Saiba mais sobre os estados de quarentena [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="troubleshooting-tips"></a>Sugestões de resolução de problemas
*  Se vir um utilizador criado sem sucesso e houver um evento de registo de auditoria com o código "1789003" significa que o utilizador é de um domínio não verificado.

## <a name="change-log"></a>Change log

* 09/10/2020 - Apoio adicional aos atributos empresariais "divisão", "organização", "costCenter" e "employeeNumber". Suporte adicionado para atributos personalizados "startDate", "auth_method" e "linha da frente"

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)
