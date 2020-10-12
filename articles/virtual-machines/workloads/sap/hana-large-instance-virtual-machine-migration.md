---
title: Migração SAP HANA em Azure (Grandes Instâncias) para máquinas virtuais Azure Microsoft Docs
description: Como migrar o SAP HANA em Azure (Grandes Instâncias) para máquinas virtuais Azure
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
ms.openlocfilehash: db51ec682f43366f5637c461e3fe4037dec8e364
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87085219"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>SAP HANA em Azure Large Instance migração para Azure Virtual Machines
Este artigo descreve possíveis cenários de implantação de Azure Large Instance e oferece abordagem de planeamento e migração com tempo de inatividade de transição minimizado

## <a name="overview"></a>Descrição geral
Desde o anúncio do Azure Large Instances for SAP HANA (HLI) em setembro de 2016, muitos clientes adotaram este hardware como uma oferta de serviço para a sua plataforma de computação em memória.  Nos últimos anos, a extensão do tamanho do Azure VM, juntamente com o suporte da implantação de escala HANA, excedeu a procura de capacidade de capacidade de base de dados ERP da maioria dos clientes empresariais.  Começamos a ver clientes expressando o interesse em migrar a sua carga de trabalho SAP HANA de servidores físicos para VMs Azure.
Este guia não é um documento de configuração passo a passo. Descreve os modelos de implantação comuns e oferece conselhos de planeamento e migração.  A intenção é chamar a atenção para as considerações necessárias para a preparação para minimizar o tempo de inatividade da transição.

