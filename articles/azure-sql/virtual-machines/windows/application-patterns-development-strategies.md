---
title: Padrões de aplicação do SQL Server em VMs Microsoft Docs
description: Este artigo cobre padrões de aplicação para SQL Server em Azure Virtual Machines. Fornece aos arquitetos e desenvolvedores de soluções uma base para uma boa arquitetura e design de aplicações.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 41863c8d-f3a3-4584-ad86-b95094365e05
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mathoma
ms.openlocfilehash: 758607c37f271657c81d4699b0895f9e997450af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91299056"
---
# <a name="application-patterns-and-development-strategies-for-sql-server-on-azure-virtual-machines"></a>Padrões de aplicação e estratégias de desenvolvimento para O SQL Server em Máquinas Virtuais Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="summary"></a>Resumo:
Determinar quais os padrões ou padrões de aplicação a utilizar para as suas aplicações baseadas no SQL Server num ambiente Azure é uma decisão de design importante e requer uma compreensão sólida de como o SQL Server e cada componente de infraestrutura do Azure funcionam em conjunto. Com o SQL Server em Serviços de Infraestrutura Azure, pode migrar, manter e monitorizar facilmente as aplicações existentes do SQL Server construídas no Windows Server para máquinas virtuais (VMs) em Azure.

O objetivo deste artigo é fornecer aos arquitetos e desenvolvedores de soluções uma base para uma boa arquitetura e design de aplicações, que podem seguir ao migrar aplicações existentes para a Azure, bem como desenvolver novas aplicações no Azure.

Para cada padrão de aplicação, encontrará um cenário no local, a sua solução respetiva com ativação em nuvem e as respetivas recomendações técnicas. Além disso, o artigo discute estratégias de desenvolvimento específicas do Azure para que possa projetar corretamente as suas aplicações. Devido aos muitos padrões de aplicação possíveis, recomenda-se que arquitetos e desenvolvedores escolham o padrão mais adequado para suas aplicações e utilizadores.

**Contribuintes técnicos:** Luis Carlos Vargas Herring, Madhan Arumugam Ramakrishnan

**Revisores técnicos:** Corey Sanders, Drew McDaniel, Narayan Annamalai, Nir Mashkowski, Sanjay Mishra, Silvano Coriani, Stefan Schackow, Tim Hickey, Tim Wieman, Xin Jin

## <a name="introduction"></a>Introdução
Pode desenvolver muitos tipos de aplicações n-tier separando os componentes das diferentes camadas de aplicação em diferentes máquinas, bem como em componentes separados. Por exemplo, pode colocar os componentes de aplicação e regras de negócio do cliente numa máquina, o nível web frontal e os componentes do nível de acesso a dados noutra máquina, e um nível de base de dados back-end noutra máquina. Este tipo de estruturação ajuda a isolar cada nível um do outro. Se alterar de onde os dados vêm, não precisa de alterar o cliente ou a aplicação web, mas apenas os componentes do nível de acesso aos dados.

Uma aplicação típica *de n-tier* inclui o nível de apresentação, o nível de negócio e o nível de dados:

| Escalão de serviço | Descrição |
| --- | --- |
| **Apresentação** |O *nível de apresentação* (nível web, nível frontal) é a camada em que os utilizadores interagem com uma aplicação. |
| **Empresa** |O *nível de negócio* (nível médio) é a camada que o nível de apresentação e o nível de dados usam para comunicar uns com os outros e inclui a funcionalidade central do sistema. |
| **Dados** |O *nível de dados* é basicamente o servidor que armazena os dados de uma aplicação (por exemplo, um servidor que executa o SQL Server). |

