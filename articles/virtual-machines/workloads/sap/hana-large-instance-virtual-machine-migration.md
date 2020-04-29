---
title: Migração SAP HANA em Azure (Grandes Instâncias) para máquinas virtuais Azure Microsoft Docs
description: Como migrar sAP HANA em Azure (Grandes Instâncias) para máquinas virtuais Azure
services: virtual-machines-linux
documentationcenter: ''
author: bentrin
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: bentrin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fd1267711871b3e55f1a6229e46ae27b360322f6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77617046"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>SAP HANA sobre migração de grandes instâncias azure para máquinas virtuais azure
Este artigo descreve possíveis cenários de implantação de grandes instâncias do Azure e oferece abordagem de planeamento e migração com tempo de transição minimizado

## <a name="overview"></a>Descrição geral
Desde o anúncio do Azure Large Instances for SAP HANA (HLI) em setembro de 2016, muitos clientes adotaram este hardware como uma oferta de serviço para a sua plataforma de computação em memória.  Nos últimos anos, a extensão do tamanho do Azure VM, juntamente com o apoio da implantação em escala HANA, excedeu a procura de capacidade de base de dados ERP da maioria dos clientes empresariais.  Começamos a ver clientes expressando o interesse de migrar a sua carga de trabalho SAP HANA de servidores físicos para VMs Azure.
Este guia não é um documento de configuração passo a passo. Descreve os modelos comuns de implantação e oferece conselhos de planeamento e migração.  A intenção é chamar a atenção necessária para a preparação para minimizar o tempo de transição.

