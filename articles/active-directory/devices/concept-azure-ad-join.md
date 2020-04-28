---
title: O que é um dispositivo azure ad?
description: Saiba como a gestão da identidade do dispositivo pode ajudá-lo a gerir dispositivos que estão a aceder a recursos no seu ambiente.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40f89fbe19b93601f9e0525f0387e402bd175fe4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78672671"
---
# <a name="azure-ad-joined-devices"></a>Dispositivos associados ao Azure AD

A adesão à Azure AD destina-se a organizações que queiram ser apenas em nuvem ou em nuvem. Qualquer organização pode implementar dispositivos azure ad não importa o tamanho ou a indústria. A Azure AD junta-se a obras mesmo num ambiente híbrido, permitindo o acesso a aplicações e recursos em nuvem e no local.

|   | Associação ao Azure AD |
| --- | --- |
| **Definição** | Juntou-se apenas à Azure AD exigindo que a conta organizacional assinasse o dispositivo |
| **Audiência primária** | Adequado tanto para organizações só em nuvem como híbridas. |
|   | Aplicável a todos os utilizadores de uma organização |
| **Propriedade dos dispositivos** | Organização |
| **Sistemas Operativos** | Todos os dispositivos do Windows 10 |
| **Aprovisionamento** | Self-service: Windows OOBE ou Definições |
|   | Inscrição em massa |
|   | Windows Autopilot |
| **Sinal de dispositivo em opções** | Contas organizacionais utilizando: |
|   | Palavra-passe |
|   | Windows Hello para empresas |
|   | Chaves de segurança FIDO2.0 (pré-visualização) |
| **Gestão de dispositivos** | Gestão de Dispositivos Móveis (exemplo: Microsoft Intune) |
|   | Cogestão com microsoft Intune e Microsoft Endpoint Configuration Manager |
| **Principais capacidades** | SSO tanto para os recursos de nuvem como para o local |
|   | Acesso Condicional através da inscrição do MDM e avaliação de conformidade com o MDM |
|   | Reset de palavra-passe de autosserviço e reset PIN do Windows Hello PIN no ecrã de bloqueio |
|   | Roaming do Estado Da Empresa através de dispositivos |

Os dispositivos aderes à Azure AD são assinados para usar uma conta aD organizacional Azure. O acesso aos recursos na organização pode ser ainda limitado com base nessa conta Azure AD e nas políticas de [Acesso Condicional](../conditional-access/overview.md) aplicadas à identidade do dispositivo.

Os administradores podem proteger e controlar ainda mais o Azure AD juntou-se a dispositivos utilizando ferramentas de Gestão de Dispositivos Móveis (MDM) como o Microsoft Intune ou em cenários de cogestão utilizando o Microsoft Endpoint Configuration Manager. Estas ferramentas fornecem um meio para impor configurações exigidas pela organização, como exigir que o armazenamento seja encriptado, complexidade de passwords, instalações de software e atualizações de software. Os administradores podem disponibilizar aplicações de organização para o Azure AD, utilizando o Gestor de Configuração para [gerir aplicações a partir da Microsoft Store for Business and Education.](/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business)

A adesão ao Azure AD pode ser realizada usando opções de self-service como a out of Box Experience (OOBE), inscrição a granel ou [Windows Autopilot](/intune/enrollment-autopilot).

Os dispositivos aderes à Azure AD ainda podem manter um único acesso de entrada aos recursos no local quando estão na rede da organização. Os dispositivos que são Azure AD juntos ainda podem autenticar servidores no local, como ficheiros, impressão e outras aplicações.

## <a name="scenarios"></a>Cenários

Embora a associação do Azure AD se destine principalmente a organizações que não têm uma infraestrutura do Windows Server Active Directory no local, pode sem dúvida utilizá-la em cenários nos quais:

- Quer transitar para uma infraestrutura baseada na cloud mediante a utilização do Azure AD e da MDM, como o Intune.
- Se, por exemplo, tiver de controlar dispositivos móveis, como tablets e telemóveis, não pode utilizar uma associação a um domínio no local.
- Os utilizadores precisam de aceder essencialmente a aplicações do Office 365 ou a outras aplicações SaaS integradas no Azure AD.
- Quer gerir um grupo de utilizadores no Azure AD em vez de no Active Directory. Este cenário pode aplicar-se, por exemplo, a trabalhadores sazonais, empreiteiros ou estudantes.
- Quer disponibilizar capacidades de associação aos colaboradores em sucursais remotas com uma infraestrutura no local limitada.

Pode configurar dispositivos associados ao Azure AD para dispositivos Windows 10.

O objetivo dos dispositivos associados ao Azure AD é simplificar:

- As implementações de dispositivos detidos pela organização no Windows
- O acesso a aplicações e recursos da organização a partir de qualquer dispositivo Windows
- A gestão baseada na cloud de dispositivos detidos pela organização
- Os utilizadores podem iniciar sessão nos seus dispositivos com o seu Azure AD ou com o trabalho de Diretório Ativo sincronizado ou contas escolares.

![Dispositivos associados ao Azure AD](./media/concept-azure-ad-join/azure-ad-joined-device.png)

A Associação do Azure AD pode ser implementada com um dos seguintes métodos:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Implementação em massa](/intune/windows-bulk-enroll)
- [Experiência personalizada](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>Passos seguintes

- [Planear a sua implementação de associação do Azure AD](azureadjoin-plan.md)
- [Como gerir o grupo de administradores locais em Azure AD aderiu a dispositivos](assign-local-admin.md)
- [Gerir identidades do dispositivo utilizando o portal Azure](device-management-azure-portal.md)
- [Gerir dispositivos velhos em Azure AD](manage-stale-devices.md)
