---
title: O que é o Backup do Azure?
description: Fornece uma descrição geral do serviço de cópia de segurança do Azure e como implementá-la como parte da sua estratégia de recuperação (BCDR) de desastre e continuidade empresariais.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 02/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: b590d75abb9e3f6465594a5c8c684b94e6f2e07f
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56864307"
---
# <a name="what-is-azure-backup"></a>O que é o Backup do Azure?

O serviço de cópia de segurança do Azure faz o backup de dados para a cloud do Microsoft Azure. Pode fazer backup de máquinas no local e cargas de trabalho e máquinas virtuais do Azure (VMs).


## <a name="why-use-azure-backup"></a>Porquê utilizar o Backup do Azure?

O Azure Backup fornece estas principais vantagens:

- **A descarga de cópia de segurança no local**: O Azure Backup oferece uma solução simple para cópia de segurança de seus recursos no local para a cloud. Obtenha cópia de segurança de curta e longo prazo, sem a necessidade de implementar complexas soluções no local cópia de segurança. 
- **Cópia de segurança de VMs IaaS do Azure**: O Azure Backup fornece cópias de segurança de forma independentes e isoladas para proteger contra destruição acidental de dados originais. As cópias de segurança são armazenadas num cofre dos serviços de recuperação com interna gerenciada de pontos de recuperação. Configuração e a escalabilidade é simples, as cópias de segurança são otimizadas e pode facilmente restaurar conforme necessário.
- **Dimensione facilmente** -cópia de segurança do Azure utiliza o poder subjacente e o dimensionamento ilimitado da nuvem do Azure para fornecer elevada disponibilidade com nenhuma manutenção ou monitorização sobrecarga. 
- **Obter a transferência de dados ilimitada** - cópia de segurança do Azure não limita a quantidade de entrada ou dados de saída transfira ou cobra por dados que são transferidos.
    - Os dados de saída são os dados transferidos a partir de um cofre dos Serviços de Recuperação durante uma operação de restauro.
    - Se efetuar uma cópia de segurança inicial offline usando o serviço importar/exportar do Azure para importar grandes quantidades de dados, há um custo associado a dados de entrada.  [Saiba mais](backup-azure-backup-import-export.md). 
