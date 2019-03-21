---
title: Mover uma VMs do Windows AWS para o Azure | Documentos da Microsoft
description: Mova uma instância do Windows de EC2 do Amazon Web Services (AWS) para uma máquina virtual do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: f66101d9847c57c5e078c3484a243e7b38823f53
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58001796"
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-an-azure-virtual-machine"></a>Mover uma VM Windows do Amazon Web Services (AWS) para uma máquina virtual do Azure

Se estiver a avaliar máquinas virtuais do Azure para alojar as suas cargas de trabalho, pode exportar uma instância de VM do Amazon Web Services (AWS) EC2 Windows existente, em seguida, carregue o disco rígido virtual (VHD) para o Azure. Depois do VHD é carregado, pode criar uma nova VM no Azure a partir do VHD. 

Este artigo aborda a mover uma única VM do AWS para o Azure. Se quiser mover as VMs do AWS para o Azure em escala, veja [migrar máquinas virtuais no Amazon Web Services (AWS) para o Azure com o Azure Site Recovery](../../site-recovery/site-recovery-migrate-aws-to-azure.md).

## <a name="prepare-the-vm"></a>Preparar a VM 
 
Pode carregar VHDs generalizadas e especializadas para o Azure. Cada tipo requer a preparação da VM antes de exportar do AWS. 

- **Um VHD generalizado** -um VHD generalizado teve todas as suas informações de conta pessoal removidas com o Sysprep. Se pretende usar o VHD como uma imagem para criar VMs novas a partir de, deve: 
 
    * [Preparar um VM do Windows](prepare-for-upload-vhd-image.md).  
    * Generalize a máquina virtual com o Sysprep.  

 
- **Especializada VHD** -um VHD especializado mantém as contas de utilizador, aplicativos e outros dados de estado da original VM. Se pretende usar o VHD como-consiste em criar uma nova VM, certifique-se de que os seguintes passos são concluídos.  
    * [Preparar um VHD do Windows para carregar para o Azure](prepare-for-upload-vhd-image.md). **Isso não é possível** generalizar a VM com o Sysprep. 
    * Remova todos os agentes instalados na VM (ou seja, as ferramentas do VMware) e ferramentas de Virtualização do convidado. 
    * Certifique-se de que a VM está configurada para solicitar o respetivo endereço IP e as definições de DNS através de DHCP. Isto garante que o servidor obtém o endereço IP na VNet em modo de arranque.  


## <a name="export-and-download-the-vhd"></a>Exportar e Baixe o VHD 

Exporte a instância do EC2 para um VHD num bucket do Amazon S3. Siga os passos no artigo de documentação da Amazon [exportar uma instância como uma VM através de VM de importação/exportação](https://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) e execute o [criar instância-export-tarefa](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) comando para exportar a instância do EC2 para um ficheiro VHD. 

O ficheiro exportado do VHD é guardado no bucket do Amazon S3 que especificar. A sintaxe básica para exportar o VHD é abaixo, Acabei de substituir o texto de marcador de posição no <brackets> com as suas informações.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

Assim que o VHD tenha sido exportado, siga as instruções em [como posso transferir um objeto de um registo de S3?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) para transferir o ficheiro VHD a partir do registo de S3. 

> [!IMPORTANT]
> As taxas por baixar o VHD de transferência de dados de custos do AWS. Ver [preços do Amazon S3](https://aws.amazon.com/s3/pricing/) para obter mais informações.


## <a name="next-steps"></a>Passos Seguintes

Agora pode carregar o VHD para o Azure e criar uma nova VM. 

- Se tiver executado Sysprep na sua origem para **Generalizar** -lo antes de exportar, consulte [carregar um VHD generalizado e utilizá-lo para criar um novo VMs no Azure](upload-generalized-managed.md)
- Se não tiver executado Sysprep antes de exportar, o VHD é considerado **especializadas**, consulte [carregar um VHD especializado para o Azure e criar uma nova VM](create-vm-specialized.md)

 
