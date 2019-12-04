---
title: 'Agente de provisionamento de nuvem Azure AD Connect: atualização automática | Microsoft Docs'
description: Este tópico descreve o recurso de atualização automática interno no agente de provisionamento de Azure AD Connect Cloud.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 193804064fbf6d1abb2ce06df1e923ec709ef6de
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794461"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Agente de provisionamento de nuvem Azure AD Connect: atualização automática

Verificar se a instalação do agente de provisionamento de nuvem Azure AD Connect está sempre atualizada nunca foi tão fácil com o recurso de **atualização automática** . Esse recurso é habilitado por padrão e não pode ser desabilitado.

O agente é instalado aqui: **"programa PROGRAMAS\AZURE ad Connect Provisioning Agent\AADConnectProvisioningAgent.exe"**

Você pode verificar sua versão clicando com o botão direito do mouse no executável e selecionando Propriedades e, em seguida, detalhes.

![Versão do arquivo do agente](media/how-to-automatic-upgrade/agent1.png)

O atualizador do agente é instalado aqui: **"programa programas\azure o agente de provisionamento do AD Connect Updater\AzureADConnectAgentUpdater.exe"**

Você pode verificar sua versão clicando com o botão direito do mouse no executável e selecionando Propriedades e, em seguida, detalhes.

![Versão do atualizador do agente](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstalling-the-agent"></a>Desinstalando o agente
Para remover o agente, navegue até **desinstalar ou alterar um programa** e desinstale o seguinte:

- Atualizador do agente do Microsoft Azure AD Connect
- Agente de provisionamento do Microsoft Azure AD Connect
- Pacote de agente de provisionamento do Microsoft Azure AD Connect

![Remoção do agente](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Passos seguintes 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é Azure AD Connect provisionamento de nuvem?](what-is-cloud-provisioning.md)

