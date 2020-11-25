---
title: Identidade do dispositivo e virtualização do ambiente de trabalho - Azure Ative Directory
description: Saiba como as identidades dos dispositivos VDI e AZure AD podem ser usadas em conjunto
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: c30ad26f079e6353dc4763b9ae968c33882d8ab6
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029352"
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

Versões não persistentes utilizam uma coleção de desktops que os utilizadores podem aceder quando necessário. Estes ambientes de trabalho não persistentes são revertidos para o seu estado original, no caso do Windows atual<sup>1</sup> isto acontece quando uma máquina virtual passa por um processo de reposição de paragem/reinício/sistema operativo e no caso do Windows de nível 2 de baixo-<sup>2</sup> isto acontece quando um utilizador assina.

Tem havido um aumento nas implementações de VDI não persistentes, uma vez que o trabalho remoto continua a ser a nova norma. À medida que os clientes implementam VDI não persistente, é importante garantir que gere o dispositivo que pode ser causado devido ao registo frequente do dispositivo sem ter uma estratégia adequada para a gestão do ciclo de vida do dispositivo.

> [!IMPORTANT]
> A não gestão do dispositivo, pode levar a um aumento da pressão sobre o consumo de quota de inquilino e o risco potencial de interrupção do serviço, se ficar sem quota de inquilino. Deve seguir as orientações abaixo indicadas ao implementar ambientes VDI não persistentes para evitar esta situação.

Este artigo cobrirá a orientação da Microsoft aos administradores sobre o suporte à identidade do dispositivo e ao VDI. Para obter mais informações sobre a identidade do dispositivo, consulte o artigo [O que é uma identidade do dispositivo](overview.md).

## <a name="supported-scenarios"></a>Cenários suportados

Antes de configurar as identidades do dispositivo em Azure AD para o seu ambiente VDI, familiarize-se com os cenários suportados. O quadro abaixo ilustra quais os cenários de provisionamento que são apoiados. O provisionamento neste contexto implica que um administrador pode configurar as identidades do dispositivo em escala sem exigir qualquer interação do utilizador final.

| Tipo de identidade do dispositivo | Infraestrutura de identidade | Dispositivos Windows | Versão da plataforma VDI | Suportado |
| --- | --- | --- | --- | --- |
| associado ao Azure AD Híbrido | Federado<sup>3</sup> | Windows current e Windows down-level | Persistente | Sim |
|   |   | Corrente do Windows | Não Persistente | Sim<sup>5</sup> |
|   |   | Dispositivos Windows de nível inferior | Não Persistente | Sim<sup>6</sup> |
|   | Gerido<sup>4</sup> | Windows current e Windows down-level | Persistente | Sim |
|   |   | Corrente do Windows | Não Persistente | Não |
|   |   | Dispositivos Windows de nível inferior | Não Persistente | Sim<sup>6</sup> |
| Azure AD associado | Federados | Corrente do Windows | Persistente | Não |
|   |   |   | Não Persistente | Não |
|   | Gerido | Corrente do Windows | Persistente | Não |
|   |   |   | Não Persistente | Não |
| Azure AD registado | Federado/Gerido | Windows corrente/Windows para baixo nível | Persistente/Não Persistente | Não Aplicável |

<sup>1</sup> Os dispositivos **atuais do Windows** representam o Windows 10, o Windows Server 2016 v1803 ou superior e o Windows Server 2019.
<sup>2</sup> dispositivos **de nível de baixo do Windows** representam o Windows 7, Windows 8.1, Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2. Para obter informações sobre o Windows 7, consulte [o Suporte para o Windows 7 está a terminar](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support). Para obter informações de suporte no Windows Server 2008 R2, consulte [Prepare-se para o fim do suporte do Windows Server 2008](https://www.microsoft.com/cloud-platform/windows-server-2008).

<sup>3</sup> Um ambiente de infraestrutura de identidade **federada** representa um ambiente com um fornecedor de identidade, como a AD FS ou outro IDP de terceiros.

<sup>4</sup> Um ambiente de infraestrutura de identidade **gerido** representa um ambiente com Azure AD como o fornecedor de identidade implantado com [sincronização de haxixe de palavra-passe (PHS)](../hybrid/whatis-phs.md) ou [autenticação pass-through (PTA)](../hybrid/how-to-connect-pta.md) com [um único sinal sem costura.](../hybrid/how-to-connect-sso.md)

<sup>5</sup> **O suporte à não persistência para a corrente do Windows** requer consideração adicional, tal como documentado abaixo na secção de orientação. Este cenário requer o Windows 10 1803, o Windows Server 2019 ou o Windows Server (canal semi-anual) a partir da versão 1803

<sup>6</sup> **O suporte à não persistência do Windows para o nível de baixo** requer consideração adicional, tal como documentado abaixo na secção de orientação.


## <a name="microsofts-guidance"></a>Orientação da Microsoft

Os administradores devem fazer referência aos seguintes artigos, com base na sua infraestrutura de identidade, para aprender a configurar a ad AD híbrida.

- [Configure híbrido Azure Ative Directy junta-se ao ambiente federado](hybrid-azuread-join-federated-domains.md)
- [Configure híbrido Azure Ative Directy junta-se para ambiente gerido](hybrid-azuread-join-managed-domains.md)

Ao implementar VDI não persistente, a Microsoft recomenda que os administradores de TI implementem as orientações abaixo. Se não o fizer, o seu diretório terá muitos dispositivos híbridos azure adá de azure estragados que foram registados a partir da sua plataforma VDI não persistente, resultando numa pressão acrescida sobre a sua quota de inquilino e risco de interrupção de serviço devido ao escorrimento da quota de inquilino.

- Se estiver a contar com a Ferramenta de Preparação do Sistema (sysprep.exe) e se estiver a utilizar uma imagem pré-Windows 10 1809 para instalação, certifique-se de que a imagem não é de um dispositivo que já está registado no AD Azure como a AD híbrido Azure aderiu.
- Se estiver a contar com uma foto de Máquina Virtual (VM) para criar VMs adicionais, certifique-se de que o instantâneo não é de um VM que já está registado no AZure AD como ad AD Híbrido Azure.
- Crie e utilize um prefixo para o nome de exibição (por exemplo, NPVDI-) do computador que indique o ambiente de trabalho como baseado em VDI não persistente.
- Para o windows de nível baixo:
   - Implementar **o auto-trabalho colocação do comando /deixar** o comando como parte do script logoff. Este comando deve ser acionado no contexto do utilizador e deve ser executado antes de o utilizador ter iniciado completamente o seu registo e enquanto ainda existir conectividade de rede.
- Para a corrente do Windows num ambiente federado (por exemplo, FS AD):
   - Implementar **dsregcmd /juntar-se** como parte da sequência de arranque VM.
   - **NÃO** execute o dsregcmd /leave como parte do processo de paragem/reinício de VM.
- Definir e implementar o processo de [gestão de dispositivos antigos.](manage-stale-devices.md)
   - Uma vez que tenha uma estratégia para identificar os dispositivos de ad AD híbrido não persistente (por exemplo, utilizando o prefixo do nome do ecrã do computador), deverá ser mais agressivo na limpeza destes dispositivos para garantir que o seu diretório não seja consumido com muitos dispositivos antigos.
   - Para implementações VDI não persistentes na corrente do Windows e no nível de baixo nível, deve eliminar dispositivos que tenham **aproximadamente o calibreTimestamp** de aproximadamente 15 dias.
 
## <a name="next-steps"></a>Passos seguintes

[Configurar o Azure Ative Directy híbrido junta-se ao ambiente federado](hybrid-azuread-join-federated-domains.md)
