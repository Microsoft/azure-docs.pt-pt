---
title: O que é Azure SQL?
titleSuffix: " "
description: 'Conheça as diferentes opções dentro da família de serviços Azure SQL: Base de Dados Azure SQL, Instância Gerida Azure SQL e Servidor SQL num VM Azure.'
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: conceptual
keywords: Nuvem de servidor SQL, Servidor SQL na nuvem, base de dados PaaS, cloud SQL Server, DBaaS, IaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/27/2020
ms.openlocfilehash: 68f6977059af84fc7fa98fe5817f9f5b4fbdcf90
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045418"
---
# <a name="what-is-azure-sql"></a>O que é Azure SQL? 
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

O Azure SQL é uma família de produtos de base de dados SQL Server geridos, seguros e inteligentes.

- **Base de Dados Azure SQL**: Suporte aplicações modernas em nuvem num serviço de base de dados inteligente e gerido, que inclui computação sem servidores. 
- **Instância Gerida Azure SQL**: Modernizar as suas aplicações existentes do SQL Server em escala com uma instância inteligente totalmente gerida como um serviço, com quase 100% de paridade de funcionalidades para o SQL Server no local a que está habituado. O melhor para a maioria das migrações para a nuvem.
- **SQL Server em VMs Azure**: Levante e altere as suas cargas de trabalho do Servidor SQL com facilidade e mantenha 100% compatibilidade do Servidor SQL e acesso ao nível do sistema operativo. 
 
O Azure SQL é construído sobre o conhecido motor SQL Server, para que possa migrar aplicações com facilidade e continuar a usar as ferramentas, idiomas e recursos que está familiarizado. As suas habilidades e experiência transferem-se para a nuvem, para que possa fazer ainda mais com o que já tem. 

Saiba como cada produto se encaixa na plataforma de dados Azure SQL da Microsoft para corresponder à opção certa para os seus requisitos de negócio. Quer dê prioridade à poupança de custos ou à administração mínima, este artigo pode ajudá-lo a decidir qual a abordagem que lhe dá contra os requisitos de negócio que mais gosta.


## <a name="overview"></a>Descrição geral

No mundo atual, impulsionando a transformação digital depende cada vez mais da nossa capacidade de gerir quantidades massivas de dados e aproveitar o seu potencial. Mas as propriedades de dados de hoje são cada vez mais complexas, com dados alojados no local, na nuvem, ou na borda da rede. Os desenvolvedores que estão a construir aplicações inteligentes e imersivas podem encontrar-se limitados por limitações que podem, em última análise, impactar a sua experiência. As limitações decorrentes de plataformas incompatíveis, segurança inadequada de dados, recursos insuficientes e barreiras de desempenho de preços criam complexidade que pode inibir a modernização e desenvolvimento de apps. 

Um dos primeiros aspetos a compreender em qualquer debate do Azure versus bases de dados do SQL Server no local é que pode utilizar todas. A plataforma de dados da Microsoft aproveita a tecnologia SQL Server e disponibiliza-a através de máquinas físicas no local, ambientes privados em nuvem, ambientes privados de nuvem e nuvem pública. 


### <a name="fully-managed-and-always-up-to-date"></a>Totalmente gerido e sempre atualizado 

Passe mais tempo a inovar e menos tempo a remendar, a atualizar e a fazer backup das suas bases de dados. O Azure é a única nuvem com O QL evergreen que aplica automaticamente as últimas atualizações e patches para que as suas bases de dados estejam sempre atualizadas — eliminando o aborrecimento final do suporte. Mesmo tarefas complexas como afinação de desempenho, alta disponibilidade, recuperação de desastres e backups são automatizados, libertando-o para se concentrar em aplicações.  

### <a name="protect-your-data-with-built-in-intelligent-security"></a>Proteja os seus dados com segurança inteligente incorporada 

O Azure monitoriza constantemente os seus dados para ameaças. Com o Azure SQL, pode:

