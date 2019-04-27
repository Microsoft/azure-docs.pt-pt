---
title: Perguntas mais frequentes sobre as VMs do Linux no Azure | Documentos da Microsoft
description: Fornece respostas para algumas das perguntas comuns sobre as máquinas de virtuais de Linux criadas com o modelo do Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-management
ms.assetid: 3648e09c-1115-4818-93c6-688d7a54a353
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: cynthn
ms.openlocfilehash: 8d421adfae335a976485ed463a69484a74be5b44
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729533"
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Perguntas frequentes sobre máquinas virtuais do Linux
Este artigo aborda algumas perguntas comuns sobre as máquinas de virtuais de Linux criadas no Azure com o modelo de implementação do Resource Manager. Para a versão do Windows deste tópico, consulte [pergunta mais frequente sobre máquinas de virtuais do Windows](../windows/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>O que posso executar numa VM do Azure?
Todos os subscritores podem executar software de servidor numa máquina virtual do Azure. Para obter mais informações, consulte [Linux em distribuições Azure-Endorsed](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quanto armazenamento posso utilizar com uma máquina virtual?
Cada disco de dados pode ser até 4 TB (4095 GB). O número de discos de dados que pode utilizar depende do tamanho da máquina virtual. Para obter mais detalhes, veja [Tamanhos das Virtual Machines](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Managed Disks do Azure são as disco recomendado ofertas de armazenamento para utilização com máquinas virtuais do Azure para armazenamento persistente de dados. Pode utilizar vários Managed Disks com cada Máquina Virtual. Managed Disks oferta dois tipos de opções de armazenamento duráveis: Discos geridos Premium e Standard. Para obter informações sobre preços, consulte [preços de discos geridos](https://azure.microsoft.com/pricing/details/managed-disks).

Contas de armazenamento do Azure também podem fornecer armazenamento para o disco do sistema operativo e qualquer discos de dados. Cada disco é um ficheiro .vhd armazenado como um blob de páginas. Para detalhes de preços, veja [Detalhes de Preço do Armazenamento](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Como posso acessar minha máquina virtual?
Estabelece uma ligação remota para iniciar sessão na máquina virtual, utilizando Secure Shell (SSH). Veja as instruções sobre como ligar [do Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [de Linux e Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Por predefinição, o SSH permite um máximo de 10 ligações simultâneas. Pode aumentar este número, editando o ficheiro de configuração.

Se estiver a ter problemas, consulte [ligações de resolução de problemas de Secure Shell (SSH)](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Pode utilizar o disco temporário (/ desenvolvimento/sdb1) para armazenar dados?
Não utilize o disco temporário (/ desenvolvimento/sdb1) para armazenar dados. Destina-se apenas existir um armazenamento temporário. Corre o risco de perda de dados que não podem ser recuperados.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Pode copiar ou clonar uma VM do Azure existente?
Sim. Para obter instruções, consulte [como criar uma cópia de uma máquina virtual do Linux no modelo de implementação do Resource Manager](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Por que eu não estou vendo Canadá Central e regiões do leste do Canadá através do Gestor de recursos do Azure?
As duas novas regiões do Canadá Central e leste do Canadá não são automaticamente registradas para criação de máquinas virtuais para as subscrições do Azure existentes. Este registo é feito automaticamente quando uma máquina virtual é implementada através do portal do Azure para qualquer outra região com o Azure Resource Manager. Depois de uma máquina virtual é implementada em qualquer outra região do Azure, as novas regiões devem estar disponíveis para máquinas de virtuais subsequentes.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Posso adicionar um NIC à minha VM depois de criada?
Sim, isso agora é possível. A VM tem primeiro de ser parada desalocada. Em seguida, pode adicionar ou remover um NIC (a menos que é a última NIC na VM). 

## <a name="are-there-any-computer-name-requirements"></a>Existem quaisquer requisitos de nome de computador?
Sim. O nome do computador pode ter um máximo de 64 carateres de comprimento. Ver [restrições e regras de convenções de nomenclatura](/azure/architecture/best-practices/naming-conventions) para obter mais informações em torno de seus recursos de atribuição de nomes.

## <a name="are-there-any-resource-group-name-requirements"></a>Existem todos os recursos os requisitos de nome de grupo?
Sim. O nome do grupo de recursos pode ter um máximo de 90 carateres de comprimento. Ver [restrições e regras de convenções de nomenclatura](/azure/architecture/best-practices/naming-conventions) para obter mais informações sobre grupos de recursos.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Quais são os requisitos de nome de utilizador, ao criar uma VM?

Nomes de utilizador devem ser de 1 a 32 carateres de comprimento.

Não são permitidos nomes de utilizador seguintes:

<table>
    <tr>
        <td style="text-align:center">administrador </td><td style="text-align:center"> admin </td><td style="text-align:center"> Utilizador </td><td style="text-align:center"> Utilizador1</td>
    </tr>
    <tr>
        <td style="text-align:center">test </td><td style="text-align:center"> user2 </td><td style="text-align:center"> test1 </td><td style="text-align:center"> user3</td>
    </tr>
    <tr>
        <td style="text-align:center">admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> a</td>
    </tr>
    <tr>
        <td style="text-align:center">actuser  </td><td style="text-align:center"> adm </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> aspnet</td>
    </tr>
    <tr>
        <td style="text-align:center">cópia de segurança </td><td style="text-align:center"> consola </td><td style="text-align:center"> David </td><td style="text-align:center"> convidado</td>
    </tr>
    <tr>
        <td style="text-align:center">John </td><td style="text-align:center"> proprietário </td><td style="text-align:center"> raiz </td><td style="text-align:center"> servidor</td>
    </tr>
    <tr>
        <td style="text-align:center">sql </td><td style="text-align:center"> suporte </td><td style="text-align:center"> support_388945a0 </td><td style="text-align:center"> sys.</td>
    </tr>
    <tr>
        <td style="text-align:center">test2 </td><td style="text-align:center"> test3 </td><td style="text-align:center"> user4 </td><td style="text-align:center"> user5</td>
    </tr>
</table>


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Quais são os requisitos de palavra-passe, quando cria uma VM?
As palavras-passe tem de ter 6-72 carateres de comprimento e satisfazer 3 dos seguintes requisitos de 4 complexidade:

* Ter carateres inferior
* Ter caracteres superiores
* Tem um dígito
* Ter um caractere especial ([\W_] corresponde ao Regex)

As palavras-passe seguintes não são permitidas:

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
        <td style="text-align:center">Palavra-passe!</td>
        <td style="text-align:center">Password1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">ILOVEYOU gerou enormes!</td>
    </tr>
</table>
