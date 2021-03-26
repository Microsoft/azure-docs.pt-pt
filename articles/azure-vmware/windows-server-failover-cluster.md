---
title: Windows Server Failover Cluster em Azure VMware Solution vSAN com discos partilhados nativos
description: Configurar o Windows Server Failover Cluster (WSFC) na Solução VMware Azure e tirar partido de soluções que requerem capacidade WSFC.
ms.topic: how-to
ms.date: 03/09/2021
ms.openlocfilehash: 8162e15675d8bbde9267126c785f152d1cb860bd
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562244"
---
# <a name="windows-server-failover-cluster-on-azure-vmware-solution-vsan-with-native-shared-disks"></a>Windows Server Failover Cluster em Azure VMware Solution vSAN com discos partilhados nativos

Neste artigo, vamos percorrer a configuração do Windows Server Failover Cluster na Solução VMware Azure. A implementação neste artigo é para provar o conceito e os fins-piloto. Recomendamos a utilização de uma configuração Cluster-in-a-Box (CIB) até que as políticas de colocação estejam disponíveis.

O Windows Server Failover Cluster (WSFC), anteriormente conhecido como Microsoft Service Cluster Service Service (MSCS), é uma funcionalidade do Sistema Operativo do Servidor do Windows (OS). O WSFC é uma característica crítica ao negócio, e para muitas aplicações é necessário. Por exemplo, o WSFC é necessário para as seguintes configurações:

- Servidor SQL configurado como:
  - Sempre em Caso de Cluster Failover (FCI), por exemplo, alta disponibilidade ao nível.
  - Sempre On Availability Group (AG), para uma elevada disponibilidade de nível de base de dados.
- Serviços de ficheiros windows:
  - Partilha de ficheiros genéricos em execução no nó de cluster ativo.
  - Scale-Out File Server (SOFS), que armazena ficheiros em volumes compartilhados de cluster (CSV).
  - Espaços de armazenamento direto (S2D); discos locais usados para criar piscinas de armazenamento em diferentes nós de cluster.

Pode hospedar o cluster WSFC em diferentes instâncias da Solução VMware Azure, conhecida como Cluster-Across-Box (CAB). Também pode colocar o cluster WSFC num único nó de Solução VMware Azure. Esta configuração é conhecida como Cluster-in-a-Box (CIB). Não recomendamos a utilização de uma solução CIB para uma implementação de produção. Se o único nó de Solução VMware Azure falhasse, todos os nós de cluster WSFC seriam desligados, e a aplicação experimentaria tempo de inatividade. A Azure VMware Solution requer um mínimo de três nós num aglomerado de nuvem privada.

