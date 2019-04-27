---
title: 'Tutorial: Configurar área de trabalho pelo Facebook para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e à área de trabalho pelo Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72c2e23b0d60ca242549ebf2c058ea8f44f2b1c8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60520143"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Tutorial: Configurar área de trabalho pelo Facebook para aprovisionamento automático de utilizadores

O objetivo deste tutorial é mostrar a os passos que necessários para executar na área de trabalho pelo Facebook e o Azure AD para aprovisionar e desaprovisionar contas de utilizador do Azure AD à área de trabalho pelo Facebook automaticamente.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com à área de trabalho pelo Facebook, precisa do seguinte:

- Uma subscrição do Azure
- Uma área de trabalho pelo Facebook início de sessão único na subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-workplace-by-facebook"></a>Atribuir utilizadores à área de trabalho pelo Facebook

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento de contas de utilizadores automático, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação no Azure AD é sincronizado.

Antes de configurar e ativar o serviço de aprovisionamento, precisa decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder à sua área de trabalho pela aplicação do Facebook. Depois de decidir, pode atribuir estes utilizadores à sua área de trabalho pela aplicação do Facebook ao seguir as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-workplace-by-facebook"></a>Dicas importantes para atribuir utilizadores à área de trabalho pelo Facebook

*   Recomenda-se que um único utilizador do Azure AD é atribuído à área de trabalho pelo Facebook para testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador à área de trabalho pelo Facebook, tem de selecionar uma função de utilizador válido. A função de "Acesso predefinido" não funciona para o aprovisionamento.

## <a name="enable-user-provisioning"></a>Ativar o aprovisionamento do utilizador

Esta secção orienta-o ao longo da ligação do Azure AD à área de trabalho pela API de aprovisionamento da conta de utilizador do Facebook e configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuído na área de trabalho pelo Facebook com base no utilizador e grupo atribuição no Azure AD.

>[!Tip]
>Também pode optar por ativar baseado em SAML início de sessão único para a área de trabalho pelo Facebook, seguindo as instruções fornecidas [portal do Azure](https://portal.azure.com). Início de sessão único a pode ser configurada independentemente do serviço de aprovisionamento automático, embora esses dois recursos complementar entre si.

### <a name="to-configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Para configurar a conta aprovisionamento de utilizadores à área de trabalho pelo Facebook no Azure AD:

É o objetivo desta secção descrevem como ativar o aprovisionamento de contas de utilizador do Active Directory à área de trabalho pelo Facebook.

O Azure AD suporta a capacidade de sincronizar automaticamente os detalhes da conta de utilizadores atribuídos à área de trabalho pelo Facebook. Esta sincronização automática permite à área de trabalho pelo Facebook para obter os dados necessários para autorizar os utilizadores para o acesso, antes de eles tentar iniciar sessão pela primeira vez. Ele também anular Aprovisiona utilizadores de área de trabalho pelo Facebook quando foi revogado acesso no Azure AD.

1. Na [portal do Azure](https://portal.azure.com), navegue para o **Azure Active Directory** > **aplicações empresariais** > **todas as aplicações** secção.

2. Se já tiver configurado a área de trabalho pelo Facebook para início de sessão único, procure a sua instância do local de trabalho pelo Facebook usando o campo de pesquisa. Caso contrário, selecione **Add** e procure **à área de trabalho pelo Facebook** na Galeria de aplicações. Selecione a área de trabalho pelo Facebook resultados da pesquisa e adicioná-lo à sua lista de aplicações.

3. Selecione a sua instância de área de trabalho pelo Facebook, em seguida, selecione o **aprovisionamento** separador.

4. Definir o **modo de aprovisionamento** ao **automática**. 

    ![a aprovisionar](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

5. Sob o **credenciais de administrador** secção, introduza o Token de acesso da sua área de trabalho pelo administrador do Facebook e defina o valor de URL de inquilino para `https://www.facebook.com/scim/v1/` . Vê-los [instruções](https://developers.facebook.com/docs/workplace/integrations/custom-integrations/apps) sobre como criar um Token de acesso para a área de trabalho. 

6. No portal do Azure, clique em **Testar ligação** para garantir que o Azure AD pode ligar à sua área de trabalho pela aplicação do Facebook. Se a ligação falhar, certifique-se de que sua área de trabalho por conta do Facebook com permissões de administrador de equipe.

7. Introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento no **notificação por E-Mail** campo e marque a caixa de verificação.

8. Clique em **guardar.**

9. Na secção de mapeamentos, selecione **sincronizar utilizadores do Azure Active Directory à área de trabalho pelo Facebook.**

10. Na **mapeamentos de atributos** secção, reveja os atributos de utilizador que são sincronizados a partir do Azure AD à área de trabalho pelo Facebook. Os atributos selecionados como **correspondência** propriedades são usadas pelo Facebook para corresponder as contas de utilizador na área de trabalho para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

11. Para ativar o Azure AD para a área de trabalho pelo Facebook, o serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção

12. Clique em **guardar.**

Para obter mais informações sobre como configurar o aprovisionamento automático, consulte [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

Agora, pode criar uma conta de teste. Aguarde até 20 minutos para verificar se a conta foram sincronizada à área de trabalho pelo Facebook.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar o início de sessão único](workplacebyfacebook-tutorial.md)
