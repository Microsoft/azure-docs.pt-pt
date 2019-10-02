---
title: Selecionando o tipo de implantação certo para o banco de dados do Azure para MariaDB
description: Este artigo descreve quais são as considerações que precisam ser feitas antes de prosseguir com a infraestrutura como um serviço (IaaS) ou plataforma como serviço (PaaS) para o banco de dados do Azure para MariaDB.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: fa87748719e2d3775034e5a2850281cf8f1a0e8a
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817439"
---
# <a name="choose-the-right-mariadb-server-option-in-azure"></a>Escolha a opção de servidor MariaDB correta no Azure

Com o Azure, suas cargas de trabalho do servidor MariaDB podem ser executadas em uma VM de infraestrutura hospedada (IaaS) ou como um serviço hospedado (PaaS). O PaaS tem várias opções de implantação e há camadas de serviço dentro de cada opção de implantação. Ao decidir entre PaaS ou IaaS, você precisa determinar se deseja gerenciar seu banco de dados, aplicar patches e fazer backups ou se deseja delegar essas operações ao Azure.</br>

Com base em sua resposta para essa pergunta, considere as seguintes opções: <br/>

O **banco de dados do Azure para MariaDB** é um mecanismo de banco de dados MariaDB totalmente gerenciado com base na versão estável da Community Edition. Esse DBaaS (banco de dados como serviço relacional), hospedado na nuvem do Azure, se encaixa na categoria do setor de PaaS (plataforma como serviço). Com uma instância gerenciada do MariaDB no Azure, você pode usar recursos internos e funcionalidades que exigem uma configuração extensiva ao usar o servidor MariaDB (local ou em uma máquina virtual do Azure). Ao usar o MariaDB como um serviço, você paga conforme o uso com opções para escalar verticalmente ou horizontalmente para maior capacidade, sem interrupção. Além disso, ao contrário do servidor MariaDB autônomo, o banco de dados do Azure para MariaDB tem recursos adicionais, como alta disponibilidade, inteligência e gerenciamento internos. <br/><br/>
O **MariaDB na VM do Azure** se encaixa na categoria de infraestrutura como serviço (IaaS) do setor e permite executar o MariaDB Server dentro de uma máquina virtual totalmente gerenciada na nuvem do Azure. Todas as versões e edições recentes do MariaDB podem ser instaladas em uma máquina virtual IaaS. A diferença mais significativa do banco de dados do Azure para MariaDB é que o MariaDB nas VMs do Azure permite o controle sobre o mecanismo de banco de dados. No entanto, esse controle acompanha o custo de responsabilidade adicional de gerenciar as máquinas virtuais e várias tarefas DBA, como manutenção/aplicação de patch do servidor de banco de dados, recuperação e design de alta disponibilidade, etc.

As principais diferenças entre essas opções são listadas na tabela a seguir:

