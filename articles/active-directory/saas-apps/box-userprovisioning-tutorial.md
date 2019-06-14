---
title: 'Tutorial: Configurar a caixa para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e a caixa.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 1c959595-6e57-4954-9c0d-67ba03ee212b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd7826455624ca4a84d668455f522cbde411ac8b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60431761"
---
# <a name="tutorial-configure-box-for-automatic-user-provisioning"></a>Tutorial: Configurar a caixa para aprovisionamento automático de utilizadores

O objetivo deste tutorial é mostrar as etapas que tiver de realizar na caixa e o Azure AD para automaticamente as contas de utilizador aprovisionar e desaprovisionar do Azure AD à caixa.

> [!NOTE]
> Este tutorial descreve um conector assentes no serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com caixa, terá dos seguintes itens:

- Um inquilino do Azure AD
- Um plano de negócios de caixa ou superior

> [!NOTE]
> Quando testa os passos neste tutorial, é recomendável que faça *não* utilizar um ambiente de produção.

Para testar os passos neste tutorial, siga as seguintes recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-box"></a>Atribuir utilizadores a caixa 

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento de contas de utilizadores automático, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação no Azure AD é sincronizado.

Antes de configurar e ativar o serviço de aprovisionamento, precisa decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder à sua aplicação de caixa. Depois de decidir, pode atribuir estes utilizadores à sua aplicação de caixa ao seguir as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

## <a name="assign-users-and-groups"></a>Atribuir utilizadores e grupos
O **caixa > utilizadores e grupos** separador no portal do Azure permite-lhe especificar os utilizadores e grupos devem ter acesso à caixa. A atribuição de um utilizador ou grupo faz com que os seguintes procedimentos ocorrer:

* Do Azure AD permite que o utilizador atribuído (seja por atribuição direta ou associação de grupo) para autenticar a caixa. Se não for atribuído um utilizador, o Azure AD não permite que iniciem sessão a caixa e devolve um erro na página de início de sessão do Azure AD.
* Um mosaico da aplicação para a Box é adicionado para o usuário [iniciador de aplicações](../manage-apps/end-user-experiences.md).
* Se o aprovisionamento automático está ativado, em seguida, os utilizadores atribuídos e/ou grupos são adicionados à fila de aprovisionamento a ser aprovisionado automaticamente.
  
  * Se apenas os objetos de utilizador foram configurados para ser aprovisionado, em seguida, todos os utilizadores atribuídos diretamente são colocados na fila de aprovisionamento e todos os utilizadores que são membros de nenhum grupo atribuído são colocados na fila de aprovisionamento. 
  * Se os objetos de grupo foram configurados para ser aprovisionado, são aprovisionados todos os objetos de grupo atribuído à caixa e todos os utilizadores que são membros desses grupos. As associações de grupo e utilizador são mantidas após a ser escritos à caixa.

Pode utilizar o **atributos > início de sessão único** separador para configurar quais atributos de utilizador (ou afirmações) são apresentadas à caixa durante a autenticação baseada no SAML e o **atributos > aprovisionamento** separador para Configure a forma como os atributos de utilizador e grupo fluir do Azure AD à caixa durante o aprovisionamento de operações.

### <a name="important-tips-for-assigning-users-to-box"></a>Dicas importantes para atribuir utilizadores a caixa 

*   Recomenda-se que um único atribuído a caixa para testar a configuração de aprovisionamento de utilizador do Azure AD. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador à caixa, tem de selecionar uma função de utilizador válido. A função de "Acesso predefinido" não funciona para o aprovisionamento.

## <a name="enable-automated-user-provisioning"></a>Ativar o aprovisionamento automatizado do utilizador

Esta secção orienta no processo de ligar o Azure AD à API de aprovisionamento da conta de utilizador da caixa e configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuído na caixa de com base na atribuição de utilizadores e grupos no Azure AD.

