---
title: Azure to Azure arquitetura de recuperação de desastres na Recuperação do Local de Azure
description: Visão geral da arquitetura utilizada quando configura a recuperação de desastres entre as regiões de Azure para VMs Azure, utilizando o serviço de Recuperação do Local Azure.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: raynew
ms.openlocfilehash: 64d1084fd7025c74676977f065062e5e94dabf1d
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97652250"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Arquitetura da recuperação após desastre do Azure para o Azure


Este artigo descreve a arquitetura, componentes e processos utilizados quando implementa a recuperação de desastres para máquinas virtuais Azure (VMs) utilizando o serviço [de Recuperação do Local Azure.](site-recovery-overview.md) Com a criação de recuperação de desastres, os VMs do Azure replicam-se continuamente numa região-alvo diferente. Se ocorrer uma paragem, pode falhar em VMs para a região secundária e acessá-las a partir daí. Quando tudo estiver a funcionar normalmente de novo, podes falhar e continuar a trabalhar na localização primária.



## <a name="architectural-components"></a>Componentes da arquitetura

Os componentes envolvidos na recuperação de desastres para os VMs do Azure são resumidos no quadro seguinte.

**Componente** | **Requisitos**
--- | ---
**VMs na região de origem** | Um dos mais VMs Azure numa [região de origem apoiada.](azure-to-azure-support-matrix.md#region-support)<br/><br/> Os VMs podem estar a executar qualquer [sistema operativo suportado.](azure-to-azure-support-matrix.md#replicated-machine-operating-systems)
**Armazenamento VM de origem** | Os VMs Azure podem ser geridos ou têm discos não geridos espalhados por contas de armazenamento.<br/><br/>[Saiba mais sobre](azure-to-azure-support-matrix.md#replicated-machines---storage) o armazenamento suportado da Azure.
**Redes VM de origem** | Os VMs podem ser localizados em uma ou mais sub-redes numa rede virtual (VNet) na região de origem. [Saiba mais](azure-to-azure-support-matrix.md#replicated-machines---networking) sobre os requisitos de networking.
**Conta de armazenamento de cache** | Precisa de uma conta de armazenamento de cache na rede de origem. Durante a replicação, as alterações em VM são armazenadas na cache antes de serem enviadas para o armazenamento do alvo.  As contas de armazenamento de cache devem ser standard.<br/><br/> A utilização de uma cache garante o mínimo impacto nas aplicações de produção que estão a funcionar num VM.<br/><br/> [Saiba mais](azure-to-azure-support-matrix.md#cache-storage) sobre os requisitos de armazenamento de cache. 
**Recursos-alvo** | Os recursos-alvo são utilizados durante a replicação, e quando ocorre uma falha. A Recuperação do Site pode configurar o recurso-alvo por predefinição, ou pode criá-los/personalizá-los.<br/><br/> Na região alvo, verifique se é capaz de criar VMs, e que a sua subscrição tem recursos suficientes para suportar tamanhos de VM que serão necessários na região alvo. 

![Diagrama mostrando a origem e a replicação do alvo.](./media/concepts-azure-to-azure-architecture/enable-replication-step-1-v2.png)

## <a name="target-resources"></a>Recursos-alvo

Quando ativa a replicação de um VM, a Recuperação do Site dá-lhe a opção de criar recursos-alvo automaticamente. 

**Recurso de Destino** | **Definição predefinida**
--- | ---
**Assinatura de destino** | O mesmo que a assinatura de origem.
**Grupo de recursos-alvo** | O grupo de recursos a que os VMs pertencem após o failover.<br/><br/> Pode ser em qualquer região de Azure, exceto na região de origem.<br/><br/> A Recuperação do Site cria um novo grupo de recursos na região alvo, com um sufixo "asr".<br/><br/>
**VNet alvo** | A rede virtual (VNet) na qual os VMs replicados estão localizados após a falha. Um mapeamento de rede é criado entre redes virtuais de origem e alvo, e vice-versa.<br/><br/> A Recuperação do Site cria um novo VNet e sub-rede, com o sufixo "asr".
**Conta de armazenamento alvo** |  Se o VM não utilizar um disco gerido, esta é a conta de armazenamento a que os dados são replicados.<br/><br/> A Recuperação do Site cria uma nova conta de armazenamento na região alvo, para espelhar a conta de armazenamento de origem.
**Discos geridos por réplicas** | Se o VM utilizar um disco gerido, estes são os discos geridos aos quais os dados são replicados.<br/><br/> A Recuperação do Site cria discos geridos por réplicas na região de armazenamento para espelhar a fonte.
**Conjuntos de disponibilidade de destino** |  Disponibilidade definida em que a replicação de VMs estão localizadas após a falha.<br/><br/> A Recuperação do Site cria uma disponibilidade definida na região alvo com o sufixo "asr", para VMs que estão localizados numa disponibilidade definida no local de origem. Se existe um conjunto de disponibilidade, é usado e um novo não é criado.
**Zonas de disponibilidade de destino** | Se a região alvo apoiar zonas de disponibilidade, a Recuperação do Local atribui o mesmo número de zona que o utilizado na região de origem.

### <a name="managing-target-resources"></a>Gestão de recursos-alvo

Pode gerir os recursos-alvo da seguinte forma:

- Pode modificar as definições do alvo à medida que ativa a replicação.
- Pode modificar as definições do alvo após a replicação já estar a funcionar. Por favor, note que o SKU padrão para a região alvo VM é o mesmo que o SKU da fonte VM (ou o próximo melhor SKU disponível em comparação com a fonte VM SKU). Semelhante a outros recursos, como o grupo de recursos-alvo, nome-alvo, entre outros, a região-alvo VM SKU também pode ser atualizada após a replicação estar em curso. Um recurso que não pode ser atualizado é o tipo de disponibilidade (instância única, definida ou zona). Para alterar esta definição é necessário desativar a replicação, modificar a definição e, em seguida, reensiable. 


## <a name="replication-policy"></a>Política de replicação 

Quando ativa a replicação do Azure VM, por defeito a Recuperação do Site cria uma nova política de replicação com as definições padrão resumidas na tabela.

**Definição de política** | **Detalhes** | **Predefinição**
--- | --- | ---
**Retenção de pontos de recuperação** | Especifica quanto tempo a Recuperação do Site mantém os pontos de recuperação | 24 horas
**Frequência instantânea consistente com aplicações** | Quantas vezes a Recuperação do Site tira uma imagem consistente da aplicação. | A cada quatro horas

### <a name="managing-replication-policies"></a>Gerir políticas de replicação

Pode gerir e modificar as definições de políticas de replicação predefinidos da seguinte forma:
- Pode modificar as definições à medida que ativa a replicação.
- Pode criar uma política de replicação a qualquer momento e, em seguida, aplicá-la quando ativar a replicação.

### <a name="multi-vm-consistency"></a>Consistência multi-VM

Se quiser que os VMs se reproduzam em conjunto e que partilhem pontos de recuperação consistentes e consistentes com aplicações no failover, pode juntá-los num grupo de replicação. A consistência multi-VM tem impacto no desempenho da carga de trabalho e só deve ser utilizada para os VM que executam cargas de trabalho que necessitam de consistência em todas as máquinas. 



## <a name="snapshots-and-recovery-points"></a>Instantâneos e pontos de recuperação

Os pontos de recuperação são criados a partir de instantâneos de discos VM tirados num determinado ponto no tempo. Quando falhas num VM, usas um ponto de recuperação para restaurar o VM no local do alvo.

Ao falhar, geralmente queremos garantir que o VM começa sem corrupção ou perda de dados, e que os dados VM são consistentes para o sistema operativo, e para aplicações que funcionam no VM. Isto depende do tipo de instantâneos tirados.

A Recuperação do Site tira as fotos da seguinte forma:

1. A Recuperação do Site tira por defeito instantâneos consistentes de dados e instantâneos consistentes com aplicações se especificar uma frequência para eles.
2. Os pontos de recuperação são criados a partir dos instantâneos e armazenados de acordo com as definições de retenção na política de replicação.

### <a name="consistency"></a>Consistência

A tabela a seguir explica diferentes tipos de consistência.

### <a name="crash-consistent"></a>Consistente com acidentes

**Descrição** | **Detalhes** | **Recomendação**
--- | --- | ---
Um instantâneo consistente captura dados que estavam no disco quando a fotografia foi tirada. Não inclui nada na memória.<br/><br/> Contém o equivalente aos dados do disco que estariam presentes se o VM se despenhasse ou o cabo de alimentação fosse retirado do servidor no instante em que a fotografia foi tirada.<br/><br/> Uma falha-consistente não garante a consistência de dados para o sistema operativo, ou para aplicações no VM. | A Recuperação do Site cria pontos de recuperação consistentes a cada cinco minutos por defeito. Esta definição não pode ser modificada.<br/><br/>  | Hoje em dia, a maioria das aplicações pode recuperar bem de pontos consistentes com crash.<br/><br/> Os pontos de recuperação consistentes em falhas são geralmente suficientes para a replicação de sistemas operativos, e aplicações como servidores DHCP e servidores de impressão.

### <a name="app-consistent"></a>Consistente com aplicativos

**Descrição** | **Detalhes** | **Recomendação**
--- | --- | ---
Os pontos de recuperação consistentes da aplicação são criados a partir de instantâneos consistentes com aplicações.<br/><br/> Um instantâneo consistente com aplicações contém todas as informações num instantâneo consistente com falhas, além de todos os dados na memória e transações em curso. | Os instantâneos consistentes em aplicações utilizam o Serviço de Cópia sombra de volume (VSS):<br/><br/>   1) A recuperação do site Azure usa o método de cópia apenas de cópia (VSS_BT_COPY) que não altera o tempo de cópia de segurança do registo de transações da Microsoft SQL e o número de sequência </br></br> 2) Quando um instantâneo é iniciado, a VSS efetua uma operação de cópia-on-write (COW) sobre o volume.<br/><br/>   3) Antes de realizar a COW, a VSS informa todas as aplicações da máquina que necessita de descarregar os seus dados de residentes de memória para o disco.<br/><br/>   4) O VSS permite então que a aplicação de backup/recuperação de desastres (neste caso, recuperação do site) leia os dados do instantâneo e prossiga. | As imagens consistentes com a aplicação são tiradas de acordo com a frequência especificada. Esta frequência deve ser sempre inferior à que definiu para reter pontos de recuperação. Por exemplo, se mantiver pontos de recuperação utilizando a definição predefinida de 24 horas, deverá definir a frequência em menos de 24 horas.<br/><br/>São mais complexos e demoram mais tempo a completar do que imagens consistentes.<br/><br/> Afetam o desempenho de aplicações em execução num VM habilitado para replicação. 

