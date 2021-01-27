---
title: Desative a PTA ao utilizar o Azure AD Connect "Não configurar" | Microsoft Docs
description: Este artigo descreve como desativar a PTA com a função Azure AD Connect "não configurar".
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26112b1e799cbde3145e7137c686b4b336db4bab
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919940"
---
# <a name="disable-pta-when-using-azure-ad-connect"></a>Desative a PTA ao utilizar o Azure AD Connect

Se estiver a utilizar a Autenticação Pass-through com Azure AD Connect e a tiver definida para **"Não configurar",** pode desativá-la. 

>[!NOTE]
>Se tiver PHS já habilitado, então a desativação da AP resultará na recaída do inquilino para PHS.

A desativação da AP pode ser feita utilizando os seguintes cmdlets. 

## <a name="prerequisites"></a>Pré-requisitos
São necessários os seguintes pré-requisitos:
- Qualquer máquina de janelas que tenha o agente PTA instalado. 
- O agente deve estar na versão 1.5.1742.0 ou mais tarde. 
- Uma conta de administrador global da Azure para executar os cmdlets PowerShell para desativar a AP.

>[!NOTE]
> Se o seu agente for mais velho, então pode não ter os cmdlets necessários para completar esta operação. Pode obter um novo agente do Azure Portal para instalá-lo em qualquer máquina de janelas e fornecer credenciais de administração. (A instalação do agente não afeta o estado de PTA na nuvem)

> [!IMPORTANT]
> Se estiver a usar a nuvem do Governo Azure, terá de passar no parâmetro ENVIRONMENTNAME com o seguinte valor. 
>
>| Nome do Ambiente | Cloud |
>| - | - |
>| AzureUSGovernment | US Gov|


## <a name="to-disable-pta"></a>Para desativar a PTA
A partir de uma sessão PowerShell, utilize o seguinte para desativar a AP:
1. PS C:\Ficheiros de programa\Microsoft Azure AD Connect Authentication Agent> `Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` ou `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` ou `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>Se não tem acesso a um agente.

Se não tiver uma máquina de agente, pode utilizar o comando seguinte para instalar um agente.

1. Descarregue o mais recente agente da Auth de portal.azure.com.
2. Instalar a funcionalidade: `.\AADConnectAuthAgentSetup.exe` ou `.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>Próximos passos

- [Início de sessão do utilizador com a Autenticação Pass-through do Azure Active Directory](how-to-connect-pta.md)
