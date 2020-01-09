---
title: 'Agente de provisionamento de nuvem Azure AD Connect: atualização automática | Microsoft Docs'
description: Este artigo descreve o recurso de atualização automática interno no agente de provisionamento de Azure AD Connect Cloud.
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
ms.openlocfilehash: d3786386f75f4b85fe89562254eab63471de6cb2
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75549402"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Agente de provisionamento de nuvem Azure AD Connect: atualização automática

Verificar se a instalação do agente de provisionamento de nuvem do Azure Active Directory (Azure AD) Connect está sempre atualizada é fácil com o recurso de atualização automática. Esse recurso é habilitado por padrão e não pode ser desabilitado.

O agente é instalado aqui: "programa Programas\azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe"

Para verificar sua versão, clique com o botão direito do mouse no executável e selecione Propriedades e, em seguida, detalhes.

![Versão do arquivo do agente](media/how-to-automatic-upgrade/agent1.png)

O atualizador do agente é instalado aqui: "programa Programas\azure o agente de provisionamento do AD Connect Updater\AzureADConnectAgentUpdater.exe"

Para verificar sua versão, clique com o botão direito do mouse no executável e selecione Propriedades e, em seguida, detalhes.

![Versão do atualizador do agente](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>Desinstalar o agente
Para remover o agente, vá para **desinstalar ou alterar um programa** e desinstale o seguinte:

- **Atualizador do agente do Microsoft Azure AD Connect**
- **Agente de provisionamento do Microsoft Azure AD Connect**
- **Pacote de agente de provisionamento do Microsoft Azure AD Connect**

![Remoção do agente](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Passos seguintes 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é Azure AD Connect provisionamento de nuvem?](what-is-cloud-provisioning.md)

