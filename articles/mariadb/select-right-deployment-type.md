---
title: Selecionando o tipo de implantação certo para o banco de dados do Azure para MariaDB
description: Este artigo descreve quais fatores devem ser considerados antes de implantar o banco de dados do Azure para MariaDB como IaaS (infraestrutura como serviço) ou PaaS (plataforma como um serviço).
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: 59064c71d19f81a5edc322bc4c8158773a104b5d
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151993"
---
# <a name="choose-the-right-mariadb-server-option-in-azure"></a>Escolha a opção de servidor MariaDB correta no Azure

Com o Azure, suas cargas de trabalho do servidor MariaDB podem ser executadas em uma infraestrutura de máquina virtual hospedada como um serviço (IaaS) ou como uma plataforma hospedada como um serviço (PaaS). O PaaS tem várias opções de implantação e há camadas de serviço dentro de cada opção de implantação. Ao escolher entre IaaS e PaaS, você deve decidir se deseja gerenciar o banco de dados, aplicar patches e fazer backups ou se deseja delegar essas operações ao Azure.

Ao tomar sua decisão, considere as duas opções a seguir:

- **Banco de dados do Azure para MariaDB:** Essa opção é um mecanismo de banco de dados MariaDB totalmente gerenciado com base na versão estável do MariaDB Community Edition. Esse DBaaS (banco de dados relacional como serviço), hospedado na plataforma de nuvem do Azure, se enquadra na categoria do setor de PaaS.

  Com uma instância gerenciada do MariaDB no Azure, você pode usar recursos internos que, caso contrário, exigem uma configuração extensiva quando o servidor MariaDB está no local ou em uma VM do Azure.

  Ao usar o MariaDB como um serviço, você paga conforme o uso das opções para escalar verticalmente ou escalar horizontalmente para maior controle sem interrupção. E, diferentemente do MariaDB Server autônomo, o banco de dados do Azure para MariaDB tem recursos adicionais, como alta disponibilidade, inteligência e gerenciamento internos.

- **MariaDB em VMs do Azure:** Essa opção se encaixa na categoria do setor de IaaS. Com esse serviço, você pode executar o MariaDB Server dentro de uma máquina virtual totalmente gerenciada na plataforma de nuvem do Azure. Todas as versões e edições recentes do MariaDB podem ser instaladas em uma máquina virtual IaaS.

  Na diferença mais significativa do banco de dados do Azure para MariaDB, o MariaDB em VMs do Azure oferece controle sobre o mecanismo de banco de dados. No entanto, esse controle tem o custo de responsabilidade de gerenciar as VMs e muitas tarefas de administração de banco de dados (DBA). Essas tarefas incluem manutenção e aplicação de patches de servidores de banco de dados, recuperação de banco de dados e design de alta disponibilidade.

As principais diferenças entre essas opções são listadas na tabela a seguir:

