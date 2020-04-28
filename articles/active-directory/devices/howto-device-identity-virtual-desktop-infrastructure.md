---
title: Identidade do dispositivo e virtualização do ambiente de trabalho - Diretório Ativo Azure
description: Saiba como as identidades do dispositivo AD VDI e Azure podem ser usadas em conjunto
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74900378"
---
# <a name="device-identity-and-desktop-virtualization"></a>Identidade do dispositivo e virtualização do ambiente de trabalho

Os administradores geralmente implementam plataformas de infraestrutura de ambiente de trabalho virtual (VDI) que hospedam sistemas operativos Windows nas suas organizações. Os administradores implantam o VDI para:

- Gestão de agilização.
- Reduzir os custos através da consolidação e centralização dos recursos.
- Proporcionar mobilidade aos utilizadores finais e a liberdade de aceder a ambientes de trabalho virtuais a qualquer momento, a partir de qualquer lugar, em qualquer dispositivo.

Existem dois tipos primários de desktops virtuais:

- Persistente
- Não persistente

As versões persistentes utilizam uma imagem única para cada utilizador ou um conjunto de utilizadores. Estes desktops exclusivos podem ser personalizados e guardados para uso futuro. 

As versões não persistentes utilizam uma coleção de desktops a que os utilizadores podem aceder de forma necessária. Estes ambientes de trabalho não persistentes são revertidos ao seu estado original após a saída do utilizador.

Este artigo cobrirá a orientação da Microsoft aos administradores sobre suporte à identidade do dispositivo e VDI. Para mais informações sobre a identidade do dispositivo, consulte o artigo [O que é uma identidade de dispositivo.](overview.md)

## <a name="supported-scenarios"></a>Cenários suportados

Antes de configurar as identidades do dispositivo em Azure AD para o seu ambiente VDI, familiarize-se com os cenários suportados. O quadro a seguir ilustra quais os cenários de fornecimento que são apoiados. O fornecimento neste contexto implica que um administrador pode configurar as identidades do dispositivo em escala sem necessitar de qualquer interação entre utilizadores finais.

| Tipo de identidade do dispositivo | Infraestrutura de identidade | Dispositivos Windows | Versão da plataforma VDI | Suportado |
| --- | --- | --- | --- | --- |
| associado ao Azure AD Híbrido | Federado* | Windows current*** e Windows de nível baixo**** | Persistente | Sim |
|   |   | Atual do Windows | Não Persistente | Não |
|   |   | Dispositivos Windows de nível inferior | Não Persistente | Sim |
|   | Gerido** | Windows current e Windows de nível baixo | Persistente | Sim |
|   |   | Atual do Windows | Não Persistente | Não |
|   |   | Dispositivos Windows de nível inferior | Não Persistente | Sim |
| Azure AD associado | Federados | Atual do Windows | Persistente | Não |
|   |   |   | Não Persistente | Não |
|   | Geridos | Atual do Windows | Persistente | Não |
|   |   |   | Não Persistente | Não |
| Azure AD registado | Federados | Atual do Windows | Persistente | Não |
|   |   |   | Não Persistente | Não |
|   | Geridos | Atual do Windows | Persistente | Não |
|   |   |   | Não Persistente | Não |

\*Um ambiente de infraestrutura de identidade **federado** representa um ambiente com um fornecedor de identidade, como a AD FS ou outro IDP de terceiros.

\*\*Um ambiente de infraestrutura de identidade **gerida** representa um ambiente com a AD Azure como o fornecedor de identidade implantado com sincronização de [hash (PHS)](../hybrid/whatis-phs.md) ou [autenticação pass-through (PTA)](../hybrid/how-to-connect-pta.md) com [um único sinal único sem emenda](../hybrid/how-to-connect-sso.md).

\*\*\*Os dispositivos **atuais do Windows** representam o Windows 10, Windows Server 2016 e Windows Server 2019.

\*\*\*\*Os dispositivos **de nível inferior do Windows** representam o Windows 7, Windows 8.1, Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2. Para obter informações sobre o suporte no Windows 7, consulte o [suporte para o Windows 7 .](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support) Para obter informações sobre o Windows Server 2008 R2, consulte [Prepare-se para o suporte do Windows Server 2008](https://www.microsoft.com/cloud-platform/windows-server-2008).

## <a name="microsofts-guidance"></a>Orientação da Microsoft

Os administradores devem fazer referência aos seguintes artigos, com base na sua infraestrutura de identidade, para aprender a configurar a adesão híbrida azure AD.

- [Configure hybrid Azure Ative Directory junta-se para ambiente federado](hybrid-azuread-join-federated-domains.md)
- [Configure hybrid Azure Ative Directory junta-se para ambiente gerido](hybrid-azuread-join-managed-domains.md)

Se estiver a contar com a Ferramenta de Preparação do Sistema (sysprep.exe) e se estiver a utilizar uma imagem pré-Windows 10 1809 para instalação, certifique-se de que a imagem não é de um dispositivo que já está registado com a Azure AD como a ad ad azure híbrida.

Se estiver a contar com um instantâneo da Máquina Virtual (VM) para criar VMs adicionais, certifique-se de que o instantâneo não é de um VM que já está registado com a AD Azure Como AD Hybrid Azure.

Ao implementar vDI não persistente, os administradores de TI devem estar atentos à gestão de dispositivos velhos em Azure AD. A Microsoft recomenda que os administradores de TI implementem as orientações abaixo. Se não o fizer, o seu diretório terá muitos dispositivos híbridos azure ad que foram registados a partir da sua plataforma VDI não persistente.

- Crie e utilize um prefixo para o nome de exibição do computador que indique o ambiente de trabalho como baseado em VDI.
- Implemente o seguinte comando como parte do script de logoff. Este comando irá desencadear uma chamada de melhor esforço para a AD Azure para apagar o dispositivo.
   - Para dispositivos de nível inferior windows – autoworkplace.exe /leave
- Defina e implemente o processo de gestão de [dispositivos velhos.](manage-stale-devices.md)
   - Uma vez que tenha uma estratégia para identificar os seus dispositivos ad ad sinuosos não persistentes Hybrid Azure, pode ser mais agressivo na limpeza destes dispositivos para garantir que o seu diretório não seja consumido com muitos dispositivos velhos.
 
## <a name="next-steps"></a>Passos seguintes

[Configurar diretório ativo híbrido Azure junta-se ao ambiente federado](hybrid-azuread-join-federated-domains.md)