|            | **Azure Database for MariaDB** | **MariaDB em VMs do Azure**    |
|:-------------------|:-----------------------------|:--------------------|
| **SLA**                | Oferece SLA de 99,99% de disponibilidade| Até 99,95% de disponibilidade com 2 ou mais instâncias no mesmo conjunto de disponibilidade. <br/>VM de instância única de 99,9% usando o armazenamento Premium <br/> 99,99% com zona de disponibilidade com 2 ou mais instâncias em 2 ou mais conjunto de disponibilidade.<br/> Observação- [SLA de máquina virtual](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) |
| **Aplicação de patch do so**        | Automático  | Gerenciado por clientes |
|**Aplicação de patch MariaDB**     | Automático  | Gerenciado por clientes |
| **Elevada disponibilidade** | O modelo de alta disponibilidade (HA) é baseado em mecanismos de failover internos quando ocorre uma interrupção no nível do nó. Nesses casos, o serviço cria automaticamente a instância e anexa o armazenamento a essa nova instância. | A alta disponibilidade é arquitetada, implementada, testada e mantida pelo cliente. Isso pode incluir Always on (clustering de failover ou replicação de grupo), envio de logs e replicação transacional, dependendo da versão do mecanismo de MariaDB em uso.|
| **Redundância de zona** | Atualmente sem suporte. | As VMs do Azure podem ser configuradas para serem executadas em diferentes zonas de disponibilidade. Para uma solução local, é esperado que os clientes criem, gerenciem e mantenham seus próprios data center secundários.|
| **Cenários Híbridos** | [Replicação de dados](https://docs.microsoft.com/azure/MariaDB/concepts-data-in-replication) permite sincronizar dados de um servidor MariaDB externo no banco de dados do Azure para o serviço MariaDB. O servidor externo pode ser local, em máquinas virtuais ou em um serviço de banco de dados hospedado por outros provedores de nuvem.  <br/> <br/> O recurso de [réplica de leitura](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) permite replicar dados de um MariaDB Server (Mestre) para até cinco servidores somente leitura (réplicas) na mesma região do Azure ou entre regiões. Réplicas somente leitura são atualizadas de forma assíncrona usando a tecnologia de replicação binlog.   <br/> <br/> Observação: a replicação de leitura entre regiões está atualmente em visualização pública.| Gerenciado por clientes <br/>
| **Backup e restauro** | O cria automaticamente [backups de servidor](https://docs.microsoft.com/azure/MariaDB/concepts-backup#backups) e os armazena em um armazenamento com redundância geográfica ou local configurado pelo usuário. O serviço usa backups completos, diferenciais e de log de transações | Gerenciado por clientes |
| **Operações de banco de dados de monitoramento** | Permite que os clientes [definam alertas](https://docs.microsoft.com/azure/MariaDB/concepts-monitoring) na operação de banco de dados e atuem nos limites de alcance. | Gerenciado por clientes |
| **Proteção de thread avançada** | Fornece [proteção avançada contra ameaças](https://docs.microsoft.com/azure/MariaDB/howto-database-threat-protection-portal) que detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados. <br/> <br/> Observação-a proteção de thread avançada está atualmente em visualização pública.| Os clientes devem criar para si mesmos.
| **Backups (recuperação de desastres)** | Armazena backups automatizados no usuário configurado em [armazenamento localmente redundante ou com redundância geográfica](https://docs.microsoft.com/azure/MariaDB/howto-restore-server-portal). Os backups também podem ser usados para restaurar um servidor para um ponto no tempo. O período de retenção pode ser definido de 7-35 dias. A restauração pode ser realizada usando o portal do Azure. <br/> | Totalmente gerenciado pelo cliente, incluindo, mas não se limitando a agendamento, teste, arquivamento, armazenamento e retenção. Uma opção adicional é usar o cofre dos serviços de recuperação do Azure para fazer backup de VMs e bancos de dados do Azure em VMs (em versão prévia). |
| **Recomendação de desempenho** | Fornece aos clientes recomendações de [desempenho](https://techcommunity.microsoft.com/t5/Azure-Database-for-MariaDB/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) proativo com base na telemetria de uso para ajudar a otimizar as cargas de trabalho. <br/> <br/> Observação-a recomendação de desempenho está atualmente em visualização pública. | Gerenciado por clientes |


## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Motivações de negócios para escolher PaaS ou IaaS

Há vários fatores que podem influenciar sua decisão de escolher PaaS ou IaaS para hospedar seus bancos de dados do MariaDB:

### <a name="cost"></a>Custo

Quer você seja um strapped de inicialização para o dinheiro ou uma equipe em uma empresa estabelecida que opere sob restrições de orçamento rígidos, o financiamento limitado geralmente é a principal consideração ao determinar a melhor solução para hospedar seus bancos de dados. Esta seção descreve as noções básicas de cobrança e licenciamento no Azure com relação ao banco de dados do Azure para MariaDB e MariaDB em VMs do Azure:

#### <a name="billing"></a>Faturação

Atualmente, o banco de dados do Azure para MariaDB está disponível como um serviço em várias camadas com preços diferentes para recursos, todos os quais são cobrados por hora a uma taxa fixa. Para obter as informações mais recentes sobre as atuais camadas de serviço com suporte, tamanhos de computação e valores de armazenamento, consulte [modelo de compra baseado em vCore](https://docs.microsoft.com/azure/MariaDB/concepts-pricing-tiers). Você pode ajustar dinamicamente as camadas de serviço e os tamanhos de computação para corresponder às necessidades de taxa de transferência variadas do seu aplicativo. Você será cobrado pelo tráfego de Internet de saída em [tarifas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/)regulares.

Com o banco de dados do Azure para MariaDB, o software de banco de dados é automaticamente configurado, corrigido e atualizado pela Microsoft, o que reduz os custos de administração. Além disso, as capacidades de [cópia de segurança incorporada](https://docs.microsoft.com/azure/MariaDB/concepts-backup) permitem-lhe obter uma redução significativa de custos, sobretudo quando tem um número elevado de bases de dados. Com o MariaDB em VMs do Azure, você pode escolher e executar qualquer uma das versões do MariaDB. Independentemente da versão do MariaDB que você usa, você paga pela VM provisionada e os custos para o tipo de licença específico usado para MariaDB.

O banco de dados do Azure para MariaDB fornece alta disponibilidade interna para qualquer tipo de interrupção de nível de nó e ainda mantém o SLA de 99,99% para o serviço. No entanto, para HA (alta disponibilidade) de banco de dados em VMs, o cliente deve passar pelas opções de alta disponibilidade disponíveis no banco de dados MariaDB, como [replicação MariaDB](https://mariadb.com/kb/en/library/setting-up-replication/). O uso de uma opção de alta disponibilidade com suporte não fornece um SLA adicional, mas permite que você > alcance a disponibilidade de banco de dados de 99,99% a um custo adicional e sobrecarga administrativa.

Para obter mais informações sobre preços, consulte os seguintes recursos:
* [Preço do banco de dados do Azure para MariaDB](https://azure.microsoft.com/pricing/details/MariaDB/)
* [Preços da máquina virtual](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Calculadora de Preços do Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administração

Para muitas empresas, a decisão de fazer a transição para um serviço de nuvem é o quanto ao descarregamento da complexidade da administração. Com o IaaS e o PaaS, a Microsoft administra a infraestrutura subjacente e replica automaticamente todos os dados para fornecer recuperação de desastre, configura e atualiza o software de dados, gerencia o balanceamento de carga e faz o failover transparente se houver um falha do servidor.

* **Com o banco de dados do Azure para MariaDB**, você pode continuar a administrar seu banco de dados, mas não precisa mais gerenciar o mecanismo de banco de dados, o sistema operacional ou o hardware. Exemplos de itens que pode continuar a administrar incluem bases de dados e inícios de sessão, otimização de índices e consultas, e auditoria e segurança. Além disso, a configuração de alta disponibilidade para outra data center requer mínima ou nenhuma configuração ou administração.<br/><br/>
* **Com o MariaDB em VMs do Azure**, você tem controle total sobre o sistema operacional e a configuração da instância do servidor MariaDB. Com uma VM, você decide quando atualizar/atualizar o sistema operacional e o software de banco de dados e quando instalar qualquer software adicional, como um aplicativo antivírus. Algumas funcionalidades automatizadas são fornecidas para simplificar significativamente a aplicação de patches, a cópia de segurança e a elevada disponibilidade. Além disso, pode controlar o tamanho da VM, o número de discos e as respetivas configurações de armazenamento. Para obter informações, consulte tamanhos de máquina virtual e serviço de nuvem para o Azure.

### <a name="time-to-move-to-azure-br"></a>Tempo para mover para o Azure <br/>
* O **banco de dados do Azure para MariaDB** é a solução certa para aplicativos projetados para a nuvem quando a produtividade do desenvolvedor e o tempo de comercialização rápido para novas soluções são essenciais. Com a funcionalidade do tipo DBA programático, o serviço é perfeito para desenvolvedores e arquitetos de nuvem, pois reduz a necessidade de gerenciar o sistema operacional e o banco de dados subjacentes.<br/><br/>
* O **MariaDB nas VMs do Azure** é perfeito para aplicativos novos ou existentes que exigem o banco de dados MariaDB ou o acesso a recursos no banco de dados MariaDB no Windows/Linux, e você deseja evitar o tempo e as despesas de adquirir um novo hardware local. Essa opção também é um bom ajuste para migrar aplicativos locais e bancos de dados existentes para o Azure no estado em que se encontram – para casos em que um banco de dados do Azure para instância MariaDB não é uma boa opção. Como não há necessidade de alterar as camadas de apresentação, aplicativo e dados, você economiza tempo e orçamento na nova arquitetura da solução existente. Em vez disso, você pode se concentrar na migração de todas as suas soluções para o Azure e no endereçamento de algumas otimizações de desempenho que podem ser exigidas pela plataforma Azure.

## <a name="next-steps"></a>Próximos Passos

* Consulte os [preços do banco de dados do Azure para MariaDB](https://azure.microsoft.com/pricing/details/MariaDB/)
* Comece por [criar o seu primeiro servidor](https://review.docs.microsoft.com/azure/MariaDB/quickstart-create-MariaDB-server-database-using-azure-portal).

