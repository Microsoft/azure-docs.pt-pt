---
title: 'Tutorial: Configurar a Nuvem Analítica SAP para o provisionamento automático do utilizador com o Azure Ative Directory Microsoft Docs'
description: Saiba como provisão e desavisagem automática de contas de utilizadores do Azure AD para a SAP Analytics Cloud.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 27d12989-efa8-4254-a4ad-8cb6bf09d839
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/13/2020
ms.author: Zhchia
ms.openlocfilehash: f56aac256407b179dae2ef421217658ac102bbb4
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89458863"
---
# <a name="tutorial-configure-sap-analytics-cloud-for-automatic-user-provisioning"></a>Tutorial: Configurar a Nuvem Analítica SAP para o fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para realizar tanto na SAP Analytics Cloud como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, o Azure AD fornece automaticamente e desnegri os utilizadores e grupos para [a SAP Analytics Cloud](https://www.sapanalytics.cloud/) utilizando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações saaS com diretório Azure Ative.](../manage-apps/user-provisioning.md) 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores na Nuvem Analítica SAP
> * Remova os utilizadores na NUVEM Analítica SAP quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e SAP Analytics Cloud
> * [Único sinal de sação para](sapboc-tutorial.md) SAP Analytics Cloud (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD AZure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global). 
* Um inquilino da NUVEM SAP Analytics
* Uma conta de utilizador na consola de administração DE Fornecimento de Identidade SAP com permissões de administração. Certifique-se de que tem acesso aos sistemas proxy na consola de administração de fornecimento de identidade. Se não vir o azulejo **Proxy Systems,** crie um incidente para o componente **BC-IAM-IPS** solicitar o acesso a este azulejo.
* Um cliente da OAuth com autorização concede credenciais de cliente na SAP Analytics Cloud. Para saber como, consulte: [Gerir clientes da OAuth e fornecedores de identidade fidedignos](https://help.sap.com/viewer/00f68c2e08b941f081002fd3691d86a7/release/en-US/4f43b54398fc4acaa5efa32badfe3df6.html)

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planeie a sua implantação de provisionamento

1. Saiba [como funciona o serviço de prestação de serviços.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinar quem estará no [âmbito do provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais os dados a [mapear entre Azure AD e SAP Analytics Cloud](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-sap-analytics-cloud-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure a Nuvem Analítica SAP para apoiar o provisionamento com a Azure AD

1. Inscreva-se na [consola de administração DE Provisioning de Identidade SAP](https://ips-xlnk9v890j.dispatcher.us1.hana.ondemand.com/) com a sua conta de administrador e, em seguida, selecione **Sistemas Proxy**.

   ![Sistemas de Procuração SAP](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems.png.png)

2. Selecione **Propriedades**.

   ![Propriedades de sistemas de procuração SAP](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-properties.png)

3. Copie o **URL** e apenda-se `/api/v1/scim` ao URL. Guarde isto para mais tarde utilizar no campo URL do **inquilino.**

   ![URL de propriedades de sistemas de procuração de SAP](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-details-url.png)

4. Utilize [o POSTMAN](https://www.postman.com/) para efetuar uma chamada POST HTTPS para o endereço: `<Token URL>?grant_type=client_credentials` onde está o URL no campo `Token URL` **OAuth2TokenServiceURL.** Este passo é necessário para gerar um token de acesso para ser usado no campo Secret Token ao configurar o fornecimento automático.

   ![SAP IP Proxy Systems Properties OAuth](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-details-oauth.png)

5. No Carteiro, utilize **a Autenticação Básica**e desateia o ID do cliente OAuth como utilizador e o segredo como palavra-passe. Esta chamada devolve um sinal de acesso. Guarde isto copiado para mais tarde para ser usado no campo **Secret Token.**

   ![Pedido POSTAL Do Carteiro](./media/sap-analytics-cloud-provisioning-tutorial/postman-post-request.png)

## <a name="step-3-add-sap-analytics-cloud-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione a NUVEM SAP Analytics da galeria de aplicações AZure AD

Adicione a NUVEM AD SAP da galeria de aplicações AZure AD para começar a gerir o fornecimento à SAP Analytics Cloud. Se já configurar a SAP Analytics Cloud para SSO, pode utilizar a mesma aplicação. No entanto, recomenda-se que crie uma aplicação separada ao testar inicialmente a integração. Saiba mais sobre a adição de uma aplicação na galeria [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Definir quem estará no âmbito do provisionamento 

O serviço de prestação de Ad Azure permite-lhe atear âmbito a quem será a provisionado com base na atribuição à aplicação e ou com base em atributos do utilizador/grupo. Se optar por escolher o âmbito de aplicação de quem será aprovisionado na sua aplicação com base na atribuição, pode utilizar os [seguintes passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por escolher o âmbito de aplicação de quem será a provisionado apenas com base em atributos do utilizador ou grupo, pode utilizar um filtro de deteção como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos à NUVEM Analítica SAP, tem de selecionar outra função que não o **Acesso Predefinido**. Os utilizadores com a função De Acesso Predefinido estão excluídos do provisionamento e serão marcados como não efetivamente intitulados nos registos de provisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece minúsculo. Teste com um pequeno conjunto de utilizadores e grupos antes de rolar para todos. Quando o âmbito de provisão é definido para utilizadores e grupos atribuídos, pode controlá-lo atribuindo um ou dois utilizadores ou grupos à aplicação. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um [filtro de deteção baseado no atributo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-sap-analytics-cloud"></a>Passo 5. Configure o fornecimento automático de utilizadores para a NUVEM SAP Analytics 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-sap-analytics-cloud-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para a NUVEM Analítica SAP em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **SAP Analytics Cloud**.

    ![A ligação sap analytics cloud na lista de aplicações](common/all-applications.png)

3. Selecione o **separador Provisioning.**

    ![Separador de provisionamento](common/provisioning.png)

4. Desa ajuste o **modo de provisionamento** para **automático**.

    ![Separador de provisionamento](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** insira o valor URL do inquilino recuperado anteriormente na **URL do inquilino.** Insira o valor do token de acesso recuperado anteriormente em **Secret Token**. Clique em **'Testar' Ligação** para garantir que o Azure AD pode ligar-se ao InVision. Se a ligação falhar, certifique-se de que a sua conta SAP Analytics Cloud tem permissões de Administração e tente novamente.

    ![provisionamento](./media/sap-analytics-cloud-provisioning-tutorial/provisioning.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e selecione a **notificação enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![E-mail de notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Provision Azure Ative Directory Users**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD a SAP Analytics Cloud na secção **De Mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador na NUVEM Analítica SAP para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que a API da Cloud SAP Analytics suporta utilizadores filtrantes com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|Suportado para filtragem|
   |---|---|---|
   |userName|String|&check;|
   |nome.dado Nome|String|
   |nome.famíliaName|String|
   |ativo|Booleano|
   |e-mails[tipo eq "work"].value|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|String|

10. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro de escotagem](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de fornecimento de Ad Azure para a Nuvem Analítica SAP, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de Provisionamento Toggled On](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja prestar à SAP Analytics Cloud, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para a provisão, clique em **Guardar**.

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

## <a name="next-steps"></a>Próximos passos

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
