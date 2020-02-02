---
title: Selecionando o tipo de implementação certo - Base de Dados Azure para MySQL
description: Este artigo descreve quais os fatores a considerar antes de implementar a Base de Dados Azure para o MySQL como uma infraestrutura como um serviço (IaaS) ou plataforma como um serviço (PaaS).
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 8cff61d547e75b186869b3ab4d57c5eb12e6e2f5
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935466"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Escolha a opção certa do MySQL Server em Azure

Com o Azure, as cargas de trabalho do seu servidor MySQL podem ser executadas numa infraestrutura de máquinavirtual hospedada como serviço (IaaS) ou como uma plataforma hospedada como um serviço (PaaS). O PaaS tem múltiplas opções de implementação, existindo níveis de serviço dentro de cada opção de implementação. Quando escolher entre iaaS e PaaS, deve decidir se pretende gerir a sua base de dados, aplicar patches e fazer backups, ou se pretende delegar essas operações no Azure.

Ao tomar a sua decisão, considere as seguintes duas opções:

- **Base de Dados Azure para MySQL**. Esta opção é um motor de base de dados MySQL totalmente gerido com base na versão estável da edição comunitária MySQL. Esta base de dados relacional como serviço (DBaaS), alojada na plataforma cloud Azure, enquadra-se na categoria industrial de PaaS.

  Com uma instância gerida de MySQL no Azure, pode utilizar funcionalidades incorporadas que de outra forma requerem uma configuração extensiva quando o MySQL Server está no local ou num VM Azure.

  Ao utilizar o MySQL como serviço, paga-se à medida que vai com opções para escalar ou escalar para um maior controlo sem interrupção. E ao contrário do Servidor MySQL autónomo, a Base de Dados Azure para o MySQL tem funcionalidades adicionais como alta disponibilidade, inteligência e gestão incorporadas.

- **MySQL em VMs Azure.** Esta opção insere-se na categoria industrial de IaaS. Com este serviço, você pode executar O Servidor MySQL dentro de uma máquina virtual totalmente gerida na plataforma de nuvem Azure. Todas as versões e edições recentes do MySQL podem ser instaladas numa máquina virtual IaaS.

  Na diferença mais significativa da Base de Dados Azure para o MySQL, a MySQL em VMs Azure oferece controlo sobre o motor de base de dados. No entanto, este controlo tem a ver com a responsabilidade de gerir os VMs e muitas tarefas de administração de bases de dados (DBA). Estas tarefas incluem a manutenção e remendar servidores de bases de dados, recuperação de bases de dados e design de alta disponibilidade.

As principais diferenças entre estas opções constam da seguinte tabela:

