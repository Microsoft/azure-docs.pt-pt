---
title: 'Tutorial: Configure Samanage para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a fornecer e desfornecer automaticamente contas de utilizador de Azure AD para Samanage.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: 182d314b24ce082d996cb692e2a7bb35265abcfe
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628069"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Tutorial: Configure Samanage para fornecimento automático de utilizadores
Este tutorial descreve os passos necessários para executar tanto no Samanage como no Azure Ative Directory (Azure AD) para configurar o fornecimento automático de utilizadores. Quando configurado, a AD Azure disponibiliza e despresta automaticamente utilizadores e grupos para [Samanage](https://www.samanage.com/pricing/) utilizando o serviço de provisionamento de AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../manage-apps/user-provisioning.md)

## <a name="migrate-to-the-new-samange-application"></a>Migrar para a nova aplicação Samange

Se tiver uma integração existente com a Samanage, consulte a secção abaixo sobre as próximas alterações. Se estiver a configurar o Samanage pela primeira vez, pode saltar esta secção e mudar-se para O Suporte de **Capacidades**.

#### <a name="whats-changing"></a>O que está a mudar?
* Alterações do lado da AD Azure: O método de autorização para fornecer utilizadores em Samange tem sido historicamente **auth Basic**. Em breve verá o método de autorização alterado para **long lived secret token**.


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>O que preciso fazer para migrar a minha integração personalizada existente para a nova aplicação?
Se tiver uma integração Samanage existente com credenciais de administração válidas, **não é necessária qualquer ação.** Migramos automaticamente os clientes para a nova aplicação. Este processo é feito completamente nos bastidores. Se as credenciais existentes expirarem, ou se precisar de autorizar o acesso à aplicação novamente, precisa de gerar um símbolo secreto de longa duração. Para gerar um novo símbolo, consulte o Passo 2 deste artigo.


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Como posso saber se o meu pedido foi migrado? 
Quando a sua aplicação for migrada, na secção **credenciais de administrador,** os campos de nome de **utilizador e** **palavra-passe do Administrador** serão substituídos por um único campo Secreto **Token.**

## <a name="capabilities-supported"></a>Capacidades suportadas
> [!div class="checklist"]
> * Criar utilizadores em Samanage
> * Remova os utilizadores em Samanage quando já não necessitam de acesso
> * Mantenha os atributos dos utilizadores sincronizados entre a Azure AD e a Samanage
> * Grupos de provisões e membros do grupo em Samanage
> * [Inscrição única](https://docs.microsoft.com/azure/active-directory/saas-apps/samanage-tutorial) para Samanage (recomendado)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* [Um inquilino da AD Azure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Uma conta de utilizador em Azure AD com [permissão](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) para configurar o provisionamento (por exemplo, Administrador de Aplicação, Administrador de Aplicação na Nuvem, Proprietário de Aplicações ou Administrador Global). 
* Um [inquilino Samanage](https://www.samanage.com/pricing/) com o pacote Profissional.
* Uma conta de utilizador em Samanage com permissões de administração.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passo 1. Planeie a sua implantação de provisionamento
1. Saiba como funciona o serviço de [provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Determinar quem estará no [âmbito do provisionamento.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Determine quais os dados a [mapear entre a AD Azure e a Samanage](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-samanage-to-support-provisioning-with-azure-ad"></a>Passo 2. Configure Samanage para apoiar o provisionamento com a Azure AD

Para gerar um símbolo secreto para autenticação, consulte [isto](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657).

## <a name="step-3-add-samanage-from-the-azure-ad-application-gallery"></a>Passo 3. Adicione Samanage da galeria de aplicações da AD Azure

Adicione Samanage da galeria de aplicações Azure AD para começar a gerir o provisionamento para Samanage. Se já tiver configurado o Samanage para SSO, pode utilizar a mesma aplicação. No entanto, recomenda-se que crie uma aplicação separada ao testar a integração inicialmente. Saiba mais sobre a adição de uma aplicação na galeria [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passo 4. Definir quem estará no âmbito do provisionamento 

O serviço de provisionamento de AD Azure permite-lhe examinar quem será provisionado com base na atribuição à aplicação e ou com base em atributos do utilizador/grupo. Se optar por examinar quem será aprovisionado na sua app com base na atribuição, pode utilizar os [seguintes passos](../manage-apps/assign-user-or-group-access-portal.md) para atribuir utilizadores e grupos à aplicação. Se optar por examinar quem será aprovisionado apenas com base em atributos do utilizador ou do grupo, pode utilizar um filtro de deteção como descrito [aqui](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Ao atribuir utilizadores e grupos ao Samanage, deve selecionar uma função diferente do **Acesso Padrão**. Os utilizadores com a função de Acesso Predefinido estão excluídos do fornecimento e serão marcados como não tendo direito efetivamente nos registos de fornecimento. Se a única função disponível na aplicação for a função de acesso padrão, pode [atualizar o manifesto de aplicação](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) para adicionar funções adicionais. 

* Comece minúsculo. Teste com um pequeno conjunto de utilizadores e grupos antes de passar para todos. Quando o âmbito de fornecimento for definido para utilizadores e grupos atribuídos, pode controlá-lo atribuindo um ou dois utilizadores ou grupos à aplicação. Quando o âmbito é definido para todos os utilizadores e grupos, pode especificar um filtro de [deteção baseado em atributos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-samanage"></a>Passo 5. Configure o fornecimento automático de utilizadores para samanage 

Esta secção orienta-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TestApp com base em atribuições de utilizador e/ou grupo em Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para Samanage em Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Samanage**.

    ![O link Samanage na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção de `https://api.samanage.com` **Credenciais de Administrador,** insere-se no URL do **Arrendatário**.  Insera o valor secreto do símbolo recuperado anteriormente em **Secret Token.** Clique na **ligação de teste** para garantir que o Azure AD pode ligar-se ao Samanage. Se a ligação falhar, certifique-se de que a sua conta Samanage tem permissões de administrador e tente novamente

    ![provisionamento](./media/samanage-provisioning-tutorial/provisioning.png)

6. No campo de email de **notificação,** introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e selecione a **notificação de e-mail enviar uma notificação de e-mail quando ocorrer uma falha** verificar a caixa.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Selecione **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Samanage**.

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Samanage na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Samanage para operações de atualização. Se optar por alterar o [atributo-alvo correspondente,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)terá de garantir que a API samanage suporta filtrar utilizadores com base nesse atributo. Selecione o botão **Guardar** para elegiro qualquer alteração.

      ![Mapeamento de utilizador de Samange](./media/samanage-provisioning-tutorial/user-attributes.png)

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups para Samanage**.

11. Reveja os atributos do grupo que são sincronizados de Azure AD para Samanage na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar os grupos em Samanage para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

      ![Mapeamentodo do Grupo Samange](./media/samanage-provisioning-tutorial/group-attributes.png)

12. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o serviço de provisionamento de AD Azure para Samanage, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

14. Defina os utilizadores e/ou grupos que deseja fornecer à Samanage, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia o ciclo inicial de sincronização de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** O ciclo inicial demora mais tempo a realizar do que os ciclos subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento da AD Azure esteja em funcionamento. 

## <a name="step-6-monitor-your-deployment"></a>Passo 6. Monitorizar a implementação
Depois de configurar o fornecimento, utilize os seguintes recursos para monitorizar a sua implementação:

1. Utilize os registos de [provisionamento](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) para determinar quais os utilizadores que foram provisionados com sucesso ou sem sucesso
2. Verifique a barra de [progresso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) para ver o estado do ciclo de provisionamento e quão perto está da conclusão
3. Se a configuração do fornecimento parecer estar num estado pouco saudável, a aplicação entrará em quarentena. Saiba mais sobre estados de quarentena [aqui.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="connector-limitations"></a>Limitações do conector

Se selecionar a opção **Sync todos os utilizadores e grupos** e configurar um valor para as **funções** Samanage atributo, o valor sob o valor Predefinido se a caixa **nula (é opcional)** deve ser expressa no seguinte formato:

- {"displayName":"role"}, onde a função é o valor padrão que deseja.

## <a name="change-log"></a>Change log

* 04/22/2020 - Método de autorização atualizado do auth básico para o token secreto de longa duração.

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
