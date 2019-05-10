---
title: Padrões de aplicação do SQL Server em VMs | Documentos da Microsoft
description: Este artigo aborda os padrões de aplicação para o SQL Server em VMs do Azure. Ele fornece programadores e arquitetos de soluções uma base para aplicação boa arquitetura e design.
services: virtual-machines-windows
documentationcenter: na
author: ninarn
manager: craigg
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 41863c8d-f3a3-4584-ad86-b95094365e05
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: ninarn
ms.openlocfilehash: 51d572ac324d0bc875e7ed81879f2456eeea4fbb
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65506621"
---
# <a name="application-patterns-and-development-strategies-for-sql-server-in-azure-virtual-machines"></a>Padrões de Aplicação e Estratégias de Desenvolvimento para o SQL Server em Máquinas Virtuais do Azure
[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="summary"></a>Resumo:
Determinar qual padrão de aplicação ou os padrões de uso para seus aplicativos baseados no servidor de SQL no Azure ambiente é uma decisão de design importantes e requer um entendimento sólido de como o SQL Server e cada componente de infraestrutura do Azure funcionam em conjunto. Com o SQL Server nos serviços de infraestrutura do Azure, pode facilmente migrar, manter e monitorizar as aplicações do SQL Server existentes criados em Windows Server para máquinas virtuais no Azure.

O objetivo deste artigo é fornecer arquitetos de soluções e os desenvolvedores de uma base para arquitetura de aplicativos de boa e design, que pode seguir após a migração de aplicativos existentes para o Azure, bem como desenvolver novas aplicações no Azure.

Para cada padrão de aplicativo, encontrará um cenário no local, sua solução de capacidade de cloud respectiva e as recomendações de técnicas relacionadas. Além disso, o artigo discute as estratégias de desenvolvimento específicos do Azure para que cria seus aplicativos corretamente. Devido a vários padrões de aplicações possíveis, recomenda-se que arquitetos e desenvolvedores devem escolher o padrão mais apropriado para seus aplicativos e os utilizadores.

**Contribuintes técnicos:** Luis Carlos Vargas Herring, Madhan Arumugam Ramakrishnan

**Revisores técnicos:** Corey Sanders, Drew McDaniel, Narayan Annamalai, Nir Mashkowski, Sanjay Mishra, Silvano Coriani, Stefan Schackow, Tim Hickey, Tim Wieman, Xin Jin

## <a name="introduction"></a>Introdução
Pode desenvolver vários tipos de aplicações de n camadas, separando os componentes de camadas do aplicativo diferente em computadores diferentes, bem como em componentes separados. Por exemplo, pode colocar o aplicativo cliente e componentes numa máquina, a camada web front-end e componentes de camada de acesso de dados em outra máquina e uma camada de banco de dados back-end em outra máquina de regras de negócio. Esse tipo de estruturar o ajuda a isolar cada camada uns dos outros. Se mudar de onde vêm dados, não precisa de alterar a aplicação de cliente ou da web, mas apenas os componentes de camada de acesso dados.

Um típico *camadas* aplicativo inclui a camada de apresentação, a camada de negócio e a camada de dados:

| Escalão | Descrição |
| --- | --- |
| **Apresentação** |O *camada de apresentação* (camada web, escalão front-end) é a camada de que os usuários interagem com um aplicativo. |
| **negócios** |O *camada de negócio* (camada média) é a camada a fileira de apresentação e a camada de dados utilizam para comunicar entre si e inclui a funcionalidade principal do sistema. |
| **Dados** |O *camada de dados* é, basicamente, o servidor que armazena dados de uma aplicação (por exemplo, um servidor executando o SQL Server). |

Camadas do aplicativo descrevem os agrupamentos lógicos da funcionalidade e os componentes num aplicativo; enquanto os escalões descrevem a distribuição física da funcionalidade e componentes em servidores físicos separados, computadores, redes ou localizações remotas. As camadas de um aplicativo podem residir no mesmo computador físico (da mesma camada) ou podem ser distribuídas por computadores separados (n camadas) e os componentes em cada camada de comunicam com componentes de outras camadas por meio de interfaces bem definidos. Pode considerar o escalão de termo quanto fazer referência a padrões de distribuição físico como duas camadas, três fileiras e camadas. R **padrão de aplicação de camada 2** contém duas camadas da aplicação: servidor de aplicativos e o servidor de base de dados. Acontece a comunicação direta entre o servidor de aplicações e o servidor de base de dados. O servidor de aplicativo contém componentes de camada web e camada de negócio. Na **padrão de aplicação de 3 camadas**, há três camadas da aplicação: web server, servidor de aplicativos, que contém a camada de lógica de negócios e/ou a componentes de acesso de dados de camada de negócio e o servidor de base de dados. A comunicação entre o servidor web e o servidor de base de dados ocorre ao longo do servidor de aplicativos. Para obter informações detalhadas sobre os escalões e das camadas do aplicativo, consulte [guia de arquitetura do aplicativo Microsoft](https://msdn.microsoft.com/library/ff650706.aspx).

Antes de começar a ler este artigo, deve ter conhecimento sobre os conceitos fundamentais do SQL Server e do Azure. Para obter informações, consulte [SQL Server Books Online](https://msdn.microsoft.com/library/bb545450.aspx), [SQL Server em máquinas de virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md) e [Azure.com](https://azure.microsoft.com/).

Este artigo descreve vários padrões de aplicação que podem ser adequados para seus aplicativos simples, bem como aplicações empresariais altamente complexas. Antes de detalhar cada padrão, é recomendável que deve se familiarizar com os serviços de armazenamento de dados disponíveis no Azure, tal como [armazenamento do Azure](../../../storage/common/storage-introduction.md), [base de dados do Azure SQL](../../../sql-database/sql-database-technical-overview.md), e [SQL Server numa máquina Virtual do Azure](virtual-machines-windows-sql-server-iaas-overview.md). Para tomar as melhores decisões de design para as suas aplicações, compreenda quando deve utilizar o serviço de armazenamento de dados claramente.

### <a name="choose-sql-server-in-an-azure-virtual-machine-when"></a>Escolha do SQL Server numa máquina Virtual do Azure, quando:
* Terá de controle no SQL Server e Windows. Por exemplo, isto pode incluir a versão do SQL Server, correções especiais, configuração de desempenho, etc.
* Precisa de uma compatibilidade total com o SQL Server no local e quiser migrar aplicativos já existentes para o Azure como-é.
* Deseja aproveitar os recursos de ambiente do Azure, mas SQL Database do Azure não suporta todas as funcionalidades pedidas pela aplicação. Isso pode incluir as seguintes áreas:
  
  * **Tamanho de base de dados**: No momento que este artigo foi atualizado, a base de dados SQL suporta uma base de dados de até 1 TB de dados. Se a sua aplicação necessitar de mais de 1 TB de dados e não pretender implementar as soluções de fragmentação personalizado, recomenda-se que utilize SQL Server na máquina Virtual do Azure. Para obter as informações mais recentes, consulte [aumentar horizontalmente o Azure SQL Database](https://msdn.microsoft.com/library/azure/dn495641.aspx), [modelo de compra DTU-Based](../../../sql-database/sql-database-service-tiers-dtu.md), e [modelo de compra baseado em vCore](../../../sql-database/sql-database-service-tiers-vcore.md)(pré-visualização).
  * **Conformidade do HIPAA**: Os clientes de cuidados de saúde e fornecedores independentes de Software (ISVs) podem optar por utilizar [SQL Server em máquinas de virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md) em vez de [base de dados do Azure SQL](../../../sql-database/sql-database-technical-overview.md) porque está abrangido na máquina Virtual do Azure do SQL Server por HIPAA Business Associate Agreement (BAA). Para obter informações sobre a conformidade, consulte [Microsoft Azure Trust Center: Conformidade](https://azure.microsoft.com/support/trust-center/compliance/).
  * **Funcionalidades de nível de instância**: Neste momento, base de dados SQL não suporta funcionalidades que residem fora do banco de dados (por exemplo, servidores vinculados, agente de tarefas, FileStream, o Service Broker, etc.). Para obter mais informações, consulte [diretrizes de base de dados SQL do Azure e as limitações](https://msdn.microsoft.com/library/azure/ff394102.aspx).

## <a name="1-tier-simple-single-virtual-machine"></a>(simples) de 1 camada: única máquina virtual
Este padrão de aplicação, vai implementar a aplicação do SQL Server e base de dados a uma máquina de virtual autónomo no Azure. A mesma máquina virtual contém a aplicação de cliente/web, componentes de negócios, camada de acesso a dados e o servidor de base de dados. A apresentação, negócios e código de acesso de dados estão logicamente separados, mas estão fisicamente localizados numa máquina de servidor único. Começar a maioria dos clientes com esse padrão de aplicação e, em seguida, podem aumentar horizontalmente ao adicionar mais funções da web ou máquinas virtuais para o seu sistema.

Este padrão de aplicação é útil quando:

* Pretende efetuar uma migração simple para a plataforma do Azure para avaliar se a plataforma responde os requisitos da aplicação ou não.
* Pretende manter todas as camadas de aplicativos hospedadas na mesma máquina virtual no mesmo centro de dados do Azure para reduzir a latência entre camadas.
* Pretende aprovisionar o desenvolvimento e os ambientes de teste para curtos períodos de tempo rapidamente.
* Deseja realizar para diferentes níveis de carga de trabalho de teste de carga, mas ao mesmo tempo que não pretende o proprietário e manter muitas máquinas físicas o tempo todo.

O diagrama a seguir demonstra um simples no local cenário e como pode implantar sua solução de cloud ativada numa única máquina virtual no Azure.

![padrão de aplicação de camada 1](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728008.png)

Implementar a camada de negócios (lógica de negócios e os componentes de acesso de dados) na mesma camada física como a camada de apresentação pode maximizar o desempenho do aplicativo, a menos que deve usar uma camada separada devido a problemas de escalabilidade ou de segurança.

Uma vez que este é um padrão muito comum para começar, poderá encontrar o seguinte artigo sobre a migração útil para mover os seus dados à sua VM do SQL Server: [Migrar uma base de dados para o SQL Server numa VM do Azure](virtual-machines-windows-migrate-sql.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>(simples) de 3 camadas: várias máquinas virtuais
Este padrão de aplicação, vai implementar uma aplicação de 3 camadas no Azure ao colocar cada camada de aplicativos numa máquina virtual diferente. Isso fornece um ambiente flexível para uma fácil cenários de aumento vertical e horizontal. Quando uma máquina virtual contém a aplicação de cliente/web, os outros um hospeda os componentes de negócios e a outros um hospeda o servidor de base de dados.

Este padrão de aplicação é útil quando:

* Pretende efetuar uma migração de aplicativos de banco de dados complexos para máquinas de virtuais do Azure.
* Quer camadas da aplicação diferente para ser alojada em regiões diferentes. Por exemplo, talvez tenha partilhado bases de dados que são implementadas em várias regiões para fins de relatórios.
* Pretende mover aplicações empresariais a partir de plataformas virtualizada no local às máquinas de virtuais do Azure. Para uma discussão detalhada sobre aplicações empresariais, consulte [o que é uma aplicação empresarial](https://msdn.microsoft.com/library/aa267045.aspx).
* Pretende aprovisionar o desenvolvimento e os ambientes de teste para curtos períodos de tempo rapidamente.
* Deseja realizar para diferentes níveis de carga de trabalho de teste de carga, mas ao mesmo tempo que não pretende o proprietário e manter muitas máquinas físicas o tempo todo.

O diagrama a seguir demonstra como colocar uma simple aplicação de 3 camadas no Azure ao colocar cada camada de aplicativos numa máquina virtual diferente.

![padrão de aplicação de 3 camadas](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728009.png)

Neste padrão de aplicativo, há apenas uma máquina virtual (VM) em cada escalão. Se tiver várias VMs no Azure, recomendamos que configure uma rede virtual. [Rede Virtual do Azure](../../../virtual-network/virtual-networks-overview.md) cria um limite de segurança fidedigna e também permite que as VMs comunicar entre si através do endereço IP privado. Além disso, sempre Certifique-se de que todas as ligações de Internet Ir apenas para a camada de apresentação. Quando seguir esse padrão de aplicação, gerir as regras de grupo de segurança de rede para controlar o acesso. Para obter mais informações, consulte [permitir acesso externo à sua VM com o portal do Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

No diagrama, os protocolos de Internet podem ser TCP, UDP, HTTP ou HTTPS.

> [!NOTE]
> Como configurar uma rede virtual no Azure é gratuito. No entanto, é-lhe cobrada o gateway de VPN que se liga a no local. Cobrado baseia-se na quantidade de tempo de ligação é aprovisionado e está disponível.
> 
> 

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>camada 2 e 3 camadas com a camada de apresentação e escaláveis
Este padrão de aplicação, vai implementar a aplicação de camada 2 ou 3 camadas da base de dados para máquinas de virtuais do Azure ao colocar cada camada da aplicação em outra máquina virtual. Além disso, aumenta horizontalmente a camada de apresentação devido ao volume maior de pedidos de cliente recebidos.

Este padrão de aplicação é útil quando:

* Pretende mover aplicações empresariais a partir de plataformas virtualizada no local às máquinas de virtuais do Azure.
* Deseja aumentar horizontalmente a camada de apresentação devido ao volume maior de pedidos de cliente recebidos.
* Pretende aprovisionar o desenvolvimento e os ambientes de teste para curtos períodos de tempo rapidamente.
* Deseja realizar para diferentes níveis de carga de trabalho de teste de carga, mas ao mesmo tempo que não pretende o proprietário e manter muitas máquinas físicas o tempo todo.
* Possui um ambiente de infraestrutura que pode aumentar e reduzir verticalmente a pedido.

O diagrama a seguir demonstra como pode colocar as camadas da aplicação em várias máquinas virtuais no Azure ao aumentar horizontalmente a camada de apresentação devido ao volume maior de pedidos de cliente recebidos. Conforme ilustrado no diagrama, o Balanceador de carga do Azure é responsável por distribuir o tráfego em várias máquinas virtuais e também determinar que servidor web para ligar a. Ter várias instâncias dos servidores web por trás de um balanceador de carga garante a elevada disponibilidade da camada de apresentação.

![Padrão de aplicação - de escalamento horizontal de camada de apresentação](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Melhores práticas para padrões de camada 2, 3 camadas ou n camadas com várias VMs numa só camada
É recomendável que coloque as máquinas virtuais que pertencem à mesma camada no mesmo serviço cloud e no mesmo a disponibilidade definida. Por exemplo, colocar um conjunto de servidores web no **CloudService1** e **AvailabilitySet1** e um conjunto de servidores de base de dados no **CloudService2** e  **AvailabilitySet2**. Um conjunto de disponibilidade no Azure permite-lhe colocar os nós de elevada disponibilidade em domínios de falha e domínios de atualização.

Para tirar partido de várias instâncias VM de uma camada, terá de configurar o Balanceador de carga do Azure entre camadas da aplicação. Para configurar o Balanceador de carga em cada escalão, crie um ponto final com balanceamento de carga em VMs cada escalão em separado. Para um escalão específico, crie VMs no mesmo serviço cloud. Isto garante que têm o mesmo endereço Virtual IP público. Em seguida, crie um ponto final das máquinas virtuais desse escalão. Em seguida, atribua o mesmo ponto final para outras máquinas virtuais nesse escalão para balanceamento de carga. Ao criar um conjunto com balanceamento de carga, distribuir o tráfego em várias máquinas virtuais e também permitir que o Balanceador de carga determinar qual nó se ligue quando ocorre uma falha de um nó VM de back-end. Por exemplo, ter várias instâncias dos servidores web por trás de um balanceador de carga garante a elevada disponibilidade da camada de apresentação.

Como melhor prática, sempre Certifique-se de que todas as ligações de internet primeiro, ir até a camada de apresentação. A camada de apresentação acessa a camada de negócio e, em seguida, a camada de negócio acessa a camada de dados. Para obter mais informações sobre como permitir o acesso para a camada de apresentação, consulte [permitir acesso externo à sua VM com o portal do Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Tenha em atenção que o Balanceador de carga no Azure funciona de maneira semelhante ao carregar balanceadores num ambiente no local. Para obter mais informações, consulte [balanceamento de carga para serviços de infraestrutura do Azure](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Além disso, recomendamos que configure uma rede privada para as suas máquinas virtuais utilizando a rede Virtual do Azure. Isso permite que eles se comuniquem entre si através do endereço IP privado. Para obter mais informações, consulte [rede Virtual do Azure](../../../virtual-network/virtual-networks-overview.md).

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>camada 2 e 3 camadas com a camada de negócio e escaláveis
Este padrão de aplicação, vai implementar a aplicação de camada 2 ou 3 camadas da base de dados para máquinas de virtuais do Azure ao colocar cada camada da aplicação em outra máquina virtual. Além disso, pode querer distribuir os componentes de servidor de aplicações para várias máquinas virtuais devido à complexidade do seu aplicativo.

Este padrão de aplicação é útil quando:

* Pretende mover aplicações empresariais a partir de plataformas virtualizada no local às máquinas de virtuais do Azure.
* Pretende distribuir os componentes de servidor de aplicações para várias máquinas virtuais devido à complexidade do seu aplicativo.
* Pretende mover pesado de lógica de negócios aplicações no local LOB (line-of-business) para máquinas de virtuais do Azure. Os aplicativos de LOB são um conjunto de aplicações para computadores críticos que são vitais para administrar uma empresa, como gestão de contas, recursos humanos (RH), folhas de pagamento, gestão de cadeias de fornecimento e aplicativos de planeamento de recursos.
* Pretende aprovisionar o desenvolvimento e os ambientes de teste para curtos períodos de tempo rapidamente.
* Deseja realizar para diferentes níveis de carga de trabalho de teste de carga, mas ao mesmo tempo que não pretende o proprietário e manter muitas máquinas físicas o tempo todo.
* Possui um ambiente de infraestrutura que pode aumentar e reduzir verticalmente a pedido.

O diagrama a seguir demonstra um cenário no local e a sua solução na cloud ativada. Neste cenário, é possível colocar os escalões de aplicação em várias máquinas virtuais no Azure ao aumentar horizontalmente a camada de negócio, que contém a camada de lógica de negócios e os componentes de acesso de dados. Conforme ilustrado no diagrama, o Balanceador de carga do Azure é responsável por distribuir o tráfego em várias máquinas virtuais e também determinar que servidor web para ligar a. Ter várias instâncias de servidores de aplicações por trás de um balanceador de carga garante a elevada disponibilidade da camada de negócio. Para obter mais informações, consulte [melhores práticas para padrões de aplicação de camada 2, 3 camadas ou n camadas com várias máquinas virtuais numa só camada](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Padrão de aplicação com o início de ampliação de camada de negócio](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>camada 2 e 3-com a camada de apresentação e Escalamento do negócio escalões e HADR
Este padrão de aplicação, vai implementar a aplicação de camada 2 ou 3 camadas da base de dados para máquinas de virtuais do Azure ao distribuir a camada de apresentação (servidor web) e os componentes de (servidor de aplicativos) da camada de negócio para várias máquinas virtuais. Além disso, implementar soluções de recuperação após desastre e elevada disponibilidade para as bases de dados em máquinas virtuais do Azure.

Este padrão de aplicação é útil quando:

* Pretende mover aplicações empresariais de plataformas virtualizadas no local para o Azure implementando alta disponibilidade do SQL Server e capacidades de recuperação após desastre.
* Deseja aumentar horizontalmente a camada de apresentação e a camada de negócios devido a maior volume de pedidos de cliente recebidos e a complexidade da sua aplicação.
* Pretende aprovisionar o desenvolvimento e os ambientes de teste para curtos períodos de tempo rapidamente.
* Deseja realizar para diferentes níveis de carga de trabalho de teste de carga, mas ao mesmo tempo que não pretende o proprietário e manter muitas máquinas físicas o tempo todo.
* Possui um ambiente de infraestrutura que pode aumentar e reduzir verticalmente a pedido.

O diagrama a seguir demonstra um cenário no local e a sua solução na cloud ativada. Neste cenário, aumenta horizontalmente a camada de apresentação e os componentes da camada de negócio em várias máquinas virtuais no Azure. Além disso, implementar elevadas disponibilidade e após desastre (HADR) técnicas de recuperação para bases de dados do SQL Server no Azure.

Executar várias cópias de um aplicativo em diferentes VMs Certifique-se de que é o balanceamento de carga pedidos entre eles. Quando tem várias máquinas virtuais, terá de certificar-se de que todas as VMs estão acessíveis e em execução num ponto no tempo. Se configurar o balanceamento de carga, o Azure Load Balancer controla o estado de funcionamento das VMs e direciona as chamadas de entrada para os nós em bom estado de VM está a funcionar corretamente. Para obter informações sobre como configurar o balanceamento de carga das máquinas virtuais, consulte [balanceamento de carga para serviços de infraestrutura do Azure](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ter várias instâncias de aplicação web e servidores por trás de um balanceador de carga garante a elevada disponibilidade das camadas de apresentação e comercial.

![Escalamento horizontal e a elevada disponibilidade](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Melhores práticas para padrões de aplicativos que exigem HADR de SQL
Quando configurar a elevada disponibilidade do SQL Server e soluções de recuperação após desastre em máquinas de virtuais do Azure, como configurar uma rede virtual para suas máquinas virtuais utilizando [rede Virtual do Azure](../../../virtual-network/virtual-networks-overview.md) é obrigatório.  Máquinas virtuais numa rede Virtual terão um endereço IP privado estável, mesmo após um período de indisponibilidade de serviço, assim pode evitar o tempo de atualização necessário para a resolução de nomes DNS. Além disso, a rede virtual permite-lhe expandir a sua rede no local para o Azure e cria um limite de segurança confiáveis. Por exemplo, se seu aplicativo tiver restrições de domínio Corporativo (por exemplo, Windows authentication, Active Directory), configurando [rede Virtual do Azure](../../../virtual-network/virtual-networks-overview.md) é necessário.

A maioria dos clientes, que executem o código de produção no Azure, mantendo as réplicas primárias e secundárias no Azure.

Para informações abrangentes e tutoriais sobre a elevada disponibilidade e técnicas de recuperação após desastre, consulte [elevada disponibilidade e recuperação após desastre para SQL Server em máquinas de virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="2-tier-and-3-tier-using-azure-vms-and-cloud-services"></a>camada 2 e 3 camadas utilizando as VMs do Azure e serviços em nuvem
Neste padrão de aplicação, implementar a aplicação de camada 2 ou 3 camadas no Azure através de ambos [serviços Cloud do Azure](../../../cloud-services/cloud-services-choose-me.md) (funções web e função de trabalho - plataforma como serviço (PaaS)) e [máquinas virtuais do Azure](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ( Infraestrutura como serviço (IaaS)). Usando [serviços Cloud do Azure](https://azure.microsoft.com/documentation/services/cloud-services/) para a camada de negócios/camada de apresentação e o SQL Server na [máquinas virtuais do Azure](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para os dados de escalão seja benéfico para a maioria dos aplicativos em execução no Azure. O motivo é que ter uma instância de computação em execução nos serviços Cloud fornece uma gestão fácil, implementação, monitorização e Escalamento horizontal.

Com os serviços Cloud, Azure mantém a infraestrutura para, realiza a manutenção de rotina, patches os sistemas operativos e tenta recuperar de falhas de hardware e de serviço. Quando seu aplicativo precisa aumentar horizontalmente, opções de escalamento horizontal automáticas e manuais estão disponíveis para o seu projeto de serviço de nuvem ao aumentar ou diminuir o número de instâncias ou máquinas virtuais que são utilizadas pela sua aplicação. Além disso, pode utilizar no local Visual Studio para implementar a sua aplicação para um projeto de serviço em nuvem no Azure.

Em resumo, se não pretender ter amplo tarefas administrativas para as empresas/apresentação escalão e seu aplicativo não necessita de qualquer configuração complexa de software ou o sistema operativo, utilize os serviços Cloud do Azure. Se a base de dados do Azure SQL não suporta todos os recursos que está procurando, utilize o SQL Server na máquina Virtual do Azure para a camada de dados. Para executar uma aplicação em serviços Cloud do Azure e armazenar dados em máquinas de virtuais do Azure combinam os benefícios de ambos os serviços. Para obter uma comparação detalhada, consulte a secção neste tópico sobre [comparando estratégias de desenvolvimento no Azure](#comparing-web-development-strategies-in-azure).

Neste padrão de aplicação, a camada de apresentação inclui uma função da web, que é um componente de serviços Cloud em execução no ambiente de execução do Azure e é personalizado para a programação de aplicativo web como suportado pelo IIS e ASP.NET. A camada de negócio ou back-end inclui uma função de trabalho, o que é um componente de serviços Cloud em execução no ambiente de execução do Azure e é útil para desenvolvimento generalizado e pode efetuar o processamento em segundo plano para uma função da web. A camada de base de dados reside numa máquina virtual do SQL Server no Azure. A comunicação entre a camada de apresentação e a camada de base de dados acontece diretamente ou através de camada de negócio – componentes da função de trabalho.

Este padrão de aplicação é útil quando:

* Pretende mover aplicações empresariais de plataformas virtualizadas no local para o Azure implementando alta disponibilidade do SQL Server e capacidades de recuperação após desastre.
* Possui um ambiente de infraestrutura que pode aumentar e reduzir verticalmente a pedido.
* Base de dados SQL do Azure não suporta todas as funcionalidades que a sua aplicação ou a base de dados precisa.
* Deseja realizar para diferentes níveis de carga de trabalho de teste de carga, mas ao mesmo tempo que não pretende o proprietário e manter muitas máquinas físicas o tempo todo.

O diagrama a seguir demonstra um cenário no local e a sua solução na cloud ativada. Neste cenário, coloca a camada de apresentação em funções da web, a camada de negócio em funções de trabalho, mas a camada de dados em máquinas virtuais no Azure. Executar várias cópias da camada de apresentação em funções da web diferentes garante para pedidos de balanceamento de carga em-los. Quando combina serviços de Cloud do Azure com máquinas virtuais do Azure, recomendamos que configure [rede Virtual do Azure](../../../virtual-network/virtual-networks-overview.md) também. Com o [rede Virtual do Azure](../../../virtual-network/virtual-networks-overview.md), pode ter estável e persistentes endereços IP privados no mesmo serviço na cloud da cloud. Depois de definir uma rede virtual para as suas máquinas virtuais e serviços cloud, pode começar a comunicar entre si através do endereço IP privado. Além disso, ter funções de trabalho/web do Azure e máquinas virtuais no mesmo [rede Virtual do Azure](../../../virtual-network/virtual-networks-overview.md) proporciona baixa latência e conectividade mais segura. Para obter mais informações, consulte [o que é um serviço cloud](../../../cloud-services/cloud-services-choose-me.md).

Conforme ilustrado no diagrama, o Azure Load Balancer distribui o tráfego por várias máquinas virtuais e também determina que o servidor de web ou o servidor de aplicação para ligar a. Ter várias instâncias dos servidores web e aplicações atrás de um balanceador de carga garante a elevada disponibilidade da camada de apresentação e a camada de negócio. Para obter mais informações, consulte [de melhores práticas para padrões de aplicação que exigem que SQL HADR](#best-practices-for-application-patterns-requiring-sql-hadr).

![Padrões de aplicação com os serviços Cloud](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728013.png)

Outra abordagem para implementar este padrão de aplicação é usar uma função da web consolidado que contém a camada de apresentação e componentes de camada de negócios, conforme mostrado no diagrama seguinte. Este padrão de aplicação é útil para aplicações que necessitam de design com monitoração de estado. Uma vez que o Azure fornece nós de computação sem monitoração de estado no funções web e de trabalho, recomendamos que implementar uma lógica para armazenar o estado da sessão através de uma das seguintes tecnologias: [A colocação em cache do Azure](https://azure.microsoft.com/documentation/services/azure-cache-for-redis/), [armazenamento de tabelas do Azure](../../../cosmos-db/table-storage-how-to-use-dotnet.md) ou [base de dados SQL do Azure](../../../sql-database/sql-database-technical-overview.md).

![Padrões de aplicação com os serviços Cloud](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728014.png)

## <a name="pattern-with-azure-vms-azure-sql-database-and-azure-app-service-web-apps"></a>Padrão com VMs do Azure, base de dados SQL do Azure e serviço de aplicações do Azure (aplicações Web)
O objetivo principal deste padrão de aplicação é mostrar como combinar a infraestrutura do Azure como um componentes de serviço (IaaS) com componentes de plataforma-como-serviço do Azure (PaaS) na sua solução. Este padrão está concentrado na SQL Database do Azure para o armazenamento de dados relacionais. Não inclui do SQL Server numa máquina virtual do Azure, que faz parte da infraestrutura do Azure como uma oferta de serviço.

Neste padrão de aplicação, implementar uma aplicação de base de dados para o Azure colocando as camadas de apresentação e comercial na mesma máquina virtual e aceder a uma base de dados em servidores de base de dados do Azure SQL (base de dados SQL). Pode implementar a camada de apresentação através de soluções da web tradicional baseada no IIS. Em alternativa, pode implementar uma apresentação combinada e a camada de negócio através de [App Service do Azure](https://azure.microsoft.com/documentation/services/app-service/web/).

Este padrão de aplicação é útil quando:

* Já tem um servidor de base de dados SQL existente configurado no Azure e pretende testar rapidamente a sua aplicação.
* Pretende testar as capacidades do ambiente do Azure.
* Pretende aprovisionar o desenvolvimento e os ambientes de teste para curtos períodos de tempo rapidamente.
* Os componentes de acesso de lógica e os dados empresariais podem ser independentes dentro de um aplicativo web.

O diagrama a seguir demonstra um cenário no local e a sua solução na cloud ativada. Neste cenário, é colocar as camadas do aplicativo numa única máquina virtual no Azure e aceda aos dados na base de dados do Azure SQL.

![Padrão de aplicação misto](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728015.png)

Se optar por implementar um web combinado e a camada de aplicativos com aplicações Web do Azure, recomendamos que mantenha a camada de aplicação ou de camada intermediária como bibliotecas de ligação dinâmica (DLLs) no contexto de um aplicativo web.

Além disso, reveja as recomendações apresentadas a [comparando estratégias de desenvolvimento da web no Azure](#comparing-web-development-strategies-in-azure) secção no final deste artigo para saber mais sobre técnicas de programação.

## <a name="n-tier-hybrid-application-pattern"></a>Padrão de aplicação híbrida de N camadas
No padrão de aplicação de n camadas híbrida, ao implementar seu aplicativo em várias camadas distribuído entre no local e o Azure. Portanto, cria um sistema híbrido flexíveis e reutilizáveis, que pode modificar ou adicionar uma camada específica sem alterar os outros escalões. Para expandir a sua rede empresarial para a cloud, utilize [rede Virtual do Azure](../../../virtual-network/virtual-networks-overview.md) serviço.

Este padrão de aplicação híbrida é útil quando:

* Quer criar aplicações que são executadas parcialmente na nuvem e parcialmente no local.
* Deseja migrar alguns ou todos os elementos de uma aplicação no local existente para a cloud.
* Pretende mover aplicações empresariais a partir de plataformas virtualizada no local para o Azure.
* Possui um ambiente de infraestrutura que pode aumentar e reduzir verticalmente a pedido.
* Pretende aprovisionar o desenvolvimento e os ambientes de teste para curtos períodos de tempo rapidamente.
* Pretende uma forma económica para fazer cópias de segurança para aplicações de base de dados empresariais.

O diagrama a seguir demonstra um padrão de aplicação de n camadas híbrida que se expande entre aplicações no local e o Azure. Conforme mostrado no diagrama, no local infraestrutura inclui [serviços de domínio do Active Directory](https://technet.microsoft.com/library/hh831484.aspx) controlador de domínio para suportar a autenticação de utilizador e a autorização. Observe que o diagrama demonstra um cenário, em que algumas partes da camada de dados residem num Datacenter no local, ao passo que algumas partes da camada de dados em direto no Azure. Consoante as necessidades da sua aplicação, pode implementar vários outros cenários híbridos. Por exemplo, poderá manter a camada de apresentação e a camada de negócio num ambiente no local, mas a camada de dados no Azure.

![Padrão de aplicação de N camadas](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728016.png)

No Azure, pode utilizar Active Directory como um diretório de nuvem autónomo para a sua organização ou pode também integrar existente do Active Directory no local com o [do Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Conforme ilustrado no diagrama, os componentes da camada de negócio podem aceder a várias origens de dados, tal como para [SQL Server no Azure](virtual-machines-windows-sql-server-iaas-overview.md) através de um endereço IP interno privado, no local do SQL Server por meio de [rede Virtual do Azure](../../../virtual-network/virtual-networks-overview.md), ou a [base de dados SQL](../../../sql-database/sql-database-technical-overview.md) usando as tecnologias de fornecedor de dados do .NET Framework. Neste diagrama, a base de dados do Azure SQL é um serviço de armazenamento de dados opcionais.

Padrão de aplicação híbrida de n camadas, pode implementar o seguinte fluxo de trabalho na ordem especificada:

1. Identificar aplicativos de banco de dados empresariais que têm de ser movidas para cima para a cloud utilizando o [Microsoft Assessment and Planning (MAP) Toolkit](https://microsoft.com/map). O MAP Toolkit coleta dados de inventário e o desempenho de computadores que estiver a considerar para virtualização e fornece recomendações sobre planejamento de avaliação e de capacidade.
2. Planear os recursos e a configuração necessária na plataforma do Azure, como contas de armazenamento e máquinas virtuais.
3. Configurar a conectividade de rede entre a rede empresarial no local e [rede Virtual do Azure](../../../virtual-network/virtual-networks-overview.md). Para configurar a ligação entre a rede empresarial no local e uma máquina virtual no Azure, utilize um dos seguintes dois métodos:
   
   1. Estabelece uma ligação entre locais e o Azure através de pontos finais públicos de numa máquina virtual no Azure. Este método fornece uma configuração fácil e permite-lhe utilizar a autenticação do SQL Server na sua máquina virtual. Além disso, configure as regras de grupo de segurança de rede para controlar o tráfego público à VM. Para obter mais informações, consulte [permitir acesso externo à sua VM com o portal do Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   2. Estabelece uma ligação entre locais e o Azure através do túnel (VPN rede) privada Virtual do Azure. Este método permite-lhe expandir as políticas de domínio a uma máquina virtual no Azure. Além disso, pode configurar regras de firewall e utilizar a autenticação do Windows na sua máquina virtual. Atualmente, o Azure suporta a VPN de site a site segura e ligações de VPN ponto a site:
      
      * Com a ligação de site a site segura, pode estabelecer conectividade de rede entre a sua rede no local e a rede virtual no Azure. Recomenda-se para ligar o seu ambiente de centro de dados no local ao Azure.
      * Com a ligação de ponto a site segura, pode estabelecer conectividade de rede entre a rede virtual no Azure e os computadores individuais em execução em qualquer lugar. Recomenda-se principalmente para fins de desenvolvimento e teste.
      
      Para obter informações sobre como ligar ao SQL Server no Azure, consulte [ligar a um SQL Server Máquina Virtual no Azure](virtual-machines-windows-sql-connect.md).
4. Configure tarefas agendadas e alertas de cópias de segurança no local num disco de máquina virtual no Azure. Para obter mais informações, consulte [cópia de segurança do SQL Server e restauro com o serviço de armazenamento de Blobs do Azure](https://msdn.microsoft.com/library/jj919148.aspx) e [cópia de segurança e restaurar para o SQL Server em máquinas de virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md).
5. Consoante as necessidades do seu aplicativo, pode implementar um dos seguintes três cenários comuns:
   
   1. Pode manter seu servidor web, servidor de aplicações e dados de minúsculas num servidor de base de dados no Azure, ao passo que a manter os dados confidenciais no local.
   2. Pode manter o seu servidor web e servidor de aplicações no local, ao passo que o servidor de base de dados numa máquina virtual no Azure.
   3. Pode manter o seu servidor de base de dados, o servidor web e servidor de aplicações no local, ao passo que tenha as réplicas de base de dados em máquinas virtuais no Azure. Esta definição permite que os servidores de web no local ou aplicativos de geração de relatórios para acessar as réplicas de base de dados no Azure. Portanto, pode conseguir reduzir a carga de trabalho numa base de dados no local. Recomendamos que implementar este cenário para pesados ler cargas de trabalho e fins de desenvolvimento. Para obter informações sobre a criação de réplicas de base de dados no Azure, consulte grupos de Disponibilidade AlwaysOn na [elevada disponibilidade e recuperação após desastre para SQL Server em máquinas de virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="comparing-web-development-strategies-in-azure"></a>Comparando as estratégias de desenvolvimento da web no Azure
Implementar e implantar uma aplicativo baseado em SQL Server no Azure de várias camadas, pode usar um dos seguintes métodos de programação duas:

* Configure um servidor de web tradicional (IIS - serviços de informação Internet) nos bancos de dados do Azure e o acesso no SQL Server em máquinas de virtuais do Azure.
* Implementar e implantar um serviço em nuvem para o Azure. Em seguida, certifique-se de que este serviço em nuvem pode aceder a bases de dados no SQL Server em máquinas virtuais do Azure. Um serviço em nuvem pode incluir várias funções web e de trabalho.

A tabela seguinte fornece uma comparação de desenvolvimento da web tradicional com serviços Cloud do Azure e aplicações Web do Azure em relação ao SQL Server em máquinas de virtuais do Azure. A tabela inclui aplicações Web do Azure, como é possível utilizar o SQL Server na VM do Azure como uma origem de dados para aplicações Web do Azure através do seu endereço IP virtual público ou o nome DNS.

|  | Desenvolvimento da web tradicional em máquinas de virtuais do Azure | Serviços cloud no Azure | Web de hospedagem com aplicações Web do Azure |
| --- | --- | --- | --- |
| **Migração de aplicações no local** |Os aplicativos existentes como-é. |Os aplicativos precisam funções web e de trabalho. |Os aplicativos existentes como-mas adequado para aplicações web independentes e serviços da web que necessitam de escalabilidade rápida. |
| **Desenvolvimento e implementação** |Visual Studio, WebMatrix, Visual Web Developer, WebDeploy, FTP, TFS, Gestor de IIS, PowerShell. |Visual Studio, SDK do Azure, TFS, do PowerShell. Cada serviço em nuvem tem dois ambientes a que pode implementar o pacote de serviço e configuração: teste e produção. Pode implementar um serviço em nuvem para o ambiente de teste para testá-lo antes de promover para produção. |Visual Studio, WebMatrix, Visual Web Developer, FTP, GIT, BitBucket, CodePlex, DropBox, GitHub, Mercurial, TFS, Web implementar, PowerShell. |
| **Administração e configuração** |É responsável por tarefas administrativas a aplicação, dados, regras de firewall, rede virtual e sistema operativo. |É responsável por tarefas administrativas na aplicação, dados, as regras de firewall e rede virtual. |É responsável por tarefas administrativas na aplicação e apenas dados. |
| **Elevada disponibilidade e recuperação após desastre (HADR)** |Recomendamos que coloque máquinas virtuais no mesmo conjunto de disponibilidade e no mesmo serviço cloud. Manter as suas VMs no mesmo conjunto de disponibilidade permite ao Azure colocar os nós de elevada disponibilidade em domínios de falha e domínios de atualização. Da mesma forma, manter as suas VMs no mesmo serviço cloud permite o balanceamento de carga e as VMs podem comunicar diretamente entre si através da rede local dentro de um centro de dados do Azure.<br/><br/>É responsável por implementar uma elevada disponibilidade e a solução de recuperação após desastre para o SQL Server em máquinas virtuais do Azure para evitar qualquer período de inatividade. Para tecnologias HADR suportadas, consulte [elevada disponibilidade e recuperação após desastre para SQL Server em máquinas de virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).<br/><br/>É responsável por fazer backup de seus próprios dados e a aplicação.<br/><br/>Azure pode mover as máquinas virtuais, se a máquina de anfitrião no Centro de dados falhar devido a problemas de hardware. Além disso, pode haver um período de indisponibilidade planeado da sua VM quando a máquina de anfitrião é atualizada para o software de segurança ou atualizações. Por conseguinte, recomendamos que mantenha, pelo menos, duas VMs em cada camada da aplicação para garantir a disponibilidade contínua. Azure não fornecem SLA para uma única máquina virtual. |O Azure gere as falhas resultantes do software de sistema operativo ou do hardware subjacente. Recomendamos que implementar várias instâncias de uma função web ou de trabalho para garantir a elevada disponibilidade da sua aplicação. Para obter informações, consulte [serviços Cloud, máquinas virtuais e contrato de nível de serviço de rede Virtual](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/).<br/><br/>É responsável por fazer backup de seus próprios dados e a aplicação.<br/><br/>Bases de dados que residem numa base de dados do SQL Server numa VM do Azure, são responsáveis por implementar uma solução de recuperação de desastre e de disponibilidade elevada para evitar qualquer período de inatividade. Para tecnologias HDAR suportadas, consulte elevada disponibilidade e recuperação após desastre para o SQL Server em máquinas de virtuais do Azure.<br/><br/>**Base de dados do SQL Server espelhamento**: Utilizar com os serviços do Azure na Cloud (funções da web/trabalho). VMs do SQL Server e um projeto de serviço em nuvem podem ser na mesma rede Virtual do Azure. Se a VM do SQL Server não estiver na mesma rede Virtual, terá de criar um Alias de servidor SQL para rotear comunicações para a instância do SQL Server. Além disso, o nome de alias tem de corresponder ao nome do servidor SQL. |Elevada disponibilidade é herdada de funções de trabalho do Azure, armazenamento de Blobs do Azure e base de dados do Azure SQL. Por exemplo, o armazenamento do Azure mantém três réplicas de todos os dados de blob, tabela e fila. Ao mesmo tempo, o Azure SQL Database mantém três réplicas dos dados em execução — uma réplica primária e duas réplicas secundárias. Para obter mais informações, consulte [armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/) e [base de dados do Azure SQL](../../../sql-database/sql-database-technical-overview.md).<br/><br/>Quando utilizar o SQL Server na VM do Azure como uma origem de dados para aplicações Web do Azure, tenha em atenção que aplicações Web do Azure não suporta a rede Virtual do Azure. Em outras palavras, todas as ligações de aplicações Web do Azure para VMs do SQL Server no Azure precisa passar por pontos de extremidade públicos das máquinas virtuais. Isto poderá provocar algumas limitações para elevada disponibilidade e cenários de recuperação após desastre. Por exemplo, a aplicação de cliente no Azure Web Apps ligar à VM do SQL Server com o espelhamento de banco de dados não seria capaz de ligar para o novo servidor primário como espelhamento de banco de dados requer que configure a rede Virtual do Azure entre as VMs de anfitrião do SQL Server no Azure. Por conseguinte, usando **espelhamento de banco de dados do SQL Server** com aplicações Web do Azure não é suportada atualmente.<br/><br/>**Grupos de Disponibilidade AlwaysOn do SQL Server**: Pode configurar grupos de Disponibilidade AlwaysOn ao utilizar aplicações Web do Azure com VMs do SQL Server no Azure. Mas precisa configurar escuta do grupo de Disponibilidade AlwaysOn para encaminhar a comunicação para a réplica primária por meio de pontos finais com balanceamento de carga públicos. |
| **Conectividade entre locais** |Pode utilizar a rede Virtual do Azure para ligar para o local. |Pode utilizar a rede Virtual do Azure para ligar para o local. |Rede Virtual do Azure é suportada. Para obter mais informações, consulte [integração da rede Virtual aplicações Web](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/). |
| **Escalabilidade** |Aumentar verticalmente está disponível por aumentando os tamanhos de máquina virtual ou adicionar mais discos. Para obter mais informações sobre os tamanhos de máquina virtual, consulte [tamanhos de Máquina Virtual do Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).<br/><br/>**Para o servidor de base de dados**: Escalamento horizontal está disponível por meio de técnicas de criação de partições de base de dados e os grupos de Disponibilidade AlwaysOn do SQL Server.<br/><br/>Para cargas de trabalho leitura pesadas, pode utilizar [grupos de Disponibilidade AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx) em vários nós secundários, bem como replicação do SQL Server.<br/><br/>Para cargas de trabalho de escrita intensivas, pode implementar dados de criação de partições horizontais em vários servidores físicos para fornecer aplicações escaláveis.<br/><br/>Além disso, pode implementar um Escalamento horizontal, utilizando [SQL Server com o encaminhamento dependente de dados](https://technet.microsoft.com/library/cc966448.aspx). Com dados dependentes encaminhamento (DDR), terá de implementar o mecanismo de particionamento no aplicativo cliente, normalmente na camada de camada de negócio, para encaminhar os pedidos de base de dados para vários nós do SQL Server. A camada de negócio contém mapeamentos de como os dados estão particionados e nó que contém os dados.<br/><br/>Pode dimensionar aplicações que estejam a executar máquinas virtuais. Para obter mais informações, consulte [como dimensionar uma aplicação](../../../cloud-services/cloud-services-how-to-scale-portal.md).<br/><br/>**Observação importante**: O **dimensionamento automático** funcionalidade no Azure permite-lhe automaticamente aumentar ou diminuir as máquinas virtuais que são utilizados pela sua aplicação. Esta funcionalidade garante que a experiência de utilizador final não é afetada negativamente durante períodos de pico e as VMs são encerradas quando a demanda é baixa. Recomenda-se que não defina a opção de dimensionamento automático do serviço em nuvem se ele inclui VMs do SQL Server. O motivo é que a funcionalidade de dimensionamento automático do Azure para ligar uma máquina virtual quando a utilização da CPU na VM for superior a algum limite e desativar uma máquina virtual quando a utilização da CPU passa menor do que o permite. A funcionalidade de dimensionamento automático é útil para aplicativos sem monitoração de estado, tais como servidores web, onde qualquer VM pode gerir a carga de trabalho sem quaisquer referências a qualquer estado anterior. No entanto, a funcionalidade de dimensionamento automático não é útil para aplicações com monitorização de estado, como o SQL Server, onde apenas uma instância permite escrever na base de dados. |Aumentar verticalmente está disponível através de várias funções web e de trabalho. Para obter mais informações sobre os tamanhos de máquina virtual para funções da web e funções de trabalho, consulte [tamanhos configurar para os serviços Cloud](../../../cloud-services/cloud-services-sizes-specs.md).<br/><br/>Ao usar **serviços Cloud**, pode definir várias funções para distribuir o processamento e também a alcançar um dimensionamento flexível da sua aplicação. Cada serviço em nuvem inclui um ou mais funções da web e/ou funções de trabalho, cada um com seus próprios arquivos do aplicativo e a configuração. Pode aumentar verticalmente um serviço em nuvem do aumento do número de instâncias de função (máquinas virtuais) implementado para uma função e reduzir verticalmente um serviço em nuvem ao reduzir o número de instâncias de função. Para obter informações detalhadas, consulte [modelos de execução do Azure](../../../cloud-services/cloud-services-choose-me.md).<br/><br/>Escalamento horizontal está disponível através do suporte de elevada disponibilidade incorporada do Azure através de [serviços Cloud, máquinas virtuais e contrato de nível de serviço de rede Virtual](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) e Balanceador de carga.<br/><br/>Para uma aplicação de várias camadas, recomendamos que se ligar a aplicação de funções da web/de trabalho para VMs através da rede Virtual do Azure do servidor de base de dados. Além disso, o Azure fornece balanceamento de carga para as VMs no mesmo serviço cloud, propagar os pedidos de utilizador por-los. As máquinas virtuais ligadas desta forma pode comunicar diretamente entre si através da rede local dentro de um centro de dados do Azure.<br/><br/>Pode configurar **dimensionamento automático** no portal do Azure, bem como os tempos de agenda. Para obter mais informações, consulte [como configurar o dimensionamento automático para um serviço em nuvem no portal do](../../../cloud-services/cloud-services-how-to-scale-portal.md). |**Aumente e diminua**: Pode aumentar/diminuir o tamanho da instância (VM) reservada para seu web site.<br/><br/>Aumente horizontalmente: Pode adicionar instâncias reservadas mais (VMs) do seu web site.<br/><br/>Pode configurar **dimensionamento automático** no portal, bem como os tempos de agenda. Para obter mais informações, consulte [como aplicações Web de escala](../../../app-service/web-sites-scale.md). |

Para obter mais informações sobre como escolher entre esses métodos de programação, consulte [aplicações Web do Azure, serviços Cloud e VMs: Quando utilizar qual](/azure/architecture/guide/technology-choices/compute-decision-tree).

## <a name="next-steps"></a>Próximos Passos
Para obter mais informações sobre como executar o SQL Server em máquinas virtuais do Azure, consulte [SQL Server em Azure Virtual Machines Overview](virtual-machines-windows-sql-server-iaas-overview.md).

