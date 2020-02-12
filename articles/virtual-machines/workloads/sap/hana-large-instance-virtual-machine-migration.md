---
title: Migração SAP HANA em Azure (Grandes Instâncias) para máquinas virtuais Azure Microsoft Docs
description: Como migrar sAP HANA em Azure (Grandes Instâncias) para máquinas virtuais Azure
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2dd01bf60104939fcf1f9bd1ccec0e7d72710041
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154923"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>SAP HANA sobre migração de grandes instâncias azure para máquinas virtuais azure
Este artigo descreve possíveis cenários de implantação de grandes instâncias do Azure e oferece abordagem de planeamento e migração com tempo de transição minimizado

## <a name="overview"></a>Descrição geral
Desde o anúncio do Azure Large Instances for SAP HANA (HLI) em setembro de 2016, muitos clientes têm adotado este hardware como um serviço para a sua plataforma de computação em memória.  Nos últimos anos, a extensão de escalabilidade Azure VM combinada com o apoio do modelo de implantação à escala HANA ultrapassou a procura de capacidade de base de dados ERP da maioria dos clientes empresariais.  Começamos a ver clientes expressando o desejo de migrar a sua carga de trabalho SAP HANA de servidores físicos para VMs Azure.
Este guia não é um documento de configuração passo a passo, mas sim, descreve os modelos comuns de implantação e oferece planeamento, a migração aconselha com a intenção de chamar as considerações necessárias para a preparação para minimizar o tempo de transição.

