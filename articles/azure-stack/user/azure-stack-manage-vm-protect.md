---
title: Proteger VMs implementadas no Azure Stack | Documentos da Microsoft
description: Diretrizes sobre como proteger máquinas virtuais implementadas no Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 3/19/2018
ms.openlocfilehash: de57ebbf0c0c4e8a18c955200d1692ea4ae9afae
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295016"
---
# <a name="protect-virtual-machines-deployed-on-azure-stack"></a>Proteger máquinas virtuais implementadas no Azure Stack

Utilize este artigo como guia para desenvolver um plano para proteger máquinas virtuais (VMs) que os utilizadores a implementar no Azure Stack.

Para proteger contra a perda de dados e o período de indisponibilidade não planeado, terá de implementar um plano de recuperação de cópia de segurança ou recuperação após desastre para aplicações de utilizador e os seus dados. Este plano pode ser exclusivo para cada aplicativo, mas segue uma estrutura estabelecida pelo estratégia de recuperação (BC/DR) de desastres e continuidade de negócio abrangente da sua organização. Um bom ponto de partida é [Azure Stack: Considerações para a recuperação de desastre e continuidade de negócio](https://aka.ms/azurestackbcdrconsiderationswp).

## <a name="azure-stack-infrastructure-recovery"></a>Recuperação de infraestrutura do Azure Stack

Os utilizadores são responsáveis por proteger as respetivas VMs separadamente dos serviços de infraestrutura do Azure Stack.

O plano de recuperação para serviços de infraestrutura do Azure Stack **não** incluem a recuperação de VMs do utilizador, contas de armazenamento ou bases de dados. Como o proprietário da aplicação, são responsáveis por implementar um plano de recuperação para seus aplicativos e dados.

Se a cloud do Azure Stack estiver offline durante um período de tempo alargado ou permanentemente irrecuperável, tem de ter um plano de recuperação coloca isso:

* Garante que o tempo de inatividade mínimo
* Mantém VMs críticas, tais como servidores de base de dados, em execução
* Permite que os aplicativos manter a atendendo às solicitações de utilizador

O operador da cloud do Azure Stack é responsável por criar um plano de recuperação para a infraestrutura do Azure Stack subjacente e serviços. Para saber mais, leia o artigo [recuperar da perda catastrófica de dados](https://docs.microsoft.com/azure/azure-stack/azure-stack-backup-recover-data).

## <a name="considerations-for-iaas-vms"></a>Considerações para IaaS VMs
O sistema operativo instalado na IaaS VM irá limitar quais produtos pode usar para proteger os dados nele contidos. Para Windows com a base de VMs de IaaS, pode utilizar os produtos Microsoft e de parceiros para proteger os dados. Para IaaS VMs baseados em Linux, a única opção é usar produtos de parceiros. Consulte a [esta folha de dados para todos os parceiros de BC/DR com os produtos validados para o Azure Stack](https://aka.ms/azurestackbcdrpartners).

## <a name="sourcetarget-combinations"></a>Combinações de origem/destino

Cada cloud do Azure Stack é implementada para um datacenter. Um ambiente separado é necessário para que possa recuperar seus aplicativos. O ambiente de recuperação pode ser outra cloud do Azure Stack noutro Datacenter ou cloud pública do Azure. Os requisitos de privacidade de dados e soberania de dados irão determinar o ambiente de recuperação para a sua aplicação. Como ativar a proteção para cada aplicativo, tem a flexibilidade para escolher uma opção de recuperação específica para cada um deles. Pode ter aplicações na subscrição de uma cópia de segurança para outro datacenter. Noutra subscrição, pode replicar dados para a cloud pública do Azure.

Planeie a estratégia de recuperação de cópia de segurança e recuperação após desastre para cada aplicação para determinar o destino para cada aplicação. Um plano de recuperação ajudará sua organização corretamente redimensionar o armazenamento capacidade necessária no local e o consumo na cloud pública do projeto.

|  | Global Azure | O Azure Stack implementado no datacenter CSP e operados pelo CSP | O Azure Stack implementados no Centro de dados do cliente e em funcionamento por parte do cliente |
|------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| **O Azure Stack implementado no datacenter CSP e operados pelo CSP** | VMs do utilizador são implementadas na pilha do Azure CSP operado.<br><br>VMs do utilizador são restauradas a partir de cópia de segurança ou a ativação pós-falha diretamente para o Azure. | CSP opera as instâncias principais e secundárias do Azure Stack em seus próprios datacenters.<br><br>VMs do utilizador são restauradas ou efetuar a ativação pós-falha entre as duas instâncias do Azure Stack. | CSP opera o Azure Stack no site primário.<br><br>Centro de dados do cliente é o destino de restauro ou de ativação pós-falha. |
| **O Azure Stack implementados no Centro de dados do cliente e em funcionamento por parte do cliente** | VMs do utilizador são implementadas para o cliente operado Azure Stack.<br><br>VMs do utilizador são restauradas a partir de cópia de segurança ou a ativação pós-falha diretamente para o Azure. | Cliente opera o Azure Stack no site primário.<br><br>Datacenter do CSP é o destino de restauro ou de ativação pós-falha. | Cliente opera as instâncias principais e secundárias do Azure Stack em seus próprios datacenters.<br><br>VMs do utilizador são restauradas ou efetuar a ativação pós-falha entre as duas instâncias do Azure Stack. |

![Combinações de origem-destino](media/azure-stack-manage-vm-backup/vm_backupdataflow_01.png)

## <a name="application-recovery-objectives"></a>Objetivos de recuperação de aplicações

Terá de determinar a quantidade de tempo de inatividade e perda de dados que pode tolerar a sua organização para cada aplicação. Quantificando o tempo de inatividade e perda de dados pode criar um plano de recuperação que minimizam o impacto de um desastre em sua organização. Para cada aplicativo, considere:

 - **Objetivo de tempo de recuperação (RTO)**  
RTO é o tempo máximo aceitável que um aplicativo pode ficar indisponível após um incidente. Por exemplo, um RTO de 90 minutos significa que tem de ser capaz de restaurar a aplicação para um Estado de execução dentro de 90 minutos desde o início de um desastre. Se tiver um RTO baixo, poderá manter uma segunda implementação continuamente em execução no modo de espera para proteger contra uma falha regional.
 - **Objetivo de ponto de recuperação (RPO)**  
RPO é a duração máxima de perda de dados que é aceitável durante um desastre. Por exemplo, se armazenar os dados numa única base de dados, sem qualquer replicação para outras bases de dados, e fizer cópias de segurança de hora a hora, poderá perder uma hora de dados.

O RTO e RPO são requisitos comerciais. Conduzir uma avaliação de risco para definir o RTO e RPO da aplicação.

Outra métrica é **tempo médio de recuperar** (MTTR), que é o tempo médio necessário para restaurar a aplicação após uma falha. MTTR é um valor empírico para um sistema. Se o MTTR exceder o RTO, então uma falha no sistema irá causar uma interrupção inaceitável do negócio, porque não será possível restaurar o sistema no RTO definido.

### <a name="backup-restore"></a>Restauro da cópia de segurança

O esquema de proteção mais comuns para aplicações baseadas em VM consiste em utilizar o software de backup. Cópia de segurança de uma VM normalmente inclui o sistema operativo, configuração do sistema operativo, binários de aplicativos e dados da aplicação. As cópias de segurança são criadas por um instantâneo de volumes, discos ou toda a VM. Com o Azure Stack, tem a flexibilidade de criar cópias de segurança de dentro do contexto de SO convidado ou do armazenamento do Azure Stack e as APIs de computação. O Azure Stack não suporta cópias de segurança e tirar ao nível do hipervisor.
 
![Restor de cópia de segurança](media/azure-stack-manage-vm-backup/vm_backupdataflow_03.png)

Recuperar a aplicação requer o restauro de uma ou mais VMs para a nuvem mesmo ou para uma cloud nova. Pode direcionar uma cloud no seu datacenter ou na cloud pública. A cloud que escolher é completamente dentro de seu controle e baseia-se nos seus requisitos de privacidade e soberania de dados.
 
 - RTO: Tempo de inatividade medido em horas
 - RPO: Perda de dados da variável (dependendo da frequência de cópia de segurança)
 - Topologia de implementação: Ativo/passivo

#### <a name="planning-your-backup-strategy"></a>Planejar sua estratégia de cópia de segurança

Planejar sua estratégia de cópia de segurança e definir os requisitos de dimensionamento começa com a quantificação do número de instâncias VM que precisam ser protegidos. Cópia de segurança de todas as VMs em todos os servidores num ambiente é uma estratégia comum. No entanto, com o Azure Stack, existem algumas VMs que têm de ser efetuada a cópia de segurança. Por exemplo, as VMs num conjunto de dimensionamento são consideradas efémeros recursos que podem voltar e vá, às vezes, sem aviso prévio. Todos os dados duráveis que precisam ser protegido são armazenados num repositório separado, como um arquivo de banco de dados ou o objeto.

Considerações importantes sobre cópias de segurança de VMs no Azure Stack:

 - **Categorização**
    - Considere um modelo em que os utilizadores optar ativamente por participar na cópia de segurança VM.
    - Defina um recuperação ao nível contrato de serviço (SLA) com base na prioridade das aplicações ou o impacto nos negócios.
 - **Dimensionamento**
    - Quando um grande número de novas VMs introdução (se for necessária a cópia de segurança), considere as cópias de segurança escalonadas.
    - Avalie produtos de cópia de segurança que podem capturar e transmitir dados de cópia de segurança para minimizar o conteúdo de recursos na solução com eficiência.
    - Avalie produtos de backup que armazenam dados de cópia de segurança utilizando cópias de segurança incrementais ou diferenciais para minimizar a necessidade de cópias de segurança completas em todas as VMs no ambiente de forma eficaz.
 - **Restaurar**
    - Produtos de cópia de segurança podem restaurar discos virtuais, dados de aplicativo dentro de uma VM existente, ou o recurso de VM inteiro e discos virtuais associados. O esquema de restauro que precisa depende de como pretende restaurar a aplicação e irá afetar o seu tempo de aplicação para recuperação. Por exemplo, pode ser mais fácil de implementar novamente o SQL server a partir de um modelo e, em seguida, restaurar as bases de dados em vez de restaurar a VM inteira ou um conjunto de VMs.

### <a name="replicationmanual-failover"></a>Ativação pós-falha de replicação/manual

Uma abordagem alternativa para alta disponibilidade é replicar as VMs de aplicação para outra cloud e dependem de uma ativação pós-falha manual. A replicação do sistema operativo, binários de aplicativos e dados da aplicação pode ser executada no nível da VM ou no nível do SO convidado. A ativação pós-falha é gerida através de software adicional que não faz parte do aplicativo.

Com esta abordagem, a aplicação é implementada numa nuvem e suas VM é replicado para outra solução em nuvem. Se uma ativação pós-falha é disparada, o secundário as VMs têm de ser ativada na cloud segundo. Em alguns cenários, a ativação pós-falha cria os discos de VMs e anexa a eles. Este processo pode demorar muito tempo a concluir, especialmente com um aplicativo de várias camadas de mensagens em fila que exija uma sequência de arranque específico. Também pode ser passos que tem de ser executados antes do aplicativo está pronto para iniciar a manutenção de pedidos.

![Ativação pós-falha do manual de replicação](media/azure-stack-manage-vm-backup/vm_backupdataflow_02.png)

 - RTO: Medida de tempo de inatividade em minutos
 - RPO: Perda de dados da variável (dependendo da frequência de replicação)
 - Topologia de implementação: Espera-by ativo/passivo
 
### <a name="high-availabilityautomatic-failover"></a>Ativação pós-falha de elevada disponibilidade/automático

Para aplicativos em que sua empresa consegue tolerar a alguns segundos ou minutos de tempo de inatividade e perda mínima de dados, terá de considerar uma configuração de elevada disponibilidade. Aplicativos de alta disponibilidade são criados de forma rápida e automaticamente recuperar de falhas. Para falhas de local hardware, a infraestrutura do Azure Stack implementa elevada disponibilidade na rede física com duas principais de comutadores rack. Para falhas de nível de computação, o Azure Stack utiliza vários nós numa unidade de escala. No nível da VM, pode utilizar os conjuntos de dimensionamento em combinação com domínios de falha para garantir a falhas em nós não encerre a sua aplicação.

Em combinação com conjuntos de dimensionamento, seu aplicativo precisará suportar nativamente uma elevada disponibilidade ou suportar a utilização de clustering de software. Por exemplo, Microsoft SQL Server suporta nativamente elevada disponibilidade de bases de dados com o modo de consolidação síncrona. No entanto, se só pode suportar replicação assíncrona, em seguida, haverá alguma perda de dados. Aplicativos também podem ser implantados num cluster de ativação pós-falha em que o software de clustering manipula a ativação pós-falha automática do aplicativo.

Usando essa abordagem, o aplicativo é ativado somente numa nuvem, mas o software é implementado em várias clouds. Outras nuvens estão em reserva modo pronto para iniciar a aplicação quando for acionada a ativação pós-falha.

 - RTO: Tempo de inatividade medido em segundos
 - RPO: Perda mínima de dados
 - Topologia de implementação: Espera-by ativo/ativo

### <a name="fault-tolerance"></a>Tolerância a falhas

O Azure Stack a disponibilidade de serviço de redundância e a infraestrutura física apenas proteger contra hardware nível falhas/falhas de tais um disco, fonte de alimentação, porta de rede ou nó. No entanto, se a aplicação tem de estar sempre disponível e nunca pode perder os dados, terá de implementar a tolerância a falhas nativamente na sua aplicação ou utilizar o software adicional para ativar a tolerância a falhas.

Em primeiro lugar, terá de garantir que a aplicação de que VMS são implementadas com o dimensionamento define para proteger contra falhas ao nível do nó. Para proteger contra a cloud ficar offline, o mesmo aplicativo já deve ser implantado para uma cloud diferente, para que ele pode continuar a atendendo às solicitações sem interrupção. Esse modelo é normalmente chamado de uma implementação de ativo-ativo.

Tenha em atenção que cada nuvem do Azure Stack é independente entre si, para que as nuvens são sempre consideradas Active Directory de uma perspectiva de infraestrutura. Neste caso, várias instâncias ativas do aplicativo são implementadas numa ou mais nuvens Active Directory.

 - RTO: Sem períodos de indisponibilidade
 - RPO: Sem perda de dados
 - Topologia de implementação: Ativo/ativo

### <a name="no-recovery"></a>Sem recuperação

Alguns aplicativos em seu ambiente não poderão ter a proteção contra tempo de inatividade não planeado ou perda de dados. Por exemplo, as VMs utilizadas para desenvolvimento e teste, normalmente, não precisa de ser recuperados. É sua decisão de fazer sem proteção de um aplicativo ou uma VM específica. O Azure Stack não oferece cópia de segurança ou a replicação de VMs da infraestrutura subjacente. Semelhante para o Azure, terá de optar ativamente por participar na proteção para cada VM em cada uma das suas subscrições.

 - RTO: Irrecuperável
 - RPO: Perda de dados completa

## <a name="recommended-topologies"></a>Topologias recomendadas

Considerações importantes para a sua implementação do Azure Stack:

|     | Recomendação | Comentários |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cópia de segurança/restauro VMs para um destino de cópia de segurança externo já implementado no seu datacenter | Recomendado | Tire partido da infraestrutura de cópia de segurança existente e suas capacidades operacionais. Lembre-se de que a infraestrutura de cópia de segurança de tamanho, para que fique pronta para proteger as instâncias VM adicionais. Certifique-se de infraestrutura de cópia de segurança não é próximos à sua origem. Pode restaurar VMs para a Azure Stack, de origem para uma instância secundária do Azure Stack ou no Azure. |
| Cópia de segurança/restauro VMs para um destino de cópia de segurança externo dedicado ao Azure Stack | Recomendado | Pode comprar a infraestrutura de cópia de segurança novo ou provisione a infraestrutura de cópia de segurança dedicado para o Azure Stack. Certifique-se de infraestrutura de cópia de segurança não é próximos à sua origem. Pode restaurar VMs para a Azure Stack, de origem para uma instância secundária do Azure Stack ou no Azure. |
| Cópia de segurança/restauro de VMs diretamente para o global Azure ou um fornecedor de serviços confiáveis | Recomendado | Capaz de atender seus requisitos regulamentares e de privacidade dos dados, desde que pode armazenar as cópias de segurança no global Azure ou um fornecedor de serviços confiáveis. O ideal é que o fornecedor de serviços também está em execução do Azure Stack para que obtém consistência na experiência operacional ao restaurar. |
| VMs de replicar/ativação pós-falha para uma instância separada do Azure Stack | Recomendado | No caso de ativação pós-falha, terá de ter uma cloud do Azure Stack segundo totalmente operacional, para que pode evitar períodos de indisponibilidade de aplicação expandida. |
| Replicar/ativação pós-falha VMs diretamente para o Azure ou um fornecedor de serviços confiáveis | Recomendado | Enquanto capaz de atender seus requisitos regulamentares e de privacidade dos dados, pode replicar os seus dados para o global Azure ou um fornecedor de serviços confiáveis. O ideal é que o fornecedor de serviços também está em execução do Azure Stack para que obtém consistência na experiência operacional após a ativação pós-falha. |
| Implementar o destino de backup na mesma cloud do Azure Stack com os seus dados de aplicação | Não recomendado | Evite armazenar cópias de segurança na mesma nuvem do Azure Stack. Período de indisponibilidade não planeado da cloud pode impedir que os dados de cópia de segurança e os dados primários. Se optar por implementar um destino de cópia de segurança como uma aplicação virtual (para fins de otimização para a cópia de segurança e restauro de mensagens em fila), certifique-se de todos os dados de forma contínua é copiado para uma localização de cópia de segurança externa. |
| Implementar a aplicação de cópia de segurança física para o mesmo rack, onde a solução do Azure Stack está instalada | Não suportado | No momento, não é possível ligar a todos os outros dispositivos na parte superior do comutadores de rack que não fazem parte da solução original. |

## <a name="next-steps"></a>Passos Seguintes

Este artigo fornecido Diretrizes gerais para proteger as VMs de utilizador implementadas no Azure Stack. Para obter informações sobre como utilizar os serviços do Azure para proteger as VMs de utilizador, consulte:

 - [Utilizar o Azure Backup para fazer cópias de segurança de ficheiros e aplicações no Azure Stack](https://docs.microsoft.com/azure/backup/backup-mabs-files-applications-azure-stack)
 - [Suporte de servidor de cópia de segurança do Azure para o Azure Stack](https://docs.microsoft.com/azure/backup/ ) 
 - [Suporte do Azure Site Recovery para o Azure Stack](https://docs.microsoft.com/azure/site-recovery/)  

Para saber mais sobre os produtos de parceiros que oferecem proteção de VM no Azure Stack, consulte "[proteger aplicações e dados no Azure Stack](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/)."
