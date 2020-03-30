---
title: Mova um Windows AWS VMs para Azure
description: Mova uma instância de Windows Amazon Web Services (AWS) EC2 para uma máquina virtual Azure.
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
ms.openlocfilehash: 9bd01f24ac2cada02f51089d238519cd6c7e0248
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74039272"
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-an-azure-virtual-machine"></a>Mova um VM Windows da Amazon Web Services (AWS) para uma máquina virtual Azure

Se estiver a avaliar as máquinas virtuais Azure para hospedar as suas cargas de trabalho, pode exportar uma instância VM EC2 Windows EC2 (AWS) Da Amazon Web Services (AWS) e depois carregar o disco rígido virtual (VHD) para o Azure. Assim que o VHD for carregado, pode criar um novo VM em Azure a partir do VHD. 

Este artigo abrange a deslocação de um único VM da AWS para o Azure. Se quiser mover VMs de AWS para Azure em escala, consulte [máquinas virtuais migrate em Amazon Web Services (AWS) para Azure com recuperação](../../site-recovery/site-recovery-migrate-aws-to-azure.md)de site Azure .

## <a name="prepare-the-vm"></a>Preparar a VM 
 
Pode enviar VHDs generalizados e especializados para o Azure. Cada tipo requer que prepare o VM antes de exportar da AWS. 

- **VHD generalizado** - um VHD generalizado teve todas as suas informações pessoais removidas usando sysprep. Se pretende utilizar o VHD como imagem para criar novos VMs, deve: 
 
    * [Prepare um VM Windows](prepare-for-upload-vhd-image.md).  
    * Generalize a máquina virtual usando sysprep.  

 
- **VHD especializado** - um VHD especializado mantém as contas de utilizador, aplicações e outros dados do Estado do seu VM original. Se pretender utilizar o VHD como está para criar um novo VM, certifique-se de que os seguintes passos estão concluídos.  
    * [Prepare um VHD do Windows para fazer o upload para o Azure](prepare-for-upload-vhd-image.md). **Não** generalize o VM utilizando sysprep. 
    * Remova quaisquer ferramentas e agentes de virtualização de hóspedes instalados no VM (isto é, ferramentas VMware). 
    * Certifique-se de que o VM está configurado para puxar o seu endereço IP e as definições de DNS através do DHCP. Isto garante que o servidor obtém um endereço IP dentro do VNet quando é iniciado.  


## <a name="export-and-download-the-vhd"></a>Exportar e baixar o VHD 

Exporte a instância EC2 para um VHD num balde Amazon S3. Siga os passos do artigo de documentação da Amazon [Exportando uma instância como VM utilizando a Importação/Exportação vM](https://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) e execute o comando de tarefas de [criação de tarefas](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) de exportação para exportar a instância EC2 para um ficheiro VHD. 

O ficheiro VHD exportado é guardado no balde Amazon S3 que especifica. A sintaxe básica para exportar o VHD é abaixo, basta substituir o texto do espaço reservado em \<parênteses> com as suas informações.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

Uma vez exportado o VHD, siga as instruções em [Como descarregue i um objeto de um Balde S3 para](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) descarregar o ficheiro VHD do balde S3. 

> [!IMPORTANT]
> A AWS cobra taxas de transferência de dados por descarregar o VHD. Consulte o [Amazon S3 Pricing](https://aws.amazon.com/s3/pricing/) para obter mais informações.


## <a name="next-steps"></a>Passos seguintes

Agora pode enviar o VHD para o Azure e criar um novo VM. 

- Se você correu Sysprep na sua fonte para **generalizá-lo** antes de exportar, consulte [Upload um VHD generalizado e use-o para criar um novo VMs em Azure](upload-generalized-managed.md)
- Se não executou a Sysprep antes de exportar, o VHD é considerado **especializado,** consulte [O upload de um VHD especializado para o Azure e crie um novo VM](create-vm-specialized.md)

 
