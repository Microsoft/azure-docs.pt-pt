---
title: O que é o Azure SQL?
description: 'Conheça as diferentes opções dentro da família de serviços Azure SQL: Azure SQL Database, Azure SQL Managed Instance e SQL Server em Azure VM.'
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: overview
keywords: Nuvem de servidor SQL, SQL Server na nuvem, base de dados PaaS, cloud SQL Server, DBaaS, IaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/27/2020
ms.openlocfilehash: 4cc1eefa93366451b568da789fd48d8a8c658439
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91618272"
---
# <a name="what-is-azure-sql"></a>O que é o Azure SQL? 
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

Azure SQL é uma família de produtos geridos, seguros e inteligentes que utilizam o motor de base de dados SQL Server na nuvem Azure.

- **Base de Dados Azure SQL**: Suporte aplicações de nuvem modernas num serviço de base de dados inteligente e gerido, que inclui computação sem servidor. 
- **Exemplo gerido Azure SQL**: Modernizar as aplicações existentes do SQL Server em escala com uma instância inteligente totalmente gerida como serviço, com quase 100% de paridade de funcionalidades com o motor de base de dados SQL Server. O melhor para a maioria das migrações para a nuvem.
- **SQL Server em VMs Azure**: Levantar e deslocar as cargas de trabalho do SQL Server com facilidade e manter 100% de compatibilidade do Servidor SQL e acesso ao nível do sistema operativo. 
 
O Azure SQL é construído sobre o familiar motor SQL Server, para que possa migrar aplicações com facilidade e continuar a usar as ferramentas, idiomas e recursos que conhece. As suas habilidades e experiência transferem-se para a nuvem, para que possa fazer ainda mais com o que já tem. 

Saiba como cada produto se encaixa na plataforma de dados Azure SQL da Microsoft para corresponder à opção certa para os requisitos do seu negócio. Quer priorize a poupança de custos ou a administração mínima, este artigo pode ajudá-lo a decidir qual a abordagem que oferece contra os requisitos de negócio que mais gosta.


Se você é novo no Azure SQL, confira o vídeo *What is Azure SQL* da nossa série de [vídeo Azure SQL](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)aprofundada :
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/What-is-Azure-SQL-3-of-61/player]



## <a name="overview"></a>Descrição geral

No mundo atual, impulsionado por dados, impulsionar a transformação digital depende cada vez mais da nossa capacidade de gerir quantidades massivas de dados e aproveitar o seu potencial. Mas as propriedades de dados de hoje são cada vez mais complexas, com dados alojados no local, na nuvem, ou na borda da rede. Os desenvolvedores que estão a construir aplicações inteligentes e imersivas podem encontrar-se limitados por limitações que podem, em última análise, afetar a sua experiência. As limitações decorrentes de plataformas incompatíveis, a inadequada segurança de dados, os recursos insuficientes e as barreiras de desempenho dos preços criam complexidade que pode inibir a modernização e desenvolvimento de aplicações. 

Um dos primeiros aspetos a compreender em qualquer debate do Azure versus bases de dados do SQL Server no local é que pode utilizar todas. A plataforma de dados da Microsoft aproveita a tecnologia SQL Server e disponibiliza-a em máquinas físicas no local, ambientes de nuvem privada, ambientes de nuvem privados hospedados por terceiros e nuvem pública. 


### <a name="fully-managed-and-always-up-to-date"></a>Totalmente gerido e sempre atualizado 

Passe mais tempo a inovar e menos tempo a remendar, a atualizar e a fazer backup das suas bases de dados. O Azure é a única nuvem com SQL sempre verde que aplica automaticamente as últimas atualizações e patches para que as suas bases de dados estejam sempre atualizadas — eliminando o ausíssam final de suporte. Até tarefas complexas como afinação de desempenho, alta disponibilidade, recuperação de desastres e backups são automatizados, libertando-o para se concentrar nas aplicações.  

### <a name="protect-your-data-with-built-in-intelligent-security"></a>Proteja os seus dados com segurança inteligente incorporada 

O Azure monitoriza constantemente os seus dados por ameaças. Com a Azure SQL, pode:

