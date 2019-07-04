---
title: O que é de estado empresarial roaming no Azure Active Directory? | Microsoft Docs
description: Roaming de estado empresarial fornece aos usuários uma experiência unificada em todos os seus dispositivos Windows e reduz o tempo necessário para configurar um novo dispositivo.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5b60970592180a2353860369e637d4b9a9bb8f9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481910"
---
# <a name="what-is-enterprise-state-roaming"></a>O que é o roaming de estado empresarial?

Com o Windows 10, [do Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) os utilizadores obtêm a capacidade de sincronizar com segurança os seus dados de definições de aplicação para a cloud e as definições do utilizador. Roaming de estado empresarial fornece aos usuários uma experiência unificada em todos os seus dispositivos Windows e reduz o tempo necessário para configurar um novo dispositivo. Roaming de estado empresarial opera semelhante ao padrão [sincronização de definições de consumidor](https://go.microsoft.com/fwlink/?linkid=2015135) que foi introduzido pela primeira vez no Windows 8. Além disso, Roaming de estado empresarial oferece:

* **Separação da empresa e dados de consumidor** – as organizações estão no controlo dos seus dados e não há mistura de dados da empresa numa conta do consumidor na cloud ou de consumidor os dados numa conta empresarial na cloud.
* **Segurança avançada** – automaticamente os dados são encriptados antes de deixarem o dispositivo do utilizador com o Windows 10 com o Azure Rights Management (Azure RMS) e dados permanecem encriptados em inatividade na cloud. Todos os conteúdos permanecem encriptados em inatividade na cloud, exceto para os espaços de nomes, como nomes de definições e nomes de aplicações do Windows.  
* **Melhor gestão e monitorização** – fornece visibilidade e controlo sobre que sincroniza as definições na sua organização e em que dispositivos por meio da integração de portal do Azure AD. 

Roaming de estado empresarial está disponível em várias regiões do Azure. Pode encontrar a lista atualizada de regiões disponíveis sobre o [serviços do Azure por regiões](https://azure.microsoft.com/regions/#services) página em Azure Active Directory.

| Artigo | Descrição |
| --- | --- |
| [Ativar estado da empresa em Roaming no Azure Active Directory](enterprise-state-roaming-enable.md) |Roaming de estado empresarial está disponível para qualquer organização com uma subscrição Premium do Azure Active Directory (Azure AD). Para obter mais detalhes sobre como obter uma subscrição do Azure AD, consulte a [produto do Azure AD](https://azure.microsoft.com/services/active-directory) página. |
| [FAQ de roaming de dados e definições](enterprise-state-roaming-faqs.md) |Este tópico responde a algumas perguntas que os administradores de TI podem ter sobre as definições e sincronização de dados de aplicações. |
| [Política de grupo e definições de MDM para sincronização de definições](enterprise-state-roaming-group-policy-settings.md) |Windows 10 fornece política de grupo e gestão de dispositivos móveis (MDM) de configurações de diretiva para limitar a sincronização de definições. |
| [Referência de configurações de roaming com o Windows 10](enterprise-state-roaming-windows-settings-reference.md) |Segue-se uma lista completa de todas as definições que irá ser movidas e/ou uma cópia de segurança no Windows 10. |
| [Resolução de problemas](enterprise-state-roaming-troubleshooting.md) |Este tópico aborda alguns passos básicos para resolução de problemas e contém uma lista dos problemas conhecidos. |

## <a name="next-steps"></a>Passos Seguintes

Para obter informações sobre como ativar o roaming de estado empresarial, consulte [ativar o roaming de estado empresarial](enterprise-state-roaming-enable.md).
