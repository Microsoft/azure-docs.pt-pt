---
title: 'Tutorial: Configurar a Formação de Sensibilização para a Segurança da Webroot para o fornecimento automático de utilizadores com diretório ativo Azure / Microsoft Docs'
description: Saiba como providenciar e desincêrenciar automaticamente contas de utilizadores do Azure AD à Webroot Security Awareness Training.
services: active-directory
author: Zhchia
writer: Zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/06/2020
ms.author: Zhchia
ms.openlocfilehash: 9345aaac306c8c009d6fc0a01c57beed3a22b10b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88523495"
---
# <a name="tutorial-configure-webroot-security-awareness-training-for-automatic-user-provisioning"></a>Tutorial: Configurar a Formação de Sensibilização para a Segurança da Webroot para o fornecimento automático de utilizadores

Este tutorial descreve os passos que você precisa realizar tanto no Webroot Security Awareness Training como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, a Azure AD fornece automaticamente e desescvisões utilizadores e grupos para [a Webroot Security Awareness Training](https://www.webroot.com/) utilizando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações saaS com diretório Azure Ative.](../manage-apps/user-provisioning.md) 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores em Formação de Sensibilização para a Segurança da Webroot
> * Remova os utilizadores no Treino de Sensibilização para a Segurança da Webroot quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e Webroot Security Awareness Training
> * Grupos de fornecimento e membros do grupo na Formação de Sensibilização para a Segurança da Webroot

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global).
* Uma consola de fornecedor de serviços gerido com treino de sensibilização para a segurança da Webroot habilitado para pelo menos um dos seus sites.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planeie a sua implantação de provisionamento
1. Saiba [como funciona o serviço de prestação de serviços.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinar quem estará no [âmbito do provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais os dados a [mapear entre Azure AD e Webroot Security Awareness Training](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-webroot-security-awareness-training-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure a Formação de Sensibilização para a Segurança da Webroot para apoiar o provisionamento com a Azure AD

### <a name="obtain-a-secret-token"></a>Obtenha um símbolo secreto

Para ligar o seu site ao Azure AD, terá de obter um **Token Secreto** para esse site na consola de gestão Webroot.

1. Inscreva-se na sua [consola de gestão Webroot](https://identity.webrootanywhere.com/v1/Account/login#tab_customers)

2. A partir do separador **Sites,** clique no ícone de engrenagem na coluna Desconseção de Segurança para o site que deseja ligar com Azure AD.

    ![Ícone de engrenagem](./media/webroot-security-awareness-training-provisioning-tutorial/gear-icon.png)

3. Clique no botão para **configurar a integração ad Ad Azure**.

    ![Configurar integração ad azure](./media/webroot-security-awareness-training-provisioning-tutorial/configure-azure-ad-integration.png)

4. Copie e guarde o **Token Secreto.** Este valor será introduzido no campo Secret Token no separador Provisioning da sua aplicação webroot Security Awareness Training no portal Azure.

5. Clique em **Concluído**.

    ![Cópia Segredo Token](./media/webroot-security-awareness-training-provisioning-tutorial/copy-secret-token.png)

## <a name="step-3-add-webroot-security-awareness-training-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione a Webroot Security Awareness Training da galeria de aplicações Azure AD

Adicione a Webroot Security Awareness Training a partir da galeria de aplicações Azure AD para começar a gerir o fornecimento à Formação de Sensibilização para a Segurança da Webroot. Saiba mais sobre a adição de uma aplicação na galeria [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Definir quem estará no âmbito do provisionamento 

O serviço de prestação de Ad Azure permite-lhe atear âmbito a quem será a provisionado com base na atribuição à aplicação e ou com base em atributos do utilizador/grupo. Se optar por escolher o âmbito de aplicação de quem será aprovisionado na sua aplicação com base na atribuição, pode utilizar os [seguintes passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por escolher o âmbito de aplicação de quem será a provisionado apenas com base em atributos do utilizador ou grupo, pode utilizar um filtro de deteção como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos à Formação de Sensibilização para a Segurança da Webroot, tem de selecionar outra função que não o **Acesso Padrão**. Os utilizadores com a função De Acesso Predefinido estão excluídos do provisionamento e serão marcados como não efetivamente intitulados nos registos de provisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece minúsculo. Teste com um pequeno conjunto de utilizadores e grupos antes de rolar para todos. Quando o âmbito de provisão é definido para utilizadores e grupos atribuídos, pode controlá-lo atribuindo um ou dois utilizadores ou grupos à aplicação. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um [filtro de deteção baseado no atributo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-webroot-security-awareness-training"></a>Passo 5. Configure o fornecimento automático de utilizadores à Formação de Sensibilização para a Segurança da Webroot 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-webroot-security-awareness-training-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para a Formação de Sensibilização para a Segurança da Webroot em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Webroot Security Awareness Training**.

    ![O link webroot Security Awareness Training na lista de aplicações](common/all-applications.png)

3. Selecione o **separador Provisioning.**

    ![Separador de provisionamento](common/provisioning.png)

4. Desa ajuste o **modo de provisionamento** para **automático**.

    ![Separador de provisionamento](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** entrada `https://awarenessapi.webrootanywhere.com/api/v2/scim` na URL do **inquilino.** Insira o valor simbólico secreto recuperado anteriormente em **Secret Token**. Clique em **Test Connection** para garantir que a Azure AD pode ligar-se ao Treino de Sensibilização para a Segurança webroot. Se a ligação falhar, certifique-se de que a sua conta de Formação de Sensibilização para a Segurança da Webroot tem permissões de administração e tente novamente.

    ![provisionamento](./media/webroot-security-awareness-training-provisioning-tutorial/provisioning.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e selecione a **notificação enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![E-mail de notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Provision Azure Ative Directory Users**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD a Webroot Security Awareness Training na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são usados para combinar as contas de utilizador no Webroot Security Awareness Training para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que a API de Formação de Sensibilização para a Segurança da Webroot suporta utilizadores filtrantes com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|Suportado para filtragem|
   |---|---|---|
   |externoId|String|&check;|
   |nome.dado Nome|String|
   |nome.famíliaName|String|
   |e-mails[tipo eq "work"].value|String|

10. Na secção **Mappings,** selecione **Provision Azure Ative Directory Groups**.

11. Reveja os atributos do grupo que são sincronizados de Azure AD a Webroot Security Awareness Training na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar com os grupos no Webroot Security Awareness Training para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

      |Atributo|Tipo|Suportado para filtragem|
      |---|---|---|
      |displayName|String|&check;|
      |membros|Referência|
      |externoId|String|

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro de escotagem](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para permitir o serviço de prestação de Ad Azure para a Formação de Sensibilização para a Segurança da Webroot, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de Provisionamento Toggled On](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar à Webroot Security Awareness Training, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para a provisão, clique em **Guardar**.

    ![Configuração de provisionamento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** O ciclo inicial demora mais tempo a ser efetuado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em funcionamento. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o provisionamento, utilize os seguintes recursos para monitorizar a sua implantação:

1. Utilize os [registos de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram a provisionados com sucesso ou sem sucesso
2. Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de provisionamento e quão perto está da sua conclusão
3. Se a configuração do provisionamento parecer estar num estado pouco saudável, a aplicação entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)  

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
