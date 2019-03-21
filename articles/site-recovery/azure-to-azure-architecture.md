---
title: Arquitetura de replicação do Azure para o Azure no Azure Site Recovery | Documentos da Microsoft
description: Este artigo fornece uma visão geral dos componentes e da arquitetura utilizada ao definir a recuperação após desastre entre regiões do Azure para VMs do Azure, com o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/31/2018
ms.author: raynew
ms.openlocfilehash: ef75ec40df50931f5a49c06184c61d2f78608dcf
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58015005"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Arquitetura da recuperação após desastre do Azure


Este artigo descreve a arquitetura, componentes e processos utilizados ao implementar a recuperação após desastre para máquinas virtuais do Azure (VMs) com o [do Azure Site Recovery](site-recovery-overview.md) serviço. Com a recuperação de desastres, configurar, as VMs do Azure continuamente replicar a partir de uma região de destino diferente. Se ocorrer uma falha, pode efetuar a ativação pós-falha de VMs para a região secundária e aceder aos mesmos a partir daí. Quando tudo o que está a funcionar normalmente novamente, pode efetuar a reativação pós-falha e continuar a trabalhar na localização primária.



## <a name="architectural-components"></a>Componentes da arquitetura

Os componentes envolvidos na recuperação após desastre para VMs do Azure estão resumidos na tabela seguinte.

