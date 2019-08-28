---
title: Mover as VMs AWS do Windows para o Azure | Microsoft Docs
description: Mova uma instância do Windows de Amazon Web Services (AWS) EC2 para uma máquina virtual do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 31f6ffc4f114039e0c53c1994f8c4364dea18298
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089511"
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-an-azure-virtual-machine"></a>Mover uma VM do Windows de Amazon Web Services (AWS) para uma máquina virtual do Azure

Se você estiver avaliando máquinas virtuais do Azure para hospedar suas cargas de trabalho, poderá exportar uma instância de VM do Windows do AWS (EC2) Amazon Web Services existente e, em seguida, carregar o VHD (disco rígido virtual) no Azure. Depois que o VHD for carregado, você poderá criar uma nova VM no Azure por meio do VHD. 

Este artigo aborda a movimentação de uma única VM do AWS para o Azure. Se você quiser mover VMs do AWS para o Azure em escala, consulte [migrar máquinas virtuais no Amazon Web Services (AWS) para o Azure com Azure site Recovery](../../site-recovery/site-recovery-migrate-aws-to-azure.md).

## <a name="prepare-the-vm"></a>Preparar a VM 
 
Você pode carregar VHDs generalizados e especializados para o Azure. Cada tipo requer que você prepare a VM antes de exportar do AWS. 

- **VHD generalizado** -um VHD generalizado teve todas as informações de sua conta pessoal removidas usando o Sysprep. Se você pretende usar o VHD como uma imagem para criar novas VMs a partir do, você deve: 
 
    * [Prepare uma VM do Windows](prepare-for-upload-vhd-image.md).  
    * Generalizar a máquina virtual usando o Sysprep.  

 
- **VHD especializado** -um VHD especializado mantém as contas de usuário, aplicativos e outros dados de estado de sua VM original. Se você pretende usar o VHD como está para criar uma nova VM, verifique se as etapas a seguir foram concluídas.  
    * [Preparar um VHD do Windows para carregar no Azure](prepare-for-upload-vhd-image.md). **Não** generalizar a VM usando Sysprep. 
    * Remova quaisquer ferramentas e agentes de virtualização convidados instalados na VM (ou seja, ferramentas do VMware). 
    * Verifique se a VM está configurada para extrair seu endereço IP e as configurações de DNS via DHCP. Isso garante que o servidor obtenha um endereço IP na VNet quando ele for iniciado.  


## <a name="export-and-download-the-vhd"></a>Exportar e baixar o VHD 

Exporte a instância EC2 para um VHD em um Bucket S3 da Amazon. Siga as etapas no artigo de documentação da Amazon exportando [uma instância como uma VM usando importação/exportação de VM](https://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) e execute o comando [Create-Instance-Export-Task](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) para exportar a instância EC2 para um arquivo VHD. 

O arquivo VHD exportado é salvo no Bucket S3 da Amazon que você especificar. A sintaxe básica para exportar o VHD está abaixo, basta substituir o texto do espaço \<reservado entre colchetes > com suas informações.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

Depois que o VHD tiver sido exportado, siga as instruções em [como baixar um objeto de um Bucket S3?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) para baixar o arquivo VHD do Bucket S3. 

> [!IMPORTANT]
> O AWS cobra taxas de transferência de dados para baixar o VHD. Consulte [preços do Amazon S3](https://aws.amazon.com/s3/pricing/) para obter mais informações.


## <a name="next-steps"></a>Passos Seguintes

Agora você pode carregar o VHD no Azure e criar uma nova VM. 

- Se você executou o Sysprep em sua origem para generalizar antes de exportar, consulte [carregar um VHD generalizado e usá-lo para criar novas VMs no Azure](upload-generalized-managed.md)
- Se você não executou o Sysprep antes da exportação, o VHD é considerado **especializado**, consulte [carregar um VHD especializado no Azure e criar uma nova VM](create-vm-specialized.md)

 
