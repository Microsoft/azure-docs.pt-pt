---
title: Perguntas frequentes para Os VMs Linux em Azure
description: Fornece respostas a algumas das perguntas comuns sobre máquinas virtuais Linux criadas com o modelo Resource Manager.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: f55b5d9093e2e8e78d3841c332d67f26e5106d38
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200826"
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Perguntas frequentes sobre máquinas virtuais Linux
Este artigo aborda algumas questões comuns sobre máquinas virtuais Linux criadas em Azure usando o modelo de implementação do Gestor de Recursos. Para a versão Windows deste tópico, consulte [perguntas frequentes sobre máquinas virtuais do Windows](../windows/faq.md)

## <a name="what-can-i-run-on-an-azure-vm"></a>O que posso executar numa VM do Azure?
Todos os subscritores podem executar software de servidor numa máquina virtual do Azure. Para mais informações, consulte [Linux em Azure-Endorsed Distributions](endorsed-distros.md)

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quanto armazenamento posso utilizar com uma máquina virtual?
Cada disco de dados pode chegar a 32.767 GiB. O número de discos de dados que pode utilizar depende do tamanho da máquina virtual. Para obter mais detalhes, veja [Tamanhos das Virtual Machines](../sizes.md).

Os Discos Geridos Azure são as ofertas recomendadas de armazenamento de discos para utilização com máquinas virtuais Azure para armazenamento persistente de dados. Pode utilizar vários Discos Geridos com cada Máquina Virtual. Os Discos Geridos oferecem dois tipos de opções de armazenamento duráveis: Discos Standard e Geridos. Para obter informações sobre preços, consulte [preços dos discos geridos.](https://azure.microsoft.com/pricing/details/managed-disks)

As contas de armazenamento Azure também podem fornecer armazenamento para o disco do sistema operativo e quaisquer discos de dados. Cada disco é um ficheiro .vhd armazenado como um blob de páginas. Para detalhes de preços, veja [Detalhes de Preço do Armazenamento](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Como posso aceder à minha máquina virtual?
Estabeleça uma ligação remota para iniciar snúm na máquina virtual, utilizando Secure Shell (SSH). Consulte as instruções sobre como ligar a [partir do Windows](ssh-from-windows.md) ou do [Linux e Mac](mac-create-ssh-keys.md). Por predefinição, o SSH permite um máximo de 10 ligações simultâneas. Pode aumentar este número, editando o ficheiro de configuração.

Se tiver problemas, consulte as [ligações Troubleshoot Secure Shell (SSH).](../troubleshooting/troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json)

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Posso utilizar o disco temporário (/dev/sdb1) para armazenar dados?
Não utilize o disco temporário (/dev/sdb1) para armazenar dados. Só existe para armazenamento temporário. Arrisca-se a perder dados que não podem ser recuperados.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Posso copiar ou clonar um Azure VM existente?
Sim. Para obter instruções, consulte [como criar uma cópia de uma máquina virtual Linux no modelo de implementação do Gestor de Recursos](copy-vm.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Porque é que não estou a ver as regiões do Canadá Central e do Canadá do Leste através do Azure Resource Manager?
As duas novas regiões do Canadá Central e Do Leste do Canadá não estão automaticamente registadas para a criação de máquinas virtuais para as assinaturas Azure existentes. Este registo é feito automaticamente quando uma máquina virtual é implantada através do portal Azure para qualquer outra região usando O Gestor de Recursos Azure. Depois de uma máquina virtual ser implantada em qualquer outra região de Azure, as novas regiões devem estar disponíveis para máquinas virtuais subsequentes.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Posso adicionar um NIC ao meu VM depois de ser criado?
Sim, isto agora é possível. O VM primeiro precisa de ser interrompido. Em seguida, pode adicionar ou remover um NIC (a menos que seja o último NIC no VM). 

## <a name="are-there-any-computer-name-requirements"></a>Há algum requisito de nome de computador?
Sim. O nome do computador pode ter um comprimento máximo de 64 caracteres. Consulte [as regras e restrições das convenções](/azure/architecture/best-practices/resource-naming) de nomeação para obter mais informações sobre o nome dos seus recursos.

## <a name="are-there-any-resource-group-name-requirements"></a>Existem requisitos de nome de grupo de recursos?
Sim. O nome do grupo de recursos pode ter um comprimento máximo de 90 caracteres. Consulte [as regras e restrições das convenções](/azure/architecture/best-practices/resource-naming) de nomeação para obter mais informações sobre grupos de recursos.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Quais são os requisitos do nome de utilizador ao criar um VM?

Os nomes de utilizador devem ter 1 a 32 caracteres de comprimento.

Não são permitidos os seguintes nomes de utilizador:

- `1`
- `123`
- `a`
- `actuser`
- `adm`
- `admin`
- `admin1`
- `admin2`
- `administrator`
- `aspnet`
- `backup`
- `console`
- `david`
- `guest`
- `john`
- `owner`
- `root`
- `server`
- `sql`
- `support_388945a0`
- `support`
- `sys`
- `test`
- `test1`
- `test2`
- `test3`
- `user`
- `user1`
- `user2`
- `user3`
- `user4`
- `user5`
- `video`


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Quais são os requisitos de senha na criação de um VM?

Existem diferentes requisitos de comprimento de senha, dependendo da ferramenta que está a usar:
 - Portal - entre 12 - 72 caracteres
 - PowerShell - entre 8 - 123 caracteres
 - CLI - entre 12 - 123 caracteres
 - Modelos de Gestor de Recursos Azure (ARM) - 12 - 72 caracteres e caracteres de controlo não são permitidos
 

As palavras-passe também devem satisfazer 3 dos seguintes 4 requisitos de complexidade:

* Ter carateres em minúscula
* Ter carateres em maiúscula
* Ter um dígito
* Ter um caráter especial (Expressão regular [\W_])

Não são permitidas as seguintes palavras-passe:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w 0</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Pa$$word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">A senha!</td>
        <td style="text-align:center">Senha1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">Iloveyou!</td>
    </tr>
</table>
