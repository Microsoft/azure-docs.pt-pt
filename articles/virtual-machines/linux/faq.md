---
title: Perguntas frequentes para VMs Linux em Azure
description: Fornece respostas a algumas das perguntas comuns sobre máquinas virtuais Linux criadas com o modelo De saúde.
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
ms.openlocfilehash: fa1d870effc92f63fb661119214fc635eae95672
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162468"
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Perguntas frequentes sobre máquinas virtuais Linux
Este artigo aborda algumas questões comuns sobre máquinas virtuais Linux criadas em Azure usando o modelo de implementação do Gestor de Recursos. Para a versão windows deste tópico, consulte [frequentemente perguntas sobre máquinas virtuais do Windows](../windows/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>O que posso executar numa VM do Azure?
Todos os subscritores podem executar software de servidor numa máquina virtual do Azure. Para mais informações, consulte [linux em distribuições apoiadas pelo Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quanto armazenamento posso utilizar com uma máquina virtual?
Cada disco de dados pode chegar a 32.767 GiB. O número de discos de dados que pode utilizar depende do tamanho da máquina virtual. Para obter mais detalhes, veja [Tamanhos das Virtual Machines](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Os Discos Geridos Azure são as ofertas recomendadas de armazenamento de discos para utilização com máquinas virtuais Azure para armazenamento persistente de dados. Pode utilizar vários Discos Geridos com cada Máquina Virtual. Os Discos Geridos oferecem dois tipos de opções de armazenamento duráveis: Discos Premium e Standard Managed. Para obter informações sobre preços, consulte preços de [discos geridos](https://azure.microsoft.com/pricing/details/managed-disks).

As contas de armazenamento azure também podem fornecer armazenamento para o disco do sistema operativo e quaisquer discos de dados. Cada disco é um ficheiro .vhd armazenado como um blob de páginas. Para detalhes de preços, veja [Detalhes de Preço do Armazenamento](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Como posso aceder à minha máquina virtual?
Estabelecer uma ligação remota para iniciar sessão na máquina virtual, utilizando a Secure Shell (SSH). Consulte as instruções sobre como se ligar [a partir do Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou do [Linux e Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Por predefinição, o SSH permite um máximo de 10 ligações simultâneas. Pode aumentar este número, editando o ficheiro de configuração.

Se tiver problemas, confira [as ligações Troubleshoot Secure Shell (SSH).](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Posso usar o disco temporário (/dev/sdb1) para armazenar dados?
Não utilize o disco temporário (/dev/sdb1) para armazenar dados. Só está lá para armazenamento temporário. Arrisca-se a perder dados que não podem ser recuperados.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Posso copiar ou clonar um VM Azure existente?
Sim. Para obter instruções, consulte [Como criar uma cópia de uma máquina virtual Linux no modelo](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)de implementação do Gestor de Recursos .

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Porque é que não estou a ver as regiões do Canadá Central e do Canadá leste através do Azure Resource Manager?
As duas novas regiões do Canadá Central e Canadá Leste não estão automaticamente registadas para criação de máquinas virtuais para assinaturas Azure existentes. Este registo é feito automaticamente quando uma máquina virtual é implantada através do portal Azure para qualquer outra região usando o Gestor de Recursos Azure. Depois de uma máquina virtual ser implantada em qualquer outra região do Azure, as novas regiões devem estar disponíveis para máquinas virtuais subsequentes.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Posso adicionar um NIC ao meu VM depois de criado?
Sim, isto agora é possível. O VM primeiro precisa de ser interrompido. Em seguida, pode adicionar ou remover um NIC (a menos que seja o último NIC no VM). 

## <a name="are-there-any-computer-name-requirements"></a>Há algum requisito de nome de computador?
Sim. O nome do computador pode ter um máximo de 64 caracteres de comprimento. Consulte [as regras e restrições](/azure/architecture/best-practices/resource-naming) de convenções de naming para obter mais informações sobre o nome dos seus recursos.

## <a name="are-there-any-resource-group-name-requirements"></a>Existem requisitos de nome de grupo de recursos?
Sim. O nome do grupo de recursos pode ter um máximo de 90 caracteres de comprimento. Consulte as regras e restrições de [convenções de nomeação](/azure/architecture/best-practices/resource-naming) para obter mais informações sobre grupos de recursos.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Quais são os requisitos do nome de utilizador na criação de um VM?

Os nomes de utilizador devem ter 1 - 32 caracteres de comprimento.

Não são permitidos os seguintes nomes de utilizador:

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

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Quais são os requisitos de senha ao criar um VM?

Existem requisitos de comprimento de senha variados, dependendo da ferramenta que está a utilizar:
 - Portal - entre 12 - 72 caracteres
 - PowerShell - entre 8 - 123 caracteres
 - CLI - entre 12 - 123
 

As palavras-passe também devem satisfazer 3 dos seguintes 4 requisitos de complexidade:

* Ter caracteres mais baixos
* Ter caracteres superiores
* Tenha um dígito
* Tenha um caráter especial (regex match [\W_])

As seguintes palavras-passe não são permitidas:

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
        <td style="text-align:center">Senha!</td>
        <td style="text-align:center">Senha1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">Iloveyou!</td>
    </tr>
</table>