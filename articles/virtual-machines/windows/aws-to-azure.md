---
title: Mover uma instância EC2 do Windows AWS para Azure
description: Mova uma instância eC2 do Windows da Amazon Web Services (AWS) para uma máquina virtual Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 9f66653996cb36e58054756ac877f859559609be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87267103"
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-an-azure-virtual-machine"></a>Mover um VM windows da Amazon Web Services (AWS) para uma máquina virtual Azure

Se estiver a avaliar as máquinas virtuais Azure para hospedar as suas cargas de trabalho, pode exportar uma instância de VM do Amazon Web Services (AWS) existente e depois fazer o upload do disco rígido virtual (VHD) para Azure. Uma vez que o VHD é carregado, você pode criar um novo VM em Azure a partir do VHD. 

Este artigo abrange a deslocação de um único VM de AWS para Azure. Se quiser mover VMs de AWS para Azure em escala, consulte [máquinas virtuais migratórias nos Serviços Web da Amazon (AWS) para Azure com Azure Site Recovery](../../site-recovery/migrate-tutorial-aws-azure.md).

## <a name="prepare-the-vm"></a>Preparar a VM 
 
Pode enviar VHDs generalizados e especializados para Azure. Cada tipo requer que prepare o VM antes de exportar da AWS. 

- **VHD generalizado** - um VHD generalizado teve todas as informações da sua conta pessoal removidas usando sysprep. Se pretender usar o VHD como imagem para criar novos VMs a partir de, deve: 
 
    * [Preparar um VM do Windows](prepare-for-upload-vhd-image.md).  
    * Generalize a máquina virtual utilizando o Sysprep.  

 
- **Specialized VHD** - um VHD especializado mantém as contas de utilizador, aplicações e outros dados estatais do seu VM original. Se pretender utilizar o VHD como é para criar um novo VM, certifique-se de que os seguintes passos estão concluídos.  
    * [Prepare um VHD do Windows para fazer o upload para o Azure](prepare-for-upload-vhd-image.md). **Não** generalize o VM utilizando o Sysprep. 
    * Remova quaisquer ferramentas e agentes de virtualização de hóspedes instalados no VM (por isso, ferramentas VMware). 
    * Certifique-se de que o VM está configurado para puxar o seu endereço IP e as definições de DNS via DHCP. Isto garante que o servidor obtém um endereço IP dentro do VNet quando este começar.  


## <a name="export-and-download-the-vhd"></a>Exportar e descarregar o VHD 

Exporte a instância EC2 para um VHD num balde Amazon S3. Siga as etapas do artigo de documentação da Amazon [Exportando uma Instância como VM Utilizando a VM Import/Export](https://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) e executar o comando [de tarefa de criação-caso-exportação](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) para exportar a instância EC2 para um ficheiro VHD. 

O ficheiro VHD exportado é guardado no balde Amazon S3 que especifica. A sintaxe básica para exportar o VHD está abaixo, basta substituir o texto do espaço reservado \<brackets> com as suas informações.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

Uma vez exportado o VHD, siga as instruções de [Como Descarregar um Objeto de um Balde S3?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) 

> [!IMPORTANT]
> AWS cobra taxas de transferência de dados para o download do VHD. Consulte [o Amazon S3 Pricing](https://aws.amazon.com/s3/pricing/) para mais informações.


## <a name="next-steps"></a>Passos seguintes

Agora pode fazer o upload do VHD para Azure e criar um novo VM. 

- Se executou a Sysprep na sua fonte para **generalizá-lo** antes de exportar, consulte [o Upload a Generalized VHD e use-o para criar um novo VMs em Azure](upload-generalized-managed.md)
- Se não executou o Sysprep antes de exportar, o VHD é considerado **especializado,** consulte [o Upload de um VHD especializado para a Azure e crie um novo VM](create-vm-specialized.md)

 
