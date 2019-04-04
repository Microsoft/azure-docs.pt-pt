---
title: Ligar ao Azure Stack | Documentos da Microsoft
description: Saiba como ligar para o ASDK.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: knithinc
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 31025582516198bdfe9da9312bae33852986a423
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884861"
---
# <a name="connect-to-the-asdk"></a>Ligar para o ASDK

Para gerir os recursos, primeiro tem de ligar para o Azure Stack Development Kit (ASDK). Neste artigo, descrevemos os passos que efetuar para ligar para o ASDK com as seguintes opções de ligação:

* [Conexão de área de trabalho remoto (RDP)](#connect-with-rdp). Quando se liga ao utilizar a ligação de ambiente de trabalho remota, um único utilizador pode ligar-se rapidamente para o kit de desenvolvimento.
* [Rede privada virtual (VPN)](#connect-with-vpn). Quando se liga através de uma VPN, vários usuários podem ligar ao mesmo tempo para os portais do Azure Stack de clientes fora da infraestrutura do Azure Stack. Uma ligação VPN requer alguma configuração.

<a name="connect-with-rdp"></a>
## <a name="connect-to-azure-stack-using-rdp"></a>Ligar ao Azure Stack através de RDP

Um único utilizador em simultâneo pode gerir os recursos no portal de administração do Azure Stack ou no portal de utilizador por meio da conexão de área de trabalho remoto diretamente a partir do computador anfitrião ASDK. 

> [!TIP]
> Esta opção também lhe permite utilizar o RDP novamente enquanto tem sessão iniciada no computador anfitrião ASDK para iniciar sessão em máquinas virtuais criadas no computador anfitrião ASDK. 

1. Abrir a ligação de ambiente de trabalho remoto (mstc.exe) e ligue-se para o desenvolvimento kit computador endereço IP do anfitrião utilizando uma conta autorizada a iniciar sessão remotamente no computador anfitrião ASDK. Por predefinição, **AzureStack\AzureStackAdmin** tem permissões para remoto no computador anfitrião ASDK.  

2. No computador anfitrião do kit de desenvolvimento, abra o Gestor de servidor (ServerManager.exe). Selecione **servidor Local**, desativar **configuração de segurança avançada do IE**e feche o Gestor de servidor.

3. Inicie sessão no portal de administração como **AzureStack\CloudAdmin** ou utilizar outras credenciais de operador do Azure Stack. É o endereço de portal de administração de ASDK [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).

4. Inicie sessão no portal de utilizador, como **AzureStack\CloudAdmin** ou utilizar outras credenciais de utilizador do Azure Stack. É o endereço de portal de utilizador ASDK [ https://portal.local.azurestack.external ](https://portal.local.azurestack.external).

> [!NOTE]
> Para obter mais informações sobre quando utilizar a conta, consulte [Noções básicas de administração de ASDK](asdk-admin-basics.md#what-account-should-i-use).

<a name="connect-with-vpn"></a>
## <a name="connect-to-azure-stack-using-vpn"></a>Ligar ao Azure Stack através de VPN

Pode estabelecer um ligação de VPN para um computador de anfitrião ASDK para acessar os portais do Azure Stack e as ferramentas instaladas localmente, como o Visual Studio e do PowerShell de túnel dividido. Vários utilizadores através de ligações de VPN, podem ligar ao mesmo tempo para recursos do Azure Stack hospedado pela ASDK.

Conectividade VPN é suportada para ambas do Azure AD e Active Directory implementações de serviços de Federação do Directory (AD FS).

> [!NOTE]
> Uma ligação VPN *não* fornecer conectividade a VMs do Azure Stack. Não será capaz de fazer o RDP para VMs do Azure Stack enquanto estiver ligado através de VPN.

### <a name="prerequisites"></a>Pré-requisitos
Antes de configurar uma ligação VPN para o ASDK, certifique-se de que cumpriu os pré-requisitos seguintes.

- Instale [do Azure Stack compatível com o Azure PowerShell](asdk-post-deploy.md#install-azure-stack-powershell) no seu computador local.  
- No desenvolvimento do [Azure Stack Kit, deve ser o blobEndpoint](asdk-post-deploy.md#download-the-azure-stack-tools) .

### <a name="set-up-vpn-connectivity"></a>Configurar a conectividade VPN

Para criar uma ligação VPN para o ASDK, abra o PowerShell como administrador no seu computador local com base em Windows. Em seguida, execute o seguinte script (o endereço IP e a palavra-passe os valores para o seu ambiente de atualização):

```powershell
# Change directories to the default Azure Stack tools directory
cd C:\AzureStack-Tools-master

# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the development kit host computer’s IP address as the ASDK certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user.
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Se o programa de configuração for concluída com êxito, **azurestack** aparece na sua lista de ligações VPN.

![Ligações de rede](media/asdk-connect/vpn.png)  

### <a name="connect-to-azure-stack"></a>Ligar ao Azure Stack

  Ligar à instância do Azure Stack através de um dos seguintes métodos:  

  * Utilize o `Connect-AzsVpn` comando:
      
    ```powershell
    Connect-AzsVpn `
      -Password $Password
    ```

  * No seu computador local, selecione **definições de rede** > **VPN** > **azurestack** > **ligar**. No prompt de início de sessão, introduza o nome de utilizador (**AzureStack\AzureStackAdmin**) e a palavra-passe.

Na primeira vez que se liga, será solicitado a instalar o certificado de raiz do Azure Stack do **AzureStackCertificateAuthority** no arquivo de certificados do seu computador local. Este passo adiciona a autoridade de certificação (AC) de ASDK à lista de anfitriões fidedignos. Clique em **Sim** para instalar o certificado.

![Certificado de raiz](media/asdk-connect/cert.png)  
  
  > [!IMPORTANT]
  > Linha de comandos da botão poderá estar ocultada por janela do PowerShell ou outras aplicações.

### <a name="test-vpn-connectivity"></a>Testar a conectividade VPN

Para testar a ligação do portal, abra um browser e, em seguida, vá para o portal de utilizador (https://portal.local.azurestack.external/) ou no portal de administração (https://adminportal.local.azurestack.external/). 

Inicie sessão com as credenciais de subscrição adequada para criar e gerir os recursos.  

## <a name="next-steps"></a>Passos Seguintes

[Resolução de problemas](asdk-troubleshooting.md)