Se o aprovisionamento automático está ativado, em seguida, os utilizadores atribuídos e/ou grupos são adicionados à fila de aprovisionamento a ser aprovisionado automaticamente.
    
 * Se apenas os objetos de utilizador estão configurados para ser aprovisionado, em seguida, os utilizadores atribuídos diretamente são colocados na fila de aprovisionamento e todos os utilizadores que são membros de nenhum grupo atribuído são colocados na fila de aprovisionamento. 
    
 * Se os objetos de grupo foram configurados para ser aprovisionado, são aprovisionados todos os objetos de grupo atribuído à caixa e todos os utilizadores que são membros desses grupos. As associações de grupo e utilizador são mantidas após a ser escritos à caixa.

> [!TIP] 
> Também pode optar por ativada baseado em SAML início de sessão único para a caixa, seguindo as instruções fornecidas [portal do Azure](https://portal.azure.com). Início de sessão único a pode ser configurada independentemente do serviço de aprovisionamento automático, embora esses dois recursos complementar entre si.

### <a name="to-configure-automatic-user-account-provisioning"></a>Para configurar o aprovisionamento de contas de utilizador automáticas:

É o objetivo desta secção descrevem como ativar o aprovisionamento de contas de utilizador do Active Directory à caixa.

1. Na [portal do Azure](https://portal.azure.com), navegue para o **Azure Active Directory > aplicações empresariais > todos os aplicativos** secção.

2. Se já tiver configurado a caixa para início de sessão único, procure a sua instância do Box através do campo de pesquisa. Caso contrário, selecione **Add** e procure **caixa** na Galeria de aplicações. Selecione a caixa de resultados da pesquisa e adicioná-lo à sua lista de aplicações.

3. Selecione a sua instância da caixa, em seguida, selecione o **aprovisionamento** separador.

4. Definir o **modo de aprovisionamento** ao **automática**. 

    ![Aprovisionamento](./media/box-userprovisioning-tutorial/provisioning.png)

5. Sob o **credenciais de administrador** secção, clique em **autorizar** para abrir uma caixa de diálogo de início de sessão de caixa numa nova janela do browser.

6. Sobre o **início de sessão para conceder acesso à caixa** página, forneça as credenciais necessárias e, em seguida, clique em **autorizar**. 
   
    ![Ativar aprovisionamento automático de utilizadores](./media/box-userprovisioning-tutorial/IC769546.png "ativar aprovisionamento automático de utilizadores")

7. Clique em **conceder acesso à caixa** para autorizar esta operação e voltar ao portal do Azure. 
   
    ![Ativar aprovisionamento automático de utilizadores](./media/box-userprovisioning-tutorial/IC769549.png "ativar aprovisionamento automático de utilizadores")

8. No portal do Azure, clique em **Testar ligação** para garantir que o Azure AD pode ligar à sua aplicação de caixa. Se a ligação falhar, certifique-se a conta do Box tem permissões de administrador de equipe e tente a **"Autorizar"** passo novamente.

9. Introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento no **notificação por E-Mail** campo e marque a caixa de verificação.

10. Clique em **guardar.**

11. Na secção de mapeamentos, selecione **sincronizar utilizadores do Azure Active Directory à caixa.**

12. Na **mapeamentos de atributos** secção, reveja os atributos de utilizador que são sincronizados a partir do Azure AD à caixa. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador na caixa para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

13. Para ativar o Azure AD para a caixa do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** na secção de definições

14. Clique em **guardar.**

Que inicia a sincronização inicial de todos os utilizadores e/ou grupos atribuídos à caixa na secção utilizadores e grupos. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e seguir links para os registos de atividades, que descrevem a todas as ações executadas pelo serviço de aprovisionamento em seu aplicativo de caixa de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

No seu inquilino de caixa, os utilizadores sincronizados estão listados na **utilizadores geridos** no **consola de administração**.

![Estado de integração](./media/box-userprovisioning-tutorial/IC769556.png "estado de integração")


## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar o início de sessão único](box-tutorial.md)
