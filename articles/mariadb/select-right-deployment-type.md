---
title: Selecionando o tipo de implementação certo - Base de Dados Azure para MariaDB
description: Este artigo descreve quais os fatores a considerar antes de implementar a Base de Dados Azure para MariaDB como infraestrutura como serviço (IaaS) ou plataforma como um serviço (PaaS).
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 47aff04dfd44ea7fd892fdee763e93d7fd13a9d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91542398"
---
# <a name="choose-the-right-mariadb-server-option-in-azure"></a>Escolha a opção de Servidor MariaDB certa em Azure

Com o Azure, as cargas de trabalho do seu servidor MariaDB podem funcionar numa infraestrutura de máquinas virtuais hospedada como um serviço (IaaS) ou como uma plataforma hospedada como um serviço (PaaS). O PaaS tem várias opções de implementação, e existem níveis de serviço dentro de cada opção de implementação. Quando escolher entre IaaS e PaaS, deve decidir se pretende gerir a sua base de dados, aplicar patches e fazer backups, ou se pretende delegar estas operações no Azure.

Ao tomar a sua decisão, considere as seguintes duas opções:

- **Base de Dados Azure para MariaDB:** Esta opção é um motor de base de dados MariaDB totalmente gerido com base na versão estável da edição comunitária mariaDB. Esta base de dados relacional como um serviço (DBaaS), hospedado na plataforma cloud Azure, insere-se na categoria industrial do PaaS.

  Com um exemplo gerido de MariaDB no Azure, pode utilizar funcionalidades incorporadas que de outra forma requerem uma configuração extensiva quando o MariaDB Server está no local ou num VM Azure.

  Ao utilizar o MariaDB como serviço, paga à medida que vai com opções para escalar ou escalar para um maior controlo sem interrupção. E ao contrário do Servidor MariaDB autónomo, a Base de Dados Azure para MariaDB tem funcionalidades adicionais como alta disponibilidade, inteligência e gestão incorporadas.

- **MariaDB em VMs Azure:** Esta opção insere-se na categoria industrial do IaaS. Com este serviço, pode executar o MariaDB Server dentro de uma máquina virtual totalmente gerida na plataforma cloud Azure. Todas as versões e edições recentes do MariaDB podem ser instaladas numa máquina virtual IaaS.

  Na diferença mais significativa da Base de Dados Azure para a MariaDB, a MariaDB em VMs Azure oferece controlo sobre o motor da base de dados. No entanto, este controlo tem o custo da responsabilidade de gerir as VMs e muitas tarefas da administração de bases de dados (DBA). Estas tarefas incluem a manutenção e remendar servidores de bases de dados, recuperação de bases de dados e design de alta disponibilidade.

As principais diferenças entre estas opções constam do quadro seguinte:

