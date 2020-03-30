---
title: Azure AD Connect nuvem que aprovisiona nova configuração de agente
description: Este artigo descreve como instalar o fornecimento de nuvens.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c086227d23901cb7904d8cc0a768e9f4b5ad43db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620976"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>Criar uma nova configuração para o fornecimento baseado em nuvem azure AD Connect

Depois de ter instalado o agente, tem de iniciar sessão no portal Azure e configurar o Azure Ative Directory (Azure AD) Conecte o fornecimento de nuvem. Siga estes passos para ativar o agente.

## <a name="configure-provisioning"></a>Configurar o provisionamento
Para configurar o fornecimento, siga estes passos.

1.  No portal do Azure, selecione **Azure Active Directory**.
1.  Selecione **Azure AD Connect**.
1.  Selecione **Gerir o fornecimento (Pré-visualização)**.

    ![Gerir o provisionamento (Pré-visualização)](media/how-to-configure/manage1.png)

1.  Selecione **Nova configuração**.
1.  No ecrã de configuração, o domínio no local é pré-povoado.
1.  Introduza um **e-mail de Notificação**. Este e-mail será notificado quando o fornecimento não for saudável.
1.  Mova o seletor para **ativar**e selecione **Guardar**.

    ![Provisionamento de Anúncios Azure (Pré-visualização)](media/tutorial-single-forest/configure2.png)

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Fornecimento de âmbito a utilizadores e grupos específicos
Pode examinar o agente para sincronizar utilizadores e grupos específicos utilizando grupos de Directórioactivo Ativo no local ou unidades organizacionais. Não é possível configurar grupos e unidades organizacionais dentro de uma configuração. 

1.  No portal do Azure, selecione **Azure Active Directory**.
1.  Selecione **Azure AD Connect**.
1.  Selecione **Gerir o fornecimento (Pré-visualização)**.
1.  Em **Configuração,** selecione a sua configuração.

    ![Secção de configuração](media/how-to-configure/scope1.png)

1.  Em **Configurar, selecione** **Todos os utilizadores** para alterar o âmbito da regra de configuração.

    ![Todas as opções de utilizadores](media/how-to-configure/scope2.png)

1. À direita, pode alterar o âmbito de aplicação para incluir apenas grupos de segurança. Introduza o nome distinto do grupo e selecione **Adicionar**.

    ![Opção de grupos de segurança selecionados](media/how-to-configure/scope3.png)

1.  Ou pode alterar o âmbito para incluir apenas unidades organizacionais específicas. Selecione **Done** and **Save**.  
2.  Uma vez alterado o âmbito, deve reiniciar o [fornecimento](#restart-provisioning) para iniciar uma sincronização imediata das alterações.

    ![Opção de unidades organizacionais selecionadas](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>Reinício do fornecimento 
Se não quiser esperar pela próxima execução programada, inicie o fornecimento executado utilizando o botão de **fornecimento Restart.** 
1.  No portal do Azure, selecione **Azure Active Directory**.
1.  Selecione **Azure AD Connect**.
1.  Selecione **Gerir o fornecimento (Pré-visualização)**.
1.  Em **Configuração,** selecione a sua configuração.

    ![Seleção de configuração para reiniciar o fornecimento](media/how-to-configure/scope1.png)

1.  No topo, selecione **reinicialização**.

## <a name="remove-a-configuration"></a>Remover uma configuração
Para eliminar uma configuração, siga estes passos.

1.  No portal do Azure, selecione **Azure Active Directory**.
1.  Selecione **Azure AD Connect**.
1.  Selecione **Gerir o fornecimento (Pré-visualização)**.
1.  Em **Configuração,** selecione a sua configuração.

    ![Seleção de configuração para remover a configuração](media/how-to-configure/scope1.png)

1.  Na parte superior do ecrã de configuração, selecione **Delete**.

    ![Eliminar botão](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>Não há confirmação antes de apagar uma configuração. Certifique-se de que esta é a ação que pretende tomar antes de selecionar **Delete**.


## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é o aprovisionamento na cloud do Azure AD Connect?](what-is-cloud-provisioning.md)