## <a name="assumptions"></a>Pressupostos
Este artigo faz as seguintes suposições:
- O único interesse considerado é uma migração homogénea do serviço de computação de base de dados HANA de Hana Large Instance (HLI) para Azure VM sem atualização ou remendo significativo de software. Estas pequenas atualizações incluem a utilização de uma versão os mais recente ou versão HANA explicitamente indicada como suportada por notas SAP relevantes. 
- Todas as atualizações/atualizações têm de ser feitas antes ou depois da migração.  Por exemplo, o SAP HANA MCOS converte-se na implantação do MDC. 
- A abordagem migratória que ofereceria menos tempo de inatividade é a Replicação do Sistema SAP HANA. Outros métodos de migração não fazem parte do âmbito deste documento.
- Esta orientação é aplicável tanto para rev3 como para Rev4 SKUs de HLI.
- A arquitetura de implantação da HANA permanece principalmente inalterada durante a migração.  Ou seja, um sistema com um DR de instância única permanecerá da mesma forma no destino.
- Os clientes analisaram e compreenderam o Acordo de Nível de Serviço (SLA) da arquitetura alvo (a ser). 
- Os termos comerciais entre as HLIs e os VMs são diferentes. Os clientes devem monitorizar a utilização dos seus VMs para a gestão de custos.
- Os clientes entendem que o HLI é uma plataforma de computação dedicada, enquanto os VMs funcionam em infraestruturas partilhadas mas isoladas.
- Os clientes validaram esse alvo vMs suportam a sua arquitetura pretendida. Para ver todos os VM SKUs suportados certificados para a implementação do SAP HANA, consulte o diretório de [hardware SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
- Os clientes validaram o plano de conceção e migração.
- Plano para recuperação de desastres VM juntamente com o local principal.  Os clientes não podem usar o HLI como o nó DR para o local primário em execução em VMs após a migração.
- Os clientes copiaram os ficheiros de backup necessários para os VMs-alvo, com base nos requisitos de recuperabilidade e conformidade das empresas. Com backups acessíveis a VM, permite a recuperação ponto-a-tempo durante o período de transição.
- Para o HSR HA, os clientes precisam de configurar e configurar o dispositivo STONITH por guias SAP HANA HA para [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) e [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker).  Não está pré-configurado como o caso hli.
- Esta abordagem de migração não cobre o HLI SKUs com a configuração Optane.

## <a name="deployment-scenarios"></a>Cenários de implementação
Os modelos comuns de implantação com clientes HLI são resumidos na tabela seguinte.  A migração para VMs Azure para todos os cenários de HLI é possível.  Para beneficiar dos serviços azure complementares disponíveis, podem ser necessárias pequenas alterações arquitetónicas.

| ID do cenário | Cenário HLI | Migrar para vm verbatim? | Observação |
| --- | --- | --- | --- |
| 1 | [Nó único com um SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-one-sid) | Sim | - |
| 2 | [Nó único com MCOS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos) | Sim | - |
| 3 | [Nó único com DR usando replicação de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication) | Não | A replicação de armazenamento não está disponível com plataforma virtual Azure, altere a atual solução DR para HSR ou backup/restauro |
| 4 | [Nó único com DR (multiuso) utilizando replicação de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication) | Não | A replicação de armazenamento não está disponível com plataforma virtual Azure, altere a atual solução DR para HSR ou backup/restauro |
| 5 | [HSR com STONITH para alta disponibilidade](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability) | Sim | Sem SBD pré-configurado para VMs alvo.  Selecione e implemente uma solução STONITH.  Opções possíveis: Agente de Esgrima Azure (suportado tanto para [RHEL,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) [SLES),](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)SBD |
| 6 | [HA com HSR, DR com replicação de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication) | Não | Substitua a replicação de armazenamento para necessidades de DR por HSR ou cópia de segurança/restauro |
| 7 | [Falha automática do anfitrião (1+1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11) | Sim | Utilize ANF para armazenamento partilhado com VMs Azure |
| 8 | [Escala-out com standby](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby) | Sim | BW/4HANA com M128s, M416s, M416ms VMs usando ANF apenas para armazenamento |
| 9 | [Escala-out sem espera](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby) | Sim | BW/4HANA com M128s, M416s, M416ms VMs (com ou sem utilização anf para armazenamento) |
| 10 | [Scale-out com DR usando replicação de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication) | Não | Substitua a replicação de armazenamento para necessidades de DR por HSR ou cópia de segurança/restauro |
| 11 | [Nó único com DR usando HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr) | Sim | - |
| 12 | [Nó único HSR para DR (custo otimizado)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized) | Sim | - |
| 13 | [HA e DR com HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr) | Sim | - |
| 14 | [HA e DR com HSR (custo otimizado)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | Sim | - |
| 15 | [Scale-out com DR usando HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr) | Sim | BW/4HANA com M128s. M416s, M416ms VMs (com ou sem utilização anf para armazenamento) |


## <a name="source-hli-planning"></a>Planeamento de fonte (HLI)
Ao embarcar num servidor HLI, tanto a Microsoft Service Management como os clientes passaram pelo planeamento da computação, rede, armazenamento e definições específicas do OS para executar a base de dados SAP HANA.  É necessário planear de forma semelhante para a migração para o Azure VM.

### <a name="sap-hana-housekeeping"></a>Limpeza sap HANA 
É uma boa prática operacional arrumar o conteúdo da base de dados para que dados indesejados e desatualizados, ou registos velhos não sejam migrados para a nova base de dados.  A limpeza envolve geralmente a desminagem ou arquivamento de dados antigos, expirados ou inativos.  Estas ações de "higiene de dados" devem ser testadas em sistemas não produtivos para validar a sua validade de aparas de dados antes da utilização da produção.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>Permitir a conectividade da rede para novos VMs e, ou rede virtual 
Na implantação do HLI de um cliente, a rede foi criada com base nas informações descritas no artigo [SAP HANA (Grandes Instâncias) arquitetura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)de rede. Além disso, o encaminhamento de tráfego de rede é feito da forma descrita na secção 'Encaminhamento em Azure'.
- Ao configurar um novo VM como alvo de migração, Se for colocado na rede virtual existente com gamas de endereços IP já autorizadas a ligar-se ao HLI, não é necessária mais nenhuma atualização de conectividade.
- Se o novo Azure VM for colocado numa nova Rede Virtual Microsoft Azure, poderá estar noutra região, e espreitar com a rede virtual existente, a chave de serviço ExpressRoute e o Id de Recursos do fornecimento original de HLI são utilizáveis para permitir o acesso a esta nova gama IP da rede virtual.  Coordene com a Microsoft Service Management para permitir a conectividade da rede virtual.  Nota: Para minimizar a latência da rede entre as camadas de aplicação e base de dados, tanto as camadas de aplicação como de base de dados devem estar na mesma rede virtual.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>Conjunto de disponibilidade de camada de aplicativo existente, zonas de disponibilidade e Grupo de Colocação de Proximidade (PPG)
O atual modelo de implantação é feito para satisfazer determinados objetivos de nível de serviço.  Neste movimento, certifique-se de que a infra-estrutura-alvo irá cumprir ou exceder os objetivos definidos.  
Mais provável do que não, os servidores de aplicações SAP dos clientes são colocados num conjunto de disponibilidade.  Se o nível atual de serviço de implantação for satisfatório e 
- Se o VM alvo assumir o nome de anfitrião do nome lógico HLI, atualizar a resolução de endereços de nome de domínio (DNS) que indique o IP do VM funcionaria sem atualizar quaisquer perfis SAP
- Se não estiver a utilizar o PPG, certifique-se de colocar todos os servidores de aplicação e DB na mesma zona para minimizar a latência da rede.
- Se estiver a utilizar o PPG, consulte a secção deste documento: 'Planeamento de Destino, Conjunto de Disponibilidade, Zonas de Disponibilidade e Grupo de Colocação de Proximidade (PPG)'.

### <a name="storage-replication-discontinuance-process-if-used"></a>Processo de descontinuação da replicação de armazenamento (se utilizado)
Se a replicação do armazenamento for utilizada como solução DR, deve ser terminada (não programada) depois de a aplicação SAP ter sido desligada.  Além disso, o último catálogo, ficheiro de registo e dados do SAP HANA foram replicados nos volumes remotos de armazenamento DR HLI.  Fazê-lo por precaução no caso de um desastre acontecer durante o servidor físico para a transição Azure VM.

### <a name="data-backups-preservation-consideration"></a>Consideração de preservação de backups de dados
Após o corte para SAP HANA no Azure VM, todos os dados baseados em instantâneos ou cópias de segurança no HLI não são facilmente acessíveis ou restoráveis a um VM, se necessário. No período de transição inicial, antes que o backup baseado em Azure construa história suficiente para satisfazer os requisitos de recuperação do Ponto no Tempo, recomendamos que se tome minés de nível de ficheiros para além de instantâneos no HLI, dias ou semanas antes do corte.  Tenha estas cópias de segurança para uma conta de Armazenamento Azure acessível pelo novo SAP HANA VM.
Além de apoiar o conteúdo do HLI, é prudente ter cópias de segurança completas da paisagem SAP facilmente acessíveis no caso de ser necessário um retrocesso.

### <a name="adjusting-system-monitoring"></a>Monitorização do sistema de ajuste 
Os clientes usam muitas ferramentas diferentes para monitorizar e enviar notificações de alerta para sistemas dentro da sua paisagem SAP.  Este item é apenas uma chamada para medidas adequadas para incorporar alterações para monitorização e atualização dos destinatários da notificação de alerta, se necessário.

### <a name="microsoft-operations-team-involvement"></a>Envolvimento da equipa de Operações da Microsoft 
Abra um bilhete a partir do portal Azure com base na instância HLI existente.  Após a criação do bilhete de apoio, um engenheiro de suporte entrará em contacto consigo por e-mail.  

### <a name="engage-microsoft-account-team"></a>Envolver a equipa de conta da Microsoft
Planeie a migração perto do tempo de renovação do contrato de iHL para minimizar desnecessária sanções em detrimento das despesas com recurso a cálculo. Para desmantelar a lâmina hli, é necessário coordenar a rescisão do contrato e o encerramento real da unidade.

## <a name="destination-planning"></a>Planeamento de destino
Erguer uma nova infraestrutura para tomar o lugar de uma existente merece alguma reflexão para garantir que a nova adição se encaixe no grande esquema das coisas.  Abaixo estão alguns pontos-chave para contemplação.

### <a name="resource-availability-in-the-target-region"></a>Disponibilidade de recursos na região alvo 
A atual região de implementação dos servidores de aplicações SAP está tipicamente próxima dos HLIs associados.  No entanto, os HLIs são oferecidos em menos locais do que as regiões azure disponíveis.  Ao migrar o HLI físico para o Azure VM, é também uma boa altura para "afinar" a distância de proximidade de todos os serviços relacionados para otimização de desempenho.  Ao fazê-lo, uma das principais considerações é garantir que a região escolhida dispõe de todos os recursos necessários.  Por exemplo, a disponibilidade de uma certa família VM ou a oferta de Zonas Azure para configuração de alta disponibilidade.

### <a name="virtual-network"></a>Rede virtual 
Os clientes precisam de escolher se devem ou não gerir a nova base de dados HANA numa rede virtual existente ou criar uma nova.  O principal fator decisivo é o atual layout de networking para a paisagem SAP.  Também quando a infraestrutura vai de uma zona para implantação de duas zonas e utiliza O PPG, impõe alterações arquitetónicas. Para mais informações, consulte o artigo [Azure PPG para obter uma latência ótima da rede com aplicação SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios).   

### <a name="security"></a>Segurança
Quer o novo SAP HANA VM aterre numa nova ou existente vnet/subnet, representa um novo serviço crítico de negócios que requer salvaguarda.  O controlo de acesso satisfatório com a política de segurança da empresa deve ser avaliado e implementado para esta nova classe de serviço.

### <a name="vm-sizing-recommendation"></a>Recomendação de dimensionamento VM
Esta migração é também uma oportunidade para o tamanho certo do seu motor de computação HANA.  Pode-se usar [as vistas do sistema](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) HANA em conjunto com o HANA Studio para entender o consumo de recursos do sistema, o que permite o dimensionamento certo para impulsionar a eficiência de gastos.

### <a name="storage"></a>Armazenamento 
O desempenho do armazenamento é um dos fatores que impacta a experiência do utilizador da aplicação SAP.  Base num dado VM SKU, existem configurações de armazenamento mínimo [publicadas SAP HANA Azure configurações](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)de armazenamento de máquinas virtuais . Recomendamos rever estas especificações mínimas e comparar com as estatísticas existentes do sistema HLI para garantir uma capacidade e desempenho adequados para o novo VM HANA.

Se configurar o PPG para o novo VM HANA e as suas severs associadas, submeta um bilhete de apoio para inspecionar e garantir a co-localização do armazenamento e do VM. Uma vez que a sua solução de backup poderá ter de ser alterada, o custo de armazenamento também deve ser revisitado para evitar surpresas de gastos operacionais.

### <a name="storage-replication-for-disaster-recovery"></a>Replicação de armazenamento para recuperação de desastres
Com o HLI, a replicação do armazenamento foi oferecida como a opção padrão para a recuperação do desastre. Esta funcionalidade não é a opção padrão para SAP HANA no Azure VM. Considere hSR, backup/restauro ou outras soluções suportadas que satisfaça as suas necessidades de negócio.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>Conjuntos de disponibilidade, zonas de disponibilidade e grupos de colocação de proximidade 
Para encurtar a distância entre a camada de aplicação e o SAP HANA para manter a latência da rede no mínimo, a nova base de dados VM e os atuais servidores de aplicações SAP devem ser colocados num PPG. Consulte o Grupo de [Colocação de Proximidade](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) para saber como as Zonas de Disponibilidade e Disponibilidade do Azure funcionam com o PPG para implementações sap.
Se os membros do sistema HANA alvo forem implantados em mais de uma Zona Azure, os clientes devem ter uma visão clara do perfil de latência das zonas escolhidas. A colocação de componentes do sistema SAP é ótima no que diz respeito à distância proximal entre a aplicação SAP e a base de dados.  A ferramenta de teste de [latência](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) da zona de disponibilidade do domínio público ajuda a facilitar a medição.  


### <a name="backup-strategy"></a>Estratégia de backup
Muitos clientes já estão a usar soluções de backup de terceiros para o SAP HANA no HLI.  Nesse caso, apenas uma base de dados adicional de VM e HANA protegida supor configurar.  Os trabalhos de apoio ao HLI em curso podem agora ser desprogramados se a máquina estiver a ser desativada após a migração.
O Azure Backup para SAP HANA em VM está agora geralmente disponível.  Consulte estes links para obter informações detalhadas sobre: [Backup](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database), [Restaurar](https://docs.microsoft.com/azure/backup/sap-hana-db-restore), [Gerir](https://docs.microsoft.com/azure/backup/sap-hana-db-manage) cópias de segurança SAP HANA em VMs Azure.

### <a name="dr-strategy"></a>Estratégia DR
Se os seus objetivos de nível de serviço acomodarem um tempo de recuperação mais longo, um simples backup para o armazenamento de bolhas e restaurar no lugar ou restaurar para um novo VM é a estratégia dr mais simples e menos dispendiosa.  
Como a plataforma de grandes instâncias onde hana DR tipicamente é feito com HSR; No Azure VM, o HSR é também a solução SAP HANA DR mais natural e nativa.  Independentemente de a implantação da fonte ser de instância única ou agrupada, é necessária uma réplica da infraestrutura de origem na região DR.
Esta réplica DR será configurada após a migração primária de HLI para VM estar completa.  O DR HANA DB registar-se-á no SAP HANA primário em caso vm como um local de replicação secundária.  

### <a name="sap-application-server-connectivity-destination-change"></a>Mudança de destino de destino de conectividade do servidor de aplicações SAP
A migração hsr resulta num novo hospedeiro HANA DB e, portanto, um novo nome de hospedeiro DB para a camada de aplicação, os perfis SAP precisam de ser modificados para refletir o novo nome de anfitrião.  Se a comutação for feita por resolução de nome que preserve o nome de anfitrião, não é necessária qualquer alteração de perfil.

### <a name="operating-system"></a>Sistema operativo
As imagens do sistema operativo para HLI e VM, apesar de estarem no mesmo nível de libertação, sles 12 SP4, por exemplo, não são idênticas. Os clientes devem validar as embalagens, correções quentes, correções quentes, remendos, núcleos e correções de segurança no HLI para instalar as mesmas embalagens no alvo.  É suportado para usar HSR para replicar de um sistema operativo mais antigo para um VM com uma versão mais recente do SO.  Verifique as versões específicas suportadas revendo a [nota SAP 2763388](https://launchpad.support.sap.com/#/notes/2763388).

### <a name="new-sap-license-request"></a>Novo pedido de licença SAP
Uma simples chamada para pedir uma nova licença SAP para o novo sistema HANA agora que foi migrado para VMs.

### <a name="service-level-agreement-sla-differences"></a>Diferenças no contrato de nível de serviço (SLA)
Os autores gostam de chamar a atenção para a diferença de disponibilidade sla entre HLI e Azure VM.  Por exemplo, os pares HLIs HA agrupados oferecem 99,99% de disponibilidade. Para conseguir o mesmo SLA, é necessário implantar VMs em zonas de disponibilidade. Este [artigo](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) descreve a disponibilidade com arquiteturas de implantação associadas para que os clientes possam planear a sua infraestrutura-alvo em conformidade.


## <a name="migration-strategy"></a>Estratégia de migração
Neste documento, cobrimos apenas a abordagem de replicação do sistema HANA para a migração do HLI para o Azure VM.  Depende da solução de armazenamento-alvo implementada, o processo difere ligeiramente. Os passos de alto nível são descritos abaixo.

### <a name="vm-with-premiumultra-disks-for-data"></a>VM com premium/ultra-discos para dados
Para VMs que são implantados com premium ou ultra-discos, a configuração padrão de replicação do sistema SAP HANA é aplicável para a configuração de HSR.  O artigo de [ajuda SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html) fornece uma visão geral dos passos envolvidos na criação da replicação do sistema, assumindo um sistema secundário, falhando na replicação primária e incapacitante do sistema.  Para efeitos da migração, só precisaremos da configuração, tomada e desativação dos passos de replicação.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>VM com ANF para dados e volumes de registo
A um nível elevado, os mais recentes instantâneos de armazenamento de HLI dos dados completos e volumes de registo devem ser copiados para o Armazenamento Azure, onde são acessíveis e recuperáveis pelo alvo HANA VM.  O processo de cópia pode ser feito com quaisquer ferramentas de cópia linux nativas.  

> [!IMPORTANT]
> A cópia e a transferência de dados podem demorar horas depende do tamanho da base de dados HANA e da largura de banda da rede.  A maior parte do processo de cópia deve ser feita antes do tempo de inatividade primário do HANA DB.

### <a name="mcos-to-mdc-conversion"></a>MCOS para Conversão MDC
O modelo de implementação de Múltiplos Componentes no One System (MCOS) foi utilizado por alguns dos nossos clientes HLI.  A motivação foi contornar a limitação instantânea do armazenamento de várias bases de dados (MDC) das versões anteriores do SAP HANA.  No modelo MCOS, várias instâncias independentes do SAP HANA são empilhadas numa lâmina HLI.  A utilização de HSR para a migração funcionaria bem, mas resultando em múltiplos VMs HANA com um inquilino DB cada.  Este estado final torna uma paisagem mais movimentada do que o que um cliente poderia estar habituado.  Com a implantação padrão SAP HANA 2.0 sendo MDC, uma alternativa viável é executar movimento de [inquilino HANA](https://launchpad.support.sap.com/#/notes/2472478) após a migração do HSR.  Este processo "consolida" estas bases de dados independentes da HANA em coinquilinos num único contentor HANA.  

### <a name="application-layer-consideration"></a>Consideração da camada de aplicação
O servidor DB é visto como o centro de um sistema SAP.  Todos os servidores de aplicações devem estar localizados perto do SAP HANA DB.  Em alguns casos, quando é desejada uma nova utilização do PPG, pode ser necessária a deslocalização dos servidores de aplicações existentes para o PPG onde o VM HANA é necessário.  A construção de novos servidores de aplicações pode ser considerada mais fácil se já tiver modelos de implementação à mão.  
Se os servidores de aplicações existentes e o novo VM HANA estiverem na melhor posição, não é necessário construir novos servidores de aplicações, a menos que seja necessária capacidade adicional.  
Se for construída uma nova infraestrutura para melhorar a disponibilidade do serviço, os servidores de aplicações existentes podem tornar-se desnecessários e devem ser desligados e eliminados.
Se o nome de anfitrião do VM alvo alterado e diferir do nome de anfitrião HLI, os perfis do servidor de aplicação SAP precisam de ser ajustados para apontar para o novo anfitrião.  Se apenas o endereço IP HANA DB tiver mudado, é necessária uma atualização de registo DNS para liderar as ligações de entrada para o novo HanA VM.

### <a name="acceptance-test"></a>Teste de aceitação
Embora a migração do HLI para vM não faça alterações materiais para o conteúdo da base de dados em comparação com uma migração heterogénea, recomendamos ainda validar as principais funcionalidades e aspeto de desempenho da nova configuração.  

### <a name="cutover-plan"></a>Plano cutover
Embora esta migração seja direta, no entanto envolve o desmantelamento de um DB existente.  Um planeamento cuidadoso para preservar o sistema de origem com o seu conteúdo associado e as imagens de backup são fundamentais no caso de ser necessário um recuo.  Um bom planeamento oferece uma inversão mais rápida.   


## <a name="post-migration"></a>Pós-migração
O trabalho de migração não é feito até termos dissociado com segurança quaisquer serviços dependentes do HLI ou conectividade para garantir a preservação da integridade dos dados.  Além disso, encerrar serviços desnecessários.  Esta secção chama alguns itens topo de mente.

### <a name="decommissioning-the-hli"></a>Desmantelamento do HLI
Após uma migração bem sucedida do HANA DB para o Azure VM, garanta que não havendo transações comerciais produtivas no HLI DB.  No entanto, manter o HLI em funcionamento por um período de tempo é igual à sua janela de retenção de cópias de segurança local é uma prática segura que garanta uma recuperação mais rápida, se necessário.  Só então a lâmina HLI deve ser desativada.  Os clientes devem concluir contratualmente os seus compromissos de HLI com a Microsoft contactando os seus representantes da Microsoft.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>Remova qualquer procuração (ex: Iptables, BIGIP) configurada para HLI 
Se um serviço de procuração como o IPTables for utilizado para direcionar o tráfego no local de e para o HLI, já não é necessário após a migração bem sucedida para vm.  No entanto, este serviço de conectividade deve ser mantido enquanto a lâmina HLI ainda estiver de prontida.  Só desligue o serviço depois de a lâmina HLI estar totalmente desativada.

### <a name="remove-global-reach-for-hli"></a>Remover o Alcance Global para o HLI 
O Global Reach é utilizado para ligar o gateway ExpressRoute dos clientes ao gateway HLI ExpressRoute.  Permite que o tráfego no local dos clientes chegue diretamente ao inquilino do HLI sem recurso a um serviço de procuração.  Esta ligação já não é necessária na ausência da unidade hli após a migração.  Tal como o caso do serviço de procuração IPTables, o GlobalReach também deve ser mantido até que a lâmina HLI esteja totalmente desativada.

### <a name="operating-system-subscription--movereuse"></a>Subscrição do sistema operativo - movimento/reutilização
À medida que os servidores VM são suportados e as lâminas HLI são desativadas, as subscrições de So podem ser substituídas ou reutilizadas para evitar o dobro do pagamento das licenças de SO.



## <a name="next-steps"></a>Passos seguintes
Veja estes artigos:
- [Configurações e operações de infraestruturas SAP HANA no Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Cargas de trabalho SAP em Azure: lista de verificação de planeamento e implementação](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist).
