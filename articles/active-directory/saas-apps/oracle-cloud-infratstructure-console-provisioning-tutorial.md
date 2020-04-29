---
title: 'Tutorial: Configure Consola de Infraestrutura oracle cloud para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a fornecer e desfornecer automaticamente contas de utilizadores de Azure AD para Oracle Cloud Infrastructure Console.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: e22c8746-7638-4a0f-ae08-7db0c477cf52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: Zhchia
ms.openlocfilehash: 5aa33529a1957b6e7728b3a87bacf6bb91d987ae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81378954"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>Tutorial: Configure consola de infraestrutura oracle cloud para fornecimento automático de utilizadores

Este tutorial descreve os passos que precisa de executar tanto na Consola de Infraestruturas oracle Cloud como no Diretório Ativo Azure (Azure AD) para configurar o fornecimento automático de utilizadores. Quando configurado, o Azure AD disponibiliza e desativa automaticamente utilizadores e grupos para a Consola de [Infraestruturas Oracle Cloud](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) utilizando o serviço de provisionamento de AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../manage-apps/user-provisioning.md) 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores na Consola de Infraestruturas Oracle Cloud
> * Remova os utilizadores na Consola de Infraestruturas Oracle Cloud quando já não necessitam de acesso
> * Mantenha os atributos dos utilizadores sincronizados entre a Azure AD e a Oracle Cloud Infrastructure Console
> * Grupos de provisão e membros do grupo na Consola de Infraestruturas Oracle Cloud
> * Inscrição única na Consola de [Infraestruturas](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial) Oracle Cloud (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD Azure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação na Nuvem, Proprietário de Aplicações ou Administrador Global). 
* Um [inquilino](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton)do Controlo de Infraestruturas da Nuvem Oracle.
* Uma conta de utilizador no Controlo de Infraestruturas oracle Cloud com permissões de administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planeie a sua implantação de provisionamento
1. Saiba como funciona o serviço de [provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinar quem estará no [âmbito do provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Determine quais os dados a [mapear entre a Azure AD e a Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure consola de infraestrutura oracle cloud para apoiar o provisionamento com AD Azure

1. Inicie sessão no portal de administração da Consola de Infraestruturas Oracle Cloud. No canto superior esquerdo do ecrã navega para **a Federação de > de Identidade.**

    ![Administração oráculo](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. Clique no URL exibido na página ao lado da Consola de Serviço de Nuvem de Identidade Oracle.

    ![URL do oráculo](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. Clique em **Adicionar Fornecedor de Identidade** para criar um novo fornecedor de identidade. Guarde o ID ID para ser usado como parte da URL do inquilino. Clique no ícone plus ao lado do separador **Aplicações** para criar um AppRole de Administrador de Domínio de Identidade OAuth e Grant IDCS.

    ![Ícone de nuvem de oráculo](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. Siga as imagens abaixo para configurar a sua aplicação. Uma vez terminada a configuração clique em **Guardar**.

    ![Configuração do Oráculo](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Política de Emissão de Símbolos do Oráculo](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. Sob o separador de configurações da sua aplicação, expanda a opção **Informação Geral** para recuperar o ID do cliente e o segredo do cliente.

    ![Geração simbólica oráculo](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. Para gerar um token secreto Base64 codificar o ID do cliente e o segredo do cliente no formato **ID:Client Secret**. Salve o símbolo secreto. Este valor será inserido no campo **Secret Token** no separador de fornecimento da sua aplicação Oracle Cloud Infrastructure Console no portal Azure.

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione a Consola de Infraestruturas Oracle Cloud da galeria de aplicações Azure AD

Adicione a Consola de Infraestruturas Oracle Cloud da galeria de aplicações Azure AD para começar a gerir o provisionamento à Consola de Infraestruturas Oracle Cloud. Se já configurasse a Consola de Infraestruturas Oracle Cloud para SSO, pode utilizar a mesma aplicação. No entanto, recomenda-se que crie uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre a adição de uma aplicação na galeria [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4. Definir quem estará no âmbito do provisionamento 

O serviço de provisionamento de AD Azure permite-lhe examinar quem será provisionado com base na atribuição à aplicação e ou com base em atributos do utilizador/grupo. Se optar por examinar quem será aprovisionado na sua app com base na atribuição, pode utilizar os [seguintes passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por examinar quem será aprovisionado apenas com base em atributos do utilizador ou do grupo, pode utilizar um filtro de deteção como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos à Consola de Infraestruturas Oracle Cloud, deve selecionar uma função diferente do **Acesso Padrão**. Os utilizadores com a função de Acesso Predefinido estão excluídos do fornecimento e serão marcados como não tendo direito efetivamente nos registos de fornecimento. Se a única função disponível na aplicação for a função de acesso padrão, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece minúsculo. Teste com um pequeno conjunto de utilizadores e grupos antes de passar para todos. Quando o âmbito de fornecimento for definido para utilizadores e grupos atribuídos, pode controlá-lo atribuindo um ou dois utilizadores ou grupos à aplicação. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um filtro de [deteção baseado em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>Passo 5. Configure o fornecimento automático de utilizadores à Consola de Infraestruturas Oracle Cloud 

Esta secção orienta-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para a Consola de Infraestruturas Oracle Cloud em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Oracle Cloud Infrastructure Console**.

    ![O link da Consola de Infraestruturas Oracle Cloud na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. Na secção **credenciais de administrador,** insera `https://<IdP ID>.identity.oraclecloud.com/admin/v1` o URL do **Arrendatário** no formato . Por exemplo, `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`. Insera o valor secreto do símbolo recuperado anteriormente em **Secret Token.** Clique em **Ligação de Teste** para garantir que o Azure AD pode ligar-se à Consola de Infraestruturas Oracle Cloud. Se a ligação falhar, certifique-se de que a sua conta Oracle Cloud Infrastructure Console tem permissões de administração e tente novamente.

    ![provisionamento](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. No campo de email de **notificação,** introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e selecione a **notificação de e-mail enviar uma notificação de e-mail quando ocorrer uma falha** verificar a caixa.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users para Oracle Cloud Infrastructure Console**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Oracle Cloud Infrastructure Console na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador na Consola de Infraestruturas Oracle Cloud para operações de atualização. Se optar por alterar o [atributo-alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que a API da Consola de Infraestruturas Oracle Cloud suporta filtrar os utilizadores com base nesse atributo. Selecione o botão **Guardar** para elegiro qualquer alteração.

      |Atributo|Tipo|
      |---|---|
      |displayName|String|
      |userName|String|
      |ativo|Booleano|
      |título|String|
      |e-mails[tipo eq "work"].valor|String|
      |língua preferida|String|
      |nome.dadoNome|String|
      |nome.familyName|String|
      |endereços[tipo eq "trabalho"].formado|String|
      |endereços[tipo eq "trabalho"].localidade|String|
      |endereços[tipo eq "trabalho"].região|String|
      |endereços[tipo eq "trabalho"].código postal|String|
      |endereços[tipo eq "trabalho"].país|String|
      |endereços[tipo eq "trabalho"].streetAddress|String|
      |urn:ietf:params:scim:schemas:extens:enterprise:2.0:Utilizador:empregadoNúmero|String|
      |urn:ietf:params:scim:schemas:extens:enterprise:2.0:User:department|String|
      |urn:ietf:params:scim:schemas:extens:enterprise:2.0:User:costCenter|String|
      |urn:ietf:params:scim:schemas:extens:enterprise:2.0:User:division|String|
      |urn:ietf:params:scim:schemas:extens:enterprise:2.0:User:manager|Referência|
      |urn:ietf:params:scim:schemas:extens:enterprise:2.0:User:organization|String|
      |urn:ietf:params:scim:schemas:oracle:idcs:extensão:utilizador:Utilizador:bypassNotifica|Booleano|
      |urn:ietf:params:scim:schemas:oracle:idcs:extension:user:User:isFederatedUser|Booleano|

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups para Oracle Cloud Infrastructure Console**.

11. Reveja os atributos do grupo que são sincronizados de Azure AD para Oracle Cloud Infrastructure Console na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar os grupos na Consola de Infraestruturas Oracle Cloud para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

      |Atributo|Tipo|
      |---|---|
      |displayName|String|
      |id externo|String|
      |membros|Referência|

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento de AD Azure para a Consola de Infraestruturas Oracle Cloud, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que gostaria de fornecer à Consola de Infraestruturas Oracle Cloud, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** O ciclo inicial demora mais tempo a realizar do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento da AD Azure esteja em funcionamento. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o fornecimento, utilize os seguintes recursos para monitorizar a sua implementação:

* Utilize os registos de [provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram provisionados com sucesso ou sem sucesso
* Verifique a barra de [progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de provisionamento e quão perto está da conclusão
* Se a configuração do fornecimento parecer estar num estado pouco saudável, a aplicação entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
