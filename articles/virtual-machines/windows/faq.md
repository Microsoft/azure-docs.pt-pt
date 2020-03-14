---
title: FAQ sobre VMs windows em Azure
description: Fornece respostas a algumas das questões comuns sobre máquinas virtuais do Windows criadas com o modelo Desemposição de Recursos.
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
ms.openlocfilehash: e3d2673ba52ba0cca36e2a999558313b64716ade
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299619"
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Perguntas frequentes sobre máquinas virtuais do Windows
Este artigo aborda algumas questões comuns sobre máquinas virtuais do Windows criadas em Azure utilizando o modelo de implementação do Gestor de Recursos. Para a versão Linux deste tópico, consulte [frequentemente perguntas sobre máquinas virtuais Linux](../linux/faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="what-can-i-run-on-an-azure-vm"></a>O que posso executar numa VM do Azure?
Todos os subscritores podem executar software de servidor numa máquina virtual do Azure. Para obter informações sobre a política de suporte para executar o software do servidor microsoft no Azure, consulte o suporte do software do [servidor microsoft para máquinas virtuais Azure](https://support.microsoft.com/kb/2721672).

Algumas versões do Windows 7, Windows 8.1 e Windows 10 estão disponíveis para assinantes de benefícios MSDN Azure e subscritores MSDN Dev e Test Pay-As-You-Go, para tarefas de desenvolvimento e teste. Para obter mais detalhes, incluindo instruções e limitações, veja [Imagens do cliente Windows para subscritores MSDN](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quanto armazenamento posso utilizar com uma máquina virtual?
Cada disco de dados pode chegar a 32.767 GiB. O número de discos de dados que pode utilizar depende do tamanho da máquina virtual. Para obter mais detalhes, veja [Tamanhos das Virtual Machines](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Os Discos Geridos Azure são as ofertas recomendadas de armazenamento de discos para utilização com máquinas virtuais Azure para armazenamento persistente de dados. Pode utilizar vários Discos Geridos com cada Máquina Virtual. Os Discos Geridos oferecem dois tipos de opções de armazenamento duráveis: Discos Premium e Standard Managed. Para obter informações sobre preços, consulte preços de [discos geridos](https://azure.microsoft.com/pricing/details/managed-disks).

As contas de armazenamento azure também podem fornecer armazenamento para o disco do sistema operativo e quaisquer discos de dados. Cada disco é um ficheiro .vhd armazenado como um blob de páginas. Para detalhes de preços, veja [Detalhes de Preço do Armazenamento](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Como posso aceder à minha máquina virtual?
Estabeleça uma ligação remota utilizando a Ligação remota de ambiente de trabalho (RDP) para um VM do Windows. Para obter instruções, consulte [Como ligar e iniciar sessão numa máquina virtual Azure que executa o Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Um máximo de duas ligações simultâneas são suportadas, a menos que o servidor esteja configurado como um anfitrião de sessão de Serviços de Ambiente de Trabalho Remoto.  

Se estiver com problemas com o Remote Desktop, consulte as ligações de ambiente de [trabalho remoto supor problemas a uma máquina virtual Azure baseada no Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Se estiver familiarizado com o Hyper-V, pode estar à procura de uma ferramenta semelhante à VMConnect. O Azure não oferece uma ferramenta semelhante porque o acesso à consola a uma máquina virtual não é suportado.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Posso usar o disco temporário (o D: conduzir por padrão) para armazenar dados?
Não use o disco temporário para armazenar dados. É apenas armazenamento temporário, por isso arriscaria perder dados que não podem ser recuperados. A perda de dados pode ocorrer quando a máquina virtual se move para um hospedeiro diferente. O redimensionamento de uma máquina virtual, a atualização do anfitrião ou uma falha de hardware no anfitrião são alguns dos motivos pelos quais uma máquina virtual pode ser movida.

Se tiver uma aplicação que precise de usar a letra D: carta de unidade, pode reatribuir cartas de acionamento para que o disco temporário utilize algo diferente de D:. Para obter instruções, veja [Alterar a letra de unidade do disco temporário do Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Como posso alterar a letra de unidade do disco temporário?
Pode alterar a letra de unidade movendo o ficheiro da página e reatribuindo as cartas de unidade, mas tem de se certificar de que faz os passos numa ordem específica. Para obter instruções, veja [Alterar a letra de unidade do disco temporário do Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Posso adicionar um VM existente a um conjunto de disponibilidade?
Não. Se quiser que o seu VM faça parte de um conjunto de disponibilidade, precisa de criar o VM dentro do conjunto. Atualmente não existe uma maneira de adicionar um VM a um conjunto de disponibilidade após a sua criação.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Posso enviar uma máquina virtual para o Azure?
Sim. Para obter instruções, consulte [As VMs migratórias no local para Azure](on-prem-to-azure.md).

## <a name="can-i-resize-the-os-disk"></a>Posso redimensionar o disco de sois?
Sim. Para obter instruções, consulte [Como expandir a unidade de OS de uma Máquina Virtual num Grupo de Recursos Azure](expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Posso copiar ou clonar um VM Azure existente?
Sim. Utilizando imagens geridas, pode criar uma imagem de uma máquina virtual e depois utilizar a imagem para construir vários novos VMs. Para obter instruções, consulte [Criar uma imagem personalizada de um VM](tutorial-custom-images.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Porque é que não estou a ver as regiões do Canadá Central e do Canadá leste através do Azure Resource Manager?

As duas novas regiões do Canadá Central e Canadá Leste não estão automaticamente registadas para criação de máquinas virtuais para assinaturas Azure existentes. Este registo é feito automaticamente quando uma máquina virtual é implantada através do portal Azure para qualquer outra região usando o Gestor de Recursos Azure. Depois de uma máquina virtual ser implantada em qualquer outra região do Azure, as novas regiões devem estar disponíveis para máquinas virtuais subsequentes.

## <a name="does-azure-support-linux-vms"></a>O Azure suporta os VMs linux?
Sim. Para criar rapidamente um VM Linux para experimentar, consulte [Create a Linux VM on Azure usando o Portal](../linux/quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Posso adicionar um NIC ao meu VM depois de criado?
Sim, isto agora é possível. O VM primeiro precisa de ser interrompido. Em seguida, pode adicionar ou remover um NIC (a menos que seja o último NIC no VM). 

## <a name="are-there-any-computer-name-requirements"></a>Há algum requisito de nome de computador?
Sim. O nome do computador pode ter um máximo de 15 caracteres de comprimento. Consulte [as regras e restrições](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging) de convenções de naming para obter mais informações sobre o nome dos seus recursos.

## <a name="are-there-any-resource-group-name-requirements"></a>Existem requisitos de nome de grupo de recursos?
Sim. O nome do grupo de recursos pode ter um máximo de 90 caracteres de comprimento. Consulte as regras e restrições de [convenções de nomeação](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming) para obter mais informações sobre grupos de recursos.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Quais são os requisitos do nome de utilizador na criação de um VM?

Os nomes de utilizador podem ter um máximo de 20 caracteres de comprimento e não podem terminar num período ("."). 

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


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Quais são os requisitos de senha ao criar um VM?

Existem requisitos de comprimento de senha variados, dependendo da ferramenta que está a utilizar:
 - Portal - entre 12 - 72 caracteres
 - PowerShell - entre 8 - 123 caracteres
 - CLI - entre 12 - 123

* Ter caracteres mais baixos
* Ter caracteres superiores
* Tenha um dígito
* Tenha um caráter especial (regex match [\W_])

As seguintes palavras-passe não são permitidas:

<table>
    <tr>
        <td>abc@123</td>
        <td>Iloveyou!</td>
        <td>P@$$w0rd</td>
        <td>P@ssw0rd</td>
        <td>P@ssword123</td>
    </tr>
    <tr>
        <td>Pa$$word</td>
        <td>pass@word1</td>
        <td>Senha!</td>
        <td>Senha1</td>
        <td>Password22</td>
    </tr>
</table>