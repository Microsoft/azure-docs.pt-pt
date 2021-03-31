---
title: O que é o estado empresarial a vaguear no Azure Ative Directory?
description: Enterprise State Roaming proporciona aos utilizadores uma experiência unificada em todos os seus dispositivos Windows
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: c22baf0a08718883f0c0c9844cc395f607b5b20d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "77194284"
---
# <a name="what-is-enterprise-state-roaming"></a>O que é o roaming de estado empresarial?

Com o Windows 10, os utilizadores [do Azure Ative Directory (Azure AD)](../fundamentals/active-directory-whatis.md) ganham a capacidade de sincronizar de forma segura as definições dos seus utilizadores e os dados de definições de aplicações para a nuvem. O Enterprise State Roaming proporciona aos utilizadores uma experiência unificada em todos os seus dispositivos Windows e reduz o tempo necessário para configurar um novo dispositivo. Enterprise State Roaming opera de forma semelhante à sincronização de [configurações](https://go.microsoft.com/fwlink/?linkid=2015135) padrão do consumidor que foi introduzida pela primeira vez no Windows 8. Além disso, a Enterprise State Roaming oferece:

* **Separação dos dados corporativos e de consumo** – As organizações controlam os seus dados, e não há mistura de dados corporativos numa conta de nuvem de consumo ou de consumidores numa conta de nuvem empresarial.
* **Segurança reforçada** – Os dados são automaticamente encriptados antes de sair do dispositivo Windows 10 do utilizador, utilizando a Azure Rights Management (Azure RMS), e os dados permanecem encriptados em repouso na nuvem. Todos os conteúdos permanecem encriptados em repouso na nuvem, exceto nos espaços de nome, como nomes de definições e nomes de aplicações do Windows.  
* **Melhor gestão e monitorização** – Proporciona controlo e visibilidade sobre quem sincroniza as definições na sua organização e em que dispositivos através da integração do portal AD AD do Azure. 

Enterprise State Roaming está disponível em várias regiões de Azure. Pode encontrar a lista atualizada das regiões disponíveis na página [Azure Services by Regions](https://azure.microsoft.com/regions/#services) no âmbito do Azure Ative Directory.

| Artigo | Description |
| --- | --- |
| [Ativar o Roaming de Estado Empresarial no Azure Active Directory](enterprise-state-roaming-enable.md) |Enterprise State Roaming está disponível para qualquer organização com uma assinatura Premium Azure Ative Directory (Azure AD). Para obter mais informações sobre como obter uma subscrição AD Azure, consulte a página do [produto AZure AD.](https://azure.microsoft.com/services/active-directory) |
| [FAQ de definições e roaming de dados](enterprise-state-roaming-faqs.md) |Este artigo responde a algumas perguntas que os administradores de TI podem ter sobre configurações e sincronização de dados de aplicações. |
| [Políticas de grupo e configurações de MDM para sincronização de configurações](enterprise-state-roaming-group-policy-settings.md) |O Windows 10 fornece definições de política de Política de Grupo e gestão de dispositivos móveis (MDM) para limitar a sincronização de definições. |
| [Referência das definições de roaming do Windows 10](enterprise-state-roaming-windows-settings-reference.md) |Uma lista de definições que serão percorridas e/ou apoiadas no Windows 10. |
| [Resolução de problemas](enterprise-state-roaming-troubleshooting.md) |Este artigo passa por alguns passos básicos para a resolução de problemas, e contém uma lista de questões conhecidas. |

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre como permitir o roaming do estado da empresa, consulte [permitir o roaming do estado da empresa.](enterprise-state-roaming-enable.md)
