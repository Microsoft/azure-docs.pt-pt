---
title: 'Tutorial: Configurar fuze para fornecimento automático de utilizadores com Azure Ative Directory | Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desa provisionar contas de utilizadores à Fuze.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2fcb85b2c16bc2dd418c2260420733dd8663382a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96179262"
---
# <a name="tutorial-configure-fuze-for-automatic-user-provisioning"></a>Tutorial: Configure Fuze para o fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no Fuze e no Azure Ative Directory (Azure AD) para configurar a Azure AD para provisão automática e desa provisionamento de utilizadores e/ou grupos para [fuze.](https://www.fuze.com/) Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).

> [!NOTE]
> Este conector encontra-se atualmente em Visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores em Fuze
> * Remova os utilizadores em Fuze quando já não necessitam de acesso
> * Mantenha os atributos do utilizador sincronizados entre Azure AD e Fuze
> * [Único sinal de](./fuze-tutorial.md) fuze (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino do Azure AD](../develop/quickstart-create-new-tenant.md).
* Uma conta de utilizador no Azure AD com [permissão](../roles/permissions-reference.md) para configurar o aprovisionamento (por ex., Administrador de Aplicações, Administrador de Aplicações de Cloud, Proprietário da Aplicação ou Administrador Global).
* [Um inquilino de Fuze.](https://www.fuze.com/)
* Uma conta de utilizador em Fuze com permissões de Administração.


## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planear a sua implementação de aprovisionamento
1. Saiba [como funciona o serviço de aprovisionamento](../app-provisioning/user-provisioning.md).
2. Determine quem vai estar no [âmbito do aprovisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine quais os dados a [mapear entre Azure AD e Fuze](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-fuze-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure fuze para apoiar o provisionamento com Azure AD

Antes de configurar o Fuze para o fornecimento automático do utilizador com a Azure AD, terá de permitir o fornecimento scim no Fuze. 

1. Comece por contactar o seu representante da Fuze para obter as seguintes informações:

    * Lista de SKUs de produto fuze atualmente em uso na sua empresa.
    * Lista de códigos de localização para as localizações da sua empresa.
    * Lista de códigos de departamento para a sua empresa.

2. Pode encontrar estes SKUs e códigos nos documentos de contração e configuração do Fuze, ou contactando o seu representante fuze.

3. Assim que os requisitos forem recebidos, o seu representante fuze fornecer-lhe-á o sinal de autenticação fuze que é necessário para permitir a integração. Este valor será introduzido no campo Secret Token no separador Provisioning da sua aplicação Fuze no portal Azure.

## <a name="step-3-add-fuze-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione Fuze da galeria de aplicações AZure AD

Adicione Fuze da galeria de aplicações AZure AD para começar a gerir o fornecimento à Fuze. Se já configurar fuze para SSO, pode utilizar a mesma aplicação. No entanto, é recomendável criar uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre como adicionar uma aplicação a partir da galeria [aqui](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4: Determinar quem vai estar no âmbito do aprovisionamento 

O serviço de aprovisionamento do Azure AD permite-lhe determinar quem vai ser aprovisionado com base na atribuição à aplicação e/ou com base em atributos do utilizador/grupo. Se optar por determinar quem vai ser aprovisionado na sua aplicação com base na atribuição, pode utilizar os seguintes [passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se escolher determinar quem vai ser aprovisionado com base apenas em atributos do utilizador ou grupo, pode utilizar um filtro de âmbito conforme descrito [aqui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Ao atribuir os utilizadores ao Fuze, tem de selecionar outra função que não **o Acesso Predefinido**. Os utilizadores com a função Acesso Predefinido são excluídos do aprovisionamento e marcados como não autorizados de forma efetiva nos registos de aprovisionamento. Se a única função disponível na aplicação for a função de acesso predefinido, pode [atualizar o manifesto de aplicação](../develop/howto-add-app-roles-in-azure-ad-apps.md) para adicionar funções adicionais. 

* Comece pequeno. Teste com um pequeno conjunto de utilizadores antes de rolar para todos. Quando o âmbito de provisão é definido para utilizadores designados, pode controlá-lo atribuindo um ou dois utilizadores à aplicação. Quando o âmbito é definido para todos os utilizadores, pode especificar um [filtro de deteção baseado em atributos](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configuring-automatic-user-provisioning-to-fuze"></a>Passo 5. Configurar o fornecimento automático do utilizador à Fuze 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Fuze com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para fuze em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais** e, em seguida, **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Fuze**.

    ![O link Fuze na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

5. Sob a secção **credenciais de administração,** insira o **url base SCIM 2.0 e** o valor de token de autenticação SCIM recuperado anteriormente do representante do Fuze em TENANT **URL** e **Secret Token**. Clique em **Testar a Ligação** para garantir que o Azure AD pode ligar-se ao Fuze. Se a ligação falhar, certifique-se de que a sua conta Fuze tem permissões de Administração e tente novamente.

    ![Token URL do inquilino](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **'Email' de Notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e verifique a caixa de verificação - **Envie uma notificação de e-mail quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Fuze**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD a Fuze na secção **De Mapeamento** de Atributos. Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador em Fuze para operações de atualização. Selecione o botão **Guardar** para escoar quaisquer alterações.

   |Atributo|Tipo|
   |---|---|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |emails[type eq "work"].value|String|
   |active|Booleano|

10. Para configurar filtros de âmbito, veja as instruções seguintes disponibilizadas no [Tutorial de filtro de âmbito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de prestação de Ad Azure para fuze, altere o **Estado de Provisionamento** para **On** na secção **Definições.**

    ![Estado do Aprovisionamento Ativado](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja providenciar à Fuze, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **Guardar**.

    ![Guardar Configuração de Aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução.

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o aprovisionamento, utilize os seguintes recursos para monitorizar a sua implementação:

1. Utilize os [registos de aprovisionamento](../reports-monitoring/concept-provisioning-logs.md) para determinar quais os utilizadores que foram aprovisionados com ou sem êxito
2. Verifique a [barra de progresso](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) para ver o estado do ciclo de aprovisionamento e quão próximo está da conclusão
3. Se a configuração de aprovisionamento parecer estar num mau estado de funcionamento, a aplicação vai entrar em quarentena. Saiba mais sobre os estados de quarentena [aqui](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="connector-limitations"></a>Limitações do conector

* Fuze suporta atributos SCIM personalizados **chamados Direitos.** Estes atributos só podem ser criados e não atualizados. 

## <a name="change-log"></a>Change log

* 06/15/2020 - Limite de taxa de integração ajustado a 10 pedidos/segundo.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do fornecimento de conta de utilizador para apps empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).