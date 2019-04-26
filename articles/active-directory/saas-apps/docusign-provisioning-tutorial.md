---
title: 'Tutorial: Configurar o DocuSign para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 294cd6b8-74d7-44bc-92bc-020ccd13ff12
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 121d147a3f8c91f17e955120b2c14f7dbd3da592
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60280089"
---
# <a name="tutorial-configure-docusign-for-automatic-user-provisioning"></a>Tutorial: Configurar o DocuSign para aprovisionamento automático de utilizadores

O objetivo deste tutorial é mostrar a os passos que necessários para executar no DocuSign e do Azure AD para aprovisionar e desaprovisionar contas de utilizador do Azure AD a DocuSign automaticamente.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes itens:

*   Um inquilino do Azure Active directory.
*   Um DocuSign início de sessão único ativado na subscrição.
*   Uma conta de utilizador no DocuSign com permissões de administrador da equipa.

## <a name="assigning-users-to-docusign"></a>Atribuir utilizadores a DocuSign

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento de contas de utilizadores automático, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o serviço de aprovisionamento, precisa decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder à sua aplicação do DocuSign. Depois de decidir, pode atribuir estes utilizadores à sua aplicação DocuSign ao seguir as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-docusign"></a>Dicas importantes para atribuir utilizadores a DocuSign

*   Recomenda-se que um único utilizador do Azure AD é atribuído a DocuSign para testar a configuração de aprovisionamento. Os utilizadores adicionais podem ser atribuídos mais tarde.

*   Quando atribuir um utilizador a DocuSign, tem de selecionar uma função de utilizador válido. A função de "Acesso predefinido" não funciona para o aprovisionamento.

> [!NOTE]
> Azure AD não suporta o aprovisionamento do grupo com o aplicativo Docusign, apenas os utilizadores podem ser aprovisionados.

## <a name="enable-user-provisioning"></a>Ativar o aprovisionamento do utilizador

Esta secção orienta-o ao longo da ligação do Azure AD para a API de aprovisionamento da conta de utilizador do DocuSign e configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuído no DocuSign com base na atribuição de utilizadores e grupos no Azure AD.

> [!Tip]
> Também pode optar por ativada baseado em SAML início de sessão único para DocuSign, seguindo as instruções fornecidas [portal do Azure](https://portal.azure.com). Início de sessão único a pode ser configurada independentemente do serviço de aprovisionamento automático, embora esses dois recursos complementar entre si.

### <a name="to-configure-user-account-provisioning"></a>Para configurar o aprovisionamento de contas de utilizador:

O objetivo desta seção é descrever como ativar o aprovisionamento de utilizadores de contas de utilizador do Active Directory para DocuSign.

1. Na [portal do Azure](https://portal.azure.com), navegue para o **Azure Active Directory > aplicações empresariais > todos os aplicativos** secção.

1. Se já tiver configurado o DocuSign para início de sessão único, procure a sua instância do DocuSign usando o campo de pesquisa. Caso contrário, selecione **Add** e procure **DocuSign** na Galeria de aplicações. Selecione o DocuSign resultados da pesquisa e adicioná-lo à sua lista de aplicações.

1. Selecione a sua instância do DocuSign, em seguida, selecione o **aprovisionamento** separador.

1. Definir o **modo de aprovisionamento** ao **automática**. 

    ![a aprovisionar](./media/docusign-provisioning-tutorial/provisioning.png)

1. Sob o **credenciais de administrador** secção, forneça as seguintes definições de configuração:
   
    a. Na **nome de utilizador administrador** caixa de texto, tipo um DocuSign o nome que tenha de conta a **administrador de sistema** perfil no DocuSign.com atribuído.
   
    b. Na **palavra-passe de administrador** caixa de texto, escreva a palavra-passe para esta conta.

1. No portal do Azure, clique em **Testar ligação** para garantir que o Azure AD pode ligar à sua aplicação do DocuSign.

1. Na **notificação por E-Mail** , insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento e marque a caixa de verificação.

1. Clique em **guardar.**

1. Na secção de mapeamentos, selecione **sincronizar utilizadores do Azure Active Directory para DocuSign.**

1. Na **mapeamentos de atributos** secção, reveja os atributos de utilizador que são sincronizados a partir do Azure AD para DocuSign. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no DocuSign para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

1. Para ativar o Azure AD para DocuSign do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** na secção de definições

1. Clique em **guardar.**

Ele começa a sincronização inicial de quaisquer utilizadores atribuídos a DocuSign na secção utilizadores e grupos. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e seguir links para aprovisionamento de registos de atividade, que descrevem a todas as ações executadas pelo serviço de aprovisionamento na sua aplicação do DocuSign.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar o início de sessão único](docusign-tutorial.md)
