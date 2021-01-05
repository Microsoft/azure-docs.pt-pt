---
title: 'Tutorial: Fornecimento de utilizadores para MilEyes - Azure AD'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores à ThousandEyes.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: 885ee993748a0a571f396cc0dc28f2c0c1a4a0c3
ms.sourcegitcommit: 00aa5afaa9fac91f1059cfed3d8dbc954caaabe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/27/2020
ms.locfileid: "97792522"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>Tutorial: Configurar MilEyes para o provisionamento automático do utilizador

O objetivo deste tutorial é mostrar-lhe os passos que precisa de executar em ThousandEyes e AD Azure para provisões automáticas e desavisagem de contas de utilizadores de Azure AD a ThousandEyes. 

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que já tem os seguintes itens:

* Um inquilino de diretório Azure Ative
* Um inquilino milEyes com o [plano Standard](https://www.thousandeyes.com/pricing) ou melhor habilitado 
* Uma conta de utilizador em ThousandEyes com permissões de Administração 

> [!NOTE]
> A integração de provisionamento da AZure AD depende da [MilEyes SCIM API,](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK)que está disponível para equipas mileyes no plano Standard ou melhor.

## <a name="assigning-users-to-thousandeyes"></a>Atribuir utilizadores a ThousandEyes

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático da conta de utilizador, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação em AZure AD são sincronizados. 

Antes de configurar e ativar o serviço de fornecimento, tem de decidir quais os utilizadores e/ou grupos em AZure AD que representam os utilizadores que precisam de acesso à sua aplicação MilEyes. Uma vez decididos, pode atribuir estes utilizadores à sua aplicação MilEyes seguindo as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>Dicas importantes para atribuir utilizadores a MilEyes

* Recomenda-se que um único utilizador AD Azure seja designado para a ThousandEyes para testar a configuração de provisionamento. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador a ThousandEyes, deve selecionar a função **Utilizador** ou outra função específica de aplicação válida (se disponível) no diálogo de atribuição. A função **de Acesso Predefinido** não funciona para o provisionamento, e estes utilizadores são ignorados.

## <a name="configuring-user-provisioning-to-thousandeyes"></a>Configurar o fornecimento de utilizadores ao ThousandEyes 

Esta secção guia-o através da ligação do seu AD Azure à conta de utilizador da ThousandEyes que fornece a API, e configura o serviço de fornecimento para criar, atualizar e desativar as contas de utilizador atribuídas em ThousandEyes com base na atribuição de utilizadores e grupos em Azure AD.

> [!TIP]
> Pode também optar por ativar Sign-On individuais baseadas em SAML para MilEyes, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal pode ser configurado independentemente do fornecimento automático, embora estas duas características se elogiem mutuamente.

### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>Configure o fornecimento automático de conta de utilizador à ThousandEyes em Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Se já configurado MilEyes para um único sign-on, procure o seu exemplo de ThousandEyes usando o campo de pesquisa. Caso contrário, **selecione Add** e procure **mileyes** na galeria de aplicações. Selecione ThousandEyes dos resultados da pesquisa e adicione-o à sua lista de aplicações.

    ![O link MilEyes na lista de Aplicações](common/all-applications.png)
    
3. Selecione a sua instância de ThousandEyes e, em seguida, selecione o **separador Provisioning.**

    ![Separador Aprovisionamento](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

![O screenshot mostra o separador Provisioning for ThousandEyes com o Modo de Provisionamento selecionado para o Modo de Provisionamento.](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)
    

5. Na secção **Credenciais Admin,**  insira o Token do Portador da **OAuth** gerado pela conta da ThousandEyes (pode encontrar e ou gerar um símbolo na sua secção **de Perfil da** conta MilEyes).

    ![A screenshot mostra onde encontrar o link 'Definições de Conta' para o Grupo de Contas Correntes.](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. No portal Azure, clique em **Test Connection** para garantir que o Azure AD pode ligar-se à sua aplicação MilEyes. Se a ligação falhar, certifique-se de que a sua conta MilEyes tem permissões de Administração e tente novamente o passo 5.

7. No campo **E-mail de Notificação**, introduza o endereço de e-mail de uma pessoa ou um grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação **Enviar uma notificação de e-mail quando ocorre uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

8. Clique em **Guardar**.

9. Na secção Mappings, selecione **Synchronize Azure Ative Directory Users to ThousandEyes**.

10. Reveja os atributos do utilizador que são sincronizados de AZure AD a ThousandEyes na secção **De mapeamento de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador em Parsable para operações de atualização. Se optar por alterar o [atributo de alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que a API Parsable suporta utilizadores filtrantes com base nesse atributo. Selecione o botão **Guardar** para escoar quaisquer alterações.

     |Atributo|Tipo|Suportado para filtragem|
     |---|---|---|
     |externalId|String|&check;|
     |userName|String|&check;|
     |active|Booleano|
     |displayName|String|
     |emails[type eq "work"].value|String|
     |nome.formatado|String|


11. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para ativar o serviço de prestação de Ad Azure para ThousandEyes, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

13. Defina os utilizadores e/ou grupos que deseja prestar à ThousandEyes, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para aprovisionar, clique em **Guardar**.

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
