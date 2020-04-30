---
title: O que é o estado empresarial a roaming em Azure Ative Directory?
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77194284"
---
# <a name="what-is-enterprise-state-roaming"></a>O que é o roaming de estado empresarial?

Com o Windows 10, os utilizadores do [Azure Ative Directory (Azure AD)](../fundamentals/active-directory-whatis.md) ganham a capacidade de sincronizar de forma segura as definições dos seus utilizadores e configurações de aplicações para a nuvem. O Enterprise State Roaming proporciona aos utilizadores uma experiência unificada nos seus dispositivos Windows e reduz o tempo necessário para configurar um novo dispositivo. O Enterprise State Roaming opera de forma semelhante à sincronização padrão de [definições](https://go.microsoft.com/fwlink/?linkid=2015135) de consumidor que foi introduzida pela primeira vez no Windows 8. Adicionalmente, a Enterprise State Roaming oferece:

* **Separação de dados corporativos e de consumidores** – As organizações controlam os seus dados, não existindo uma mistura de dados corporativos numa conta de nuvem de consumo ou dados de consumidores numa conta na nuvem empresarial.
* **Segurança reforçada** – Os dados são automaticamente encriptados antes de deixarem o dispositivo Windows 10 do utilizador utilizando a Azure Rights Management (Azure RMS), e os dados permanecem encriptados em repouso na nuvem. Todos os conteúdos permanecem encriptados em repouso na nuvem, exceto para os espaços de nome, como nomes de definições e nomes de aplicativos do Windows.  
* **Melhor gestão e monitorização** – Proporciona controlo e visibilidade sobre quem sincroniza as configurações na sua organização e em que dispositivos através da integração do portal Azure AD. 

Enterprise State Roaming está disponível em várias regiões azure. Pode encontrar a lista atualizada das regiões disponíveis na página [Azure Services by Regions](https://azure.microsoft.com/regions/#services) no âmbito do Azure Ative Directory.

| Artigo | Descrição |
| --- | --- |
| [Ativar o Roaming de Estado Empresarial no Azure Active Directory](enterprise-state-roaming-enable.md) |Enterprise State Roaming está disponível para qualquer organização com uma subscrição premium Azure Ative Directory (Azure AD). Para obter mais informações sobre como obter uma subscrição de AD Azure, consulte a página de [produto Azure AD.](https://azure.microsoft.com/services/active-directory) |
| [FAQ de definições e roaming de dados](enterprise-state-roaming-faqs.md) |Este artigo responde a algumas questões que os administradores de TI podem ter sobre configurações e sincronização de dados de aplicações. |
| [Política de grupo e definições de MDM para sincronização de configurações](enterprise-state-roaming-group-policy-settings.md) |O Windows 10 fornece definições de política de política de grupo e de gestão de dispositivos móveis (MDM) para limitar a sincronização de configurações. |
| [Referência das definições de roaming do Windows 10](enterprise-state-roaming-windows-settings-reference.md) |Uma lista de definições que serão vagueadas e/ou apoiadas no Windows 10. |
| [Resolução de problemas](enterprise-state-roaming-troubleshooting.md) |Este artigo passa por alguns passos básicos para resolução de problemas, e contém uma lista de questões conhecidas. |

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre a viabilidade do roaming do estado empresarial, consulte [o roaming do estado da empresa.](enterprise-state-roaming-enable.md)
