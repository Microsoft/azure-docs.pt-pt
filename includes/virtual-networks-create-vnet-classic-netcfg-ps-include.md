---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: bda289e73b9a782cd56c0c94b8f53e8002b1ccf4
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59532500"
---
## <a name="how-to-create-a-virtual-network-using-a-network-config-file-from-powershell"></a>Como criar uma rede virtual com um ficheiro de configuração de rede do PowerShell
Para definir todas as redes virtuais disponíveis para uma subscrição, o Azure utiliza um arquivo xml. Pode transferir este ficheiro, editá-lo para modificar ou eliminar redes virtuais existentes e criar novas redes virtuais. Neste tutorial, saiba como transferir este ficheiro, conhecido como ficheiro de configuração (ou netcfg) de rede e editá-lo para criar uma nova rede virtual. Para saber mais sobre o ficheiro de configuração de rede, consulte a [esquema de configuração de rede virtual do Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

Para criar uma rede virtual com um ficheiro netcfg com o PowerShell, execute os seguintes passos:

1. Se nunca tiver utilizado o Azure PowerShell, conclua os passos a [como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) article, em seguida, inicie sessão no Azure e selecione a sua subscrição.
2. A partir da consola do Azure PowerShell, utilize o **Get-AzureVnetConfig** cmdlet para transferir o ficheiro de configuração de rede para um diretório no seu computador executando o seguinte comando: 
   
   ```powershell
   Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
   ```
   
   Resultado esperado:
  
      ```
      XMLConfiguration                                                                                                     
      ----------------                                                                                                     
      <?xml version="1.0" encoding="utf-8"?>...
      ```

3. Abra o ficheiro que guardou no passo 2, através de qualquer aplicação de editor de XML ou de texto e procure o  **\<VirtualNetworkSites >** elemento. Se tiver quaisquer redes já criados, a cada rede é apresentado como seu próprio  **\<VirtualNetworkSite >** elemento.
4. Para criar a rede virtual descrita neste cenário, adicione o seguinte XML abaixo a  **\<VirtualNetworkSites >** elemento:

   ```xml
         <?xml version="1.0" encoding="utf-8"?>
         <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
           <VirtualNetworkConfiguration>
             <VirtualNetworkSites>
                 <VirtualNetworkSite name="TestVNet" Location="East US">
                   <AddressSpace>
                     <AddressPrefix>192.168.0.0/16</AddressPrefix>
                   </AddressSpace>
                   <Subnets>
                     <Subnet name="FrontEnd">
                       <AddressPrefix>192.168.1.0/24</AddressPrefix>
                     </Subnet>
                     <Subnet name="BackEnd">
                       <AddressPrefix>192.168.2.0/24</AddressPrefix>
                     </Subnet>
                   </Subnets>
                 </VirtualNetworkSite>
             </VirtualNetworkSites>
           </VirtualNetworkConfiguration>
         </NetworkConfiguration>
   ```
   
5. Guarde o ficheiro de configuração de rede.
6. A partir da consola do Azure PowerShell, utilize o **Set-AzureVnetConfig** cmdlet para carregar o ficheiro de configuração de rede ao executar o seguinte comando: 
   
   ```powershell
   Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
   ```
   
   Devolveu saída:
   
      ```
      OperationDescription OperationId                          OperationStatus
      -------------------- -----------                          ---------------
      Set-AzureVNetConfig  <Id>                                 Succeeded 
      ```
   
   Se **OperationStatus** není *Succeeded* no resultado retornado, verificar novamente o arquivo xml para erros e completado passo 6.

7. A partir da consola do Azure PowerShell, utilize o **Get-AzureVnetSite** cmdlet para verificar se a nova rede foi adicionada ao executar o seguinte comando: 

   ```powershell
   Get-AzureVNetSite -VNetName TestVNet
   ```
   
   A saída (abreviada) devolvida inclui o seguinte texto:
  
      ```
      AddressSpacePrefixes : {192.168.0.0/16}
      Location             : Central US
      Name                 : TestVNet
      State                : Created
      Subnets              : {FrontEnd, BackEnd}
      OperationDescription : Get-AzureVNetSite
      OperationStatus      : Succeeded
      ```
