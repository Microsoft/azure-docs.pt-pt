---
title: 'Tutorial: Configurar o ServiceNow para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como aprovisionar e desaprovisionar contas de utilizador do Azure AD ao ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19b3e4cc5ba4bc0173721947bd1e1a680ca7b3a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60869843"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning-with-azure-active-directory"></a>Tutorial: Configurar o ServiceNow para aprovisionamento automático de utilizadores no Azure Active Directory

O objetivo deste tutorial é mostrar a os passos que necessários para executar em ServiceNow e o Azure AD para aprovisionar e desaprovisionar contas de utilizador do Azure AD ao ServiceNow.

> [!NOTE]
> Este tutorial descreve um conector assentes no serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ServiceNow, terá dos seguintes itens:

- Uma subscrição do Azure
- Para o ServiceNow, uma instância ou um inquilino do ServiceNow, versão Calgary ou superior
- Para o ServiceNow Express, uma instância do ServiceNow Express, versão Helsínquia ou superior

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).


## <a name="assigning-users-to-servicenow"></a>Atribuir utilizadores a ServiceNow

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento de contas de utilizadores automático, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação no Azure AD é sincronizado.

Antes de configurar e ativar o serviço de aprovisionamento, precisa decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder à sua aplicação ServiceNow. Depois de decidir, pode atribuir estes utilizadores à sua aplicação ServiceNow ao seguir as instruções aqui: [Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)


> [!IMPORTANT]
>*   Recomenda-se que um único utilizador do Azure AD é atribuído ao ServiceNow para testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.
>*   Ao atribuir um utilizador ao ServiceNow, tem de selecionar uma função de utilizador válido. A função de "Acesso predefinido" não funciona para o aprovisionamento.

## <a name="enable-automated-user-provisioning"></a>Ativar o aprovisionamento automatizado do utilizador

Esta secção orienta-o ao longo da ligação do Azure AD para a API de aprovisionamento da conta de utilizador do ServiceNow e configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuído do ServiceNow com base na atribuição de utilizadores e grupos no Azure AD.

> [!TIP]
>Também pode optar por ativada baseado em SAML início de sessão único para o ServiceNow, seguindo as instruções fornecidas [portal do Azure](https://portal.azure.com). Início de sessão único a pode ser configurada independentemente do serviço de aprovisionamento automático, embora esses dois recursos complementar entre si.

### <a name="configure-automatic-user-account-provisioning"></a>Configurar o aprovisionamento de contas de utilizadores automático

1. Na [portal do Azure](https://portal.azure.com), navegue para o **Azure Active Directory > aplicações empresariais > todos os aplicativos** secção.

1. Se já tiver configurado o ServiceNow para início de sessão único, procure a sua instância do ServiceNow com o campo de pesquisa. Caso contrário, selecione **Add** e procure **ServiceNow** na Galeria de aplicações. Selecione o ServiceNow resultados da pesquisa e adicioná-lo à sua lista de aplicações.

1. Selecione a sua instância do ServiceNow, em seguida, selecione o **aprovisionamento** separador.

1. Definir o **aprovisionamento** modo **automática**. 

    ![a aprovisionar](./media/servicenow-provisioning-tutorial/provisioning.png)

1. Na secção de credenciais de administrador, execute os seguintes passos:
   
    a. Na **nome da instância do ServiceNow** caixa de texto, escreva o nome de instância do ServiceNow.

    b. Na **nome de utilizador de administrador do ServiceNow** caixa de texto, escreva o nome de utilizador do administrador.

    c. Na **palavra-passe de administrador do ServiceNow** caixa de texto, a senha do administrador.

1. No portal do Azure, clique em **Testar ligação** para garantir que o Azure AD pode ligar à sua aplicação ServiceNow. Se a ligação falhar, certifique-se de que sua conta ServiceNow com permissões de administrador de equipe e tente a **"Credenciais de administrador"** passo novamente.

1. Introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento no **notificação por E-Mail** campo e marque a caixa de verificação.

1. Clique em **guardar.**

1. Na secção de mapeamentos, selecione **sincronizar utilizadores do Azure Active Directory ao ServiceNow.**

1. Na **mapeamentos de atributos** secção, reveja os atributos de utilizador que são sincronizados a partir do Azure AD ao ServiceNow. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador do ServiceNow para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

1. Para ativar o Azure AD para o ServiceNow do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** na secção de definições

1. Clique em **guardar.**

Ele começa a sincronização inicial de todos os utilizadores e/ou grupos atribuídos ao ServiceNow na secção utilizadores e grupos. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e seguir links para os registos de atividades, que descrevem a todas as ações executadas pelo serviço de aprovisionamento na sua aplicação ServiceNow de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar o início de sessão único](servicenow-tutorial.md)


