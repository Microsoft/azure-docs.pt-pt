---
title: Desative a PTA ao utilizar a Ligação AD Azure "Não configurar" [ Não configurar' [ Não configurar' [ Não configurar' [ Não configurar' [ Não configurar' [ Não configurar' [ Não configura Microsoft Docs
description: Este artigo descreve como desativar a PTA com a função Azure AD Connect "não configurar".
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa1046dc64fed3edb6c9d04f76a96f488769ff42
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81726803"
---
# <a name="disable-pta-when-using-azure-ad-connect-do-not-configure"></a>Desative a PTA ao utilizar a Ligação AD Azure "Não configure"

Se estiver a utilizar a Autenticação Pass-through com o Azure AD Connect e tiver definido para "Não configurar", pode desativá-lo. A Desativação de PTA pode ser feita utilizando os seguintes cmdlets. 

## <a name="prerequisites"></a>Pré-requisitos
São necessários os seguintes pré-requisitos:
- Qualquer máquina de janelas que tenha o agente pta instalado. 
- O agente deve estar na versão 1.5.1742.0 ou mais tarde. 
- Uma conta de administrador global azure para executar os cmdlets PowerShell para desativar a PTA.

>[!NOTE]
> Se o seu agente for mais velho, poderá não ter os cmdlets necessários para completar esta operação. Pode obter um novo agente do Portal Azure uma instalação em qualquer máquina de janelas e fornecer credenciais de administração. (Instalar o agente não afeta o estado da PTA na nuvem)

> [!IMPORTANT]
> Se estiver a utilizar a nuvem do Governo Azure, terá de passar no parâmetro ENVIRONMENTNAME com o seguinte valor. 
>
>| Nome do Ambiente | Nuvem |
>| - | - |
>| AzureUSGovernment | US Gov|


## <a name="to-disable-pta"></a>Para desativar a PTA
A partir de uma sessão powerShell, utilize o seguinte para desativar a PTA:
1. PS C:\Program Files\Microsoft Azure AD Connect Agente de Autenticação>`Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` ou `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` ou `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>Se não tem acesso a um agente.

Se não tiver uma máquina de agente, pode utilizar o comando seguinte para instalar um agente.

1. Baixe o mais recente Agente Auth de portal.azure.com.
2. Instale a `.\AADConnectAuthAgentSetup.exe` funcionalidade: ou`.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>Passos seguintes

- [Início de sessão do utilizador com a Autenticação Pass-through do Azure Active Directory](how-to-connect-pta.md)