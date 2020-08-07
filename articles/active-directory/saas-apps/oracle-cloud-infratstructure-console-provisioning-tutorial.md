---
title: 'Tutorial: Configurar consola de infraestrutura oracle cloud para fornecimento automático de utilizadores com Diretório Ativo Azure Microsoft Docs'
description: Saiba como providenciar e desabilitado automaticamente contas de utilizadores do Azure AD para a Consola de Infraestruturas da Nuvem Oracle.
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
ms.openlocfilehash: 94f5be93d95cc1c524e6db464cac68879ed0b0e7
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926795"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>Tutorial: Configurar consola de infraestrutura oracle cloud para fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para realizar tanto na Consola de Infraestruturas da Oracle Cloud como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, o Azure AD fornece automaticamente e desnegri os utilizadores e grupos para a [Oracle Cloud Infrastructure Console](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) utilizando o serviço de provisionamento Ad Ad Azure. Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações saaS com diretório Azure Ative.](../manage-apps/user-provisioning.md) 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores na Consola de Infraestruturas oracle Cloud
> * Remova os utilizadores na Consola de Infraestruturas oracle Cloud quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre a Azure AD e a Consola de Infraestruturas oracle Cloud
> * Grupos de fornecimento e membros do grupo na Oracle Cloud Infrastructure Console
> * Único sinal de assinatura para consola [de infraestrutura](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial) de nuvem oracle (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global). 
* Um [inquilino](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton)do Controlo de Infraestruturas da Nuvem oracle.
* Uma conta de utilizador no Controlo de Infraestruturas da Oracle Cloud com permissões de administração.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planeie a sua implantação de provisionamento
1. Saiba [como funciona o serviço de prestação de serviços.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinar quem estará no [âmbito do provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais os dados a [mapear entre a Azure AD e a Consola de Infraestruturas da Nuvem oracle.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure consola de infraestrutura em nuvem oracle para apoiar o fornecimento com Azure AD

1. Faça login no portal de administração da Oracle Cloud Infrastructure Console. No canto superior esquerdo do ecrã navegue para **a Federação > Identidade**.

    ![Administrador do Oráculo](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. Clique no URL exibido na página ao lado da Consola de Serviço de Cloud da Oracle Identity.

    ![URL do Oráculo](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. Clique no **Add Identity Provider** para criar um novo fornecedor de identidade. Guarde o idP id para ser usado como parte da URL do inquilino. Clique no ícone plus ao lado do separador **Aplicações** para criar um AppRole de Administrador de Domínio de Identidade da OAuth e Grant IDCS.

    ![Ícone de nuvem de oráculo](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. Siga as imagens abaixo para configurar a sua aplicação. Uma vez feita a configuração, clique em **Guardar**.

    ![Configuração do Oráculo](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Política de Emissão de Token Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. No separador de configurações da sua aplicação expande a opção **Informação Geral** para recuperar o ID do cliente e o segredo do cliente.

    ![Geração simbólica de oráculo](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. Para gerar um token secreto Base64 codificar o ID do cliente e o segredo do cliente no formato **ID:Client Secret**. Salve o símbolo secreto. Este valor será introduzido no campo **Secret Token** no separador de provisionamento da sua aplicação Oracle Cloud Infrastructure Console no portal Azure.

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione consola de infraestrutura oracle cloud da galeria de aplicações AZure AD

Adicione a Consola de Infraestruturas Oracle Cloud da galeria de aplicações AZure AD para começar a gerir o fornecimento à Consola de Infraestruturas oracle Cloud. Se já configurar previamente a Consola de Infraestruturas oracle Cloud para SSO, pode utilizar a mesma aplicação. No entanto, recomenda-se que crie uma aplicação separada ao testar inicialmente a integração. Saiba mais sobre a adição de uma aplicação na galeria [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Definir quem estará no âmbito do provisionamento 

O serviço de prestação de Ad Azure permite-lhe atear âmbito a quem será a provisionado com base na atribuição à aplicação e ou com base em atributos do utilizador/grupo. Se optar por escolher o âmbito de aplicação de quem será aprovisionado na sua aplicação com base na atribuição, pode utilizar os [seguintes passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por escolher o âmbito de aplicação de quem será a provisionado apenas com base em atributos do utilizador ou grupo, pode utilizar um filtro de deteção como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos à Consola de Infraestruturas da Nuvem Oracle, tem de selecionar outra função que não o **Acesso Predefinido**. Os utilizadores com a função De Acesso Predefinido estão excluídos do provisionamento e serão marcados como não efetivamente intitulados nos registos de provisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece minúsculo. Teste com um pequeno conjunto de utilizadores e grupos antes de rolar para todos. Quando o âmbito de provisão é definido para utilizadores e grupos atribuídos, pode controlá-lo atribuindo um ou dois utilizadores ou grupos à aplicação. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um [filtro de deteção baseado no atributo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>Passo 5. Configure o fornecimento automático de utilizadores ao Oracle Cloud Infrastructure Console 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para a Consola de Infraestruturas oracle cloud em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Oracle Cloud Infrastructure Console**.

    ![A ligação da Consola de Infraestruturas oracle Cloud na lista de aplicações](common/all-applications.png)

3. Selecione o **separador Provisioning.**

    ![Separador de provisionamento](common/provisioning.png)

4. Desa ajuste o **modo de provisionamento** para **automático**.

    ![Separador de provisionamento](common/provisioning-automatic.png)

5. Sob a secção **Credenciais de Administração,** insira o URL do **Inquilino** no formato `https://<IdP ID>.identity.oraclecloud.com/admin/v1` . Por exemplo, `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`. Insira o valor simbólico secreto recuperado anteriormente em **Secret Token**. Clique em **Testar Ligação** para garantir que o Azure AD pode ligar-se à Consola de Infraestruturas da Nuvem oracle. Se a ligação falhar, certifique-se de que a sua conta Oracle Cloud Infrastructure Console tem permissões de administração e tente novamente.

    ![provisionamento](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e selecione a **notificação enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![E-mail de notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para a Consola de Infraestruturas de Nuvem Oracle.**

9. Reveja os atributos do utilizador que são sincronizados do AD AD a Oracle Cloud Infrastructure Console na secção **De mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador na Consola de Infraestruturas oracle Cloud para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que a API da Consola de Infraestruturas da Oracle Cloud suporta utilizadores filtrantes com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

      |Atributo|Tipo|
      |---|---|
      |displayName|String|
      |userName|String|
      |ativo|Booleano|
      |título|String|
      |e-mails[tipo eq "work"].value|String|
      |preferiuLanguage|String|
      |nome.dado Nome|String|
      |nome.famíliaName|String|
      |endereços[tipo eq "work"].formatado|String|
      |endereços[tipo eq "work"].localidade|String|
      |endereços[tipo eq "work"].região|String|
      |endereços[tipo eq "work"].postalCode|String|
      |endereços[tipo eq "work"].país|String|
      |endereços[tipo eq "work"].streetAddress|String|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Referência|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|
      |urn:ietf:params:scim:schemas:oracle:idcs:extensão:utilizador:Utilizador:bypassNotificação|Booleano|
      |urn:ietf:params:scim:schemas:oracle:idcs:extension:user:User:isFederatedUser|Booleano|

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups para Oracle Cloud Infrastructure Console**.

11. Reveja os atributos do grupo que são sincronizados do AD AD a Oracle Cloud Infrastructure Console na secção **De mapeamento de Atributos.** Os atributos selecionados como propriedades **correspondentes** são utilizados para combinar com os grupos na Consola de Infraestruturas da Nuvem oracle para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

      |Atributo|Tipo|
      |---|---|
      |displayName|String|
      |externoId|String|
      |membros|Referência|

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro de escotagem](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de fornecimento de AD Azure para consola de infraestruturas de nuvem oracle, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de Provisionamento Toggled On](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja prestar à Consola de Infraestruturas da Oracle Cloud, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para a provisão, clique em **Guardar**.

    ![Configuração de provisionamento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** O ciclo inicial demora mais tempo a ser efetuado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em funcionamento. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o provisionamento, utilize os seguintes recursos para monitorizar a sua implantação:

* Utilize os [registos de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram a provisionados com sucesso ou sem sucesso
* Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de provisionamento e quão perto está da sua conclusão
* Se a configuração do provisionamento parecer estar num estado pouco saudável, a aplicação entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
