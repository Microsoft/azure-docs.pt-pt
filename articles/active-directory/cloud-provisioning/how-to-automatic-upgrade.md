---
title: 'Agente de fornecimento de nuvem Azure AD Connect: Atualização automática / Microsoft Docs'
description: Este artigo descreve a funcionalidade de atualização automática incorporada no agente de fornecimento de nuvem Azure AD Connect.
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
ms.openlocfilehash: f09b2fc685881aa8a7bd87b6a855c657af9ef43d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190318"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Agente de fornecimento de nuvem Azure AD Connect: Upgrade automático

Certificar-se de que a instalação do agente de fornecimento de nuvem Azure Ative (Azure AD) connect é sempre atualizada com a funcionalidade de atualização automática.

O agente está instalado aqui: "Ficheiros de programa\Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe"

Para verificar a sua versão, clique no direito das propriedades executáveis e selecione propriedades e, em seguida, detalhes.

![Versão de ficheiro do agente](media/how-to-automatic-upgrade/agent1.png)

O atualizador de agente está instalado aqui: "Ficheiros de programa\Azure AD Connect Provisioning Agent Updater\AzureADConnectAgentUpdater.exe"

Para verificar a sua versão, clique no direito das propriedades executáveis e selecione propriedades e, em seguida, detalhes.

![Versão atualizador de agente](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>Desinstalar o agente
Para remover o agente, vá para **Desinstalar ou alterar um programa** e desinstalar o seguinte:

- **Atualização do agente de ligação ad da Microsoft Azure**
- **Agente de provisionamento de ligação ad da Microsoft Azure**
- **Pacote de agente de provisionamento de ligação ad da Microsoft Azure**

![Remoção de agente](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é o aprovisionamento na cloud do Azure AD Connect?](what-is-cloud-provisioning.md)

