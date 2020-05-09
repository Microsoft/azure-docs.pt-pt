---
title: Arquitetura de recuperação de desastres azure para Azure em Recuperação do Sítio Azure
description: Visão geral da arquitetura utilizada quando se cria uma recuperação de desastres entre as regiões de Azure para VMs Azure, utilizando o serviço azure de recuperação do site.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: raynew
ms.openlocfilehash: a9468f437a89a85f28b6ce869b948ca2a4aff7bf
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983334"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Arquitetura da recuperação após desastre do Azure para o Azure


Este artigo descreve a arquitetura, componentes e processos utilizados quando implementa a recuperação de desastres para máquinas virtuais Azure (VMs) utilizando o serviço de recuperação do [site Azure.](site-recovery-overview.md) Com a recuperação de desastres criada, os VMs Azure replicam-se continuamente de uma região alvo diferente. Se ocorrer uma paragem, pode falhar sobre as VMs para a região secundária e acede-las a partir daí. Quando tudo estiver a funcionar normalmente outra vez, podes voltar atrás e continuar a trabalhar no local principal.



## <a name="architectural-components"></a>Componentes da arquitetura

Os componentes envolvidos na recuperação de desastres para os VMs Azure são resumidos na tabela seguinte.

**Componente** | **Requisitos**
--- | ---
**VMs na região de origem** | Um dos mais VMs Azure numa [região de origem apoiada.](azure-to-azure-support-matrix.md#region-support)<br/><br/> Os VMs podem estar a executar qualquer [sistema operativo suportado](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).
**Armazenamento VM de origem** | Os VMs azure podem ser geridos ou têm discos não geridos espalhados por contas de armazenamento.<br/><br/>[Saiba mais sobre](azure-to-azure-support-matrix.md#replicated-machines---storage) o armazenamento azure suportado.
**Redes VM de origem** | Os VMs podem ser localizados em uma ou mais subnets numa rede virtual (VNet) na região de origem. [Saiba mais](azure-to-azure-support-matrix.md#replicated-machines---networking) sobre os requisitos de networking.
**Conta de armazenamento cache** | Precisa de uma conta de armazenamento de cache na rede de origem. Durante a replicação, as alterações vm são armazenadas na cache antes de serem enviadas para o armazenamento do alvo.  As contas de armazenamento de cache devem ser standard.<br/><br/> A utilização de uma cache garante um impacto mínimo nas aplicações de produção que estão a funcionar num VM.<br/><br/> [Saiba mais](azure-to-azure-support-matrix.md#cache-storage) sobre os requisitos de armazenamento de cache. 
**Recursos-alvo** | Os recursos-alvo são utilizados durante a replicação, e quando ocorre uma falha. A Recuperação do Site pode configurar o recurso-alvo por padrão, ou pode criá-los/personalizá-los.<br/><br/> Na região alvo, verifique se é capaz de criar VMs e que a sua subscrição tem recursos suficientes para suportar tamanhos de VM que serão necessários na região alvo. 

![Replicação de origem e alvo](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

## <a name="target-resources"></a>Recursos-alvo

Quando ativa a replicação de um VM, a Recuperação do Site dá-lhe a opção de criar recursos-alvo automaticamente. 

**Recurso de destino** | **Definição predefinida**
--- | ---
**Subscrição de alvo** | O mesmo que a assinatura de origem.
**Grupo de recursos-alvo** | O grupo de recursos a que os VMs pertencem após a falha.<br/><br/> Pode estar em qualquer região de Azure, exceto na região de origem.<br/><br/> A Recuperação do Site cria um novo grupo de recursos na região alvo, com um sufixo "asr".<br/><br/>
**Alvo VNet** | A rede virtual (VNet) na qual os VMs replicados estão localizados após a falha. É criado um mapeamento de rede entre redes virtuais de origem e alvo, e vice-versa.<br/><br/> A Recuperação do Site cria uma nova VNet e subnet, com o sufixo "asr".
**Conta de armazenamento alvo** |  Se o VM não utilizar um disco gerido, esta é a conta de armazenamento à qual os dados são replicados.<br/><br/> A Recuperação do Site cria uma nova conta de armazenamento na região alvo, para espelhar a conta de armazenamento de origem.
**Réplica de discos geridos** | Se o VM utilizar um disco gerido, estes são os discos geridos aos quais os dados são replicados.<br/><br/> A Recuperação do Site cria discos geridos réplicas na região de armazenamento para espelhar a fonte.
**Conjuntos de disponibilidade de destino** |  Conjunto de disponibilidade no qual os VMs de replicação estão localizados após a falha.<br/><br/> A Recuperação do Site cria uma disponibilidade definida na região alvo com o sufixo "asr", para VMs que se encontram localizados numa disponibilidade definida na localização de origem. Se existe um conjunto de disponibilidade, é usado e um novo não é criado.
**Zonas de disponibilidade de destino** | Se a região alvo apoiar as zonas de disponibilidade, a Recuperação do Local atribui o mesmo número de zona que o utilizado na região de origem.

### <a name="managing-target-resources"></a>Gestão de recursos-alvo

Pode gerir os recursos-alvo da seguinte forma:

- Pode modificar as definições de alvo à medida que ativa a replicação.
- Pode modificar as definições de alvo depois de a replicação já estar a funcionar. Por favor, note que o SKU padrão para a região alvo VM é o mesmo que o SKU da fonte VM (ou o próximo Melhor SKU disponível em comparação com a fonte VM SKU). À semelhança de outros recursos, como o grupo de recursos-alvo, o nome-alvo e outros, a região-alvo VM SKU também pode ser atualizada após a replicação estar em curso. Um recurso que não pode ser atualizado é o tipo de disponibilidade (instância única, conjunto ou zona). Para alterar esta definição, é necessário desativar a replicação, modificar a definição e, em seguida, reativar. 


## <a name="replication-policy"></a>Política de replicação 

Quando ativa a replicação do Azure VM, por padrão, a Recuperação do Site cria uma nova política de replicação com as definições predefinidas resumidas na tabela.

**Definição de políticas** | **Detalhes** | **Predefinição**
--- | --- | ---
**Retenção de pontos de recuperação** | Especifica quanto tempo a Recuperação do Site mantém os pontos de recuperação | 24 horas
**Frequência instantânea consistente com aplicativos** | Quantas vezes a Recuperação do Site tira uma imagem consistente com aplicações. | A cada quatro horas

### <a name="managing-replication-policies"></a>Gestão de políticas de replicação

Pode gerir e modificar as definições de políticas de replicação predefinidas da seguinte forma:
- Pode modificar as definições à medida que ativa a replicação.
- Pode criar uma política de replicação a qualquer momento e depois aplicá-la quando ativa a replicação.

### <a name="multi-vm-consistency"></a>Consistência multi-VM

Se quiser que os VMs se reproduzam em conjunto, e partilhem pontos de recuperação consistentes e consistentes com aplicações no failover, podem juntá-los num grupo de replicação. A consistência multi-VM afeta o desempenho da carga de trabalho, e só deve ser usada para vMs que executam cargas de trabalho que precisam de consistência em todas as máquinas. 



## <a name="snapshots-and-recovery-points"></a>Instantâneos e pontos de recuperação

Os pontos de recuperação são criados a partir de instantâneos de discos VM tirados num determinado momento. Quando falha um VM, utiliza um ponto de recuperação para restaurar o VM no local-alvo.

Ao falhar, geralmente queremos garantir que o VM começa sem corrupção ou perda de dados, e que os dados vM são consistentes para o sistema operativo, e para aplicações que funcionam no VM. Isto depende do tipo de fotos tiradas.

A Recuperação do Site tira fotos da seguinte forma:

1. A Recuperação do Site retira instantâneos consistentes com o crash de dados por padrão e instantâneos consistentes com aplicações se especificar uma frequência para eles.
2. Os pontos de recuperação são criados a partir dos instantâneos e armazenados de acordo com as definições de retenção na política de replicação.

### <a name="consistency"></a>Consistência

A tabela que se segue explica diferentes tipos de consistência.

### <a name="crash-consistent"></a>Consistente com acidentes

**Descrição** | **Detalhes** | **Recomendação**
--- | --- | ---
Um instantâneo consistente capta dados que estavam no disco quando a fotografia foi tirada. Não inclui nada na memória.<br/><br/> Contém o equivalente aos dados em disco que estariam presentes se o VM se despenhasse ou o cabo de alimentação fosse retirado do servidor no instante em que o instantâneo foi tirado.<br/><br/> Um crash-consistente não garante a consistência dos dados para o sistema operativo, ou para aplicações no VM. | A Recuperação do Site cria pontos de recuperação consistentes a cada cinco minutos por padrão. Esta definição não pode ser modificada.<br/><br/>  | Hoje em dia, a maioria das aplicações pode recuperar bem de pontos consistentes com acidentes.<br/><br/> Os pontos de recuperação consistentes com falhas são geralmente suficientes para a replicação de sistemas operativos, e aplicações como servidores DHCP e servidores de impressão.

### <a name="app-consistent"></a>Aplicações consistentes

**Descrição** | **Detalhes** | **Recomendação**
--- | --- | ---
Os pontos de recuperação consistentes com aplicações são criados a partir de instantâneos consistentes com aplicações.<br/><br/> Um instantâneo consistente com aplicações contém toda a informação num instantâneo consistente com quebra, além de todos os dados na memória e transações em curso. | Instantâneos consistentes com aplicativos utilizam o Serviço de Cópia de Sombra de Volume (VSS):<br/><br/>   1) Quando um instantâneo é iniciado, o VSS executa uma operação de cópia-on-write (COW) no volume.<br/><br/>   2) Antes de realizar a COW, a VSS informa todas as aplicações da máquina de que necessita de descarregar os seus dados residentes de memória para o disco.<br/><br/>   3) O VSS permite então que a aplicação de recuperação de cópias de segurança/desastre (neste caso, a Recuperação do Site) leia os dados instantâneos e proceda. | As imagens consistentes com aplicações são tiradas de acordo com a frequência que especifica. Esta frequência deve ser sempre inferior à configuração para reter pontos de recuperação. Por exemplo, se conservar os pontos de recuperação utilizando a definição predefinida de 24 horas, deverá definir a frequência em menos de 24 horas.<br/><br/>São mais complexos e demoram mais tempo a completar do que imagens consistentes com acidentes.<br/><br/> Afetam o desempenho de aplicações em execução num VM habilitado para a replicação. 

## <a name="replication-process"></a>Processo de replicação

Quando ativa a replicação de um VM Azure, acontece o seguinte:

1. A extensão do serviço de mobilidade de recuperação do site é instalada automaticamente no VM.
2. A extensão regista o VM com recuperação do site.
3. A replicação contínua começa para o VM.  As escritas do disco são imediatamente transferidas para a conta de armazenamento de cache na localização de origem.
4. A Recuperação do Site processa os dados na cache, e envia-os para a conta de armazenamento alvo, ou para os discos geridos da réplica.
5. Após o processamento dos dados, os pontos de recuperação consistentes com acidentes são gerados a cada cinco minutos. Os pontos de recuperação consistentes com aplicações são gerados de acordo com a definição especificada na política de replicação.

![Ativar o processo de replicação, passo 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

**Processo de replicação**

## <a name="connectivity-requirements"></a>Requisitos de conectividade

 Os VMs Azure que replica precisam de conectividade de saída. A Recuperação do Site nunca necessita de conectividade de entrada para o VM. 

### <a name="outbound-connectivity-urls"></a>Conectividade de saída (URLs)

Se o acesso de saída para VMs for controlado com URLs, permita estes URLs.

| **URL** | **Detalhes** |
| ------- | ----------- |
| *.blob.core.windows.net | Permite que os dados sejam escritos da VM para a conta de armazenamento em cache na região de origem. |
| login.microsoftonline.com | Fornece autorização e autenticação para os URLs do serviço Site Recovery. |
| *.hypervrecoverymanager.windowsazure.com | Permite que a VM comunique com o serviço Site Recovery. |
| *.servicebus.windows.net | Permite que a VM escreva dados de monitorização e diagnóstico do Site Recovery. |
| *.vault.azure.net | Permite o acesso para permitir a replicação de máquinas virtuais ativadas pela ADE através do portal
| *.automation.ext.azure.com | Permite permitir a tualização automática do agente de mobilidade para um item replicado via portal

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividade de saída para intervalos de endereços IP

Para controlar a conectividade de saída dos VMs utilizando endereços IP, permita estes endereços.
Por favor, note que detalhes dos requisitos de conectividade da rede podem ser encontrados em [papel branco em rede](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) 

#### <a name="source-region-rules"></a>Regras da região de origem

**Regra** |  **Detalhes** | **Etiqueta de serviço**
--- | --- | --- 
Permitir saída de HTTPS: porta 443 | Permitir gamas que correspondam a contas de armazenamento na região fonte | Um armazém. \<> de nome da região
Permitir saída de HTTPS: porta 443 | Permitir gamas que correspondam ao Diretório Ativo Azure (Azure AD)  | AzureActiveDirectory
Permitir saída de HTTPS: porta 443 | Permitir intervalos que correspondam ao Centro de Eventos na região alvo. | EventsHub. \<> de nome da região
Permitir saída de HTTPS: porta 443 | Permitir gamas que correspondam à recuperação do site Azure  | Recuperação do AzureSite
Permitir saída de HTTPS: porta 443 | Permitir gamas que correspondam ao Cofre chave Azure (isto é necessário apenas para permitir a replicação de máquinas virtuais ativadas pela ADE via portal) | AzureKeyVault
Permitir saída de HTTPS: porta 443 | Permitir gamas que correspondam ao Controlador de Automação Azure (isto é necessário apenas para permitir a tualização automática do agente de mobilidade para um item replicado via portal) | GuestAndHybridManagement

#### <a name="target-region-rules"></a>Regras da região-alvo

**Regra** |  **Detalhes** | **Etiqueta de serviço**
--- | --- | --- 
Permitir saída de HTTPS: porta 443 | Permitir gamas que correspondam a contas de armazenamento na região alvo | Um armazém. \<> de nome da região
Permitir saída de HTTPS: porta 443 | Permitir gamas que correspondam a Azure AD  | AzureActiveDirectory
Permitir saída de HTTPS: porta 443 | Permitir intervalos que correspondam ao Centro de Eventos na região de origem. | EventsHub. \<> de nome da região
Permitir saída de HTTPS: porta 443 | Permitir gamas que correspondam à recuperação do site Azure  | Recuperação do AzureSite
Permitir saída de HTTPS: porta 443 | Permitir gamas que correspondam ao Cofre chave Azure (isto é necessário apenas para permitir a replicação de máquinas virtuais ativadas pela ADE via portal) | AzureKeyVault
Permitir saída de HTTPS: porta 443 | Permitir gamas que correspondam ao Controlador de Automação Azure (isto é necessário apenas para permitir a tualização automática do agente de mobilidade para um item replicado via portal) | GuestAndHybridManagement


#### <a name="control-access-with-nsg-rules"></a>Controlar o acesso com as regras do NSG

Se controlar a conectividade VM filtrando o tráfego da rede de e para as redes/subredes Azure utilizando [as regras NSG,](https://docs.microsoft.com/azure/virtual-network/security-overview)note os seguintes requisitos:

- As regras do NSG para a região de Azure de origem devem permitir o acesso de saída para o tráfego de replicação.
- Recomendamos que crie regras num ambiente de teste antes de as colocar em produção.
- Utilize [etiquetas](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) de serviço em vez de permitir endereços IP individuais.
    - As etiquetas de serviço representam um grupo de prefixos de endereçoIP reunidos para minimizar a complexidade ao criar regras de segurança.
    - A Microsoft atualiza automaticamente as etiquetas de serviço ao longo do tempo. 
 
Saiba mais sobre [a conectividade de saída](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) para a Recuperação do Site e controle a conectividade com [nsgs.](concepts-network-security-group-with-site-recovery.md)


### <a name="connectivity-for-multi-vm-consistency"></a>Conectividade para a consistência multi-VM

Se ativar a consistência multi-VM, as máquinas no grupo de replicação comunicam entre si pela porta 20004.
- Certifique-se de que não há nenhum dispositivo de firewall a bloquear a comunicação interna entre as VMs através da porta 20004.
- Se pretender que as VMs do Linux pertençam a um grupo de replicação, certifique-se de que o tráfego de saída na porta 20004 é aberto manualmente de acordo com as orientações da versão específica do Linux.




## <a name="failover-process"></a>Processo de failover

Quando inicia uma falha, os VMs são criados no grupo de recursos-alvo, rede virtual alvo, sub-rede alvo e no conjunto de disponibilidade de destino. Durante uma falha, pode usar qualquer ponto de recuperação.

![Processo de failover](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Passos seguintes

[Reproduza rapidamente](azure-to-azure-quickstart.md) um VM Azure para uma região secundária.
