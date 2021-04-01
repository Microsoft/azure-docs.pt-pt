---
title: O que é a identidade do dispositivo no Azure Ative Directory?
description: Saiba como a gestão da identidade do dispositivo pode ajudá-lo a gerir os dispositivos que estão a aceder a recursos no seu ambiente.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8399f7101697af429b8c073c101dbfea203e98ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87025596"
---
# <a name="what-is-a-device-identity"></a>O que é uma identidade de dispositivo?

Com a proliferação de dispositivos de todas as formas e tamanhos e o conceito Bring Your Own Device (BYOD), os profissionais de TI são confrontados com dois objetivos um pouco opostos:

- Permitir que os utilizadores finais sejam produtivos onde e quando
- Proteja os bens da organização

Para proteger estes ativos, o pessoal de TI precisa primeiro de gerir as identidades do dispositivo. Os funcionários de TI podem basear-se na identidade do dispositivo com ferramentas como a Microsoft Intune para garantir que os padrões de segurança e conformidade são cumpridos. O Azure Ative Directory (Azure AD) permite uma única sação de saúde a dispositivos, apps e serviços de qualquer parte através destes dispositivos.

- Os seus utilizadores têm acesso aos bens da sua organização de que necessitam. 
- A sua equipa de TI obtém os controlos necessários para proteger a sua organização.

A gestão da identidade do dispositivo é a base para o [acesso condicional baseado em dispositivos.](../conditional-access/require-managed-devices.md) Com políticas de Acesso Condicional baseadas em dispositivos, pode garantir que o acesso a recursos no seu ambiente só é possível com dispositivos geridos.

## <a name="getting-devices-in-azure-ad"></a>Obtenção de dispositivos em Azure AD

Para obter um dispositivo em Azure AD, tem várias opções:

- **Azure AD registado**
   - Os dispositivos registados no Azure AD são normalmente propriedade pessoal ou dispositivos móveis, e são registados com uma conta pessoal da Microsoft ou outra conta local.
      - Windows 10
      - iOS
      - Android
      - MacOS
- **Azure AD associado**
   - Os dispositivos que são Azure AD aderidos são propriedade de uma organização, e são assinados com uma conta AD AZure pertencente a essa organização. Só existem na nuvem.
      - Windows 10 
      - [Máquinas virtuais do Windows Server 2019 em execução em Azure](howto-vm-sign-in-azure-ad-windows.md) (o núcleo do servidor não é suportado)
- **associado ao Azure AD Híbrido**
   - Os dispositivos que são híbridos Azure AD aderidos são propriedade de uma organização, e são assinados com uma conta ative Directory Domain Services pertencente a essa organização. Existem na nuvem e no local.
      - Windows 7, 8.1 ou 10
      - Windows Server 2008 ou mais recente

![Dispositivos exibidos na lâmina de dispositivos AD Azure](./media/overview/azure-active-directory-devices-all-devices.png)

> [!NOTE]
> Um estado híbrido refere-se a mais do que apenas o estado de um dispositivo. Para que um estado híbrido seja válido, é também necessário um utilizador Azure AD válido.

## <a name="device-management"></a>Gestão de dispositivos

Os dispositivos em Azure AD podem ser geridos utilizando ferramentas de Gestão de Dispositivos Móveis (MDM) como o Microsoft Intune, Microsoft Endpoint Configuration Manager, Group Policy (hybrid Azure AD join), ferramentas mobile application management (MAM) ou outras ferramentas de terceiros.

## <a name="resource-access"></a>Acesso a recursos

Registar e juntar dispositivos ao Azure AD dá aos seus utilizadores um Sinal sem emenda (SSO) para cloud resources. Este processo também permite aos administradores a capacidade de aplicar políticas de Acesso Condicional a recursos baseados no dispositivo a partir do que são acedidos. 

> [!NOTE]
> As políticas de acesso condicional baseadas em dispositivos requerem dispositivos híbridos aderentes Azure AD ou dispositivos registados Azure AD ou Azure AD.

O token de atualização primária (PRT) contém informações sobre o dispositivo e é necessário para sSO. Se tiver uma política de acesso condicional baseada no dispositivo definida numa aplicação, sem o PRT, o acesso é negado. As políticas híbridas de acesso condicionado requerem um dispositivo estatal híbrido e um utilizador válido que esteja inscrito.

Os dispositivos que são Azure AD unidos ou híbridoS Azure AD juntaram-se para beneficiar da SSO para os recursos da sua organização no local, bem como recursos em nuvem. Mais informações podem ser encontradas no artigo, [como o SSO para os recursos no local funciona em dispositivos aderidos a Azure AD](azuread-join-sso.md).

## <a name="device-security"></a>Segurança do dispositivo

- **Os dispositivos registados Azure AD** utilizam uma conta gerida pelo utilizador final, esta conta é ou uma conta Microsoft ou outra credencial gerida localmente com uma ou mais das seguintes.
   - Palavra-passe
   - PIN
   - Padrão
   - Windows Hello
- **A azure AD juntou-se ou híbrido Azure AD ad adsumo uma** conta organizacional em Azure AD garantido com um ou mais dos seguintes dispositivos.
   - Palavra-passe
   - Windows Hello para empresas

## <a name="provisioning"></a>Aprovisionamento

A colocação de dispositivos no Azure AD pode ser feita de forma self-service ou de um processo de fornecimento controlado por administradores.

## <a name="summary"></a>Resumo

Com a gestão de identidade do dispositivo em Azure AD, pode:

- Simplificar o processo de introdução e gestão de dispositivos em Azure AD
- Forneça aos seus utilizadores um acesso fácil de usar aos recursos baseados na nuvem da sua organização

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [os dispositivos registados Azure AD](concept-azure-ad-register.md)
- Saiba mais sobre [dispositivos aderidos a Azure AD](concept-azure-ad-join.md)
- Saiba mais sobre [dispositivos híbridos Azure AD](concept-azure-ad-join-hybrid.md)
- Para obter uma visão geral de como gerir as identidades dos dispositivos no portal Azure, consulte [a Gestão de identidades dos dispositivos utilizando o portal Azure](device-management-azure-portal.md).
- Para saber mais sobre o Acesso Condicional baseado no dispositivo, consulte [as políticas de Acesso Condicional baseado em dispositivos Configure Azure Ative.](../conditional-access/require-managed-devices.md)