|            | Azure Database for MariaDB | MariaDB em VMs do Azure    |
|:-------------------|:-----------------------------|:--------------------|
| Contrato de nível de serviço (SLA)                | Oferece SLA de 99,99% de disponibilidade| Até 99,95% de disponibilidade com duas ou mais instâncias no mesmo conjunto de disponibilidade.<br/><br/>99,9% de disponibilidade com uma única VM de instância usando o armazenamento Premium.<br/><br/>99,99% usando Zonas de Disponibilidade com várias instâncias em vários conjuntos de disponibilidade.<br/><br/>Consulte o [SLA de máquinas virtuais](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). |
| Aplicação de patch do sistema operacional        | Automático  | Gerenciado por clientes |
| Aplicação de patch MariaDB     | Automático  | Gerenciado por clientes |
| Elevada disponibilidade | O modelo de alta disponibilidade (HA) é baseado em mecanismos de failover internos para quando ocorre uma interrupção no nível do nó. Nesses casos, o serviço cria automaticamente uma nova instância e anexa o armazenamento a essa instância. | Os clientes arquitetam, implementam, testam e mantêm a alta disponibilidade. Os recursos podem incluir clustering de failover Always on, replicação de grupo Always on, envio de logs ou replicação transacional.|
| Redundância de zona | Atualmente sem suporte | As VMs do Azure podem ser configuradas para serem executadas em diferentes zonas de disponibilidade. Para uma solução local, os clientes devem criar, gerenciar e manter seus próprios data center secundários.|
| Cenários híbridos | Com [replicação de dados](https://docs.microsoft.com/azure/MariaDB/concepts-data-in-replication), você pode sincronizar dados de um servidor MariaDB externo no banco de dados do Azure para o serviço MariaDB. O servidor externo pode ser local, em máquinas virtuais ou em um serviço de banco de dados hospedado por outros provedores de nuvem.<br/><br/> Com o recurso de [réplica de leitura](https://docs.microsoft.com/azure/mariadb/concepts-read-replicas) , você pode replicar dados de um servidor mestre do Azure para MariaDB para até cinco servidores de réplica somente leitura. As réplicas estão dentro da mesma região do Azure ou entre regiões. Réplicas somente leitura são atualizadas de forma assíncrona usando a tecnologia de replicação binlog.<br/><br/>A replicação de leitura entre regiões está atualmente em visualização pública.| Gerenciado por clientes
| Backup e restauração | O cria automaticamente [backups de servidor](https://docs.microsoft.com/azure/MariaDB/concepts-backup#backups) e os armazena em um armazenamento configurado pelo usuário que seja localmente redundante ou com redundância geográfica. O serviço usa backups completos, diferenciais e de log de transações | Gerenciado por clientes |
| Operações de banco de dados de monitoramento | Oferece aos clientes a capacidade de [definir alertas](https://docs.microsoft.com/azure/MariaDB/concepts-monitoring) na operação de banco de dados e agir sobre o alcance de limites. | Gerenciado por clientes |
| Proteção Avançada Contra Ameaças | Fornece [proteção avançada contra ameaças](https://docs.microsoft.com/azure/MariaDB/howto-database-threat-protection-portal). Essa proteção detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.<br/><br/>A proteção avançada contra ameaças está atualmente em visualização pública.| Os clientes devem criar essa proteção para si mesmos.
| Recuperação após desastre | Armazena backups automatizados em armazenamento com redundância [local ou](https://docs.microsoft.com/azure/MariaDB/howto-restore-server-portal)com redundância geográfica configurado pelo usuário. Os backups também podem restaurar um servidor para um ponto no tempo. O período de retenção é de 7 a 35 dias. A restauração é realizada usando o portal do Azure. | Totalmente gerenciado pelos clientes. As responsabilidades incluem, mas não se limitam a agendamento, teste, arquivamento, armazenamento e retenção. Uma opção adicional é usar um cofre dos serviços de recuperação do Azure para fazer backup de VMs e bancos de dados do Azure em VMs. Esta opção está em visualização. |
| Recomendações de desempenho | Fornece aos clientes [recomendações de desempenho](https://techcommunity.microsoft.com/t5/Azure-Database-for-MariaDB/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) com base em arquivos de log de uso gerados pelo sistema. As recomendações ajudam a otimizar as cargas de trabalho.<br/><br/>As recomendações de desempenho estão atualmente em visualização pública. | Gerenciado por clientes |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Motivações de negócios para escolher PaaS ou IaaS

Há vários fatores que podem influenciar sua decisão de escolher PaaS ou IaaS para hospedar seus bancos de dados do MariaDB.

### <a name="cost"></a>Custo

O financiamento limitado geralmente é a principal consideração que determina a melhor solução para hospedar seus bancos de dados. Isso é verdade se você for uma inicialização com pouco dinheiro ou uma equipe em uma empresa estabelecida que opere sob restrições de orçamento rígidas. Esta seção descreve as noções básicas de cobrança e licenciamento no Azure, pois elas se aplicam ao banco de dados do Azure para MariaDB e MariaDB em VMs do Azure.

#### <a name="billing"></a>Faturação

O banco de dados do Azure para MariaDB está disponível atualmente como um serviço em várias camadas com preços diferentes para os recursos. Todos os recursos são cobrados por hora a uma taxa fixa. Para obter as informações mais recentes sobre as camadas de serviço, tamanhos de computação e valores de armazenamento com suporte no momento, consulte [modelo de compra baseado em vCore](https://docs.microsoft.com/azure/MariaDB/concepts-pricing-tiers). Você pode ajustar dinamicamente as camadas de serviço e os tamanhos de computação para corresponder às necessidades de taxa de transferência variadas do seu aplicativo. Você será cobrado pelo tráfego de Internet de saída em [tarifas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/)regulares.

Com o banco de dados do Azure para MariaDB, a Microsoft configura, corrige e atualiza automaticamente o software de banco de dados. Essas ações automatizadas reduzem os custos de administração. Além disso, o banco de dados do Azure para MariaDB tem recursos de [backup internos](https://docs.microsoft.com/azure/MariaDB/concepts-backup) . Esses recursos ajudam a alcançar uma economia de custos significativa, especialmente quando você tem um grande número de bancos de dados. Por outro lado, com o MariaDB nas VMs do Azure, você pode escolher e executar qualquer versão do MariaDB. Não importa qual versão do MariaDB você usa, você paga pela VM provisionada e os custos para o tipo de licença MariaDB específico usado.

O banco de dados do Azure para MariaDB fornece alta disponibilidade interna para qualquer tipo de interrupção no nível de nó e, ao mesmo tempo, mantém a garantia de 99,99% de SLA para o serviço. No entanto, para alta disponibilidade de banco de dados nas VMs, os clientes devem usar as opções de alta disponibilidade como [replicação MariaDB](https://mariadb.com/kb/en/library/setting-up-replication/) que estão disponíveis em um banco de dados MariaDB. O uso de uma opção de alta disponibilidade com suporte não fornece um SLA adicional. Mas ele permite que você obtenha mais de 99,99% de disponibilidade de banco de dados com custo adicional e sobrecarga administrativa.

Para obter mais informações sobre preços, consulte os seguintes artigos:
* [Preço do banco de dados do Azure para MariaDB](https://azure.microsoft.com/pricing/details/MariaDB/)
* [Preços da máquina virtual](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administração

Para muitas empresas, a decisão de fazer a transição para um serviço de nuvem é o quanto ao descarregamento da complexidade da administração, pois trata-se de um custo. Com IaaS e PaaS, Microsoft:

- Administra a infraestrutura subjacente.
- Replica automaticamente todos os dados para fornecer recuperação de desastre.
- Configura e atualiza o software de banco de dados.
- Gerencia o balanceamento de carga.
- O failover transparente se houver uma falha no servidor.

A lista a seguir descreve as considerações administrativas para cada opção:

* Com o banco de dados do Azure para MariaDB, você pode continuar a administrar seu banco de dados. Mas você não precisa mais gerenciar o mecanismo de banco de dados, o sistema operacional ou o hardware. Exemplos de itens que você pode continuar a administrar incluem:

  - Bases de Dados
  - Iniciar sessão
  - Otimização de índices
  - Ajuste de consulta
  - Auditoria
  - Segurança

  Além disso, a configuração da alta disponibilidade para outra data center exige um mínimo de sem configuração ou administração.

* Com o MariaDB em VMs do Azure, você tem controle total sobre o sistema operacional e a configuração da instância do servidor MariaDB. Com uma VM, você decide quando atualizar ou atualizar o sistema operacional e o software de banco de dados. Você também decide quando instalar qualquer software adicional, como um aplicativo antivírus. Alguns recursos automatizados são fornecidos para simplificar bastante a aplicação de patches, backup e alta disponibilidade. Você pode controlar o tamanho da VM, o número de discos e suas configurações de armazenamento. Para obter mais informações, consulte [tamanhos de máquina virtual e serviço de nuvem para o Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

### <a name="time-to-move-to-azure"></a>Tempo para mover para o Azure

* O banco de dados do Azure para MariaDB é a solução certa para aplicativos projetados para a nuvem quando a produtividade do desenvolvedor e o rápido tempo de colocação no mercado para novas soluções são essenciais. Com a funcionalidade programática que é como o DBA, o serviço é adequado para desenvolvedores e arquitetos de nuvem, pois reduz a necessidade de gerenciar o sistema operacional subjacente e o banco de dados.

* Quando você quiser evitar o tempo e as despesas de adquirir um novo hardware local, o MariaDB nas VMs do Azure é a solução certa para aplicativos que exigem um banco de dados MariaDB ou acesso aos recursos do MariaDB no Windows ou no Linux. Essa solução também é adequada para migrar os aplicativos locais e bancos de dados existentes para o Azure intactos, para casos em que o banco de dados do Azure para MariaDB é uma boa opção.

  Como não há necessidade de alterar as camadas de apresentação, aplicativo e dados, você economiza tempo e orçamento na rearquitetura de sua solução existente. Em vez disso, você pode se concentrar em migrar todas as suas soluções para o Azure e abordar algumas otimizações de desempenho que a plataforma do Azure pode exigir.

## <a name="next-steps"></a>Passos seguintes

* Consulte o [banco de dados do Azure para obter o preço do MariaDB](https://azure.microsoft.com/pricing/details/MariaDB/).
* Comece por [criar o seu primeiro servidor](https://review.docs.microsoft.com/azure/MariaDB/quickstart-create-MariaDB-server-database-using-azure-portal).