## <a name="replication-process"></a>Processo de replicação

Quando ativa a replicação de um Azure VM, acontece o seguinte:

1. A extensão do serviço de mobilidade de recuperação do local é automaticamente instalada no VM.
2. A extensão regista o VM com recuperação do local.
3. Começa a replicação contínua para o VM.  As gravações em disco são imediatamente transferidas para a conta de armazenamento de cache no local de origem.
4. A Recuperação do Site processa os dados na cache e envia-os para a conta de armazenamento alvo, ou para os discos geridos por réplica.
5. Após o processamento dos dados, os pontos de recuperação consistentes em acidentes são gerados a cada cinco minutos. Os pontos de recuperação consistentes da aplicação são gerados de acordo com a definição especificada na política de replicação.

![Diagrama mostrando o processo de replicação, passo 2.](./media/concepts-azure-to-azure-architecture/enable-replication-step-2-v2.png)

**Processo de replicação**

## <a name="connectivity-requirements"></a>Requisitos de conectividade

 Os VMs Azure que replica precisam de conectividade de saída. A Recuperação do Site nunca necessita de conectividade de entrada com o VM. 

### <a name="outbound-connectivity-urls"></a>Conectividade de saída (URLs)

Se o acesso de saída para VMs for controlado com URLs, permita estes URLs.