- Remediar potenciais ameaças em tempo real com alertas inteligentes de [deteção de ameaças avançadas](https://docs.microsoft.com/azure/security/fundamentals/threat-detection#advanced-threat-detection-features-other-azure-services) e alertas de avaliação de vulnerabilidade proactiva. 
- Obtenha uma proteção líder do setor, multi-camadas, com [controlos de segurança incorporados,](https://azure.microsoft.com/overview/security/) incluindo T-SQL, autenticação, networking e gestão chave. 
- Aproveite a cobertura de [conformidade](https://azure.microsoft.com/overview/trusted-cloud/compliance/) mais abrangente de qualquer serviço de base de dados em nuvem. 


### <a name="business-motivations"></a>Motivações empresariais

Existem vários fatores que podem influenciar a sua decisão de escolher entre as diferentes ofertas de dados:

- [Custo](#cost) - Tanto a opção PaaS como a IaaS incluem o preço base que cobre infraestruturas subjacentes e licenciamento. No entanto, com a opção IaaS, você precisa investir tempo e recursos adicionais para gerir a sua base de dados, enquanto no PaaS obtém estas funcionalidades de administração incluídas no preço. O IaaS permite-lhe desligar os recursos enquanto não os está a usar para diminuir o custo, enquanto o PaaS está sempre a funcionar a menos que deixe cair e recrie os seus recursos quando são necessários.
- [Administração](#administration) - Opções PaaS reduzem o tempo que precisa para investir para administrar a base de dados. No entanto, também limita o leque de tarefas e scripts de administração personalizados que pode executar ou executar. Por exemplo, o CLR não é suportado com bases de dados únicas ou reunidas, mas é suportado para uma Instância Gerida SQL. Além disso, nenhuma opção de implantação no PaaS suporta a utilização de bandeiras de vestígios.
- [Acordo de Nível de Serviço](#service-level-agreement-sla) - Tanto o IaaS como o PaaS fornecem SLA de alta qualidade da indústria. A opção PaaS garante 99,99% SLA, enquanto o IaaS garante 99,95% SLA para infraestruturas, o que significa que precisa implementar mecanismos adicionais para garantir a disponibilidade das suas bases de dados. Pode obter 99,99% SLA criando uma máquina virtual SQL adicional, e implementando a solução de alta disponibilidade do grupo SQL Server Always On. 
- Hora de [se mudar para o Azure](#market) - SQL Server no Azure VM é a combinação exata do seu ambiente, por isso a migração das instalações para o Azure VM não é diferente de mover as bases de dados de um servidor no local para outro. A Instância Gerida sQL também permite uma migração fácil; no entanto, pode haver algumas mudanças que precisa de aplicar antes da sua migração. 


## <a name="service-comparison"></a>Comparação de serviços

   ![Opções do SQL Server na nuvem: SQL server em IaaS ou base de dados SQL SaaS na nuvem.](./media/azure-sql-iaas-vs-paas-what-is-overview/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Como se pode ver no diagrama, cada oferta de serviço pode ser caracterizada pelo nível de administração que tem sobre a infraestrutura, e pelo grau de eficiência de custos.

Em Azure, pode ter as suas cargas de trabalho do SQL Server a funcionar como um serviço hospedado[(PaaS),](https://azure.microsoft.com/overview/what-is-paas/)ou uma infraestrutura hospedada[(IaaS).](https://azure.microsoft.com/overview/what-is-iaas/) Dentro do PaaS, você tem múltiplas opções de produto, e níveis de serviço dentro de cada opção. A questão-chave que precisa de fazer ao decidir entre paaS ou IaaS é se quer gerir a sua base de dados, aplicar patches e receber cópias de segurança, ou quer delegar estas operações no Azure?

### <a name="azure-sql-database"></a>Base de Dados SQL do Azure

A Base de [**Dados Azure SQL**](database/sql-database-paas-overview.md) é uma base de dados relacional (DBaaS) hospedada em Azure que se enquadra na categoria industrial de *Plataforma-as-a-Service (PaaS)*. O melhor para aplicações modernas em nuvem que querem usar as mais recentes funcionalidades estáveis do SQL Server e têm restrições de tempo no desenvolvimento e marketing. Um motor de base de dados SQL totalmente gerido, baseado na mais recente estabilidade Enterprise Edition do SQL Server. A base de dados SQL tem duas opções de implementação baseadas em hardware e software padronizados que são propriedade, hospedada e mantida pela Microsoft. Com o SQL Server, pode utilizar funcionalidades e funcionalidades incorporadas que requerem uma configuração extensiva (quer no local quer numa máquina virtual Azure). Ao utilizar a Base de Dados SQL, paga apenas o que utilizar, dispondo de opções para aumentar verticalmente ou horizontalmente para obter um maior desempenho sem interrupções. A Base de Dados SQL tem algumas funcionalidades adicionais que não estão disponíveis no SQL Server, tais como alta disponibilidade, inteligência e gestão incorporadas.


A Base de Dados Azure SQL oferece as seguintes opções de implementação:
  - Como uma [***única base***](database/single-database-overview.md) de dados com o seu próprio conjunto de recursos geridos através de um servidor [lógico SQL](database/logical-servers.md). Uma única base de dados é semelhante a uma [base de dados contida](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) no Servidor SQL. Esta opção está otimizada para o desenvolvimento de aplicações modernas de novas aplicações nascidas na nuvem. Estão disponíveis opções [de hiperescala](database/service-tier-hyperscale.md) e [servidor.](database/serverless-tier-overview.md)
  - Um [***pool elástico,***](database/elastic-pool-overview.md)que é uma coleção de bases de dados com um conjunto partilhado de recursos geridos através de um [servidor lógico SQL](database/logical-servers.md). As bases de dados individuais podem ser movidas para dentro e para fora de uma piscina elástica. Esta opção está otimizada para o desenvolvimento de aplicações modernas de novas aplicações nascidas na nuvem usando o padrão de aplicação SaaS multi-inquilino. Os pools elásticos fornecem uma solução rentável para gerir o desempenho de várias bases de dados que têm padrões de utilização variáveis.

### <a name="azure-sql-managed-instance"></a>Instância Gerida do Azure SQL

[**O Azure SQL Managed Instance**](managed-instance/sql-managed-instance-paas-overview.md) enquadra-se na categoria industrial de *Plataforma-as-A-Service (PaaS)* e é o melhor para a maioria das migrações para a nuvem. SQL Managed Instance é uma coleção de bases de dados de sistemas e utilizadores com um conjunto partilhado de recursos que está pronto para levantar e mudar.  O melhor para novas aplicações ou aplicações existentes no local que queiram utilizar as mais recentes funcionalidades estáveis do SQL Server e que são migradas para a nuvem com alterações mínimas. Uma Instância Gerida SQL é semelhante a uma instância do motor de base de [dados Microsoft SQL Server](https://docs.microsoft.com/sql/database-engine/sql-server-database-engine-overview) que oferece recursos partilhados para bases de dados e funcionalidades adicionais com âmbito de instância. O SQL Managed Instance suporta a migração da base de dados a partir de instalações com alterações mínimas ou sem base de dados. Esta opção fornece todos os benefícios paaS da Base de Dados Azure SQL, mas adiciona capacidades que anteriormente só estavam disponíveis em VMs de Servidor SQL. Isto inclui uma rede virtual nativa (VNet) e quase 100% compatibilidade com o SQL Server no local. As Instâncias Geridas SQL fornecem acesso completo ao Servidor SQL e compatibilidade de funcionalidades para a migração de Servidores SQL para O Azure.

### <a name="sql-server-on-azure-vm"></a>SQL Server numa VM do Azure

[**O SQL Server no Azure VM**](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) enquadra-se na categoria de indústria *Infraestrutura-as-a-Service (IaaS)* e permite-lhe executar o SQL Server dentro de uma máquina virtual totalmente gerida (VM) em Azure. O melhor para migrações e aplicações que requerem acesso ao nível dos OS. As máquinas virtuais SQL em Azure estão prontas para as aplicações existentes que requerem migração rápida para a nuvem com alterações mínimas ou sem alterações. As máquinas virtuais SQL oferecem controlo administrativo total sobre a instância Do Servidor SQL e o SISTEMA subjacente à migração para O Azure. Cenários de desenvolvimento e teste rápidos quando não pretende comprar hardware de SQL Server de não produção no local. As máquinas virtuais SQL também funcionam em hardware padronizado que é propriedade, hospedada e mantida pela Microsoft. Ao utilizar máquinas virtuais SQL, pode pagar-as-as-sua-go para uma licença SQL Server já incluída numa imagem do SQL Server ou utilizar facilmente uma licença existente. Também pode parar ou retomar o VM conforme necessário. O SQL Server instalado e hospedado na nuvem funciona em máquinas virtuais Windows Server ou Linux em funcionamento no Azure, também conhecida como uma infraestrutura como um serviço (IaaS). As máquinas virtuais SQL são uma boa opção para migrar no local bases de dados e aplicações do SQL Server sem qualquer alteração na base de dados. Todas as versões e edições recentes do SQL Server estão disponíveis para instalação numa máquina virtual IaaS. A diferença mais significativa entre a Base de Dados SQL e as Instâncias Geridas SQL é que o SQL Server em VMs Azure permite o controlo total sobre o motor de base de dados. Pode escolher quando iniciar a manutenção/remendar, alterar o modelo de recuperação para simples ou a granel, fazer uma pausa ou iniciar o serviço quando necessário, e pode personalizar totalmente o motor de base de dados do SQL Server. Com este controlo adicional vem a responsabilidade acrescida de gerir a máquina virtual.

Otimizado para migrar as aplicações existentes para o Azure ou alargar as aplicações existentes no local para a nuvem em implantações híbridas. Além disso, pode utilizar o SQL Server numa máquina virtual para desenvolver e testar aplicações do SQL Server tradicionais . Com máquinas virtuais SQL, você tem todos os direitos administrativos sobre uma instância dedicada do Servidor SQL e um VM baseado em nuvem. É uma opção perfeita quando uma organização já tem recursos de TI disponíveis para manter as máquinas virtuais. Estas capacidades permite-lhe criar um sistema altamente personalizado para fazer face aos requisitos de disponibilidade e desempenho específicos da sua aplicação.


As diferenças adicionais estão listadas na tabela seguinte, mas tanto a Base de ***Dados SQL como a SQL Managed Instance estão otimizadas para reduzir ao mínimo os custos*** globais de gestão para o fornecimento e gestão de muitas bases de dados. Os custos de administração em curso são reduzidos, uma vez que não é preciso gerir nenhuma máquina virtual, sistema operativo ou software de base de dados. Não é necessário gerir as atualizações, a elevada disponibilidade ou as [cópias de segurança](database/automated-backups-overview.md). Em geral, a Base de Dados SQL e a Instância Gerida SQL podem aumentar drasticamente o número de bases de dados geridas por um único recurso de TI ou desenvolvimento. [As piscinas elásticas](database/elastic-pool-overview.md) também apoiam arquiteturas de aplicações multi-arrendatárias SaaS com características que incluem isolamento de inquilinos e a capacidade de escalar para reduzir custos através da partilha de recursos através de bases de dados. [A SQL Managed Instance](managed-instance/sql-managed-instance-paas-overview.md) fornece suporte para funcionalidades por exemplo que permitem uma migração fácil das aplicações existentes, bem como a partilha de recursos entre bases de dados.

### <a name="comparison-table"></a>Tabela de comparação

| Base de Dados SQL do Azure | Instância Gerida do Azure SQL | SQL Server numa VM do Azure |
| :--- | :--- | :--- |
|Suporta a maioria das capacidades de base de dados no local. As funcionalidades do SQL Server mais utilizadas estão disponíveis.<br/>99,995% de disponibilidade garantida.<br/>Reforços embutidos, remendos, recuperação.<br/>Última versão estável do Motor de Base de Dados.<br/>Capacidade de atribuir recursos necessários (CPU/armazenamento) a bases de dados individuais.<br/>Inteligência avançada e segurança incorporadas.<br/>Mudança de recursos on-line (CPU/armazenamento).| Suporta quase todas as capacidades de nível de instância e base de dados no local. Alta compatibilidade com o SQL Server no local.<br/>99,99% de disponibilidade garantida.<br/>Reforços embutidos, remendos, recuperação.<br/>Última versão estável do Motor de Base de Dados.<br/>Migração fácil do Servidor SQL.<br/>Endereço IP privado dentro do Azure VNet.<br/>Inteligência avançada e segurança incorporadas.<br/>Mudança de recursos on-line (CPU/armazenamento).| Tem controlo total sobre o motor SQL Server. Suporta todas as capacidades no local.<br/>Até 99,99% de disponibilidade.<br/>Paridade total com a versão correspondente do SQL Server no local.<br/>Versão fixa e conhecida do motor de base de dados.<br/>Migração fácil do SQL Server no local.<br/>Endereço IP privado dentro do Azure VNet.<br/>Tem capacidade para implementar aplicações ou serviços no anfitrião onde o SQL Server está colocado.|
|A migração do SQL Server pode ser um desafio.<br/>Algumas funcionalidades do SQL Server não estão disponíveis.<br/>Não há tempo de manutenção exata garantido (mas quase transparente).<br/>A compatibilidade com a versão SQL Server só pode ser alcançada utilizando níveis de compatibilidade de bases de dados.<br/>O endereço IP privado não pode ser atribuído (pode limitar o acesso através de regras de firewall).|Existe ainda um número mínimo de funcionalidades do SQL Server que não estão disponíveis.<br/>Não há tempo de manutenção exata garantido (mas quase transparente).<br/>A compatibilidade com a versão SQL Server só pode ser alcançada utilizando níveis de compatibilidade de bases de dados.|Tens de gerir os teus backups e remendos.<br>Você precisa implementar sua própria solução de Alta Disponibilidade.<br/>Há um tempo de inatividade ao mesmo tempo que altera os recursos (CPU/armazenamento)|
| Bases de dados de até 100 TB. | Até 8 TB. | Casos do Servidor SQL com até 256 TB de armazenamento. A instância pode suportar tantas bases de dados quanto necessário. |
| A aplicação no local pode aceder a dados na Base de Dados Azure SQL. | [Implementação](managed-instance/vnet-existing-add-subnet.md) de rede virtual nativa e conectividade com o seu ambiente no local usando a Rota Expresso Azure ou o Gateway VPN. | Com máquinas virtuais SQL, pode ter aplicações que funcionam parcialmente na nuvem e em parte no local. Por exemplo, pode expandir a sua rede no local e o Domínio do Active Directory para a nuvem através da [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md). Para obter mais informações sobre soluções híbridas em nuvem, consulte O alargamento das [soluções de dados no local para a nuvem](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |


## <a name="cost"></a>Custo

Quer seja uma startup que está presa a dinheiro, ou uma equipa de uma empresa estabelecida que opera sob restrições orçamentais apertadas, o financiamento limitado é muitas vezes o principal impulsionador na hora de decidir como hospedar as suas bases de dados. Nesta secção, você aprende sobre o básico de faturação e licenciamento em Azure associado à família de serviços Azure SQL.  Também podem aprender a calcular o custo total da aplicação.

### <a name="billing-and-licensing-basics"></a>Noções básicas de faturação e licenciamento

Atualmente, tanto a Base de **Dados SQL** como a **SQL Managed Instance** são vendidas como um serviço e estão disponíveis com várias opções e em vários níveis de serviço com diferentes preços para recursos, todos os quais são faturados de hora a hora a uma taxa fixa com base no nível de serviço e no tamanho da computação que escolher. Para obter as informações mais recentes sobre os atuais níveis de serviço suportados, tamanhos de cálculo e valores de armazenamento, consulte o [modelo de compra baseado em DTU para a Base de Dados SQL](database/service-tiers-dtu.md) e o modelo de compra baseado em VCore tanto para a Base de Dados SQL como para a [Instância Gerida SQL.](database/service-tiers-vcore.md)

- Com a base de dados SQL, pode escolher um nível de serviço que se adapte às suas necessidades a partir de uma ampla gama de preços a partir de 5$/mês para o nível básico e pode criar [piscinas elásticas](database/elastic-pool-overview.md) para partilhar recursos entre bases de dados para reduzir custos e acomodar picos de utilização.
- Com a SQL Managed Instance, também pode trazer a sua própria licença. Para obter mais informações sobre o licenciamento de bring-your-own, consulte [A Mobilidade de Licença através](https://azure.microsoft.com/pricing/license-mobility/) da Garantia de Software no Azure ou use a [calculadora de benefícios híbridos Azure](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) para ver como **economizar até 40%**.

Além disso, é-lhe faturado o tráfego de Internet de saída a [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) regulares. Pode ajustar dinamicamente os níveis de serviço e calcular tamanhos para corresponder às necessidades variadas de entrada da sua aplicação.

Com a Base de **Dados SQL** e a **Instância Gerida SQL,** o software de base de dados é automaticamente configurado, corrigido e atualizado pelo Azure, o que reduz os custos de administração. Além disso, as capacidades de [cópia de segurança incorporada](database/automated-backups-overview.md) permitem-lhe obter uma redução significativa de custos, sobretudo quando tem um número elevado de bases de dados.

Com **o SQL em VMs Azure,** pode utilizar qualquer uma das imagens Do Servidor SQL fornecidas pela plataforma (que inclui uma licença) ou trazer a sua licença SQL Server. Todas as versões suportadas do SQL Server (2008R2, 2012, 2014, 2016, 2017, 2019) e edições (Developer, Express, Web, Standard, Enterprise) estão disponíveis. Além disso, as versões Traga a sua própria licença (BYOL) das imagens estão disponíveis. Quando utiliza as imagens fornecidas pelo Azure, o custo operacional depende do tamanho da VM e a edição do SQL Server que escolher. Independentemente do tamanho vm ou edição do SQL Server, você paga o custo de licenciamento por minuto do SQL Server e do Windows ou Linux Server, juntamente com o custo de Armazenamento Azure para os discos VM. A opção de faturação por minuto permite-lhe utilizar o SQL Server durante o tempo que precisar, sem comprar licenças adicionais do SQL Server. Se levar a sua própria licença SQL Server para o Azure, é cobrado apenas pelos custos de servidor e armazenamento. Para obter mais informações sobre o modelo «traga a sua própria licença», consulte o artigo [Mobilidade de Licenças através do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Além disso, é-lhe faturado o tráfego de Internet de saída a [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) regulares.

#### <a name="calculating-the-total-application-cost"></a>Calcular o custo total da aplicação

Quando começa a utilizar uma plataforma na nuvem, o custo de execução da sua aplicação inclui o custo para novos custos de desenvolvimento e administração em curso, além dos custos de serviço da plataforma de nuvem pública.

Para obter mais informações sobre preços, consulte os seguintes recursos:

- [Base de Dados SQL & preços de instância gerida sQL](https://azure.microsoft.com/pricing/details/sql-database/)
- [Preços de máquina virtual](https://azure.microsoft.com/pricing/details/virtual-machines/) para [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) e para [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Calculadora de Preços Azure](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>Administração

Para muitas empresas, a decisão de fazer a transição para um serviço em nuvem passa tanto por descartar a complexidade de administração como pelo custo. Com o IaaS e o PaaS, o Azure administra a infraestrutura subjacente e replica automaticamente todos os dados para fornecer recuperação de desastres, configurae e melhora o software da base de dados, gere o equilíbrio de carga e falha transparente se houver uma falha de servidor dentro de um centro de dados.

- Com a Base de **Dados SQL** e a **Instância Gerida SQL,** pode continuar a administrar a sua base de dados, mas já não precisa de gerir o motor de base de dados, o sistema operativo ou o hardware. Exemplos de itens que pode continuar a administrar incluem bases de dados e inícios de sessão, otimização de índices e consultas, e auditoria e segurança. Além disso, configurar alta disponibilidade para outro centro de dados requer uma configuração e administração mínimas.
- Com **sQL em VMs Azure,** tem controlo total sobre o sistema operativo e a configuração da instância do Servidor SQL. Com um VM, cabe-lhe decidir quando atualizar/atualizar o sistema operativo e o software de base de dados e quando instalar qualquer software adicional, como o antivírus. Algumas funcionalidades automatizadas são fornecidas para simplificar significativamente a aplicação de patches, a cópia de segurança e a elevada disponibilidade. Além disso, pode controlar o tamanho da VM, o número de discos e as respetivas configurações de armazenamento. O Azure permite-lhe alterar o tamanho de uma VM conforme necessário. Para obter informações, consulte o artigo [Máquina Virtual e Tamanhos do Serviço em Nuvem do Azure](../virtual-machines/windows/sizes.md).

## <a name="service-level-agreement-sla"></a>Contrato de Nível de Serviço (SLA)

Para muitos departamentos de TI, cumprir as obrigações de tempo de atividade de um Contrato de Nível de Serviço (SLA) é uma prioridade máxima. Nesta secção, vamos ver que SLA se aplica a cada opção de alojamento de base de dados.

Tanto para a Base de **Dados Azure SQL** como para a **Azure SQL Managed Instance,** a Microsoft fornece uma disponibilidade de SLA de 99,99%. Para obter as informações mais recentes, consulte [Contrato de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/sql-database/).

Para **a SQL em VMs Azure,** a Microsoft fornece uma disponibilidade De SLA de 99,95% que cobre apenas a máquina virtual. Este SLA não abrange os processos (como o SQL Server) em execução na VM e requer que aloje, pelo menos, duas instâncias de VM num conjunto de disponibilidade. Para obter as informações mais recentes, consulte o [SLA de VM](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Para a elevada disponibilidade de base de dados (HA) dentro de VMs, deve configurar uma das opções de alta disponibilidade suportadas no Servidor SQL, como os [grupos de disponibilidade Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Utilizar uma opção de elevada disponibilidade suportadas não fornece um SLA adicional, mas permite-lhe alcançar uma disponibilidade de base de dados > 99,99%.

## <a name="time-to-move-to-azure"></a><a name="market"></a>Hora de se mudar para Azure

A Base de **Dados Azure SQL** é a solução certa para aplicações desenhadas na nuvem quando a produtividade do desenvolvedor e o tempo rápido para o mercado para novas soluções são fundamentais. Com funcionalidade semelhante a DBA programática, é perfeita para programadores e arquitetos de nuvem, uma vez que reduz a necessidade de gerir a base de dados e o sistema operativo subjacentes.

**O Azure SQL Managed Instance** simplifica consideravelmente a migração de aplicações existentes para o Azure, permitindo-lhe colocar rapidamente no mercado aplicações de base de dados migradas em Azure.

**Os SQL em VMs Azure** são perfeitos se as suas aplicações existentes ou novas requerem grandes bases de dados ou acesso a todas as funcionalidades no SQL Server ou Windows/Linux, e pretende evitar o tempo e as despesas de aquisição de novos hardware no local. Também é um bom ajuste quando se pretende migrar as aplicações e bases de dados existentes no local para o Azure as-is - nos casos em que a Base de Dados SQL ou a SQL Managed Instance não são um bom encaixe. Uma vez que não precisa de alterar as camadas de apresentação, aplicação e dados, poupa tempo e orçamento na re-arquitetação da sua solução existente. Em vez disso, pode concentrar-se na migração de todas as suas soluções para o Azure e na execução de algumas otimizações de desempenho que possam ser exigidas pela plataforma do Azure. Para obter mais informações, consulte o artigo [Melhores Práticas de Desempenho para o SQL Server em Azure Virtual Machines](virtual-machines/windows/performance-guidelines-best-practices.md).

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>Próximos passos

- Consulte [A sua primeira Base de Dados SQL do Azure](database/single-database-create-quickstart.md) para começar a utilizar a Base de Dados SQL.
- Consulte [o seu primeiro Caso Gerido Azure SQL](managed-instance/instance-create-quickstart.md) para começar com a SQL Managed Instance. 
- Consulte [Preços de Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).
- Consulte [Aprovisionar uma máquina virtual do SQL Server no Azure](virtual-machines/windows/create-sql-vm-portal.md) para começar a utilizar o SQL Server em VMs do Azure.
- Identifique a base de [dados SQL ou sku de instância gerida sQL para a sua base de dados no local](/sql/dma/dma-sku-recommend-sql-db/).
