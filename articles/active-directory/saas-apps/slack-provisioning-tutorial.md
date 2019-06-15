---
title: 'Tutorial: Configurar o Slack para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para automaticamente aprovisionar e desaprovisionar contas de utilizador para o Slack.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 036112027fcf210f0ac2ff1e631c8b0bd4b5e9ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65964388"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Tutorial: Configurar o Slack para aprovisionamento automático de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos necessários para executar no Slack e do Azure AD para automaticamente as contas de utilizador aprovisionar e desaprovisionar do Azure AD para o Slack.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes itens:

* Um inquilino do Azure Active Directory
* Um Slack inquilino com o [além de plano](https://aadsyncfabric.slack.com/pricing) ou melhor ativado
* Uma conta de utilizador no Slack com permissões de administrador da equipa

Nota: Conta com o Azure AD, integração de aprovisionamento a [Slack SCIM API](https://api.slack.com/scim), que está disponível para as equipes de Slack no mais planear ou melhor.

## <a name="assigning-users-to-slack"></a>Atribuir utilizadores a Slack

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento de contas de utilizadores automático, serão sincronizados apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação no Azure AD.

Antes de configurar e ativar o serviço de aprovisionamento, terá de decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder à sua aplicação Slack. Depois de decidir, pode atribuir estes utilizadores à sua aplicação Slack ao seguir as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Dicas importantes para atribuir utilizadores a Slack

* Recomenda-se que um único utilizador do Azure AD é atribuído ao Slack para testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um utilizador para o Slack, tem de selecionar o **utilizador** ou função de "Grupo" na caixa de diálogo atribuição. A função de "Acesso predefinido" não funciona para o aprovisionamento.

## <a name="configuring-user-provisioning-to-slack"></a>Configurar o aprovisionamento para Slack 

Esta secção orienta-o ao longo da ligação do Azure AD para a API de aprovisionamento da conta de utilizador do Slack e configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuído no Slack com base na atribuição de utilizadores e grupos no Azure AD.

**Sugestão:** Também pode optar por ativada baseado em SAML início de sessão único para o Slack, seguindo as instruções fornecidas [portal do Azure](https://portal.azure.com). Início de sessão único a pode ser configurada independentemente do serviço de aprovisionamento automático, embora esses dois recursos complementar entre si.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Para configurar o aprovisionamento automático de utilizadores conta para o Slack no Azure AD:

1. Na [portal do Azure](https://portal.azure.com), navegue para o **Azure Active Directory > aplicações empresariais > todos os aplicativos** secção.

2. Se já tiver configurado o Slack para início de sessão único, procure a sua instância do Slack usando o campo de pesquisa. Caso contrário, selecione **Add** e procure **Slack** na Galeria de aplicações. Selecione o Slack resultados da pesquisa e adicioná-lo à sua lista de aplicações.

3. Selecione a sua instância do Slack, em seguida, selecione o **aprovisionamento** separador.

4. Definir o **modo de aprovisionamento** ao **automática**.

   ![Slack aprovisionamento](./media/slack-provisioning-tutorial/Slack1.PNG)

5. Sob o **credenciais de administrador** secção, clique em **autorizar**. Esta ação abre uma caixa de diálogo de autorização Slack numa nova janela do browser.

6. Na nova janela, inicie sessão no Slack com a sua conta de administrador da equipa. na caixa de diálogo resultante autorização, selecione que pretende ativar o aprovisionamento de equipe do Slack e, em seguida, selecione **autorizar**. Depois de concluído, regresse ao portal do Azure para concluir a configuração de aprovisionamento.

    ![Caixa de diálogo de autorização](./media/slack-provisioning-tutorial/Slack3.PNG)

7. No portal do Azure, clique em **Testar ligação** para garantir que o Azure AD pode ligar à sua aplicação Slack. Se a ligação falhar, certifique-se de que sua conta do Slack com permissões de administrador de equipe e tente novamente o passo de "Autorizar".

8. Introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento no **notificação por E-Mail** campo e marque a caixa de verificação abaixo.

9. Clique em **Guardar**.

10. Na secção de mapeamentos, selecione **sincronizar utilizadores do Azure Active Directory para o Slack**.

11. Na **mapeamentos de atributos** secção, reveja os atributos de utilizador que serão sincronizados do Azure AD para o Slack. Tenha em atenção que os atributos selecionados como **correspondência** propriedades serão utilizadas para corresponder as contas de utilizador no Slack para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

12. Para ativar o Azure AD para o Slack do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção

13. Clique em **Guardar**.

Isso iniciará a sincronização inicial de todos os utilizadores e/ou grupos atribuídos a Slack na secção utilizadores e grupos. Tenha em atenção que a sincronização inicial demora mais tempo a executar que sincroniza subsequentes, o que ocorrer aproximadamente a cada 10 minutos, desde que o serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e seguir links para aprovisionamento de relatórios de atividade, que descrevem a todas as ações executadas pelo serviço de aprovisionamento na sua aplicação Slack.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>[Opcional] Configurar o aprovisionamento do objeto de grupo para Slack

Opcionalmente, pode ativar o aprovisionamento de objetos de grupo do Azure AD para o Slack. Isto é diferente de "atribuição de grupos de utilizadores", em que o grupo real de objeto, além de seus membros serão replicados do Azure AD para o Slack. Por exemplo, se tiver um grupo com o nome "My Group" no Azure AD, será criado um grupo de idêntico com o nome "My Group" dentro do Slack.

### <a name="to-enable-provisioning-of-group-objects"></a>Para ativar o aprovisionamento de objetos de grupo:

1. Na secção de mapeamentos, selecione **sincronizar grupos do Azure Active Directory para o Slack**.

2. No painel de mapeamento do atributo, defina Enabled como Yes.

3. Na **mapeamentos de atributos** secção, reveja os atributos de grupo que serão sincronizados do Azure AD para o Slack. Tenha em atenção que os atributos selecionados como **correspondência** propriedades serão utilizadas para fazer corresponder os grupos no Slack para operações de atualização. 

4. Clique em **Guardar**.

Este resultado em qualquer objeto de grupo atribuído a Slack no **utilizadores e grupos** secção totalmente a ser sincronizados do Azure AD para o Slack. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e seguir links para aprovisionamento de registos de atividade, que descrevem a todas as ações executadas pelo serviço de aprovisionamento na sua aplicação Slack.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações de conector

* Durante a configuração do Slack **displayName** atributo, lembre-se dos seguintes comportamentos:

  * Os valores não são totalmente exclusivos (por exemplo, os 2 utilizadores podem ter o mesmo nome a apresentar)

  * Suporta carateres não ingleses, espaços, uso. 
  
  * Permitido pontuação inclui pontos finais, carateres de sublinhado, hífenes, apóstrofos, parênteses Retos (por exemplo, **([{}])** ) e os separadores (por exemplo, **, /;** ).
  
  * Apenas atualiza se estas duas definições estão configuradas na área de trabalho/organização do Slack - **perfil a sincronização está ativada** e **os utilizadores não é possível alterar o nome a apresentar**.
  
  * Do Slack **nome de utilizador** atributo tem de ser em 21 carateres e ter um valor exclusivo.

## <a name="additional-resources"></a>Recursos Adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