| **Nome**                  | **Comercial**                               | **Administração Pública**                                 | **Descrição** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Armazenamento                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Permite que os dados sejam escritos da VM para a conta de armazenamento em cache na região de origem. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Fornece autorização e autenticação para os URLs do serviço Site Recovery. |
| Replicação               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`     | Permite que a VM comunique com o serviço Site Recovery. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Permite que a VM escreva dados de monitorização e diagnóstico do Site Recovery. |
| Cofre de Chaves                 | `*.vault.azure.net`                        | `*.vault.usgovcloudapi.net`                  | Permite o acesso para permitir a replicação de máquinas virtuais ativadas por ADE através do portal |
| Automatização do Azure          | `*.automation.ext.azure.com`               | `*.azure-automation.us`                      | Permite permitir a atualização automática do agente de mobilidade para um item replicado via portal |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividade de saída para intervalos de endereços IP

Para controlar a conectividade de saída para VMs utilizando endereços IP, permita estes endereços.
Por favor, note que detalhes dos requisitos de conectividade da rede podem ser encontrados em [papel branco em rede](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) 

#### <a name="source-region-rules"></a>Regras da região de origem

**Regra** |  **Detalhes** | **Etiqueta de serviço**
--- | --- | --- 
Permitir saída HTTPS: porta 443 | Permitir gamas que correspondam a contas de armazenamento na região origem | O armazém.\<region-name>
Permitir saída HTTPS: porta 443 | Permitir gamas que correspondam ao Azure Ative Directory (Azure AD)  | AzureActiveDirectory
Permitir saída HTTPS: porta 443 | Permitir intervalos que correspondam ao Centro de Eventos na região alvo. | EventsHub.\<region-name>
Permitir saída HTTPS: porta 443 | Permitir gamas que correspondam à Recuperação do Local de Azure  | AzureSiteRecovery
Permitir saída HTTPS: porta 443 | Permitir gamas que correspondam ao Cofre da Chave Azure (isto é necessário apenas para permitir a replicação de máquinas virtuais ativadas por ADE via portal) | AzureKeyVault
Permitir saída HTTPS: porta 443 | Permitir gamas que correspondam ao Controlador de Automação Azure (isto é necessário apenas para permitir uma atualização automática do agente de mobilidade para um item replicado via portal) | GuestAndHybridManagement

#### <a name="target-region-rules"></a>Regras da região-alvo

**Regra** |  **Detalhes** | **Etiqueta de serviço**
--- | --- | --- 
Permitir saída HTTPS: porta 443 | Permitir gamas que correspondam a contas de armazenamento na região alvo | O armazém.\<region-name>
Permitir saída HTTPS: porta 443 | Permitir gamas que correspondam ao AZure AD  | AzureActiveDirectory
Permitir saída HTTPS: porta 443 | Permitir gamas que correspondam ao Centro de Eventos na região de origem. | EventsHub.\<region-name>
Permitir saída HTTPS: porta 443 | Permitir gamas que correspondam à Recuperação do Local de Azure  | AzureSiteRecovery
Permitir saída HTTPS: porta 443 | Permitir gamas que correspondam ao Cofre da Chave Azure (isto é necessário apenas para permitir a replicação de máquinas virtuais ativadas por ADE via portal) | AzureKeyVault
Permitir saída HTTPS: porta 443 | Permitir gamas que correspondam ao Controlador de Automação Azure (isto é necessário apenas para permitir uma atualização automática do agente de mobilidade para um item replicado via portal) | GuestAndHybridManagement


#### <a name="control-access-with-nsg-rules"></a>Controlar o acesso com as regras da NSG

Se controlar a conectividade VM filtrando o tráfego de rede de e para as redes/subesí redes Azure utilizando [as regras NSG,](../virtual-network/network-security-groups-overview.md)note os seguintes requisitos:

- As regras NSG para a região de Azure de origem devem permitir o acesso de saída para o tráfego de replicação.
- Recomendamos que crie regras num ambiente de teste antes de as colocar em produção.
- Utilize etiquetas de serviço em vez de permitir [endereços](../virtual-network/network-security-groups-overview.md#service-tags) IP individuais.
    - As etiquetas de serviço representam um grupo de prefixos de endereço IP reunidos para minimizar a complexidade ao criar regras de segurança.
    - A Microsoft atualiza automaticamente as tags de serviço ao longo do tempo. 
 
Saiba mais sobre [a conectividade de saída](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) para a Recuperação do Site e controlar a conectividade com os [NSGs.](concepts-network-security-group-with-site-recovery.md)


### <a name="connectivity-for-multi-vm-consistency"></a>Conectividade para a consistência multi-VM

Se ativar a consistência multi-VM, as máquinas no grupo de replicação comunicam entre si pela porta 20004.
- Certifique-se de que não há nenhum dispositivo de firewall a bloquear a comunicação interna entre as VMs através da porta 20004.
- Se pretender que as VMs do Linux pertençam a um grupo de replicação, certifique-se de que o tráfego de saída na porta 20004 é aberto manualmente de acordo com as orientações da versão específica do Linux.




## <a name="failover-process"></a>Processo de failover

Quando inicia uma falha, os VMs são criados no grupo de recursos-alvo, rede virtual alvo, sub-rede alvo e no conjunto de disponibilidade de destino. Durante uma falha, pode utilizar qualquer ponto de recuperação.

![Diagrama que mostra o processo de failover com ambientes de origem e alvo.](./media/concepts-azure-to-azure-architecture/failover-v2.png)

## <a name="next-steps"></a>Passos seguintes

[Replique rapidamente](azure-to-azure-quickstart.md) um Azure VM para uma região secundária.