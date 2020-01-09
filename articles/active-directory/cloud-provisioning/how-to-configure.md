---
title: Configuração de novo agente de provisionamento de Azure AD Connect nuvem
description: Este artigo descreve como instalar o provisionamento de nuvem.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 853950cf441007eac0170f32f28f2d9c16a71292
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75549436"
---
# <a name="azure-ad-connect-cloud-provisioning-new-agent-configuration"></a>Configuração de novo agente de provisionamento de Azure AD Connect nuvem

Depois de instalar o agente, você precisa entrar no portal do Azure e configurar o provisionamento de nuvem do Azure Active Directory (Azure AD) Connect. Siga estas etapas para habilitar o agente.

## <a name="configure-provisioning"></a>Configurar provisionamento
Para configurar o provisionamento, siga estas etapas.

1.  No portal do Azure, selecione **Azure Active Directory**.
1.  Selecione **Azure ad Connect**.
1.  Selecione **gerenciar provisionamento (versão prévia)** .

    ![Gerenciar provisionamento (versão prévia)](media/how-to-configure/manage1.png)

1.  Selecione **nova configuração**.
1.  Na tela de configuração, o domínio local é preenchido previamente.
1.  Insira um **email de notificação**. Este email será notificado quando o provisionamento não estiver íntegro.
1.  Mova o seletor para **habilitar**e selecione **salvar**.

    ![Provisionamento do Azure AD (versão prévia)](media/tutorial-single-forest/configure2.png)

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Provisionamento de escopo para usuários e grupos específicos
Você pode fazer o escopo do agente para sincronizar usuários e grupos específicos usando grupos de Active Directory locais ou unidades organizacionais. Você não pode configurar grupos e unidades organizacionais em uma configuração. 

1.  No portal do Azure, selecione **Azure Active Directory**.
1.  Selecione **Azure ad Connect**.
1.  Selecione **gerenciar provisionamento (versão prévia)** .
1.  Em **configuração**, selecione sua configuração.

    ![Seção de configuração](media/how-to-configure/scope1.png)

1.  Em **Configurar**, selecione **todos os usuários** para alterar o escopo da regra de configuração.

    ![Opção todos os usuários](media/how-to-configure/scope2.png)

1. À direita, você pode alterar o escopo para incluir apenas grupos de segurança. Insira o nome distinto do grupo e selecione **Adicionar**.

    ![Opção de grupos de segurança selecionados](media/how-to-configure/scope3.png)

1.  Ou você pode alterar o escopo para incluir apenas unidades organizacionais específicas. Selecione **concluído** e **salvar**.

    ![Opção de unidades organizacionais selecionadas](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>Reiniciar o provisionamento 
Se você não quiser aguardar a próxima execução agendada, acione o provisionamento executado usando o botão **reiniciar provisionamento** . 
1.  No portal do Azure, selecione **Azure Active Directory**.
1.  Selecione **Azure ad Connect**.
1.  Selecione **gerenciar provisionamento (versão prévia)** .
1.  Em **configuração**, selecione sua configuração.

    ![Seleção de configuração para reiniciar o provisionamento](media/how-to-configure/scope1.png)

1.  Na parte superior, selecione **reiniciar provisionamento**.

## <a name="remove-a-configuration"></a>Remover uma configuração
Para excluir uma configuração, siga estas etapas.

1.  No portal do Azure, selecione **Azure Active Directory**.
1.  Selecione **Azure ad Connect**.
1.  Selecione **gerenciar provisionamento (versão prévia)** .
1.  Em **configuração**, selecione sua configuração.

    ![Seleção de configuração para remover a configuração](media/how-to-configure/scope1.png)

1.  Na parte superior da tela de configuração, selecione **excluir**.

    ![Botão de eliminar](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>Não há nenhuma confirmação antes de excluir uma configuração. Certifique-se de que essa é a ação que você deseja executar antes de selecionar **excluir**.


## <a name="next-steps"></a>Passos seguintes 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é Azure AD Connect provisionamento de nuvem?](what-is-cloud-provisioning.md)