**Componente** | **Requisitos**
--- | ---
**VMs na região de origem** | Um dos mais VMs do Azure numa [suportada região de origem](azure-to-azure-support-matrix.md#region-support).<br/><br/> As VMs podem executar qualquer [sistema operativo suportado](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).
**Armazenamento de VM de origem** | VMs do Azure podem ser geridas, ou se disseminar de discos não geridos em contas de armazenamento.<br/><br/>[Saiba mais sobre](azure-to-azure-support-matrix.md#replicated-machines---storage) suportado o armazenamento do Azure.
**Redes VM de origem** | As VMs podem ser localizadas num ou mais sub-redes numa rede virtual (VNet) na região de origem. [Saiba mais](azure-to-azure-support-matrix.md#replicated-machines---networking) sobre requisitos de rede.
**Conta de armazenamento de cache** | Precisa de uma conta de armazenamento de cache na rede de origem. Durante a replicação, alterações VM são armazenadas na cache antes de serem enviados para o armazenamento de destino.<br/><br/> Utilizar uma cache garante um impacto mínimo sobre as aplicações de produção em execução numa VM.<br/><br/> [Saiba mais](azure-to-azure-support-matrix.md#cache-storage) sobre os requisitos de armazenamento de cache. 
**Recursos de destino** | Recursos de destino são utilizados durante a replicação e quando ocorre uma ativação pós-falha. Recuperação de sites pode configurar o recurso de destino por predefinição, ou pode criar/personalizá-las.<br/><br/> Na região de destino, verifique que pode criar VMs e de que a sua subscrição tem recursos suficientes para suportar tamanhos de VM que serão necessários na região de destino. 

![Replicação de origem e destino](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

## <a name="target-resources"></a>Recursos de destino

Quando ativa a replicação para uma VM, o Site Recovery dá-lhe a opção de criação automática de recursos de destino. 

**Recurso de destino** | **Definição predefinida**
--- | ---
**Subscrição de destino** | Mesmo que a subscrição de origem.
**Grupo de recursos de destino** | O grupo de recursos ao qual as VMs pertencem após a ativação pós-falha.<br/><br/> Ele pode estar em qualquer região do Azure, exceto a região de origem.<br/><br/> Site Recovery cria um novo grupo de recursos na região de destino, com um sufixo "asr".<br/><br/>
**VNet de destino** | A rede virtual (VNet) no qual as VMs replicadas se encontram após a ativação pós-falha. É criado um mapeamento de rede entre redes virtuais de origem e destino e vice-versa.<br/><br/> Site Recovery cria uma nova VNet e sub-rede, com o sufixo "asr".
**Conta de armazenamento de destino** |  Se a VM não utilizar um disco gerido, esta é a conta de armazenamento para o qual os dados são replicados.<br/><br/> Site Recovery cria uma nova conta de armazenamento na região de destino, para espelhar a conta de armazenamento de origem.
**Discos geridos de réplica** | Se a VM utiliza um disco gerido, trata-se os discos geridos para que os dados são replicados.<br/><br/> Site Recovery cria discos geridos de réplica na região de armazenamento para espelhar a origem.
**Conjuntos de disponibilidade de destino** |  Conjunto de disponibilidade na qual as VMs de replicação estão localizadas após a ativação pós-falha.<br/><br/> Site Recovery cria um conjunto de disponibilidade na região de destino com o sufixo "asr", para as VMs que estão localizadas num conjunto de disponibilidade na localização de origem. Se existir um conjunto de disponibilidade, é utilizado e não é criado um novo.
**Zonas de disponibilidade de destino** | Se a região de destino suporta zonas de disponibilidade, recuperação de Site atribui o mesmo número de zona que foi utilizado na região de origem.

### <a name="managing-target-resources"></a>Gerir recursos de destino

Pode gerir os recursos de destino da seguinte forma:

- Como ativar a replicação, é possível modificar as definições de destino.
- Pode modificar as definições de destino após a replicação já está a funcionar. A exceção é o tipo de disponibilidade (instância única, conjunto ou zona). Para alterar esta definição que tem de desativar a replicação, modificar a definição e, em seguida, reativar.



## <a name="replication-policy"></a>Política de replicação 

Quando ativa a replicação de VM do Azure, por predefinição Site Recovery cria uma nova política de replicação com as predefinições resumidas na tabela.

**Definição de política** | **Detalhes** | **Predefinição**
--- | --- | ---
**Retenção do ponto de recuperação** | Especifica o tempo que o Site Recovery mantém os pontos de recuperação | 24 horas
**Frequência de instantâneo consistente da aplicação** | A frequência com que o Site Recovery tira um instantâneo consistente com a aplicação. | Cada 60 minutos.

### <a name="managing-replication-policies"></a>Gestão de políticas de replicação

Pode gerir e modificar as predefinições das políticas de replicação da seguinte forma:
- Pode modificar as definições, como ativar a replicação.
- Pode criar uma política de replicação em qualquer altura e, em seguida, aplicá-la quando ativa a replicação.

### <a name="multi-vm-consistency"></a>Consistência de multi-VMs

Se pretender que as VMs para replicar em conjunto e partilhar consistentes de falhas e pontos de recuperação consistente com a aplicação a ativação pós-falha, pode recolhê-los em conjunto num grupo de replicação. Consistência multi-VM afeta o desempenho da carga de trabalho e só deve ser utilizada para VMs com cargas de trabalho que necessitam de consistência em todas as máquinas. 



## <a name="snapshots-and-recovery-points"></a>Os instantâneos e pontos de recuperação

Pontos de recuperação são criados a partir de instantâneos de discos VM executados num ponto específico no tempo. Quando realizar a ativação pós-falha numa VM, utilize um ponto de recuperação para restaurar a VM na localização de destino.

Quando efetuar a ativação pós-falha, em geral queremos garantir que a VM é iniciado sem corrupção ou perda de dados e de que os dados da VM são consistentes para o sistema operativo e para aplicações que são executadas na VM. Isso depende do tipo de instantâneos tirados.

O site Recovery tira instantâneos da seguinte forma:

1. Site Recovery tira instantâneos consistentes com falhas de dados por padrão e instantâneos consistentes com a aplicação, se especificar uma frequência para eles.
2. Pontos de recuperação são criados a partir de instantâneos e armazenados de acordo com as definições de retenção na política de replicação.

### <a name="consistency"></a>Consistência

A tabela seguinte explica os diferentes tipos de consistência.

### <a name="crash-consistent"></a>Consistente com a Falha

**Descrição** | **Detalhes** | **Recomendação**
--- | --- | ---
Um instantâneo consistente de falhas captura de dados que se encontravam no disco quando o instantâneo foi tirado. Ele não inclui qualquer coisa na memória.<br/><br/> Ela contém o equivalente dos dados no disco que estarão presentes se a VM falhou ou o cabo de alimentação foi obtido a partir do servidor no instante em que o instantâneo foi tirado.<br/><br/> Um consistentes de falhas não garantem a consistência de dados para o sistema operativo, ou para aplicações na VM. | Site Recovery cria pontos de recuperação consistentes com falhas em cinco minutos por predefinição. Não é possível modificar esta definição.<br/><br/>  | Hoje em dia, a maioria das aplicações pode recuperar bem a partir de pontos de consistentes de falhas.<br/><br/> Pontos de recuperação consistentes com falhas, geralmente são suficientes para a replicação de sistemas operativos e aplicações, tais como servidores DHCP e servidores de impressão.

### <a name="app-consistent"></a>Consistente com a Aplicação

**Descrição** | **Detalhes** | **Recomendação**
--- | --- | ---
Pontos de recuperação consistente com a aplicação são criados a partir de instantâneos consistentes com a aplicação.<br/><br/> Um instantâneo consistente com a aplicação contém todas as informações num instantâneo consistente de falhas, além de todos os dados na memória e de transações em curso. | Instantâneos consistentes com a aplicação utilizam o serviço de cópia de sombra de volumes (VSS):<br/><br/>   1) quando um instantâneo é iniciado, o VSS efetuar uma operação de (COW) de cópia ao escrever no volume.<br/><br/>   2) antes de executar dos ovos de ouro, o VSS informa todas as aplicações na máquina que necessita para liberar os dados de memória residente no disco.<br/><br/>   3) o VSS, em seguida, permite que a aplicação de recuperação após desastre/cópia de segurança (no caso este Site Recovery) para ler os dados de instantâneo e continuar. | São tirados instantâneos consistentes com a aplicação em conformidade com a frequência que especificar. Esta frequência deve ser sempre menor do que definir a retenção de pontos de recuperação. Por exemplo, se mantiver os pontos de recuperação com as definições predefinidas de 24 horas, deve definir a frequência em menos de 24 horas.<br/><br/>Estão mais complexos e demorar mais tempo a concluir do que os instantâneos consistentes com falhas.<br/><br/> Elas afetam o desempenho de aplicações em execução numa VM ativada para replicação. 

## <a name="replication-process"></a>Processo de replicação

Ao ativar a replicação para uma VM do Azure, ocorrerá o seguinte:

1. A extensão de serviço de mobilidade de recuperação de Site é automaticamente instalada na VM.
2. A extensão registra a VM com o Site Recovery.
3. Começa a replicação contínua para a VM.  Escritas de disco imediatamente são transferidas para a conta de armazenamento de cache na localização de origem.
4. Recuperação de site processa os dados na cache e envia-os para a conta de armazenamento de destino ou para a réplica de discos geridos.
5. Depois dos dados são processados, pontos de recuperação consistentes com falhas são gerados a cada cinco minutos. Pontos de recuperação consistente com a aplicação são gerados, de acordo com a definição especificada na política de replicação.

![Ativar o processo de replicação, o passo 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

**Processo de replicação**

## <a name="connectivity-requirements"></a>Requisitos de conectividade

 As VMs do Azure, replicar tem conectividade de saída. Recuperação de site não tem necessidade conectividade de entrada para a VM. 

### <a name="outbound-connectivity-urls"></a>Conectividade de saída (URLs)

Se o acesso de saída para VMs é controlado com URLs, que estes URLs.

| **URL** | **Detalhes** |
| ------- | ----------- |
| *.blob.core.windows.net | Permite que os dados sejam escritos da VM para a conta de armazenamento em cache na região de origem. |
| login.microsoftonline.com | Fornece autorização e autenticação para os URLs do serviço Site Recovery. |
| *.hypervrecoverymanager.windowsazure.com | Permite que a VM comunique com o serviço Site Recovery. |
| *.servicebus.windows.net | Permite que a VM escreva dados de monitorização e diagnóstico do Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividade de saída para intervalos de endereços IP

Para controlar a conectividade de saída para VMs que utilizem endereços IP, permita estes endereços.

#### <a name="source-region-rules"></a>Regras de região de origem

**Regra** |  **Detalhes** | **Etiqueta de serviço**
--- | --- | --- 
Permitir HTTPS a saída: porta 443 | Permitir intervalos que correspondem às contas de armazenamento na região de origem | Armazenamento. < nome da região >.
Permitir HTTPS a saída: porta 443 | Permita intervalos que correspondem ao Azure Active Directory (Azure AD).<br/><br/> Se o Azure AD endereços são adicionados no futuro que tem de criar novas regras do grupo de segurança de rede (NSG).  | AzureActiveDirectory
Permitir HTTPS a saída: porta 443 | Permitir o acesso ao [pontos de extremidade do Site Recovery](https://aka.ms/site-recovery-public-ips) que correspondem à localização de destino. 

#### <a name="target-region-rules"></a>Regras de região de destino

**Regra** |  **Detalhes** | **Etiqueta de serviço**
--- | --- | --- 
Permitir HTTPS a saída: porta 443 | Permita intervalos que correspondem às contas de armazenamento na região de destino. | Armazenamento. < nome da região >.
Permitir HTTPS a saída: porta 443 | Permita intervalos que correspondem ao Azure AD.<br/><br/> Se os endereços do Azure AD são adicionados no futuro que tem de criar novas regras NSG.  | AzureActiveDirectory
Permitir HTTPS a saída: porta 443 | Permitir o acesso ao [pontos de extremidade do Site Recovery](https://aka.ms/site-recovery-public-ips) que correspondem para a localização de origem. 


#### <a name="control-access-with-nsg-rules"></a>Controlar o acesso com regras NSG

Se controlar a conectividade de VM ao filtrar o tráfego de rede de e para redes do Azure/sub-redes usando [regras do NSG](https://docs.microsoft.com/azure/virtual-network/security-overview), tenha em atenção os seguintes requisitos:

- As regras do NSG para a região do Azure de origem devem permitir acesso de saída para o tráfego de replicação.
- Recomendamos que crie regras num ambiente de teste antes de colocá-los em produção.
- Uso [etiquetas de serviço](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) em vez de permitir endereços IP individuais.
    - Etiquetas de serviço representam um grupo de prefixos de endereços IP reunidas em conjunto para minimizar a complexidade ao criar regras de segurança.
    - Microsoft atualiza automaticamente as etiquetas de serviço ao longo do tempo. 
 
Saiba mais sobre [conectividade de saída](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) para o Site Recovery, e [controlar a conectividade com NSGs](concepts-network-security-group-with-site-recovery.md).


### <a name="connectivity-for-multi-vm-consistency"></a>Conectividade de consistência de multi-VMS

Se ativar a consistência multi-VM, as máquinas no grupo de replicação comunicam entre si pela porta 20004.
- Certifique-se de que não há nenhum dispositivo de firewall a bloquear a comunicação interna entre as VMs através da porta 20004.
- Se pretender que as VMs do Linux pertençam a um grupo de replicação, certifique-se de que o tráfego de saída na porta 20004 é aberto manualmente de acordo com as orientações da versão específica do Linux.




## <a name="failover-process"></a>Processo de ativação pós-falha

Quando iniciar uma ativação pós-falha, as VMs são criadas no grupo de recursos de destino, a rede virtual de destino, a sub-rede de destino e no conjunto de disponibilidade de destino. Durante uma ativação pós-falha, pode utilizar qualquer ponto de recuperação.

![Processo de ativação pós-falha](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Passos Seguintes

[Replicar rapidamente](azure-to-azure-quickstart.md) uma VM do Azure para uma região secundária.
