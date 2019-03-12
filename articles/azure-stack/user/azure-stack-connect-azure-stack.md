---
title: Ligar ao Azure Stack | Documentos da Microsoft
description: Saiba como ligar o Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 77cfade382d2c2533b803412157a1d5268e89983
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57764324"
---
# <a name="connect-to-azure-stack"></a>Ligar ao Azure Stack

Para gerir os recursos, tem de ligar para o Azure Stack Development Kit. Este artigo descreve os passos necessários para ligar para o kit de desenvolvimento. Pode usar qualquer uma das seguintes opções de ligação:

* Ambiente de trabalho remoto: permite que um único utilizador em simultâneo, ligue-se rapidamente do kit de desenvolvimento.
* Rede privada virtual (VPN): permite que vários utilizadores em simultâneo, ligar a partir de clientes fora da infraestrutura do Azure Stack (requer a configuração).

## <a name="connect-to-azure-stack-with-remote-desktop"></a>Ligar ao Azure Stack com o ambiente de trabalho remoto
Com uma ligação de ambiente de trabalho remoto, um único utilizador em simultâneo pode trabalhar com o portal para gerir os recursos.

1. Abra uma conexão de área de trabalho remoto e ligue-se para o kit de desenvolvimento. Introduza **AzureStack\AzureStackAdmin** como o nome de utilizador e a palavra-passe administrativa que indicou durante a configuração do Azure Stack.  

2. A partir do computador no kit de desenvolvimento, abra o Gestor de servidor, clique em **servidor Local**, desativar a segurança avançada do Internet Explorer e, em seguida, feche o Gestor de servidor.

3. Para abrir o portal, navegue para (https://portal.local.azurestack.external/) e inicie sessão com credenciais de utilizador.


## <a name="connect-to-azure-stack-with-vpn"></a>Ligar ao Azure Stack com VPN

Pode estabelecer um ligação de rede privada Virtual (VPN) para um Kit de desenvolvimento do Azure Stack de túnel dividido. Através da ligação de VPN, pode aceder ao portal de administrador, o portal de utilizador e instalado localmente ferramentas como o Visual Studio e o PowerShell para gerir os recursos do Azure Stack. A conectividade VPN é suportada em ambos os Directory(AAD) Active Directory do Azure e o Active Directory Federation Services(AD FS) com base em implementações. Ligações VPN ativar vários clientes ligar ao Azure Stack, ao mesmo tempo. 

> [!NOTE] 
> Esta ligação VPN não fornecer conectividade a VMs de infraestrutura do Azure Stack. 

### <a name="prerequisites"></a>Pré-requisitos

* Instale [compatíveis do Azure PowerShell do Azure Stack](azure-stack-powershell-install.md) no seu computador local.  
* No desenvolvimento do [Azure Stack Kit, deve ser o blobEndpoint](azure-stack-powershell-download.md) . 

### <a name="configure-vpn-connectivity"></a>Configurar a conectividade da VPN

Para criar uma ligação VPN para o kit de desenvolvimento, abra uma sessão elevada do PowerShell do computador baseado em Windows local e execute o seguinte script (Certifique-se atualizar os valores de endereço e a palavra-passe IP para o seu ambiente):

```PowerShell 
# Configure winrm if it's not already configured
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer’s host IP address & certificate authority (CA) to the list of trusted hosts. Make sure to update the IP address and password values for your environment. 

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<Administrator password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Se a configuração for concluída com êxito, deverá ver **azurestack** na sua lista de ligações VPN.

![Ligações de rede](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Ligar ao Azure Stack

Ligar à instância do Azure Stack, utilizando qualquer um dos seguintes dois métodos:  

* Ao utilizar o `Connect-AzsVpn ` comando: 
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Quando lhe for pedido, o anfitrião do Azure Stack de confiança e instalar o certificado a partir **AzureStackCertificateAuthority** no arquivo de certificados do seu computador local. (linha de comandos pode parecer, por trás da janela de sessão do PowerShell). 

* Abra o seu computador local **definições de rede** > **VPN** > clique em **azurestack** > **ligar**. No prompt de início de sessão, introduza o nome de utilizador (AzureStack\AzureStackAdmin) e a palavra-passe.

### <a name="test-the-vpn-connectivity"></a>Testar a conectividade VPN

Para testar a ligação do portal, abra um browser e navegue para o portal de utilizador (https://portal.local.azurestack.external/), iniciar sessão e criar recursos.  

## <a name="next-steps"></a>Passos Seguintes



