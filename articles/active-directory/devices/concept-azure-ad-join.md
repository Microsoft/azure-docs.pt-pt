---
title: O que é um dispositivo AZure AD?
description: Saiba mais sobre os dispositivos aderidos ao Azure AD e como a gestão da identidade do dispositivo pode ajudá-lo a gerir dispositivos que estão a aceder a recursos no seu ambiente.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b7cc0535549f2dbcdef4ab043ee506527fdbc5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90601457"
---
# <a name="azure-ad-joined-devices"></a>Dispositivos associados ao Azure AD

A ad a ad azure destina-se a organizações que querem ser apenas cloud-first ou cloud-only. Qualquer organização pode implementar dispositivos aderidos a Azure AD independentemente do tamanho ou indústria. A Azure AD junta-se a obras mesmo num ambiente híbrido, permitindo o acesso a apps e recursos em nuvem e no local.

| Associação ao Azure AD | Description |
| --- | --- |
| **Definição** | Juntou-se apenas à Azure AD exigindo conta organizacional para iniciar seduca no dispositivo |
| **Público primário** | Adequado para organizações apenas em nuvem e híbridas. |
|   | Aplicável a todos os utilizadores de uma organização |
| **Propriedade dos dispositivos** | Organização |
| **Sistemas Operativos** | Todos os dispositivos Windows 10, exceto o Windows 10 Home |
|   | [Máquinas virtuais do Windows Server 2019 em execução em Azure](howto-vm-sign-in-azure-ad-windows.md) (o núcleo do servidor não é suportado) |
| **Aprovisionamento** | Self-service: Windows OOBE ou Definições |
|   | Inscrição em massa |
|   | Windows Autopilot |
| **Sinal do dispositivo em opções** | Contas organizacionais utilizando: |
|   | Palavra-passe |
|   | Windows Hello para empresas |
|   | Teclas de segurança FIDO2.0 (pré-visualização) |
| **Gestão de dispositivos** | Gestão de Dispositivos Móveis (exemplo: Microsoft Intune) |
|   | Cogestão com o Microsoft Intune e o Microsoft Endpoint Configuration Manager |
| **Principais capacidades** | SSO para recursos de nuvem e no local |
|   | Acesso Condicional através da inscrição de MDM e avaliação de conformidade do MDM |
|   | Redefinição de password de autosserviço e reset do Windows Hello PIN no ecrã de bloqueio |
|   | Roaming do Estado da Empresa através de dispositivos |

Os dispositivos aderidos a Azure AD são assinados para usar uma conta AZure AD organizacional. O acesso aos recursos na organização pode ser ainda limitado com base na conta AD Azure e [nas políticas de Acesso Condicional](../conditional-access/howto-conditional-access-policy-compliant-device.md) aplicadas à identidade do dispositivo.

Os administradores podem proteger e controlar ainda mais os dispositivos aderidos ao Azure AD utilizando ferramentas de Gestão de Dispositivos Móveis (MDM) como o Microsoft Intune ou em cenários de cogestão utilizando o Microsoft Endpoint Configuration Manager. Estas ferramentas fornecem um meio para impor configurações exigidas pela organização, como exigir que o armazenamento seja encriptado, complexidade de palavras-passe, instalações de software e atualizações de software. Os administradores podem disponibilizar aplicações de organização para dispositivos aderidos ao Azure AD utilizando o Gestor de Configuração para [Gerir aplicações da Microsoft Store para negócios e educação.](/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business)

A ad a ad azure pode ser realizada usando opções de self-service como a Experiência out of Box (OOBE), a inscrição a granel ou [o Windows Autopilot](/intune/enrollment-autopilot).

Os dispositivos aderidos a Azure AD ainda podem manter um único acesso de acesso a recursos no local quando estão na rede da organização. Os dispositivos que são Azure AD unidos ainda podem autenticar para servidores no local como ficheiros, impressão e outras aplicações.

## <a name="scenarios"></a>Cenários

Embora a associação do Azure AD se destine principalmente a organizações que não têm uma infraestrutura do Windows Server Active Directory no local, pode sem dúvida utilizá-la em cenários nos quais:

- Quer transitar para uma infraestrutura baseada na cloud mediante a utilização do Azure AD e da MDM, como o Intune.
- Se, por exemplo, tiver de controlar dispositivos móveis, como tablets e telemóveis, não pode utilizar uma associação a um domínio no local.
- Os seus utilizadores precisam principalmente de aceder ao Microsoft 365 ou a outras aplicações SaaS integradas com AZure AD.
- Quer gerir um grupo de utilizadores no Azure AD em vez de no Active Directory. Este cenário pode aplicar-se, por exemplo, a trabalhadores sazonais, empreiteiros ou estudantes.
- Quer disponibilizar capacidades de associação aos colaboradores em sucursais remotas com uma infraestrutura no local limitada.

Pode configurar dispositivos ligados a Azure AD para todos os dispositivos Windows 10, com exceção do Windows 10 Home.

O objetivo dos dispositivos associados ao Azure AD é simplificar:

- As implementações de dispositivos detidos pela organização no Windows
- O acesso a aplicações e recursos da organização a partir de qualquer dispositivo Windows
- A gestão baseada na cloud de dispositivos detidos pela organização
- Os utilizadores devem inscrever-se nos seus dispositivos com o seu AD Azure ou com contas de Ative Directory ou escola sincronizadas.

![Dispositivos associados ao Azure AD](./media/concept-azure-ad-join/azure-ad-joined-device.png)

A Associação do Azure AD pode ser implementada com um dos seguintes métodos:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Implementação em massa](/intune/windows-bulk-enroll)
- [Experiência personalizada](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>Passos seguintes

- [Planear a sua implementação de associação do Azure AD](azureadjoin-plan.md)
- [Como gerir o grupo de administradores locais em Azure AD juntou dispositivos](assign-local-admin.md)
- [Gerir identidades de dispositivos com o portal do Azure](device-management-azure-portal.md)
- [Gerir dispositivos obsoletos no AAD](manage-stale-devices.md)
