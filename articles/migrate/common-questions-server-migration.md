---
title: Perguntas comuns sobre migração de servidores Azure Migrate
description: Obtenha respostas para perguntas comuns sobre a utilização da migração do servidor Azure Migrate para migrar máquinas.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 08/28/2020
ms.openlocfilehash: 63c7f226dcd99ec8040f2078ce12be0fe3c594df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99548818"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Migração do servidor Azure Migrate: Questões comuns

Este artigo responde a perguntas comuns sobre a ferramenta Azure Migrate: Server Migration. Se tiver outras questões, verifique estes recursos:

- [Perguntas gerais](resources-faq.md) sobre Azure Migrate
- Perguntas sobre o [aparelho Azure Migrate](common-questions-appliance.md)
- Perguntas sobre [descoberta, avaliação e visualização de dependência](common-questions-discovery-assessment.md)
- Obtenha perguntas respondidas no [fórum Azure Migrate](https://aka.ms/AzureMigrateForum)

## <a name="does-azure-migrate-convert-uefi-based-machines-to-bios-based-machines-and-migrate-them-to-azure-as-azure-generation-1-vms"></a>A Azure Migrate converte máquinas baseadas em UEFI em máquinas baseadas em BIOS e migra-as para Azure como VMs de geração Azure 1?
Azure Migrate: A ferramenta de migração do servidor migra todas as máquinas baseadas na UEFI para Azure como Azure geração 2 VMs. Já não apoiamos a conversão de VMs baseados na UEFI em VMs baseados em BIOS. Note que todas as máquinas baseadas em BIOS são migradas para Azure como Azure geração 1 VMs apenas.

## <a name="how-can-i-migrate-uefi-based-machines-to-azure-as-azure-generation-1-vms"></a>Como posso migrar máquinas baseadas na UEFI para Azure como Azure geração 1 VMs?
Azure Migrate: A ferramenta de migração do servidor migra máquinas baseadas em UEFI para Azure como Azure geração 2 VMs. Se quiser emigrá-los para a geração Azure 1 VMs, converta o tipo de bota em BIOS antes de iniciar a replicação e, em seguida, utilize a ferramenta Azure Migrate: Server Migration para migrar para Azure.
 
## <a name="which-operating-systems-are-supported-for-migration-of-uefi-based-machines-to-azure"></a>Que sistemas operativos são suportados para a migração de máquinas baseadas na UEFI para Azure?

| **Sistemas operativos suportados para máquinas baseadas na UEFI** | **VMware sem agente para Azure**                                                                                                             | **Hyper-V sem agente para Azure** | **VMware baseado em agente, nuvens físicas e outras para Azure** |
| ------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ | ---------------------------------------------------------- |
| Windows Server 2019, 2016, 2012 R2, 2012                | Y                                                                                                                                         | Y                              | Y                                                          |
| Windows 10 Pro, Empresa Windows 10                   | Y                                                                                                                                         | Y                              | Y                                                          |
| SUSE Linux Enterprise Server 15 SP1                     | Y                                                                                                                                         | Y                              | Y                                                          |
| SUSE Linux Enterprise Server 12 SP4                     | Y                                                                                                                                         | Y                              | Y                                                          |
| Ubuntu Server 16.04, 18.04, 19.04, 19.10                | Y                                                                                                                                         | Y                              | Y                                                          |
| RHEL 8.1, 8.0, 7.8, 7.7, 7.6, 7.5, 7.4, 7.0, 6.x        | Y<br>                 _RHEL 8.x requer [preparação manual](./prepare-for-migration.md#linux-machines)_   | Y                              | Y                                                          |
| Cent OS 8.1, 8.0, 7.7, 7.6, 7.5, 7.4, 6.x               | Y<br>_Cent OS 8.x requer [preparação manual](./prepare-for-migration.md#linux-machines)_ | Y                              | Y                                                          |
| Oracle Linux 7.7, 7.7-CI                                |  Y                                                                                                                                        | Y                              | Y                                                          |

## <a name="can-i-use-the-recovery-services-vault-created-by-azure-migrate-for-disaster-recovery-scenarios"></a>Posso usar o cofre de serviços de recuperação criado por Azure Migrate para cenários de recuperação de desastres?
Não recomendamos a utilização do cofre de serviços de recuperação criado pela Azure Migrate para cenários de recuperação de desastres. Fazê-lo pode resultar em falhas de replicação de início em Azure Migrate. 

## <a name="where-should-i-install-the-replication-appliance-for-agent-based-migrations"></a>Onde devo instalar o aparelho de replicação para migrações baseadas em agentes?

O aparelho de replicação deve ser instalado numa máquina dedicada. O aparelho de replicação não deve ser instalado numa máquina de origem que pretende replicar ou no aparelho de deteção e avaliação Azure Migrate que possa ter instalado anteriormente. Siga o [tutorial](./tutorial-migrate-physical-virtual-machines.md) para mais detalhes.

## <a name="how-can-i-migrate-my-aws-ec2-instances-to-azure"></a>Como posso migrar os meus casos AWS EC2 para Azure?

Reveja este [artigo](./tutorial-migrate-aws-virtual-machines.md) para descobrir, avaliar e migrar as suas instâncias AWS EC2 para Azure.

## <a name="can-i-migrate-aws-vms-running-amazon-linux-operating-system"></a>Posso migrar VMS AWS com sistema operativo Amazon Linux?

Os VMs que executam o Amazon Linux não podem ser migrados como é, uma vez que o Amazon Linux OS é apenas suportado em AWS.
Para migrar cargas de trabalho em execução no Amazon Linux, você pode girar um CentOS/RHEL VM em Azure e migrar a carga de trabalho em execução na máquina AWS Linux usando uma abordagem de migração de carga de trabalho relevante. Por exemplo, dependendo da carga de trabalho, pode existir ferramentas específicas da carga de trabalho para ajudar na migração – como por exemplo, para bases de dados ou ferramentas de implementação no caso de servidores web.

## <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>Que geografias são apoiadas para a migração com Azure Migrate?

Reveja as regiões suportadas em [clouds públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e do [Azure Government](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-regions"></a>Podemos usar o mesmo projeto Azure Migrate para migrar para várias regiões?

Embora possa criar avaliações para várias regiões num projeto Azure Migrate, um projeto Azure Migrate pode ser usado para migrar servidores apenas para uma região de Azure. Você pode criar projetos adicionais de Azure Migrate para cada região para onde precisa migrar.

- Para migrações de VMware sem agente, a região alvo fica bloqueada assim que ativar a primeira replicação.
- Para migrações baseadas em agentes (VMware, servidores físicos e servidores de outras nuvens), a região alvo é bloqueada uma vez que o botão "Criar Recursos" é clicado no portal enquanto configura o aparelho de replicação.
- Para migrações hiper-V sem agente, a região alvo é bloqueada uma vez que o botão "Criar Recursos" é clicado no portal enquanto configura o fornecedor de replicação Hyper-V.

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-subscriptions"></a>Podemos usar o mesmo projeto Azure Migrate para migrar para várias subscrições? 

Sim, você pode migrar para várias subscrições na mesma região alvo para um projeto Azure Migrate. Pode selecionar a subscrição-alvo enquanto permite a replicação de uma máquina ou de um conjunto de máquinas. A região alvo é a primeira replicação de postes bloqueados para migrações de VMware sem agente e durante a instalação de aparelhos de replicação e fornecedor de hiper-V para migrações baseadas em agentes e migrações de hiper-V sem agente, respectivamente.

## <a name="what-are-the-migration-options-in-azure-migrate-server-migration"></a>Quais são as opções de migração em Azure Migrate: Migração de servidores?

A ferramenta Azure Migrate: Server Migration fornece duas opções para realizar migrações dos seus Servidores/VMs de origem para Azure – migração sem agentes e migração baseada em agentes.

Independentemente da opção de migração escolhida, o primeiro passo para migrar um servidor usando a migração do Azure: Migração do servidor é permitir a replicação para o servidor. Isto executa uma replicação inicial dos seus dados VM/servidor para Azure. Após a replicação inicial ser concluída, é estabelecida uma replicação em curso (delta-sync em curso) para migrar dados incrementais para Azure. Uma vez que a operação atinja a fase delta-sync, pode optar por migrar para Azure a qualquer momento.  

Eis algumas considerações a ter em mente ao decidir sobre a opção de migração.

**As migrações sem agentes** não requerem que nenhum software (agentes) seja implantado na origem VMs/servidores que estão a ser migrados. A opção sem agente orquestra a replicação integrando-se com a funcionalidade fornecida pelo fornecedor de virtualização.
As opções de replicação sem agente estão disponíveis para [VMware VMs](./tutorial-migrate-vmware.md) e [VMs Hiper-V](./tutorial-migrate-hyper-v.md).

**As migrações baseadas em agentes** exigem que o software (agentes) Azure Migrate seja instalado na fonte VMs/máquinas a migrar. A opção baseada em agente não depende da plataforma de virtualização para a funcionalidade de replicação e pode, portanto, ser usada com qualquer servidor que executa uma arquitetura x86/x64 e uma versão de um sistema operativo suportado pelo método de replicação baseado no agente.

A opção de migração baseada em agentes pode ser utilizada para [VMware VMs,](./tutorial-migrate-vmware-agent.md) [Hiper-VMs,](./tutorial-migrate-physical-virtual-machines.md) [servidores físicos,](./tutorial-migrate-physical-virtual-machines.md) [VMs em execução em AWS,](./tutorial-migrate-aws-virtual-machines.md)VMs em execução em GCP, ou VMs em execução em um fornecedor de virtualização diferente. A migração baseada em agentes trata as suas máquinas como servidores físicos para efeitos da migração.

Embora a Migração Sem Agente ofereça comodidade e simplicidade adicionais sobre as opções de replicação baseadas em agente para os cenários suportados (VMWare e Hyper-V), poderá considerar a utilização do cenário baseado no agente para os seguintes casos de utilização:

- Ambiente constrangido do IOPS: A replicação sem agente utiliza instantâneos e consome armazenamento IOPS/largura de banda. Recomendamos o método de migração baseado em agente se houver restrições no armazenamento/IOPS no seu ambiente.
- Se não tiver um servidor vCenter, pode tratar os seus VMware VMs como servidores físicos e utilizar o fluxo de trabalho de migração baseado em agentes.

Para saber mais, reveja este [artigo](./server-migrate-overview.md) para comparar opções de migração para migrações VMware.

## <a name="how-does-agentless-migration-work"></a>Como funciona a migração sem agente?

Azure Migrate: A Migração do Servidor oferece opções de replicação sem agentes para a migração de máquinas virtuais VMware e máquinas virtuais Hiper-V que executam o Windows ou o Linux. A ferramenta também fornece uma opção de replicação adicional baseada em agente para servidores Windows e Linux que podem ser usados para migrar servidores físicos, bem como máquinas virtuais x86/x64 em VMware, Hyper-V, AWS, GCP, etc. A opção de replicação baseada em agente requer a instalação de software de agente no servidor/máquina virtual que está a ser migrado, enquanto na opção sem agente nenhum software precisa de ser instalado nas próprias máquinas virtuais, oferecendo assim comodidade e simplicidade adicionais sobre a opção de replicação baseada no agente.

A opção de replicação sem agente funciona utilizando mecanismos fornecidos pelo fornecedor de virtualização (VMware, Hyper-V). No caso das máquinas virtuais VMware, o mecanismo de replicação sem agente utiliza instantâneos VMware e VMware alterou a tecnologia de rastreio de blocos para replicar dados de discos de máquinas virtuais. Este mecanismo é semelhante ao utilizado por muitos produtos de reserva. No caso das máquinas virtuais Hyper-V, o mecanismo de replicação sem agente utiliza instantâneos VM e a capacidade de rastreio de alterações da réplica Hyper-V para replicar dados de discos de máquinas virtuais.

Quando a replicação é configurada para uma máquina virtual, passa primeiro por uma fase inicial de replicação. Durante a replicação inicial, é tirada uma imagem VM e uma cópia completa dos dados dos discos instantâneos são replicados em discos geridos na sua subscrição. Após a replicação inicial para o VM estar concluída, o processo de replicação transita para uma fase de replicação incremental (replicação delta). Na fase de replicação incremental, as alterações de dados que ocorreram desde o último ciclo de replicação concluído são periodicamente replicadas e aplicadas aos discos geridos por réplicas, mantendo assim a replicação em sincronização com as alterações que ocorriam no VM. No caso das máquinas virtuais VMware, a tecnologia de rastreio de blocos alterada da VMware é utilizada para acompanhar as mudanças entre ciclos de replicação. No início do ciclo de replicação, é tirada uma imagem VM e é utilizada uma localização de blocos alterada para obter as alterações entre o instantâneo atual e o último instantâneo replicado com sucesso. Desta forma, apenas os dados que mudaram desde o último ciclo de replicação concluído precisam de ser replicados para manter a replicação para o VM em sincronização. No final de cada ciclo de replicação, o instantâneo é libertado e a consolidação do instantâneo é realizada para a máquina virtual. Da mesma forma, no caso das máquinas virtuais Hyper-V, o motor de rastreio de mudança de réplica Hiper-V é utilizado para acompanhar as mudanças entre ciclos de replicação consecutivos.
Quando executa a operação de migração numa máquina virtual replicante, tem a opção de desligar a máquina virtual no local e executar uma replicação incremental final para garantir a perda zero de dados. Ao executar a opção migradora, os discos geridos por réplica correspondentes à máquina virtual são utilizados para criar a máquina virtual em Azure.

Para começar, consulte os tutoriais de [migração sem agente VMware](./tutorial-migrate-vmware.md) e [hiper-V sem agente.](./tutorial-migrate-hyper-v.md)

## <a name="how-does-agent-based-migration-work"></a>Como funciona a migração baseada em agentes?

Além das opções de migração sem agente para máquinas virtuais VMware e máquinas virtuais Hiper-V, a ferramenta de migração do Servidor oferece uma opção de migração baseada em agentes para migrar servidores Windows e Linux que estão a funcionar em servidores físicos, ou funcionando como máquinas virtuais x86/x64 em VMware, Hyper-V, AWS, Google Cloud Platform, etc.

O método de migração baseado em agente utiliza software de agente instalado no servidor em migração para replicar dados do servidor para OZure. O processo de replicação utiliza uma arquitetura de descarregamento na qual o agente retransmite dados de replicação a um servidor de replicação dedicado chamado de aparelho de replicação ou Servidor de Configuração (ou a um Servidor de Processo de escala). [Saiba mais](./agent-based-migration-architecture.md) sobre como funciona a opção de migração baseada em agentes. 

Nota: O aparelho de replicação é diferente do aparelho de descoberta Azure Migrate e deve ser instalado numa máquina separada/dedicada.

## <a name="how-do-i-gauge-the-bandwidth-requirement-for-my-migrations"></a>Como posso avaliar o requisito de largura de banda para as minhas migrações?

A largura de banda para replicação de dados para a Azure depende de uma série de fatores e é uma função da rapidez com que o aparelho Azure Migrate pode ler e replicar os dados para o Azure. A replicação tem duas fases: replicação inicial e replicação delta.

Quando a replicação começa para um VM, ocorre um ciclo inicial de replicação no qual cópias completas dos discos são replicadas. Após a replicação inicial estar concluída, os ciclos de replicação incrementais (ciclos delta) são programados periodicamente para transferir quaisquer alterações que tenham ocorrido desde o ciclo de replicação anterior.

### <a name="agentless-vmware-vm-migration"></a>Migração VMware VMware sem agente

Pode calcular o requisito de largura de banda com base no volume de dados necessário para ser movido na onda e tempo em que gostaria que a replicação inicial fosse completada (idealmente, gostaria que a replicação inicial tivesse completado pelo menos 3-4 dias antes da janela de migração real para lhe dar tempo suficiente para realizar uma migração de teste antes da janela real e para manter o tempo de inatividade ao mínimo durante a janela).

Pode estimar a largura de banda ou o tempo necessário para a migração VMware VM sem agente utilizando a seguinte fórmula:

Tempo para completar a replicação inicial = {tamanho de discos (ou tamanho usado se disponível) * 0,7 (assumindo uma média de compressão de 30% – estimativa conservadora)}/largura de banda disponível para replicação.

### <a name="agent-based-vmware-vm-migration"></a>Migração VMware VM baseada em agente

Para um método de replicação baseado em agente, o planejador de implementação pode ajudar a perfilar o ambiente para o churn de dados e ajudar a prever o requisito de largura de banda necessário. Para saber mais, veja este [artigo.](./agent-based-migration-architecture.md#plan-vmware-deployment) 

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>Como posso acelerar a replicação na utilização do aparelho Azure Migrate para a replicação de VMware sem agente?  

Pode acelerar usando a NetQosPolicy. Por exemplo:

O AppNamePrefix para usar na NetQosPolicy é "GatewayWindowsService.exe". Pode criar uma política sobre o aparelho Azure Migrate para acelerar o tráfego de replicação do aparelho, criando uma política como esta:

New-NetQosPolicy -Nome "ThrottleReplication" -AppPathNameMatchCondição "GatewayWindowsService.exe" -ThrottleRateActionBitsPerSegd 1 MB

## <a name="how-is-the-data-transmitted-from-on-prem-environment-to-azure-is-it-encrypted-before-transmission"></a>Como é transmitidos os dados do ambiente on-prem para o Azure? Está encriptado antes da transmissão?

O aparelho Azure Migrate no caso de replicação sem agente comprime dados e encripta antes de ser carregado. Os dados são transmitidos através de um canal de comunicação seguro através de https e utilizam o TLS 1.2 ou mais tarde. Além disso, o Azure Storage encripta automaticamente os seus dados quando este é persistido na nuvem (encriptação em repouso).  

## <a name="how-does-churn-rate-affect-agentless-replication"></a>Como é que a taxa de churn afeta a replicação sem agente?

Como a replicação sem agente se dobra nos dados, o *padrão de churn* é mais importante do que a *taxa de churn*. Quando um ficheiro é escrito uma e outra vez, a taxa não tem muito impacto. No entanto, um padrão em que todos os outros sectores são escritos causa uma grande agitação no ciclo seguinte. Como minimizamos a quantidade de dados que transferimos, permitimos que os dados dobrem o máximo possível antes de agendarmos o próximo ciclo.

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Com que frequência é programado um ciclo de replicação?

A fórmula para agendar o próximo ciclo de replicação é (tempo de ciclo / 2) ou uma hora, o que for maior.

Por exemplo, se um VM demorar quatro horas para um ciclo delta, o próximo ciclo é programado em duas horas, e não na próxima hora. O processo é diferente imediatamente após a replicação inicial, quando o primeiro ciclo delta é programado imediatamente.

## <a name="how-do-i-migrate-windows-server-2003-running-on-vmwarehyper-v-to-azure"></a>Como posso migrar o Windows Server 2003 em funcionamento em VMware/Hyper-V para Azure?

[O suporte alargado do Windows Server 2003](/troubleshoot/azure/virtual-machines/run-win-server-2003#microsoft-windows-server-2003-end-of-support) terminou em 14 de julho de 2015.  A equipa de suporte do Azure continua a ajudar na resolução de problemas que dizem respeito a executar o Windows Server 2003 no Azure. No entanto, este suporte está limitado a questões que não requerem resolução de problemas ou patches ao nível do SISTEMA.
Migrar as suas aplicações para instâncias Azure que executam uma versão mais recente do Windows Server é a abordagem recomendada para garantir que está a aproveitar eficazmente a flexibilidade e fiabilidade da nuvem Azure.

No entanto, se ainda optar por migrar o Seu Windows Server 2003 para Azure, pode utilizar a ferramenta Azure Migrate: Server Migration se o seu Servidor do Windows for um VM em execução em VMware ou Hyper-V Rever este artigo para [preparar as máquinas do Windows Server 2003 para migração](./prepare-windows-server-2003-migration.md).

## <a name="what-is-the-difference-between-the-test-migration-and-migrate-operations"></a>Qual é a diferença entre as operações de migração de testes e migração?

A migração de testes fornece uma forma de testar e validar migrações antes da migração real. Testar a migração funciona permitindo-lhe criar cópias de teste de replicação de VMs num ambiente de caixa de areia em Azure. O ambiente da caixa de areia é demarcado por uma rede virtual de teste que especifica. A operação de migração de testes não é disruptiva, com as aplicações a continuarem a funcionar na fonte enquanto lhe permite realizar testes numa cópia clonada num ambiente isolado de caixa de areia. Pode realizar vários testes conforme necessário para validar a migração, realizar testes de aplicações e resolver quaisquer problemas antes da migração real.

## <a name="will-windows-server-2008-and-2008-r2-be-supported-in-azure-after-migration"></a>O Windows Server 2008 e o R2 de 2008 serão suportados no Azure após a migração?

Pode migrar os seus servidores Windows Server 2008 e 2008 R2 para máquinas virtuais Azure e obter Atualizações de Segurança Alargadas durante três anos após as datas de Fim de Suporte, sem custos adicionais acima do custo de funcionamento da máquina virtual. Pode utilizar a ferramenta Azure Migrate: Server Migration para migrar as cargas de trabalho do Windows Server 2008 e 2008 R2.

## <a name="is-there-a-rollback-option-for-azure-migrate"></a>Existe uma opção de reversão para a Azure Migrate?

Pode utilizar a opção de Migração de Testes para validar a funcionalidade e desempenho da sua aplicação no Azure. Pode realizar qualquer número de migrações de teste e executar a migração final depois de estabelecer confiança através da operação de migração de testes. Uma migração de teste não afeta a máquina no local, que permanece operacional e continua a replicar-se até que realize a migração real. Se houve erros durante a migração do teste UAT, pode optar por adiar a migração final e manter a sua fonte VM/servidor em funcionamento e replicação para Azure. Pode voltar a atacar a migração final assim que resolver os erros.  
Nota: Uma vez realizada uma migração final para Azure e a máquina de origem no local foi desligada, não é possível efetuar uma reversão de Azure para o seu ambiente no local.

## <a name="can-i-select-the-virtual-network-and-subnet-to-use-for-test-migrations"></a>Posso selecionar a Rede Virtual e a sub-rede para usar para fazer migrações de teste?

Pode selecionar uma Rede Virtual para migrações de teste. A sub-rede é selecionada automaticamente com base na seguinte lógica:

- Se uma sub-rede-alvo (que não seja o padrão) foi especificada como uma entrada enquanto permite a replicação, então a Azure Migrate prioriza usando uma sub-rede com o mesmo nome na Rede Virtual selecionada para a migração do teste.
- Se a sub-rede com o mesmo nome não for encontrada, então a Azure Migrate seleciona a primeira sub-rede disponível alfabeticamente que não é uma sub-rede Gateway/Application Gateway/Firewall/Bastion.

## <a name="why-is-the-test-migration-button-disabled-for-my-server"></a>Porque é que o botão de migração de teste está desativado para o meu Servidor?

O botão de migração do teste pode estar num estado de desativação nos seguintes cenários:

- Não é possível iniciar uma migração de teste até que uma replicação inicial (IR) esteja concluída para o VM. O botão de migração do teste será desativado até que o processo de INFRA esteja concluído. Pode realizar uma migração de teste uma vez que o seu VM esteja numa fase de sincronização delta.
- O botão pode ser desativado se uma migração de teste já estiver concluída, mas não foi realizada uma limpeza de migração de testes para esse VM. Efetue uma limpeza de migração de teste e relempenhem a operação.

## <a name="what-happens-if-i-dont-clean-up-my-test-migration"></a>O que acontece se eu não limpar a migração do meu teste?

A migração de testes simula a migração real criando um teste Azure VM usando dados replicados. O servidor será implantado com uma cópia pontual dos dados replicados para o Grupo de Recursos alvo (selecionado enquanto permite a replicação) com um sufixo "teste". As migrações de teste destinam-se a validar a funcionalidade do servidor de modo a minimizar os problemas de migração pós-migração. Se a migração do teste não for limpa após os testes, a máquina virtual de teste continuará a funcionar em Azure e incorrerá em cargas. Para fazer a limpeza após uma migração de teste, vá à visualização de máquinas de replicação na ferramenta de migração do servidor e utilize a ação de "migração de testes de limpeza" na máquina.

## <a name="can-i-migrate-active-directory-domain-controllers-using-azure-migrate"></a>Posso migrar controladores de domínio do Ative Directory usando a Azure Migrate?

A ferramenta de migração do servidor é agnóstica de aplicação e funciona para a maioria das aplicações. Quando migrar um servidor utilizando a ferramenta De migração do servidor, todas as aplicações instaladas no servidor são migradas juntamente com ele. No entanto, para algumas aplicações, métodos de migração alternativos que não a migração de servidores podem ser mais adequados para a migração.  Para o Ative Directory, no caso de ambientes híbridos onde o site no local está ligado ao seu ambiente Azure, pode estender o seu Diretório para Azure adicionando controladores de domínio adicionais em Azure e configurando a replicação ative directy. Se estiver a migrar para um ambiente isolado em Azure, exigindo os seus próprios controladores de domínio (ou testar aplicações num ambiente de caixa de areia), pode migrar servidores utilizando a ferramenta de migração do servidor.

## <a name="what-happens-if-i-dont-stop-replication-after-migration"></a>O que acontece se eu não parar a replicação após a migração?

Quando para a replicação, a ferramenta Azure Migrate: Server Migration limpa os discos geridos na subscrição que foram criados para replicação. Se não parar a replicação após uma migração, continuará a incorrer em taxas para estes discos. A replicação de stop não impactará os discos ligados a máquinas que já foram migradas.

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>Preciso de VMware vCenter para migrar VMware VMs?

Para [migrar VMware VMs](server-migrate-overview.md) utilizando a migração baseada em agentes VMware ou sem agentes, os anfitriões ESXi nos quais estão localizados os VMs devem ser geridos pelo vCenter Server. Se não tiver vCenter Server, pode migrar VMware VMs migrando-os como servidores físicos. [Saiba mais](migrate-support-matrix-physical-migration.md).

## <a name="can-i-upgrade-my-os-while-migrating"></a>Posso atualizar o meu so durante a migração?

A ferramenta Azure Migrate: A ferramenta de migração do servidor apenas suporta migrações similares atualmente. A ferramenta não suporta o upgrade da versão SO durante a migração. A máquina migrada terá o mesmo SISTEMA que a máquina de origem.

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliances"></a>Implementei dois (ou mais) aparelhos para descobrir VMs no meu servidor vCenter. No entanto, quando tento migrar os VMs, só vejo VMs correspondentes a um dos aparelhos.

Se existirem várias aplicações configuradas, será necessário que não haja nenhuma sobreposição entre as VMs nas contas do vCenter indicadas. Uma deteção com uma sobreposição desse tipo é um cenário não suportado.

## <a name="how-do-i-know-if-my-vm-was-successfully-migrated"></a>Como sei se o meu VM foi migrado com sucesso?

Uma vez migrado o seu VM/servidor com sucesso, pode visualizar e gerir o VM a partir da página Máquinas Virtuais. Ligue-se ao VM migrado para validar.
Em alternativa, pode rever o "Estado do Trabalho" para a operação para verificar se a migração foi concluída com sucesso. Se vir algum erro, resolva-os e recandiduça a operação de migração.

## <a name="can-i-consolidate-multiple-source-vms-into-one-vm-while-migrating"></a>Posso consolidar vMs de múltiplas fontes em um VM enquanto migra?

Azure Migrate servidor capacidades de migração suportam como migrações atualmente. Não suportamos a consolidação de servidores ou a atualização do sistema operativo como parte da migração. 

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>Como é que a replicação sem agente afeta os servidores VMware?

A replicação sem agente resulta em algum impacto de desempenho nos anfitriões VMware vCenter Server e VMware ESXi. Como a replicação sem agente utiliza instantâneos, consome IOPS no armazenamento, pelo que é necessária uma largura de banda de armazenamento IOPS. Não recomendamos a utilização de replicação sem agente se tiver restrições no armazenamento ou IOPs no seu ambiente.


## <a name="next-steps"></a>Passos seguintes

Leia a visão geral do [Azure Migrate](migrate-services-overview.md).
