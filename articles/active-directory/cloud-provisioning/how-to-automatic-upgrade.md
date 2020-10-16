---
title: 'Azure AD Connect agente de provisionamento em nuvem: Atualização automática / Microsoft Docs'
description: Este artigo descreve a funcionalidade de atualização automática incorporada no agente de provisionamento de nuvem Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d0f7093f44a284ec26907d7c4bcfb2bdfd04763
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85360916"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect agente de provisionamento em nuvem: Atualização automática

Certificar-se de que o seu Azure Ative Directory (Azure AD) Conecte a instalação do agente de provisionamento em nuvem é sempre atualizado é fácil com a funcionalidade de atualização automática.

O agente está instalado aqui: "Ficheiros de programa\Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe"

Para verificar a sua versão, clique com o botão direito para executar propriedades e selecione propriedades e, em seguida, detalhes.

![Versão de ficheiro de agente](media/how-to-automatic-upgrade/agent1.png)

O atualizador do agente está instalado aqui: "Ficheiros de programa\Azure AD Connect Provisioning Agent Updater\AzureADConnectAgentUpdater.exe"

Para verificar a sua versão, clique com o botão direito para executar propriedades e selecione propriedades e, em seguida, detalhes.

![Versão do atualizador de agentes](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>Desinstalar o agente
Para remover o agente, vá a **Desinstalar ou alterar um programa** e desinstalar o seguinte:

- **Microsoft Azure AD Connect Agent Updater**
- **Microsoft Azure AD Connect Provisioning Agent**
- **Microsoft Azure AD Connect Provisioning Agent Package**

![Remoção de agentes](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é o aprovisionamento na cloud do Azure AD Connect?](what-is-cloud-provisioning.md)

