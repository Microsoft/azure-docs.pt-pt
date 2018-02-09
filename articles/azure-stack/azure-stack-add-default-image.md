---
title: Adicionar a imagem VM predefinida para a pilha do Azure Marketplace | Microsoft Docs
description: Adicione a imagem predefinida do Windows Server 2016 VM para a pilha do Azure Marketplace.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 2849E53F-3D58-48A5-8007-3238FC39F630
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 1/23/2018
ms.author: mabrigg
ms.openlocfilehash: be4a61f185435238db68e4dc43c323a30a754f03
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2018
---
# <a name="add-the-windows-server-2016-vm-image-to-the-azure-stack-marketplace"></a>Adicionar a imagem de VM do Windows Server 2016 a pilha do Azure Marketplace

Por predefinição, não existem imagens da máquina virtual estão disponíveis no mercado de pilha do Azure. Um operador de pilha do Azure tem de adicionar uma imagem para o mercado para os utilizadores a aceder. Pode adicionar a imagem do Windows Server 2016 a pilha do Azure Marketplace utilizando um dos seguintes métodos:

* [Transferir a imagem do Azure Marketplace](#add-the-image-by-downloading-it-from-the-azure-marketplace). Utilize esta opção se estão a funcionar num cenário ligado e que registou a instância de pilha do Azure com o Azure.

* [Adicionar a imagem com o PowerShell](#add-the-image-by-using-powershell). Utilize esta opção se tiver implementado pilha do Azure num cenário de desligado, ou em situações com conectividade limitada.

## <a name="add-the-image-by-downloading-it-from-the-azure-marketplace"></a>Adicionar a imagem, transferindo-a no Azure Marketplace

1. Implementar a pilha do Azure e, em seguida, inicie sessão no seu Kit de desenvolvimento de pilha do Azure.

2. Selecione **mais serviços** > **Marketplace gestão** > **adicionar a partir do Azure**. 

3. Localizar ou procurar o **Datacenter do Windows Server 2016** imagem e, em seguida, selecione **transferir**.

   ![Transferir a imagem a partir do Azure](media/azure-stack-add-default-image/download-image.png)

Quando a transferência estiver concluída, a imagem está disponível em **Marketplace gestão**. A imagem também está disponível em **computação** e está disponível para criar novas máquinas virtuais.

## <a name="add-the-image-by-using-powershell"></a>Adicionar a imagem com o PowerShell

### <a name="prerequisites"></a>Pré-requisitos 

Execute os seguintes pré-requisitos a partir de [kit de desenvolvimento](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) ou a partir de um baseados em Windows clientes externos, se estiver [ligado através de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

1. Instalar [módulos do Azure pilha compatível com o Azure PowerShell](azure-stack-powershell-install.md).  

2. Transferir o [ferramentas necessárias para trabalhar com a pilha de Azure](azure-stack-powershell-download.md).  

3. Na página de avaliações de servidor do Windows, [transferir a avaliação do Windows Server 2016](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016). Quando lhe for pedido, selecione a versão do ficheiro ISO da transferência. Registe o caminho para a localização de transferência, o que é utilizado mais tarde nos passos descritos neste artigo. Este passo necessita de conectividade à internet.  

### <a name="add-the-image-to-the-azure-stack-marketplace"></a>Adicionar a imagem para a pilha do Azure Marketplace
   
1. Importe os módulos do Azure pilha ligar e ComputeAdmin utilizando os seguintes comandos:

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.   
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   ```

2. Inicie sessão no seu ambiente de pilha do Azure. Execute um dos seguintes scripts, dependendo se implementou o ambiente de pilha do Azure utilizando o Azure Active Directory (Azure AD) ou os serviços de Federação do Active Directory (AD FS). (Substitui o Azure AD `tenantName`, `GraphAudience` ponto final, e `ArmEndpoint` valores para refletir a configuração do seu ambiente.)  

   * **Azure Active Directory**. Utilize o seguinte cmdlet:

    ```PowerShell
    # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Resource Manager endpoint for your environment>"

    # For Azure Stack Development Kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
    $GraphAudience = "<GraphAuidence endpoint for your environment>"
    
    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint $ArmEndpoint

    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience $GraphAudience

    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
      -EnvironmentName AzureStackAdmin

    Login-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID 
    ```

   * **Serviços de Federação do Active Directory**. Utilize o seguinte cmdlet:
    
    ```PowerShell
    # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Resource Manager endpoint for your environment>"

    # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
    $GraphAudience = "<GraphAuidence endpoint for your environment>"

    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint $ArmEndpoint

    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience $GraphAudience `
      -EnableAdfsAuthentication:$true

    $TenantID = Get-AzsDirectoryTenantId `
     -ADFS `
     -EnvironmentName "AzureStackAdmin" 

    Login-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID 
    ```
   
3. Adicione a imagem do Windows Server 2016 a pilha do Azure Marketplace. (Substituir *fully_qualified_path_to_ISO* com o caminho para o ficheiro do Windows Server 2016 ISO que transferiu.)

    ```PowerShell
    $ISOPath = "<fully_qualified_path_to_ISO>"

    # Add a Windows Server 2016 Evaluation VM image.
    New-AzsServer2016VMImage `
      -ISOPath $ISOPath

    ```

Para garantir que a imagem de VM do Windows Server 2016 tem a atualização cumulativa mais recente, inclua o `IncludeLatestCU` parâmetro ao executar o `New-AzsServer2016VMImage` cmdlet. Para obter informações sobre parâmetros permitidos para o `New-AzsServer2016VMImage` cmdlet, consulte [parâmetros](#parameters). Demora aproximadamente uma hora para publicar a imagem para a pilha do Azure Marketplace. 

## <a name="parameters-for-new-azsserver2016vmimage"></a>Parâmetros para o novo AzsServer2016VMImage

### <a name="new-azsserver2016vmimage"></a>New-AzsServer2016VMImage 

Cria e carrega a imagem completa ou de um novo 2016 núcleo de servidor e cria um item do marketplace para o mesmo.

| Parâmetros | Necessário | Exemplo | Descrição |
|-----|-----|------|---- |
|ISOPath|Sim| N:\ISO\en_windows_16_x64_dvd | O caminho completamente qualificado para o 2016 ISO transferido do Windows Server.|
|Net35|Não| Verdadeiro | Instala o tempo de execução de .NET 3.5 a imagem do Windows Server 2016. Por predefinição, este valor é definido como **verdadeiro**.|
|Versão|Não| Completo |  Especifica **Core**, **completa**, ou **ambos** imagens do Windows Server 2016. Por predefinição, este valor é definido como **completa**.|
|VHDSizeInMB|Não| 40,960 | Define o tamanho (em MB) da imagem VHD ser adicionado ao seu ambiente de pilha do Azure. Por predefinição, este valor é definido como 40,960 MB.|
|CreateGalleryItem|Não| Verdadeiro | Especifica se deve ser criado um item do Marketplace para a imagem do Windows Server 2016. Por predefinição, este valor é definido como **verdadeiro**.|
|localização |Não | D:\ | Especifica a localização para o qual a imagem do Windows Server 2016 deve ser publicada.|
|IncludeLatestCU|Não| Falso | Aplica-se a atualização cumulativa mais recente do Windows Server 2016 para o novo VHD. Verifique o script para se certificar de que aponta para a atualização mais recente ou utilize uma das opções de dois. |
|CUUri |Não | https://yourupdateserver/winservupdate2016 | Define o Windows Server 2016 a atualização cumulativa para executar a partir de um URI específico. |
|CUPath |Não | C:\winservupdate2016 | Define o Windows Server 2016 a atualização cumulativa para ser executada a partir de um caminho local. Esta opção é útil se tiver implementado a instância de pilha do Azure num ambiente desligado.|

## <a name="next-steps"></a>Passos Seguintes

[Aprovisionar uma máquina virtual](azure-stack-provision-vm.md)
