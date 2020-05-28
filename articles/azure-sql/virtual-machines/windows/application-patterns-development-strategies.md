---
title: Padrões de aplicação do servidor SQL em VMs / Microsoft Docs
description: Este artigo abrange padrões de aplicação para O Servidor SQL em VMs Azure. Fornece a solução arquitetos e desenvolvedores uma base para uma boa arquitetura de aplicação e design.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 41863c8d-f3a3-4584-ad86-b95094365e05
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mathoma
ms.openlocfilehash: 209445378b71b4d6da2f40ea2744d3a2da0d8c45
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84046328"
---
# <a name="application-patterns-and-development-strategies-for-sql-server-in-azure-virtual-machines"></a>Padrões de Aplicação e Estratégias de Desenvolvimento para o SQL Server em Máquinas Virtuais do Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]





[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="summary"></a>Resumo:
Determinar que padrão de aplicação ou padrões a utilizar para as suas aplicações baseadas em SQL-Server em ambiente Azure é uma decisão de design importante e requer uma compreensão sólida de como o SQL Server e cada componente de infraestrutura do Azure funcionam em conjunto. Com o SQL Server nos Serviços de Infraestrutura Azure, pode migrar, manter e monitorizar facilmente as aplicações existentes do SQL Server incorporadas no Windows Server para máquinas virtuais em Azure.

O objetivo deste artigo é fornecer aos arquitetos e desenvolvedores uma base para uma boa arquitetura e design de aplicações, que podem seguir quando migrarem as aplicações existentes para o Azure, bem como desenvolver novas aplicações no Azure.

Para cada padrão de aplicação, encontrará um cenário no local, a sua respetiva solução ativada pela nuvem e as respetivas recomendações técnicas. Além disso, o artigo discute estratégias de desenvolvimento específicas do Azure para que possa projetar as suas aplicações corretamente. Devido aos muitos padrões de aplicação possíveis, recomenda-se que arquitetos e desenvolvedores escolham o padrão mais adequado para as suas aplicações e utilizadores.

**Contribuintes Técnicos:** Luis Carlos Vargas Herring, Madhan Arumugam Ramakrishnan

**Revisores Técnicos:** Corey Sanders, Drew McDaniel, Narayan Annamalai, Nir Mashkowski, Sanjay Mishra, Silvano Coriani, Stefan Schackow, Tim Hickey, Tim Wieman, Xin Jin

## <a name="introduction"></a>Introdução
Pode desenvolver muitos tipos de aplicações n-tier separando os componentes das diferentes camadas de aplicação em diferentes máquinas, bem como em componentes separados. Por exemplo, pode colocar os componentes da aplicação do cliente e das regras de negócio numa máquina, componentes front-end web e nível de acesso a dados noutra máquina, e um nível de base de dados de back-end noutra máquina. Este tipo de estruturação ajuda a isolar cada um dos níveis uns dos outros. Se alterar de onde vêm os dados, não precisa de alterar o cliente ou aplicação web, mas apenas os componentes do nível de acesso a dados.

Uma aplicação típica *n-tier* inclui o nível de apresentação, o nível de negócio, e o nível de dados:

| Escalão | Descrição |
| --- | --- |
| **Apresentação** |O *nível de apresentação* (nível web, nível frontal) é a camada em que os utilizadores interagem com uma aplicação. |
| **Empresa** |O *nível de negócio* (nível médio) é a camada que o nível de apresentação e o nível de dados utilizam para comunicar entre si e inclui a funcionalidade central do sistema. |
| **Dados** |O *nível de dados* é basicamente o servidor que armazena os dados de uma aplicação (por exemplo, um servidor que executa o Servidor SQL). |

As camadas de aplicação descrevem os agrupamentos lógicos da funcionalidade e componentes numa aplicação; enquanto os níveis descrevem a distribuição física da funcionalidade e dos componentes em servidores físicos separados, computadores, redes ou localizações remotas. As camadas de uma aplicação podem residir no mesmo computador físico (o mesmo nível) ou podem ser distribuídas por computadores separados (n-tier), e os componentes em cada camada comunicam com componentes noutras camadas através de interfaces bem definidas. Pode pensar no termo como referindo-se a padrões de distribuição física, tais como dois níveis, três níveis e n-tier. Um **padrão de aplicação de 2 camadas** contém dois níveis de aplicação: servidor de aplicações e servidor de base de dados. A comunicação direta ocorre entre o servidor de aplicações e o servidor de base de dados. O servidor de aplicações contém componentes de nível web e de nível de negócio. No **padrão de aplicação de 3 camadas,** existem três níveis de aplicação: servidor web, servidor de aplicações, que contém os componentes de acesso a dados de nível de lógica empresarial e/ou de nível de negócio, e o servidor de base de dados. A comunicação entre o servidor web e o servidor de base de dados acontece sobre o servidor de aplicações. Para obter informações detalhadas sobre camadas e camadas de aplicações, consulte o Guia de [Arquitetura da Aplicação da Microsoft](https://msdn.microsoft.com/library/ff650706.aspx).

Antes de começar a ler este artigo, deve ter conhecimento sobre os conceitos fundamentais do SQL Server e do Azure. Para obter informações, consulte [SQL Server Books Online,](https://msdn.microsoft.com/library/bb545450.aspx) [SQL Server em Máquinas Virtuais Azure](sql-server-on-azure-vm-iaas-what-is-overview.md) e [Azure.com](https://azure.microsoft.com/).

Este artigo descreve vários padrões de aplicação que podem ser adequados para as suas aplicações simples, bem como as aplicações empresariais altamente complexas. Antes de detalhar cada padrão, recomendamos que se familiarize com os serviços de armazenamento de dados disponíveis em Azure, tais como [Armazenamento Azure,](../../../storage/common/storage-introduction.md)Base de [Dados Azure SQL](../../database/sql-database-paas-overview.md)e [Servidor SQL numa Máquina Virtual Azure.](sql-server-on-azure-vm-iaas-what-is-overview.md) Para tomar as melhores decisões de design para as suas aplicações, compreenda quando utilizar claramente qual o serviço de armazenamento de dados.

### <a name="choose-sql-server-in-an-azure-virtual-machine-when"></a>Escolha o Servidor SQL numa máquina virtual Azure, quando:
* Precisa de controlo no SQL Server e no Windows. Por exemplo, isto pode incluir a versão SQL Server, hotfixes especiais, configuração de desempenho, etc.
* Você precisa de uma compatibilidade completa com o SQL Server no local e quer mover aplicações existentes para Azure as-is.
* Deseja aproveitar as capacidades do ambiente Azure, mas a Base de Dados Azure SQL não suporta todas as funcionalidades que a sua aplicação necessita. Isto pode incluir as seguintes áreas:
  
  * **Tamanho da base**de dados : No momento em que este artigo foi atualizado, a Base de Dados SQL suporta uma base de dados de até 1 TB de dados. Se a sua aplicação necessitar de mais de 1 TB de dados e não quiser implementar soluções personalizadas de sharding, recomenda-se que utilize o SQL Server numa Máquina Virtual Azure. Para obter as informações mais recentes, consulte a Base de [Dados SqL Azure,](https://msdn.microsoft.com/library/azure/dn495641.aspx) [o Modelo de Compra Baseado em DTU](../../database/service-tiers-dtu.md)e o [Modelo de Compra baseado em VCore (pré-visualização).](../../database/service-tiers-vcore.md)
  * **Conformidade HIPAA**: Os clientes de cuidados de saúde e os fornecedores independentes de software (ISVs) podem escolher [o Servidor SQL em Máquinas Virtuais Azure](sql-server-on-azure-vm-iaas-what-is-overview.md) em vez da Base de [Dados Azure SQL](../../database/sql-database-paas-overview.md) porque o Servidor SQL numa Máquina Virtual Azure é coberto pelo ACORDO ASSOCIADO COMERCIAL HIPAA (BAA). Para obter informações sobre a conformidade, consulte [o Microsoft Azure Trust Center: Compliance](https://azure.microsoft.com/support/trust-center/compliance/).
  * **Características de nível de instância**: Neste momento, a Base de Dados SQL não suporta funcionalidades que vivem fora da base de dados (tais como Servidores Linked, trabalhos de agente, FileStream, Corretor de Serviços, etc.). Para mais informações, consulte as Diretrizes e Limitações da Base de [Dados Azure SQL](https://msdn.microsoft.com/library/azure/ff394102.aspx).

## <a name="1-tier-simple-single-virtual-machine"></a>1 nível (simples): máquina virtual única
Neste padrão de aplicação, implementa a sua aplicação e base de dados SQL Server para uma máquina virtual autónoma em Azure. A mesma máquina virtual contém a sua aplicação cliente/web, componentes empresariais, camada de acesso a dados e o servidor de base de dados. O código de apresentação, negócios e acesso a dados estão logicamente separados, mas estão fisicamente localizados numa máquina de um único servidor. A maioria dos clientes começa com este padrão de aplicação e, em seguida, eles escalam adicionando mais papéis web ou máquinas virtuais ao seu sistema.

Este padrão de aplicação é útil quando:

* Pretende realizar uma simples migração para a plataforma Azure para avaliar se a plataforma responde ou não aos requisitos da sua aplicação.
* Pretende manter todos os níveis de aplicação alojados na mesma máquina virtual no mesmo centro de dados Azure para reduzir a latência entre os níveis.
* Pretende fornecer rapidamente ambientes de desenvolvimento e teste por curtos períodos de tempo.
* Pretende efetuar testes de stress para diferentes níveis de carga de trabalho, mas ao mesmo tempo não quer possuir e manter muitas máquinas físicas a toda a hora.

O diagrama seguinte demonstra um cenário simples no local e como pode implantar a sua solução ativada pela nuvem numa única máquina virtual em Azure.

![Padrão de aplicação de 1 nível](./media/application-patterns-development-strategies/IC728008.png)

A implantação da camada de negócio (lógica empresarial e componentes de acesso a dados) no mesmo nível físico que a camada de apresentação pode maximizar o desempenho da aplicação, a menos que tenha de utilizar um nível separado devido a questões de escalabilidade ou segurança.

Uma vez que este é um padrão muito comum para começar, você pode achar o seguinte artigo sobre migração útil para mover os seus dados para o seu SQL Server VM: [Migrar uma Base de Dados para o Servidor SQL em um VM Azure](migrate-to-vm-from-sql-server.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>3 camadas (simples): múltiplas máquinas virtuais
Neste padrão de aplicação, implementa uma aplicação de 3 níveis no Azure colocando cada nível de aplicação numa máquina virtual diferente. Isto proporciona um ambiente flexível para uma escala fácil e em escala. Quando uma máquina virtual contém a sua aplicação cliente/web, a outra acolhe os seus componentes de negócio, e a outra acolhe o servidor de base de dados.

Este padrão de aplicação é útil quando:

* Pretende efetuar uma migração de aplicações complexas de base de dados para máquinas virtuais Azure.
* Você quer que diferentes níveis de aplicação sejam hospedados em diferentes regiões. Por exemplo, pode ter partilhado bases de dados que são implantadas em várias regiões para efeitos de reporte.
* Você quer mover aplicações empresariais de plataformas virtualizadas no local para Máquinas Virtuais Azure. Para uma discussão detalhada sobre aplicações empresariais, consulte [o que é uma aplicação empresarial.](https://msdn.microsoft.com/library/aa267045.aspx)
* Pretende fornecer rapidamente ambientes de desenvolvimento e teste por curtos períodos de tempo.
* Pretende efetuar testes de stress para diferentes níveis de carga de trabalho, mas ao mesmo tempo não quer possuir e manter muitas máquinas físicas a toda a hora.

O diagrama seguinte demonstra como pode colocar uma aplicação simples de 3 camadas em Azure colocando cada nível de aplicação numa máquina virtual diferente.

![Padrão de aplicação de 3 níveis](./media/application-patterns-development-strategies/IC728009.png)

Neste padrão de aplicação, existe apenas uma máquina virtual (VM) em cada nível. Se tiver vários VMs em Azure, recomendamos que instale uma rede virtual. A [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) cria um limite de segurança confiável e também permite que os VMs se comuniquem entre si através do endereço IP privado. Além disso, certifique-se sempre de que todas as ligações à Internet vão apenas para o nível de apresentação. Ao seguir este padrão de aplicação, gerencie as regras do grupo de segurança da rede para controlar o acesso. Para mais informações, consulte [Permitir o acesso externo ao seu VM utilizando o portal Azure](../../../virtual-machines/windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

No diagrama, os Protocolos de Internet podem ser TCP, UDP, HTTP ou HTTPS.

> [!NOTE]
> A criação de uma rede virtual em Azure é gratuita. No entanto, é cobrado pelo gateway VPN que se liga ao local. Esta carga baseia-se no tempo que essa ligação é aprovisionada e disponível.
> 
> 

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>2 níveis e 3 níveis com escala de nível de apresentação
Neste padrão de aplicação, implementa uma aplicação de base de dados de 2 ou 3 níveis para máquinas virtuais Azure, colocando cada nível de aplicação numa máquina virtual diferente. Além disso, você escala o nível de apresentação devido ao aumento do volume de pedidos de clientes que chegam.

Este padrão de aplicação é útil quando:

* Você quer mover aplicações empresariais de plataformas virtualizadas no local para Máquinas Virtuais Azure.
* Pretende escalonar o nível de apresentação devido ao aumento do volume de pedidos de clientes.
* Pretende fornecer rapidamente ambientes de desenvolvimento e teste por curtos períodos de tempo.
* Pretende efetuar testes de stress para diferentes níveis de carga de trabalho, mas ao mesmo tempo não quer possuir e manter muitas máquinas físicas a toda a hora.
* Queremos possuir um ambiente de infraestruturas que possa aumentar e descer a pedido.

O diagrama seguinte demonstra como pode colocar os níveis de aplicação em várias máquinas virtuais em Azure, escalando o nível de apresentação devido ao aumento do volume de pedidos de clientes que chegam. Como se pode ver no diagrama, o Azure Load Balancer é responsável por distribuir tráfego através de várias máquinas virtuais e também determinar a qual servidor web se conectar. Ter várias instâncias dos servidores web por trás de um equilibrador de carga garante a elevada disponibilidade do nível de apresentação.

![Padrão de aplicação - escala de nível de apresentação para fora](./media/application-patterns-development-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Boas práticas para padrões de 2 níveis, 3 ou n-tier que têm vários VMs em um nível
Recomenda-se que coloque as máquinas virtuais que pertencem ao mesmo nível no mesmo serviço de nuvem e no mesmo conjunto de disponibilidade. Por exemplo, coloque um conjunto de servidores web no **CloudService1** e **DisponibilidadeSet1** e um conjunto de servidores de base de dados em **CloudService2** e **AvailabilitySet2**. Um conjunto de disponibilidade sintetiza em Azure que permite colocar os nódosos de alta disponibilidade em domínios de falha separados e domínios de atualização.

Para alavancar várias instâncias vm de um nível, é necessário configurar o Equilíbrio de Carga Azure entre os níveis de aplicação. Para configurar o Balancer de Carga em cada nível, crie um ponto final equilibrado em cada nível de VMs separadamente. Para um nível específico, crie primeiro VMs no mesmo serviço de nuvem. Isto garante que têm o mesmo endereço IP virtual público. Em seguida, crie um ponto final numa das máquinas virtuais desse nível. Em seguida, atribua o mesmo ponto final às outras máquinas virtuais desse nível para equilibrar a carga. Ao criar um conjunto equilibrado em carga, distribui o tráfego através de várias máquinas virtuais e também permite que o Balancer de carga determine qual o nó a ligar quando um nó VM de backend falha. Por exemplo, ter várias instâncias dos servidores web por trás de um balancedor de carga garante a elevada disponibilidade do nível de apresentação.

Como uma boa prática, certifique-se sempre de que todas as ligações à Internet vão primeiro para o nível de apresentação. A camada de apresentação acede ao nível de negócio, e depois o nível de negócio acede ao nível de dados. Para obter mais informações sobre como permitir o acesso à camada de apresentação, consulte [Permitir o acesso externo ao seu VM utilizando o portal Azure](../../../virtual-machines/windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Note que o Balancer load em Azure funciona semelhante aos equilibradores de carga num ambiente no local. Para mais informações, consulte o equilíbrio de carga para os serviços de [infraestrutura Azure.](../../../virtual-machines/windows/tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Além disso, recomendamos que instale uma rede privada para as suas máquinas virtuais utilizando a Rede Virtual Azure. Isto permite-lhes comunicar entre si através do endereço IP privado. Para mais informações, consulte [a Rede Virtual Azure.](../../../virtual-network/virtual-networks-overview.md)

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>2 níveis e 3 níveis com escala de nível de negócio
Neste padrão de aplicação, implementa uma aplicação de base de dados de 2 ou 3 níveis para máquinas virtuais Azure, colocando cada nível de aplicação numa máquina virtual diferente. Além disso, é melhor distribuir os componentes do servidor de aplicações a várias máquinas virtuais devido à complexidade da sua aplicação.

Este padrão de aplicação é útil quando:

* Você quer mover aplicações empresariais de plataformas virtualizadas no local para Máquinas Virtuais Azure.
* Pretende distribuir os componentes do servidor de aplicações a várias máquinas virtuais devido à complexidade da sua aplicação.
* Você quer mover a lógica empresarial aplicações lob (linha de negócio) para Máquinas Virtuais Azure. As aplicações LOB são um conjunto de aplicações informáticas críticas que são vitais para gerir uma empresa, tais como contabilidade, recursos humanos (RH), folha de pagamento, gestão da cadeia de fornecimento e aplicações de planeamento de recursos.
* Pretende fornecer rapidamente ambientes de desenvolvimento e teste por curtos períodos de tempo.
* Pretende efetuar testes de stress para diferentes níveis de carga de trabalho, mas ao mesmo tempo não quer possuir e manter muitas máquinas físicas a toda a hora.
* Queremos possuir um ambiente de infraestruturas que possa aumentar e descer a pedido.

O diagrama seguinte demonstra um cenário no local e a sua solução ativada pela nuvem. Neste cenário, coloca os níveis de aplicação em múltiplas máquinas virtuais no Azure, escalando o nível de negócio, que contém o nível de lógica empresarial e componentes de acesso a dados. Como se pode ver no diagrama, o Azure Load Balancer é responsável por distribuir tráfego através de várias máquinas virtuais e também determinar a qual servidor web se conectar. Ter várias instâncias dos servidores de aplicação por trás de um balancedor de carga garante a elevada disponibilidade do nível de negócio. Para mais informações, consulte [as melhores práticas para padrões de aplicação de 2 níveis, 3 ou n-tier que tenham múltiplas máquinas virtuais num só nível](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Padrão de aplicação com escala de nível de negócio para fora](./media/application-patterns-development-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>2 níveis e 3 níveis com escala de nível de apresentação e de negócios e HADR
Neste padrão de aplicação, implementa uma aplicação de base de dados de 2 níveis ou 3 níveis para máquinas virtuais Azure distribuindo o nível de apresentação (servidor web) e os componentes do nível de negócio (servidor de aplicações) para várias máquinas virtuais. Além disso, implementa soluções de alta disponibilidade e recuperação de desastres para as suas bases de dados em máquinas virtuais Azure.

Este padrão de aplicação é útil quando:

* Deseja mover aplicações empresariais de plataformas virtualizadas no local para O Azure implementando capacidades de alta disponibilidade e recuperação de desastres do SQL Server.
* Pretende escalonar o nível de apresentação e o nível de negócio devido ao aumento do volume de pedidos de clientes e à complexidade da sua aplicação.
* Pretende fornecer rapidamente ambientes de desenvolvimento e teste por curtos períodos de tempo.
* Pretende efetuar testes de stress para diferentes níveis de carga de trabalho, mas ao mesmo tempo não quer possuir e manter muitas máquinas físicas a toda a hora.
* Queremos possuir um ambiente de infraestruturas que possa aumentar e descer a pedido.

O diagrama seguinte demonstra um cenário no local e a sua solução ativada pela nuvem. Neste cenário, você escala o nível de apresentação e os componentes de nível de negócio em várias máquinas virtuais em Azure. Além disso, implementa técnicas de alta disponibilidade e recuperação de desastres (HADR) para bases de dados do SQL Server em Azure.

Executando várias cópias de uma aplicação em Diferentes VMs certifique-se de que você está a carregar pedidos de equilíbrio em todos eles. Quando tiver várias máquinas virtuais, tem de se certificar de que todos os seus VMs estão acessíveis e a funcionar a dada altura. Se configurar o equilíbrio de carga, o Azure Load Balancer acompanha a saúde dos VMs e direciona as chamadas para os nós VM funcionais saudáveis corretamente. Para obter informações sobre como configurar o equilíbrio de carga das máquinas virtuais, consulte o equilíbrio de carga para os serviços de [infraestrutura Azure](../../../virtual-machines/windows/tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ter várias instâncias de servidores web e aplicações por trás de um equilibrador de carga garante a elevada disponibilidade da apresentação e dos níveis de negócio.

![Escala-out e alta disponibilidade](./media/application-patterns-development-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Boas práticas para padrões de aplicação que requerem SQL HADR
Quando configura soluções de alta disponibilidade e recuperação de desastres do SQL Server em Máquinas Virtuais Azure, é obrigatório criar uma rede virtual para as suas máquinas virtuais utilizando a [Rede Virtual Azure.](../../../virtual-network/virtual-networks-overview.md)  As máquinas virtuais dentro de uma Rede Virtual terão um endereço IP privado estável mesmo após um tempo de paragem de serviço, assim pode evitar o tempo de atualização necessário para a resolução de nomes DNS. Além disso, a rede virtual permite-lhe estender a sua rede no local ao Azure e criar um limite de segurança confiável. Por exemplo, se a sua aplicação tiver restrições de domínio corporativo (como, autenticação do Windows, Diretório Ativo), é necessário configurar a [Rede Virtual Azure.](../../../virtual-network/virtual-networks-overview.md)

A maioria dos clientes, que estão a executar o código de produção no Azure, mantêm réplicas primárias e secundárias em Azure.

Para obter informações completas e tutoriais sobre técnicas de alta disponibilidade e recuperação de desastres, consulte alta disponibilidade e recuperação de [desastres para o Servidor SQL em Máquinas Virtuais Azure](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="2-tier-and-3-tier-using-azure-vms-and-cloud-services"></a>2 níveis e 3 níveis utilizando VMs Azure e Serviços cloud
Neste padrão de aplicação, você implementa aplicação de 2 ou 3 níveis para o Azure utilizando tanto os [Serviços Azure Cloud](../../../cloud-services/cloud-services-choose-me.md) (funções web como os trabalhadores - Plataforma como serviço (PaaS)) e [Máquinas Virtuais Azure](../../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Infraestrutura como serviço (IaaS)). A utilização de [Serviços Azure Cloud](https://azure.microsoft.com/documentation/services/cloud-services/) para o nível de apresentação/nível de negócio e O Servidor SQL em [Máquinas Virtuais Azure](../../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para o nível de dados é benéfico para a maioria das aplicações em execução no Azure. A razão é que ter uma instância computacional em execução na Cloud Services fornece uma gestão fácil, implementação, monitorização e escala.

Com a Cloud Services, o Azure mantém a infraestrutura para si, realiza a manutenção de rotina, corrige os sistemas operativos e tenta recuperar de falhas de serviço e hardware. Quando a sua aplicação necessita de opções de escala, automática seleção e manual para o seu projeto de serviço na nuvem, aumentando ou diminuindo o número de instâncias ou máquinas virtuais que são usadas pela sua aplicação. Além disso, pode utilizar o Visual Studio no local para implementar a sua aplicação num projeto de serviço na nuvem em Azure.

Em resumo, se não quiser possuir tarefas administrativas extensivas para o nível de apresentação/negócio e a sua aplicação não requer qualquer configuração complexa de software ou sistema operativo, utilize os Serviços Azure Cloud. Se a Base de Dados Azure SQL não suportar todas as funcionalidades que procura, utilize o Servidor SQL numa Máquina Virtual Azure para o nível de dados. Executar uma aplicação em Azure Cloud Services e armazenar dados em Máquinas Virtuais Azure combina os benefícios de ambos os serviços. Para uma comparação detalhada, consulte a secção deste tópico sobre a comparação de estratégias de [desenvolvimento no Azure.](#comparing-web-development-strategies-in-azure)

Neste padrão de aplicação, o nível de apresentação inclui um papel web, que é um componente de Serviços cloud que funciona no ambiente de execução do Azure e é personalizado para programação de aplicações web como suportado pelo IIS e ASP.NET. O nível de negócio ou backend inclui um papel de trabalhador, que é uma componente de Serviços cloud que funciona no ambiente de execução do Azure e é útil para o desenvolvimento generalizado, e pode realizar processamento de fundo para um papel web. O nível de base de dados reside numa máquina virtual Do Servidor SQL em Azure. A comunicação entre o nível de apresentação e o nível de base de dados acontece diretamente ou sobre o nível de negócio – componentes de função do trabalhador.

Este padrão de aplicação é útil quando:

* Deseja mover aplicações empresariais de plataformas virtualizadas no local para O Azure implementando capacidades de alta disponibilidade e recuperação de desastres do SQL Server.
* Queremos possuir um ambiente de infraestruturas que possa aumentar e descer a pedido.
* A Base de Dados Azure SQL não suporta todas as funcionalidades de que a sua aplicação ou base de dados necessita.
* Pretende efetuar testes de stress para diferentes níveis de carga de trabalho, mas ao mesmo tempo não quer possuir e manter muitas máquinas físicas a toda a hora.

O diagrama seguinte demonstra um cenário no local e a sua solução ativada pela nuvem. Neste cenário, coloca-se o nível de apresentação em funções web, o nível de negócio nas funções dos trabalhadores, mas o nível de dados em máquinas virtuais em Azure. Executar várias cópias do nível de apresentação em diferentes funções web garante carregar pedidos de equilíbrio através deles. Ao combinar serviços azure cloud com máquinas virtuais Azure, recomendamos que também instale a [Rede Virtual Azure.](../../../virtual-network/virtual-networks-overview.md) Com a [Rede Virtual Azure,](../../../virtual-network/virtual-networks-overview.md)pode ter endereços IP privados estáveis e persistentes dentro do mesmo serviço de nuvem na nuvem. Uma vez que você define uma rede virtual para suas máquinas virtuais e serviços de nuvem, eles podem começar a comunicar entre si através do endereço IP privado. Além disso, ter máquinas virtuais e funções azure web/trabalhador na mesma [Rede Virtual Azure](../../../virtual-network/virtual-networks-overview.md) proporciona baixa latência e conectividade mais segura. Para mais informações, consulte [O que é um serviço na nuvem.](../../../cloud-services/cloud-services-choose-me.md)

Como se pode ver no diagrama, o Azure Load Balancer distribui o tráfego através de várias máquinas virtuais e também determina a que servidor web ou servidor de aplicações se conectar. Ter várias instâncias dos servidores web e de aplicação por trás de um balancer de carga garante a elevada disponibilidade do nível de apresentação e do nível de negócio. Para mais informações, consulte [as melhores práticas para padrões de aplicação que requerem SQL HADR](#best-practices-for-application-patterns-requiring-sql-hadr).

![Padrões de aplicação com serviços em nuvem](./media/application-patterns-development-strategies/IC728013.png)

Outra abordagem para implementar este padrão de aplicação é usar uma função web consolidada que contenha componentes de nível de apresentação e de nível de negócio, como mostrado no diagrama seguinte. Este padrão de aplicação é útil para aplicações que requerem design imponente. Uma vez que o Azure fornece nós de cálculo apátridas nas funções web e dos trabalhadores, recomendamos que implemente uma lógica para armazenar estado de sessão utilizando uma das seguintes tecnologias: [Azure Caching,](https://azure.microsoft.com/documentation/services/azure-cache-for-redis/) [Azure Table Storage](../../../cosmos-db/table-storage-how-to-use-dotnet.md) ou [Azure SQL Database.](../../database/sql-database-paas-overview.md)

![Padrões de aplicação com serviços em nuvem](./media/application-patterns-development-strategies/IC728014.png)

## <a name="pattern-with-azure-vms-azure-sql-database-and-azure-app-service-web-apps"></a>Padrão com VMs Azure, Base de Dados Azure SQL e Serviço de Aplicações Azure (Aplicações Web)
O principal objetivo deste padrão de aplicação é mostrar-lhe como combinar a infraestrutura Azure como um serviço (IaaS) componentes com componentes de plataforma Azure-as-a-service (PaaS) na sua solução. Este padrão está focado na Base de Dados Azure SQL para armazenamento relacional de dados. Não inclui o SQL Server numa máquina virtual Azure, que faz parte da infraestrutura Azure como oferta de serviços.

Neste padrão de aplicação, implementa uma aplicação de base de dados para o Azure colocando os níveis de apresentação e negócio sem a mesma máquina virtual e acedendo a uma base de dados nos servidores da Base de Dados Azure SQL (Base de Dados SQL). Pode implementar o nível de apresentação utilizando soluções web tradicionais baseadas no IIS. Ou, pode implementar uma apresentação combinada e um nível de negócio utilizando o [Azure App Service.](https://azure.microsoft.com/documentation/services/app-service/web/)

Este padrão de aplicação é útil quando:

* Já tem um servidor de base de dados SQL existente configurado no Azure e pretende testar a sua aplicação rapidamente.
* Quer testar as capacidades do ambiente Azure.
* Pretende fornecer rapidamente ambientes de desenvolvimento e teste por curtos períodos de tempo.
* A sua lógica de negócio e componentes de acesso a dados podem ser autossuficientes dentro de uma aplicação web.

O diagrama seguinte demonstra um cenário no local e a sua solução ativada pela nuvem. Neste cenário, coloca os níveis de aplicação numa única máquina virtual no Azure e acede dados na Base de Dados Azure SQL.

![Padrão de aplicação mista](./media/application-patterns-development-strategies/IC728015.png)

Se optar por implementar um nível combinado de web e aplicação utilizando aplicações Web Azure, recomendamos que mantenha o nível médio ou nível de aplicação como bibliotecas de ligação dinâmica (DLLs) no contexto de uma aplicação web.

Além disso, reveja as recomendações dadas nas estratégias de [comparação](#comparing-web-development-strategies-in-azure) de desenvolvimento web na secção Azure no final deste artigo para saber mais sobre técnicas de programação.

## <a name="n-tier-hybrid-application-pattern"></a>Padrão de aplicação híbrida n-nível
No padrão de aplicação híbrida n-tier, implementa a sua aplicação em vários níveis distribuídos entre as instalações e o Azure. Por isso, cria-se um sistema híbrido flexível e reutilizável, que pode modificar ou adicionar um nível específico sem alterar os outros níveis. Para estender a sua rede corporativa à nuvem, utiliza o serviço [Azure Virtual Network.](../../../virtual-network/virtual-networks-overview.md)

Este padrão de aplicação híbrida é útil quando:

* Você quer construir aplicações que funcionam parcialmente na nuvem e em parte no local.
* Você quer migrar alguns ou todos os elementos de uma aplicação existente no local para a nuvem.
* Você quer mover aplicações empresariais de plataformas virtualizadas no local para Azure.
* Queremos possuir um ambiente de infraestruturas que possa aumentar e descer a pedido.
* Pretende fornecer rapidamente ambientes de desenvolvimento e teste por curtos períodos de tempo.
* Você quer uma maneira rentável de obter backups para aplicações de base de dados da empresa.

O diagrama seguinte demonstra um padrão de aplicação híbrida n-tier que se estende através das instalações e Azure. Como mostrado no diagrama, a infraestrutura no local inclui o controlador de [domínio ative Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) para apoiar a autenticação e autorização do utilizador. Note que o diagrama demonstra um cenário, em que algumas partes do nível de dados vivem num centro de dados no local, enquanto algumas partes do nível de dados vivem em Azure. Dependendo das necessidades da sua aplicação, pode implementar vários outros cenários híbridos. Por exemplo, pode manter o nível de apresentação e o nível de negócio num ambiente no local, mas o nível de dados em Azure.

![Padrão de aplicação n-tier](./media/application-patterns-development-strategies/IC728016.png)

No Azure, pode utilizar o Ative Directory como um diretório de nuvem autónomo para a sua organização, ou também pode integrar o diretório ativo existente no local com o [Diretório Ativo Azure.](https://azure.microsoft.com/documentation/services/active-directory/) Como se pode ver no diagrama, os componentes de nível de negócio podem aceder a múltiplas fontes de dados, como o [SQL Server em Azure](sql-server-on-azure-vm-iaas-what-is-overview.md) através de um endereço IP interno privado, ao Servidor SQL no local através [da Rede Virtual Azure,](../../../virtual-network/virtual-networks-overview.md)ou à Base de [Dados SQL](../../database/sql-database-paas-overview.md) utilizando as tecnologias de fornecedor de dados .NET Framework. Neste diagrama, a Base de Dados Azure SQL é um serviço opcional de armazenamento de dados.

No padrão de aplicação híbrida n-tier, pode implementar o seguinte fluxo de trabalho na ordem especificada:

1. Identifique aplicações de base de dados empresariais que precisam de ser movidas para a nuvem utilizando o Kit de [Ferramentas de Avaliação e Planeamento da Microsoft (MAP).](https://microsoft.com/map) O MAP Toolkit reúne dados de inventário e desempenho de computadores que está a considerar para virtualização e fornece recomendações sobre o planeamento de capacidade e avaliação.
2. Planeie os recursos e configurações necessários na plataforma Azure, como contas de armazenamento e máquinas virtuais.
3. Criar conectividade de rede entre a rede corporativa no local e a [Rede Virtual Azure.](../../../virtual-network/virtual-networks-overview.md) Para estabelecer a ligação entre a rede corporativa no local e uma máquina virtual em Azure, utilize um dos dois seguintes métodos:
   
   1. Estabeleça uma ligação entre as instalações e o Azure através de pontos finais públicos numa máquina virtual em Azure. Este método proporciona uma configuração fácil e permite-lhe utilizar a autenticação do Servidor SQL na sua máquina virtual. Além disso, estabeleça as regras do seu grupo de segurança de rede para controlar o tráfego público para o VM. Para mais informações, consulte [Permitir o acesso externo ao seu VM utilizando o portal Azure](../../../virtual-machines/windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   2. Estabelecer uma ligação entre as instalações e o túnel Azure via Azure Virtual Private (VPN). Este método permite-lhe estender as políticas de domínio a uma máquina virtual em Azure. Além disso, pode configurar regras de firewall e utilizar a autenticação do Windows na sua máquina virtual. Atualmente, o Azure suporta ligações VPN seguras do site-para-site e vpn ponto-a-local:
      
      * Com uma ligação local-local segura, pode estabelecer conectividade de rede entre a sua rede no local e a sua rede virtual em Azure. Recomenda-se a ligação do seu ambiente de centro de dados no local ao Azure.
      * Com uma ligação segura ponto-a-local, pode estabelecer conectividade de rede entre a sua rede virtual em Azure e os seus computadores individuais em qualquer lugar. É principalmente recomendado para fins de desenvolvimento e teste.
      
      Para obter informações sobre como se conectar ao SQL Server em Azure, consulte [Connect to a SQL Server Virtual Machine on Azure](ways-to-connect-to-sql.md).
4. Instale trabalhos programados e alertas que reporem os dados no local num disco de máquina virtual em Azure. Para mais informações, consulte a Cópia de Segurança e Restauro do [Servidor SQL com o Serviço de Armazenamento De Blob Azure](https://msdn.microsoft.com/library/jj919148.aspx) e [cópia de segurança e restaurar para o Servidor SQL em Máquinas Virtuais Azure](../../../azure-sql/virtual-machines/windows/backup-restore.md).
5. Dependendo das necessidades da sua aplicação, pode implementar um dos três cenários comuns seguintes:
   
   1. Pode manter o seu servidor web, servidor de aplicações e dados insensíveis num servidor de base de dados no Azure, mando manter os dados sensíveis no local.
   2. Pode manter o seu servidor web e servidor de aplicações no local, enquanto o servidor de base de dados numa máquina virtual em Azure.
   3. Pode manter no local o seu servidor de base de dados, servidor web e servidor de aplicações, mando manter as réplicas da base de dados em máquinas virtuais no Azure. Esta definição permite que os servidores web no local ou aplicações de reporte acedam às réplicas da base de dados em Azure. Por isso, pode conseguir reduzir a carga de trabalho numa base de dados no local. Recomendamos que implemente este cenário para trabalhos de trabalho de leitura pesada e fins de desenvolvimento. Para obter informações sobre a criação de réplicas de bases de dados em Azure, consulte SempreOn Availability Groups em [Alta Disponibilidade e Recuperação de Desastres para o Servidor SQL em Máquinas Virtuais Azure](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="comparing-web-development-strategies-in-azure"></a>Comparar estratégias de desenvolvimento web em Azure
Para implementar e implementar uma aplicação baseada em Servidor EsqL multi-nível no Azure, pode utilizar um dos dois seguintes métodos de programação:

* Criar um servidor web tradicional (IIS - Serviços de Informação de Internet) em Azure e aceder a bases de dados no SQL Server em Máquinas Virtuais Azure.
* Implementar e implementar um serviço de nuvem para o Azure. Em seguida, certifique-se de que este serviço de nuvem pode aceder a bases de dados no SQL Server em máquinas Azure Virtual. Um serviço na nuvem pode incluir múltiplas funções web e trabalhadores.

A tabela seguinte proporciona uma comparação do desenvolvimento web tradicional com os Serviços Azure Cloud e Web Apps Azure no que diz respeito ao SQL Server em Máquinas Virtuais Azure. A tabela inclui aplicações Web Azure, pois é possível utilizar o SQL Server no Azure VM como fonte de dados para aplicações Web Azure através do seu endereço IP virtual público ou nome DNS.

|  | Desenvolvimento web tradicional em Máquinas Virtuais Azure | Serviços de Nuvem em Azure | Web Hosting com Web Apps Azure |
| --- | --- | --- | --- |
| **Migração de candidaturas a partir de instalações** |Aplicações existentes como estão. |As aplicações precisam de funções web e de trabalhadores. |Aplicações existentes como é, mas adequado para aplicações web independentes e serviços web que requerem escalabilidade rápida. |
| **Desenvolvimento e Implantação** |Estúdio Visual, WebMatrix, Desenvolvedor de Web Visual, Webdeploy, FTP, TFS, IIS Manager, Powershell. |Estúdio Visual, Azure SDK, TFS, PowerShell. Cada serviço na nuvem tem dois ambientes para os quais pode implementar o seu pacote de serviço e configuração: encenação e produção. Você pode implementar um serviço de nuvem para o ambiente de preparação para testá-lo antes de promovê-lo para a produção. |Estúdio Visual, WebMatrix, Desenvolvedor de Web Visual, FTP, GIT, BitBucket, CodePlex, DropBox, GitHub, Mercurial, TFS, Web Deploy, PowerShell. |
| **Administração e Configuração** |É responsável por tarefas administrativas na aplicação, dados, regras de firewall, rede virtual e sistema operativo. |É responsável por tarefas administrativas na aplicação, dados, regras de firewall e rede virtual. |É responsável por tarefas administrativas apenas na aplicação e dados. |
| **Alta Disponibilidade e Recuperação de Desastres (HADR)** |Recomendamos que coloque máquinas virtuais no mesmo conjunto de disponibilidade e no mesmo serviço de nuvem. Manter os seus VMs no mesmo conjunto de disponibilidade permite ao Azure colocar os nódosos de alta disponibilidade em domínios de falha separados e domínios de atualização. Da mesma forma, manter os seus VMs no mesmo serviço de nuvem permite o equilíbrio de carga e os VMs podem comunicar diretamente uns com os outros através da rede local dentro de um centro de dados Azure.<br/><br/>Você é responsável por implementar uma solução de alta disponibilidade e recuperação de desastres para o SQL Server em Máquinas Virtuais Azure para evitar qualquer tempo de inatividade. Para tecnologias HADR suportadas, consulte [Alta Disponibilidade e Recuperação de Desastres para o Servidor SQL em Máquinas Virtuais Azure](business-continuity-high-availability-disaster-recovery-hadr-overview.md).<br/><br/>É responsável por fazer backup dos seus próprios dados e aplicação.<br/><br/>O Azure pode mover as suas máquinas virtuais se a máquina hospedeira do centro de dados falhar devido a problemas de hardware. Além disso, pode haver tempo de inatividade planeado do seu VM quando a máquina hospedeira for atualizada para atualizações de segurança ou software. Por isso, recomendamos que mantenha pelo menos dois VMs em cada nível de aplicação para garantir a disponibilidade contínua. O Azure não fornece SLA para uma única máquina virtual. |O Azure gere as falhas resultantes do software subjacente ao hardware ou ao sistema operativo. Recomendamos que implemente múltiplas instâncias de uma função web ou de trabalhador para garantir a elevada disponibilidade da sua aplicação. Para obter informações, consulte [Serviços cloud, máquinas virtuais e acordo](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)de nível de serviço de rede virtual.<br/><br/>É responsável por fazer backup dos seus próprios dados e aplicação.<br/><br/>Para bases de dados que residam numa base de dados do SQL Server num VM Azure, é responsável pela implementação de uma solução de alta disponibilidade e recuperação de desastres para evitar qualquer tempo de inatividade. Para tecnologias HDAR suportadas, consulte Alta Disponibilidade e Recuperação de Desastres para o Servidor SQL em Máquinas Virtuais Azure.<br/><br/>Espelho da base de dados do **servidor SQL**: Utilize com serviços de nuvem azure (funções web/trabalhador). Os VMs do Servidor SQL e um projeto de serviço em nuvem podem estar na mesma Rede Virtual Azure. Se o VM do Servidor SQL não estiver na mesma Rede Virtual, precisa de criar um SQL Server Alias para direcionar a comunicação para a instância do Servidor SQL. Além disso, o nome do pseudónimo deve coincidir com o nome Do Servidor SQL. |A Alta Disponibilidade é herdada das funções de trabalhador azure, armazenamento de blob Azure e Base de Dados Azure SQL. Por exemplo, o Azure Storage mantém três réplicas de todos os dados de blob, mesa e fila. A qualquer momento, a Base de Dados Azure SQL mantém três réplicas de dados em funcionamento - uma réplica primária e duas réplicas secundárias. Para mais informações, consulte [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) e [Azure SQL Database](../../database/sql-database-paas-overview.md).<br/><br/>Ao utilizar o SQL Server no Azure VM como fonte de dados para aplicações Web Azure, lembre-se que as Aplicações Web Azure não suportam a Rede Virtual Azure. Por outras palavras, todas as ligações desde as Web Apps azure aos VMs do Servidor SQL em Azure devem passar por pontos finais públicos de máquinas virtuais. Isto pode causar algumas limitações para cenários de alta disponibilidade e recuperação de desastres. Por exemplo, a aplicação do cliente em Web Apps Azure que se conecta ao SQL Server VM com o espelhamento da base de dados não seria capaz de se ligar ao novo servidor primário, uma vez que o espelhamento da base de dados requer que tenha configurado a Rede Virtual Azure entre vMs de anfitriões do SQL Server em Azure. Portanto, a utilização da Base de Dados do **Servidor SQL Espelhada** com Aplicações Web Azure não é suportada atualmente.<br/><br/>Grupos de disponibilidade alwayson do **servidor SQL**: Pode configurar grupos de disponibilidade alwayson ao utilizar aplicações Web Azure com VMs de servidor SQL em Azure. Mas é necessário configurar o SempreOn Availability Group Listener para encaminhar a comunicação para a réplica primária através de pontos finais equilibrados em carga pública. |
| **conectividade transversal** |Pode utilizar a Rede Virtual Azure para se ligar às instalações. |Pode utilizar a Rede Virtual Azure para se ligar às instalações. |A Rede Virtual Azure é suportada. Para mais informações, consulte web [Apps Integração de Redes Virtuais.](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/) |
| **Escalabilidade** |A escala está disponível aumentando o tamanho da máquina virtual ou adicionando mais discos. Para obter mais informações sobre tamanhos de máquinas virtuais, consulte [tamanhos de máquina virtual para Azure](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).<br/><br/>**Para o Servidor de Dados**: O scale-out está disponível através de técnicas de partilha de bases de dados e grupos de disponibilidade de sempre ons do Servidor SQL.<br/><br/>Para trabalhos de trabalho de leitura pesada, pode utilizar grupos de [disponibilidade AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx) em vários nós secundários, bem como replicação do Servidor SQL.<br/><br/>Para cargas de trabalho de escrita pesadas, pode implementar dados horizontais de partição em vários servidores físicos para fornecer escala de aplicação.<br/><br/>Além disso, pode implementar uma escala utilizando o [Servidor SQL com encaminhamento dependente](https://technet.microsoft.com/library/cc966448.aspx)de dados . Com o Encaminhamento Dependente de Dados (DDR), é necessário implementar o mecanismo de partição na aplicação do cliente, tipicamente na camada de nível de negócio, para encaminhar os pedidos de base de dados para vários nós do SQL Server. O nível de negócio contém mapeamentos para a forma como os dados são divididos e qual nó contém os dados.<br/><br/>Pode escalar aplicações que estão a executar máquinas virtuais. Para mais informações, consulte [Como Escalar uma Aplicação](../../../cloud-services/cloud-services-how-to-scale-portal.md).<br/><br/>**Nota importante:** A função **AutoScale** em Azure permite-lhe aumentar ou diminuir automaticamente as Máquinas Virtuais que são utilizadas pela sua aplicação. Esta funcionalidade garante que a experiência do utilizador final não é afetada negativamente durante os períodos de pico, e os VMs são desligados quando a procura é baixa. Recomenda-se que não defina a opção AutoScale para o seu serviço na nuvem se incluir VMs do Servidor SQL. A razão é que a função AutoScale permite ao Azure ligar uma máquina virtual quando o uso do CPU nesse VM é superior a um limiar, e desligar uma máquina virtual quando o uso do CPU é inferior ao seu. A funcionalidade AutoScale é útil para aplicações apátridas, como servidores web, onde qualquer VM pode gerir a carga de trabalho sem quaisquer referências a qualquer estado anterior. No entanto, a funcionalidade AutoScale não é útil para aplicações imponentes, como o SQL Server, onde apenas uma instância permite escrever para a base de dados. |A escala está disponível utilizando várias funções web e operárias. Para obter mais informações sobre tamanhos de máquinas virtuais para funções web e funções de trabalhador, consulte [Tamanhos de Configuração para Serviços de Cloud](../../../cloud-services/cloud-services-sizes-specs.md).<br/><br/>Ao utilizar **os Serviços cloud,** pode definir múltiplas funções para distribuir o processamento e também obter uma escala flexível da sua aplicação. Cada serviço na nuvem inclui uma ou mais funções web e/ou funções de trabalhador, cada uma com os seus próprios ficheiros de aplicação e configuração. Você pode escalar um serviço na nuvem, aumentando o número de exemplos (máquinas virtuais) implantados para um papel e reduzir um serviço na nuvem, diminuindo o número de exemplos. Para obter informações detalhadas, consulte [os Modelos de Execução Azure](../../../cloud-services/cloud-services-choose-me.md).<br/><br/>O scale-out está disponível através de suporte de alta disponibilidade azure incorporado através de [Serviços cloud, máquinas virtuais e Acordo de Nível](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) de Serviço de Rede Virtual e Balancer de Carga.<br/><br/>Para uma aplicação de vários níveis, recomendamos que ligue a aplicação de funções web/trabalhador a VMs de servidor de base de dados através da Rede Virtual Azure. Além disso, o Azure fornece o equilíbrio de carga para VMs no mesmo serviço na nuvem, espalhando pedidos de utilizador através deles. As máquinas virtuais ligadas desta forma podem comunicar-se diretamente através da rede local dentro de um centro de dados Azure.<br/><br/>Pode configurar a **AutoScale** no portal Azure, bem como os horários. Para mais informações, consulte [Como configurar a escala automática para um Serviço de Nuvem no portal](../../../cloud-services/cloud-services-how-to-scale-portal.md). |**Escala para cima e para baixo:** Pode aumentar/diminuir o tamanho da instância (VM) reservada para o seu web site.<br/><br/>Escala para fora: Pode adicionar instâncias mais reservadas (VMs) para o seu web site.<br/><br/>Pode configurar a **AutoScale** no portal, bem como os horários. Para mais informações, consulte [Como Escalar aplicações web](../../../app-service/manage-scale-up.md). |

Para obter mais informações sobre a escolha entre estes métodos de programação, consulte [Web Apps, Cloud Services e VMs: Quando utilizar quais](/azure/architecture/guide/technology-choices/compute-decision-tree).

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre a execução do Servidor SQL em máquinas Azure Virtual, consulte [o Servidor SQL na visão geral das máquinas virtuais azure](sql-server-on-azure-vm-iaas-what-is-overview.md).

