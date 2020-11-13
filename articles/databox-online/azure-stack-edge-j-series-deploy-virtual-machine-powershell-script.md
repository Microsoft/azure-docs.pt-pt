---
title: Implemente VMs no seu dispositivo GPU Azure Stack Edge Pro através do script Azure PowerShell
description: Descreve como criar e gerir máquinas virtuais (VMs) num dispositivo Azure Stack Edge Pro utilizando um script Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: cd877a91c7c082ce7845394f2636857f33b7940f
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578761"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-azure-powershell-script"></a>Implemente VMs no seu dispositivo GPU Azure Stack Edge Pro através do script Azure PowerShell

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Este tutorial descreve como criar e gerir um VM no seu dispositivo Azure Stack Edge Pro utilizando um script Azure PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a criar e gerir um VM no seu dispositivo Azure Stack Edge Pro utilizando este script, tem de se certificar de que completou os pré-requisitos listados nos seguintes passos:

### <a name="for-azure-stack-edge-pro-device-via-the-local-web-ui"></a>Para o dispositivo Azure Stack Edge Pro através da web UI local

1. Completou as definições de rede no seu dispositivo Azure Stack Edge Pro, conforme descrito no [dispositivo Step 1: Configure Azure Stack Edge Pro](azure-stack-edge-j-series-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device).

2. Permitiu uma interface de rede para o cálculo. Este IP de interface de rede é utilizado para criar um interruptor virtual para a implementação de VM. Os seguintes passos acompanham-no através do processo:

    1. Aceda às definições de **Compute**. Selecione a interface de rede que utilizará para criar um interruptor virtual.

        > [!IMPORTANT] 
        > Só é possível configurar uma porta para o cálculo.

    2. Ativar o cálculo na interface de rede. O Azure Stack Edge Pro cria e gere um interruptor virtual correspondente a essa interface de rede.

3. Criou e instalou todos os certificados no seu dispositivo Azure Stack Edge Pro e na loja de raiz fidedigna do seu cliente. Siga o procedimento descrito no [Passo 2: Criar e instalar certificados](azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates).

### <a name="for-your-windows-client"></a>Para o seu cliente Windows

1. Definiu o protocolo de internet virtual (VIP) dos serviços Azure na sua página **de Rede** na UI web local do dispositivo. Você precisa adicionar este VIP para:

    - O ficheiro do anfitrião no cliente, OU,
    - A configuração do servidor DNS
    
    > [!IMPORTANT]
    > Recomendamos que modifique a configuração do servidor DNS para resolução de nomes de ponto final.

    1. Inicie **o Notepad** como administrador (os privilégios de administrador são necessários para guardar o ficheiro) e, em seguida, abra o ficheiro **de anfitriões** localizado em `C:\Windows\System32\Drivers\etc` .
    
        ![Ficheiro de anfitriões do Windows Explorer](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file.png)
    
    2. Adicione as seguintes entradas no ficheiro dos **anfitriões** substituindo os valores adequados para o seu dispositivo:
    
        ```
        <Azure consistent services VIP> login.<appliance name>.<DNS domain>
        <Azure consistent services VIP> management.<appliance name>.<DNS domain>
        <Azure consistent services VIP> <storage name>.blob.<appliance name>.<DNS domain>
        ```
        Para a conta de armazenamento, pode fornecer um nome que pretende que o script use mais tarde para criar uma nova conta de armazenamento. O script não verifica se a conta de armazenamento existe.

    3. Utilize a seguinte imagem para referência. Guarde o ficheiro dos **anfitriões.**

        ![hospeda ficheiro no Bloco de Notas](media/azure-stack-edge-j-series-deploy-virtual-machine-cli-python/hosts-screenshot-boxed.png)

2. [Descarregue o script PowerShell](https://aka.ms/ase-vm-powershell) utilizado neste procedimento.

3. Certifique-se de que o seu cliente Windows está a executar o PowerShell 5.0 ou mais tarde.

4. Certifique-se de que o `Azure.Storage Module version 4.5.0` sistema está instalado. Pode obter este módulo na [PowerShell Gallery.](https://www.powershellgallery.com/packages/Azure.Storage/4.5.0) Para instalar este módulo, escreva:

    `Install-Module -Name Azure.Storage -RequiredVersion 4.5.0`

    Para verificar a versão do módulo instalado, escreva:

    `Get-InstalledModule -name Azure.Storage`

    Para desinstalar quaisquer outros módulos de versão, escreva:

    `Uninstall-Module -Name Azure.Storage`

5. [Faça o download do AzCopy 10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10#download-azcopy) para o seu cliente Windows. Tome nota deste local pois irá passá-lo como parâmetro enquanto executa o script.

6. Certifique-se de que o seu cliente Windows está a executar TLS 1.2 ou mais tarde.


## <a name="create-a-vm"></a>Criar uma VM

1. Execute o PowerShell como um administrador.
2. Vá à pasta onde descarregou o script no seu cliente.  
3. Utilize o seguinte comando para executar o script:
 
    `.\ArmPowershellClient.ps1 -VNetAddressSpace <AddressSpace> -NicPrivateIp <Private IP> -VHDPath <Path> -VHDFile <VHD File, with extension> -StorageAccountName <Name> -OS <Windows/Linux> -VMSize <Supported VM Size> -VMUserName <UserName to be used to login into VM> -VMPassword <Password for the VM login> --AzCopy10Path <Absolute Path>`

    Aqui estão os exemplos quando o script é executado para criar um VM Windows e um Linux VM.

    **Para um Windows VM:**

    `.\ArmPowershellClient.ps1 -VNetAddressSpace 5.5.0.0/16 -NicPrivateIp 5.5.168.73 -VHDPath \\intel01\d$\vm_vhds\AzureWindowsVMmode -VHDFile WindowsServer2016Datacenter.vhd -StorageAccountName teaaccount1 -OS Windows -VMSize Standard_D1_v2 -VMUserName Administrator -VMPassword Password1 -AzCopy10Path C:\azcopy10\azcopy.exe`

    **Para um Linux VM:**

    `.\ArmPowershellClient.ps1 -VNetAddressSpace 5.5.0.0/16 -NicPrivateIp 5.5.168.83 -VHDPath \\intel01\d$\vm_vhds\AzurestackLinux -VHDFile ubuntu13.vhd -StorageAccountName sa2 -OS Linux -VMSize Standard_D1_v2 -VMUserName Administrator -VMPassword Password1 -AzCopy10Path C:\azcopy10\azcopy.exe`
    
4. Para limpar os recursos que o script criou, utilize os seguintes comandos:
    
    ```powershell
    Get-AzureRmVM | Remove-AzureRmVM -Force
    Get-AzureRmNetworkInterface | Remove-AzureRmNetworkInterface -Force
    Get-AzureRmResource | Remove-AzureRmResource -f
    Get-AzureRmResourceGroup | Remove-AzureRmResourceGroup -f
    ```


## <a name="next-steps"></a>Passos seguintes

[Implementar VMs utilizando cmdlets Azure PowerShell](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md)