- Remediar potenciais ameaças em tempo real com alertas inteligentes [de deteção de ameaças avançadas](https://docs.microsoft.com/azure/security/fundamentals/threat-detection#advanced-threat-detection-features-other-azure-services) e de avaliação de vulnerabilidades proativas. 
- Obtenha proteção multicamada líder do setor com [controlos de segurança incorporados,](https://azure.microsoft.com/overview/security/) incluindo T-SQL, autenticação, networking e gestão de chaves. 
- Aproveite a cobertura de [conformidade](https://azure.microsoft.com/overview/trusted-cloud/compliance/) mais abrangente de qualquer serviço de base de dados em nuvem. 


### <a name="business-motivations"></a>Motivações empresariais

Existem vários fatores que podem influenciar a sua decisão de escolher entre as diferentes ofertas de dados:

- [Custo](#cost): Tanto a opção PaaS como a IaaS incluem o preço base que cobre infraestruturas subjacentes e licenciamento. No entanto, com a opção IaaS é necessário investir tempo e recursos adicionais para gerir a sua base de dados, enquanto no PaaS obtém estas funcionalidades de administração incluídas no preço. O IaaS permite-lhe desligar recursos enquanto não os utiliza para diminuir o custo, enquanto o PaaS está sempre em funcionamento, a menos que deixe cair e re-crie os seus recursos quando são necessários.
- [Administração](#administration): As opções PaaS reduzem o tempo que precisa para investir para administrar a base de dados. No entanto, também limita o leque de tarefas e scripts de administração personalizados que você pode executar ou executar. Por exemplo, o CLR não é suportado com base de dados SQL, mas é suportado para um exemplo de SQL Managed Instance. Além disso, nenhuma opção de implantação no PaaS suporta o uso de bandeiras de vestígios.
- [Acordo de nível de serviço](#service-level-agreement-sla): Tanto o IaaS como o PaaS fornecem SLA padrão elevado e padrão da indústria. A opção PaaS garante 99,99% SLA, enquanto o IaaS garante 99,95% SLA para infraestruturas, o que significa que precisa de implementar mecanismos adicionais para garantir a disponibilidade das suas bases de dados. Pode atingir 99,99% de SLA criando uma máquina virtual SQL adicional e implementando a solução de alta disponibilidade do grupo SQL Always On availability group. 
- [Hora de mudar para Azure](#market): SQL Server on Azure VM é a correspondência exata do seu ambiente, por isso a migração de instalações para o VM Azure não é diferente de mover as bases de dados de um servidor no local para outro. A SQL Managed Instance também permite uma migração fácil; no entanto, pode haver algumas alterações que precisa aplicar antes da sua migração. 


## <a name="service-comparison"></a>Comparação de serviços

   ![Opções do Servidor SQL da Nuvem: SQL Server em IaaS ou SaaS SQL Database na nuvem.](./media/azure-sql-iaas-vs-paas-what-is-overview/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Como se pode ver no diagrama, cada oferta de serviço pode ser caracterizada pelo nível de administração que tem sobre a infraestrutura, e pelo grau de eficiência de custos.

Em Azure, pode ter as suas cargas de trabalho do SQL Server a funcionar como um serviço hospedado[(PaaS),](https://azure.microsoft.com/overview/what-is-paas/)ou uma infraestrutura hospedada[(IaaS).](https://azure.microsoft.com/overview/what-is-iaas/) Dentro do PaaS, você tem várias opções de produto, e níveis de serviço dentro de cada opção. A questão-chave que precisa de fazer ao decidir entre o PaaS ou o IaaS é se pretende gerir a sua base de dados, aplicar patches e fazer backups, ou pretende delegar estas operações no Azure?

### <a name="azure-sql-database"></a>Base de Dados SQL do Azure

[A Azure SQL Database](database/sql-database-paas-overview.md) é uma base de dados relacional como um serviço (DBaaS) hospedado em Azure que se enquadra na categoria industrial de *Plataforma-as-a-Service (PaaS)*. 
- O melhor para aplicações em nuvem modernas que querem usar as mais recentes funcionalidades estáveis do SQL Server e têm restrições de tempo no desenvolvimento e marketing. 
- Um motor de base de dados SQL Server totalmente gerido, baseado na mais recente edição estável da Enterprise Edition do SQL Server. A SQL Database tem duas opções de implementação construídas em hardware e software padronizados que são propriedade, hospedada e mantida pela Microsoft. 

Com o SQL Server, pode utilizar funcionalidades e funcionalidades incorporadas que requerem uma configuração extensiva (seja no local ou numa máquina virtual Azure). Ao utilizar a Base de Dados SQL, paga apenas o que utilizar, dispondo de opções para aumentar verticalmente ou horizontalmente para obter um maior desempenho sem interrupções. A SQL Database tem algumas funcionalidades adicionais que não estão disponíveis no SQL Server, tais como alta disponibilidade incorporada, inteligência e gestão.


A Azure SQL Database oferece as seguintes opções de implementação:
  - Como uma única base de [*dados*](database/single-database-overview.md) com o seu próprio conjunto de recursos geridos através de um [servidor lógico SQL](database/logical-servers.md). Uma única base de dados é semelhante a uma [base de dados contida](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) no SQL Server. Esta opção está otimizada para o desenvolvimento moderno de aplicações de novas aplicações nascidas na nuvem. Estão disponíveis opções [de hiperescala](database/service-tier-hyperscale.md) e [sem servidor.](database/serverless-tier-overview.md)
  - Uma [*piscina elástica,*](database/elastic-pool-overview.md)que é uma coleção de bases de dados com um conjunto partilhado de recursos geridos através de um [servidor lógico SQL](database/logical-servers.md). Bases de dados individuais podem ser transferidas para dentro e para fora de uma piscina elástica. Esta opção é otimizada para o desenvolvimento moderno de aplicações de novas aplicações nascidas na nuvem usando o padrão de aplicação SaaS multi-inquilino. As piscinas elásticas fornecem uma solução rentável para gerir o desempenho de várias bases de dados que têm padrões de utilização variáveis.

### <a name="azure-sql-managed-instance"></a>Instância Gerida do Azure SQL

[Azure SQL Managed Instance](managed-instance/sql-managed-instance-paas-overview.md) insere-se na categoria industrial de *Platform-as-a-Service (PaaS)* e é o melhor para a maioria das migrações para a nuvem. SQL Managed Instance é uma coleção de bases de dados de sistemas e utilizadores com um conjunto partilhado de recursos que está pronto para levantar e deslocar.  
- O melhor para novas aplicações ou aplicações existentes no local que queiram utilizar as mais recentes funcionalidades estáveis do SQL Server e que são migradas para a nuvem com alterações mínimas. Um caso de SQL Managed Instance é semelhante a uma instância do motor de base de dados do [Microsoft SQL Server](https://docs.microsoft.com/sql/database-engine/sql-server-database-engine-overview) que oferece recursos partilhados para bases de dados e funcionalidades adicionais de âmbito de instância. 
- A SQL Managed Instance suporta a migração de bases de dados a partir de instalações com alterações mínimas a nenhuma base de dados. Esta opção fornece todos os benefícios PaaS da Base de Dados Azure SQL, mas adiciona capacidades que anteriormente só estavam disponíveis em VMs do SQL Server. Isto inclui uma rede virtual nativa e quase 100% de compatibilidade com o SQL Server no local. Exemplos de SQL Managed Instance fornecem acesso completo ao SERVIDOR SQL e compatibilidade de recursos para migrar servidores SQL para Azure.

### <a name="sql-server-on-azure-vm"></a>SQL Server numa VM do Azure

[O SQL Server em Azure VM](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) enquadra-se na categoria *industrial Infra-as-a-Service (IaaS)* e permite-lhe executar o SQL Server dentro de uma máquina virtual (VM) totalmente gerida em Azure. 
- O melhor para migrações e aplicações que requerem acesso ao nível do SISTEMA. As máquinas virtuais SQL em Azure estão prontas para o elevador e a mudança para aplicações existentes que requerem uma migração rápida para a nuvem com alterações mínimas ou sem alterações. As máquinas virtuais SQL oferecem controlo administrativo total sobre a instância do SQL Server e o SISTEMA subjacente para migração para Azure. 
- Cenários de desenvolvimento e teste rápidos quando não pretende comprar hardware de SQL Server de não produção no local. As máquinas virtuais SQL também funcionam em hardware padronizado que é propriedade, hospedada e mantida pela Microsoft. Ao utilizar máquinas virtuais SQL, pode pagar como ir para uma licença SQL Server já incluída numa imagem do SQL Server ou utilizar facilmente uma licença existente. Também pode parar ou retomar o VM conforme necessário. 
- O SQL Server instalado e hospedado na nuvem funciona em máquinas virtuais Windows Server ou Linux em funcionamento no Azure, também conhecido como uma infraestrutura como um serviço (IaaS). As máquinas virtuais SQL são uma boa opção para migrar bases de dados e aplicações do SQL Server sem qualquer alteração na base de dados. Todas as versões e edições recentes do SQL Server estão disponíveis para instalação numa máquina virtual IaaS. 

    A diferença mais significativa entre a Base de Dados SQL e a SqL Managed Instance é que o SQL Server em Azure Virtual Machines permite o controlo total sobre o motor da base de dados. Pode escolher quando iniciar a manutenção/remendos, alterar o modelo de recuperação para simples ou registado a granel, fazer uma pausa ou iniciar o serviço quando necessário, podendo personalizar totalmente o motor de base de dados SQL Server. Com este controlo adicional surge a responsabilidade acrescida de gerir a máquina virtual.
- Otimizado para migrar aplicações existentes para a Azure ou alargar as aplicações existentes no local para a nuvem em implementações híbridas. Além disso, pode utilizar o SQL Server numa máquina virtual para desenvolver e testar aplicações do SQL Server tradicionais . Com máquinas virtuais SQL, você tem todos os direitos administrativos sobre uma instância dedicada do SQL Server e um VM baseado na nuvem. É uma opção perfeita quando uma organização já tem recursos de TI disponíveis para manter as máquinas virtuais. Estas capacidades permite-lhe criar um sistema altamente personalizado para fazer face aos requisitos de disponibilidade e desempenho específicos da sua aplicação.

As diferenças adicionais estão listadas no quadro seguinte, mas tanto a *SQL Database como a SQL Managed Instance estão otimizadas para reduzir os custos globais de gestão ao mínimo para o provisionamento e gestão de muitas bases de dados.* Os custos de administração em curso são reduzidos, uma vez que não tem de gerir máquinas virtuais, sistema operativo ou software de base de dados. Não é necessário gerir as atualizações, a elevada disponibilidade ou as [cópias de segurança](database/automated-backups-overview.md). 

Em geral, a BASE de Dados SQL e a SQL Managed Instance podem aumentar drasticamente o número de bases de dados geridas por um único recurso de TI ou desenvolvimento. [As piscinas elásticas](database/elastic-pool-overview.md) também suportam arquiteturas de aplicações multi-arrendatários SaaS com características que incluem isolamento de inquilinos e a capacidade de escalar para reduzir custos através da partilha de recursos através de bases de dados. [A SQL Managed Instance](managed-instance/sql-managed-instance-paas-overview.md) fornece suporte para funcionalidades por exemplo que permitem uma fácil migração de aplicações existentes, bem como a partilha de recursos entre bases de dados.

### <a name="comparison-table"></a>Tabela de comparação

| Base de Dados SQL do Azure | Instância Gerida do Azure SQL | SQL Server numa VM do Azure |
| :--- | :--- | :--- |
|Suporta a maioria das capacidades de nível de base de dados no local. As funcionalidades do SQL Server mais utilizadas estão disponíveis.<br/>99,995% de disponibilidade garantida.<br/>Reforços incorporados, remendos, recuperação.<br/>Versão mais recente do Motor base de dados estável.<br/>Capacidade de atribuir recursos necessários (CPU/armazenamento) a bases de dados individuais.<br/>Inteligência e segurança avançadas.<br/>Mudança online de recursos (CPU/armazenamento).| Suporta quase todas as capacidades de nível de instância e de nível de base de dados no local. Alta compatibilidade com o SQL Server.<br/>99,99% de disponibilidade garantida.<br/>Reforços incorporados, remendos, recuperação.<br/>Versão mais recente do Motor base de dados estável.<br/>Fácil migração do SQL Server.<br/>Endereço IP privado dentro da Rede Virtual Azure.<br/>Inteligência e segurança avançadas.<br/>Mudança online de recursos (CPU/armazenamento).| Tem controlo total sobre o motor SQL Server. Suporta todas as capacidades no local.<br/>Até 99,99% de disponibilidade.<br/>Paridade total com a versão correspondente do SQL Server no local.<br/>Versão fixa e conhecida do Motor de Base de Dados.<br/>Fácil migração do SQL Server.<br/>Endereço IP privado dentro da Rede Virtual Azure.<br/>Tem a capacidade de implementar aplicações ou serviços no anfitrião onde o SQL Server é colocado.|
|A migração do SQL Server pode ser um desafio.<br/>Algumas funcionalidades do SQL Server não estão disponíveis.<br/>Sem tempo de manutenção exato garantido (mas quase transparente).<br/>A compatibilidade com a versão SQL Server só pode ser alcançada utilizando níveis de compatibilidade na base de dados.<br/>Suporte de endereço IP privado com [Link Privado Azure](database/private-endpoint-overview.md).|Existe ainda um número mínimo de funcionalidades do SQL Server que não estão disponíveis.<br/>Sem tempo de manutenção exato garantido (mas quase transparente).<br/>A compatibilidade com a versão SQL Server só pode ser alcançada utilizando níveis de compatibilidade na base de dados.|Tens de gerir os teus backups e patches.<br>Tens de implementar a tua própria solução de High-Availability.<br/>Há uma inatividade ao alterar os recursos (CPU/armazenamento)|
| Bases de dados de até 100 TB. | Até 8 TB. | SQL Server com até 256 TB de armazenamento. A instância pode suportar tantas bases de dados quanto necessário. |
| A aplicação no local pode aceder a dados na Base de Dados Azure SQL. | [Implementação de rede virtual nativa](managed-instance/vnet-existing-add-subnet.md) e conectividade com o seu ambiente no local usando a Rota Expresso Azure ou VPN Gateway. | Com máquinas virtuais SQL, pode ter aplicações que funcionam parcialmente na nuvem e em parte no local. Por exemplo, pode expandir a sua rede no local e o Domínio do Active Directory para a nuvem através da [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md). Para obter mais informações sobre soluções híbridas em nuvem, consulte [a extensão das soluções de dados no local para a nuvem.](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud) |


## <a name="cost"></a>Cost

Quer seja uma startup que está presa a dinheiro, ou uma equipa de uma empresa estabelecida que opera sob restrições orçamentais apertadas, o financiamento limitado é muitas vezes o principal impulsionador na hora de decidir como hospedar as suas bases de dados. Nesta secção, você aprende sobre o básico de faturação e licenciamento em Azure associado à família de serviços Azure SQL.  Também podem aprender a calcular o custo total da aplicação.

### <a name="billing-and-licensing-basics"></a>Noções básicas de faturação e licenciamento

Atualmente, tanto a **SQL Database** como **a SQL Managed Instance** são vendidas como um serviço e estão disponíveis com várias opções e em vários níveis de serviço com diferentes preços de recursos, todos os quais são cobrados por hora a uma taxa fixa com base no nível de serviço e no tamanho do cálculo que você escolhe. Para obter as informações mais recentes sobre os atuais níveis de serviço suportados, tamanhos de cálculo e valores de armazenamento, consulte [o modelo de compra baseado em DTU para a Base de Dados SQL](database/service-tiers-dtu.md) e o modelo de compra baseado em [vCore tanto para a Base de Dados SQL como para a SQL Managed Instance](database/service-tiers-vcore.md).

- Com a BASE de Dados SQL, pode escolher um nível de serviço que se adapte às suas necessidades a partir de uma vasta gama de preços a partir de 5$/mês para o nível básico e pode criar [piscinas elásticas](database/elastic-pool-overview.md) para partilhar recursos entre bases de dados para reduzir custos e acomodar picos de utilização.
- Com a SQL Managed Instance, também pode trazer a sua própria licença. For more information on bring-your-own licensing, see [License Mobility through Software Assurance on Azure](https://azure.microsoft.com/pricing/license-mobility/) or use the [Azure Hybrid Benefit calculator](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) to see how to **save up to 40%**.

Além disso, é-lhe faturado o tráfego de Internet de saída a [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) regulares. Pode ajustar dinamicamente os níveis de serviço e os tamanhos de cálculo para corresponder às variadas necessidades de produção da sua aplicação.

Com **base de dados SQL** e **SQL Managed Instance,** o software de base de dados é automaticamente configurado, remendado e atualizado pela Azure, o que reduz os custos de administração. Além disso, as capacidades de [cópia de segurança incorporada](database/automated-backups-overview.md) permitem-lhe obter uma redução significativa de custos, sobretudo quando tem um número elevado de bases de dados.

Com **SQL em VMs Azure,** pode utilizar qualquer uma das imagens do SQL Server fornecidas pela plataforma (que inclui uma licença) ou trazer a sua licença SQL Server. Todas as versões suportadas do SQL Server (2008R2, 2012, 2014, 2016, 2017, 2019) e edições (Developer, Express, Web, Standard, Enterprise) estão disponíveis. Além disso, as versões Traga a sua própria licença (BYOL) das imagens estão disponíveis. Quando utiliza as imagens fornecidas pelo Azure, o custo operacional depende do tamanho da VM e a edição do SQL Server que escolher. Independentemente do tamanho VM ou edição do SQL Server, paga o custo de licenciamento por minuto do SQL Server e do Windows ou Linux Server, juntamente com o custo de Armazenamento Azure para os discos VM. A opção de faturação por minuto permite-lhe utilizar o SQL Server durante o tempo que precisar, sem comprar licenças adicionais do SQL Server. Se levar a sua própria licença SQL Server para o Azure, é cobrado apenas pelos custos de servidor e armazenamento. Para obter mais informações sobre o modelo «traga a sua própria licença», consulte o artigo [Mobilidade de Licenças através do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Além disso, é-lhe faturado o tráfego de Internet de saída a [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) regulares.

#### <a name="calculating-the-total-application-cost"></a>Calcular o custo total da aplicação

Quando começa a usar uma plataforma na nuvem, o custo de execução da sua aplicação inclui o custo de novo desenvolvimento e custos de administração em curso, além dos custos de serviço da plataforma de nuvem pública.

Para obter mais informações sobre preços, consulte os seguintes recursos:

- [Base de dados SQL & preços de instância gerida sql](https://azure.microsoft.com/pricing/details/sql-database/)
- [Preços da máquina virtual](https://azure.microsoft.com/pricing/details/virtual-machines/) para [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) e para [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Calculadora de Preços de Azure](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>Administração

Para muitas empresas, a decisão de fazer a transição para um serviço em nuvem passa tanto por descartar a complexidade de administração como pelo custo. Com o IaaS e o PaaS, o Azure administra a infraestrutura subjacente e replica automaticamente todos os dados para fornecer recuperação de desastres, configura e atualiza o software da base de dados, gere o equilíbrio de carga, e faz falha transparente se houver uma falha do servidor dentro de um centro de dados.

- Com **a SQL Database** e a **SQL Managed Instance,** pode continuar a administrar a sua base de dados, mas já não precisa de gerir o motor da base de dados, o sistema operativo ou o hardware. Exemplos de itens que pode continuar a administrar incluem bases de dados e inícios de sessão, otimização de índices e consultas, e auditoria e segurança. Além disso, configurar alta disponibilidade para outro centro de dados requer configuração e administração mínimas.
- Com **o SQL no Azure VM,** tem controlo total sobre o sistema operativo e a configuração de instância do SQL Server. Com um VM, cabe-lhe decidir quando atualizar/atualizar o sistema operativo e o software de base de dados e quando instalar qualquer software adicional, como antivírus. Algumas funcionalidades automatizadas são fornecidas para simplificar significativamente a aplicação de patches, a cópia de segurança e a elevada disponibilidade. Além disso, pode controlar o tamanho da VM, o número de discos e as respetivas configurações de armazenamento. O Azure permite-lhe alterar o tamanho de uma VM conforme necessário. Para obter informações, consulte o artigo [Máquina Virtual e Tamanhos do Serviço em Nuvem do Azure](../virtual-machines/windows/sizes.md).

## <a name="service-level-agreement-sla"></a>Contrato de nível de serviço (SLA)

Para muitos departamentos de TI, o cumprimento de obrigações de um acordo de nível de serviço (SLA) é uma prioridade máxima. Nesta secção, vamos ver que SLA se aplica a cada opção de alojamento de base de dados.

Tanto para **a Azure SQL Database** como **para a Azure SQL Managed Instance,** a Microsoft fornece um SLA de disponibilidade de 99,99%. Para obter as informações mais recentes, consulte o [acordo de nível de serviço.](https://azure.microsoft.com/support/legal/sla/sql-database/)

Para **o SQL em Azure VM,** a Microsoft fornece um SLA de disponibilidade de 99,95% que cobre apenas a máquina virtual. Este SLA não abrange os processos (como o SQL Server) em execução na VM e requer que aloje, pelo menos, duas instâncias de VM num conjunto de disponibilidade. Para obter as informações mais recentes, consulte o [SLA de VM](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Para uma base de dados de alta disponibilidade (HA) dentro de VMs, deverá configurar uma das opções de alta disponibilidade suportadas no SQL Server, tais como [grupos de disponibilidade Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Utilizar uma opção de elevada disponibilidade suportadas não fornece um SLA adicional, mas permite-lhe alcançar uma disponibilidade de base de dados > 99,99%.

## <a name="time-to-move-to-azure"></a><a name="market"></a>Hora de se mudar para Azure.

**A Azure SQL Database** é a solução certa para aplicações desenhadas em nuvem quando a produtividade do desenvolvedor e o tempo rápido para o mercado para novas soluções são cruciais. Com funcionalidade semelhante a DBA programática, é perfeita para programadores e arquitetos de nuvem, uma vez que reduz a necessidade de gerir a base de dados e o sistema operativo subjacentes.

**A Azure SQL Managed Instance** simplifica muito a migração de aplicações existentes para o Azure, permitindo-lhe trazer rapidamente aplicações de base de dados migradas para o mercado em Azure.

**O SQL no Azure VM** é perfeito se as suas aplicações existentes ou novas necessitarem de grandes bases de dados ou acesso a todas as funcionalidades no SQL Server ou Windows/Linux, e pretende evitar o tempo e as despesas de aquisição de novos hardware no local. Também é um bom ajuste quando se pretende migrar aplicações e bases de dados existentes no local para Azure como está - nos casos em que a SQL Database ou SQL Managed Instance não é um bom ajuste. Uma vez que não precisa de alterar as camadas de apresentação, aplicação e dados, poupa tempo e orçamento na recontecção da sua solução existente. Em vez disso, pode concentrar-se na migração de todas as suas soluções para o Azure e na execução de algumas otimizações de desempenho que possam ser exigidas pela plataforma do Azure. Para obter mais informações, consulte o artigo [Melhores Práticas de Desempenho para o SQL Server em Azure Virtual Machines](virtual-machines/windows/performance-guidelines-best-practices.md).

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>Passos seguintes

- Consulte [A sua primeira Base de Dados SQL do Azure](database/single-database-create-quickstart.md) para começar a utilizar a Base de Dados SQL.
- Consulte [o seu primeiro Azure SQL Managed Instance](managed-instance/instance-create-quickstart.md) para começar com a SQL Managed Instance. 
- Consulte [Preços de Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).
- Consulte [Aprovisionar uma máquina virtual do SQL Server no Azure](virtual-machines/windows/create-sql-vm-portal.md) para começar a utilizar o SQL Server em VMs do Azure.
- [Identifique a Base de Dados SQL certa ou o SQL Managed Instance SKU para a sua base de dados no local.](/sql/dma/dma-sku-recommend-sql-db/)
