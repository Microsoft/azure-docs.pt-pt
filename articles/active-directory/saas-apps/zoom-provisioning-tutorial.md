---
title: 'Tutorial: Configure Zoom para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Saiba como provisão e desavisagem automática de contas de utilizadores do Azure AD ao Zoom.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/3/2019
ms.author: Zhchia
ms.openlocfilehash: fb5e51503fa20cd3ace6070cb1b51f5bf32d0e13
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87016270"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Tutorial: Configure Zoom para o fornecimento automático de utilizadores

Este tutorial descreve os passos necessários para realizar tanto no Zoom como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático do utilizador. Quando configurado, a Azure AD fornece automaticamente e desescvisões utilizadores e grupos para [Zoom](https://zoom.us/pricing/) usando o serviço de provisionamento Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações saaS com diretório Azure Ative.](../manage-apps/user-provisioning.md) 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores em Zoom
> * Remova os utilizadores no Zoom quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e Zoom
> * [Único sinal de zoom](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-tutorial) (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD Azure.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação cloud, Proprietário de Aplicações ou Administrador Global). 
* [Um inquilino de Zoom.](https://zoom.us/pricing)
* Uma conta de utilizador em Zoom com permissões de Administrador.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planeie a sua implantação de provisionamento
1. Saiba [como funciona o serviço de prestação de serviços.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinar quem estará no [âmbito do provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais os dados a [mapear entre Azure AD e Zoom](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-zoom-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure Zoom para apoiar o provisionamento com a Azure AD

1. Inscreva-se na sua [Consola de Administração de Zoom](https://zoom.us/signin). Navegue para **Advanced > Zoom para Desenvolvedores** no painel de navegação esquerdo.

    ![Integrações de Zoom](media/zoom-provisioning-tutorial/zoom01.png)

2. Navegue para **Gerir** no canto superior direito da página. 

    ![Instalação de zoom](media/zoom-provisioning-tutorial/zoom02.png)

3. Navegue para a sua aplicação AD Azure criada. 
    
    ![Zoom App](media/zoom-provisioning-tutorial/zoom03.png)

4. Selecione **Credenciais de Aplicação** no painel de navegação esquerdo.

    ![Zoom App](media/zoom-provisioning-tutorial/zoom04.png)

5. Copie e guarde o **Token JWT**. Este valor será introduzido no campo **Secret Token** no separador Provisioning da sua aplicação Zoom no portal Azure. Se precisar de um novo token não expirado, terá de reconfigurar o tempo de validade que irá gerar um novo token. 

    ![Instalação de zoom](media/zoom-provisioning-tutorial/zoom05.png)

## <a name="step-3-add-zoom-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione Zoom da galeria de aplicações AZure AD

Adicione Zoom da galeria de aplicações AZure AD para começar a gerir o provisionamento ao Zoom. Se tiver configurado previamente Zoom para SSO, pode utilizar a mesma aplicação. No entanto, recomenda-se que crie uma aplicação separada ao testar inicialmente a integração. Saiba mais sobre a adição de uma aplicação na galeria [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Definir quem estará no âmbito do provisionamento 

O serviço de prestação de Ad Azure permite-lhe atear âmbito a quem será a provisionado com base na atribuição à aplicação e ou com base em atributos do utilizador/grupo. Se optar por escolher o âmbito de aplicação de quem será aprovisionado na sua aplicação com base na atribuição, pode utilizar os [seguintes passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por escolher o âmbito de aplicação de quem será a provisionado apenas com base em atributos do utilizador ou grupo, pode utilizar um filtro de deteção como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos ao Zoom, tem de selecionar outra função que não **o Acesso Predefinido**. Os utilizadores com a função De Acesso Predefinido estão excluídos do provisionamento e serão marcados como não efetivamente intitulados nos registos de provisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece minúsculo. Teste com um pequeno conjunto de utilizadores e grupos antes de rolar para todos. Quando o âmbito de provisão é definido para utilizadores e grupos atribuídos, pode controlá-lo atribuindo um ou dois utilizadores ou grupos à aplicação. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um [filtro de deteção baseado no atributo](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-zoom"></a>Passo 5. Configure o fornecimento automático do utilizador ao Zoom 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Para configurar o fornecimento automático do utilizador para zoom em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Zoom**.

    ![O link Zoom na lista de Aplicações](common/all-applications.png)

3. Selecione o **separador Provisioning.**

    ![Separador de provisionamento](common/provisioning.png)

4. Desa ajuste o **modo de provisionamento** para **automático**.

    ![Separador de provisionamento](common/provisioning-automatic.png)

5. Sob a secção **de Credenciais de Administração,** `https://api.zoom.us/scim` insira em **URL de inquilino.** Insira o valor **JWT Token** recuperado anteriormente em **Secret Token**. Clique em **Testar a Ligação** para garantir que o Azure AD pode ligar-se ao Zoom. Se a ligação falhar, certifique-se de que a sua conta Zoom tem permissões de Administração e tente novamente.

    ![Provisão de zoom](./media/zoom-provisioning-tutorial/provisioning.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e selecione a **notificação enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![E-mail de notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para Zoom**.

9. Reveja os atributos do utilizador que são sincronizados de AD AD a Zoom na secção **De mapeamento de atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador no Zoom para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que a API de Zoom suporta utilizadores filtrantes com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|
   |---|---|
   |userName|Cadeia|
   |ativo|Booleano|
   |nome.dado Nome|Cadeia|
   |nome.famíliaName|Cadeia|
   |e-mails[tipo eq "work"]|Cadeia|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Cadeia|

10. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro de escotagem](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de provisionamento Azure AD para Zoom, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de Provisionamento Toggled On](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja providenciar ao Zoom, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para a provisão, clique em **Guardar**.

    ![Configuração de provisionamento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** O ciclo inicial demora mais tempo a ser efetuado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em funcionamento. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o provisionamento, utilize os seguintes recursos para monitorizar a sua implantação:

1. Utilize os [registos de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram a provisionados com sucesso ou sem sucesso
2. Verifique a [barra de progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de provisionamento e quão perto está da sua conclusão
3. Se a configuração do provisionamento parecer estar num estado pouco saudável, a aplicação entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)  

## <a name="connector-limitations"></a>Limitações do conector
* O Zoom só permite um máximo de 9.999 utilizadores básicos hoje em dia.

## <a name="change-log"></a>Change log
* 05/14/2020 - Apoio às operações de ATUALIZAÇÃO adicionadas para e-mails[tipo eq "trabalho"] atribuem.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
