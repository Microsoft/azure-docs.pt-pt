---
title: Instale o agente de provisionamento de nuvem Azure AD Connect utilizando o powershell
description: Saiba como instalar o agente de provisionamento de nuvem Azure AD Connect utilizando cmdlets de powershell.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c20cfb96b5cd6e1d05e332fa7157fe6e0cde8656
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614187"
---
# <a name="install-the-azure-ad-connect-provisioning-agent-using-powershell-cmdlets"></a>Instale o agente de provisionamento Azure AD Connect utilizando cmdlets powershell 
O documento que se segue irá orientar-lhe como instalar o agente de provisionamento Azure AD Connect utilizando cmdlets PowerShell.
 

## <a name="prerequisite"></a>Pré-requisito: 


>[!IMPORTANT]
>As seguintes instruções de instalação pressupõem que todos os [Pré-requisitos foram cumpridos.](how-to-prerequisites.md)
>
> O servidor do windows necessita de ter o TLS 1.2 ativado antes de instalar o agente de provisionamento AZure AD Connect utilizando cmdlets de powershell. Para ativar o TLS 1.2 pode utilizar os passos [aqui](how-to-prerequisites.md#tls-requirements)encontrados .

 

## <a name="install-the-azure-ad-connect-provisioning-agent-using-powershell-cmdlets"></a>Instale o agente de provisionamento Azure AD Connect utilizando cmdlets powershell 


 1. Inscreva-se no portal Azure e, em seguida, vá para **o Diretório Ativo Azure**.
 2. No menu esquerdo, selecione **Azure AD Connect**.
 3. **Selecione Gerir o provisionamento (pré-visualização)**  >  **Rever todos os agentes**.
 4. Descarregue o agente de provisionamento Azure AD Connect do portal Azure para um local.  

   ![Baixar agente no local](media/how-to-install/install-9.png)</br>
 5. Para efeitos destas instruções, o agente foi descarregado para a seguinte pasta: pasta "C:\ProvisioningSetup". 
 6. Instalar ProvisioningAgent em modo silencioso

   ```
   $installerProcess = Start-Process c:\temp\AADConnectProvisioningAgent.Installer.exe /quiet -NoNewWindow -PassThru 
   $installerProcess.WaitForExit()  
   ```
 7. Módulo PS do Agente de Provisionamento de Importação 

   ```
   Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.Powershell.dll" 
   ```
 8. Ligue-se ao AzureAD usando credenciais de administrador global, pode personalizar esta secção para obter senha de uma loja segura. 

   ```
   $globalAdminPassword = ConvertTo-SecureString -String "Global admin password" -AsPlainText -Force 

   $globalAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("GlobalAdmin@contoso.onmicrosoft.com", $globalAdminPassword) 
   ```

   Connect-AADCloudSyncAzureAD -$globalAdminCreds credenciais 

 9. Adicione a conta gMSA, forneça credenciais do administrador de domínio para criar conta gMSA padrão 
 
   ```
   $domainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 

   $domainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $domainAdminPassword) 

   Add-AADCloudSyncGMSA -Credential $domainAdminCreds 
   ```
 10. Ou use o cmdlet acima como abaixo para fornecer uma conta gMSA pré-criada 

 
   ```
   Add-AADCloudSyncGMSA -CustomGMSAName preCreatedGMSAName$ 
   ```
 11. Adicionar domínio 

   ```
   $contosoDomainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 

   $contosoDomainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $contosoDomainAdminPassword) 

   Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds 
   ```
 12. Ou use o cmdlet acima como abaixo para configurar controladores de domínio preferidos 

   ```
   $preferredDCs = @("PreferredDC1", "PreferredDC2", "PreferredDC3") 

   Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds -PreferredDomainControllers $preferredDCs 
   ```
 13. Repita o passo anterior para adicionar mais domínios, por favor forneça os nomes das contas e os nomes de domínio dos respetivos domínios 
 14. Reinicie o serviço 
   ```
   Restart-Service -Name AADConnectProvisioningAgent  
   ```
 15.  Vá ao portal azul para criar a configuração de sincronização de nuvem.

## <a name="provisioning-agent-gmsa-powershell-cmdlets"></a>Comandantes gMSA PowerShell do agente de provisionamento
Agora que instalou o agente, pode aplicar mais permissões granulares ao gMSA.  Consulte [o agente de provisão de nuvem GMSA PowerShell](how-to-gmsa-cmdlets.md) para obter informações e instruções passo a passo sobre a configuração das permissões.

## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [Azure AD Connect agente de provisão de nuvem gMSA PowerShell cmdlets](how-to-gmsa-cmdlets.md)
- [O que é a sincronização de nuvem AZure AD Connect?](what-is-cloud-sync.md)