---
title: Erro do gestor de arranque do Windows - 0xC0000428 Estado De imagem Inválida Hash
titlesuffix: Azure Virtual Machines
description: Este artigo fornece medidas para resolver problemas em que uma imagem de pré-visualização foi usada e o período experimental expirou, o que impede o arranque de uma Máquina Virtual Azure (VM).
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 26c411f9-6c22-4f45-a445-c5781e547828
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/03/2020
ms.author: v-miegge
ms.openlocfilehash: e58e349d7b7385ec913986c39462c17deadcb61d
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91969607"
---
# <a name="windows-boot-manager-error---0xc0000428-status-invalid-image-hash"></a>Erro do gestor de arranque do Windows - 0xC0000428 Estado De imagem Inválida Hash

Este artigo fornece medidas para resolver problemas em que uma imagem de pré-visualização foi usada e o período experimental expirou, o que impede o arranque de uma Máquina Virtual Azure (VM).

## <a name="symptom"></a>Sintoma

Quando utilizar [diagnósticos boot](./boot-diagnostics.md) para visualizar a imagem do VM, verá que a imagem mostra o Gestor de Boot do Windows com a mensagem:

  `File: \windows\system32\boot\winload.exe`

  `Status: 0xc0000428`

  `Info: Windows cannot verify the digital signature for this file.`

  ![A Figura 1 apresenta o Windows Boot Manager com o estado "Ox0000428", e informações "O Windows não pode verificar a assinatura digital deste ficheiro".](./media/windows-boot-error-invalid-image-hash/1-cannot-verify-signature.png)

ou a mensagem:

  `File: \Windows\system32\winload.exe`

  `Status: 0xc0000428`

  `Info: The digital signature for this file couldn't be verified.`
    
  ![A Figura 2 apresenta o Windows Boot Manager com o estatuto "Ox0000428", e informações "A assinatura digital deste ficheiro não pôde ser verificada".](./media/windows-boot-error-invalid-image-hash/2-digital-signature-not-verified.png)

## <a name="cause"></a>Causa

A imagem que foi usada para construir o VM foi uma imagem de pré-visualização com uma data de validade em vez de uma imagem RTM (Release to Manufacturing). 

As imagens de pré-visualização têm um ciclo de vida designado e a imagem que vê é exibida quando passa a data de validade, o que significa que o julgamento da imagem acabou.

### <a name="example-of-preview-images"></a>Exemplo de imagens de pré-visualização

`a699494373c04fc0bc8f2bb1389d6106__Windows-Server-Technical-Preview-201505.01-en.us-127GB.vhd`

Não pode prolongar a data de expiração de uma imagem de visualização. Quando a pré-visualização expira, a VM deixa de arrancar.

- Dependendo do produto, o período experimental pode variar. Por exemplo, as imagens de pré-visualização do Windows têm um período experimental de 180 dias.

- No Azure, todas as imagens para o Windows que são versões de pré-visualização incluirão uma nota na sua descrição de que não se destinam à produção e que estão disponíveis para utilização apenas durante um período experimental especificado ou como um "lançamento de pré-visualização".

## <a name="solution"></a>Solução

Se a sua imagem for uma imagem de pré-visualização, não há forma de prolongar a data de validade da imagem utilizada, terá de [implementar um novo VM](../windows/quick-create-portal.md) utilizando uma imagem de não pré-visualização. Os passos abaixo irão ajudá-lo a identificar se utilizou uma imagem de pré-visualização, bem como fornecer recursos para ajudá-lo a transferir dados deste VM para um novo VM. Se identificou positivamente a imagem como uma imagem de pré-visualização, a imagem não é recuperável, uma vez que está agora expirada.

Dependendo da sua preferência, pode utilizar a Azure PowerShell ou a Azure CLI para consultar a sua imagem para determinar se se trata de uma imagem de pré-visualização. Pode utilizar estes comandos para confirmar que a imagem é uma imagem de pré-visualização.

### <a name="query-using-azure-powershell"></a>Consulta usando Azure PowerShell

1. Abra a aplicação Windows PowerShell.
1. Execute os seguintes comandos:

   ```powershell
   $locName = "<LOCATION>" 
   $pubName = "<PUBLISHER NAME>" 
   $offerName = "<OFFER NAME>" 
   $skuName= "<YEAR WHEN THIS IMAGE WAS RELEASED>"
   Get-AzVMImagePublisher -Location $locName | Select $pubName
   Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
   Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
   Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName | Select Version
   ```

- Nos comandos anteriores, `<LOCATION>` `<PUBLISHER NAME>` substitua, e pela `<OFFER NAME>` `<YEAR WHEN THIS IMAGE WAS RELEASED>` informação indicada. Remova também os símbolos "<" e ">".

  Veja o seguinte exemplo:

  ```powershell
  $locName = "West US" 
  $pubName = "MicrosoftWindowsServer" 
  $offerName = "WindowsServer" 
  $skuName= "2016-Datacenter"
  Get-AzVMImagePublisher -Location $locName | Select $pubName
  Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
  Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
  Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName | Select Version
  ```

### <a name="query-using-the-azure-cli"></a>Consulta utilizando o Azure CLI

1. Se ainda não o fez, terá de [instalar o Azure CLI](/cli/azure/install-azure-cli).
1. Uma vez descarregado, use o Comando Prompt ou o PowerShell para introduzir o `az login` comando e, em seguida, inscreva-se com as credenciais da sua conta.
1. Uma vez iniciado, insira os seguintes comandos:

   ```powershell
   az vm image list-publishers --location "<LOCATION>"
   az vm image list-offers --location "West US" --publisher "<PUBLISHER NAME>"
   az vm image list-skus --location "West US" --publisher "<PUBLISHER NAME>" --offer "<OFFER NAME>"
   az vm image list  --location "West US" --publisher "<PUBLISHER NAME>" --offer "<OFFER NAME>" --sku "<YEAR WHEN THIS IMAGE WAS RELEASED>"
   ```

- Nos comandos anteriores, `<LOCATION>` `<PUBLISHER NAME>` substitua, e pela `<OFFER NAME>` `<YEAR WHEN THIS IMAGE WAS RELEASED>` informação indicada. Remova também os símbolos "<" e ">".

  Veja o seguinte exemplo:

  ```powershell
  az vm image list-publishers --location "West US"
  az vm image list-offers --location "West US" --publisher "MicrosoftWindowsServer"
  az vm image list-skus --location "West US" --publisher "MicrosoftWindowsServer" --offer "WindowsServer"
  az vm image list  --location "West US" --publisher "MicrosoftWindowsServer" --offer "WindowsServer" --sku "2016-Datacenter"
  ```