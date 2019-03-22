---
title: Utilizar o Azure Site Recovery para proteger um servidor de ficheiros
description: Este artigo descreve como utilizar o Azure Site Recovery para proteger um servidor de ficheiros
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: 51754021f5029a751be90bfc4194ac6347c1e278
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58005202"
---
# <a name="protect-a-file-server-by-using-azure-site-recovery"></a>Utilizar o Azure Site Recovery para proteger um servidor de ficheiros 

O [Azure Site Recovery](site-recovery-overview.md) contribui para a sua estratégia de continuidade comercial e recuperação após desastre (BCDR) ao manter as suas aplicações empresariais em funcionamento durante falhas planeadas e não planeadas. O Site Recovery gere e orquestra a recuperação após desastre de computadores no local e de máquinas virtuais (VMs) do Azure. A recuperação após desastre inclui replicação, ativação pós-falha e recuperação de várias cargas de trabalho.

Este artigo descreve como utilizar o Site Recovery para proteger um servidor de ficheiros e faz outras recomendações de acordo com os diversos ambientes. 

- [Replicar máquinas de servidor de ficheiros de IaaS do Azure](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Utilizar o Site Recovery para replicar um servidor de ficheiros no local](#replicate-an-on-premises-file-server-by-using-site-recovery)

## <a name="file-server-architecture"></a>Arquitetura de servidor de ficheiros
O objetivo dos sistemas de partilha de ficheiros distribuídos é proporcionar um ambiente no qual um grupo de utilizadores distribuídos geograficamente pode trabalhar eficientemente nos ficheiros e ter a certeza de que os seus requisitos de integridade são aplicados. Um ecossistema de servidor de ficheiros no local típico que suporta um elevado número de utilizadores em simultâneo e de itens de conteúdos utiliza a Replicação do Sistema de Ficheiros Distribuído (DFSR) para o agendamento da replicação e a limitação da largura de banda. 

O DFSR utiliza um algoritmo de compressão, denominado Remote Differential Compression (RDC), que pode ser utilizado para atualizar, de forma eficaz, os ficheiros através de uma rede com largura de banda limitada. Deteta inserções, remoções e reorganizações de dados nos ficheiros. O DFSR é ativado para replicar apenas os blocos de ficheiros alterados quando os ficheiros são atualizados. Também há ambientes de servidores de ficheiros, no qual são feitas cópias de segurança diárias em horas fora de pico, o que satisfaz as necessidades de recuperação após desastres. O DFSR não está implementado.

O diagrama seguinte ilustra o ambiente do servidor de ficheiros com o DFSR implementado.
                
![Arquitetura do DFSR](media/site-recovery-file-server/dfsr-architecture.JPG)

No diagrama anterior, muitos servidores de ficheiros, chamados membros, participam ativamente na replicação de ficheiros entre um grupo de replicação. Os conteúdos na pasta replicada estão disponíveis para todos os clientes que enviam pedidos para qualquer um dos membros, mesmo que esse membro esteja offline.

## <a name="disaster-recovery-recommendations-for-file-servers"></a>Recomendações de recuperação após desastre para os servidores de ficheiros

* **Replicar um servidor de ficheiros utilizando a recuperação de Site**: Servidores de ficheiros podem ser replicados para o Azure utilizando a recuperação de Site. Se um ou mais servidores de ficheiros no local estiverem inacessíveis, as VMs de recuperação podem ser colocadas no Azure. As VMs podem, então, servir os pedidos dos clientes, no local, desde que haja conectividade VPN site a site e que o Active Directory esteja configurado no Azure. Pode utilizar este método no caso de um ambiente com o DFSR configurado ou de um ambiente de servidor de ficheiros simples sem o DFSR. 

* **Expandir o DFSR para uma VM de IaaS do Azure**: Num ambiente de servidor de ficheiros em cluster com o DFSR implementado, pode estender o DFSR no local para o Azure. Em seguida, é ativada uma VM do Azure para realizar a função de servidor de ficheiros. 

    * Depois de as dependências da conectividade VPN site a site e do Active Directory serem processadas e de o DFSR estar em vigor, quando um ou mais servidores de ficheiros no local estiverem inacessíveis, os clientes podem ligar-se à VM do Azure, que serve os pedidos.

    * Pode utilizar esta abordagem se o Site Recovery não suportar as configurações das suas VMs. Um exemplo é um disco em cluster partilhado, que é, por vezes, utilizado em ambientes de servidores de ficheiros. O DFSR também funciona bem em ambientes com pouca largura de banda e uma taxa de renovação média. Tem de ter em conta os custos adicionais de ter uma VM do Azure em funcionamento constante. 

* **Utilizar o Azure File Sync para replicar os ficheiros**: Se planeia utilizar a cloud ou já utilizar uma VM do Azure, pode utilizar o Azure File Sync. O Azure File Sync oferece a sincronização das partilhas de ficheiros completamente geridas na cloud que são acessíveis através do [protocolo SMB (Server Message Block), que é a norma da indústria](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx). As partilhas de ficheiros do Azure podem, depois, ser montadas em simultâneo por implementações na cloud ou no local de Windows, Linux e macOS. 

O diagrama seguinte ajuda-o a determinar a estratégia a utilizar para o seu ambiente de servidor de ficheiros.

![Árvore de decisões](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-in-your-decisions-about-disaster-recovery-to-azure"></a>Fatores a ter em conta nas decisões relativas à recuperação após desastre para o Azure

|Ambiente  |Recomendação  |Pontos a considerar |
|---------|---------|---------|
|Ambiente do servidor de ficheiros com ou sem DFSR|   [Utilizar o Site Recovery para replicação](#replicate-an-on-premises-file-server-by-using-site-recovery)   |    O Site Recovery não suporta clusters de discos partilhados nem o armazenamento ligado à rede (NAS). Se o ambiente utilizar estas configurações, utilize uma das outras abordagens, conforme adequado. <br> O Site Recovery não suporta SMB 3.0. A VM replicada só incorpora as alterações quando as alterações feitas aos ficheiros são atualizadas na localização original dos mesmos.
|Ambiente do servidor de ficheiros com DFSR     |  [Expandir o DFSR para uma máquina virtual de IaaS do Azure](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |      O DFSR funciona bem em ambientes de largura de banda bastante limitada. Esta abordagem exige uma VM do Azure em funcionamento permanente. Tem de ter em conta os custos da VM no planejamento.         |
|VM de IaaS do Azure     |     Sincronização de ficheiros    |     Se utilizar o File Sync num cenário de recuperação após desastre, durante a ativação pós-falha, tem de realizar ações manuais para garantir que as partilhas de ficheiros estão acessíveis para a máquina cliente de forma transparente. O File Sync requer que a porta 445 esteja aberta na máquina cliente.     |


### <a name="site-recovery-support"></a>Suporte do Site Recovery
Uma vez que a replicação do Site Recovery não depende da aplicação, espera-se que estas recomendações se apliquem aos cenários seguintes.

| Origem    |Para um site secundário    |Para o Azure
|---------|---------|---------|
|Azure| -|Sim|
|Hyper-V|   Sim |Sim
|VMware |Sim|   Sim
|Servidor físico|   Sim |Sim
 

> [!IMPORTANT]
> Antes de continuar com uma das três abordagens abaixo, confirme que estas dependências foram tidas em conta.

**Conetividade site a site**: É necessário estabelecer uma ligação direta entre o site no local e a rede do Azure para permitir a comunicação entre servidores. Utilize uma ligação VPN site a site protegida a uma rede virtual do Azure que seja utilizada como o site de recuperação após desastre. Para obter mais informações, veja [Establish a site-to-site VPN connection between an on-premises site and an Azure virtual network](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) (Estabelecer uma ligação VPN site a site entre um site no local e uma rede virtual do Azure).

**Active Directory**: DFSR depende do Active Directory. Isto significa que a floresta do Active Directory com controladores de domínio local é expandida para o site de recuperação após desastre no Azure. Mesmo que não esteja a utilizar o DFSR, caso seja necessário conceder acesso ou verificar o acesso dos utilizadores, tem de seguir os passos abaixo. Para obter mais informações, veja [Extend on-premises Active Directory to Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory) (Expandir o Active Directory no local para o Azure).

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Recomendação da recuperação após desastre para máquinas virtuais de IaaS do Azure

Se estiver a configurar e a gerir a recuperação após desastre de servidores de ficheiros alojados em VMs de IaaS do Azure, pode escolher entre duas opções, em função de querer mudar para os [Ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction):

* [Utilizar o File Sync](#use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine)
* [Utilizar o Site Recovery](#replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery)

## <a name="use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine"></a>Utilize o File Sync para replicar ficheiros alojados numa máquina virtual de IaaS

Os Ficheiros do Azure podem ser utilizados para substituir completamente ou complementar os servidores de ficheiros no local ou em dispositivos NAS tradicionais. As partilhas de ficheiros do Azure também podem ser replicadas com o File Sync para servidores Windows, no local ou na cloud, para desempenho e colocação em cache distribuída dos dados onde estão a ser utilizados. Os passos abaixo descrevem a recomendação de recuperação após desastre para as VMs do Azure que desempenham a mesma funcionalidade dos servidores de ficheiros tradicionais:
* Utilize o Site Recovery para proteger as máquinas. Siga os passos em [Replicar uma VM do Azure noutra região do Azure](azure-to-azure-quickstart.md).
* Utilize o File Sync para replicar os ficheiros da VM que funciona como o servidor de ficheiros para a cloud.
* Utilize a funcionalidade de [plano de recuperação](site-recovery-create-recovery-plans.md) do Site Recovery para adicionar scripts para [montar a partilha de ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) e aceder à mesma na máquina virtual.

Os passos abaixo descrevem resumidamente a utilização do File Sync:

1. [Crie uma conta de armazenamento no Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Se tiver escolhido o armazenamento georredundante de acesso de leitura para as suas contas de armazenamento, obtém acesso de leitura aos seus dados a partir da região secundária, em caso de desastre. Para obter mais informações, consulte [recuperação após desastre e ativação pós-falha forçada (pré-visualização) no armazenamento do Azure](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2ffiless%2ftoc.json).
2. [Crie uma partilha de ficheiros](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Inicie o File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) no servidor de ficheiros do Azure.
4. Crie um grupo de sincronização. Os pontos finais num grupo de sincronização são mantidos em sincronia entre si. Os grupos de sincronização têm de conter, pelo menos, um ponto final da cloud, que representa uma partilha de ficheiros do Azure. Também têm de conter um ponto final de servidor, que representa um caminho num servidor do Windows.
5. Os ficheiros são agora mantidos em sincronia na partilha de ficheiros do Azure e no servidor no local.
6. Em caso de desastre no ambiente no local, utilize um [plano de recuperação](site-recovery-create-recovery-plans.md) para fazer uma ativação pós-falha. Adicione o script para [montar a partilha de ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) e aceder à partilha na sua máquina virtual.

### <a name="replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery"></a>Utilizar o Site Recovery para replicar uma máquina virtual de servidor de ficheiros de IaaS

Se tiver clientes no local que acedem à máquina virtual de servidor de ficheiros de IaaS, siga os passos abaixo. Caso contrário, avance para o passo 3.

1. Estabeleça uma ligação VPN site a site entre o site no local e a rede do Azure.
2. Expanda o Active Directory no local.
3. [Configure a recuperação após desastre](azure-to-azure-tutorial-enable-replication.md) na máquina virtual de servidor de ficheiros de IaaS para uma região secundária.


Para obter mais informações sobre a recuperação após desastre para uma região secundária, veja [este artigo](concepts-azure-to-azure-architecture.md).


## <a name="replicate-an-on-premises-file-server-by-using-site-recovery"></a>Utilizar o Site Recovery para replicar um servidor de ficheiros no local

Os passos abaixo descrevem a replicação para uma VM de VMware. Veja [este tutorial](tutorial-hyper-v-to-azure.md) para obter os passos para replicar uma VM de Hyper-V.

1. [Prepare os recursos do Azure](tutorial-prepare-azure.md) para a replicação de máquinas no local.
2. Estabeleça uma ligação VPN site a site entre o site no local e a rede do Azure. 
3. Expanda o Active Directory no local.
4. [Prepare os servidores do VMware no local](tutorial-prepare-on-premises-vmware.md).
5. [Configure a recuperação após desastre](tutorial-vmware-to-azure.md) para o Azure das VMs no local.

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Expandir o DFSR para uma máquina virtual de IaaS do Azure

1. Estabeleça uma ligação VPN site a site entre o site no local e a rede do Azure. 
2. Expanda o Active Directory no local.
3. [Crie e aprovisione uma VM de servidor de ficheiros](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) na rede virtual do Azure.
Confirme que a máquina virtual é adicionada à mesma rede virtual do Azure, a qual tem a conectividade cruzada ao ambiente no local. 
4. Instale e [configure DFSR](https://blogs.technet.microsoft.com/b/filecab/archive/2013/08/21/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of-the-clones.aspx) no Windows Server.
5. [Implemente um espaço de nomes do DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).
6. Com o espaço de nomes do DFS implementado, é possível fazer a ativação pós-falha das pastas partilhadas do site de produção para os sites de recuperação após desastre mediante a atualização dos destinos da pasta do espaço de nomes do DFS. Quando as alterações ao espaço de nomes DFS forem replicadas através do Active Directory, os utilizadores são ligados aos destinos da pasta adequados de forma transparente.

## <a name="use-file-sync-to-replicate-your-on-premises-files"></a>Utilizar o File Sync para replicar para os ficheiros no local
Pode utilizar o File Sync para replicar ficheiros para a cloud. Em caso de desastre e de indisponibilidade do servidor de ficheiros no local, pode montar as localizações dos ficheiros pretendidas a partir da cloud e continuar a servir os pedidos a partir de máquinas cliente.
Para integrar o File Sync com o Site Recovery:

* Utilize o Site Recovery para proteger as máquinas do servidor de ficheiros. Siga os passos [neste tutorial](tutorial-vmware-to-azure.md).
* Utilize o File Sync para replicar os ficheiros da máquina que funciona como servidor de ficheiros para a cloud.
* Utilize a funcionalidade de plano de recuperação no Site Recovery para adicionar scripts para montar a partilha de ficheiros do Azure na VM de servidor de ficheiros com ativação pós-falha no Azure.

Siga os passos abaixo para utilizar o File Sync:

1. [Crie uma conta de armazenamento no Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Se tiver escolhido o armazenamento georredundante de acesso de leitura (recomendado) para as suas contas de armazenamento, obtém acesso de leitura aos seus dados a partir da região secundária, em caso de desastre. Para obter mais informações, consulte [recuperação após desastre e ativação pós-falha forçada (pré-visualização) no armazenamento do Azure](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2ffiless%2ftoc.json)....
2. [Crie uma partilha de ficheiros](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Implemente o File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) no servidor de ficheiros no local.
4. Crie um grupo de sincronização. Os pontos finais num grupo de sincronização são mantidos em sincronia entre si. Os grupos de sincronização têm de conter, pelo menos, um ponto final da cloud, que representa uma partilha de ficheiros do Azure. Também têm de conter um ponto final de servidor, que representa um caminho no servidor do Windows no local.
5. Os ficheiros são agora mantidos em sincronia na partilha de ficheiros do Azure e no servidor no local.
6. Em caso de desastre no ambiente no local, utilize um [plano de recuperação](site-recovery-create-recovery-plans.md) para fazer uma ativação pós-falha. Adicione o script para montar a partilha de ficheiros do Azure e aceder à partilha na sua máquina virtual.

> [!NOTE]
> Confirme que a porta 445 está aberta. Os Ficheiros do Azure utilizam o protocolo SMB. O SMB comunica através da porta TCP 445. Veja se a sua firewall não está a bloquear a porta TCP 445 numa máquina cliente.


## <a name="do-a-test-failover"></a>Fazer uma ativação pós-falha de teste

1. Aceda ao portal do Azure e selecione o seu cofre dos Serviços de Recuperação.
2. Selecione o plano de recuperação criado para o ambiente do servidor de ficheiros.
3. Selecione **Ativação Pós-falha de Teste**.
4. Selecione o ponto de recuperação e a rede virtual do Azure, para iniciar o processo de ativação pós-falha de teste.
5. Quando o ambiente secundário estiver em funcionamento, execute as suas validações.
6. Depois de concluídas as validações, selecione **Limpar ativação pós-falha de teste** no plano de recuperação e o ambiente do teste é limpo.

Para obter mais informações sobre como realizar uma ativação pós-falha de teste, veja [Test failover to Site Recovery](site-recovery-test-failover-to-azure.md) (Ativação pós-falha de teste para o Site Recovery).

Para obter orientações sobre como realizar a ativação pós-falha de teste para o Active Directory e o DNS, veja [Test failover considerations for Active Directory and DNS](site-recovery-active-directory.md) (Considerações da ativação pós-falha de teste para o Active Directory e o DNS).

## <a name="do-a-failover"></a>Fazer uma ativação pós-falha

1. Aceda ao portal do Azure e selecione o seu cofre dos Serviços de Recuperação.
2. Selecione o plano de recuperação criado para o ambiente do servidor de ficheiros.
3. Selecione **Ativação pós-falha**.
4. Selecione o ponto de recuperação para iniciar o processo de ativação pós-falha.

Para obter mais informações sobre como realizar uma ativação pós-falha, veja [Failover in Site Recovery](site-recovery-failover.md) (Ativação pós-falha no Site Recovery).
