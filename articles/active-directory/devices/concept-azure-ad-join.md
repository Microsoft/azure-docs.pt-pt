---
title: O que é um Azure AD associado a um dispositivo?
description: Saiba como gestão de identidades do dispositivo pode ajudá-lo a gerir dispositivos que estão a aceder a recursos no seu ambiente.
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
ms.openlocfilehash: 4af3aea7218ea8792bb66188e8df7baf9f460b0b
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462814"
---
# <a name="azure-ad-joined-devices"></a>Dispositivos associados ao Azure AD

Associação do Azure AD destina-se para as organizações que querem ser cloud-first ou apenas na cloud. Qualquer organização pode implementar dispositivos associados ao Azure AD, não importa o tamanho ou da indústria. Associação do Azure AD funciona até mesmo num ambiente híbrido, permitir o acesso a aplicações na cloud e no local e recursos.

|   | Associação do Azure AD |
| --- | --- |
| **Definição** | Associado a um apenas para o Azure AD que requerem uma conta organizacional para iniciar sessão no dispositivo |
| **Público-alvo principal** | Adequado para ambos apenas na cloud e organizações híbridas. |
|   | Aplicável a todos os utilizadores numa organização |
| **Propriedade do dispositivo** | Organização |
| **Sistemas operativos** | Todos os dispositivos Windows 10 |
| **Aprovisionamento** | Self-Service: Windows OOBE ou definições |
|   | Inscrição em massa |
|   | Windows Autopilot |
| **Início de sessão de dispositivo nas opções** | Contas organizacionais com: |
|   | Palavra-passe |
|   | Windows Hello para Empresas |
|   | Chaves de segurança FIDO2.0 (pré-visualização) |
| **Gestão de Dispositivos** | Gestão de dispositivos móveis (exemplo: Microsoft Intune) |
|   | Cogestão com o Microsoft Intune e o System Center Configuration Manager |
| **Principais capacidades** | SSO para recursos na nuvem e no local |
|   | Acesso condicional através de inscrição de MDM e de avaliação de conformidade do MDM |
|   | Self-service de reposição de palavra-passe e o Windows Hello PIN, repor no ecrã de bloqueio |
|   | Roaming de estado empresarial em dispositivos |

Dispositivos do Azure AD associado tiver entrados nos através de uma organização conta do Azure AD. Acesso a recursos na organização pode ser ainda mais limitada com base nessa conta do Azure AD e [políticas de acesso condicional](../conditional-access/overview.md) aplicadas para a identidade de dispositivo.

Os administradores podem proteger e ainda mais com as ferramentas de gestão de dispositivos móveis (MDM), como o Microsoft Intune ou em cenários de cogestão usando o System Center Configuration Manager de dispositivos associados ao controle do Azure AD. Essas ferramentas fornecem um meio para impor configurações necessárias de organização como exigir armazenamento seja encriptado, a complexidade de palavra-passe, instalações de software e atualizações de software. Os administradores podem disponibilizar aplicativos da organização para dispositivos associados ao Azure AD através do [System Center Configuration Manager e a Microsoft Store para empresas](https://docs.microsoft.com/sccm/apps/deploy-use/manage-apps-from-the-windows-store-for-business).

Associação do Azure AD pode ser feita usando opções de Self-serviços, como o de caixa de experiência original (OOBE), a inscrição em massa, ou [Windows Autopilot](https://docs.microsoft.com/intune/enrollment-autopilot).

Dispositivos do Azure AD associado ainda podem manter o acesso de início de sessão único para recursos no local quando estão na rede da organização. Dispositivos que estão associados ao Azure AD ainda possam autenticar para servidores no local, como o ficheiro, impressão e outras aplicações.

## <a name="scenarios"></a>Cenários

Embora a associação do Azure AD se destine principalmente a organizações que não têm uma infraestrutura do Windows Server Active Directory no local, pode sem dúvida utilizá-la em cenários nos quais:

- Quer transitar para uma infraestrutura baseada na cloud mediante a utilização do Azure AD e da MDM, como o Intune.
- Se, por exemplo, tiver de controlar dispositivos móveis, como tablets e telemóveis, não pode utilizar uma associação a um domínio no local.
- Os utilizadores precisam de aceder essencialmente a aplicações do Office 365 ou a outras aplicações SaaS integradas no Azure AD.
- Quer gerir um grupo de utilizadores no Azure AD em vez de no Active Directory. Este cenário pode aplicar, por exemplo, a funcionários temporários, contratantes ou estudantes.
- Quer disponibilizar capacidades de associação aos colaboradores em sucursais remotas com uma infraestrutura no local limitada.

Pode configurar dispositivos associados ao Azure AD para dispositivos Windows 10.

O objetivo dos dispositivos associados ao Azure AD é simplificar:

- As implementações de dispositivos detidos pela organização no Windows
- O acesso a aplicações e recursos da organização a partir de qualquer dispositivo Windows
- A gestão baseada na cloud de dispositivos detidos pela organização
- Os utilizadores iniciem sessão nos seus dispositivos com o seu Azure AD ou do Active Directory sincronizado contas escolares ou.

![Dispositivos associados ao Azure AD](./media/concept-azure-ad-join/azure-ad-joined-device.png)

A Associação do Azure AD pode ser implementada com um dos seguintes métodos:

- [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Implementação em massa](https://docs.microsoft.com/intune/windows-bulk-enroll)
- [Experiência personalizada](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>Passos Seguintes

- [Planear a implementação de associação do Azure AD](azureadjoin-plan.md)
- [Dispositivos associados ao como gerir o grupo de administradores locais no Azure AD](assign-local-admin.md)
- [Gerir identidades de dispositivos no portal do Azure](device-management-azure-portal.md)
- [Gerir dispositivos obsoletos no Azure AD](manage-stale-devices.md)