É importante implementar uma configuração WSFC suportada. Vai querer que a sua solução seja suportada na vSphere e com a Azure VMware Solution. A VMware fornece um documento detalhado sobre o WSFC no vSphere 6.7, [Configuração para Cluster de Failover e Microsoft Cluster Service](https://docs.vmware.com/en/VMware-vSphere/6.7/vsphere-esxi-vcenter-server-67-setup-mscs.pdf).

Este artigo centra-se no WSFC no Windows Server 2016 e no Windows Server 2019. As versões mais antigas do Windows Server estão fora do [suporte mainstream](https://support.microsoft.com/lifecycle/search?alpha=windows%20server) e por isso não as consideramos aqui.

Primeiro terá de [criar um WSFC.](/windows-server/failover-clustering/create-failover-cluster) Para obter mais informações sobre o WSFC, consulte [o Clustering failover no Windows Server](/windows-server/failover-clustering/failover-clustering-overview). Utilize as informações que fornecemos neste artigo para as especificidades de uma implementação WSFC na Solução VMware Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Ambiente de solução Azure VMware
- Mídia de instalação do Microsoft Windows Server OS

## <a name="reference-architecture"></a>Arquitetura de referência

A Azure VMware Solution fornece suporte nativo para WSFC virtualizado. Suporta reservas persistentes SCSI-3 (SCSI3PR) a nível de disco virtual. Este suporte é exigido pela WSFC para arbitrar o acesso a um disco partilhado entre nós. O suporte de SCSI3PRs permite a configuração do WSFC com um recurso de disco partilhado entre VMs de forma nativa nas datastores vSAN.

O diagrama seguinte ilustra a arquitetura dos nós virtuais WSFC numa nuvem privada Azure VMware Solution. Mostra onde reside a Azure VMware Solution, incluindo os servidores virtuais WSFC (caixa vermelha), em relação à plataforma Azure mais ampla. Este diagrama ilustra uma arquitetura típica de centro-falado, mas uma configuração semelhante é possível com o uso de Azure Virtual WAN. Ambos oferecem todo o valor que outros serviços Azure podem trazer-lhe.

[![Diagrama mostrando a arquitetura dos nós virtuais WSFC numa nuvem privada Azure VMware Solution.](media/windows-server-failover-cluster/windows-server-failover-architecture.png)](media/windows-server-failover-cluster/windows-server-failover-architecture.png#lightbox)

## <a name="supported-configurations"></a>Configurações suportadas

Atualmente, as seguintes configurações são suportadas:

- Microsoft Windows Server 2012 ou mais tarde
- Até cinco nódoas de agrupamento de failover por cluster
- Até quatro adaptadores PVSCSI por VM
- Até 64 discos por adaptador PVSCSI

## <a name="virtual-machine-configuration-requirements"></a>Requisitos de configuração da Máquina Virtual

### <a name="wsfc-node-configuration-parameters"></a>Parâmetros de configuração do nó WSFC

- Instale as ferramentas VMware mais recentes em cada nó WSFC.
- Misturar discos não partilhados e partilhados num único adaptador SCSI virtual não é suportado. Por exemplo, se o disco do sistema (unidade C:) está anexado ao SCSI0:0, o primeiro disco partilhado seria anexado ao SCSI1:0. Um nó VM de um WSFC tem o mesmo controlador SCSI virtual máximo que um VM comum - até quatro (4) controladores SCSI virtuais.
- Os discos virtuais SCSI IDs devem ser consistentes entre todos os VMs que hospedam nós do mesmo WSFC.

| **Componente** | **Requisitos** |
| --- | --- |
| Versão de hardware VM | 11 ou mais para apoiar Live vMotion. |
| Virtual NIC | Cartão de interface de rede paravirtualizado VMXNET3 (NIC); ativar o Windows Receive Side Scaling (RSS) no NIC virtual. |
| Memória | Utilize a memória completa da reserva VM para os nós no cluster WSFC. |
| Aumente o tempo limite de E/S de cada nó WSFC. | Modificar a máquina local do \_ HKEY\System\CurrentControlSet\Services\Disk\TimeOutValueSet \_ para 60 segundos ou mais. (Se recriar o cluster, este valor pode ser reposto para o seu padrão, por isso deve alterá-lo novamente.) |
| Monitorização de saúde do cluster windows | O valor do Parâmetro SameSubnetThreshold da monitorização de saúde do cluster Windows deve ser modificado para permitir 10 batimentos cardíacos perdidos no mínimo. Este é [o padrão no Windows Server 2016](https://techcommunity.microsoft.com/t5/failover-clustering/tuning-failover-cluster-network-thresholds/ba-p/371834). Esta recomendação aplica-se a todas as aplicações que utilizam o WSFC, incluindo discos partilhados e não partilhados. |

### <a name="wsfc-node---boot-disks-configuration-parameters"></a>Nó WSFC - Parâmetros de configuração dos discos de arranque


| **Componente** | **Requisitos** |
| --- | --- |
| Tipo de Controlador SCSI | LSI Logic SAS |
| Modo disco | Virtual |
| Partilha de ônibus da SCSI | Nenhum |
| Modifique as definições avançadas para um controlador SCSI virtual que hospeda o dispositivo de arranque. | Adicione as seguintes definições avançadas a cada nó WSFC:<br /> scsiX.returnNoConnectDuringAPD = "TRUE"<br />scsiX.returnBusyOnNoConnectStatus = "FALSE"<br />Onde X é o dispositivo de arranque SCSI número de identificação do controlador de autocarros. Por predefinição, X está definido para 0. |

### <a name="wsfc-node---shared-disks-configuration-parameters"></a>Nó WSFC - Parâmetros de configuração de discos partilhados


| **Componente** | **Requisitos** |
| --- | --- |
| Tipo de Controlador SCSI | VMware Paravirtualize (PVSCSI) |
| Modo disco | Independente - Persistente (passo 2 na ilustração abaixo). Ao utilizar esta definição, certifique-se de que todos os discos estão excluídos das imagens. As fotos não são suportadas para VMs baseados no WSFC. |
| Partilha de ônibus da SCSI | Físico (passo 1 na ilustração abaixo) |
| Bandeira multi-escritora | Não utilizado |
| Formato de disco | Espesso a provisionado. (Eager Zeroed Thick (EZT) não é necessário com vSAN.) |

:::image type="content" source="media/windows-server-failover-cluster/edit-settings-virtual-hardware.png" alt-text="Uma imagem mostrando a página de Definições de Edição para hardware virtual.":::

## <a name="non-supported-scenarios"></a>Cenários não apoiados

As seguintes funcionalidades não são suportadas para o WSFC na Solução VMware Azure:

- Lojas de dados NFS
- Espaços de Armazenamento
- vSAN usando o serviço iSCSI
- vSAN Stretched Cluster
- Compatibilidade reforçada vMotion (EVC)
- v Tolerância à Falha da Sphere (FT)
- Instantâneos
- Live (online) armazenamento vMotion
- Virtualização de ID N-Port (NPIV)

Alterações quentes no hardware da máquina virtual podem perturbar o batimento cardíaco entre os nós WSFC.

As seguintes atividades não são suportadas e podem causar o fracasso do nó WSFC:

- Memória de adição quente
- CPU de adição quente
- Usando instantâneos
- Aumentando o tamanho de um disco partilhado
- Pausa e retoma do estado da máquina virtual
- Excesso de compromisso de memória que conduz à troca de ESXi ou balão de memória VM
- Hot Extend Local VMDK arquivo, mesmo que não esteja associado com o controlador de partilha de ônibus SCSI

## <a name="configure-wsfc-with-shared-disks-on-azure-vmware-solution-vsan"></a>Configure o WSFC com discos partilhados na Azure VMware Solution vSAN

1. Certifique-se de que existe um ambiente de Diretório Ativo.
2. Crie máquinas virtuais (VMs) na loja de dados vSAN.
3. Ligue em todos os VMs, configuure o nome de anfitrião, endereços IP, junte todos os VMs a um domínio de Diretório Ativo e instale as últimas atualizações de SISTEMA disponíveis.
4. Instale as mais recentes Ferramentas VMware.
5. Ativar e configurar a função de Cluster de Falha do Servidor do Windows em cada VM.
6. Configure uma Testemunha de Cluster para quórum (uma testemunha de partilha de ficheiros funciona bem).
7. Desligue todos os nós do aglomerado WSFC.
8. Adicione um ou mais controladores PARAvirtual SCSI (até quatro) a cada parte VM do WSFC. Utilize as definições nos parágrafos anteriores.
9. No primeiro nó de cluster, adicione todos os discos partilhados necessários usando adicionar disco rígido **de novo dispositivo**  >  . A partilha do disco deve ser deixada como **Não Especificado** (padrão) e modo disco como Independente **- Persistente**. Fixe-o ao controlador(s) criado nos passos anteriores.
10. Continue com os restantes nós WSFC. Adicione os discos criados no passo anterior selecionando **Adicionar Novo Dispositivo** Disco Rígido  >  **Existente**. Certifique-se de que mantém os mesmos SCSI IDs em todos os nós WSFC.
11. Ligue o primeiro nó WSFC; iniciar e abrir a consola de gestão de discos (mmc). Certifique-se de que os discos partilhados adicionados podem ser geridos pelo SO e são inicializados. Formatar os discos e atribuir uma letra de unidade.
12. Energia nos outros nós WSFC.
13. Adicione o disco ao cluster WSFC utilizando o **assistente de adicionar discos** e adicione-o a um Volume Partilhado de Cluster.
14. Teste uma falha utilizando o **assistente de disco Move** e certifique-se de que o cluster WSFC com discos partilhados funciona corretamente.
15. Executar o **assistente de Cluster de Validação** para confirmar se o cluster e os seus nós estão a funcionar corretamente.

    É importante manter em mente os seguintes itens específicos do teste de Validação do Cluster:

       - **Validar a Reserva Persistente dos Espaços de Armazenamento.** Se não estiver a utilizar espaços de armazenamento com o seu cluster (como no Azure VMware Solution vSAN), este teste não é aplicável. Pode ignorar quaisquer resultados do teste de Reserva Persistente dos Espaços de Armazenamento Validados, incluindo este aviso. Para evitar avisos, pode excluir este teste.
        
      - **Validar comunicação de rede.** O teste de Validação do Cluster lançará um aviso de que apenas uma interface de rede por nó de cluster está disponível. Pode ignorar este aviso. A Azure VMware Solution fornece a disponibilidade e desempenho necessários, uma vez que os nós estão ligados a um dos segmentos NSX-T. No entanto, mantenha este item como parte do teste de Validação do Cluster, uma vez que validará outros aspetos da comunicação da rede.

16. Crie uma regra de DRS para colocar os VMs WSFC nos mesmos nós de Solução VMware Azure. Para tal, precisa de uma regra de afinidade hospedeira-vm. Desta forma, os nós de cluster serão executados no mesmo anfitrião Azure VMware Solution. Mais uma vez, isto é para fins piloto até que as políticas de colocação estejam disponíveis.

    >[!NOTE]
    > Para isso é necessário criar um bilhete Pedido de Apoio. A nossa organização de apoio Azure poderá ajudá-lo com isto.

## <a name="related-information"></a>Informações relacionadas

- [Clustering de Ativação Pós-falha no Windows Server](/windows-server/failover-clustering/failover-clustering-overview)
- [Diretrizes para o Clustering da Microsoft sobre vSphere (1037959) (vmware.com)](https://kb.vmware.com/s/article/1037959)
- [Sobre a configuração para o cluster de failover e o serviço de cluster da Microsoft (vmware.com)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.mscs.doc/GUID-1A2476C0-CA66-4B80-B6F9-8421B6983808.html)
- [vSAN 6.7 U3 - WSFC com Discos Partilhados &amp; Reservas Persistentes SCSI-3 (vmware.com)](https://blogs.vmware.com/virtualblocks/2019/08/23/vsan67-u3-wsfc-shared-disksupport/)
- [Limites de solução Azure VMware](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-vmware-solution-limits)

## <a name="next-steps"></a>Passos seguintes

Agora que cobriu a criação de um WSFC na Solução VMware Azure, talvez queira saber:

- Configurar o seu novo WSFC adicionando mais aplicações que requerem a capacidade WSFC. Por exemplo, SQL Server e SAP ASCS.
- A preparar uma solução de reserva.
  - [Configuração do Servidor de Backup Azure para solução VMware Azure](./set-up-backup-server-for-azure-vmware-solution.md)
  - [Soluções de backup para máquinas virtuais Azure VMware Solution](./ecosystem-back-up-vms.md)