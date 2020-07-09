---
title: Azure AD Connect nuvem que provisiona nova configuração de agente
description: Este artigo descreve como instalar o fornecimento de nuvens.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50f02ea42bb792320da6e2523b733f09afd412a0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85360967"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>Criar uma nova configuração para o Azure AD Connect Provisioning baseado em nuvem

Depois de ter instalado o agente, tem de iniciar seducação no portal Azure e configurar o Azure Ative Directory (Azure AD) Ligar o provisionamento em nuvem. Siga estes passos para ativar o agente.

## <a name="configure-provisioning"></a>Disposição de configuração
Para configurar o provisionamento, siga estes passos.

1.  No portal Azure, selecione **Azure Ative Directory**.
1.  Selecione **Azure Ad Connect**.
1.  **Selecione Gerir o provisionamento (Pré-visualização)**.

    ![Gerir o provisionamento (Pré-visualização)](media/how-to-configure/manage1.png)

1.  Selecione **nova configuração**.
1.  No ecrã de configuração, o domínio no local é pré-voizado.
1.  Introduza um **e-mail de Notificação**. Este e-mail será notificado quando o provisionamento não for saudável.
1.  Mova o seletor para **ativar**e **selecione Guardar**.

    ![Provisão de Ad Azure (Pré-visualização)](media/tutorial-single-forest/configure2.png)

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Âmbito de fornecimento a utilizadores e grupos específicos
Pode procurar o agente para sincronizar utilizadores e grupos específicos utilizando grupos de Diretório Ativo no local ou unidades organizacionais. Não é possível configurar grupos e unidades organizacionais dentro de uma configuração. 

1.  No portal Azure, selecione **Azure Ative Directory**.
1.  Selecione **Azure Ad Connect**.
1.  **Selecione Gerir o provisionamento (Pré-visualização)**.
1.  Em **Configuração,** selecione a sua configuração.

    ![Secção de configuração](media/how-to-configure/scope1.png)

1.  Em **Configuração**, selecione **Todos os utilizadores** para alterar o âmbito da regra de configuração.

    ![Todas as opções de utilizadores](media/how-to-configure/scope2.png)

1. À direita, pode alterar o âmbito de aplicação para incluir apenas grupos de segurança. Introduza o nome distinto do grupo e selecione **Add**.

    ![Opção de grupos de segurança selecionados](media/how-to-configure/scope3.png)

1.  Ou pode alterar o âmbito para incluir apenas unidades organizacionais específicas. Selecione **Feito** e **Guarde**.  
2.  Uma vez alterado o âmbito, deve [reiniciar o provisionamento](#restart-provisioning) para iniciar uma sincronização imediata das alterações.

    ![Opção de unidades organizacionais selecionadas](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>Reiniciar o provisionamento 
Se não quiser esperar pela próxima execução programada, desencadeie o provisionamento executado utilizando o botão **de provisionamento Restart.** 
1.  No portal Azure, selecione **Azure Ative Directory**.
1.  Selecione **Azure Ad Connect**.
1.  **Selecione Gerir o provisionamento (Pré-visualização)**.
1.  Em **Configuração,** selecione a sua configuração.

    ![Seleção de configuração para reiniciar o provisionamento](media/how-to-configure/scope1.png)

1.  No topo, **selecione Reatamento**.

## <a name="remove-a-configuration"></a>Remover uma configuração
Para eliminar uma configuração, siga estes passos.

1.  No portal Azure, selecione **Azure Ative Directory**.
1.  Selecione **Azure Ad Connect**.
1.  **Selecione Gerir o provisionamento (Pré-visualização)**.
1.  Em **Configuração,** selecione a sua configuração.

    ![Seleção de configuração para remover configuração](media/how-to-configure/scope1.png)

1.  Na parte superior do ecrã de configuração, **selecione Delete**.

    ![Eliminar botão](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>Não há confirmação antes de apagar uma configuração. Certifique-se de que esta é a ação que pretende tomar antes de selecionar **Delete**.


## <a name="next-steps"></a>Próximos passos 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é o aprovisionamento na cloud do Azure AD Connect?](what-is-cloud-provisioning.md)
