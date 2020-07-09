---
title: Identidade do dispositivo e virtualização do ambiente de trabalho - Azure Ative Directory
description: Saiba como as identidades dos dispositivos VDI e AZure AD podem ser usadas em conjunto
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b431cee3b8e5fc168dec2766442d6f6b9869d1e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74900378"
---
# <a name="device-identity-and-desktop-virtualization"></a>Identidade do dispositivo e virtualização do ambiente de trabalho

Os administradores normalmente implantam plataformas virtuais de infraestrutura de ambiente de trabalho (VDI) que hospedam sistemas operativos Windows nas suas organizações. Os administradores implantam o VDI para:

- Dinamizar a gestão.
- Reduzir custos através da consolidação e centralização de recursos.
- Fornecer mobilidade aos utilizadores finais e a liberdade de aceder a desktops virtuais a qualquer momento, a partir de qualquer lugar, em qualquer dispositivo.

Existem dois tipos primários de ambientes de trabalho virtuais:

- Persistente
- Não persistente

As versões persistentes utilizam uma imagem de ambiente de trabalho única para cada utilizador ou um conjunto de utilizadores. Estes ambientes de trabalho exclusivos podem ser personalizados e guardados para uso futuro. 

Versões não persistentes utilizam uma coleção de desktops que os utilizadores podem aceder quando necessário. Estes ambientes de trabalho não persistentes são revertidos para o seu estado original após a indicação do utilizador.

Este artigo cobrirá a orientação da Microsoft aos administradores sobre o suporte à identidade do dispositivo e ao VDI. Para obter mais informações sobre a identidade do dispositivo, consulte o artigo [O que é uma identidade do dispositivo](overview.md).

## <a name="supported-scenarios"></a>Cenários suportados

Antes de configurar as identidades do dispositivo em Azure AD para o seu ambiente VDI, familiarize-se com os cenários suportados. O quadro abaixo ilustra quais os cenários de provisionamento que são apoiados. O provisionamento neste contexto implica que um administrador pode configurar as identidades do dispositivo em escala sem exigir qualquer interação do utilizador final.

| Tipo de identidade do dispositivo | Infraestrutura de identidade | Dispositivos Windows | Versão da plataforma VDI | Suportado |
| --- | --- | --- | --- | --- |
| associado ao Azure AD Híbrido | Federado* | Windows current*** e Windows down-level**** | Persistente | Sim |
|   |   | Corrente do Windows | Não Persistente | Não |
|   |   | Dispositivos Windows de nível inferior | Não Persistente | Sim |
|   | Gerido** | Windows current e Windows down-level | Persistente | Sim |
|   |   | Corrente do Windows | Não Persistente | Não |
|   |   | Dispositivos Windows de nível inferior | Não Persistente | Sim |
| Azure AD associado | Federados | Corrente do Windows | Persistente | Não |
|   |   |   | Não Persistente | Não |
|   | Gerido | Corrente do Windows | Persistente | Não |
|   |   |   | Não Persistente | Não |
| Azure AD registado | Federados | Corrente do Windows | Persistente | Não |
|   |   |   | Não Persistente | Não |
|   | Gerido | Corrente do Windows | Persistente | Não |
|   |   |   | Não Persistente | Não |

\*Um ambiente de infraestrutura de identidade **federada** representa um ambiente com um fornecedor de identidade, como o AD FS ou outro IDP de terceiros.

\*\*Um ambiente de infraestrutura de identidade **gerido** representa um ambiente com Azure AD como o fornecedor de identidade implantado com [sincronização de haxixe de palavra-passe (PHS)](../hybrid/whatis-phs.md) ou [autenticação de passagem (PTA)](../hybrid/how-to-connect-pta.md) com [um único sinal sem emenda.](../hybrid/how-to-connect-sso.md)

\*\*\*Os dispositivos **atuais do Windows** representam o Windows 10, o Windows Server 2016 e o Windows Server 2019.

\*\*\*\*Os dispositivos **de nível inferior** do Windows representam o Windows 7, o Windows 8.1, o Windows Server 2008 R2, o Windows Server 2012 e o Windows Server 2012 R2. Para obter informações sobre o Windows 7, consulte [o Suporte para o Windows 7 está a terminar](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support). Para obter informações de suporte no Windows Server 2008 R2, consulte [Prepare-se para o fim do suporte do Windows Server 2008](https://www.microsoft.com/cloud-platform/windows-server-2008).

## <a name="microsofts-guidance"></a>Orientação da Microsoft

Os administradores devem fazer referência aos seguintes artigos, com base na sua infraestrutura de identidade, para aprender a configurar a ad AD híbrida.

- [Configure híbrido Azure Ative Directy junta-se ao ambiente federado](hybrid-azuread-join-federated-domains.md)
- [Configure híbrido Azure Ative Directy junta-se para ambiente gerido](hybrid-azuread-join-managed-domains.md)

Se estiver a contar com a Ferramenta de Preparação do Sistema (sysprep.exe) e se estiver a utilizar uma imagem pré-Windows 10 1809 para instalação, certifique-se de que a imagem não é de um dispositivo que já está registado no AD Azure como a AD híbrido Azure aderiu.

Se estiver a contar com uma foto de Máquina Virtual (VM) para criar VMs adicionais, certifique-se de que o instantâneo não é de um VM que já está registado no AZure AD como ad AD Híbrido Azure.

Ao implementar vDI não persistente, os administradores de TI devem estar atentos à gestão de dispositivos em Azure AD. A Microsoft recomenda que os administradores de TI implementem a orientação abaixo. Se não o fizer, o seu diretório terá muitos dispositivos híbridos Azure AD que foram registados a partir da sua plataforma VDI não persistente.

- Crie e utilize um prefixo para o nome de exibição do computador que indique o ambiente de trabalho como baseado em VDI.
- Implementar o seguinte comando como parte do script logoff. Este comando irá desencadear uma chamada de melhor esforço para a Azure AD para eliminar o dispositivo.
   - Para dispositivos de nível inferior do Windows – autoworkplace.exe /leave
- Definir e implementar o processo de [gestão de dispositivos antigos.](manage-stale-devices.md)
   - Uma vez que tenha uma estratégia para identificar os dispositivos híbridos Azure AD não persistentes, pode ser mais agressivo na limpeza destes dispositivos para garantir que o seu diretório não seja consumido com muitos dispositivos antigos.
 
## <a name="next-steps"></a>Próximos passos

[Configurar o Azure Ative Directy híbrido junta-se ao ambiente federado](hybrid-azuread-join-federated-domains.md)
