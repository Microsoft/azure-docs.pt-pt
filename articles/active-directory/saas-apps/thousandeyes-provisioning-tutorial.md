---
title: 'Tutorial: Configurar ThousandEyes para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador para ThousandEyes automaticamente.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: daveba
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: f008e981abb11a4927ec045c33342bbac9a05bd8
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58436809"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>Tutorial: Configurar ThousandEyes para aprovisionamento automático de utilizadores


O objetivo deste tutorial é mostrar a os passos que necessários para executar no ThousandEyes e do Azure AD para aprovisionar e desaprovisionar contas de utilizador do Azure AD para ThousandEyes automaticamente. 

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes itens:

*   Um inquilino do Azure Active directory
*   Um ativo [ThousandEyes conta](https://www.thousandeyes.com/pricing)
*   Uma conta de utilizador ThousandEyes que tenha sido atribuída uma função que inclui as seguintes permissões de 3:
    * ver todos os utilizadores
    * Editar utilizador
    * Permissões de acesso de API

> [!NOTE]
> O Azure AD aprovisionamento integração depende da [ThousandEyes SCIM API](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK_ThousandEyes-support-for-SCIM). 

## <a name="assigning-users-to-thousandeyes"></a>Atribuir utilizadores a ThousandEyes

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento de contas de utilizadores automático, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação no Azure AD é sincronizado. 

Antes de configurar e ativar o serviço de aprovisionamento, precisa decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder à sua aplicação ThousandEyes. Depois de decidir, pode atribuir estes utilizadores à sua aplicação ThousandEyes ao seguir as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>Dicas importantes para atribuir utilizadores a ThousandEyes

*   Recomenda-se que um único utilizador do Azure AD é atribuído a ThousandEyes para testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador para ThousandEyes, tem de selecionar um a **utilizador** função ou outro válido específico do aplicativo função (se disponível) na caixa de diálogo de atribuição. O **acesso predefinido** função não funciona para o aprovisionamento e estes utilizadores são ignorados.

## <a name="configure-auto-provisioned-user-roles-in-thousandeyes"></a>Configurar funções de utilizador aprovisionado automaticamente no ThousandEyes

Para cada grupo de conta, é de aprovisionamento automático utilizadores para que podem configurar um conjunto de funções a serem aplicadas quando é criada a nova conta de utilizador. Por predefinição, o aprovisionamento automático de utilizadores são atribuídos a _utilizador normal_ função para todas as contas de grupos, a menos que configurado em contrário.

1. Para especificar um novo conjunto de funções para os utilizadores aprovisionados ThousandEyes no registo e navegue para a secção de definições de SCIM **> o ícone de utilizador no canto superior direito > definições da conta > organização > segurança e autenticação.** 

   ![Navegar para as definições de API SCIM](https://monosnap.com/file/kqY8Il7eysGFAiCLCQWFizzM27PiBG)

2. Adicionar uma entrada para cada grupo de conta, atribua um conjunto de funções, em seguida, *guardar* as suas alterações.

   ![Definir funções predefinidas e os grupos de conta para utilizadores criados através da API de SCIM](https://monosnap.com/file/16siam6U8xDQH1RTnaxnmIxvsZuNZG)


## <a name="configuring-user-provisioning-to-thousandeyes"></a>Configurar o aprovisionamento para ThousandEyes 

Esta secção orienta-o ao longo da ligação do Azure AD para a API de aprovisionamento da conta de utilizador do ThousandEyes e configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuído no ThousandEyes com base na atribuição de utilizadores e grupos no Azure AD .

> [!TIP]
> Também pode optar por ativada baseado em SAML único início de sessão (SSO) para ThousandEyes, seguindo a [instruções fornecidas na base de dados de conhecimento do Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-tutorial) para concluir o SSO. SSO pode ser configurado independentemente do serviço de aprovisionamento automático, embora esses dois recursos complementam uma à outra.


### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>Configurar o aprovisionamento automático de utilizadores conta para ThousandEyes no Azure AD


1. Na [portal do Azure](https://portal.azure.com), navegue para o **Azure Active Directory > aplicações empresariais > todos os aplicativos** secção.

2. Se já tiver configurado ThousandEyes para início de sessão único, procure a sua instância do ThousandEyes usando o campo de pesquisa. Caso contrário, selecione **Add** e procure **ThousandEyes** na Galeria de aplicações. Selecione ThousandEyes resultados da pesquisa e adicioná-lo à sua lista de aplicações.

3. Selecione a sua instância de ThousandEyes, em seguida, selecione o **aprovisionamento** separador.

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![ThousandEyes aprovisionamento](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)

5. Sob o **credenciais de administrador** secção, de entrada a **Token de portador do OAuth** gerados por conta dos seus ThousandEyes (pode encontrar e ou gerar um token na sua conta de ThousandEyes  **Perfil** secção).

    ![ThousandEyes aprovisionamento](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. No portal do Azure, clique em **Testar ligação** para garantir que o Azure AD pode ligar à sua aplicação de ThousandEyes. Se a ligação falhar, certifique-se de que a conta de ThousandEyes tem permissões de administrador e tente novamente o passo 5.

7. Introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento no **notificação por E-Mail** campo e marque a caixa de verificação "Enviar uma notificação por e-mail quando ocorre uma falha."

8. Clique em **Guardar**. 

9. Na secção de mapeamentos, selecione **sincronizar utilizadores do Azure Active Directory para ThousandEyes**.

10. Na **mapeamentos de atributos** secção, reveja os atributos de utilizador que são sincronizados a partir do Azure AD para ThousandEyes. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no ThousandEyes para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

11. Para ativar o Azure AD para ThousandEyes do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção

12. Clique em **Guardar**. 

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos atribuídos a ThousandEyes na secção utilizadores e grupos. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e seguir links para os registos de atividades, que descrevem a todas as ações executadas pelo serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)