## <a name="assumptions"></a>Pressupostos
Este artigo faz as seguintes suposições:
- O único interesse considerado é uma migração homogénea do serviço de computação de base de dados HANA de Hana Large Instance (HLI) para Azure VM sem atualização ou remendo significativo de software. Estas pequenas atualizações incluem a utilização de uma versão os mais recente ou versão HANA explicitamente indicada como suportada por notas SAP relevantes. 
- Todas as atualizações/atualizações se desejarem ser realizadas antes ou depois da migração.  Por exemplo, o SAP HANA MCOS converte-se na implantação do MDC. 
- A abordagem migratória que ofereceria menos tempo de inatividade é a Replicação do Sistema SAP HANA. Outros métodos de migração não fazem parte do âmbito de aplicação deste documento.
- Isto é aplicável tanto para Rev3 como Para Rev4 SKUs de HLI.
- A arquitetura de implantação da HANA permanece principalmente inalterada durante a migração.  Ou seja, um sistema com uma única instância dr permanecerá da mesma forma no destino.
- Os clientes analisaram e compreenderam o Acordo de Nível de Serviço (SLA) da arquitetura alvo (a ser). 
- Devido às diferenças em termos comerciais entre ASE e VMs, os clientes devem monitorizar a utilização dos seus VMs para a gestão de custos.
- Os clientes compreendem e reconhecem que o HLI é uma plataforma computacional dedicada. No entanto, os VMs funcionam em infraestruturas partilhadas, que são seguras e isoladas de outros inquilinos.
- Os clientes validaram esse alvo vMs suportam a sua arquitetura pretendida. Para ver todos os VM SKUs suportados certificados para a implementação do SAP HANA, consulte o diretório de [hardware SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
- Os clientes validaram o plano de conceção e migração.
- Plano para o nó de recuperação de desastres, juntamente com o local principal.  Os clientes não poderão usar o nó HLI DR para o local primário em execução em VMs após a migração.
- Os clientes copiaram os ficheiros de backup necessários para os VMs-alvo, com base nos requisitos de recuperabilidade e conformidade das empresas. Isto é para cobrir as necessidades de recuperação pontual durante o período de transição.
- Para o HSR HA, os clientes precisam de configurar e configurar o dispositivo STONITH por guias SAP HANA HA para [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) e [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker).  Não está pré-configurado como o caso hli.
- Esta abordagem de migração não abrange as SKUs HLI com a configuração Optane.

## <a name="deployment-scenarios"></a>Cenários de implementação
Os modelos comuns de implantação com clientes HLI são resumidos na tabela seguinte.  A migração para VMs Azure para todos os cenários de HLI é possível.  Alguns cenários podem exigir uma pequena modificação arquitetónica para obter vantagens das ofertas atuais de serviços Azure.

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

### <a name="sap-hana-housekeeping"></a>Limpeza SAP HANA 
Antes de migrar a base de dados da HANA, é uma boa prática arrumar o conteúdo da base de dados para que dados indesejados e desatualizados ou registos velhos não sejam migrados para a nova base de dados.  A limpeza envolve geralmente a apagar ou arquivar dados antigos, expirados ou inativos.  Por conseguinte, estas ações de "higiene de dados" devem ser testadas em sistemas não produtivos para validar a sua validade de aparas de dados antes da utilização da produção.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>Permitir a conectividade da rede para novos VMs e, ou rede virtual 
Na implantação do HLI de um cliente, a conectividade da rede a partir de vnets Azure foi estabelecida com base nas informações descritas no artigo [SAP HANA (Grandes Instâncias) arquitetura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)de rede. Além disso, o encaminhamento de tráfego de rede é feito da forma descrita na secção 'Encaminhamento em Azure'.
1. Para efeitos da criação de um novo VM como alvo HSR para a migração, Se o novo Azure VM for colocado na rede virtual existente com gamas de endereços IP, que já têm permissão para se ligar ao nó HLI, não é necessária mais nenhuma atualização de conectividade HLI
2. Se o novo Azure VM for colocado numa nova rede virtual (pode estar noutra região) e espreitar pela rede virtual existente, a chave de serviço ExpressRoute e o Id de Recursos do fornecimento original de HLI podem ser utilizados para permitir o acesso a esta nova gama IP da rede virtual.  Coordene com a Microsoft Service Management para permitir a conectividade da rede virtual.  Nota: Para minimizar a latência da rede entre as camadas de aplicação e base de dados, tanto as camadas de aplicação como de base de dados devem estar na mesma rede virtual.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group"></a>Conjunto de disponibilidade de camada de aplicativo existente, zonas de disponibilidade e grupo de colocação de proximidade
O atual modelo de implantação é feito para satisfazer determinados objetivos de nível de serviço.  Neste movimento, certifique-se de que a infra-estrutura-alvo irá cumprir ou exceder os objetivos definidos.  
Mais provável do que não, os servidores de aplicações SAP dos clientes são colocados num conjunto de disponibilidade.  Se o nível atual de serviço de implantação for satisfatório e 
- Se a substituição do servidor SAP HANA for feita através da resolução de nomes, trocando os IPs pelo nome de anfitrião DB, nada mais precisa de ser feito.  
- Se não estiver a utilizar o PPG, certifique-se de colocar todos os servidores de aplicação e DB na mesma zona para minimizar a latência da rede.
- Se estiver a utilizar o PPG, consulte a secção deste documento Planeamento de Destino, Conjunto de Disponibilidade, Zonas de Disponibilidade e Grupo de Colocação de Proximidade (PPG)

### <a name="storage-replication-discontinuance-process-if-used"></a>Processo de descontinuação da replicação de armazenamento (se utilizado)
Se a replicação do armazenamento estiver a ser utilizada como solução DR, a replicação terá de ser terminada (não programada) depois de a aplicação SAP ter sido desligada e o último catálogo SAP HANA, ficheiro de registo, cópias de dados foram replicadas nos volumes de armazenamento remoto.  Esta ação é uma tática de precaução que temos uma base de dados atual sobre o DR HLI no caso de um desastre acontecer durante a transição física para a VM Azure.

### <a name="data-backups-preservation-consideration"></a>Consideração de preservação de backups de dados
Após o corte para SAP HANA no Azure VM, todos os dados ou cópias de segurança baseadas em instantâneos no HLI NÃO são facilmente acessíveis ou restáveis a um VM, se necessário. Durante o período de transição precoce, antes de o backup baseado em Azure construir um histórico suficiente para satisfazer os requisitos de recuperação do Ponto no Tempo, recomendamos a obtenção de cópias de segurança de nível de ficheiro, além de instantâneos no HLI, dias/semanas antes do corte.  Tenha estas cópias de segurança para uma conta de Armazenamento Azure acessível pelo novo SAP HANA VM. Além de apoiar o conteúdo do HLI, é também uma prática prudente ter todos os backups da paisagem SAP facilmente acessíveis no caso de ser necessário um retrocesso.

### <a name="adjusting-system-monitoring"></a>Monitorização do sistema de ajuste 
Os clientes usam muitas ferramentas diferentes para monitorizar e enviar notificações de alerta para sistemas dentro da sua paisagem SAP.  Este item é apenas uma simples chamada para uma ação adequada ao adicionar os novos VM(s) incorporando qualquer ajustamento para monitorização e atualização dos destinatários da notificação de alerta, se necessário.

### <a name="microsoft-operations-team-involvement"></a>Envolvimento da equipa de operações da Microsoft 
Abra um bilhete a partir das bases do portal Azure na instância HLI existente.  Após a criação do bilhete de apoio, um engenheiro de suporte entrará em contacto consigo por e-mail.  

### <a name="engage-microsoft-account-team"></a>Envolver a equipa da Conta Microsoft
Planeie a migração perto do tempo de renovação do contrato de iHL para minimizar desnecessária sanções em detrimento das despesas com recurso a cálculo. Para desmantelar a lâmina hli, é necessário coordenar a rescisão do contrato e o encerramento real da unidade.

## <a name="destination-planning"></a>Planeamento de destino
Erguer uma nova infraestrutura para tomar o lugar de uma existente merece alguma reflexão para garantir que a nova adição se encaixe no grande esquema das coisas.  Abaixo estão alguns pontos-chave para contemplação.

### <a name="resource-availability-in-the-target-region"></a>Disponibilidade de recursos na região Alvo 
A atual região de implementação do servidor de aplicações SAP está tipicamente próxima dos HLIs associados.  No entanto, os HLIs são oferecidos em menos locais do que as regiões azure disponíveis.  Ao migrar do HLI físico para o Azure VM, apresenta a oportunidade de "afinar" a distância de proximidade de todos os serviços relacionados para otimização de desempenho.  Ao fazê-lo, uma consideração fundamental seria assegurar que a região escolhida dispor de recursos de interesse.  Por exemplo, a disponibilidade de uma certa família VM ou a oferta de Zonas Azure para elevada consideração de disponibilidade.

### <a name="virtual-network"></a>Rede virtual 
O arquiteto de infraestruturas será confrontado com uma escolha de executar a nova base de dados HANA dentro da rede virtual de aplicação SAP existente ou para criar uma nova.  O principal fator decisivo é o atual layout de networking para a paisagem SAP.  No caso de uma mudança de infraestrutura de implantação, por exemplo, passar de uma zona para implantação de duas zonas e tirar partido do PPG. Esta melhoria da disponibilidade impõe mudanças arquitetónicas. Para mais informações, consulte o artigo [Azure PPG para obter uma latência ótima da rede com aplicação SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios).   

### <a name="security"></a>Segurança
Quer o novo SAP HANA VM aterre numa nova ou existente vnet/subnet, representa um novo serviço crítico de negócios que requer salvaguarda.  O controlo adequado do acesso, em conformidade com a política de segurança da empresa para esta nova classe de serviço, deve ser avaliado e implementado. 

### <a name="vm-sizing-recommendation"></a>Recomendação de dimensionamento VM
Esta migração é também uma oportunidade para o tamanho certo do seu motor de computação HANA.  Pode-se aproveitar [as vistas do sistema](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) HANA em conjunto com o HANA Studio para entender o consumo de recursos do sistema, o que permite o dimensionamento certo para impulsionar a eficiência de gastos.

### <a name="storage"></a>Armazenamento 
O desempenho do armazenamento é um dos fatores que impactam a experiência do utilizador da aplicação SAP.  Base num dado VM SKU, existem recomendações mínimas de layout de armazenamento [publicadas configurações de armazenamento virtual SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage). Recomendamos rever estas especificações mínimas e comparar com as estatísticas existentes do sistema HLI para garantir uma capacidade e desempenho adequados para o novo VM HANA.

Se configurar o PPG para o novo VM HANA e as suas sedes associadas, submeta um bilhete de apoio para inspecionar e garantir a collocalização do armazenamento e do VM HANA.  
Uma vez que a sua solução de backup poderá ter de ser alterada, o custo de armazenamento também deve ser revisitado para evitar surpresas de gastos operacionais. 

### <a name="storage-replication-for-disaster-recovery"></a>Replicação de armazenamento para recuperação de desastres
No HLI, a replicação do armazenamento foi oferecida como a opção padrão para a replicação de recuperação de desastres para a base de dados HANA. Esta funcionalidade não é a opção predefinida com o Azure VM. Considere hSR, backup/restauro ou outras soluções suportadas que satisfaça as suas necessidades de negócio.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>Conjuntos de disponibilidade, zonas de disponibilidade e grupos de colocação de proximidade 
Tendo em conta a minimização da distância entre a camada de aplicação e o SAP HANA para manter a latência da rede no mínimo, a nova base de dados VM e os atuais servidores de aplicações SAP devem ser colocados num Grupo de Colocação de Proximidade (PPG). Consulte o Grupo de [Colocação](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) de Proximidade para saber como as Zonas de Disponibilidade e Disponibilidade do Azure funcionam com o PPG para implementações sap.
Se os membros do sistema HANA alvo forem implantados em mais de uma Zona Azure, os clientes devem ter uma visão clara do perfil de latência das zonas escolhidas. A colocação de componentes do sistema SAP é ótima no que diz respeito à distância proximal entre a aplicação SAP e a base de dados.  A ferramenta de teste de [latência](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) da zona de disponibilidade do domínio público ajuda a facilitar a medição.  


### <a name="backup-strategy"></a>Estratégia de backup
Muitos clientes já estão a usar soluções de backup de terceiros para o SAP HANA no HLI.  Nesse caso, apenas uma base de dados adicional de VM e HANA protegida supor configurar.  Os trabalhos de apoio ao HLI em curso para o agora não estão agendados se a máquina estiver a ser desativada após a migração.
O backup azure para SAP HANA em VM está agora geralmente disponível.  Consulte estes links para obter informações detalhadas sobre: [Backup](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database), [Restaurar](https://docs.microsoft.com/azure/backup/sap-hana-db-restore), [Gerir](https://docs.microsoft.com/azure/backup/sap-hana-db-manage) cópias de segurança SAP HANA em VMs Azure.

### <a name="dr-strategy"></a>Estratégia DR
Se os seus objetivos de nível de serviço acomodarem um tempo de recuperação mais longo de um simples backup para o armazenamento de bolhas e restaurar no lugar ou para um novo VM, é a estratégia dr mais simples e menos dispendiosa.  
Como a plataforma de grandes instâncias onde hana DR tipicamente é feito com HSR; No Azure VM, o HSR é também a solução SAP HANA DR mais natural e nativa.  Independentemente de a implantação da fonte ser uma instância única, agrupada com ou sem falha automática, ou com vários nós a escala HANA, é necessária uma réplica hSR-alvo da infraestrutura de origem na região DR. Esta réplica DR alvo HSR será estabelecida após a migração primária de HLI para VM.  A instância DR HANA registar-se-á no SAP HANA primário em caso vm como um local de replicação secundária.  

### <a name="sap-application-server-connectivity-destination-change"></a>Mudança de destino de destino de conectividade do servidor de aplicações SAP
A abordagem de migração hsr resulta num novo hospedeiro HANA DB e, portanto, um novo nome de hospedeiro DB para a camada de aplicação, os perfis SAP precisam de ser modificados para refletir o novo nome de anfitrião.  Se o interruptor for feito por resolução de nome que preserve o nome de anfitrião, não é necessária qualquer alteração de perfil.

### <a name="operating-system"></a>Sistema operativo
As imagens do sistema operativo para HLI e VM, mesmo estando no mesmo nível de libertação, sles 11 SP4, por exemplo, não são idênticas. Os clientes devem validar as embalagens, correções quentes, correções quentes, patches, kernel e correções de segurança no HLI para que estas possam ser instaladas no sistema operativo da VM alvo.  Além disso, é comum os clientes configurarem uma versão so mais recente no VM alvo para SAP HSR.  Isto é suportado por [nota SAP 2763388](https://launchpad.support.sap.com/#/notes/2763388).

### <a name="new-sap-license-request"></a>Novo pedido de licença SAP
Uma simples chamada para pedir uma nova licença SAP para o novo sistema HANA agora que foi migrado para VMs.

### <a name="service-level-agreement-sla-differences"></a>Diferenças no contrato de nível de serviço (SLA)
Os autores gostam de chamar a atenção para a diferença de disponibilidade sla entre HLI e Azure VM.  Por exemplo, os pares HLIs HA agrupados oferecem 99,99% de disponibilidade. Para conseguir o mesmo SLA, é necessário implantar VMs em zonas de disponibilidade. Este [artigo](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) descreve a disponibilidade com arquiteturas de implantação associadas para que os clientes possam planear essa infraestrutura de acordo com essa infraestrutura.


## <a name="migration-strategy"></a>Estratégia de migração
Neste documento, cobrimos apenas a abordagem de replicação do sistema HANA para a migração do HLI para o Azure VM.  Depende da solução de armazenamento-alvo implementada, o processo difere ligeiramente. Os passos de alto nível são descritos abaixo.

### <a name="vm-with-premiumultra-disks-for-data"></a>VM com premium/ultra-discos para dados
Para VMs que são implantados com premium ou ultra-discos, a configuração padrão de replicação do sistema SAP HANA aplica-se e pode ser usada para configurar HSR.  O artigo de [ajuda SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html) referenciado fornece uma visão geral dos passos envolvidos na criação de replicação do sistema entre dois sistemas, assumindo um sistema secundário, falhando num sistema primário e desativando a replicação do sistema.  Para efeitos da migração, só precisaremos da configuração, tomada e replicação do sistema desativação nas etapas de fonte do HLI.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>VM com ANF para dados e volumes de registo
A um nível elevado, os mais recentes instantâneos de armazenamento de HLI dos dados completos e volumes de registo devem ser copiados para o Armazenamento Azure, onde são acessíveis e recuperáveis pelo alvo HANA VM.  O processo de cópia pode ser feito com quaisquer ferramentas de cópia linux nativas.  

>[!Important]
> A transferência de cópias e dados pode demorar horas depende do tamanho da base de dados HANA e da largura de banda da rede.  A maior parte do processo de cópia deve ser feita antes do tempo de inatividade primário do HANA DB.

### <a name="mcos-to-mdc-conversion"></a>MCOS para Conversão MDC
O modelo de implementação de Múltiplos Componentes no One System (MCOS) foi escolhido por alguns dos nossos clientes HLI.  A motivação foi contornar a limitação instantânea do armazenamento de várias bases de dados (MDC) das versões anteriores do SAP HANA.  No modelo MCOS, várias instâncias independentes do SAP HANA são empilhadas numa lâmina HLI.  A utilização de HSR para a migração funcionaria bem, resultando em múltiplos VMs HANA com um inquilino DB cada.  Este estado final torna uma paisagem mais movimentada do que o que um cliente poderia estar habituado.  Com a implantação padrão SAP HANA 2.0 sendo MDC, uma alternativa viável é executar movimento de [inquilino HANA](https://launchpad.support.sap.com/#/notes/2472478) após a migração do HSR.  Este processo "consolida" estas bases de dados independentes da HANA em coinquilinos num único contentor HANA.  

### <a name="application-layer-consideration"></a>Consideração da camada de aplicação
O servidor DB é visto como o centro de um sistema SAP.  Todos os servidores de aplicações devem estar localizados perto do SAP HANA DB.  Em alguns casos, quando é desejada uma nova utilização do PPG, pode ser necessária a deslocalização dos servidores de aplicações existentes para o PPG onde o VM HANA é necessário.  A construção de novos servidores de aplicações pode ser considerada mais fácil se já tiver modelos de implementação à mão.  
Se os servidores de aplicações existentes e o novo VM HANA estiverem na melhor posição, não é necessário construir novos servidores de aplicações a menos que seja desejada capacidade adicional.  
Se for construída uma nova infraestrutura para melhorar a disponibilidade do serviço, os servidores de aplicações existentes podem tornar-se desnecessários e devem ser desligados e eliminados.
Se o nome de anfitrião do VM alvo alterado e diferir do nome de anfitrião HLI, os perfis do servidor de aplicação SAP precisam de ser ajustados para apontar para o novo anfitrião.  Se apenas o endereço IP HANA DB tiver mudado, é necessária uma atualização de registo DNS para liderar as ligações de entrada para o novo HanA VM.

### <a name="acceptance-test"></a>Teste de aceitação
Embora a migração do HLI para vM não faça alterações materiais para o conteúdo da base de dados em comparação com uma migração heterogénea, recomendamos ainda validar as principais funcionalidades e aspeto de desempenho da nova configuração.  

### <a name="cutover-plan"></a>Plano cutover
Embora esta migração seja direta, no entanto envolve o desmantelamento de um DB existente.  Um planeamento cuidadoso para preservar o sistema de origem com o seu conteúdo associado e as imagens de backup são fundamentais no caso de ser necessário um recuo.  Um bom planeamento oferece uma inversão mais rápida.   


## <a name="post-migration"></a>Pós-migração
O trabalho de migração não é feito até termos dissociado com segurança quaisquer serviços dependentes do HLI ou conectividade para garantir a preservação da integridade dos dados.  Além disso, encerrar serviços desnecessários.  Esta secção chama alguns itens topo de mente.

### <a name="decommissioning-the-hli"></a>Desmantelamento do HLI
Após uma migração bem sucedida do HANA DB para a VM Azure, nenhuma transação de negócios de produção deve ser realizada no HLI DB.  No entanto, manter o HLI a funcionar durante um período igual à janela local de retenção de dados de backup é uma prática segura que garanta a recuperação mais rápida dos dados, se necessário.  Só então a lâmina HLI deve ser desativada.  Para além da empresa técnica, os clientes no seu melhor interesse devem concluir contratualmente os compromissos de HLI com a Microsoft.  Os clientes devem contactar os seus representantes da Microsoft para trabalhar em processo de desmantelamento do HLI.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>Remova qualquer procuração (ex: Iptables, BIGIP) configurada para HLI 
Se um serviço de procuração como o de um IPTables for utilizado para direcionar o tráfego no local de e para o HLI, esse serviço já não é necessário após a migração bem sucedida para vm.  No entanto, este serviço de conectividade deve ser mantido enquanto a lâmina HLI ainda estiver de pé, como discutido no tópico de desmantelamento.  Este serviço pode ser desligado depois de a lâmina HLI estar totalmente desativada. 

### <a name="remove-global-reach-for-hli"></a>Remover o Alcance Global para o HLI 
O Global Reach é normalmente utilizado para ligar o gateway ExpressRoute do cliente com o gateway HLI ExpressRoute permitindo que o tráfego no local do cliente chegue diretamente ao inquilino hli sem a necessidade de um serviço de procuração.  Tal como acontece com o serviço de procuração IPTables, o GlobalReach deve ser mantido até que a lâmina HLI esteja totalmente desativada.

### <a name="operating-system-subscription--movereuse"></a>Subscrição do sistema operativo - movimento/reutilização
À medida que os servidores VM são suportados e as lâminas HLI são desativadas, as subscrições de So podem ser substituídas ou reutilizadas para evitar o dobro do pagamento das licenças de SO.



## <a name="next-steps"></a>Passos seguintes
Consulte estes artigos:
- [Configurações e operações de infraestruturas SAP HANA no Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Cargas de trabalho SAP em Azure: lista de verificação de planeamento e implementação](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist).