| Atributo          | Azure Database for MariaDB | MariaDB em VMs Azure    |
|:-------------------|:-----------------------------|:--------------------|
| Contrato de nível de serviço (SLA)                | Oferece SLA de 99,99% de disponibilidade| Até 99,95% de disponibilidade com duas ou mais instâncias no mesmo conjunto de disponibilidade.<br/><br/>99,9% de disponibilidade com um único exemplo VM usando armazenamento premium.<br/><br/>99,99% usando Zonas de Disponibilidade com múltiplas instâncias em conjuntos de disponibilidade múltiplas.<br/><br/>Consulte o [SLA das Máquinas Virtuais.](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) |
| Patching do sistema operativo        | Automático  | Gerido por clientes |
| Remendação MariaDB     | Automático  | Gerido por clientes |
| Elevada disponibilidade | O modelo de alta disponibilidade (HA) baseia-se em mecanismos incorporados de failover para quando ocorre uma interrupção do nível do nó. Nesses casos, o serviço cria automaticamente uma nova instância e anexa o armazenamento a este caso. | Clientes arquitetos, implementar, testar e manter alta disponibilidade. As capacidades podem incluir o agrupamento de falhas sempre ligado, a replicação do grupo sempre ligado, o envio de registos ou a replicação transacional.|
| Redundância entre zonas | Atualmente não suportado | Os VMs Azure podem ser configurado para funcionar em diferentes zonas de disponibilidade. Para uma solução no local, os clientes devem criar, gerir e manter o seu próprio centro de dados secundário.|
| Cenários híbridos | Com [a replicação de dados,](https://docs.microsoft.com/azure/MariaDB/concepts-data-in-replication)pode sincronizar dados de um servidor MariaDB externo na Base de Dados Azure para o serviço MariaDB. O servidor externo pode estar no local, em máquinas virtuais ou num serviço de base de dados hospedado por outros fornecedores de nuvem.<br/><br/> Com a funcionalidade [de réplica de leitura,](https://docs.microsoft.com/azure/mariadb/concepts-read-replicas) pode replicar dados de uma Base de Dados Azure para o servidor de origem MariaDB até cinco servidores de réplica apenas de leitura. As réplicas estão dentro da mesma região de Azure ou em todas as regiões. As réplicas apenas de leitura são assíncroneamente atualizadas usando a tecnologia de replicação binlog.<br/><br/>A replicação de leitura transversal está atualmente em pré-visualização pública.| Gerido por clientes
| Backup e restauro | Cria automaticamente [cópias de segurança do servidor](https://docs.microsoft.com/azure/MariaDB/concepts-backup#backups) e armazena-as num armazenamento configurado pelo utilizador que seja localmente redundante ou geo-redundante. O serviço leva cópias de segurança completas, diferenciais e de registo de transações | Gerido por clientes |
| Monitorização das operações de base de dados | Oferece aos clientes a capacidade de [definir alertas](https://docs.microsoft.com/azure/MariaDB/concepts-monitoring) sobre a operação da base de dados e agir ao atingir limiares. | Gerido por clientes |
| Proteção Avançada Contra Ameaças | Fornece [proteção avançada de ameaças](https://docs.microsoft.com/azure/MariaDB/howto-database-threat-protection-portal). Esta proteção deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de aceder ou explorar bases de dados.<br/><br/>A Advanced Threat Protection está atualmente em pré-visualização pública.| Os clientes devem construir esta proteção para si mesmos.
| Recuperação após desastre | Armazena cópias de segurança automatizadas em [armazenamento localmente redundante ou geo-redundante](https://docs.microsoft.com/azure/MariaDB/howto-restore-server-portal)configurado pelo utilizador. As cópias de segurança também podem restaurar um servidor a um ponto no tempo. O período de retenção é de 7 a 35 dias. A restauração é realizada utilizando o portal Azure. | Totalmente gerido pelos clientes. As responsabilidades incluem, mas não se limitam a agendamento, testes, arquivamento, armazenamento e retenção. Uma opção adicional é usar um cofre dos Serviços de Recuperação Azure para apoiar os VMs e bases de dados em VMs. Esta opção está em pré-visualização. |
| Recomendações de desempenho | Fornece aos clientes recomendações de [desempenho baseadas](https://techcommunity.microsoft.com/t5/Azure-Database-for-MariaDB/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) em ficheiros de registo de utilização gerados pelo sistema. As recomendações ajudam a otimizar as cargas de trabalho.<br/><br/>As recomendações de desempenho estão atualmente em visualização pública. | Gerido por clientes |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Motivações empresariais para escolher PaaS ou IaaS

Existem vários fatores que podem influenciar a sua decisão de escolher PaaS ou IaaS para hospedar as suas bases de dados MariaDB.

### <a name="cost"></a>Cost

O financiamento limitado é muitas vezes a principal consideração que determina a melhor solução para hospedar as suas bases de dados. Isto é verdade quer seja uma startup com pouco dinheiro ou uma equipa numa empresa estabelecida que opera sob restrições orçamentais apertadas. Esta secção descreve os fundamentos de faturação e licenciamento em Azure, uma vez que se aplicam à Base de Dados Azure para MariaDB e MariaDB em VMs Azure.

#### <a name="billing"></a>Faturação

A Azure Database for MariaDB está atualmente disponível como um serviço em vários níveis com diferentes preços de recursos. Todos os recursos são cobrados de hora a hora a uma taxa fixa. Para obter as informações mais recentes sobre os níveis de serviço, tamanhos de cálculo e valores de armazenamento atualmente suportados, consulte [o modelo de compra baseado em vCore.](https://docs.microsoft.com/azure/MariaDB/concepts-pricing-tiers) Pode ajustar dinamicamente os níveis de serviço e os tamanhos de cálculo para corresponder às variadas necessidades de produção da sua aplicação. Você é cobrado para o tráfego de Internet de saída a [taxas regulares de transferência de dados.](https://azure.microsoft.com/pricing/details/data-transfers/)

Com a Azure Database para MariaDB, a Microsoft configura automaticamente, patches e atualiza o software da base de dados. Estas ações automatizadas reduzem os custos da sua administração. Além disso, a Base de Dados Azure para MariaDB tem capacidades [de backup incorporadas.](https://docs.microsoft.com/azure/MariaDB/concepts-backup) Estas capacidades ajudam-no a obter poupanças significativas de custos, especialmente quando se tem um grande número de bases de dados. Em contraste, com o MariaDB em VMs Azure pode escolher e executar qualquer versão MariaDB. Independentemente da versão MariaDB que utilize, paga-se o VM provisitado e os custos para o tipo específico de licença MariaDB utilizado.

A Azure Database for MariaDB fornece alta disponibilidade incorporada para qualquer tipo de interrupção de nível de nó, mantendo ainda a garantia de 99,99% SLA para o serviço. No entanto, para a alta disponibilidade da base de dados dentro de VMs, os clientes devem utilizar as opções de alta disponibilidade, como [a replicação MariaDB](https://mariadb.com/kb/en/library/setting-up-replication/) que estão disponíveis numa base de dados MariaDB. A utilização de uma opção de alta disponibilidade suportada não fornece um SLA adicional. Mas permite-lhe alcançar uma disponibilidade de base de dados superior a 99,99% a custos adicionais e despesas administrativas.

Para obter mais informações sobre preços, consulte os seguintes artigos:
* [Base de Dados Azure para preços MariaDB](https://azure.microsoft.com/pricing/details/MariaDB/)
* [Preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administração

Para muitas empresas, a decisão de transitar para um serviço de nuvem tem tanto a ver com descarregar a complexidade da administração como com o custo. Com IaaS e PaaS, Microsoft:

- Administra a infraestrutura subjacente.
- Replica automaticamente todos os dados para fornecer recuperação de desastres.
- Configura e atualiza o software da base de dados.
- Gere o equilíbrio de carga.
- Faz falha transparente se houver uma falha no servidor.

A lista que se segue descreve considerações administrativas para cada opção:

* Com a Azure Database for MariaDB, pode continuar a administrar a sua base de dados. Mas já não precisas de gerir o motor da base de dados, o sistema operativo ou o hardware. Exemplos de itens que pode continuar a administrar incluem:

  - Bases de Dados
  - Iniciar sessão
  - Afinação de índices
  - Sintonização de consulta
  - Auditoria
  - Segurança

  Além disso, configurar alta disponibilidade para outro centro de dados requer uma configuração ou administração mínimas.

* Com o MariaDB em VMs Azure, tem controlo total sobre o sistema operativo e a configuração de instância do servidor MariaDB. Com um VM, você decide quando atualizar ou atualizar o sistema operativo e o software de base de dados. Também decide quando instalar qualquer software adicional, como uma aplicação antivírus. Algumas funcionalidades automatizadas são fornecidas para simplificar muito o patching, a cópia de segurança e a alta disponibilidade. Pode controlar o tamanho do VM, o número de discos e as suas configurações de armazenamento. Para obter mais informações, consulte [os tamanhos de serviço de máquina virtual e cloud para Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)

### <a name="time-to-move-to-azure"></a>Hora de se mudar para Azure.

* A Azure Database for MariaDB é a solução certa para aplicações desenhadas na nuvem quando a produtividade do desenvolvedor e o tempo rápido para o mercado de novas soluções são críticos. Com uma funcionalidade programática que é como a DBA, o serviço é adequado para arquitetos e desenvolvedores em nuvem porque reduz a necessidade de gestão do sistema operativo subjacente e da base de dados.

* Quando pretende evitar o tempo e a despesa de adquirir novo hardware no local, o MariaDB em VMs Azure é a solução certa para aplicações que requerem uma base de dados MariaDB ou acesso a funcionalidades MariaDB no Windows ou Linux. Esta solução também é adequada para a migração de aplicações e bases de dados existentes no local para Azure intactas, para os casos em que a Base de Dados Azure para MariaDB é um mau ajuste.

  Como não há necessidade de alterar as camadas de apresentação, aplicação e dados, poupa tempo e orçamento na rearquecação da sua solução existente. Em vez disso, pode focar-se em migrar todas as suas soluções para a Azure e abordar algumas otimizações de desempenho que a plataforma Azure poderá necessitar.

## <a name="next-steps"></a>Passos seguintes

* Consulte [a Base de Dados Azure para os preços mariadb](https://azure.microsoft.com/pricing/details/MariaDB/).
* Começa por [criar o teu primeiro servidor.](https://docs.microsoft.com/azure/MariaDB/quickstart-create-MariaDB-server-database-using-azure-portal)
