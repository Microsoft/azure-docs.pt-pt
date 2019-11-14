---
title: Perguntas frequentes sobre VMs do Linux no Azure
description: Fornece respostas para algumas das perguntas mais comuns sobre máquinas virtuais do Linux criadas com o modelo do Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-management
ms.assetid: 3648e09c-1115-4818-93c6-688d7a54a353
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: 107f2376df7eb767953b32b3447937e24869894c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036173"
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Perguntas frequentes sobre Máquinas Virtuais do Linux
Este artigo aborda algumas perguntas comuns sobre as máquinas virtuais do Linux criadas no Azure usando o modelo de implantação do Resource Manager. Para obter a versão do Windows deste tópico, consulte perguntas frequentes [sobre máquinas virtuais do Windows](../windows/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>O que posso executar numa VM do Azure?
Todos os subscritores podem executar software de servidor numa máquina virtual do Azure. Para obter mais informações, consulte [Linux no Azure – distribuições endossadas](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quanto armazenamento posso utilizar com uma máquina virtual?
Cada disco de dados pode ter até 32.767 GiB. O número de discos de dados que pode utilizar depende do tamanho da máquina virtual. Para obter mais detalhes, veja [Tamanhos das Virtual Machines](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

O Azure Managed Disks são as ofertas de armazenamento em disco recomendadas para uso com máquinas virtuais do Azure para armazenamento persistente de dados. Pode utilizar vários Managed Disks com cada Máquina Virtual. Os Managed Disks oferecem dois tipos de opções de armazenamento duráveis: Managed Disks Premium e Standard. Para obter informações sobre preços, consulte [preços de Managed disks](https://azure.microsoft.com/pricing/details/managed-disks).

As contas de armazenamento do Azure também podem fornecer armazenamento para o disco do sistema operacional e quaisquer discos de dados. Cada disco é um ficheiro .vhd armazenado como um blob de páginas. Para detalhes de preços, veja [Detalhes de Preço do Armazenamento](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Como posso acessar minha máquina virtual?
Estabeleça uma conexão remota para fazer logon na máquina virtual usando Secure Shell (SSH). Consulte as instruções sobre como se conectar [do Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [do Linux e Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Por predefinição, o SSH permite um máximo de 10 ligações simultâneas. Pode aumentar este número, editando o ficheiro de configuração.

Se você estiver tendo problemas, confira [solucionar problemas de conexões Secure Shell (SSH)](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Posso usar o disco temporário (/dev/sdb1) para armazenar dados?
Não use o disco temporário (/dev/sdb1) para armazenar dados. Ele só está lá para armazenamento temporário. Você tem risco de perder dados que não podem ser recuperados.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Posso copiar ou clonar uma VM existente do Azure?
Sim. Para obter instruções, consulte [como criar uma cópia de uma máquina virtual do Linux no modelo de implantação do Gerenciador de recursos](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Por que não estou vendo as regiões centro-oeste e leste do Canadá por meio de Azure Resource Manager?
As duas novas regiões da central do Canadá e do leste do Canadá não são registradas automaticamente para a criação de máquinas virtuais para assinaturas do Azure existentes. Esse registro é feito automaticamente quando uma máquina virtual é implantada por meio do portal do Azure para qualquer outra região usando Azure Resource Manager. Depois que uma máquina virtual é implantada em qualquer outra região do Azure, as novas regiões devem estar disponíveis para as máquinas virtuais subsequentes.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Posso adicionar uma NIC à minha VM após sua criação?
Sim, agora isso é possível. A VM primeiro precisa ser interrompida desalocada. Em seguida, você pode adicionar ou remover uma NIC (a menos que seja a última NIC na VM). 

## <a name="are-there-any-computer-name-requirements"></a>Há algum requisito de nome de computador?
Sim. O nome do computador pode ter no máximo 64 caracteres de comprimento. Consulte [regras e restrições de convenções de nomenclatura](/azure/architecture/best-practices/resource-naming) para obter mais informações sobre como nomear seus recursos.

## <a name="are-there-any-resource-group-name-requirements"></a>Há algum requisito de nome de grupo de recursos?
Sim. O nome do grupo de recursos pode ter no máximo 90 caracteres de comprimento. Consulte [regras e restrições de convenções de nomenclatura](/azure/architecture/best-practices/resource-naming) para obter mais informações sobre grupos de recursos.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Quais são os requisitos de nome de usuário ao criar uma VM?

Os nomes de acessados devem ter de 1-32 caracteres de comprimento.

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
| `video`         |

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Quais são os requisitos de senha ao criar uma VM?

Há requisitos de comprimento de senha variados, dependendo da ferramenta que você está usando:
 - Portal-entre 12-72 caracteres
 - PowerShell-entre 8-123 caracteres
 - CLI-entre 12-123
 

As senhas também devem atender a três dos 4 seguintes requisitos de complexidade:

* Ter caracteres inferiores
* Ter caracteres superiores
* Ter um dígito
* Ter um caractere especial (correspondência de Regex [\ W_])

As seguintes senhas não são permitidas:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Pa$$word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">La!</td>
        <td style="text-align:center">Password1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">ILOVEYOU!</td>
    </tr>
</table>
