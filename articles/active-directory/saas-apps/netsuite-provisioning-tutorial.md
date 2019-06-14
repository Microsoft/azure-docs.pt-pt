---
title: 'Tutorial: Configurar o Netsuite OneWorld para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Netsuite OneWorld.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 8a6d3994-ee33-4a6f-b0a2-9d0389467f16
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 928070ae7e5c9077c6f77e8cb7beb36815f47d6a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515820"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Tutorial: Configurar o Netsuite para aprovisionamento automático de utilizadores

O objetivo deste tutorial é mostrar a os passos que necessários para executar no Netsuite OneWorld e do Azure AD para aprovisionar e desaprovisionar contas de utilizador do Azure AD para o Netsuite automaticamente.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes itens:

*   Um inquilino do Azure Active directory.
*   Uma subscrição de Netsuite OneWorld. Tenha em atenção que o aprovisionamento automático de utilizadores é atualmente suportado apenas com o NetSuite OneWorld.
*   Uma conta de utilizador no Netsuite com permissões de administrador.

## <a name="assigning-users-to-netsuite-oneworld"></a>Atribuir utilizadores a Netsuite OneWorld

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento de contas de utilizadores automático, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o serviço de aprovisionamento, precisa decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder à sua aplicação Netsuite. Depois de decidir, pode atribuir estes utilizadores à sua aplicação Netsuite ao seguir as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-netsuite-oneworld"></a>Dicas importantes para atribuir utilizadores a Netsuite OneWorld

*   Recomenda-se que um único utilizador do Azure AD é atribuído a Netsuite para testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador para o Netsuite, tem de selecionar uma função de utilizador válido. A função de "Acesso predefinido" não funciona para o aprovisionamento.

## <a name="enable-user-provisioning"></a>Ativar o aprovisionamento do utilizador

Esta secção orienta-o ao longo da ligação do Azure AD para a conta de utilizador de Netsuite API de aprovisionamento e configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuído no Netsuite com base na atribuição de utilizadores e grupos no Azure AD.

> [!TIP] 
> Também pode optar por ativada baseado em SAML início de sessão único para o Netsuite, seguindo as instruções fornecidas [portal do Azure](https://portal.azure.com). Início de sessão único a pode ser configurada independentemente do serviço de aprovisionamento automático, embora esses dois recursos complementar entre si.

### <a name="to-configure-user-account-provisioning"></a>Para configurar o aprovisionamento de contas de utilizador:

O objetivo desta seção é descrever como ativar o aprovisionamento de utilizadores de contas de utilizador do Active Directory para o Netsuite.

1. Na [portal do Azure](https://portal.azure.com), navegue para o **Azure Active Directory > aplicações empresariais > todos os aplicativos** secção.

1. Se já tiver configurado o Netsuite para início de sessão único, procure a sua instância do Netsuite usando o campo de pesquisa. Caso contrário, selecione **Add** e procure **Netsuite** na Galeria de aplicações. Selecione o Netsuite resultados da pesquisa e adicioná-lo à sua lista de aplicações.

1. Selecione a sua instância de Netsuite, em seguida, selecione o **aprovisionamento** separador.

1. Definir o **modo de aprovisionamento** ao **automática**. 

    ![Aprovisionamento](./media/netsuite-provisioning-tutorial/provisioning.png)

1. Sob o **credenciais de administrador** secção, forneça as seguintes definições de configuração:
   
    a. Na **nome de utilizador administrador** caixa de texto, tipo um Netsuite o nome que tenha de conta a **administrador de sistema** perfil no Netsuite.com atribuído.
   
    b. Na **palavra-passe de administrador** caixa de texto, escreva a palavra-passe para esta conta.
      
1. No portal do Azure, clique em **Testar ligação** para garantir que o Azure AD pode ligar à sua aplicação de Netsuite.

1. Na **notificação por E-Mail** , insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento e marque a caixa de verificação.

1. Clique em **guardar.**

1. Na secção de mapeamentos, selecione **sincronizar utilizadores do Azure Active Directory para o Netsuite.**

1. Na **mapeamentos de atributos** secção, reveja os atributos de utilizador que são sincronizados a partir do Azure AD para o Netsuite. Tenha em atenção que os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no Netsuite para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

1. Para ativar o Azure AD para o Netsuite do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** na secção de definições

1. Clique em **guardar.**

Ele começa a sincronização inicial de todos os utilizadores e/ou grupos atribuídos a Netsuite na secção utilizadores e grupos. Tenha em atenção que a sincronização inicial exige mais para efetuar sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e seguir links para os registos de atividades, que descrevem a todas as ações executadas pelo serviço de aprovisionamento na sua aplicação Netsuite de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar o início de sessão único](netsuite-tutorial.md)
