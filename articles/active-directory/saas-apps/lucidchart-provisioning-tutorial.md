---
title: 'Tutorial: Configurar LucidChart para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador para LucidChart automaticamente.
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
ms.date: 03/27/2019
ms.author: asmalser-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc181625ead251480bb107fc59e3aae46afab1ee
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59257693"
---
# <a name="tutorial-configure-lucidchart-for-automatic-user-provisioning"></a>Tutorial: Configurar LucidChart para aprovisionamento automático de utilizadores

O objetivo deste tutorial é mostrar a os passos que necessários para executar no LucidChart e do Azure AD para aprovisionar e desaprovisionar contas de utilizador do Azure AD para LucidChart automaticamente. 

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes itens:

* Um inquilino do Azure Active directory
* Um inquilino LucidChart com o [plano Enterprise](https://www.lucidchart.com/user/117598685#/subscriptionLevel) ou melhor ativado
* Uma conta de utilizador no LucidChart com permissões de administrador

## <a name="assigning-users-to-lucidchart"></a>Atribuir utilizadores a LucidChart

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento de contas de utilizadores automático, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação no Azure AD é sincronizado.

Antes de configurar e ativar o serviço de aprovisionamento, precisa decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder à sua aplicação LucidChart. Depois de decidir, pode atribuir estes utilizadores à sua aplicação LucidChart ao seguir as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-lucidchart"></a>Dicas importantes para atribuir utilizadores a LucidChart

* Recomenda-se que um único utilizador do Azure AD é atribuído a LucidChart para testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um utilizador para LucidChart, tem de selecionar um a **utilizador** função, ou outro válido específico do aplicativo função (se disponível) na caixa de diálogo atribuição. O **acesso predefinido** função não funciona para o aprovisionamento e estes utilizadores são ignorados.

## <a name="configuring-user-provisioning-to-lucidchart"></a>Configurar o aprovisionamento para LucidChart

Esta secção orienta-o ao longo da ligação do Azure AD para a API de aprovisionamento da conta de utilizador do LucidChart e configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuído no LucidChart com base na atribuição de utilizadores e grupos no Azure AD.

> [!TIP]
> Também pode optar por ativada baseado em SAML início de sessão único para LucidChart, seguindo as instruções fornecidas [portal do Azure](https://portal.azure.com). Início de sessão único a pode ser configurada independentemente do serviço de aprovisionamento automático, embora esses dois recursos complementar entre si.

### <a name="configure-automatic-user-account-provisioning-to-lucidchart-in-azure-ad"></a>Configurar o aprovisionamento automático de utilizadores conta para LucidChart no Azure AD

1. Na [portal do Azure](https://portal.azure.com), navegue para o **Azure Active Directory > aplicações empresariais > todos os aplicativos** secção.

2. Se já tiver configurado LucidChart para início de sessão único, procure a sua instância do LucidChart usando o campo de pesquisa. Caso contrário, selecione **Add** e procure **LucidChart** na Galeria de aplicações. Selecione LucidChart resultados da pesquisa e adicioná-lo à sua lista de aplicações.

3. Selecione a sua instância de LucidChart, em seguida, selecione o **aprovisionamento** separador.

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Aprovisionamento LucidChart](./media/lucidchart-provisioning-tutorial/LucidChart1.png)

5. Sob o **credenciais de administrador** secção, de entrada a **segredo de Token** gerados por conta da sua LucidChart (pode encontrar o token na sua conta: **Da Equipe** > **integração de aplicações** > **SCIM**).

    ![Aprovisionamento LucidChart](./media/lucidchart-provisioning-tutorial/LucidChart2.png)

6. No portal do Azure, clique em **Testar ligação** para garantir que o Azure AD pode ligar à sua aplicação de LucidChart. Se a ligação falhar, certifique-se de que a conta de LucidChart tem permissões de administrador e tente novamente o passo 5.

7. Introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento no **notificação por E-Mail** campo e marque a caixa de verificação "Enviar uma notificação por e-mail quando ocorre uma falha."

8. Clique em **Guardar**.

9. Na secção de mapeamentos, selecione **sincronizar utilizadores do Azure Active Directory para LucidChart**.

10. Na **mapeamentos de atributos** secção, reveja os atributos de utilizador que são sincronizados a partir do Azure AD para LucidChart. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no LucidChart para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

11. Para ativar o Azure AD para LucidChart do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção

12. Clique em **Guardar**.

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos atribuídos a LucidChart na secção utilizadores e grupos. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e seguir links para os registos de atividades, que descrevem a todas as ações executadas pelo serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)