- **Manter os dados seguros**: Encriptação de dados permite a transmissão segura e o armazenamento dos seus dados na cloud pública. A frase de acesso de encriptação é armazenada por si localmente, e nunca é transmitida ou armazenada no Azure. Se for necessário restaurar quaisquer dados, é o único a ter a frase de acesso ou a chave de encriptação.
- **Obter cópias de segurança consistente com a aplicação**: Uma cópia de segurança consistentes com aplicações significa que um ponto de recuperação tem todos os dados necessários para restaurar a cópia de segurança. O Azure Backup fornece cópias de segurança consistentes com as aplicações, o que garante que não são necessárias correções adicionais para restaurar os dados. Restaurar dados consistentes com as aplicações reduz o tempo de restauro, permitindo-lhe voltar rapidamente a um estado de execução.
- **Manter os dados de curtos e longo prazo**: Pode utilizar cofres dos serviços de recuperação para a retenção de dados de curto e longo prazo. O Azure não limita o período de tempo durante o qual os dados podem permanecer num cofre dos Serviços de Recuperação. Puderem mantê-lo para o tempo que pretender. O Azure Backup tem um limite de 9999 pontos de recuperação por instância protegida. [Saiba mais](backup-introduction-to-azure-backup.md#backup-and-retention)sobre como este limite afeta a suas necessidades de cópia de segurança.
- **Gestão de armazenamento automática** - os ambientes híbridos necessitam frequentemente de armazenamento heterogéneo - alguns no local e outros na nuvem. Com o Azure Backup, não existe nenhum custo para a utilização de dispositivos de armazenamento no local. O Azure Backup atribui automaticamente e gere o armazenamento de cópia de segurança e utiliza um modelo de pay-as que use, para que só paga o armazenamento que consumir. [Saiba mais](https://azure.microsoft.com/pricing/details/backup) sobre os preços.
- **Várias opções de armazenamento** -cópia de segurança do Azure oferece dois tipos de replicação para manter os seus dados/armazenamento altamente disponíveis.
    - [Armazenamento localmente redundante (LRS)](../storage/common/storage-redundancy-lrs.md) replica os seus dados três vezes (cria três cópias dos seus dados) numa unidade de escala de armazenamento num Data Center. Todas as cópias dos dados existem na mesma região. O LRS é uma opção de baixo custo para proteger os dados contra falhas de hardware locais.
    - [Armazenamento georredundante (GRS)](../storage/common/storage-redundancy-grs.md) é o padrão e a opção de replicação recomendada. O GRS replica os dados para uma região secundária (a centenas de quilómetros da localização primária da origem de dados). O GRS custa mais do que o LRS, mas o GRS proporciona um nível mais elevado de durabilidade aos seus dados, mesmo se ocorrer uma indisponibilidade regional.


## <a name="whats-the-difference-between-azure-backup-and-azure-site-recovery"></a>O que é a diferença entre a cópia de segurança do Azure e o Azure Site Recovery?

Os serviços da cópia de segurança do Azure e o Azure Site Recovery contribuem para uma estratégia de recuperação (BCDR) de desastres e continuidade do negócio da sua empresa. BCDR consiste em dois objetivos gerais:

- Mantenha os dados empresariais seguros e recuperáveis quando ocorrem falhas.
- Mantenha as suas aplicações e cargas de trabalho em funcionamento durante os tempos de inatividade planejados quanto não planejados.

Ambos os serviços fornecem funcionalidades diferentes mas complementares.

- **Azure Site Recovery**: Recuperação de sites fornece uma solução de recuperação após desastre para máquinas no local e para as VMs do Azure. Replicar máquinas a partir de uma localização primária para uma secundária. Momentos de desastre, pós-falha de máquinas para a localização secundária e aceder aos mesmos a partir daí. Quando tudo estiver em execução normalmente novamente, falhar máquinas back para recuperá-los no site primário.
- **O Azure Backup**: O serviço de cópia de segurança do Azure faz uma cópia dos dados a partir de máquinas no local e VMs do Azure. Dados podem ser uma cópia de segurança e recuperar num nível granular, incluindo a cópia de segurança de ficheiros, pastas, estado do sistema de máquina e cópia de segurança de dados com suporte para a aplicação. Cópia de segurança do Azure processa dados num nível mais granular do que o Site Recovery. Por exemplo, se uma apresentação no computador portátil se danificasse, pode utilizar o Azure Backup para restaurar a apresentação. Se quiser manter uma configuração de VM e os dados seguros e acessível, poderia usar o Site Recovery.  

Utilize os pontos de tabela para ajudar a descobrir as suas necessidades BCDR. 

**Objetivo** | **Detalhes** | **Comparação**
--- | --- | --- | --- |
**Cópia de segurança/retenção de dados** | Dados de cópia de segurança podem ser mantidos e armazenados durante dias, meses ou até anos a fio se for necessário numa perspetiva de conformidade. | Soluções de cópia de segurança, como a cópia de segurança do Azure permitem-lhe escolher os dados que pretende criar cópias de segurança e fazer um ajuste as políticas de cópia de segurança e retenção.<br/><br/> Recuperação de sites não permite a mesma ajuste fino.
**Objetivo de ponto de recuperação (RPO)** | A quantidade de perda de dados aceitável se uma recuperação tiver de ser efetuada. | As cópias de segurança têm mais variável RPO.<br/><br/> Cópias de segurança VM normalmente têm um RPO de um dia, enquanto as cópias de segurança da base de dados têm RPOs tão baixos como 15 minutos.<br/><br/> Recuperação de sites fornece um RPO baixo, uma vez que a replicação é contínua ou com frequência, para que o delta entre a cópia de origem e de réplica é pequeno.
**Objetivo de tempo de recuperação (RTO)** |A quantidade de tempo que demora a concluir uma recuperação ou um restauro. | Devido ao RPO maior, a quantidade de dados que uma solução de cópia de segurança necessita para processar é, normalmente, muito mais elevada, o que resulta em RTOs maiores. Por exemplo, pode demorar dias para restaurar dados de bandas, consoante o tempo que demora a transportar a banda a partir de uma localização fora das instalações. | Soluções de recuperação após desastre, como a recuperação de Site têm um RPO baixo, uma vez que a replicação contínua/frequentes, geralmente, significa que o destino é mais elevada sincronizado com a origem. |

## <a name="what-backup-scenarios-are-supported"></a>Quais cenários de cópia de segurança são suportados?

Cópia de segurança do Azure pode fazer uma cópia de segurança de máquinas no local e VMs do Azure.

**Machine** | **Criar cópias de segurança cenário**
--- | ---
**Cópia de segurança no local** |  1) execute o agente de cópia de segurança Microsoft Azure Recovery dos serviços Azure (MARS) no local máquinas do Windows para fazer backup de arquivos individuais e o estado do sistema. <br/><br/>2) cópia de segurança de máquinas no local para um servidor de cópia de segurança (System Center Data Protection Manager (DPM) ou Microsoft Azure Backup Server (MABS)) e, em seguida, configure o servidor de backup para cópia de segurança para um cofre dos serviços de recuperação de cópia de segurança do Azure no Azure.
**VMs do Azure** | 1) ative cópia de segurança para VMs do Azure individuais. Quando ativa a cópia de segurança, o Azure Backup instala uma extensão para o agente de VM do Azure que está em execução na VM. O agente faz o backup de toda a VM.<br/><br/> 2) execute o agente de MARS numa VM do Azure. Isto é útil se pretender efetuar cópias de segurança de ficheiros e pastas individuais na VM.<br/><br/> 3) cópia de segurança de uma VM do Azure para um servidor DPM ou MABS em execução no Azure. Em seguida, criar cópias de segurança do servidor DPM/MABS para um cofre de cópia de segurança do Azure a utilizar. 