## <a name="assumptions"></a>Pressupostos
Este artigo faz os seguintes pressupostos:
- O único interesse considerado é uma migração homogénea do serviço de computação hana de Hana Large Instance (HLI) para Azure VM sem uma atualização ou remendamento significativo de software. Estas atualizações menores incluem a utilização de uma versão de SO mais recente ou versão HANA explicitamente indicada como suportada por notas SAP relevantes. 
- Todas as atualizações/atualizações têm de ser feitas antes ou depois da migração.  Por exemplo, SAP HANA MCOS convertendo-se para implantação de MDC. 
- A abordagem de migração que ofereceria menos tempo de inatividade é a replicação do sistema SAP HANA. Outros métodos de migração não fazem parte do âmbito deste documento.
- Esta orientação é aplicável tanto para rev3 como rev4 SKUs de HLI.
- A arquitetura de implantação hana permanece principalmente inalterada durante a migração.  Ou seja, um sistema com uma única instância DR permanecerá da mesma forma no destino.
- Os clientes analisaram e compreenderam o Acordo de Nível de Serviço (SLA) da arquitetura-alvo (a ser). 
- Os termos comerciais entre HLIs e VMs são diferentes. Os clientes devem monitorizar o uso dos seus VMs para a gestão de custos.
- Os clientes entendem que o HLI é uma plataforma de computação dedicada, enquanto os VMs funcionam em infraestruturas partilhadas mas isoladas.
- Os clientes validaram que os VMs-alvo suportam a sua arquitetura pretendida. Para ver todos os SKUs VM suportados certificados para a implementação sap HANA, consulte o [diretório de hardware SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
- Os clientes validaram o plano de conceção e migração.
- Plano para recuperação de desastres VM juntamente com o local principal.  Os clientes não podem usar o HLI como nó DR para o site principal em execução em VMs após a migração.
- Os clientes copiaram os ficheiros de backup necessários para direcionar os VMs, com base nos requisitos de recuperação e conformidade do negócio. Com cópias de segurança acessíveis em VM, permite uma recuperação pontual durante o período de transição.
- Para o HSR HA, os clientes precisam de configurar e configurar o dispositivo STONITH per sap HANA HA para [sles](./high-availability-guide-suse-pacemaker.md) e [RHEL](./high-availability-guide-rhel-pacemaker.md).  Não está pré-configurado como o caso HLI.
- Esta abordagem de migração não cobre os SKUs HLI com a configuração optane.

## <a name="deployment-scenarios"></a>Cenários de implementação
Os modelos de implantação comuns com os clientes HLI são resumidos na tabela seguinte.  A migração para Azure VMs para todos os cenários HLI é possível.  Para beneficiar dos serviços complementares da Azure disponíveis, podem ser necessárias pequenas alterações arquitetónicas.

| ID do cenário | Cenário HLI | Migrar para vM verbatim? | Observação |
| --- | --- | --- | --- |
| 1 | [Nó único com um SID](./hana-supported-scenario.md#single-node-with-one-sid) | Sim | - |
| 2 | [Nó único com MCOS](./hana-supported-scenario.md#single-node-mcos) | Sim | - |
| 3 | [Nó único com DR usando replicação de armazenamento](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication) | Não | A replicação de armazenamento não está disponível com a plataforma virtual Azure, alterar a atual solução DR para HSR ou backup/restaurar |
| 4 | [Nó único com DR (multiusos) usando replicação de armazenamento](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication) | Não | A replicação de armazenamento não está disponível com a plataforma virtual Azure, alterar a atual solução DR para HSR ou backup/restaurar |
| 5 | [HSR com STONITH para alta disponibilidade](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability) | Sim | Não há SBD pré-configurado para VMs alvo.  Selecione e desloque uma solução STONITH.  Opções possíveis: Agente de Esgrima Azure (suportado tanto para [RHEL](./high-availability-guide-rhel-pacemaker.md), [SLES),](./high-availability-guide-suse-pacemaker.md)SBD |
| 6 | [HA com HSR, DR com replicação de armazenamento](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication) | Não | Substitua a replicação de armazenamento por necessidades de DR por HSR ou backup/restauro |
| 7 | [Realização de falha automática (1+1)](./hana-supported-scenario.md#host-auto-failover-11) | Sim | Utilize ANF para armazenamento partilhado com VMs Azure |
| 8 | [Escala-out com standby](./hana-supported-scenario.md#scale-out-with-standby) | Sim | BW/4HANA com M128s, M416s, M416ms VMs usando ANF apenas para armazenamento |
| 9 | [Escala sem standby](./hana-supported-scenario.md#scale-out-without-standby) | Sim | BW/4HANA com M128s, M416s, M416ms VMs (com ou sem utilização de ANF para armazenamento) |
| 10 | [Escala com DR usando replicação de armazenamento](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication) | Não | Substitua a replicação de armazenamento por necessidades de DR por HSR ou backup/restauro |
| 11 | [Nó único com DR usando HSR](./hana-supported-scenario.md#single-node-with-dr-using-hsr) | Sim | - |
| 12 | [Único nó HSR a DR (otimizado em custos)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized) | Sim | - |
| 13 | [HA e DR com HSR](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr) | Sim | - |
| 14 | [HA e DR com HSR (otimizado em custos)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | Sim | - |
| 15 | [Escala com DR usando HSR](./hana-supported-scenario.md#scale-out-with-dr-using-hsr) | Sim | BW/4HANA com M128s. M416s, M416ms VMs (com ou sem utilização de ANF para armazenamento) |


## <a name="source-hli-planning"></a>Planeamento de fonte (HLI)
Ao embarcar num servidor HLI, tanto a Microsoft Service Management como os clientes passaram pelo planeamento das definições específicas do computação, rede, armazenamento e oses específicos do SISTEMA para executar a base de dados SAP HANA.  É necessário um planeamento semelhante para a migração para a Azure VM.

### <a name="sap-hana-housekeeping"></a>Limpeza SAP HANA 
É uma boa prática operacional arrumar o conteúdo da base de dados para que dados indesejados e desatualizados ou registos velhos não sejam migrados para a nova base de dados.  A limpeza geralmente envolve a eliminação ou arquivamento de dados antigos, expirados ou inativos.  Estas ações de "higiene dos dados" devem ser testadas em sistemas não produtivos para validar a sua validade antes da utilização da produção.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>Permitir conectividade de rede para novos VMs e, ou rede virtual 
Na implementação de HLI de um cliente, a rede foi criada com base nas informações descritas no artigo [SAP HANA (Grandes Instâncias) arquitetura de rede](./hana-network-architecture.md). Além disso, o encaminhamento de tráfego de rede é feito da forma descrita na secção "Encaminhamento em Azure".
- Ao configurar um novo VM como alvo de migração, se for colocado na rede virtual existente com intervalos de endereços IP já autorizados a ligar-se ao HLI, não é necessária mais nenhuma atualização de conectividade.
- Se o novo Azure VM for colocado numa nova Rede Virtual Microsoft Azure, pode estar noutra região, e acompanhado pela rede virtual existente, a chave de serviço ExpressRoute e o ID de recursos a partir do fornecimento original de HLI são utilizáveis para permitir o acesso a esta nova gama IP de rede virtual.  Coordene com a Microsoft Service Management para permitir a conectividade da rede virtual à conectividade HLI.  Nota: Para minimizar a latência da rede entre as camadas de aplicação e base de dados, tanto as camadas de aplicação como de base de dados devem estar na mesma rede virtual.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>Conjunto de disponibilidade de camadas de aplicativo existente, zonas de disponibilidade e grupo de colocação de proximidade (PPG)
O atual modelo de implantação é feito para satisfazer determinados objetivos de nível de serviço.  Neste movimento, certifique-se de que a infra-estrutura-alvo irá cumprir ou exceder as metas definidas.  
Mais provável que não, os servidores de aplicações SAP dos clientes são colocados num conjunto de disponibilidade.  Se o nível atual de serviço de implantação for satisfatório e 
- Se o VM-alvo assumir o nome de anfitrião do nome lógico HLI, a atualização da resolução do endereço do serviço de nome de domínio (DNS) que aponta para o IP do VM funcionaria sem atualizar quaisquer perfis SAP
- Se não estiver a utilizar o PPG, certifique-se de colocar todos os servidores de aplicação e DB na mesma zona para minimizar a latência da rede.
- Se estiver a utilizar o PPG, consulte a secção deste documento: 'Destination Planning, Availability set, Availability Zones e Closey Placement Group (PPG)'.

### <a name="storage-replication-discontinuance-process-if-used"></a>Processo de descontinuação de replicação de armazenamento (se utilizado)
Se a replicação do armazenamento for utilizada como solução DR, deve ser encerrada (não programada) depois de a aplicação SAP ter sido encerrada.  Além disso, o último catálogo SAP HANA, ficheiro de registo e backups de dados foram replicados nos volumes remotos de armazenamento DR HLI.  Fazê-lo como precaução no caso de um desastre acontecer durante o servidor físico para a transição VM Azure.

### <a name="data-backups-preservation-consideration"></a>Consideração de preservação de backups de dados
Após o corte para SAP HANA em Azure VM, todos os dados baseados em instantâneos ou cópias de segurança de registo no HLI não são facilmente acessíveis ou restaurados a um VM, se necessário. No período de transição precoce, antes de o backup baseado no Azure construir histórico suficiente para satisfazer os requisitos de recuperação do ponto no tempo, recomendamos a tomada de backups de nível de ficheiros para além de instantâneos no HLI, dias ou semanas antes do corte.  Coser estas cópias de segurança para uma conta de Armazenamento Azure acessível pelo novo SAP HANA VM.
Além de apoiar o conteúdo do HLI, é prudente ter cópias de segurança completas da paisagem SAP facilmente acessíveis no caso de ser necessário um revés.

### <a name="adjusting-system-monitoring"></a>Regulação da monitorização do sistema 
Os clientes usam muitas ferramentas diferentes para monitorizar e enviar notificações de alerta para sistemas dentro da sua paisagem SAP.  Este item é apenas uma chamada para as medidas adequadas para incorporar alterações para monitorizar e atualizar os destinatários da notificação de alerta, se necessário.

### <a name="microsoft-operations-team-involvement"></a>Envolvimento da equipa da Microsoft Operations 
Abra um bilhete a partir do portal Azure com base na instância HLI existente.  Após a criação do bilhete de apoio, um engenheiro de suporte entrará em contacto consigo por e-mail.  

### <a name="engage-microsoft-account-team"></a>Engage Microsoft account team
Planeie a migração perto do tempo de renovação do aniversário do contrato HLI para minimizar as despesas desnecessárias em relação ao recurso computacional. Para desativar a lâmina HLI, é necessário coordenar a rescisão do contrato e o encerramento real da unidade.

## <a name="destination-planning"></a>Planeamento de destinos
Erguer uma nova infraestrutura para ocupar o lugar de uma existente merece alguma reflexão para garantir que o novo acréscimo se enquadre no vasto esquema das coisas.  Abaixo estão alguns pontos-chave para a contemplação.

### <a name="resource-availability-in-the-target-region"></a>Disponibilidade de recursos na região alvo 
A atual região de implementação de servidores de aplicações SAP está tipicamente próxima dos HLIs associados.  No entanto, os HLIs são oferecidos em menos locais do que as regiões de Azure disponíveis.  Ao migrar o HLI físico para Azure VM, é também uma boa altura para "afinar" a distância de proximidade de todos os serviços relacionados para otimização de desempenho.  Ao fazê-lo, uma consideração fundamental é assegurar que a região escolhida tenha todos os recursos necessários.  Por exemplo, a disponibilidade de certa família VM ou a oferta de Zonas Azure para uma configuração de alta disponibilidade.

### <a name="virtual-network"></a>Rede virtual 
Os clientes têm de escolher se executam a nova base de dados HANA numa rede virtual existente ou se criam uma nova.  O principal fator decisivo é o layout atual de networking para a paisagem SAP.  Também quando a infraestrutura vai de uma zona para duas zonas de implantação e utiliza PPG, impõe mudança arquitetónica. Para obter mais informações, consulte o artigo [Azure PPG para obter a latência ideal da rede com a aplicação SAP](./sap-proximity-placement-scenarios.md).   

### <a name="security"></a>Segurança
Quer o novo SAP HANA VM aterre numa nova vnet/subnet existente, representa um novo serviço crítico de negócio que requer salvaguarda.  O controlo de acessos em conformidade com a política de segurança de informações da empresa deve ser avaliado e implementado para esta nova classe de serviço.

### <a name="vm-sizing-recommendation"></a>Recomendação de dimensionamento de VM
Esta migração é também uma oportunidade para corrigir o tamanho do seu motor computacional HANA.  Pode-se usar [as vistas do sistema](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) HANA em conjunto com o HANA Studio para entender o consumo de recursos do sistema, o que permite o tamanho certo para impulsionar a eficiência de gastos.

### <a name="storage"></a>Armazenamento 
O desempenho do armazenamento é um dos fatores que impacta a experiência do utilizador da aplicação SAP.  Base em um dado VM SKU, existem configurações de armazenamento mínimo [publicadas CONFIGURAÇÕES DE ARMAZENAMENTO HANA Azure de armazenamento virtual](./hana-vm-operations-storage.md). Recomendamos a revisão destas especificações mínimas e comparando com as estatísticas do sistema HLI existentes para garantir a capacidade e desempenho adequados da OI para o novo HANA VM.

Se configurar o PPG para o novo HANA VM e as suas severidades associadas, envie um bilhete de apoio para inspecionar e garantir a co-localização do armazenamento e do VM. Uma vez que a sua solução de backup pode ter de ser alterada, o custo de armazenamento também deve ser revisitado para evitar surpresas de gastos operacionais.

### <a name="storage-replication-for-disaster-recovery"></a>Replicação de armazenamento para recuperação de desastres
Com o HLI, a replicação de armazenamento foi oferecida como a opção padrão para a recuperação de desastres. Esta função não é a opção padrão para SAP HANA em Azure VM. Considere a HSR, backup/restauro ou outras soluções suportadas que satisfaçam as suas necessidades de negócio.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>Conjuntos de disponibilidade, zonas de disponibilidade e grupos de colocação de proximidade 
Para encurtar a distância entre a camada de aplicação e o SAP HANA para manter a latência da rede no mínimo, a nova base de dados VM e os atuais servidores de aplicações SAP devem ser colocados num PPG. Consulte o [Grupo de Colocação de Proximidade](./sap-proximity-placement-scenarios.md) para saber como funciona o Conjunto de Disponibilidades e Zonas de Disponibilidade do Azure com o PPG para implementações DE SAP.
Se os membros do sistema HANA alvo forem implantados em mais de uma Zona Azure, os clientes devem ter uma visão clara do perfil de latência das zonas escolhidas. A colocação de componentes do sistema SAP é ótima no que diz respeito à distância proximal entre a aplicação SAP e a base de dados.  A ferramenta de [teste de latência de zona de](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) disponibilidade de domínio público ajuda a facilitar a medição.  


### <a name="backup-strategy"></a>Estratégia de backup
Muitos clientes já estão a utilizar soluções de backup de terceiros para o SAP HANA na HLI.  Neste caso, apenas é necessário configurar uma base de dados adicional de VM e HANA.  Os postos de trabalho de reserva em curso no HLI podem agora não ser programados se a máquina for desmantelada após a migração.
A azure Backup para SAP HANA em VM está geralmente disponível.  Consulte estes links para obter informações detalhadas sobre: [Backup](../../../backup/backup-azure-sap-hana-database.md), [Restaurar,](../../../backup/sap-hana-db-restore.md) [Gerir](../../../backup/sap-hana-db-manage.md) a cópia de segurança SAP HANA em VMs Azure.

### <a name="dr-strategy"></a>Estratégia DR
Se os seus objetivos de nível de serviço acomodarem um tempo de recuperação mais longo, uma cópia de segurança simples para o armazenamento de bolhas e restaurar no lugar ou restaurar para um novo VM é a estratégia de DR mais simples e menos dispendiosa.  
Como a grande plataforma de instância onde HANA DR normalmente é feito com HSR; No Azure VM, o HSR é também a solução SAP HANA DR mais natural e nativa.  Independentemente de a implantação da fonte ser de instância única ou agrupada, é necessária uma réplica da infraestrutura de origem na região DR.
Esta réplica DR será configurada após a migração primária do HLI para vM estar completa.  O DR HANA DB registar-se-á no HANA primário em caso VM como um local de replicação secundária.  

### <a name="sap-application-server-connectivity-destination-change"></a>Alteração do destino de conectividade do servidor de aplicações SAP
A migração do HSR resulta num novo hospedeiro HANA DB e, portanto, um novo nome de anfitrião DB para a camada de aplicação, os perfis SAP precisam de ser modificados para refletir o novo nome de hospedeiro.  Se a comutação for feita por resolução de nome preservando o nome de anfitrião, não é necessária qualquer alteração de perfil.

### <a name="operating-system"></a>Sistema operativo
As imagens do sistema operativo para HLI e VM, apesar de estarem no mesmo nível de libertação, sLES 12 SP4, por exemplo, não são idênticas. Os clientes devem validar as embalagens necessárias, correções quentes, patches, kernel e correções de segurança no HLI para instalar os mesmos pacotes no alvo.  É suportado para usar HSR para replicar de um sistema operativo mais antigo para um VM com uma versão mais recente do SO.  Verifique as versões suportadas específicas através da revisão da [nota SAP 2763388](https://launchpad.support.sap.com/#/notes/2763388).

### <a name="new-sap-license-request"></a>Novo pedido de licença SAP
Uma simples chamada para pedir uma nova licença SAP para o novo sistema HANA agora que foi migrado para VMs.

### <a name="service-level-agreement-sla-differences"></a>Diferenças de contrato de nível de serviço (SLA)
Os autores gostam de chamar a atenção para a diferença de disponibilidade SLA entre HLI e Azure VM.  Por exemplo, os pares HLIs HA agrupados oferecem 99,99% de disponibilidade. Para alcançar o mesmo SLA, é necessário implantar VMs em zonas de disponibilidade. Este [artigo](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) descreve a disponibilidade com arquiteturas de implantação associadas para que os clientes possam planear a sua infraestrutura-alvo em conformidade.


## <a name="migration-strategy"></a>Estratégia de migração
Neste documento, abrangemos apenas a abordagem de replicação do sistema HANA para a migração de HLI para Azure VM.  Depende da solução de armazenamento alvo implementada, o processo difere ligeiramente. Os passos de alto nível são descritos abaixo.

### <a name="vm-with-premiumultra-disks-for-data"></a>VM com discos premium/ultra para dados
Para os VMs que são implantados com discos premium ou ultra-discos, a configuração padrão de replicação do sistema SAP HANA é aplicável para a configuração do HSR.  O [artigo de ajuda SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html) fornece uma visão geral dos passos envolvidos na criação da replicação do sistema, assumindo um sistema secundário, falhando de volta à primária, e desativando a replicação do sistema.  Para efeitos da migração, só precisamos da configuração, tomada de posse e desativação de etapas de replicação.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>VM com ANF para volumes de dados e registos
A um nível elevado, as mais recentes imagens de armazenamento de HLI dos dados completos e volumes de registo precisam de ser copiadas para o Azure Storage, onde são acessíveis e recuperáveis pelo HANA VM alvo.  O processo de cópia pode ser feito com quaisquer ferramentas de cópia Linux nativas.  

> [!IMPORTANT]
> Copiar e transferir dados pode demorar horas depende do tamanho da base de dados HANA e da largura de banda da rede.  A maior parte do processo de cópia deve ser feita antes do tempo de inatividade primário do HANA DB.

### <a name="mcos-to-mdc-conversion"></a>Conversão MCOS para MDC
O modelo de implementação de múltiplos componentes num sistema (MCOS) foi utilizado por alguns dos nossos clientes HLI.  A motivação foi contornar a limitação instantânea do depósito de várias bases de dados (MDC) de versões anteriores do SAP HANA.  No modelo MCOS, várias instâncias INDEPENDENTEs SAP HANA são empilhadas numa lâmina HLI.  A utilização de HSR para a migração funcionaria bem, mas resultando em vários VMs HANA com um inquilino DB cada.  Este estado final torna uma paisagem mais movimentada do que o que um cliente poderia estar habituado.  Com a implementação padrão SAP HANA 2.0 sendo MDC, uma alternativa viável é realizar movimento [de inquilino HANA](https://launchpad.support.sap.com/#/notes/2472478) após a migração de HSR.  Este processo "consolida" estas bases de dados independentes da HANA em cotenantes num único contentor HANA.  

### <a name="application-layer-consideration"></a>Consideração da camada de aplicação
O servidor DB é visto como o centro de um sistema SAP.  Todos os servidores de aplicações devem estar localizados perto do SAP HANA DB.  Em alguns casos, quando é desejada uma nova utilização do PPG, a deslocalização dos servidores de aplicações existentes para o PPG onde o HANA VM é necessário.  A construção de novos servidores de aplicações pode ser considerada mais fácil se já tiver modelos de implementação à mão.  
Se os servidores de aplicações existentes e os novos HANA VM estiverem idealmente localizados, não é necessário construir novos servidores de aplicações a menos que seja necessária capacidade adicional.  
Se for construída uma nova infraestrutura para aumentar a disponibilidade do serviço, os servidores de aplicações existentes podem tornar-se desnecessários e devem ser desligados e eliminados.
Se o nome de anfitrião VM alvo mudou e diferiu do nome de anfitrião HLI, os perfis do servidor de aplicações SAP precisam de ser ajustados para apontar para o novo anfitrião.  Se apenas o endereço IP HANA DB tiver sido alterado, é necessária uma atualização de registo DNS para conduzir as ligações de entrada ao novo HANA VM.

### <a name="acceptance-test"></a>Teste de aceitação
Embora a migração de HLI para VM não altere materialmente o conteúdo da base de dados em comparação com uma migração heterogénea, recomendamos ainda validar funcionalidades-chave e aspeto de desempenho da nova configuração.  

### <a name="cutover-plan"></a>Plano cutover
Embora esta migração seja direta, no entanto, envolve o desmantelamento de um DB existente.  Um planeamento cuidadoso para preservar o sistema de origem com o seu conteúdo associado e imagens de backup são fundamentais para o caso de ser necessário um recuo.  Um bom planeamento oferece uma inversão mais rápida.   


## <a name="post-migration"></a>Pós-migração
O trabalho de migração não é feito até termos dissociado com segurança quaisquer serviços dependentes do HLI ou conectividade para garantir a integridade dos dados.  Além disso, encerre serviços desnecessários.  Esta secção chama alguns itens topo de série.

### <a name="decommissioning-the-hli"></a>Desmantelamento do HLI
Após uma migração bem sucedida do HANA DB para a Azure VM, certifique-se de que não existem transações comerciais produtivas no DB HLI.  No entanto, manter o HLI a funcionar durante um período de tempo igual à sua janela de retenção de backup local é uma prática segura que garante uma recuperação mais rápida, se necessário.  Só então a lâmina HLI deve ser desativada.  Os clientes devem concluir contratualmente os seus compromissos com a Microsoft contactando os seus representantes da Microsoft.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>Remover qualquer proxy (ex: Iptables, BIGIP) configurado para HLI 
Se um serviço de procuração como o IPTables for utilizado para encaminhar o tráfego no local de e para o HLI, deixará de ser necessário após a migração bem sucedida para VM.  No entanto, este serviço de conectividade deve ser mantido enquanto a lâmina HLI ainda estiver em pé.  Só desligue o serviço depois de a lâmina HLI estar totalmente desativada.

### <a name="remove-global-reach-for-hli"></a>Remover alcance global para HLI 
O Global Reach é utilizado para ligar o gateway ExpressRoute dos clientes ao gateway HLI ExpressRoute.  Permite que o tráfego dos clientes no local chegue diretamente ao inquilino do HLI sem a utilização de um serviço de procuração.  Esta ligação já não é necessária na ausência da unidade HLI após a migração.  Tal como no caso do serviço de procuração IPTables, a GlobalReach também deve ser mantida até que a lâmina HLI seja totalmente desativada.

### <a name="operating-system-subscription--movereuse"></a>Assinatura do sistema operativo – movimento/reutilização
À medida que os servidores VM são levantados e as lâminas HLI são desativadas, as subscrições de SO podem ser substituídas ou reutilizadas para evitar o duplo pagamento das licenças de SO.



## <a name="next-steps"></a>Passos seguintes
Veja estes artigos:
- [Configurações e operações de infraestrutura SAP HANA em Azure](./hana-vm-operations.md).
- [Cargas de trabalho SAP em Azure: lista de verificação de planeamento e implantação](./sap-deployment-checklist.md).
