---
title: O que é um dispositivo ingressado no Azure AD?
description: Saiba como o gerenciamento de identidades de dispositivo pode ajudá-lo a gerenciar dispositivos que estão acessando recursos em seu ambiente.
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
ms.openlocfilehash: 24ec4373bceb3cc3c9e5be2c7a0dab1f62197b3c
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76512203"
---
# <a name="azure-ad-joined-devices"></a>Dispositivos associados ao Azure AD

O ingresso no Azure AD destina-se a organizações que desejam ser somente na nuvem ou somente em nuvem. Qualquer organização pode implantar dispositivos ingressados no Azure AD, independentemente do tamanho ou do setor. O ingresso no Azure AD funciona mesmo em um ambiente híbrido, permitindo o acesso a aplicativos e recursos locais e na nuvem.

|   | Ingresso no Azure AD |
| --- | --- |
| **Definição** | Ingressado somente no Azure AD exigindo que a conta organizacional se conecte ao dispositivo |
| **Público-alvo principal** | Adequado para organizações híbridas e somente de nuvem. |
|   | Aplicável a todos os usuários em uma organização |
| **Propriedade do dispositivo** | Organização |
| **Sistemas operacionais** | Todos os dispositivos Windows 10 |
| **Aprovisionamento** | Autoatendimento: Windows OOBE ou configurações |
|   | Inscrição em massa |
|   | Windows Autopilot |
| **Opções de entrada do dispositivo** | Contas organizacionais usando: |
|   | Palavra-passe |
|   | Windows Hello para Empresas |
|   | Chaves de segurança do FIDO 2.0 (versão prévia) |
| **Gestão de Dispositivos** | Gerenciamento de dispositivo móvel (exemplo: Microsoft Intune) |
|   | Cogerenciamento com Microsoft Intune e Microsoft Endpoint Configuration Manager |
| **Principais recursos** | SSO para recursos de nuvem e locais |
|   | Acesso condicional por meio de registro de MDM e avaliação de conformidade de MDM |
|   | Redefinição de senha por autoatendimento e redefinição de PIN do Windows Hello na tela de bloqueio |
|   | Enterprise State Roaming entre dispositivos |

Os dispositivos ingressados no Azure AD são conectados ao usando uma conta organizacional do Azure AD. O acesso a recursos na organização pode ser ainda mais limitado com base nessa conta do Azure AD e [nas políticas de acesso condicional](../conditional-access/overview.md) aplicadas à identidade do dispositivo.

Os administradores podem proteger e controlar ainda mais os dispositivos adicionados ao Azure AD usando ferramentas de MDM (gerenciamento de dispositivo móvel) como Microsoft Intune ou em cenários de cogerenciamento usando o Microsoft Endpoint Configuration Manager. Essas ferramentas fornecem um meio de impor configurações necessárias à organização, como exigir que o armazenamento seja criptografado, complexidade de senha, instalações de software e atualizações de software. Os administradores podem disponibilizar aplicativos da organização para dispositivos ingressados no Azure AD usando Configuration Manager para [gerenciar aplicativos da Microsoft Store para negócios e educação](https://docs.microsoft.com/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business).

O ingresso no Azure AD pode ser feito usando opções de autoatendimento como a OOBE (configuração inicial do uso), o registro em massa ou o [Windows AutoPilot](https://docs.microsoft.com/intune/enrollment-autopilot).

Os dispositivos ingressados no Azure AD ainda podem manter o acesso de logon único a recursos locais quando estão na rede da organização. Os dispositivos que são ingressados no Azure AD ainda podem ser autenticados em servidores locais como arquivos, impressão e outros aplicativos.

## <a name="scenarios"></a>Cenários

Embora a associação do Azure AD se destine principalmente a organizações que não têm uma infraestrutura do Windows Server Active Directory no local, pode sem dúvida utilizá-la em cenários nos quais:

- Quer transitar para uma infraestrutura baseada na cloud mediante a utilização do Azure AD e da MDM, como o Intune.
- Se, por exemplo, tiver de controlar dispositivos móveis, como tablets e telemóveis, não pode utilizar uma associação a um domínio no local.
- Os utilizadores precisam de aceder essencialmente a aplicações do Office 365 ou a outras aplicações SaaS integradas no Azure AD.
- Quer gerir um grupo de utilizadores no Azure AD em vez de no Active Directory. Esse cenário pode ser aplicado, por exemplo, a trabalhadores sazonais, prestadores de trabalho ou alunos.
- Quer disponibilizar capacidades de associação aos colaboradores em sucursais remotas com uma infraestrutura no local limitada.

Pode configurar dispositivos associados ao Azure AD para dispositivos Windows 10.

O objetivo dos dispositivos associados ao Azure AD é simplificar:

- As implementações de dispositivos detidos pela organização no Windows
- O acesso a aplicações e recursos da organização a partir de qualquer dispositivo Windows
- A gestão baseada na cloud de dispositivos detidos pela organização
- Os usuários podem entrar em seus dispositivos com o Azure AD ou sincronizados Active Directory contas corporativas ou de estudante.

![Dispositivos associados ao Azure AD](./media/concept-azure-ad-join/azure-ad-joined-device.png)

A Associação do Azure AD pode ser implementada com um dos seguintes métodos:

- [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Implementação em massa](https://docs.microsoft.com/intune/windows-bulk-enroll)
- [Experiência personalizada](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>Passos seguintes

- [Planejar sua implementação de ingresso no Azure AD](azureadjoin-plan.md)
- [Como gerenciar o grupo de administradores locais em dispositivos ingressados no Azure AD](assign-local-admin.md)
- [Gerenciar identidades de dispositivo usando o portal do Azure](device-management-azure-portal.md)
- [Gerenciar dispositivos obsoletos no Azure AD](manage-stale-devices.md)
