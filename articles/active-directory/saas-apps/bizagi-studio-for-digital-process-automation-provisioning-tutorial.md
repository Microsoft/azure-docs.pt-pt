---
title: 'Tutorial: Configure Bizagi Studio para automatização de processos digitais para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Saiba como provisão e desprovisionar automaticamente as contas de utilizadores do Azure AD para o Bizagi Studio para a Automatização de Processos Digitais.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 2fbff65a-5345-4c08-a6c7-60b80d867a3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2020
ms.author: Zhchia
ms.openlocfilehash: 4eaac716d06b102a07872059af28da4986889caa
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673445"
---
# <a name="tutorial-configure-bizagi-studio-for-digital-process-automation-for-automatic-user-provisioning"></a>Tutorial: Configure Bizagi Studio para automatização de processos digitais para fornecimento automático de utilizadores

Este tutorial descreve os passos que precisa de executar tanto no Bizagi Studio para a Automatização de Processos Digitais como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático de utilizadores. Quando configurado para o fazer, o Azure AD fornece automaticamente e desprovisiona utilizadores e grupos para [o Bizagi Studio for Digital Process Automation,](https://www.bizagi.com/) utilizando o serviço de fornecimento de AD Azure. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores no Bizagi Studio para automatização de processos digitais
> * Remova os utilizadores no Estúdio Bizagi para automatização de processos digitais quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre a Azure AD e o Bizagi Studio para automatização de processos digitais
> * [Único sinal no](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial) Bizagi Studio para automatização de processos digitais (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem o seguinte:

* [Um inquilino do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant). 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento. Exemplos incluem administrador de aplicação, administrador de aplicação em nuvem, proprietário de aplicações ou administrador global. 
* Bizagi Studio para a versão 11.2.4.2X ou posterior.

## <a name="plan-your-provisioning-deployment"></a>Planear a sua implementação de aprovisionamento
Siga estes passos para o planeamento:

1. Saiba [como funciona o serviço de aprovisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determinar quem estará [no âmbito do provisionamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine quais os dados a [mapear entre a Azure AD e o Bizagi Studio para a Automatização de Processos Digitais.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="configure-to-support-provisioning-with-azure-ad"></a>Configure para apoiar o provisionamento com a Azure AD
Para configurar o Bizagi Studio para a Automatização de Processos Digitais para apoiar o provisionamento com a AZure AD, siga estes passos:

1. Inscreva-se no seu portal de trabalho como utilizador com **permissões de Administração**.

2. Vá à **Admin**  >  **Security**  >  **OAuth 2 Applications**.

   ![Screenshot de Bizagi, com OAuth 2 Aplicações em destaque.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/admin.png)

3. Selecione **Adicionar**.
4. Para **Grant Type**, selecione o símbolo do **portador**. Para **o âmbito permitido**, selecione **API** e **USER SYNC**. Em seguida, selecione **Guardar**.

   ![Screenshot da Aplicação de Registo, com o Tipo de Subvenção e o Âmbito Permitido em destaque.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/token.png)

5. Copie e guarde o Segredo do **Cliente.** No portal Azure, para a sua aplicação Bizagi Studio for Digital Process Automation, no **separador Provisioning,** o valor secreto do cliente é introduzido no campo **Secret Token.**

   ![Screenshot de Oauth, com cliente secreto alto.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/secret.png)

## <a name="add-the-application-from-the-azure-ad-gallery"></a>Adicione a aplicação da galeria AD AZure

Para começar a gerir o fornecimento ao Bizagi Studio para a Automatização de Processos Digitais, adicione a aplicação da galeria de aplicações AD AZure. Se já criou o Bizagi Studio para automatização de processos digitais para uma única sação, pode utilizar a mesma aplicação. No entanto, quando está inicialmente a testar a integração, deve criar uma aplicação separada. Para mais informações, consulte [Quickstart: Adicione uma aplicação ao seu inquilino Azure Ative Directory (Azure AD).](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="define-who-is-in-scope-for-provisioning"></a>Definir quem está no âmbito do provisionamento 

Com o serviço de prestação de Ad Azure, pode estender o âmbito de aplicação a quem for a provisionado com base na atribuição à aplicação, com base em atributos do utilizador e do grupo, ou ambos. Se tiver uma mira com base na atribuição, consulte os passos em [utilizadores de atribuição ou de não-design, e grupos, para uma aplicação que utilize a API do Gráfico](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se se basear apenas em atributos do utilizador ou grupo, pode utilizar um filtro de deteção. Para obter mais informações, consulte [o provisionamento de aplicações baseados em Atributos com filtros de escoamento](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

Note os seguintes pontos sobre a deteção:

* Ao atribuir utilizadores e grupos ao Bizagi Studio para automatização de processos digitais, tem de selecionar uma função diferente do **Acesso Predefinido**. Os utilizadores com a função de acesso predefinido estão excluídos do provisionamento, e estão marcados nos registos de provisionamento, tal como não serão efetivamente intitulados. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar mais funções. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores e grupos antes de implementar para todos. Quando o âmbito de provisionamento é definido para utilizadores e grupos atribuídos, você pode controlá-lo atribuindo um ou dois utilizadores ou grupos à app. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um [filtro de deteção baseado em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="configure-automatic-user-provisioning"></a>Configurar o aprovisionamento automático de utilizadores 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e grupos. Está a fazer isso na sua aplicação de teste, com base em atribuições de utilizador e grupo no Azure AD.

### <a name="configure-automatic-user-provisioning-for-bizagi-studio-for-digital-process-automation-in-azure-ad"></a>Configure o fornecimento automático de utilizadores para o Bizagi Studio para automatização de processos digitais em Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações empresariais**  >  **Todas as aplicações**.

    ![Screenshot do portal Azure, com aplicações da Enterprise e todas as aplicações em destaque.](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Bizagi Studio para Automatização de Processos Digitais.**

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções de Gestão, com Provisioning em destaque.](common/provisioning.png)

4. Desa ajuste **o modo de provisionamento** para **automático**.

    ![Screenshot do controlo do modo de provisionamento, com o Automático em destaque.](common/provisioning-automatic.png)

5. Na secção **Credenciais de Administração,** insira o URL do seu inquilino e o símbolo secreto do Bizagi Studio for Digital Process Automation. 

      * **URL do inquilino:** Insira o ponto final bizagi SCIM, com a seguinte estrutura:  `<Your_Bizagi_Project>/scim/v2/` .
         Por exemplo: `https://my-company.bizagi.com/scim/v2/`.

      * **Ficha secreta:** Este valor é recuperado a partir do passo discutido anteriormente neste artigo.

      Para garantir que o Azure AD pode ligar-se ao Estúdio Bizagi para automação de processos digitais, selecione **Test Connection**. Se a ligação falhar, certifique-se de que o seu Estúdio Bizagi para Automatização de Processos Digitais tem permissões de administrador e tente novamente.

   ![Screenshot de Credenciais de Administrador, com Ligação de Teste em destaque.](common/provisioning-testconnection-tenanturltoken.png)

6. Para **o Email de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento. Selecione a opção para **Enviar uma notificação de e-mail quando ocorrer uma falha**.

    ![Screenshot das opções de e-mail de notificação.](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para Bizagi Studio for Digital Process Automation**.

9. Na secção **Attribute-Mapping,** reveja os atributos do utilizador que são sincronizados de AZure AD a Bizagi Studio para Automatização de Processos Digitais. Os atributos selecionados como propriedades **correspondentes** são utilizados para combinar as contas de utilizador no Estúdio Bizagi para automatização de processos digitais para operações de atualização. Se alterar o [atributo de alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)deve certificar-se de que o Estúdio Bizagi para a API de Automação de Processos Digitais suporta utilizadores filtrantes com base nesse atributo. **Selecione Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|Suportado para filtragem|
   |---|---|---|
   |userName|String|&check;|
   |active|Booleano|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |nome.formatado|String|
   |phoneNumbers[type eq "mobile"].value|String|

   Os atributos de extensão personalizada podem ser adicionados navegando para **mostrar opções avançadas > editar lista de atributos para Bizagi**. Os atributos de extensão personalizados devem ser pré-fixados com **urna:ietf:params:scim:schemas:extension:bizagi:2.0:UserProperties:**. Por exemplo, se o atributo de extensão personalizada for **IdentificationNumber,** o atributo deve ser adicionado como **urna:ietf:params:scim:schemas:extension:bizagi:2.0:UserProperties:IdentificationNumber**. **Selecione Guardar** para escoar quaisquer alterações.
   
    ![Editar a lista de atributos.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/edit.png)  

   Mais informações sobre como adicionar atributos personalizados podem ser encontradas em [Atributos de Aplicação Personalizadas.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)

> [!NOTE]
> Apenas propriedades básicas do tipo são suportadas (por exemplo, String, Inteiro, Boolean, DateTime, etc). As propriedades ligadas a mesas paramétricas ou vários tipos ainda não estão suportadas.

10. Para configurar filtros de deteção, consulte o tutorial do [filtro scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de fornecimento de AD Azure para o Estúdio Bizagi para Automatização de Processos Digitais, na secção **Definições,** altere o **Estado de Provisionamento** para **On**.

    ![Screenshot do estado de provisionamento alternar.](common/provisioning-toggle-on.png)

12. Defina os utilizadores e grupos que pretende providenciar ao Bizagi Studio para a Automatização de Processos Digitais. Na secção **Definições,** escolha os valores pretendidos no **Âmbito**.

    ![Screenshot das opções scope.](common/provisioning-scope.png)

13. Quando estiver pronto para a provisão, **selecione Save**.

    ![Screenshot do controlo Save.](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo de sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições**. O ciclo inicial leva mais tempo a ser executado do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do Azure AD esteja em execução. 

## <a name="monitor-your-deployment"></a>Monitorizar a implementação
Depois de configurar o provisionamento, use os seguintes recursos para monitorizar a sua implantação:

- Utilize os [registos de provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram a provisionados com sucesso ou sem sucesso.
- Verifique a barra de [progresso](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de provisionamento e o quão perto está de ser concluído.
- Se a configuração do provisionamento estiver num estado pouco saudável, a aplicação entrará em quarentena. Para obter mais informações, consulte [o provisionamento do pedido de quarentena.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)  

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)
