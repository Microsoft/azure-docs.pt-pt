---
title: Perguntas frequentes sobre VMs do Windows no Azure | Microsoft Docs
description: Fornece respostas para algumas das perguntas mais comuns sobre máquinas virtuais do Windows criadas com o modelo do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-management
ms.assetid: 757da816-a050-4889-a010-6f75d7978eb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: 95b7644ab8e0254a53eb4a0872a5af98a0894aec
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839227"
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Perguntas frequentes sobre Máquinas Virtuais do Windows
Este artigo aborda algumas perguntas comuns sobre as máquinas virtuais do Windows criadas no Azure usando o modelo de implantação do Resource Manager. Para a versão do Linux deste tópico, consulte perguntas frequentes [sobre máquinas virtuais do Linux](../linux/faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="what-can-i-run-on-an-azure-vm"></a>O que posso executar numa VM do Azure?
Todos os subscritores podem executar software de servidor numa máquina virtual do Azure. Para obter informações sobre a política de suporte para executar o software de servidor Microsoft no Azure, consulte [suporte de software de servidor da Microsoft para máquinas virtuais do Azure](https://support.microsoft.com/kb/2721672).

Determinadas versões do Windows 7, Windows 8.1 e Windows 10 estão disponíveis para assinantes do benefício do Azure no MSDN e assinantes de desenvolvimento e teste do MSDN pagos conforme o uso, para tarefas de implantação e teste. Para obter mais detalhes, incluindo instruções e limitações, veja [Imagens do cliente Windows para subscritores MSDN](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quanto armazenamento posso utilizar com uma máquina virtual?
Cada disco de dados pode ter até 32.767 GiB. O número de discos de dados que pode utilizar depende do tamanho da máquina virtual. Para obter mais detalhes, veja [Tamanhos das Virtual Machines](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

O Azure Managed Disks são as ofertas de armazenamento em disco recomendadas para uso com máquinas virtuais do Azure para armazenamento persistente de dados. Pode utilizar vários Managed Disks com cada Máquina Virtual. Os Managed Disks oferecem dois tipos de opções de armazenamento duráveis: Managed Disks Premium e Standard. Para obter informações sobre preços, consulte [preços de Managed disks](https://azure.microsoft.com/pricing/details/managed-disks).

As contas de armazenamento do Azure também podem fornecer armazenamento para o disco do sistema operacional e quaisquer discos de dados. Cada disco é um ficheiro .vhd armazenado como um blob de páginas. Para detalhes de preços, veja [Detalhes de Preço do Armazenamento](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Como posso acessar minha máquina virtual?
Estabeleça uma conexão remota usando o Conexão de Área de Trabalho Remota (RDP) para uma VM do Windows. Para obter instruções, consulte [como se conectar e entrar em uma máquina virtual do Azure que executa o Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Há suporte para no máximo duas conexões simultâneas, a menos que o servidor esteja configurado como um host de sessão Serviços de Área de Trabalho Remota.  

Se você tiver problemas com o Área de Trabalho Remota, consulte [solucionar problemas de conexões de área de trabalho remota com uma máquina virtual do Azure baseada no Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Se estiver familiarizado com o Hyper-V, poderá querer uma ferramenta semelhante ao VMConnect. O Azure não oferece uma ferramenta semelhante porque o acesso à consola para uma máquina virtual não é suportado.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Posso usar o disco temporário (a unidade D: por padrão) para armazenar dados?
Não use o disco temporário para armazenar dados. Ele é apenas um armazenamento temporário, portanto, você correrá o risco de perder dados que não podem ser recuperados. A perda de dados pode ocorrer quando a máquina virtual é movida para um host diferente. O redimensionamento de uma máquina virtual, a atualização do anfitrião ou uma falha de hardware no anfitrião são alguns dos motivos pelos quais uma máquina virtual pode ser movida.

Se você tiver um aplicativo que precisa usar a letra da unidade D:, você poderá reatribuir letras de unidade para que o disco temporário use algo diferente de D:. Para obter instruções, veja [Alterar a letra de unidade do disco temporário do Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Como posso alterar a letra de unidade do disco temporário?
Você pode alterar a letra da unidade movendo o arquivo de paginação e reatribuindo letras de unidade, mas precisará certificar-se de fazer as etapas em uma ordem específica. Para obter instruções, veja [Alterar a letra de unidade do disco temporário do Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Posso adicionar uma VM existente a um conjunto de disponibilidade?
Não. Se você quiser que sua VM faça parte de um conjunto de disponibilidade, você precisará criar a VM no conjunto. Atualmente, não há uma maneira de adicionar uma VM a um conjunto de disponibilidade após sua criação.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Posso carregar uma máquina virtual no Azure?
Sim. Para obter instruções, consulte [migrando VMs locais para o Azure](on-prem-to-azure.md).

## <a name="can-i-resize-the-os-disk"></a>Posso redimensionar o disco do sistema operacional?
Sim. Para obter instruções, consulte [como expandir a unidade do sistema operacional de uma máquina virtual em um grupo de recursos do Azure](expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Posso copiar ou clonar uma VM existente do Azure?
Sim. Usando imagens gerenciadas, você pode criar uma imagem de uma máquina virtual e, em seguida, usar a imagem para criar várias novas VMs. Para obter instruções, consulte [criar uma imagem personalizada de uma VM](tutorial-custom-images.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Por que não estou vendo as regiões centro-oeste e leste do Canadá por meio de Azure Resource Manager?

As duas novas regiões da central do Canadá e do leste do Canadá não são registradas automaticamente para a criação de máquinas virtuais para assinaturas do Azure existentes. Esse registro é feito automaticamente quando uma máquina virtual é implantada por meio do portal do Azure para qualquer outra região usando Azure Resource Manager. Depois que uma máquina virtual é implantada em qualquer outra região do Azure, as novas regiões devem estar disponíveis para as máquinas virtuais subsequentes.

## <a name="does-azure-support-linux-vms"></a>O Azure dá suporte a VMs do Linux?
Sim. Para criar rapidamente uma VM do Linux para experimentar, consulte [criar uma VM do Linux no Azure usando o portal](../linux/quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Posso adicionar uma NIC à minha VM após sua criação?
Sim, agora isso é possível. A VM primeiro precisa ser interrompida desalocada. Em seguida, você pode adicionar ou remover uma NIC (a menos que seja a última NIC na VM). 

## <a name="are-there-any-computer-name-requirements"></a>Há algum requisito de nome de computador?
Sim. O nome do computador pode ter no máximo 15 caracteres de comprimento. Consulte [regras e restrições de convenções de nomenclatura](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#sample-naming-convention) para obter mais informações sobre como nomear seus recursos.

## <a name="are-there-any-resource-group-name-requirements"></a>Há algum requisito de nome de grupo de recursos?
Sim. O nome do grupo de recursos pode ter no máximo 90 caracteres de comprimento. Consulte [regras e restrições de convenções de nomenclatura](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming) para obter mais informações sobre grupos de recursos.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Quais são os requisitos de nome de usuário ao criar uma VM?

Os nomes de acessados podem ter no máximo 20 caracteres de comprimento e não podem terminar com um ponto ("."). 

Os seguintes nomes de não são permitidos:

| | | | |
|-----------------|-----------|--------------------|----------|
| `administrator` | `admin`   | `user`             | `user1`  |
| `test`          | `user2`   | `test1`            | `user3`  |
| `admin1`        | `1`       | `123`              | `a`      |
| `actuser`       | `adm`     | `admin2`           | `aspnet` |
| `backup`        | `console` | `david`            | `guest`  |
| `john`          | `owner`   | `root`             | `server` |
| `sql`           | `support` | `support_388945a0` | `sys`    |
| `test2`         | `test3`   | `user4`            | `user5`  |


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Quais são os requisitos de senha ao criar uma VM?

Há requisitos de comprimento de senha variados, dependendo da ferramenta que você está usando:
 - Portal-entre 12-72 caracteres
 - PowerShell-entre 8-123 caracteres
 - CLI-entre 12-123

* Ter caracteres inferiores
* Ter caracteres superiores
* Ter um dígito
* Ter um caractere especial (correspondência de Regex [\ W_])

As seguintes senhas não são permitidas:

<table>
    <tr>
        <td>abc@123</td>
        <td>ILOVEYOU!</td>
        <td>P @ $ $w 0rd</td>
        <td>P@ssw0rd</td>
        <td>P@ssword123</td>
    </tr>
    <tr>
        <td>$word PA $</td>
        <td>pass@word1</td>
        <td>La!</td>
        <td>Password1</td>
        <td>Password22</td>
    </tr>
</table>
