---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: bc3590e90cacfa4966f0d1f64aa1c8d49483cb1b
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56883780"
---
Este artigo aborda algumas perguntas comuns dos utilizadores sobre máquinas virtuais do Azure criadas com o modelo de implementação clássico.

## <a name="can-i-migrate-my-vm-created-in-the-classic-deployment-model-to-the-new-resource-manager-model"></a>Posso migrar a minha VM criada no modelo de implementação clássico para o novo modelo do Resource Manager?
Sim. Para obter instruções sobre como migrar, veja:

* [Migre do clássico para o Azure Resource Manager com o Azure PowerShell](../articles/virtual-machines/windows/migration-classic-resource-manager-ps.md).
* [Migre do clássico para o Azure Resource Manager com a CLI do Azure](../articles/virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md).

## <a name="what-can-i-run-on-an-azure-vm"></a>O que posso executar numa VM do Azure?
Todos os subscritores podem executar software de servidor numa máquina virtual do Azure. Pode executar versões recentes do Windows Server, bem como diversas distribuições em Linux. Para detalhes de suporte, veja:

• Para VMs do Windows -- [Suporte de software de servidores da Microsoft para Máquinas Virtuais do Azure](https://go.microsoft.com/fwlink/p/?LinkId=393550)

• Para VMs do Linux -- [Linux em Distribuições Apoiadas pelo Azure](https://go.microsoft.com/fwlink/p/?LinkId=393551)

Para imagens do cliente Windows, determinadas versões do Windows 7 e Windows 8.1 estão disponíveis para subscritores MSDN dos benefícios do Azure e subscritores MSDN de Desenvolvimento e Teste "Pay As You Go" para tarefas de desenvolvimento e teste. Para obter mais detalhes, incluindo instruções e limitações, veja [Imagens do cliente Windows para subscritores MSDN](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/).

## <a name="why-are-affinity-groups-being-deprecated"></a>Por que razão os grupos de afinidades vão ser preteridos?
Os grupos de afinidade são um conceito legado para um agrupamento geográfico das implementações de serviços cloud dum cliente e contas de armazenamento no Azure. Foram originalmente fornecidos para melhorar o desempenho de rede de VMs nos primeiros designs de redes do Azure. Suportavam também a versão inicial de redes virtuais (VNets), que estavam limitadas a um pequeno conjunto de hardware numa região.

A rede atual do Azure numa região foi concebida para que os grupos de afinidades já não sejam necessários. As redes virtuais estão também num âmbito regional, pelo que um grupo de afinidades já não é necessário quando utilizar uma rede virtual. Devido a estas melhorias, já não recomendamos que os clientes utilizem grupos de afinidade, pois podem ser limitativos em alguns cenários. A utilização de grupos de afinidade irá associar desnecessariamente as suas VMs a hardware específico que limita a escolha dos tamanhos de VM que estão disponíveis para si. Poderá também originar erros relacionados com a capacidade quando tentar adicionar novas VMs, se o hardware específico associado ao grupo de afinidades estiver na capacidade máxima.

As funcionalidades de grupo de afinidades já foram preteridos no modelo de implementação Azure Resource Manager e no portal do Azure. Para o portal clássico do Azure, estamos a preterir o suporte para a criação de grupos de afinidade e a criação de recursos de armazenamento que são afixados a um grupo de afinidades. Não precisa de modificar os serviços cloud existentes que estão a utilizar um grupo de afinidades. No entanto, não deve utilizar os grupos de afinidade para novos serviços cloud, a menos que tal seja recomendado por um profissional de suporte do Azure.

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quanto armazenamento posso utilizar com uma máquina virtual?
Cada disco de dados pode ter até 1 TB. O número de discos de dados que pode utilizar depende do tamanho da máquina virtual. Para obter mais detalhes, veja [Tamanhos das Virtual Machines](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Uma conta de armazenamento do Azure fornece armazenamento para o disco do sistema operativo e qualquer discos de dados. Cada disco é um ficheiro .vhd armazenado como um blob de páginas. Para detalhes de preços, veja [Detalhes de Preço do Armazenamento](https://go.microsoft.com/fwlink/p/?LinkId=396819).

## <a name="which-virtual-hard-disk-types-can-i-use"></a>Que tipos de disco rígido virtual posso utilizar?
O Azure só suporta discos rígidos virtuais fixos em formato VHD. Se tiver um VHDX que pretende utilizar no Azure, terá primeiro de convertê-lo com o Gestor de Hyper-V ou o cmdlet [convert-VHD](https://go.microsoft.com/fwlink/p/?LinkId=393656). Depois de o fazer, utilize o cmdlet [Add-AzureVHD](https://msdn.microsoft.com/library/azure/dn495173.aspx) (no modo Gestão de Serviço) para carregar o VHD para uma conta de armazenamento no Azure, para poder utilizá-lo com as máquinas virtuais.

* Para obter instruções do Linux, veja [Creating and Uploading a Virtual Hard Disk that Contains the Linux Operating System (Criar e carregar discos rígidos virtuais que contêm o sistema operativo Linux)](../articles/virtual-machines/linux/classic/create-upload-vhd-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="are-these-virtual-machines-the-same-as-hyper-v-virtual-machines"></a>Estas máquinas virtuais são iguais às máquinas virtuais de Hyper-V?
Em muitos aspetos, são semelhantes às VMs de Hyper-V da "Geração 1", mas não são exatamente iguais. Ambas fornecem hardware virtualizado e os discos rígidos virtuais em formato VHD são compatíveis. Isto significa que é possível mover entre o Hyper-V e o Azure. Três diferenças principais que, por vezes, surpreendem os utilizadores, são:

* O Azure não fornece acesso à consola para uma máquina virtual. Não é possível aceder a uma VM até terminar o arranque.
* Na maior parte dos [tamanhos](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), as VMs do Azure só têm 1 adaptador de rede virtual, o que significa que também só podem ter 1 endereço IP externo. (Os tamanhos A8 e A9 utilizam um segundo adaptador de rede para a comunicação de aplicação entre instâncias em cenários limitados.)
* As VMs do Azure não suportam as funcionalidades de VM do Hyper-V da Geração 2. Para obter detalhes sobre estas funcionalidades, veja [Especificações de Máquina Virtual para o Hyper-V](https://technet.microsoft.com/library/dn592184.aspx) e [Descrição Geral de Máquina Virtual da Geração 2](https://technet.microsoft.com/library/dn282285.aspx).

## <a name="can-these-virtual-machines-use-my-existing-on-premises-networking-infrastructure"></a>Estas máquinas virtuais podem utilizar a minha infraestrutura de rede existente no local?
Para máquinas de virtuais criadas no modelo de implementação clássico, pode utilizar a Rede Virtual do Azure para ampliar a infraestrutura existente. A abordagem é como configurar uma sucursal. Pode aprovisionar e gerir redes privadas virtuais (VPNs) no Azure, bem como ligá-las de forma segura à infraestrutura de TI no local. Para obter detalhes, veja [Descrição Geral da Rede Virtual do Azure](../articles/virtual-network/virtual-networks-overview.md).

Terá de especificar a rede a que pretende que a máquina virtual pertença quando criar a máquina virtual. Não é possível associar uma máquina virtual existente a uma rede virtual. No entanto, pode contornar este problema ao desanexar o disco rígido virtual (VHD) da máquina virtual existente e, em seguida, utilizá-lo para criar uma nova máquina virtual com a configuração de rede que pretende.

## <a name="how-can-i-access--my-virtual-machine"></a>Como posso aceder à minha máquina virtual?
Tem de estabelecer uma ligação remota para iniciar sessão para a máquina virtual utilizando a conexão de área de trabalho remoto para uma VM do Windows ou um Secure Shell (SSH) para uma VM do Linux. Para obter instruções, veja:

* [Como Iniciar Sessão numa Máquina Virtual com o Windows Server](../articles/virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Um máximo de 2 ligações simultâneas são suportadas, a menos que o servidor esteja configurado como um anfitrião de sessões de Serviços de Ambiente de Trabalho Remoto.  
* [Como Iniciar Sessão numa Máquina Virtual com o Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Por predefinição, o SSH permite um máximo de 10 ligações simultâneas. Pode aumentar este número, editando o ficheiro de configuração.

Se tiver problemas com o Ambiente de Trabalho Remoto ou o SSH, instale e utilize a extensão [VMAccess](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ajudá-lo a corrigir o problema.

Para VMs do Windows, as opções adicionais incluem:

* No portal do Azure, localize a VM, em seguida, clique em **repor o acesso remoto** na barra de comandos.
* Reveja [Resolução de problemas de ligações ao Ambiente de Trabalho Remoto para uma Máquina Virtual do Azure baseada no Windows](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Utilize a comunicação remota do Windows PowerShell para ligar à VM ou criar pontos finais adicionais para que outros recursos liguem à VM. Para obter mais detalhes, veja [Como Configurar Pontos Finais para uma Máquina Virtual](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Se estiver familiarizado com o Hyper-V, poderá querer uma ferramenta semelhante ao VMConnect. O Azure não oferece uma ferramenta semelhante porque o acesso à consola para uma máquina virtual não é suportado.

## <a name="can-i-use-the-temporary-disk-the-d-drive-for-windows-or-devsdb1-for-linux-to-store-data"></a>Posso utilizar o disco temporário (a unidade D: para o Windows ou /dev/sdb1 para Linux) para armazenar dados?
Não deve utilizar o disco temporário (por predefinição, a unidade D: para o Windows ou /dev/sdb1 para Linux) para armazenar dados. São apenas de armazenamento temporário, pelo que correria o risco de perder dados que não poderiam ser recuperados. Isto pode ocorrer quando a máquina virtual é movida para outro anfitrião. O redimensionamento de uma máquina virtual, a atualização do anfitrião ou uma falha de hardware no anfitrião são alguns dos motivos pelos quais uma máquina virtual pode ser movida.

## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Como posso alterar a letra de unidade do disco temporário?
Na máquina virtual do Windows, pode alterar a letra de unidade ao mover o ficheiro de paginação e ao reatribuir letras de unidade, mas tem de certificar-se de que efetua os passos por uma ordem específica. Para obter instruções, veja [Alterar a letra de unidade do disco temporário do Windows](../articles/virtual-machines/windows/change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="how-can-i-upgrade-the-guest-operating-system"></a>Como posso atualizar o sistema operativo convidado?
Geralmente, a atualização do termo implica mover para uma versão mais recente do sistema operativo, permanecendo no mesmo hardware. Para VMs do Azure, o processo de transição para uma versão mais recente é diferente para Linux e Windows:

* Para VMs do Linux, utilize as ferramentas de gestão de pacotes e os procedimentos adequados de distribuição.
* Para uma máquina virtual do Windows, terá de migrar o servidor através de algo como as Ferramentas de Migração do Windows Server. Não tente atualizar o SO convidado, enquanto o mesmo residir no Azure. Isto não é suportado devido ao risco de perder o acesso à máquina virtual. Se ocorrerem problemas durante a atualização, poderia perder a capacidade para iniciar uma sessão de Ambiente de Trabalho Remoto e não conseguiria resolver os problemas.

Para obter mais detalhes gerais sobre as ferramentas e os processos para migrar o Windows Server, veja [Migrar Funções e Funcionalidades para o Windows Server](https://go.microsoft.com/fwlink/p/?LinkId=396940).

## <a name="whats-the-default-user-name-and-password-on-the-virtual-machine"></a>Quais são o nome de utilizador e a palavra-passe predefinidos da máquina virtual?
As imagens fornecidas pelo Azure não têm um nome de utilizador e a palavra-passe pré-configurados. Ao criar máquina virtual utilizando uma dessas imagens, terá de fornecer um nome de utilizador e palavra-passe, que irá utilizar para iniciar sessão para a máquina virtual.

Caso se tenha esquecido do nome de utilizador ou palavra-passe e tiver instalado o Agente de VM, pode instalar e utilizar a extensão [VMAccess](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para corrigir o problema.

Detalhes adicionais:

* Para as imagens do Linux, se utilizar o portal do Azure, "azureuser" é fornecido como um nome de utilizador padrão, mas pode alterar isto utilizando "Da Galeria" em vez de "Criação rápida" como a forma de criar a máquina virtual. Utilizar “Da galeria” também lhe permite decidir se deve utilizar uma palavra-passe, uma chave SSH ou ambas para iniciar a sessão. A conta de utilizador é de um utilizador sem privilégios que tenha acesso “sudo” para executar comandos com privilégios. A conta “raiz” está desativada.
* Para as imagens do Windows, terá de fornecer um nome de utilizador e palavra-passe quando criar a VM. A conta é adicionada ao grupo de Administradores.

## <a name="can-azure-run-anti-virus-on-my-virtual-machines"></a>O Azure pode executar antivírus nas minhas máquinas virtuais?
O Azure oferece várias opções para soluções de software antivírus, mas a gestão das mesmas cabe-lhe a si. Por exemplo, poderá precisar de uma subscrição separada para o antimalware software, e terá de decidir quando executar análises e instalar atualizações. Pode adicionar suporte de antivírus com uma extensão de VM para o Antimalware da Microsoft, o Symantec Endpoint Protection ou o Agente de TrendMicro Deep Security quando cria uma máquina virtual do Windows ou mais tarde. As extensões da Symantec e TrendMicro permitem-lhe utilizar uma subscrição de avaliação gratuita por tempo limitado ou uma subscrição empresarial existente. O Antimalware da Microsoft é gratuito. Para obter mais detalhes, veja:

* [Como instalar e configurar o Symantec Endpoint Protection numa VM do Azure](https://go.microsoft.com/fwlink/p/?LinkId=404207)
* [Como instalar e configurar o Trend Micro Deep Security como Serviço numa VM do Azure](https://go.microsoft.com/fwlink/p/?LinkId=404206)
* [Implementar Soluções Antimalware em Máquinas Virtuais do Azure](https://azure.microsoft.com/blog/2014/05/13/deploying-antimalware-solutions-on-azure-virtual-machines/)

## <a name="what-are-my-options-for-backup-and-recovery"></a>Quais são as minhas opções de cópia de segurança e recuperação?
O Azure Backup está disponível como pré-visualização em determinados regiões. Para obter detalhes, veja [Fazer cópia de segurança de máquinas virtuais do Azure](../articles/backup/backup-azure-arm-vms.md). Estão disponíveis outras soluções de parceiros certificados. Para saber o que está atualmente disponível, procure no Azure Marketplace.

Uma opção adicional é utilizar as capacidades de instantâneos do armazenamento de blobs. Para tal, terá de encerrar a VM antes de qualquer operação que dependa de um instantâneo de blob. Isto guarda escritas de dados pendentes e coloca o sistema de ficheiros num estado consistente.

## <a name="how-does-azure-charge-for-my-vm"></a>Como o Azure cobra pela minha VM?
O Azure cobra um preço por hora com base no tamanho da VM e do sistema operativo. Para horas parciais, o Azure cobra apenas os minutos de utilização. Se criar a VM com uma imagem VM que contém determinado software previamente instalado, poderão aplicar-se encargos de software adicionais por hora. O Azure cobra em separado pelo armazenamento do sistema operativo e os discos de dados da VM. O armazenamento em disco temporário é gratuito.

É-lhe cobrado quando o estado da VM está Em execução ou Parado, mas não lhe é cobrado quando o estado da VM está Parado (Desalocado). Para colocar uma VM no estado Parado (Desalocado), efetue um dos seguintes procedimentos:

* Encerre ou elimine a VM no portal do Azure.
* Utilize o cmdlet Stop-AzureVM disponível no módulo Azure PowerShell.
* Utilize a operação Função de Encerramento na API REST da Gestão de Serviços e especifique StoppedDeallocated para o elemento PostShutdownAction.

Para obter mais detalhes, veja [Preços das Máquinas Virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/).

## <a name="will-azure-reboot-my-vm-for-maintenance"></a>O Azure reiniciará a minha VM para manutenção?
Por vezes, o Azure reinicia a VM como parte das atualizações de manutenção regulares e planeadas nos datacenters do Azure.

Os eventos de manutenção não planeada podem ocorrer quando o Azure deteta um problema de hardware grave que afeta a VM. Para eventos não planeados, o Azure migra automaticamente a VM para um anfitrião em bom estado de funcionamento e reinicia a VM.

Para qualquer VM autónoma (ou seja, em que a VM não faz parte de um conjunto de disponibilidade), o Azure notifica o Administrador de Serviços da subscrição por e-mail, pelo menos, uma semana antes da manutenção planeada, porque as VMs poderiam ser reiniciadas durante a atualização. As aplicações em execução nas VMs poderão passar por períodos de indisponibilidade.

Também pode utilizar o portal do Azure ou o Azure PowerShell para ver os registos de reinício quando o reinício ocorreu devido a manutenção planeada. Para obter detalhes, veja [Ver Registos de Reinício da VM](https://azure.microsoft.com/blog/2015/04/01/viewing-vm-reboot-logs/).

Para fornecer redundância, coloque duas ou mais VMs configuradas de forma semelhante no mesmo conjunto de disponibilidade. Isto ajuda a garantir que pelo menos uma VM está disponível durante a manutenção planeada ou não planeada. O Azure garante determinados níveis de disponibilidade de VM para esta configuração. Para obter detalhes, veja [Gerir a disponibilidade das máquinas virtuais](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="additional-resources"></a>Recursos adicionais
[Sobre as Máquinas Virtuais do Azure](../articles/virtual-machines/virtual-machines-linux-about.md)

[Criar e Gerir VMs do Linux com a CLI do Azure](../articles/virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Criar e gerir VMs do Windows com o Azure PowerShell](../articles/virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

