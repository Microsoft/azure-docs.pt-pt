---
title: Arquitetura de recuperação de desastre do Azure para o Azure no Azure Site Recovery
description: Visão geral da arquitetura usada quando você configura a recuperação de desastre entre regiões do Azure para VMs do Azure, usando o serviço de Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 1/08/2020
ms.author: raynew
ms.openlocfilehash: e5fdf0a14586a0a2ea97d222f4be481e8fe31e51
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754510"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Arquitetura da recuperação após desastre do Azure para o Azure


Este artigo descreve a arquitetura, os componentes e os processos usados quando você implanta a recuperação de desastres para VMs (máquinas virtuais) do Azure usando o serviço de [Azure site Recovery](site-recovery-overview.md) . Com a recuperação de desastres configurada, as VMs do Azure são replicadas continuamente de para uma região de destino diferente. Se ocorrer uma interrupção, você poderá fazer failover de VMs para a região secundária e acessá-las a partir daí. Quando tudo estiver executando normalmente novamente, você poderá fazer failback e continuar trabalhando no local principal.



## <a name="architectural-components"></a>Componentes da arquitetura

Os componentes envolvidos na recuperação de desastre para VMs do Azure são resumidos na tabela a seguir.

**Componente** | **Requisitos**
--- | ---
**VMs na região de origem** | Uma ou mais VMs do Azure em uma [região de origem com suporte](azure-to-azure-support-matrix.md#region-support).<br/><br/> As VMs podem estar executando qualquer [sistema operacional com suporte](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).
**Armazenamento de VM de origem** | As VMs do Azure podem ser gerenciadas ou ter discos não gerenciados distribuídos entre contas de armazenamento.<br/><br/>[Saiba mais sobre](azure-to-azure-support-matrix.md#replicated-machines---storage) o armazenamento do Azure com suporte.
**Redes VM de origem** | As VMs podem estar localizadas em uma ou mais sub-redes em uma rede virtual (VNet) na região de origem. [Saiba mais](azure-to-azure-support-matrix.md#replicated-machines---networking) sobre os requisitos de rede.
**Conta de armazenamento em cache** | Você precisa de uma conta de armazenamento em cache na rede de origem. Durante a replicação, as alterações de VM são armazenadas no cache antes de serem enviadas ao armazenamento de destino.  As contas de armazenamento em cache devem ser padrão.<br/><br/> O uso de um cache garante um impacto mínimo sobre os aplicativos de produção em execução em uma VM.<br/><br/> [Saiba mais](azure-to-azure-support-matrix.md#cache-storage) sobre os requisitos de armazenamento em cache. 
**Recursos de destino** | Os recursos de destino são usados durante a replicação e quando ocorre um failover. Site Recovery pode configurar o recurso de destino por padrão, ou você pode criá-los/personalizá-los.<br/><br/> Na região de destino, verifique se você é capaz de criar VMs e se sua assinatura tem recursos suficientes para dar suporte a tamanhos de VM que serão necessários na região de destino. 

![Replicação de origem e de destino](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

## <a name="target-resources"></a>Recursos de destino

Quando você habilita a replicação para uma VM, Site Recovery oferece a opção de criar recursos de destino automaticamente. 

**Recurso de destino** | **Configuração padrão**
--- | ---
**Assinatura de destino** | O mesmo que a assinatura de origem.
**Grupo de recursos de destino** | O grupo de recursos ao qual as VMs pertencem após o failover.<br/><br/> Ele pode estar em qualquer região do Azure, exceto na região de origem.<br/><br/> Site Recovery cria um novo grupo de recursos na região de destino, com um sufixo "ASR".<br/><br/>
**VNet de destino** | A rede virtual (VNet) na qual as VMs replicadas estão localizadas após o failover. Um mapeamento de rede é criado entre as redes virtuais de origem e de destino e vice-versa.<br/><br/> Site Recovery cria uma nova VNet e sub-rede com o sufixo "ASR".
**Conta de armazenamento de destino** |  Se a VM não usar um disco gerenciado, essa será a conta de armazenamento para a qual os dados são replicados.<br/><br/> Site Recovery cria uma nova conta de armazenamento na região de destino para espelhar a conta de armazenamento de origem.
**Discos gerenciados de réplica** | Se a VM usa um disco gerenciado, esses são os discos gerenciados para os quais os dados são replicados.<br/><br/> Site Recovery cria discos gerenciados de réplica na região de armazenamento para espelhar a origem.
**Conjuntos de disponibilidade de destino** |  Conjunto de disponibilidade no qual as VMs de replicação estão localizadas após o failover.<br/><br/> Site Recovery cria um conjunto de disponibilidade na região de destino com o sufixo "ASR", para VMs que estão localizadas em um conjunto de disponibilidade no local de origem. Se houver um conjunto de disponibilidade, ele é usado e um novo não é criado.
**Zonas de disponibilidade de destino** | Se a região de destino oferecer suporte a zonas de disponibilidade, Site Recovery atribuirá o mesmo número de zona usado na região de origem.

### <a name="managing-target-resources"></a>Gerenciando recursos de destino

Você pode gerenciar os recursos de destino da seguinte maneira:

- Você pode modificar as configurações de destino ao habilitar a replicação.
- Você pode modificar as configurações de destino depois que a replicação já estiver funcionando. A exceção é o tipo de disponibilidade (instância única, conjunto ou zona). Para alterar essa configuração, você precisa desabilitar a replicação, modificar a configuração e, em seguida, reabilitar.



## <a name="replication-policy"></a>Política de replicação 

Quando você habilita a replicação de VM do Azure, por padrão Site Recovery cria uma nova política de replicação com as configurações padrão resumidas na tabela.

**Definição de política** | **Detalhes** | **Predefinição**
--- | --- | ---
**Retenção do ponto de recuperação** | Especifica por quanto tempo Site Recovery mantém os pontos de recuperação | 24 horas
**Frequência de instantâneo consistente com o aplicativo** | Com que frequência Site Recovery usa um instantâneo consistente com o aplicativo. | A cada quatro horas

### <a name="managing-replication-policies"></a>Gerenciando políticas de replicação

Você pode gerenciar e modificar as configurações de políticas de replicação padrão da seguinte maneira:
- Você pode modificar as configurações ao habilitar a replicação.
- Você pode criar uma política de replicação a qualquer momento e, em seguida, aplicá-la quando habilitar a replicação.

### <a name="multi-vm-consistency"></a>Consistência de várias VMs

Se você quiser que as VMs se repliquem juntas e tenham pontos de recuperação consistentes e consistentes com o aplicativo e com falhas em um failover, você poderá reuni-los em um grupo de replicação. A consistência de várias VMs afeta o desempenho da carga de trabalho e só deve ser usada para VMs que executam cargas de trabalho que precisam de consistência em todas as máquinas. 



## <a name="snapshots-and-recovery-points"></a>Instantâneos e pontos de recuperação

Os pontos de recuperação são criados a partir de instantâneos de discos de VM feitos em um ponto específico no tempo. Ao fazer failover de uma VM, você usa um ponto de recuperação para restaurar a VM no local de destino.

Ao fazer failover, geralmente queremos garantir que a VM comece sem corrupção ou perda de dados, e que os dados da VM sejam consistentes para o sistema operacional e para aplicativos executados na VM. Isso depende do tipo de instantâneos tirados.

Site Recovery usa instantâneos da seguinte maneira:

1. O Site Recovery usa instantâneos de dados consistentes com falhas por padrão e instantâneos consistentes com o aplicativo se você especificar uma frequência para eles.
2. Os pontos de recuperação são criados a partir dos instantâneos e armazenados de acordo com as configurações de retenção na política de replicação.

### <a name="consistency"></a>Consistência

A tabela a seguir explica os diferentes tipos de consistência.

### <a name="crash-consistent"></a>Com consistência de falhas

**Descrição** | **Detalhes** | **Recomendação**
--- | --- | ---
Um instantâneo com consistência de falha captura dados que estavam no disco quando o instantâneo foi tirado. Ele não inclui nada na memória.<br/><br/> Ele contém o equivalente dos dados em disco que estaria presente se a VM falhasse ou o cabo de alimentação foi puxado do servidor no instante em que o instantâneo foi tirado.<br/><br/> Uma falha consistente não garante a consistência de dados para o sistema operacional ou para aplicativos na VM. | O Site Recovery cria pontos de recuperação consistentes com falhas a cada cinco minutos por padrão. Essa configuração não pode ser modificada.<br/><br/>  | Hoje, a maioria dos aplicativos pode se recuperar bem de pontos consistentes com falhas.<br/><br/> Os pontos de recuperação consistentes com falhas geralmente são suficientes para a replicação de sistemas operacionais e aplicativos como servidores DHCP e servidores de impressão.

### <a name="app-consistent"></a>Consistente com o aplicativo

**Descrição** | **Detalhes** | **Recomendação**
--- | --- | ---
Os pontos de recuperação consistentes com o aplicativo são criados a partir de instantâneos consistentes com o aplicativo.<br/><br/> Um instantâneo consistente com o aplicativo contém todas as informações em um instantâneo consistente com falhas, além de todos os dados na memória e transações em andamento. | Os instantâneos consistentes com o aplicativo usam o Serviço de Cópias de Sombra de Volume (VSS):<br/><br/>   1) quando um instantâneo é iniciado, o VSS executa uma operação vaca (cópia em gravação) no volume.<br/><br/>   2) antes de executar o vaca, o VSS informa todos os aplicativos no computador de que ele precisa para liberar seus dados residentes na memória para o disco.<br/><br/>   3) o VSS permite que o aplicativo de backup/recuperação de desastre (neste caso Site Recovery) Leia os dados do instantâneo e continue. | Os instantâneos consistentes com o aplicativo são obtidos de acordo com a frequência especificada. Essa frequência deve ser sempre menor do que você definiu para reter pontos de recuperação. Por exemplo, se você mantiver pontos de recuperação usando a configuração padrão de 24 horas, deverá definir a frequência em menos de 24 horas.<br/><br/>Elas são mais complexas e demoram mais para serem concluídas do que instantâneos consistentes com falhas.<br/><br/> Eles afetam o desempenho dos aplicativos em execução em uma VM habilitada para replicação. 

## <a name="replication-process"></a>Processo de replicação

Quando você habilita a replicação para uma VM do Azure, acontece o seguinte:

1. A extensão do serviço de mobilidade Site Recovery é instalada automaticamente na VM.
2. A extensão registra a VM com Site Recovery.
3. A replicação contínua começa para a VM.  Gravações de disco são imediatamente transferidas para a conta de armazenamento de cache no local de origem.
4. Site Recovery processa os dados no cache e os envia para a conta de armazenamento de destino ou para os discos gerenciados de réplica.
5. Depois que os dados são processados, pontos de recuperação consistentes com falha são gerados a cada cinco minutos. Os pontos de recuperação consistentes com o aplicativo são gerados de acordo com a configuração especificada na política de replicação.

![Habilitar processo de replicação, etapa 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

**Processo de replicação**

## <a name="connectivity-requirements"></a>Requisitos de conectividade

 As VMs do Azure replicadas precisam de conectividade de saída. Site Recovery nunca precisa de conectividade de entrada para a VM. 

### <a name="outbound-connectivity-urls"></a>Conectividade de saída (URLs)

Se o acesso de saída para VMs for controlado com URLs, permita essas URLs.

| **URL** | **Detalhes** |
| ------- | ----------- |
| *.blob.core.windows.net | Permite que os dados sejam escritos da VM para a conta de armazenamento em cache na região de origem. |
| login.microsoftonline.com | Fornece autorização e autenticação para os URLs do serviço Site Recovery. |
| *.hypervrecoverymanager.windowsazure.com | Permite que a VM comunique com o serviço Site Recovery. |
| *.servicebus.windows.net | Permite que a VM escreva dados de monitorização e diagnóstico do Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividade de saída para intervalos de endereços IP

Para controlar a conectividade de saída para VMs usando endereços IP, permita esses endereços.
Observe que os detalhes dos requisitos de conectividade de rede podem ser encontrados no [White Paper de rede](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) 

#### <a name="source-region-rules"></a>Regras de região de origem

**Régua** |  **Detalhes** | **Etiqueta de serviço**
--- | --- | --- 
Permitir saída HTTPS: porta 443 | Permitir intervalos que correspondem às contas de armazenamento na região de origem | Repositório.\<nome da região >
Permitir saída HTTPS: porta 443 | Permitir intervalos que correspondem a Azure Active Directory (Azure AD).<br/><br/> Se os endereços do Azure AD forem adicionados no futuro, você precisará criar novas regras de NSG (grupo de segurança de rede).  | AzureActiveDirectory
Permitir saída HTTPS: porta 443 | Permitir intervalos que correspondem ao Hub de eventos na região de destino. | EventsHub.\<nome da região >
Permitir saída HTTPS: porta 443 | Permitir intervalos que correspondem a Azure Site Recovery  | AzureSiteRecovery

#### <a name="target-region-rules"></a>Regras de região de destino

**Régua** |  **Detalhes** | **Etiqueta de serviço**
--- | --- | --- 
Permitir saída HTTPS: porta 443 | Permitir intervalos que correspondem às contas de armazenamento na região de destino | Repositório.\<nome da região >
Permitir saída HTTPS: porta 443 | Permitir intervalos que correspondem ao Azure AD.<br/><br/> Se os endereços do Azure AD forem adicionados no futuro, você precisará criar novas regras do NSG.  | AzureActiveDirectory
Permitir saída HTTPS: porta 443 | Permitir intervalos que correspondem ao Hub de eventos na região de origem. | EventsHub.\<nome da região >
Permitir saída HTTPS: porta 443 | Permitir intervalos que correspondem a Azure Site Recovery  | AzureSiteRecovery


#### <a name="control-access-with-nsg-rules"></a>Controlar o acesso com regras NSG

Se você controlar a conectividade da VM filtrando o tráfego de rede de e para redes/sub-redes do Azure usando [regras NSG](https://docs.microsoft.com/azure/virtual-network/security-overview), observe os seguintes requisitos:

- As regras de NSG para a região de origem do Azure devem permitir o acesso de saída para o tráfego de replicação.
- Recomendamos que você crie regras em um ambiente de teste antes de colocá-las em produção.
- Use [marcas de serviço](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) em vez de permitir endereços IP individuais.
    - As marcas de serviço representam um grupo de prefixos de endereço IP coletados em conjunto para minimizar a complexidade ao criar regras de segurança.
    - A Microsoft atualiza automaticamente as marcas de serviço ao longo do tempo. 
 
Saiba mais sobre a [conectividade de saída](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) para site Recovery e controle da [conectividade com o NSGs](concepts-network-security-group-with-site-recovery.md).


### <a name="connectivity-for-multi-vm-consistency"></a>Conectividade para consistência de várias VMs

Se ativar a consistência multi-VM, as máquinas no grupo de replicação comunicam entre si pela porta 20004.
- Certifique-se de que não há nenhum dispositivo de firewall a bloquear a comunicação interna entre as VMs através da porta 20004.
- Se pretender que as VMs do Linux pertençam a um grupo de replicação, certifique-se de que o tráfego de saída na porta 20004 é aberto manualmente de acordo com as orientações da versão específica do Linux.




## <a name="failover-process"></a>Processo de failover

Quando você inicia um failover, as VMs são criadas no grupo de recursos de destino, na rede virtual de destino, na sub-rede de destino e no conjunto de disponibilidade de destino. Durante um failover, você pode usar qualquer ponto de recuperação.

![Processo de failover](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Passos seguintes

[Replique rapidamente](azure-to-azure-quickstart.md) uma VM do Azure para uma região secundária.
