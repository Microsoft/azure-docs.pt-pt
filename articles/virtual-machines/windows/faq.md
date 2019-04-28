---
title: FAQ sobre as VMs do Windows no Azure | Documentos da Microsoft
description: Fornece respostas para algumas das perguntas comuns sobre as máquinas de virtuais do Windows criadas com o modelo do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-management
ms.assetid: 757da816-a050-4889-a010-6f75d7978eb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: cynthn
ms.openlocfilehash: 65d4326763ef9754159e94c9426f3aee69f80ffd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61095664"
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Perguntas frequentes sobre o máquinas de virtuais do Windows
Este artigo aborda algumas perguntas comuns sobre as máquinas de virtuais do Windows criadas no Azure com o modelo de implementação do Resource Manager. Para a versão do Linux deste tópico, consulte [pergunta mais frequente sobre máquinas virtuais do Linux](../linux/faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>O que posso executar numa VM do Azure?
Todos os subscritores podem executar software de servidor numa máquina virtual do Azure. Para obter informações sobre a política de suporte para software de servidor Microsoft em execução no Azure, consulte [o suporte do software de servidores da Microsoft para máquinas de virtuais do Azure](https://support.microsoft.com/kb/2721672)

Determinadas versões do Windows 7, Windows 8.1 e Windows 10 estão disponíveis para subscritores do benefício do MSDN do Azure e os assinantes do MSDN de programação e teste pay as you go, para tarefas de desenvolvimento e teste. Para obter mais detalhes, incluindo instruções e limitações, veja [Imagens do cliente Windows para subscritores MSDN](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quanto armazenamento posso utilizar com uma máquina virtual?
Cada disco de dados pode ser até 4 TB (4095 GB). O número de discos de dados que pode utilizar depende do tamanho da máquina virtual. Para obter mais detalhes, veja [Tamanhos das Virtual Machines](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Managed Disks do Azure são as disco recomendado ofertas de armazenamento para utilização com máquinas virtuais do Azure para armazenamento persistente de dados. Pode utilizar vários Managed Disks com cada Máquina Virtual. Managed Disks oferta dois tipos de opções de armazenamento duráveis: Discos geridos Premium e Standard. Para obter informações sobre preços, consulte [preços de discos geridos](https://azure.microsoft.com/pricing/details/managed-disks).

Contas de armazenamento do Azure também podem fornecer armazenamento para o disco do sistema operativo e qualquer discos de dados. Cada disco é um ficheiro .vhd armazenado como um blob de páginas. Para detalhes de preços, veja [Detalhes de Preço do Armazenamento](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Como posso acessar minha máquina virtual?
Estabelece uma ligação remota utilizando a ligação de ambiente de trabalho remoto (RDP) para uma VM do Windows. Para obter instruções, consulte [como ligar e iniciar sessão a uma máquina virtual do Azure a executar o Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Um máximo de duas ligações simultâneas são suportadas, a menos que o servidor está configurado como um anfitrião de sessões de serviços de ambiente de trabalho remoto.  

Se estiver a ter problemas com o ambiente de trabalho remoto, consulte [ligações de resolução de problemas de ambiente de trabalho remoto para um baseado no Windows Azure Virtual Machine](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Se estiver familiarizado com o Hyper-V, poderá querer uma ferramenta semelhante ao VMConnect. O Azure não oferece uma ferramenta semelhante porque o acesso à consola para uma máquina virtual não é suportado.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Pode utilizar o disco temporário (a unidade d: por predefinição) para armazenar dados?
Não utilize o disco temporário para armazenar dados. Só é armazenamento temporário, pelo que correria o risco de perda de dados que não podem ser recuperados. Quando a máquina virtual for movido para outro anfitrião, pode ocorrer perda de dados. O redimensionamento de uma máquina virtual, a atualização do anfitrião ou uma falha de hardware no anfitrião são alguns dos motivos pelos quais uma máquina virtual pode ser movida.

Se tiver um aplicativo que precisa usar a letra de unidade d:, poderá reatribuir letras de unidade para que o disco temporário utiliza algo diferente de d:. Para obter instruções, veja [Alterar a letra de unidade do disco temporário do Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Como posso alterar a letra de unidade do disco temporário?
Pode alterar a letra de unidade ao mover o ficheiro de página e ao reatribuir letras de unidade, mas precisa para se certificar de que siga os passos numa ordem específica. Para obter instruções, veja [Alterar a letra de unidade do disco temporário do Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Pode adicionar uma VM existente para um conjunto de disponibilidade?
Não. Se pretender que a VM para fazer parte de um conjunto de disponibilidade, terá de criar a VM no conjunto. Atualmente não há uma maneira de adicionar uma VM para um conjunto de disponibilidade depois ter sido criado.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Pode carregar uma máquina virtual para o Azure?
Sim. Para obter instruções, consulte [migrar VMs no local para o Azure](on-prem-to-azure.md).

## <a name="can-i-resize-the-os-disk"></a>Pode redimensionar o disco do SO?
Sim. Para obter instruções, consulte [como expandir a unidade do SO de uma Máquina Virtual num grupo de recursos do Azure](expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Pode copiar ou clonar uma VM do Azure existente?
Sim. Utilizar imagens gerenciadas, pode criar uma imagem de uma máquina virtual e, em seguida, utilizar a imagem para criar várias VMs novas. Para obter instruções, consulte [criar uma imagem personalizada de uma VM](tutorial-custom-images.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Por que eu não estou vendo Canadá Central e regiões do leste do Canadá através do Gestor de recursos do Azure?

As duas novas regiões do Canadá Central e leste do Canadá não são automaticamente registradas para criação de máquinas virtuais para as subscrições do Azure existentes. Este registo é feito automaticamente quando uma máquina virtual é implementada através do portal do Azure para qualquer outra região com o Azure Resource Manager. Depois de uma máquina virtual é implementada em qualquer outra região do Azure, as novas regiões devem estar disponíveis para máquinas de virtuais subsequentes.

## <a name="does-azure-support-linux-vms"></a>O Azure suporta VMs do Linux?
Sim. Para criar rapidamente uma VM do Linux para experimentar, veja [criar uma VM do Linux no Azure através do Portal](../linux/quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Posso adicionar um NIC à minha VM depois de criada?
Sim, isso agora é possível. A VM tem primeiro de ser parada desalocada. Em seguida, pode adicionar ou remover um NIC (a menos que é a última NIC na VM). 

## <a name="are-there-any-computer-name-requirements"></a>Existem quaisquer requisitos de nome de computador?
Sim. O nome do computador pode ter um máximo de 15 carateres de comprimento. Ver [restrições e regras de convenções de nomenclatura](/azure/architecture/best-practices/naming-conventions#compute) para obter mais informações em torno de seus recursos de atribuição de nomes.

## <a name="are-there-any-resource-group-name-requirements"></a>Existem todos os recursos os requisitos de nome de grupo?
Sim. O nome do grupo de recursos pode ter um máximo de 90 carateres de comprimento. Ver [restrições e regras de convenções de nomenclatura](/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions) para obter mais informações sobre grupos de recursos.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Quais são os requisitos de nome de utilizador, ao criar uma VM?

Nomes de utilizador pode ter um máximo de 20 carateres de comprimento e não pode terminar num ponto final ("."). 


Não são permitidos nomes de utilizador seguintes:
<table>
    <tr>
        <td style="text-align:center">1</td><td style="text-align:center">123</td><td style="text-align:center">a</td><td style="text-align:center">actuser</td>
    </tr>
    <tr>
        <td style="text-align:center">adm</td><td style="text-align:center">admin</td><td style="text-align:center">admin1</td><td style="text-align:center">admin2</td>
    </tr>   <tr>
        <td style="text-align:center">administrador</td><td style="text-align:center">aspnet</td><td style="text-align:center">cópia de segurança</td><td style="text-align:center">consola</td>
    </tr>
    <tr>
        <td style="text-align:center">David </td><td style="text-align:center">convidado</td><td style="text-align:center">John</td><td style="text-align:center">proprietário</td>
    </tr>
    <tr>
        <td style="text-align:center">raiz</td><td style="text-align:center">servidor</td><td style="text-align:center">sql</td><td style="text-align:center">suporte</td>
    </tr>
    <tr>
        <td style="text-align:center">support_388945a0</td><td style="text-align:center">sys.</td><td style="text-align:center">test</td><td style="text-align:center">test1</td>
    </tr>
    <tr>
        <td style="text-align:center">test2</td><td style="text-align:center">test3</td><td style="text-align:center">Utilizador</td><td style="text-align:center">Utilizador1</td>
    </tr>
    <tr>
        <td style="text-align:center">user2</td><td style="text-align:center">user3</td><td style="text-align:center">user4</td><td style="text-align:center">user5</td>
    </tr>
</table>

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Quais são os requisitos de palavra-passe, quando cria uma VM?
As palavras-passe tem de ter 12-123 carateres de comprimento e satisfazer 3 dos seguintes requisitos de 4 complexidade:

* Ter carateres inferior
* Ter caracteres superiores
* Tem um dígito
* Ter um caractere especial ([\W_] corresponde ao Regex)

As palavras-passe seguintes não são permitidas:

<table>
    <tr>
        <td>abc@123</td>
        <td>ILOVEYOU gerou enormes!</td>
        <td>P@$$w0rd</td>
        <td>P@ssw0rd</td>
        <td>P@ssword123</td>
    </tr>
    <tr>
        <td>Pa$$word</td>
        <td>pass@word1</td>
        <td>Palavra-passe!</td>
        <td>Password1</td>
        <td>Password22</td>
    </tr>
</table>