## <a name="why-use-a-backup-server"></a>Por que usar um servidor de cópia de segurança?




As vantagens da cópia de segurança de máquinas e aplicações para o armazenamento do MABS/DPM e, em seguida, cópia de segurança de armazenamento do DPM/MABS para um cofre são os seguintes:

- O backup em MABS/DPM fornece cópias de segurança com suporte para a aplicação otimizadas para aplicações comuns, como o SQL Server, Exchange e SharePoint, no adicionais para cópias de segurança do ficheiro/pasta/volume e backups de estado do computador (bare-metal, o estado do sistema).
- Para máquinas no local, não terá de instalar o agente de MARS em cada máquina que pretende criar cópias de segurança. Cada máquinas executa o agente de proteção do DPM/MABS e o agente de MARS é executado no MABS/DPM apenas.
- Tem mais flexibilidade e opções de agendamento granulares para a execução de cópias de segurança.
- Pode gerir as cópias de segurança para várias máquinas que agrupa em grupos de proteção numa única consola. Isto é particularmente útil quando as aplicações são dispostos em camadas através de várias máquinas e pretende que o backup deles em conjunto.

Saiba mais sobre [funciona como cópia de segurança](backup-architecture.md#architecture-back-up-to-dpmmabs) ao utilizar um servidor de cópia de segurança e o [suportar requisitos](backup-support-matrix-mabs-dpm.md) para servidores de cópia de segurança.

## <a name="what-can-i-back-up"></a>O que posso criar cópias de segurança?

**Machine** | **Método de cópia de segurança** | **Fazer cópia de segurança**
--- | --- | ---
**VMs do Windows no local** | Execute o agente MARS | Cópia de segurança de ficheiros, pastas, estado do sistema.<br/><br/> Máquinas do Linux não suportadas.
**Máquinas no local** | Cópia de segurança para DPM/MABS | Cópia de segurança tudo o que é protegido pelo [DPM](backup-support-matrix-mabs-dpm.md#supported-backups-to-dpm) ou [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs), incluindo ficheiros/pastas/partilhas/volumes e dados específicos da aplicação. 
**VMs do Azure** | Extensão execução de cópia de segurança do agente VM do Azure | Criar cópias de segurança VM inteira
**VMs do Azure** | Execute o agente MARS | Cópia de segurança de ficheiros, pastas, estado do sistema.<br/><br/> Máquinas do Linux não suportadas.
**VMs do Azure** | Criar cópias de segurança para o MABS/DPM em execução no Azure | Cópia de segurança tudo o que é protegido pelo [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs) ou [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807) incluindo dados de ficheiros/pastas/partilhas/volumes e específicos da aplicação.

## <a name="what-backup-agents-do-i-need"></a>Os agentes de cópia de segurança é necessário?

**Cenário** | **Agente** 
--- | --- 
**Fazer cópia de segurança de VMs do Azure** | Nenhum agente necessário. Extensão VM do Azure para cópia de segurança está instalado na VM do Azure ao executar a primeira cópia de segurança de VM do Azure.<br/><br/> Suporte para o suporte do Windows e Linux.
**Cópia de segurança de máquinas do Windows no local** | Transferir, instalar e executar o agente de MARS diretamente na máquina. 
**VMs de cópia de segurança do Azure com o agente MARS** | Transferir, instalar e executar o agente de MARS diretamente na máquina. O agente de MARS pode ser executado em conjunto com a extensão de cópia de segurança.
**Cópia de segurança de máquinas no local e VMs do Azure para o DPM/MABS** | O agente de proteção do DPM ou MABS é executado nas máquinas que pretende proteger. O agente de MARS é executado no servidor do DPM/MABS para criar cópias de segurança para o Azure.

## <a name="which-backup-agent-should-i-use"></a>O agente de cópia de segurança devo utilizar?

**Cópia de segurança** | **Solução** | **Limitação**
--- | --- | ---
**Eu quero fazer cópias de segurança de uma VM inteira do Azure** | Ative cópia de segurança para a VM. A extensão de cópia de segurança serão automaticamente configurada no Windows ou VM do Linux do Azure. | VM inteira é uma cópia de segurança <br/><br/> Para VMs do Windows, a cópia de segurança é consistente com a aplicação. para Linux, a cópia de segurança é consistente com ficheiros. Se precisar com suporte para a aplicação para VMs do Linux tem de configurar esta com scripts personalizados.
**Eu quero fazer cópias de segurança de ficheiros/pastas específicas numa VM do Azure** | Implemente o agente de MARS na VM.
**Desejo fazer diretamente a máquinas do Windows no local** | Instale o agente de MARS na máquina. | Pode fazer backup de arquivos, pastas e estado do sistema para o Azure. As cópias de segurança não têm consciência de aplicação.
**Quero diretamente fazer cópias de segurança de máquinas do Linux no local** | Precisa implantar o DPM ou MABS para criar cópias de segurança para o Azure.
**Eu quero fazer cópias de segurança de aplicações em execução no local** | Para cópias de segurança de aplicação com suporte para máquinas têm de ser protegidas pelo DPM ou MABS.
**Quero granulares e flexíveis cópia de segurança e recuperação definições para as VMs do Azure** | Protege VMs do Azure com o MABS/DPM em execução no Azure para flexibilidade adicional de agendamento de cópia de segurança e total flexibilidade para proteger e restaurar ficheiros, pastas, volumes, aplicações e estado do sistema.


## <a name="next-steps"></a>Passos Seguintes

- [Revisão](backup-architecture.md) a arquitetura e componentes para diferentes cenários de cópia de segurança.
- [Certifique-se de](backup-support-matrix.md) suportado recursos e configurações para cópia de segurança.

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