As camadas de aplicação descrevem os agrupamentos lógicos da funcionalidade e dos componentes numa aplicação; que os níveis descrevem a distribuição física da funcionalidade e dos componentes em servidores físicos separados, computadores, redes ou locais remotos. As camadas de uma aplicação podem residir no mesmo computador físico (o mesmo nível) ou podem ser distribuídas por computadores separados (n-tier), e os componentes em cada camada comunicam com componentes noutras camadas através de interfaces bem definidas. Pode pensar no termo tier como referindo-se a padrões de distribuição física tais como dois níveis, três níveis e n-tier. Um **padrão de aplicação de 2 níveis** contém dois níveis de aplicação: servidor de aplicações e servidor de base de dados. A comunicação direta ocorre entre o servidor de aplicações e o servidor de base de dados. O servidor de aplicações contém componentes de nível web e de nível de negócio. No **padrão de aplicação de 3 níveis,** existem três níveis de aplicação: servidor web, servidor de aplicações, que contém o nível de lógica de negócio e/ou componentes de acesso a dados de nível de negócio, e o servidor de base de dados. A comunicação entre o servidor web e o servidor de base de dados acontece no servidor de aplicações. Para obter informações detalhadas sobre as camadas e níveis de aplicações, consulte [o Guia de Arquitetura da Aplicação da Microsoft](https://msdn.microsoft.com/library/ff650706.aspx).

Antes de começar a ler este artigo, deve ter conhecimento sobre os conceitos fundamentais do SQL Server e do Azure. Para obter informações, consulte [os livros de servidor SQL online,](https://msdn.microsoft.com/library/bb545450.aspx)o [SQL Server em Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md) e [Azure.com](https://azure.microsoft.com/).

Este artigo descreve vários padrões de aplicação que podem ser adequados para as suas aplicações simples, bem como as aplicações empresariais altamente complexas. Antes de detalhar cada padrão, recomendamos que se familiarize com os serviços de armazenamento de dados disponíveis em Azure, tais como [Azure Storage,](../../../storage/common/storage-introduction.md) [Azure SQL Database,](../../database/sql-database-paas-overview.md)e [SQL Server numa máquina virtual Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). Para tomar as melhores decisões de design para as suas aplicações, compreenda quando utilizar claramente qual o serviço de armazenamento de dados.

### <a name="choose-sql-server-on-azure-virtual-machines-when"></a>Escolha o Servidor SQL em Máquinas Virtuais Azure, quando:

* Precisa de controlo no SQL Server e Windows. Por exemplo, isto pode incluir a versão SQL Server, hotfixes especiais, configuração de desempenho, etc.
* Você precisa de uma compatibilidade completa com o SQL Server e quer mover as aplicações existentes para Azure como está.
* Pretende aproveitar as capacidades do ambiente Azure, mas a Base de Dados Azure SQL não suporta todas as funcionalidades que a sua aplicação necessita. Isto pode incluir as seguintes áreas:
  
  * **Tamanho da base de dados**: No momento em que este artigo foi atualizado, a SQL Database suporta uma base de dados de até 1 TB de dados. Se a sua aplicação necessitar de mais de 1 TB de dados e não quiser implementar soluções de sharding personalizadas, recomenda-se que utilize o SQL Server numa máquina virtual Azure. Para obter as informações mais recentes, consulte [a Base de Dados SQL de Escala,](https://msdn.microsoft.com/library/azure/dn495641.aspx) [Modelo de Compras Baseada em DTU](../../database/service-tiers-dtu.md)e [Modelo de Compras baseado em vCore (pré-visualização).](../../database/service-tiers-vcore.md)
  * **Conformidade HIPAA:** Os clientes de saúde e fornecedores independentes de software (ISVs) podem escolher [o SQL Server em Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md) em vez de [Azure SQL Database](../../database/sql-database-paas-overview.md) porque o SQL Server em Azure Virtual Machines é coberto pelo ACORDO DE ASSOCIADO DE NEGÓCIOS DA HIPAA (BAA). Para obter informações sobre a conformidade, consulte [o Microsoft Azure Trust Center: Compliance](https://azure.microsoft.com/support/trust-center/compliance/).
  * **Características de nível de exemplo**: Neste momento, a Base de Dados SQL não suporta funcionalidades que vivem fora da base de dados (tais como Servidores Ligados, Empregos de Agentes, FileStream, Corretor de Serviços, etc.). Para mais informações, consulte [as Diretrizes e Limitações da Base de Dados Azure SQL.](https://msdn.microsoft.com/library/azure/ff394102.aspx)

## <a name="1-tier-simple-single-virtual-machine"></a>1 níveis (simples): Máquina virtual única
Neste padrão de aplicação, implementa a aplicação e base de dados do SQL Server para uma máquina virtual autónoma em Azure. A mesma máquina virtual contém a sua aplicação cliente/web, componentes de negócios, camada de acesso a dados e o servidor de base de dados. O código de apresentação, negócios e acesso a dados está logicamente separado, mas estão fisicamente localizados numa máquina de servidor único. A maioria dos clientes começa com este padrão de aplicação e, em seguida, escalam adicionando mais funções web ou máquinas virtuais ao seu sistema.

Este padrão de aplicação é útil quando:

* Pretende realizar uma simples migração para a plataforma Azure para avaliar se a plataforma responde ou não aos requisitos da sua aplicação.
* Pretende manter todos os níveis de aplicação alojados na mesma máquina virtual no mesmo centro de dados Azure para reduzir a latência entre os níveis.
* Pretende-se que se provisa rapidamente ambientes de desenvolvimento e teste por curtos períodos de tempo.
* Pretende realizar testes de stress para diferentes níveis de carga de trabalho, mas ao mesmo tempo não quer possuir e manter muitas máquinas físicas a toda a hora.

O diagrama seguinte demonstra um cenário simples no local e como pode implementar a sua solução ativada em nuvem numa única máquina virtual em Azure.

![Padrão de aplicação de 1 níveis](./media/application-patterns-development-strategies/IC728008.png)

A implantação da camada de negócio (lógica de negócio e componentes de acesso a dados) no mesmo nível físico que a camada de apresentação pode maximizar o desempenho da aplicação, a menos que você deve usar um nível separado devido a problemas de escalabilidade ou segurança.

Uma vez que este é um padrão muito comum para começar, você pode encontrar o seguinte artigo sobre migração útil para mover os seus dados para o seu SQL Server VM: [Migrar uma Base de Dados para SQL Server em um VM Azure](migrate-to-vm-from-sql-server.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>3 níveis (simples): Múltiplas máquinas virtuais
Neste padrão de aplicação, implementa-se uma aplicação de 3 níveis no Azure colocando cada nível de aplicação numa máquina virtual diferente. Isto proporciona um ambiente flexível para cenários fáceis de escalar e escalar. Quando uma máquina virtual contém a sua aplicação cliente/web, a outra acolhe os seus componentes de negócio, e a outra hospeda o servidor de base de dados.

Este padrão de aplicação é útil quando:

* Pretende realizar uma migração de aplicações complexas de base de dados para máquinas virtuais Azure.
* Pretende que diferentes níveis de aplicação sejam hospedados em diferentes regiões. Por exemplo, pode ter bases de dados partilhadas que são implantadas em várias regiões para efeitos de reporte.
* Pretende mover aplicações empresariais de plataformas virtualizadas no local para Azure Virtual Machines. Para uma discussão detalhada sobre aplicações empresariais, consulte [O que é uma Aplicação Empresarial.](https://msdn.microsoft.com/library/aa267045.aspx)
* Pretende-se que se provisa rapidamente ambientes de desenvolvimento e teste por curtos períodos de tempo.
* Pretende realizar testes de stress para diferentes níveis de carga de trabalho, mas ao mesmo tempo não quer possuir e manter muitas máquinas físicas a toda a hora.

O diagrama que se segue demonstra como é possível colocar uma aplicação simples de 3 níveis no Azure, colocando cada nível de aplicação numa máquina virtual diferente.

![Padrão de aplicação de 3 níveis](./media/application-patterns-development-strategies/IC728009.png)

Neste padrão de aplicação, existe apenas uma máquina virtual em cada nível. Se tiver vários VMs em Azure, recomendamos que crie uma rede virtual. [A Rede Virtual Azure](../../../virtual-network/virtual-networks-overview.md) cria um limite de segurança confiável e também permite que os VMs se comuniquem entre si através do endereço IP privado. Além disso, certifique-se sempre de que todas as ligações à Internet vão apenas para o nível de apresentação. Ao seguir este padrão de aplicação, gerencie as regras do grupo de segurança da rede para controlar o acesso. Para obter mais informações, consulte [Permitir o acesso externo ao seu VM utilizando o portal Azure.](../../../virtual-machines/windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

No diagrama, os protocolos de Internet podem ser TCP, UDP, HTTP ou HTTPS.

> [!NOTE]
> A criação de uma rede virtual em Azure é gratuita. No entanto, você é cobrado para o gateway VPN que se conecta ao local. Esta taxa baseia-se no tempo que a ligação é a provisionada e disponível.
> 
> 

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>2 níveis e 3 níveis com escala de nível de apresentação
Neste padrão de aplicação, implementa uma aplicação de base de dados de 2 níveis ou 3 níveis para máquinas virtuais Azure, colocando cada nível de aplicação numa máquina virtual diferente. Além disso, escalona o nível de apresentação devido ao aumento do volume de pedidos de clientes que chegam.

Este padrão de aplicação é útil quando:

* Pretende mover aplicações empresariais de plataformas virtualizadas no local para Azure Virtual Machines.
* Pretende aumentar o nível de apresentação devido ao aumento do volume de pedidos de clientes que chegam.
* Pretende-se que se provisa rapidamente ambientes de desenvolvimento e teste por curtos períodos de tempo.
* Pretende realizar testes de stress para diferentes níveis de carga de trabalho, mas ao mesmo tempo não quer possuir e manter muitas máquinas físicas a toda a hora.
* Quer possuir um ambiente de infraestrutura que possa aumentar e descer a pedido.

O diagrama seguinte demonstra como pode colocar os níveis de aplicação em várias máquinas virtuais em Azure, escalando o nível de apresentação devido ao aumento do volume de pedidos de clientes que chegam. Como visto no diagrama, o Azure Load Balancer é responsável por distribuir tráfego por várias máquinas virtuais e também determinar a que servidor web se ligar. Ter múltiplas instâncias dos servidores web por trás de um balanceador de carga garante a alta disponibilidade do nível de apresentação.

![Padrão de aplicação - escala de nível de apresentação](./media/application-patterns-development-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Melhores práticas para padrões de 2 níveis, 3 níveis ou n-tier que têm múltiplos VMs em um nível
Recomenda-se que coloque as máquinas virtuais que pertencem ao mesmo nível no mesmo serviço de nuvem e no mesmo conjunto de disponibilidade. Por exemplo, coloque um conjunto de servidores web no **CloudService1** e **no AvailabilitySet1** e num conjunto de servidores de bases de dados no **CloudService2** e **no AvailabilitySet2**. Uma disponibilidade definida no Azure permite-lhe colocar os nós de alta disponibilidade em domínios de avaria separados e fazer upgrade de domínios.

Para aproveitar várias instâncias VM de um nível, é necessário configurar o Balançador de Carga Azure entre os níveis de aplicação. Para configurar o Balanceador de Carga em cada nível, crie separadamente um ponto final equilibrado em carga nos VMs de cada nível. Para um nível específico, primeiro crie VMs no mesmo serviço de nuvem. Isto garante que possuem o mesmo endereço IP virtual público. Em seguida, crie um ponto final numa das máquinas virtuais daquele nível. Em seguida, atribua o mesmo ponto final às outras máquinas virtuais do nível para equilibrar a carga. Ao criar um conjunto equilibrado de carga, distribui tráfego por várias máquinas virtuais e também permite que o Balanceador de Carga determine qual o nó a ligar quando um nó VM de backend falha. Por exemplo, ter várias instâncias dos servidores web por trás de um balanceador de carga garante a alta disponibilidade do nível de apresentação.

Como uma boa prática, certifique-se sempre de que todas as ligações à Internet vão primeiro para o nível de apresentação. A camada de apresentação acessa o nível de negócio, e então o nível de negócio acessa o nível de dados. Para obter mais informações sobre como permitir o acesso à camada de apresentação, consulte Permitir o [acesso externo ao seu VM utilizando o portal Azure](../../../virtual-machines/windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Note que o Balanceador de Carga em Azure funciona semelhante aos equilibradores de carga num ambiente no local. Para obter mais informações, consulte [o equilíbrio de carga para os serviços de infraestrutura Azure.](../../../virtual-machines/windows/tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Além disso, recomendamos que crie uma rede privada para as suas máquinas virtuais utilizando a Rede Virtual Azure. Isto permite-lhes comunicar entre si através do endereço IP privado. Para mais informações, consulte [a Rede Virtual Azure.](../../../virtual-network/virtual-networks-overview.md)

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>2 níveis e 3 níveis com escala de nível de negócio
Neste padrão de aplicação, implementa uma aplicação de base de dados de 2 níveis ou 3 níveis para as Máquinas Virtuais Azure, colocando cada nível de aplicação numa máquina virtual diferente. Além disso, é melhor distribuir os componentes do servidor de aplicações a várias máquinas virtuais devido à complexidade da sua aplicação.

Este padrão de aplicação é útil quando:

* Pretende mover aplicações empresariais de plataformas virtualizadas no local para Azure Virtual Machines.
* Pretende distribuir os componentes do servidor de aplicações a várias máquinas virtuais devido à complexidade da sua aplicação.
* Você quer mover aplicações pesadas de lógica de negócios lob (linha de negócios) para Azure Virtual Machines. As aplicações LOB são um conjunto de aplicações de computador críticas que são vitais para a gestão de uma empresa, tais como contabilidade, recursos humanos (RH), folha de pagamento, gestão da cadeia de fornecimento e aplicações de planeamento de recursos.
* Pretende-se que se provisa rapidamente ambientes de desenvolvimento e teste por curtos períodos de tempo.
* Pretende realizar testes de stress para diferentes níveis de carga de trabalho, mas ao mesmo tempo não quer possuir e manter muitas máquinas físicas a toda a hora.
* Quer possuir um ambiente de infraestrutura que possa aumentar e descer a pedido.

O diagrama seguinte demonstra um cenário no local e a sua solução ativada pela nuvem. Neste cenário, coloca-se os níveis de aplicação em várias máquinas virtuais em Azure, escalando o nível de negócio, que contém o nível de lógica de negócio e componentes de acesso a dados. Como visto no diagrama, o Azure Load Balancer é responsável por distribuir tráfego por várias máquinas virtuais e também determinar a que servidor web se ligar. Ter vários casos dos servidores de aplicações por trás de um balanceador de carga garante a elevada disponibilidade do nível de negócio. Para obter mais informações, consulte [as melhores práticas para padrões de aplicação de 2 níveis, 3 níveis ou n-tier que tenham várias máquinas virtuais num só nível](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Padrão de aplicação com escala de nível de negócio](./media/application-patterns-development-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>2 níveis e 3 níveis com apresentação e níveis de negócio escala-out e HADR
Neste padrão de aplicação, implementa uma aplicação de base de dados de 2 níveis ou 3 níveis para as Máquinas Virtuais Azure distribuindo o nível de apresentação (servidor web) e os componentes de nível de negócio (servidor de aplicações) para várias máquinas virtuais. Além disso, implementa soluções de alta disponibilidade e recuperação de desastres (HADR) para as suas bases de dados em Azure Virtual Machines.

Este padrão de aplicação é útil quando:

* Pretende mover aplicações empresariais de plataformas virtualizadas no local para a Azure, implementando capacidades de alta disponibilidade e recuperação de desastres do SQL Server.
* Pretende aumentar o nível de apresentação e o nível de negócio devido ao aumento do volume de pedidos de clientes recebidos e à complexidade da sua aplicação.
* Pretende-se que se provisa rapidamente ambientes de desenvolvimento e teste por curtos períodos de tempo.
* Pretende realizar testes de stress para diferentes níveis de carga de trabalho, mas ao mesmo tempo não quer possuir e manter muitas máquinas físicas a toda a hora.
* Quer possuir um ambiente de infraestrutura que possa aumentar e descer a pedido.

O diagrama seguinte demonstra um cenário no local e a sua solução ativada pela nuvem. Neste cenário, escalou o nível de apresentação e os componentes de nível de negócio em várias máquinas virtuais em Azure. Além disso, implementa técnicas de alta disponibilidade e recuperação de desastres (HADR) para bases de dados sql server em Azure.

Executando várias cópias de uma aplicação em VMs diferentes certifique-se de que você está a carregar pedidos de equilíbrio através deles. Quando tiver várias máquinas virtuais, tem de se certificar de que todos os seus VMs estão acessíveis e a funcionar a dada altura. Se configurar o equilíbrio de carga, o Azure Load Balancer rastreia a saúde dos VMs e direciona as chamadas recebidas para os nós VM funcionais saudáveis corretamente. Para obter informações sobre como configurar o equilíbrio de carga das máquinas virtuais, consulte o [equilíbrio de carga para os serviços de infraestrutura Azure](../../../virtual-machines/windows/tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ter múltiplas instâncias de servidores web e de aplicações atrás de um balanceador de carga garante a elevada disponibilidade dos níveis de apresentação e negócio.

![Escala e alta disponibilidade](./media/application-patterns-development-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Melhores práticas para padrões de aplicação que requerem HADR SQL
Quando configura soluções de alta disponibilidade e recuperação de desastres no SQL Server em Azure Virtual Machines, a criação de uma rede virtual para as suas máquinas virtuais utilizando [a Rede Virtual Azure](../../../virtual-network/virtual-networks-overview.md) é obrigatória. As máquinas virtuais dentro de uma Rede Virtual terão um endereço IP privado estável mesmo após um tempo de inatividade de serviço, evitando assim o tempo de atualização necessário para a resolução do nome DNS. Além disso, a rede virtual permite-lhe estender a sua rede no local até Azure e cria uma fronteira de segurança fidedigna. Por exemplo, se a sua aplicação tiver restrições de domínio corporativo (como, autenticação do Windows, Diretório Ativo), é necessário configurar [a Rede Virtual Azure.](../../../virtual-network/virtual-networks-overview.md)

A maioria dos clientes, que estão a executar o código de produção no Azure, estão a manter réplicas primárias e secundárias em Azure.

Para obter informações e tutoriais completos sobre técnicas de alta disponibilidade e recuperação de [desastres, consulte Alta Disponibilidade e Recuperação de Desastres para O SQL Server em Máquinas Virtuais Azure](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="2-tier-and-3-tier-using-azure-virtual-machines-and-cloud-services"></a>2 níveis e 3 níveis usando máquinas virtuais Azure e serviços cloud
Neste padrão de aplicação, implementa uma aplicação de 2 níveis ou 3 níveis para o Azure, utilizando tanto os [Serviços de Nuvem Azure](../../../cloud-services/cloud-services-choose-me.md) (funções web e trabalhadora - Plataforma como Serviço (PaaS)) e [Máquinas Virtuais Azure](../../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Infraestrutura como Serviço (IaaS)). A utilização [de Serviços em Nuvem Azure](https://azure.microsoft.com/documentation/services/cloud-services/) para o nível de apresentação/nível de negócio e o SQL Server em [Azure Virtual Machines](../../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para o nível de dados é benéfico para a maioria das aplicações em execução no Azure. A razão é que ter um caso de computação em execução em Cloud Services fornece uma gestão, implementação, monitorização e escala fácil.

Com a Cloud Services, o Azure mantém a infraestrutura para si, realiza manutenção de rotina, corrige os sistemas operativos e tenta recuperar de falhas de serviço e hardware. Quando a sua aplicação necessita de opções de escala, automática e manual para o seu projeto de serviço na nuvem, aumentando ou diminuindo o número de casos ou máquinas virtuais que são utilizadas pela sua aplicação. Além disso, pode utilizar o Visual Studio no local para implementar a sua aplicação num projeto de serviço em nuvem em Azure.

Em resumo, se não pretender possuir tarefas administrativas extensivas para o nível de apresentação/negócio e a sua aplicação não necessitar de qualquer configuração complexa de software ou sistema operativo, utilize os Azure Cloud Services. Se o Azure SQL Database não suportar todas as funcionalidades que procura, utilize o SQL Server numa máquina virtual Azure para o nível de dados. Executar uma aplicação no Azure Cloud Services e armazenar dados em Azure Virtual Machines combina os benefícios de ambos os serviços. Para uma comparação detalhada, consulte a secção neste tópico sobre [comparar estratégias de desenvolvimento em Azure.](#comparing-web-development-strategies-in-azure)

Neste padrão de aplicação, o nível de apresentação inclui uma função web, que é uma componente de Cloud Services em execução no ambiente de execução Azure e é personalizado para programação de aplicações web como suportado pelo IIS e ASP.NET. O nível de negócio ou backend inclui um papel de trabalhador, que é uma componente de Cloud Services em execução no ambiente de execução Azure e é útil para o desenvolvimento generalizado, e pode executar o processamento de fundo para um papel web. O nível de base de dados reside numa máquina virtual SQL Server em Azure. A comunicação entre o nível de apresentação e o nível de base de dados acontece diretamente ou sobre o nível de negócio – componentes de função dos trabalhadores.

Este padrão de aplicação é útil quando:

* Pretende mover aplicações empresariais de plataformas virtualizadas no local para a Azure, implementando capacidades de alta disponibilidade e recuperação de desastres do SQL Server.
* Quer possuir um ambiente de infraestrutura que possa aumentar e descer a pedido.
* A Azure SQL Database não suporta todas as funcionalidades de que a sua aplicação ou base de dados necessita.
* Pretende realizar testes de stress para diferentes níveis de carga de trabalho, mas ao mesmo tempo não quer possuir e manter muitas máquinas físicas a toda a hora.

O diagrama seguinte demonstra um cenário no local e a sua solução ativada pela nuvem. Neste cenário, coloca-se o nível de apresentação em funções web, o nível de negócio nas funções dos trabalhadores, mas o nível de dados em máquinas virtuais em Azure. Executar várias cópias do nível de apresentação em diferentes funções web garante carregar pedidos de equilíbrio através deles. Ao combinar serviços em nuvem Azure com máquinas virtuais Azure, recomendamos que crie também [a Rede Virtual Azure.](../../../virtual-network/virtual-networks-overview.md) Com [a Rede Virtual Azure,](../../../virtual-network/virtual-networks-overview.md)pode ter endereços IP privados estáveis e persistentes dentro do mesmo serviço de nuvem na nuvem. Uma vez que você define uma rede virtual para as suas máquinas virtuais e serviços na nuvem, eles podem começar a comunicar entre si sobre o endereço IP privado. Além disso, ter máquinas virtuais e funções web/trabalhador Azure na mesma [Rede Virtual Azure](../../../virtual-network/virtual-networks-overview.md) proporciona baixa latência e conectividade mais segura. Para mais informações, consulte [O que é um serviço de nuvem.](../../../cloud-services/cloud-services-choose-me.md)

Como visto no diagrama, o Azure Load Balancer distribui o tráfego por várias máquinas virtuais e também determina a que servidor web ou servidor de aplicações se deve ligar. Ter múltiplas instâncias dos servidores web e de aplicações por trás de um balanceador de carga garante a alta disponibilidade do nível de apresentação e do nível de negócio. Para obter mais informações, consulte [as melhores práticas para padrões de aplicação que requerem HADR SQL](#best-practices-for-application-patterns-requiring-sql-hadr).

![Padrões de aplicação com serviços em nuvem](./media/application-patterns-development-strategies/IC728013.png)

Outra abordagem para implementar este padrão de aplicação é usar uma função web consolidada que contenha componentes de nível de apresentação e de nível de negócio, como mostrado no diagrama seguinte. Este padrão de aplicação é útil para aplicações que requerem design imponente. Uma vez que a Azure fornece nós de computação apátridas nas funções web e trabalhadora, recomendamos que implemente uma lógica para armazenar o estado da sessão utilizando uma das seguintes tecnologias: [Azure Caching,](https://azure.microsoft.com/documentation/services/azure-cache-for-redis/) [Azure Table Storage](../../../cosmos-db/table-storage-how-to-use-dotnet.md) ou [Azure SQL Database](../../database/sql-database-paas-overview.md).

![Padrões de aplicação com serviços em nuvem](./media/application-patterns-development-strategies/IC728014.png)

## <a name="pattern-with-azure-virtual-machines-azure-sql-database-and-azure-app-service-web-apps"></a>Padrão com máquinas virtuais Azure, Base de Dados Azure SQL e Serviço de Aplicações Azure (Web Apps)
O objetivo principal deste padrão de aplicação é mostrar-lhe como combinar a infraestrutura Azure como componentes de serviço (IaaS) com componentes de plataforma-as-a-service (PaaS) da Azure na sua solução. Este padrão está focado na Base de Dados Azure SQL para armazenamento relacional de dados. Não inclui o SQL Server numa máquina virtual Azure, que faz parte da infraestrutura Azure como uma oferta de serviços.

Neste padrão de aplicação, implementa uma aplicação de base de dados para o Azure colocando os níveis de apresentação e negócios na mesma máquina virtual e acedendo a uma base de dados nos servidores Azure SQL Database (SQL Database). Pode implementar o nível de apresentação utilizando soluções web tradicionais baseadas em IIS. Ou, você pode implementar um nível de apresentação combinado e nível de negócio usando [o Azure App Service](https://azure.microsoft.com/documentation/services/app-service/web/).

Este padrão de aplicação é útil quando:

* Já tem um servidor sql database existente configurado no Azure e pretende testar a sua aplicação rapidamente.
* Quer testar as capacidades do ambiente Azure.
* Pretende-se que se provisa rapidamente ambientes de desenvolvimento e teste por curtos períodos de tempo.
* A sua lógica de negócio e os componentes de acesso a dados podem ser autossuficientes dentro de uma aplicação web.

O diagrama seguinte demonstra um cenário no local e a sua solução ativada pela nuvem. Neste cenário, coloca os níveis de aplicação numa única máquina virtual em Azure e acede aos dados na Base de Dados Azure SQL.

![Padrão de aplicação misto](./media/application-patterns-development-strategies/IC728015.png)

Se optar por implementar um nível combinado de web e aplicação utilizando aplicações Web Azure, recomendamos que mantenha o nível intermédio ou de aplicação como bibliotecas de ligações dinâmicas (DLLs) no contexto de uma aplicação web.

Além disso, reveja as recomendações dadas nas [estratégias de desenvolvimento da Web comparando na secção Azure](#comparing-web-development-strategies-in-azure) no final deste artigo para saber mais sobre técnicas de programação.

## <a name="n-tier-hybrid-application-pattern"></a>Padrão de aplicação híbrida de nível N
No padrão de aplicação híbrida n-tier, implementa a sua aplicação em vários níveis distribuídos entre as instalações e o Azure. Por isso, cria-se um sistema híbrido flexível e reutilizável, que pode modificar ou adicionar um nível específico sem alterar os outros níveis. Para estender a sua rede corporativa à nuvem, utiliza o serviço [Azure Virtual Network.](../../../virtual-network/virtual-networks-overview.md)

Este padrão de aplicação híbrida é útil quando:

* Você quer construir aplicações que funcionam em parte na nuvem e em parte no local.
* Você quer migrar alguns ou todos os elementos de uma aplicação existente no local para a nuvem.
* Pretende mover aplicações empresariais de plataformas virtualizadas no local para a Azure.
* Quer possuir um ambiente de infraestrutura que possa aumentar e descer a pedido.
* Pretende-se que se provisa rapidamente ambientes de desenvolvimento e teste por curtos períodos de tempo.
* Pretende uma forma eficaz de fazer backups para aplicações de base de dados empresariais.

O diagrama seguinte demonstra um padrão de aplicação híbrida n-tier que se estende por todo o local e Azure. Como mostrado no diagrama, a infraestrutura no local inclui o controlador de domínio [do Ative Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) para apoiar a autenticação e autorização do utilizador. Note que o diagrama demonstra um cenário, onde algumas partes do nível de dados vivem num centro de dados no local, enquanto algumas partes do nível de dados vivem em Azure. Dependendo das necessidades da sua aplicação, pode implementar vários outros cenários híbridos. Por exemplo, pode manter o nível de apresentação e o nível de negócio num ambiente no local, mas o nível de dados em Azure.

![Padrão de aplicação n-tier](./media/application-patterns-development-strategies/IC728016.png)

No Azure, pode utilizar o Ative Directory como diretório de nuvem autónomo para a sua organização, ou também pode integrar o Ative Directory existente no local com [o Azure Ative Directory.](https://azure.microsoft.com/documentation/services/active-directory/) Como visto no diagrama, os componentes de nível de negócio podem aceder a múltiplas fontes de dados, tais como [o SQL Server em Azure](sql-server-on-azure-vm-iaas-what-is-overview.md) através de um endereço IP interno privado, para o SqL Server no local através [da Rede Virtual Azure,](../../../virtual-network/virtual-networks-overview.md)ou para a [Base de Dados SQL](../../database/sql-database-paas-overview.md) utilizando as tecnologias de fornecedor de dados .NET Framework. Neste diagrama, a Base de Dados Azure SQL é um serviço opcional de armazenamento de dados.

No padrão de aplicação híbrida n-nível, pode implementar o seguinte fluxo de trabalho na ordem especificada:

1. Identifique as aplicações de base de dados empresariais que precisam de ser movidas para a nuvem utilizando o [Kit de Ferramentas de Avaliação e Planeamento (MAP)](https://microsoft.com/map)da Microsoft. O MAP Toolkit recolhe dados de inventário e desempenho de computadores que está a considerar para virtualização e fornece recomendações sobre capacidade e planeamento de avaliação.
2. Planeie os recursos e configuração necessários na plataforma Azure, como contas de armazenamento e máquinas virtuais.
3. Confiúdão da conectividade de rede entre a rede corporativa no local e [a Rede Virtual Azure.](../../../virtual-network/virtual-networks-overview.md) Para estabelecer a ligação entre a rede corporativa no local e uma máquina virtual em Azure, utilize um dos dois métodos seguintes:
   
   1. Estabeleça uma ligação entre as instalações e a Azure através de pontos finais públicos numa máquina virtual em Azure. Este método proporciona uma configuração fácil e permite-lhe utilizar a autenticação do SQL Server na sua máquina virtual. Além disso, crie as regras do seu grupo de segurança de rede para controlar o tráfego público para o VM. Para obter mais informações, consulte [Permitir o acesso externo ao seu VM utilizando o portal Azure.](../../../virtual-machines/windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
   2. Estabeleça uma ligação entre o túnel da rede virtual privada Azure (VPN). Este método permite-lhe estender as políticas de domínio a uma máquina virtual em Azure. Além disso, pode configurar regras de firewall e utilizar a autenticação do Windows na sua máquina virtual. Atualmente, a Azure suporta ligações VPN locais seguras e VPN ponto-a-local:
      
      * Com uma ligação local-local segura, pode estabelecer conectividade de rede entre a sua rede no local e a sua rede virtual em Azure. É aconselhável ligar o ambiente do centro de dados no local ao Azure.
      * Com uma ligação segura ponto-a-local, pode estabelecer conectividade de rede entre a sua rede virtual em Azure e os seus computadores individuais a funcionar em qualquer lugar. Recomenda-se principalmente para fins de desenvolvimento e teste.
      
      Para obter informações sobre como ligar ao SQL Server em Azure, consulte [Connect to a SQL Server virtual machine on Azure](ways-to-connect-to-sql.md).
4. Crie postos de trabalho programados e alertas que retravam dados no local num disco de máquina virtual em Azure. Para obter mais informações, consulte [o SQL Server Backup and Restore with Azure Blob storage service](https://msdn.microsoft.com/library/jj919148.aspx) and Backup and Restore for [SQL Server on Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/backup-restore.md).
5. Dependendo das necessidades da sua aplicação, pode implementar um dos três cenários comuns seguintes:
   
   1. Pode manter o seu servidor web, servidor de aplicações e dados insensíveis num servidor de base de dados em Azure, enquanto mantém os dados sensíveis no local.
   2. Pode manter o servidor web e o servidor de aplicações no local, enquanto o servidor de base de dados numa máquina virtual em Azure.
   3. Pode manter o servidor de base de dados, o servidor web e o servidor de aplicações no local, enquanto mantém as réplicas da base de dados em máquinas virtuais em Azure. Esta definição permite que os servidores web ou aplicações de reporte no local acedam às réplicas da base de dados em Azure. Portanto, pode conseguir reduzir a carga de trabalho numa base de dados no local. Recomendamos que implemente este cenário para trabalhos de trabalho de leitura pesada e finalidades de desenvolvimento. Para obter informações sobre a criação de réplicas de bases de dados em Azure, consulte os Grupos de Disponibilidade AlwaysOn em [Alta Disponibilidade e Recuperação de Desastres para O Servidor SQL em Máquinas Virtuais Azure](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="comparing-web-development-strategies-in-azure"></a>Comparar estratégias de desenvolvimento web em Azure
Para implementar e implementar uma aplicação baseada em servidor SQL de vários níveis no Azure, pode utilizar um dos dois seguintes métodos de programação:

* Crie um servidor web tradicional (IIS - Serviços de Informação na Internet) em Azure e aceda bases de dados no SQL Server em Azure Virtual Machines.
* Implementar e implementar um serviço de nuvem para Azure. Em seguida, certifique-se de que este serviço de nuvem pode aceder a bases de dados no SQL Server em Azure Virtual Machines. Um serviço de nuvem pode incluir vários papéis web e trabalhador.

A tabela seguinte fornece uma comparação do desenvolvimento web tradicional com Azure Cloud Services e Azure Web Apps no que diz respeito ao SQL Server em Azure Virtual Machines. A tabela inclui aplicações web Azure, uma vez que é possível utilizar o SQL Server num Azure VM como fonte de dados para aplicações web Azure através do seu endereço IP virtual público ou nome DNS.

|  | Desenvolvimento web tradicional em Azure Virtual Machines | Serviços em nuvem em Azure | Hospedagem web com aplicações web Azure |
| --- | --- | --- | --- |
| **Migração de aplicações a partir de instalações** |Aplicações existentes como está. |As aplicações precisam de funções web e de trabalhadores. |As aplicações existentes são, mas adequadas para aplicações web autossuficientes e serviços web que requerem uma rápida escalabilidade. |
| **Desenvolvimento e implantação** |Visual Studio, WebMatrix, Visual Web Developer, WebDeploy, FTP, TFS, IIS Manager, PowerShell. |Estúdio visual, Azure SDK, TFS, Powershell. Cada serviço em nuvem tem dois ambientes aos quais pode implementar o seu pacote de serviço e configuração: encenação e produção. Pode implantar um serviço de nuvem no ambiente de preparação para testá-lo antes de o promover à produção. |Visual Studio, WebMatrix, Visual Web Developer, FTP, GIT, BitBucket, CodePlex, DropBox, GitHub, Mercurial, TFS, Web Deploy, PowerShell. |
| **Administração e instalação** |É responsável por tarefas administrativas sobre a aplicação, dados, regras de firewall, rede virtual e sistema operativo. |É responsável por tarefas administrativas sobre a aplicação, dados, regras de firewall e rede virtual. |É responsável por tarefas administrativas apenas na aplicação e dados. |
| **Alta disponibilidade e recuperação de desastres (HADR)** |Recomendamos que coloque máquinas virtuais no mesmo conjunto de disponibilidade e no mesmo serviço de nuvem. Manter os seus VMs no mesmo conjunto de disponibilidade permite ao Azure colocar os nós de alta disponibilidade em domínios de avaria separados e domínios de atualização. Da mesma forma, manter os seus VMs no mesmo serviço de nuvem permite equilibrar a carga e os VMs podem comunicar diretamente uns com os outros através da rede local dentro de um centro de dados Azure.<br/><br/>Você é responsável por implementar uma solução de alta disponibilidade e recuperação de desastres para o SQL Server em Azure Virtual Machines para evitar qualquer tempo de inatividade. Para tecnologias HADR suportadas, consulte [Alta Disponibilidade e Recuperação de Desastres para O Servidor SQL em Máquinas Virtuais Azure](business-continuity-high-availability-disaster-recovery-hadr-overview.md).<br/><br/>Você é responsável por fazer o backup dos seus próprios dados e aplicação.<br/><br/>O Azure pode mover as suas máquinas virtuais se a máquina hospedeira do centro de dados falhar devido a problemas de hardware. Além disso, poderá ser planeada a inatividade do seu VM quando a máquina hospedeira for atualizada para atualizações de segurança ou software. Por isso, recomendamos que mantenha pelo menos dois VMs em cada nível de aplicação para garantir a disponibilidade contínua. O Azure não fornece SLA para uma única máquina virtual. |O Azure gere as falhas resultantes do hardware ou software do sistema operativo subjacente. Recomendamos que implemente várias instâncias de uma função web ou de trabalhador para garantir a elevada disponibilidade da sua aplicação. Para obter informações, consulte [serviços em nuvem, máquinas virtuais e Acordo de Nível de Serviço de Rede Virtual.](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)<br/><br/>Você é responsável por fazer o backup dos seus próprios dados e aplicação.<br/><br/>Para bases de dados residentes numa base de dados do SQL Server num VM Azure, é responsável pela implementação de uma solução de alta disponibilidade e recuperação de desastres para evitar qualquer inatividade. Para tecnologias HDAR suportadas, consulte Alta Disponibilidade e Recuperação de Desastres para O Servidor SQL em Máquinas Virtuais Azure.<br/><br/>**Sql Server Database Mirroring**: Use com Azure Cloud Services (funções web/trabalhador). SQL Server VMs e um projeto de serviço em nuvem podem estar na mesma Rede Virtual Azure. Se o SQL Server VM não estiver na mesma Rede Virtual, é necessário criar um Alias de Servidor SQL para encaminhar a comunicação para o caso do SQL Server. Além disso, o nome do pseudónimo deve corresponder ao nome SQL Server. |A Alta Disponibilidade é herdada das funções de trabalhador da Azure, do armazenamento de blob Azure e da Base de Dados Azure SQL. Por exemplo, o Azure Storage mantém três réplicas de todos os dados de bolhas, tabelas e filas. A qualquer momento, a Base de Dados Azure SQL mantém três réplicas de dados em execução - uma réplica primária e duas réplicas secundárias. Para mais informações, consulte [a Azure Storage](https://azure.microsoft.com/documentation/services/storage/) e [a Azure SQL Database](../../database/sql-database-paas-overview.md).<br/><br/>Ao utilizar o SQL Server num Azure VM como fonte de dados para aplicações web Azure, lembre-se que as Aplicações Web Azure não suportam a Rede Virtual Azure. Por outras palavras, todas as ligações desde Azure Web Apps a SQL Server VMs em Azure devem passar por pontos finais públicos de máquinas virtuais. Isto pode causar algumas limitações para cenários de alta disponibilidade e recuperação de desastres. Por exemplo, a aplicação do cliente em Azure Web Apps que se conectam ao SQL Server VM com o espelhamento da base de dados não seria capaz de se ligar ao novo servidor primário, uma vez que o espelhamento da base de dados requer que crie a Rede Virtual Azure entre VMs do anfitrião sql server em Azure. Portanto, a utilização **de Sql Server Database Mirroring** com Azure Web Apps não é suportada atualmente.<br/><br/>**Sql Server AlwaysOn Availability Groups**: Pode configurar grupos de disponibilidade AlwaysOn quando utilizar aplicações web Azure com VMs do servidor SQL em Azure. Mas é necessário configurar o AlwaysOn Availability Group Listener para encaminhar a comunicação para a réplica primária através de pontos finais equilibrados em carga pública. |
| **Conectividade entre vários locais** |Pode utilizar a Rede Virtual Azure para se ligar ao local. |Pode utilizar a Rede Virtual Azure para se ligar ao local. |A Rede Virtual Azure é suportada. Para obter mais informações, consulte [a Integração de Redes Virtuais de Aplicações Web.](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/) |
| **Escalabilidade** |A escala está disponível aumentando os tamanhos de máquinas virtuais ou adicionando mais discos. Para obter mais informações sobre tamanhos de máquinas virtuais, consulte [tamanhos de máquina virtual para Azure.](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)<br/><br/>**Para o Servidor de Bases**de Dados : O scale-out está disponível através de técnicas de partição de bases de dados e grupos SQL Server AlwaysOn Availability.<br/><br/>Para cargas de trabalho de leitura pesada, pode utilizar [grupos de disponibilidade AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx) em vários nós secundários, bem como replicação do servidor SQL.<br/><br/>Para cargas de trabalho de escrita pesada, pode implementar dados de partição horizontal em vários servidores físicos para fornecer a escala de aplicação.<br/><br/>Além disso, pode implementar uma escala-out utilizando [o SQL Server com o Encaminhamento Dependente de Dados](https://technet.microsoft.com/library/cc966448.aspx). Com o Data Dependent Encaminhing (DDR), é necessário implementar o mecanismo de partição na aplicação do cliente, tipicamente na camada de nível de negócio, para encaminhar os pedidos de base de dados para vários nós do SQL Server. O nível de negócio contém mapeamentos de como os dados são divididos e qual nó contém os dados.<br/><br/>Pode escalar aplicações que estão a executar máquinas virtuais. Para mais informações, consulte [Como Escalar uma Aplicação.](../../../cloud-services/cloud-services-how-to-scale-portal.md)<br/><br/>**Nota importante**: A **função AutoScale** em Azure permite-lhe aumentar ou diminuir automaticamente as máquinas virtuais que são utilizadas pela sua aplicação. Esta funcionalidade garante que a experiência do utilizador final não é afetada negativamente durante os períodos de pico e que os VM são desligados quando a procura é baixa. Recomenda-se que não desconte a opção AutoScale para o seu serviço na nuvem se incluir VMs do Servidor SQL. A razão é que a funcionalidade AutoScale permite ao Azure ligar uma máquina virtual quando a utilização do CPU nesse VM é superior a algum limiar, e desligar uma máquina virtual quando o uso do CPU for inferior ao seu. A funcionalidade AutoScale é útil para aplicações apátridas, como servidores web, onde qualquer VM pode gerir a carga de trabalho sem qualquer referência a qualquer estado anterior. No entanto, a funcionalidade AutoScale não é útil para aplicações estatais, como o SQL Server, onde apenas um caso permite escrever para a base de dados. |A escala está disponível utilizando várias funções web e trabalhadora. Para obter mais informações sobre tamanhos de máquinas virtuais para funções web e funções de trabalhador, consulte [Configure Sizes for Cloud Services](../../../cloud-services/cloud-services-sizes-specs.md).<br/><br/>Ao utilizar **o Cloud Services,** pode definir várias funções para distribuir o processamento e também obter um dimensionamento flexível da sua aplicação. Cada serviço na nuvem inclui uma ou mais funções web e/ou de trabalhador, cada uma com os seus próprios ficheiros de aplicação e configuração. Pode aumentar um serviço de nuvem aumentando o número de casos de funções (máquinas virtuais) implantados para um papel e reduzir um serviço de cloud diminuindo o número de casos de funções. Para obter informações detalhadas, consulte [os Modelos de Execução Azure](../../../cloud-services/cloud-services-choose-me.md).<br/><br/>Scale-out está disponível através de suporte de alta disponibilidade do Azure incorporado através [de Serviços cloud, máquinas virtuais e Acordo de Nível de Serviço de Rede Virtual](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) e Balanceador de Carga.<br/><br/>Para uma aplicação multi-nível, recomendamos que conecte a aplicação de funções web/trabalhador a VMs do servidor de base de dados através da Rede Virtual Azure. Além disso, o Azure fornece o equilíbrio de carga para VMs no mesmo serviço de nuvem, espalhando os pedidos do utilizador através deles. Máquinas virtuais ligadas desta forma podem comunicar-se diretamente entre si através da rede local dentro de um centro de dados Azure.<br/><br/>Pode configurar o **AutoScale** no portal Azure, bem como os horários. Para obter mais informações, consulte [Como configurar a escala automática para um Serviço de Cloud no portal](../../../cloud-services/cloud-services-how-to-scale-portal.md). |**Escalar para cima e para baixo**: Pode aumentar/diminuir o tamanho da instância (VM) reservada para o seu site.<br/><br/>Escala para fora: Pode adicionar mais instâncias reservadas (VMs) para o seu site.<br/><br/>Pode configurar **o AutoScale** no portal, bem como os horários. Para obter mais informações, consulte [Como escalar as aplicações web.](../../../app-service/manage-scale-up.md) |

Para obter mais informações sobre a escolha entre estes métodos de programação, consulte [Azure Web Apps, Cloud Services e VMs: Quando usar quais](/azure/architecture/guide/technology-choices/compute-decision-tree).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a execução do SQL Server em Máquinas Virtuais Azure, consulte [o SQL Server na Visão Geral das Máquinas Virtuais Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).