|            | Base de Dados do Azure para MySQL | MySQL em VMs Azure    |
|:-------------------|:-----------------------------|:--------------------|
| Contrato de nível de serviço (SLA)                | Oferece SLA de 99,99% de disponibilidade| Até 99,95% de disponibilidade com duas ou mais instâncias no mesmo conjunto de disponibilidade.<br/><br/>99,9% de disponibilidade com uma única instância VM utilizando armazenamento premium.<br/><br/>99,99% usando Zonas de Disponibilidade com múltiplas instâncias em vários conjuntos de disponibilidade.<br/><br/>Consulte as [Máquinas Virtuais SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). |
| Patching do sistema operativo        | Automático  | Gerido por clientes |
| Patching MySQL     | Automático  | Gerido por clientes |
| Elevada disponibilidade | O modelo de alta disponibilidade (HA) baseia-se em mecanismos de falha incorporados para quando ocorre uma interrupção do nível do nó. Nestes casos, o serviço cria automaticamente uma nova instância e anexa o armazenamento a este caso. | Cliente arquiteto, implementar, testar e manter alta disponibilidade. As capacidades podem incluir agrupamento, replicação, etc.|
| Redundância de zona | Atualmente não suportado | Os VMs azure podem ser configurados para funcionar em diferentes zonas de disponibilidade. Para uma solução no local, os clientes devem criar, gerir e manter o seu próprio centro de dados secundário.|
| Cenários híbridos | Com a [Replicação data-in,](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication)pode sincronizar dados de um servidor MySQL externo para a Base de Dados Azure para o serviço MySQL. O servidor externo pode estar no local, em máquinas virtuais, ou num serviço de base de dados hospedado por outros fornecedores de nuvem.<br/><br/> Com a funcionalidade de [réplica de leitura,](https://docs.microsoft.com/azure/mysql/concepts-read-replicas) pode replicar dados de uma Base de Dados Azure para o servidor principal mySQL até cinco servidores de réplica sem leitura. As réplicas estão dentro da mesma região de Azure ou em todas as regiões. As réplicas apenas de leitura são atualizadas assincronicamente utilizando a tecnologia de replicação do binlog.| Gerido por clientes
| Backup e restauro | Cria automaticamente [cópias](https://docs.microsoft.com/azure/mysql/concepts-backup#backups) de segurança do servidor e armazena-as em armazenamento configurado pelo utilizador que é localmente redundante ou geo-redundante. O serviço requer cópias de segurança completas, diferenciais e de registo de transações | Gerido por clientes |
| Operações de base de dados de monitorização | Oferece aos clientes a capacidade de [estabelecer alertas](https://docs.microsoft.com/azure/mysql/concepts-monitoring) sobre a operação da base de dados e agir ao atingir limiares. | Gerido por clientes |
| Proteção Avançada Contra Ameaças | Fornece [Proteção Avançada de Ameaças](https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal). Esta proteção deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de acesso ou exploração de bases de dados. | Os clientes devem construir esta proteção para si mesmos.
| Recuperação após desastre | Armazene cópias de segurança automatizadas em [armazenamento localmente redundante ou geo-redundante](https://docs.microsoft.com/azure/mysql/howto-restore-server-portal)configurado pelo utilizador. As cópias de segurança também podem restaurar um servidor a um ponto no tempo. O período de retenção é de 7 a 35 dias. A restauração é realizada utilizando o portal Azure. | Totalmente gerido pelos clientes. As responsabilidades incluem, mas não se limitam a agendamento, teste, arquivamento, armazenamento e retenção. Uma opção adicional é usar um cofre dos Serviços de Recuperação Azure para fazer o backe Azure VMs e bases de dados em VMs. Esta opção está em pré-visualização. |
| Recomendações de desempenho | Fornece aos clientes recomendações de [desempenho baseadas](https://techcommunity.microsoft.com/t5/Azure-Database-for-MySQL/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) em ficheiros de registo de utilização gerados pelo sistema. As recomendações ajudam a otimizar as cargas de trabalho. | Gerido por clientes |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Motivações empresariais para escolher PaaS ou IaaS

Existem vários fatores que podem influenciar a sua decisão de escolher PaaS ou IaaS para hospedar as suas bases de dados MySQL.

### <a name="cost"></a>Custo

O financiamento limitado é muitas vezes a principal consideração que determina a melhor solução para hospedar as suas bases de dados. Isto é verdade, quer seja uma startup com pouco dinheiro ou uma equipa numa empresa estabelecida que opera sob restrições orçamentais apertadas. Esta secção descreve os fundamentos de faturação e licenciamento em Azure, à medida que se aplicam à Base de Dados Azure para MySQL e MySQL em VMs Azure.

#### <a name="billing"></a>Faturação

A Base de Dados Azure para mySQL está atualmente disponível como um serviço em vários níveis com preços diferentes para recursos. Todos os recursos são cobrados de hora em hora a uma taxa fixa. Para obter as informações mais recentes sobre os níveis de serviço suportados atualmente, tamanhos de computação e valores de armazenamento, consulte o [modelo de compra baseado em vCore](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers). Pode ajustar dinamicamente os níveis de serviço e calcular tamanhos para corresponder às necessidades variadas de entrada da sua aplicação. É cobrado pelo tráfego de Internet cessante a taxas regulares de [transferência de dados.](https://azure.microsoft.com/pricing/details/data-transfers/)

Com a Base de Dados Azure para MySQL, a Microsoft configura automaticamente, patches e atualiza o software de base de dados. Estas ações automatizadas reduzem os seus custos de administração. Além disso, a Base de Dados Azure para mySQL tem capacidades [de backup incorporadas.](https://docs.microsoft.com/azure/mysql/concepts-backup) Estas capacidades ajudam-no a obter poupanças significativas de custos, especialmente quando se tem um grande número de bases de dados. Em contraste, com mySQL em VMs Azure pode escolher e executar qualquer versão MySQL. Independentemente da versão MySQL que utilize, paga-se pelo VM provisionado e pelos custos do tipo de licença MySQL específico utilizado.

A Base de Dados Azure para o MySQL proporciona uma elevada disponibilidade incorporada para qualquer tipo de interrupção ao nível do nó, mantendo a garantia de 99,99% de SLA para o serviço. No entanto, para a elevada disponibilidade de bases de dados dentro de VMs, os clientes devem usar as opções de alta disponibilidade como [a replicação MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication.html) que estão disponíveis numa base de dados MySQL. A utilização de uma opção de alta disponibilidade suportada não fornece um SLA adicional. Mas permite-lhe obter uma disponibilidade superior a 99,99% da base de dados a custos adicionais e despesas administrativas.

Para obter mais informações sobre os preços, consulte os seguintes artigos:
* [Base de Dados Azure para preços MySQL](https://azure.microsoft.com/pricing/details/mysql/)
* [Preços de máquina virtual](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administração

Para muitas empresas, a decisão de transitar para um serviço na nuvem tem tanto a ver com o descarregamento da complexidade da administração como com o custo. Com IaaS e PaaS, Microsoft:

- Administra a infraestrutura subjacente.
- Replica automaticamente todos os dados para fornecer recuperação de desastres.
- Configura e atualiza o software da base de dados.
- Gere o equilíbrio de carga.
- Falha transparente se houver uma falha no servidor.

A seguinte lista descreve considerações administrativas para cada opção:

* Com base de dados Azure para MySQL, pode continuar a administrar a sua base de dados. Mas já não precisas de gerir o motor de base de dados, o sistema operativo ou o hardware. Exemplos de itens que pode continuar a administrar incluem:

  - Bases de dados
  - Iniciar sessão
  - Otimização de índices
  - Afinação de consultas
  - Auditoria
  - Segurança

  Além disso, configurar alta disponibilidade para outro centro de dados requer uma configuração ou administração mínimas.

* Com o MySQL nos VMs Azure, tem controlo total sobre o sistema operativo e a configuração da instância do servidor MySQL. Com um VM, decide quando atualizar ou atualizar o sistema operativo e o software de base de dados. Também decide quando instalar qualquer software adicional, como uma aplicação antivírus. Algumas funcionalidades automatizadas são fornecidas para simplificar muito o patching, backup e alta disponibilidade. Pode controlar o tamanho do VM, o número de discos e as suas configurações de armazenamento. Para mais informações, consulte a máquina virtual e os tamanhos do [serviço em nuvem para o Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

### <a name="time-to-move-to-azure"></a>Hora de se mudar para Azure

* A Base de Dados Azure para o MySQL é a solução certa para aplicações desenhadas na nuvem quando a produtividade do desenvolvedor e o tempo rápido para comercializar novas soluções são fundamentais. Com funcionalidade programática que é como a DBA, o serviço é adequado para arquitetos e desenvolvedores de nuvem porque reduz a necessidade de gestão do sistema operativo subjacente e da base de dados.

* Quando pretende evitar o tempo e as despesas de aquisição de novos hardware no local, o MySQL em VMs Azure é a solução certa para aplicações que requerem uma base de dados MySQL ou acesso a funcionalidades MySQL no Windows ou Linux. Esta solução também é adequada para migrar as aplicações e bases de dados existentes no local para O Azure intactos, para casos em que a Base de Dados Azure para mySQL é um mau ajuste.

  Como não há necessidade de alterar a apresentação, aplicação e camadas de dados, poupa tempo e orçamento na rearquitetação da sua solução existente. Em vez disso, pode focar-se em migrar todas as suas soluções para o Azure e abordar algumas otimizações de desempenho que a plataforma Azure poderá necessitar.

## <a name="next-steps"></a>Passos seguintes

* Consulte a [Base de Dados Azure para obter preços MySQL](https://azure.microsoft.com/pricing/details/MySQL/).
* Comece por [criar o seu primeiro servidor](https://review.docs.microsoft.com/azure/MySQL/quickstart-create-MySQL-server-database-using-azure-portal).
