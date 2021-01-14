---
title: FAQ sobre VMs windows em Azure
description: Fornece respostas a algumas das questões comuns sobre máquinas virtuais windows criadas com o modelo Resource Manager.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: eaa3f56389e8cc101b57aefce833266fa20f66f7
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197477"
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Perguntas frequentes sobre máquinas virtuais do Windows
Este artigo aborda algumas questões comuns sobre máquinas virtuais do Windows criadas no Azure utilizando o modelo de implementação do Gestor de Recursos. Para a versão Linux deste tópico, consulte [perguntas frequentes sobre máquinas virtuais Linux.](../linux/faq.md)

## <a name="what-can-i-run-on-an-azure-vm"></a>O que posso executar numa VM do Azure?
Todos os subscritores podem executar software de servidor numa máquina virtual do Azure. Para obter informações sobre a política de suporte para executar o software do servidor da Microsoft em Azure, consulte o [suporte ao software do servidor da Microsoft para máquinas virtuais Azure](https://support.microsoft.com/kb/2721672).

Algumas versões do Windows 7, Windows 8.1 e Windows 10 estão disponíveis para assinantes do MSDN Azure benefit e msdn Dev e test Pay-As-You-Go, para tarefas de desenvolvimento e teste. Para obter mais detalhes, incluindo instruções e limitações, veja [Imagens do cliente Windows para subscritores MSDN](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quanto armazenamento posso utilizar com uma máquina virtual?
Cada disco de dados pode chegar a 32.767 GiB. O número de discos de dados que pode utilizar depende do tamanho da máquina virtual. Para obter mais detalhes, veja [Tamanhos das Virtual Machines](../sizes.md).

Os Discos Geridos Azure são as ofertas recomendadas de armazenamento de discos para utilização com máquinas virtuais Azure para armazenamento persistente de dados. Pode utilizar vários Discos Geridos com cada Máquina Virtual. Os Discos Geridos oferecem dois tipos de opções de armazenamento duráveis: Discos Standard e Geridos. Para obter informações sobre preços, consulte [preços dos discos geridos.](https://azure.microsoft.com/pricing/details/managed-disks)

As contas de armazenamento Azure também podem fornecer armazenamento para o disco do sistema operativo e quaisquer discos de dados. Cada disco é um ficheiro .vhd armazenado como um blob de páginas. Para detalhes de preços, veja [Detalhes de Preço do Armazenamento](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Como posso aceder à minha máquina virtual?
Estabeleça uma ligação remota utilizando a Ligação de Ambiente de Trabalho Remoto (RDP) para um VM do Windows. Para obter instruções, consulte [como ligar e iniciar sê-lo numa máquina virtual Azure que executa o Windows](connect-logon.md). Um máximo de duas ligações simultâneas são suportadas, a menos que o servidor esteja configurado como um anfitrião de sessão de Serviços de Ambiente de Trabalho Remoto.  

Se tiver problemas com o Ambiente de Trabalho Remoto, consulte [as ligações de ambiente de trabalho remoto de resolução de problemas a uma Máquina Virtual Azure baseada no Windows](../troubleshooting/troubleshoot-rdp-connection.md?toc=/azure/virtual-machines/windows/toc.json). 

Se estiver familiarizado com o Hyper-V, poderá estar à procura de uma ferramenta semelhante à VMConnect. O Azure não oferece uma ferramenta semelhante porque o acesso à consola a uma máquina virtual não é suportado.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Posso utilizar o disco temporário (o D: unidade por defeito) para armazenar dados?
Não utilize o disco temporário para armazenar dados. É apenas armazenamento temporário, por isso arriscaria perder dados que não podem ser recuperados. A perda de dados pode ocorrer quando a máquina virtual se move para um hospedeiro diferente. O redimensionamento de uma máquina virtual, a atualização do anfitrião ou uma falha de hardware no anfitrião são alguns dos motivos pelos quais uma máquina virtual pode ser movida.

Se tiver uma aplicação que precisa de usar a letra D: drive letter, pode reatribuir letras de unidade para que o disco temporário utilize algo diferente de D:. Para obter instruções, veja [Alterar a letra de unidade do disco temporário do Windows](change-drive-letter.md).


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Como posso alterar a letra de unidade do disco temporário?
Pode alterar a letra de unidade movendo o ficheiro de página e reatribuindo as letras de unidade, mas tem de se certificar de que faz os passos numa ordem específica. Para obter instruções, veja [Alterar a letra de unidade do disco temporário do Windows](change-drive-letter.md).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Posso adicionar um VM existente a um conjunto de disponibilidade?
N.º Se quiser que o seu VM faça parte de um conjunto de disponibilidade, tem de criar o VM dentro do conjunto. Atualmente não existe uma forma de adicionar um VM a um conjunto de disponibilidade depois de ter sido criado.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Posso enviar uma máquina virtual para o Azure?
Sim. Para obter instruções, consulte [VMs migratórios no local para Azure](on-prem-to-azure.md).

## <a name="can-i-resize-the-os-disk"></a>Posso redimensionar o disco de so?
Sim. Para obter instruções, consulte [como expandir a unidade de so de uma máquina virtual num Grupo de Recursos Azure](expand-os-disk.md).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Posso copiar ou clonar um Azure VM existente?
Sim. Utilizando imagens geridas, pode criar uma imagem de uma máquina virtual e depois usar a imagem para construir vários novos VMs. Para obter instruções, consulte [Criar uma imagem personalizada de um VM](tutorial-custom-images.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Porque é que não estou a ver as regiões do Canadá Central e do Canadá do Leste através do Azure Resource Manager?

As duas novas regiões do Canadá Central e Do Leste do Canadá não estão automaticamente registadas para a criação de máquinas virtuais para as assinaturas Azure existentes. Este registo é feito automaticamente quando uma máquina virtual é implantada através do portal Azure para qualquer outra região usando O Gestor de Recursos Azure. Depois de uma máquina virtual ser implantada em qualquer outra região de Azure, as novas regiões devem estar disponíveis para máquinas virtuais subsequentes.

## <a name="does-azure-support-linux-vms"></a>O Azure suporta os VMs Linux?
Sim. Para criar rapidamente um Linux VM para experimentar, consulte [Criar um Linux VM em Azure utilizando o Portal](../linux/quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Posso adicionar um NIC ao meu VM depois de ser criado?
Sim, isto agora é possível. O VM primeiro precisa de ser interrompido. Em seguida, pode adicionar ou remover um NIC (a menos que seja o último NIC no VM). 

## <a name="are-there-any-computer-name-requirements"></a>Há algum requisito de nome de computador?
Sim. O nome do computador pode ter um comprimento máximo de 15 caracteres. Consulte [as regras e restrições das convenções](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging) de nomeação para obter mais informações sobre o nome dos seus recursos.

## <a name="are-there-any-resource-group-name-requirements"></a>Existem requisitos de nome de grupo de recursos?
Sim. O nome do grupo de recursos pode ter um comprimento máximo de 90 caracteres. Consulte [as regras e restrições das convenções](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming) de nomeação para obter mais informações sobre grupos de recursos.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Quais são os requisitos do nome de utilizador ao criar um VM?

Os nomes de utilizador podem ter um comprimento máximo de 20 caracteres e não podem terminar num período ("."). 

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

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Quais são os requisitos de senha na criação de um VM?

Existem diferentes requisitos de comprimento de senha, dependendo da ferramenta que está a usar:
 - Portal - entre 12 - 72 caracteres
 - PowerShell - entre 8 - 123 caracteres
 - CLI - entre 12 - 123

* Ter carateres em minúscula
* Ter carateres em maiúscula
* Ter um dígito
* Ter um caráter especial (Expressão regular [\W_])

Não são permitidas as seguintes palavras-passe:

<table>
    <tr>
        <td>abc@123</td>
        <td>Iloveyou!</td>
        <td>P@$$w 0</td>
        <td>P@ssw0rd</td>
        <td>P@ssword123</td>
    </tr>
    <tr>
        <td>Pa$$word</td>
        <td>pass@word1</td>
        <td>A senha!</td>
        <td>Senha1</td>
        <td>Password22</td>
    </tr>
</table>