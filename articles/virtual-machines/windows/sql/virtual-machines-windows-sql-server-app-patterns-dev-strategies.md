---
title: Padrões de aplicativo SQL Server em VMs | Microsoft Docs
description: Este artigo aborda os padrões de aplicativo para SQL Server em VMs do Azure. Ele fornece aos arquitetos e desenvolvedores de soluções uma base para uma boa arquitetura e design de aplicativos.
services: virtual-machines-windows
documentationcenter: na
author: ninarn
manager: craigg
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 41863c8d-f3a3-4584-ad86-b95094365e05
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: ninarn
ms.openlocfilehash: 89935669d9dd2f7a30fbdc80694cc822df044c78
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102036"
---
# <a name="application-patterns-and-development-strategies-for-sql-server-in-azure-virtual-machines"></a>Padrões de Aplicação e Estratégias de Desenvolvimento para o SQL Server em Máquinas Virtuais do Azure
[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="summary"></a>Resumo:
Determinar o padrão ou padrões de aplicativo a serem usados para seus aplicativos baseados em SQL Server no ambiente do Azure é uma decisão de design importante e requer uma compreensão sólida de como SQL Server e cada componente de infra-estrutura do Azure funciona em conjunto. Com SQL Server nos serviços de infraestrutura do Azure, você pode facilmente migrar, manter e monitorar seus aplicativos existentes do SQL Server no Windows Server para máquinas virtuais no Azure.

O objetivo deste artigo é fornecer aos arquitetos e desenvolvedores de soluções uma base para uma boa arquitetura e design de aplicativos, que eles podem seguir ao migrar aplicativos existentes para o Azure, bem como desenvolver novos aplicativos no Azure.

Para cada padrão de aplicativo, você encontrará um cenário local, sua respectiva solução habilitada para nuvem e as recomendações técnicas relacionadas. Além disso, o artigo discute estratégias de desenvolvimento específicas do Azure para que você possa projetar seus aplicativos corretamente. Devido aos muitos padrões de aplicativo possíveis, é recomendável que arquitetos e desenvolvedores escolham o padrão mais apropriado para seus aplicativos e usuários.

**Colaboradores técnicos:** Luis Carlos Vargas Herring, Madhan Arumugam Ramakrishnan

**Revisores técnicos:** Corey Sanders, desenhei McDaniel, Narayan Annamalai, nir Mashkowski, Sanjay Mishra, Silvano Coriani, Stefan Schackow, Tim Hickey, Tim Wieman, Xin Jin

## <a name="introduction"></a>Introdução
Você pode desenvolver muitos tipos de aplicativos de n camadas separando os componentes das diferentes camadas de aplicativo em computadores diferentes, bem como em componentes separados. Por exemplo, você pode posicionar os componentes do aplicativo cliente e das regras de negócio em um computador, na camada da Web de front-end e nos componentes da camada de acesso a dados em outra máquina e em uma camada de banco de dados back-end em outra máquina. Esse tipo de estruturação ajuda a isolar cada camada umas das outras. Se você alterar a origem dos dados, não precisará alterar o cliente ou o aplicativo Web, mas apenas os componentes da camada de acesso a dados.

Um aplicativo de *n camadas* típico inclui a camada de apresentação, a camada de negócios e a camada de dados:

| Escalão | Descrição |
| --- | --- |
| **Presentation** |A camada de *apresentação* (camada da Web, camada de front-end) é a camada na qual os usuários interagem com um aplicativo. |
| **Essenciais** |A camada de *negócios* (camada intermediária) é a camada que a camada de apresentação e a camada de dados usam para se comunicar entre si e inclui a funcionalidade básica do sistema. |
| **Dados** |A *camada de dados* é basicamente o servidor que armazena os dados de um aplicativo (por exemplo, um servidor que executa o SQL Server). |

As camadas de aplicativo descrevem os agrupamentos lógicos da funcionalidade e dos componentes em um aplicativo; enquanto as camadas descrevem a distribuição física da funcionalidade e dos componentes em servidores físicos separados, computadores, redes ou locais remotos. As camadas de um aplicativo podem residir no mesmo computador físico (a mesma camada) ou podem ser distribuídas em computadores separados (n camadas), e os componentes em cada camada se comunicam com os componentes em outras camadas por meio de interfaces bem definidas. Você pode considerar o tipo de termo como referência a padrões de distribuição física, como duas camadas, três camadas e n camadas. Um **padrão de aplicativo de 2 camadas** contém duas camadas de aplicativo: servidor de aplicativos e servidor de banco de dados. A comunicação direta ocorre entre o servidor de aplicativos e o servidor de banco de dados. O servidor de aplicativos contém componentes da camada da Web e da camada de negócios. No **padrão de aplicativo de 3 camadas**, há três camadas de aplicativo: servidor Web, servidor de aplicativos, que contém a camada de lógica de negócios e/ou componentes de acesso a dados de camada de negócios e o servidor de banco de dado. A comunicação entre o servidor Web e o servidor de banco de dados ocorre no servidor de aplicativos. Para obter informações detalhadas sobre camadas de aplicativo e camadas, consulte [o guia de arquitetura do aplicativo da Microsoft](https://msdn.microsoft.com/library/ff650706.aspx).

Antes de começar a ler este artigo, você deve ter conhecimento dos conceitos fundamentais do SQL Server e do Azure. Para obter informações, consulte [manuais online do SQL Server](https://msdn.microsoft.com/library/bb545450.aspx), [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md) e [Azure.com](https://azure.microsoft.com/).

Este artigo descreve vários padrões de aplicativo que podem ser adequados para seus aplicativos simples, bem como para aplicativos empresariais altamente complexos. Antes de detalhar cada padrão, recomendamos que você Familiarize-se com os serviços de armazenamento de dados disponíveis no Azure, como o [armazenamento do Azure](../../../storage/common/storage-introduction.md), o [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md)e o [SQL Server em uma máquina virtual do Azure](virtual-machines-windows-sql-server-iaas-overview.md). Para tomar as melhores decisões de design para seus aplicativos, entenda quando usar o serviço de armazenamento de dados claramente.

### <a name="choose-sql-server-in-an-azure-virtual-machine-when"></a>Escolha SQL Server em uma máquina virtual do Azure, quando:
* Você precisa de controle no SQL Server e no Windows. Por exemplo, isso pode incluir a versão SQL Server, hotfixes especiais, configuração de desempenho, etc.
* Você precisa de uma compatibilidade total com o SQL Server local e deseja mover os aplicativos existentes para o Azure no estado em que se encontram.
* Você deseja aproveitar os recursos do ambiente do Azure, mas o banco de dados SQL do Azure não oferece suporte a todos os recursos que seu aplicativo requer. Isso pode incluir as seguintes áreas:
  
  * **Tamanho do banco de dados**: No momento em que este artigo foi atualizado, o banco de dados SQL dá suporte a um banco de dado de até 1 TB de data. Se seu aplicativo exigir mais de 1 TB de dados e você não quiser implementar soluções de fragmentação personalizadas, é recomendável usar SQL Server em uma máquina virtual do Azure. Para obter as informações mais recentes, consulte dimensionando o [banco de dados SQL do Azure](https://msdn.microsoft.com/library/azure/dn495641.aspx), [modelo de compra baseado em DTU](../../../sql-database/sql-database-service-tiers-dtu.md)e [modelo de compra baseado em vCore](../../../sql-database/sql-database-service-tiers-vcore.md)(versão prévia).
  * **Conformidade com HIPAA**: Os clientes de saúde e os ISVs (fornecedores independentes de software) podem escolher [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md) em vez do [banco de dados SQL do Azure](../../../sql-database/sql-database-technical-overview.md) porque SQL Server em uma máquina virtual do Azure é coberta pelo Baa (contrato de parceiro comercial da HIPAA) . Para obter informações sobre conformidade, [consulte a central de confiabilidade do Microsoft Azure: Conformidade](https://azure.microsoft.com/support/trust-center/compliance/).
  * **Recursos de nível de instância**: Neste momento, o banco de dados SQL não dá suporte a recursos que residem fora do banco de dados (como servidores vinculados, trabalhos do Agent, FileStream, Service Broker, etc.). Para obter mais informações, consulte [diretrizes e limitações do banco de dados SQL do Azure](https://msdn.microsoft.com/library/azure/ff394102.aspx).

## <a name="1-tier-simple-single-virtual-machine"></a>1 camada (simples): máquina virtual única
Nesse padrão de aplicativo, você implanta seu aplicativo SQL Server e o banco de dados em uma máquina virtual autônoma no Azure. A mesma máquina virtual contém seu aplicativo cliente/Web, componentes de negócios, camada de acesso de dados e o servidor de banco de dados. A apresentação, os negócios e o código de acesso a dados são separados logicamente, mas estão fisicamente localizados em uma máquina de servidor único. A maioria dos clientes começa com esse padrão de aplicativo e, em seguida, eles são expandidos adicionando mais funções Web ou máquinas virtuais ao sistema.

Esse padrão de aplicativo é útil quando:

* Você deseja executar uma migração simples para a plataforma do Azure para avaliar se a plataforma atende aos requisitos do seu aplicativo ou não.
* Você deseja manter todas as camadas de aplicativo hospedadas na mesma máquina virtual na mesma data center do Azure para reduzir a latência entre as camadas.
* Você deseja provisionar rapidamente ambientes de desenvolvimento e teste por curtos períodos de tempo.
* Você deseja executar testes de estresse para vários níveis de carga de trabalho, mas ao mesmo tempo você não deseja possuir e manter muitas máquinas físicas o tempo todo.

O diagrama a seguir demonstra um cenário local simples e como você pode implantar sua solução habilitada para nuvem em uma única máquina virtual no Azure.

![padrão de aplicativo de 1 camada](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728008.png)

Implantar a camada de negócios (lógica de negócios e componentes de acesso a dados) na mesma camada física que a camada de apresentação pode maximizar o desempenho do aplicativo, a menos que você precise usar uma camada separada devido a questões de escalabilidade ou segurança.

Como esse é um padrão muito comum para começar, você pode encontrar o seguinte artigo sobre migração útil para mover seus dados para a VM SQL Server: [Migrar um banco de dados para SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>3 camadas (simples): várias máquinas virtuais
Nesse padrão de aplicativo, você implanta um aplicativo de três camadas no Azure colocando cada camada de aplicativo em uma máquina virtual diferente. Isso fornece um ambiente flexível para cenários fáceis de expansão e redução. Quando uma máquina virtual contém seu aplicativo cliente/Web, a outra hospeda seus componentes de negócios e a outra hospeda o servidor de banco de dados.

Esse padrão de aplicativo é útil quando:

* Você deseja executar uma migração de aplicativos de banco de dados complexos para máquinas virtuais do Azure.
* Você deseja que diferentes camadas de aplicativo sejam hospedadas em regiões diferentes. Por exemplo, você pode ter bancos de dados compartilhados que são implantados em várias regiões para fins de relatório.
* Você deseja mover aplicativos empresariais de plataformas virtualizadas locais para máquinas virtuais do Azure. Para obter uma discussão detalhada sobre aplicativos empresariais, consulte [o que é um aplicativo empresarial](https://msdn.microsoft.com/library/aa267045.aspx).
* Você deseja provisionar rapidamente ambientes de desenvolvimento e teste por curtos períodos de tempo.
* Você deseja executar testes de estresse para vários níveis de carga de trabalho, mas ao mesmo tempo você não deseja possuir e manter muitas máquinas físicas o tempo todo.

O diagrama a seguir demonstra como você pode colocar um aplicativo simples de três camadas no Azure colocando cada camada de aplicativo em uma máquina virtual diferente.

![padrão de aplicativo de três camadas](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728009.png)

Nesse padrão de aplicativo, há apenas uma VM (máquina virtual) em cada camada. Se você tiver várias VMs no Azure, recomendamos que configure uma rede virtual. A [rede virtual do Azure](../../../virtual-network/virtual-networks-overview.md) cria um limite de segurança confiável e também permite que as VMs se comuniquem entre si por meio do endereço IP privado. Além disso, sempre certifique-se de que todas as conexões com a Internet vão apenas para a camada de apresentação. Ao seguir esse padrão de aplicativo, gerencie as regras do grupo de segurança de rede para controlar o acesso. Para obter mais informações, consulte [permitir acesso externo à sua VM usando o portal do Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

No diagrama, os protocolos de Internet podem ser TCP, UDP, HTTP ou HTTPS.

> [!NOTE]
> A configuração de uma rede virtual no Azure é gratuita. No entanto, você é cobrado pelo gateway de VPN que se conecta ao local. Essa cobrança é baseada na quantidade de tempo que a conexão é provisionada e está disponível.
> 
> 

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>2 camadas e 3 camadas com expansão da camada de apresentação
Nesse padrão de aplicativo, você implanta o aplicativo de banco de dados de duas ou três camadas em máquinas virtuais do Azure colocando cada camada de aplicativo em uma máquina virtual diferente. Além disso, você escala horizontalmente a camada de apresentação devido ao maior volume de solicitações de entrada de clientes.

Esse padrão de aplicativo é útil quando:

* Você deseja mover aplicativos empresariais de plataformas virtualizadas locais para máquinas virtuais do Azure.
* Você deseja escalar horizontalmente a camada de apresentação devido ao volume maior de solicitações de cliente de entrada.
* Você deseja provisionar rapidamente ambientes de desenvolvimento e teste por curtos períodos de tempo.
* Você deseja executar testes de estresse para vários níveis de carga de trabalho, mas ao mesmo tempo você não deseja possuir e manter muitas máquinas físicas o tempo todo.
* Você deseja ter um ambiente de infraestrutura que possa ser escalado verticalmente e reduzido sob demanda.

O diagrama a seguir demonstra como você pode posicionar as camadas de aplicativo em várias máquinas virtuais no Azure expandindo a camada de apresentação devido ao aumento do volume de solicitações de clientes de entrada. Como visto no diagrama, Azure Load Balancer é responsável por distribuir o tráfego entre várias máquinas virtuais e também determinar a qual servidor Web se conectar. Ter várias instâncias dos servidores Web por trás de um balanceador de carga garante a alta disponibilidade da camada de apresentação.

![Padrão de aplicativo-camada de apresentação escalar horizontalmente](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Práticas recomendadas para padrões de duas camadas, de três camadas ou de n camadas que têm várias VMs em uma camada
É recomendável que você coloque as máquinas virtuais que pertencem à mesma camada no mesmo serviço de nuvem e no mesmo conjunto de disponibilidade. Por exemplo, coloque um conjunto de servidores Web em **CloudService1** e **AvailabilitySet1** e um conjunto de servidores de banco de dados em **CloudService2** e **AvailabilitySet2**. Um conjunto de disponibilidade no Azure permite que você coloque os nós de alta disponibilidade em domínios de falha e domínios de atualização separados.

Para aproveitar várias instâncias de VM de uma camada, você precisa configurar Azure Load Balancer entre as camadas do aplicativo. Para configurar Load Balancer em cada camada, crie um ponto de extremidade com balanceamento de carga em cada VM da camada separadamente. Para uma camada específica, primeiro crie VMs no mesmo serviço de nuvem. Isso garante que eles tenham o mesmo endereço IP virtual público. Em seguida, crie um ponto de extremidade em uma das máquinas virtuais nessa camada. Em seguida, atribua o mesmo ponto de extremidade às outras máquinas virtuais nessa camada para o balanceamento de carga. Ao criar um conjunto com balanceamento de carga, você distribui o tráfego entre várias máquinas virtuais e também permite que o Load Balancer determine qual nó se conectar quando um nó de VM de back-end falhar. Por exemplo, ter várias instâncias dos servidores Web por trás de um balanceador de carga garante a alta disponibilidade da camada de apresentação.

Como prática recomendada, sempre certifique-se de que todas as conexões com a Internet primeiro vão para a camada de apresentação. A camada de apresentação acessa a camada de negócios e, em seguida, a camada de negócios acessa a camada de dados. Para obter mais informações sobre como permitir o acesso à camada de apresentação, consulte [permitir acesso externo à sua VM usando o portal do Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Observe que o Load Balancer no Azure funciona de forma semelhante aos balanceadores de carga em um ambiente local. Para obter mais informações, consulte [balanceamento de carga para serviços de infraestrutura do Azure](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Além disso, recomendamos que você configure uma rede privada para suas máquinas virtuais usando a rede virtual do Azure. Isso permite que eles se comuniquem entre si por meio do endereço IP privado. Para obter mais informações, consulte [rede virtual do Azure](../../../virtual-network/virtual-networks-overview.md).

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>2 camadas e 3 camadas com expansão da camada de negócios
Nesse padrão de aplicativo, você implanta o aplicativo de banco de dados de duas ou três camadas em máquinas virtuais do Azure colocando cada camada de aplicativo em uma máquina virtual diferente. Além disso, talvez você queira distribuir os componentes do servidor de aplicativos para várias máquinas virtuais devido à complexidade do seu aplicativo.

Esse padrão de aplicativo é útil quando:

* Você deseja mover aplicativos empresariais de plataformas virtualizadas locais para máquinas virtuais do Azure.
* Você deseja distribuir os componentes do servidor de aplicativos para várias máquinas virtuais devido à complexidade do seu aplicativo.
* Você deseja mover aplicativos LOB (linha de negócios) de lógica de negócios pesados para as máquinas virtuais do Azure. Os aplicativos LOB são um conjunto de aplicativos de computador críticos que são vitais para a execução de uma empresa, como contabilidade, recursos humanos (RH), folha de pagamento, gerenciamento de cadeia de fornecedores e aplicativos de planejamento de recursos.
* Você deseja provisionar rapidamente ambientes de desenvolvimento e teste por curtos períodos de tempo.
* Você deseja executar testes de estresse para vários níveis de carga de trabalho, mas ao mesmo tempo você não deseja possuir e manter muitas máquinas físicas o tempo todo.
* Você deseja ter um ambiente de infraestrutura que possa ser escalado verticalmente e reduzido sob demanda.

O diagrama a seguir demonstra um cenário local e sua solução de nuvem habilitada. Nesse cenário, você coloca as camadas de aplicativo em várias máquinas virtuais no Azure expandindo a camada de negócios, que contém a camada de lógica de negócios e os componentes de acesso a dados. Como visto no diagrama, Azure Load Balancer é responsável por distribuir o tráfego entre várias máquinas virtuais e também determinar a qual servidor Web se conectar. Ter várias instâncias dos servidores de aplicativos por trás de um balanceador de carga garante a alta disponibilidade da camada de negócios. Para obter mais informações, consulte [práticas recomendadas para padrões de aplicativos de duas camadas, de três camadas ou de n camadas que têm várias máquinas virtuais em uma camada](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Padrão de aplicativo com camada de negócios escalar horizontalmente](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>2 camadas e 3 camadas com a apresentação e as camadas de negócios expansão e HADR
Nesse padrão de aplicativo, você implanta o aplicativo de banco de dados de duas ou três camadas em máquinas virtuais do Azure distribuindo os componentes da camada de apresentação (servidor Web) e da camada de negócios (servidor de aplicativos) para várias máquinas virtuais. Além disso, você implementa soluções de alta disponibilidade e recuperação de desastres para seus bancos de dados em máquinas virtuais do Azure.

Esse padrão de aplicativo é útil quando:

* Você deseja mover aplicativos corporativos de plataformas virtualizadas locais para o Azure implementando SQL Server recursos de alta disponibilidade e recuperação de desastres.
* Você deseja escalar horizontalmente a camada de apresentação e a camada de negócios devido ao maior volume de solicitações de clientes de entrada e à complexidade do seu aplicativo.
* Você deseja provisionar rapidamente ambientes de desenvolvimento e teste por curtos períodos de tempo.
* Você deseja executar testes de estresse para vários níveis de carga de trabalho, mas ao mesmo tempo você não deseja possuir e manter muitas máquinas físicas o tempo todo.
* Você deseja ter um ambiente de infraestrutura que possa ser escalado verticalmente e reduzido sob demanda.

O diagrama a seguir demonstra um cenário local e sua solução de nuvem habilitada. Nesse cenário, você escala horizontalmente os componentes da camada de apresentação e da camada de negócios em várias máquinas virtuais no Azure. Além disso, você implementa técnicas de alta disponibilidade e de recuperação de desastres (HADR) para bancos de dados SQL Server no Azure.

Executando várias cópias de um aplicativo em VMs diferentes, certifique-se de que você está fazendo o balanceamento de carga de solicitações entre elas. Quando você tiver várias máquinas virtuais, precisará certificar-se de que todas as suas VMs estejam acessíveis e em execução em um ponto no tempo. Se você configurar o balanceamento de carga, o Azure Load Balancer acompanhará a integridade das VMs e direcionará as chamadas de entrada para os nós de VM funcionando corretamente. Para obter informações sobre como configurar o balanceamento de carga das máquinas virtuais, consulte [balanceamento de carga para serviços de infraestrutura do Azure](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ter várias instâncias de servidores Web e de aplicativos por trás de um balanceador de carga garante a alta disponibilidade das camadas de apresentação e de negócios.

![Expansão e alta disponibilidade](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Práticas recomendadas para padrões de aplicativos que exigem o SQL HADR
Quando você configura SQL Server soluções de alta disponibilidade e recuperação de desastres em máquinas virtuais do Azure, a configuração de uma rede virtual para suas máquinas virtuais usando a [rede virtual do Azure](../../../virtual-network/virtual-networks-overview.md) é obrigatória.  As máquinas virtuais em uma rede virtual terão um endereço IP privado estável mesmo após um tempo de inatividade do serviço, portanto, você pode evitar o tempo de atualização necessário para a resolução de nomes DNS. Além disso, a rede virtual permite que você estenda sua rede local para o Azure e crie um limite de segurança confiável. Por exemplo, se seu aplicativo tiver restrições de domínio corporativo (como, autenticação do Windows Active Directory), a configuração da [rede virtual do Azure](../../../virtual-network/virtual-networks-overview.md) será necessária.

A maioria dos clientes, que estão executando o código de produção no Azure, estão mantendo as réplicas primárias e secundárias no Azure.

Para obter informações abrangentes e tutoriais sobre técnicas de alta disponibilidade e recuperação de desastres, consulte [alta disponibilidade e recuperação de desastre para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="2-tier-and-3-tier-using-azure-vms-and-cloud-services"></a>2 camadas e 3 camadas usando VMs do Azure e serviços de nuvem
Nesse padrão de aplicativo, você implanta o aplicativo de duas ou três camadas no Azure usando os serviços de [nuvem do Azure](../../../cloud-services/cloud-services-choose-me.md) (funções Web e de trabalho – PaaS (plataforma como serviço)) e [máquinas virtuais do Azure](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (infraestrutura como serviço (IaaS)). Usar os [serviços de nuvem do Azure](https://azure.microsoft.com/documentation/services/cloud-services/) para a camada de apresentação/nível comercial e SQL Server nas [máquinas virtuais do Azure](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para a camada de dados é benéfico para a maioria dos aplicativos em execução no Azure. O motivo é que ter uma instância de computação em execução nos serviços de nuvem fornece um gerenciamento, implantação, monitoramento e expansão fáceis.

Com os serviços de nuvem, o Azure mantém a infraestrutura para você, executa a manutenção rotineira, corrige os sistemas operacionais e tenta se recuperar de falhas de serviço e de hardware. Quando seu aplicativo precisa de expansão, as opções de expansão automática e manual estão disponíveis para seu projeto de serviço de nuvem, aumentando ou diminuindo o número de instâncias ou máquinas virtuais usadas pelo seu aplicativo. Além disso, você pode usar o Visual Studio local para implantar seu aplicativo em um projeto de serviço de nuvem no Azure.

Em resumo, se você não quiser ter tarefas administrativas extensivas para a camada de apresentação/negócios e seu aplicativo não exigir nenhuma configuração complexa de software ou do sistema operacional, use os serviços de nuvem do Azure. Se o banco de dados SQL do Azure não oferecer suporte a todos os recursos que você está procurando, use SQL Server em uma máquina virtual do Azure para a camada data. A execução de um aplicativo nos serviços de nuvem do Azure e o armazenamento de dados em máquinas virtuais do Azure combina os benefícios de ambos os serviços. Para obter uma comparação detalhada, consulte a seção neste tópico sobre como [comparar estratégias de desenvolvimento no Azure](#comparing-web-development-strategies-in-azure).

Nesse padrão de aplicativo, a camada de apresentação inclui uma função Web, que é um componente de serviços de nuvem em execução no ambiente de execução do Azure e é personalizado para programação de aplicativo Web com suporte do IIS e do ASP.NET. A camada Business ou back-end inclui uma função de trabalho, que é um componente de serviços de nuvem em execução no ambiente de execução do Azure e é útil para o desenvolvimento generalizado e pode executar o processamento em segundo plano para uma função Web. A camada de banco de dados reside em uma máquina virtual SQL Server no Azure. A comunicação entre a camada de apresentação e a camada de banco de dados ocorre diretamente ou pela camada de negócios – componentes da função de trabalho.

Esse padrão de aplicativo é útil quando:

* Você deseja mover aplicativos corporativos de plataformas virtualizadas locais para o Azure implementando SQL Server recursos de alta disponibilidade e recuperação de desastres.
* Você deseja ter um ambiente de infraestrutura que possa ser escalado verticalmente e reduzido sob demanda.
* O banco de dados SQL do Azure não dá suporte a todos os recursos que seu aplicativo ou banco de dados precisa.
* Você deseja executar testes de estresse para vários níveis de carga de trabalho, mas ao mesmo tempo você não deseja possuir e manter muitas máquinas físicas o tempo todo.

O diagrama a seguir demonstra um cenário local e sua solução de nuvem habilitada. Nesse cenário, você coloca a camada de apresentação em funções Web, a camada de negócios nas funções de trabalho, mas a camada de dados em máquinas virtuais no Azure. A execução de várias cópias da camada de apresentação em diferentes funções da Web garante que as solicitações de balanceamento de carga entre elas. Ao combinar os serviços de nuvem do Azure com as máquinas virtuais do Azure, recomendamos que você configure também a [rede virtual do Azure](../../../virtual-network/virtual-networks-overview.md) . Com a [rede virtual do Azure](../../../virtual-network/virtual-networks-overview.md), você pode ter endereços IP privados estáveis e persistentes dentro do mesmo serviço de nuvem na nuvem. Depois de definir uma rede virtual para suas máquinas virtuais e serviços de nuvem, eles podem começar a se comunicar entre si por meio do endereço IP privado. Além disso, ter máquinas virtuais e funções Web/de trabalho do Azure na mesma [rede virtual do Azure](../../../virtual-network/virtual-networks-overview.md) fornece baixa latência e conectividade mais segura. Para obter mais informações, consulte [o que é um serviço de nuvem](../../../cloud-services/cloud-services-choose-me.md).

Como visto no diagrama, o Azure Load Balancer distribui o tráfego entre várias máquinas virtuais e também determina a qual servidor Web ou servidor de aplicativos se conectar. Ter várias instâncias dos servidores Web e de aplicativo por trás de um balanceador de carga garante a alta disponibilidade da camada de apresentação e da camada de negócios. Para obter mais informações, consulte [práticas recomendadas para padrões de aplicativo que exigem o SQL HADR](#best-practices-for-application-patterns-requiring-sql-hadr).

![Padrões de aplicativo com serviços de nuvem](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728013.png)

Outra abordagem para implementar esse padrão de aplicativo é usar uma função Web consolidada que contenha componentes da camada de apresentação e da camada de negócios, conforme mostrado no diagrama a seguir. Esse padrão de aplicativo é útil para aplicativos que exigem design com estado. Como o Azure fornece nós de computação sem monitoração de estado em funções Web e de trabalho, recomendamos que você implemente uma lógica para armazenar o estado de sessão usando uma das seguintes tecnologias: [Cache do Azure](https://azure.microsoft.com/documentation/services/azure-cache-for-redis/), [armazenamento de tabelas do Azure](../../../cosmos-db/table-storage-how-to-use-dotnet.md) ou [banco de dados SQL do Azure](../../../sql-database/sql-database-technical-overview.md).

![Padrões de aplicativo com serviços de nuvem](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728014.png)

## <a name="pattern-with-azure-vms-azure-sql-database-and-azure-app-service-web-apps"></a>Padrão com VMs do Azure, banco de dados SQL do Azure e serviço de Azure App (aplicativos Web)
O objetivo principal desse padrão de aplicativo é mostrar como combinar componentes de IaaS (infraestrutura como serviço) do Azure com os componentes de PaaS (plataforma como serviço) do Azure em sua solução. Esse padrão concentra-se no banco de dados SQL do Azure para armazenamento de dado relacional. Ele não inclui SQL Server em uma máquina virtual do Azure, que faz parte da oferta de infraestrutura como serviço do Azure.

Nesse padrão de aplicativo, você implanta um aplicativo de banco de dados no Azure colocando as camadas de apresentação e de negócios na mesma máquina virtual e acessando um banco de dados em servidores do banco de dados SQL do Azure (banco de dados SQL). Você pode implementar a camada de apresentação usando soluções da Web tradicionais baseadas no IIS. Ou, você pode implementar uma camada combinada de apresentação e de negócios usando o [serviço Azure app](https://azure.microsoft.com/documentation/services/app-service/web/).

Esse padrão de aplicativo é útil quando:

* Você já tem um servidor de banco de dados SQL existente configurado no Azure e deseja testar seu aplicativo rapidamente.
* Você deseja testar os recursos do ambiente do Azure.
* Você deseja provisionar rapidamente ambientes de desenvolvimento e teste por curtos períodos de tempo.
* Sua lógica de negócios e os componentes de acesso a dados podem ser independentes em um aplicativo Web.

O diagrama a seguir demonstra um cenário local e sua solução de nuvem habilitada. Nesse cenário, você coloca as camadas de aplicativo em uma única máquina virtual no Azure e acessa os dados no banco de dado SQL do Azure.

![Padrão de aplicativo misto](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728015.png)

Se você optar por implementar uma camada de aplicativo e Web combinada usando aplicativos Web do Azure, recomendamos que você mantenha a camada intermediária ou de aplicativo como DLLs (bibliotecas de vínculo dinâmico) no contexto de um aplicativo Web.

Além disso, examine as recomendações fornecidas na seção [comparando estratégias de desenvolvimento da Web no Azure](#comparing-web-development-strategies-in-azure) no final deste artigo para saber mais sobre técnicas de programação.

## <a name="n-tier-hybrid-application-pattern"></a>Padrão de aplicativo híbrido de N camadas
No padrão de aplicativo híbrido de n camadas, você implementa seu aplicativo em várias camadas distribuídas entre o local e o Azure. Portanto, você cria um sistema híbrido flexível e reutilizável, que pode modificar ou adicionar uma camada específica sem alterar as outras camadas. Para estender sua rede corporativa para a nuvem, use o serviço de [rede virtual do Azure](../../../virtual-network/virtual-networks-overview.md) .

Esse padrão de aplicativo híbrido é útil quando:

* Você deseja criar aplicativos que são executados parcialmente na nuvem e parcialmente no local.
* Você deseja migrar alguns ou todos os elementos de um aplicativo local existente para a nuvem.
* Você deseja mover aplicativos empresariais de plataformas virtualizadas locais para o Azure.
* Você deseja ter um ambiente de infraestrutura que possa ser escalado verticalmente e reduzido sob demanda.
* Você deseja provisionar rapidamente ambientes de desenvolvimento e teste por curtos períodos de tempo.
* Você deseja uma maneira econômica de fazer backups para aplicativos de banco de dados corporativo.

O diagrama a seguir demonstra um padrão de aplicativo híbrido de n camadas que se estende pelo local e pelo Azure. Como mostrado no diagrama, a infraestrutura local inclui [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) controlador de domínio para dar suporte à autenticação e autorização do usuário. Observe que o diagrama demonstra um cenário, em que algumas partes da camada de dados residem em um data center local, enquanto algumas partes da camada de dados residem no Azure. Dependendo das necessidades do seu aplicativo, você pode implementar vários outros cenários híbridos. Por exemplo, você pode manter a camada de apresentação e a camada de negócios em um ambiente local, mas a camada de dados no Azure.

![Padrão de aplicativo de N camadas](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728016.png)

No Azure, você pode usar Active Directory como um diretório de nuvem autônomo para sua organização ou também pode integrar Active Directory existentes no local com o [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Como visto no diagrama, os componentes da camada de negócios podem acessar várias fontes de dados, como [SQL Server no Azure](virtual-machines-windows-sql-server-iaas-overview.md) por meio de um endereço IP interno privado, para SQL Server local por meio da [rede virtual do Azure](../../../virtual-network/virtual-networks-overview.md)ou para o [banco de dados SQL](../../../sql-database/sql-database-technical-overview.md) usando o. Tecnologias do provedor de dados do .NET Framework. Neste diagrama, o banco de dados SQL do Azure é um serviço de armazenamento de dado opcional.

No padrão de aplicativo híbrido de n camadas, você pode implementar o seguinte fluxo de trabalho na ordem especificada:

1. Identifique aplicativos de banco de dados corporativos que precisam ser movidos para a nuvem usando o [Microsoft Assessment and Planning (MAP) Toolkit](https://microsoft.com/map). O MAP Toolkit coleta dados de desempenho e inventário de computadores que você está considerando para a virtualização e fornece recomendações sobre planejamento de capacidade e avaliação.
2. Planeje os recursos e a configuração necessários na plataforma Azure, como contas de armazenamento e máquinas virtuais.
3. Configure a conectividade de rede entre a rede corporativa local e a [rede virtual do Azure](../../../virtual-network/virtual-networks-overview.md). Para configurar a conexão entre a rede corporativa local e uma máquina virtual no Azure, use um dos dois métodos a seguir:
   
   1. Estabeleça uma conexão entre o local e o Azure por meio de pontos de extremidade públicos em uma máquina virtual no Azure. Esse método fornece uma configuração fácil e permite que você use SQL Server autenticação em sua máquina virtual. Além disso, configure as regras do grupo de segurança de rede para controlar o tráfego público para a VM. Para obter mais informações, consulte [permitir acesso externo à sua VM usando o portal do Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   2. Estabeleça uma conexão entre o local e o Azure por meio do túnel de VPN (rede virtual privada) do Azure. Esse método permite que você estenda as políticas de domínio para uma máquina virtual no Azure. Além disso, você pode configurar regras de firewall e usar a autenticação do Windows em sua máquina virtual. Atualmente, o Azure dá suporte a conexões VPN site a site seguras e VPN de ponto a site:
      
      * Com a conexão segura site a site, você pode estabelecer a conectividade de rede entre sua rede local e sua rede virtual no Azure. É recomendável conectar seu ambiente de data center local ao Azure.
      * Com a conexão ponto a site segura, você pode estabelecer a conectividade de rede entre sua rede virtual no Azure e seus computadores individuais em execução em qualquer lugar. É principalmente recomendado para fins de desenvolvimento e teste.
      
      Para obter informações sobre como se conectar ao SQL Server no Azure, consulte [conectar-se a uma máquina Virtual SQL Server no Azure](virtual-machines-windows-sql-connect.md).
4. Configure trabalhos agendados e alertas que fazem backup de dados locais em um disco de máquina virtual no Azure. Para obter mais informações, consulte [SQL Server Backup e restauração com o serviço de armazenamento de BLOBs do Azure](https://msdn.microsoft.com/library/jj919148.aspx) e [backup e restauração para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md).
5. Dependendo das necessidades do seu aplicativo, você pode implementar um dos três cenários comuns a seguir:
   
   1. Você pode manter o servidor Web, o servidor de aplicativos e os dados insensíveis em um servidor de banco de dado no Azure, enquanto mantém os dados confidenciais no local.
   2. Você pode manter o servidor Web e o servidor de aplicativos no local, enquanto o servidor de banco de dados em uma máquina virtual no Azure.
   3. Você pode manter seu servidor de banco de dados, servidor Web e servidor de aplicativos no local, enquanto mantém as réplicas de banco de dados em máquinas virtuais no Azure. Essa configuração permite que os servidores Web ou aplicativos de relatório locais acessem as réplicas de banco de dados no Azure. Portanto, você pode conseguir reduzir a carga de trabalho em um banco de dados local. Recomendamos que você implemente esse cenário para cargas de trabalho de leitura pesadas e fins de desenvolvimento. Para obter informações sobre como criar réplicas de banco de dados no Azure, consulte Grupos de Disponibilidade AlwaysOn em [alta disponibilidade e recuperação de desastres para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="comparing-web-development-strategies-in-azure"></a>Comparando estratégias de desenvolvimento da Web no Azure
Para implementar e implantar um aplicativo baseado em SQL Server de várias camadas no Azure, você pode usar um dos dois métodos de programação a seguir:

* Configure um servidor Web tradicional (Serviços de Informações da Internet do IIS) no Azure e acesse bancos de dados no SQL Server em máquinas virtuais do Azure.
* Implemente e implante um serviço de nuvem no Azure. Em seguida, verifique se esse serviço de nuvem pode acessar bancos de dados no SQL Server em máquinas virtuais do Azure. Um serviço de nuvem pode incluir várias funções Web e de trabalho.

A tabela a seguir fornece uma comparação do desenvolvimento para a Web tradicional com os serviços de nuvem do Azure e os aplicativos Web do Azure em relação a SQL Server em máquinas virtuais do Azure. A tabela inclui aplicativos Web do Azure, pois é possível usar SQL Server na VM do Azure como uma fonte de dados para aplicativos Web do Azure por meio de seu endereço IP virtual público ou nome DNS.

|  | Desenvolvimento para a Web tradicional em máquinas virtuais do Azure | Serviços de nuvem no Azure | Hospedagem na Web com aplicativos Web do Azure |
| --- | --- | --- | --- |
| **Migração de aplicativo do local** |Aplicativos existentes como estão. |Os aplicativos precisam de funções Web e de trabalho. |Aplicativos existentes como são, mas adequados para aplicativos Web independentes e serviços Web que exigem escalabilidade rápida. |
| **Desenvolvimento e implantação** |Visual Studio, WebMatrix, Visual Web Developer, WebDeploy, FTP, TFS, Gerenciador do IIS, PowerShell. |Visual Studio, SDK do Azure, TFS, PowerShell. Cada serviço de nuvem tem dois ambientes nos quais você pode implantar seu pacote de serviço e configuração: preparo e produção. Você pode implantar um serviço de nuvem no ambiente de preparo para testá-lo antes de promovê-lo para produção. |Visual Studio, WebMatrix, Visual Web Developer, FTP, GIT, BitBucket, CodePlex, DropBox, GitHub, mercurial, TFS, Implantação da Web, PowerShell. |
| **Administração e configuração** |Você é responsável por tarefas administrativas no aplicativo, dados, regras de firewall, rede virtual e sistema operacional. |Você é responsável por tarefas administrativas no aplicativo, dados, regras de firewall e rede virtual. |Você é responsável por tarefas administrativas somente no aplicativo e nos dados. |
| **Alta disponibilidade e recuperação de desastres (HADR)** |Recomendamos que você coloque máquinas virtuais no mesmo conjunto de disponibilidade e no mesmo serviço de nuvem. Manter suas VMs no mesmo conjunto de disponibilidade permite que o Azure Coloque os nós de alta disponibilidade em domínios de falha e domínios de atualização separados. Da mesma forma, manter suas VMs no mesmo serviço de nuvem permite o balanceamento de carga e as VMs podem se comunicar diretamente umas com as outras pela rede local dentro de um data center do Azure.<br/><br/>Você é responsável por implementar uma solução de alta disponibilidade e recuperação de desastres para SQL Server em máquinas virtuais do Azure para evitar qualquer tempo de inatividade. Para tecnologias HADR com suporte, consulte [alta disponibilidade e recuperação de desastre para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).<br/><br/>Você é responsável por fazer backup de seus próprios dados e aplicativos.<br/><br/>O Azure pode mover suas máquinas virtuais se o computador host na data center falhar devido a problemas de hardware. Além disso, pode haver tempo de inatividade planejado da VM quando a máquina host é atualizada para atualizações de segurança ou software. Portanto, recomendamos que você mantenha pelo menos duas VMs em cada camada de aplicativo para garantir a disponibilidade contínua. O Azure não fornece SLA para uma única máquina virtual. |O Azure gerencia as falhas resultantes do hardware subjacente ou do software do sistema operacional. Recomendamos que você implemente várias instâncias de uma função Web ou de trabalho para garantir a alta disponibilidade do seu aplicativo. Para obter informações, consulte [serviços de nuvem, máquinas virtuais e contrato de nível de serviço de rede virtual](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/).<br/><br/>Você é responsável por fazer backup de seus próprios dados e aplicativos.<br/><br/>Para bancos de dados que residem em um banco de SQL Server em uma VM do Azure, você é responsável por implementar uma solução de alta disponibilidade e recuperação de desastres para evitar qualquer tempo de inatividade. Para tecnologias HADR com suporte, consulte alta disponibilidade e recuperação de desastre para SQL Server em máquinas virtuais do Azure.<br/><br/>**Espelhamento de banco de dados SQL Server**: Use com os serviços de nuvem do Azure (funções Web/de trabalho). SQL Server VMs e um projeto de serviço de nuvem podem estar na mesma rede virtual do Azure. Se SQL Server VM não estiver na mesma rede virtual, você precisará criar um alias de SQL Server para rotear a comunicação para a instância do SQL Server. Além disso, o nome do alias deve corresponder ao nome do SQL Server. |A alta disponibilidade é herdada de funções de trabalho do Azure, armazenamento de BLOBs do Azure e banco de dados SQL do Azure. Por exemplo, o armazenamento do Azure mantém três réplicas de todos os dados de BLOB, tabela e fila. A qualquer momento, o banco de dados SQL do Azure mantém três réplicas de um dado em execução — uma réplica primária e duas réplicas secundárias. Para obter mais informações, consulte [armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/) e [banco de dados SQL do Azure](../../../sql-database/sql-database-technical-overview.md).<br/><br/>Ao usar SQL Server na VM do Azure como uma fonte de dados para aplicativos Web do Azure, tenha em mente que os aplicativos Web do Azure não dão suporte à rede virtual do Azure. Em outras palavras, todas as conexões de aplicativos Web do Azure para SQL Server VMs no Azure devem passar por pontos de extremidade públicos de máquinas virtuais. Isso pode causar algumas limitações para cenários de alta disponibilidade e recuperação de desastres. Por exemplo, o aplicativo cliente em aplicativos Web do Azure que se conectam a SQL Server VM com espelhamento de banco de dados não conseguiria se conectar ao novo servidor primário, pois o espelhamento de banco de dados requer que você configure a rede virtual do Azure entre SQL Server VMs de host no Azure. Portanto, o uso do espelhamento de **banco de dados SQL Server** com aplicativos Web do Azure não tem suporte no momento.<br/><br/>**SQL Server grupos de disponibilidade AlwaysOn**: Você pode configurar Grupos de Disponibilidade AlwaysOn ao usar aplicativos Web do Azure com SQL Server VMs no Azure. Mas você precisa configurar o ouvinte do grupo de disponibilidade AlwaysOn para rotear a comunicação para a réplica primária por meio de pontos de extremidade públicos com balanceamento de carga. |
| **Conectividade entre locais** |Você pode usar a rede virtual do Azure para se conectar ao local. |Você pode usar a rede virtual do Azure para se conectar ao local. |Há suporte para a rede virtual do Azure. Para obter mais informações, consulte [integração de rede virtual de aplicativos Web](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/). |
| **Escalabilidade** |A expansão está disponível aumentando os tamanhos das máquinas virtuais ou adicionando mais discos. Para obter mais informações sobre tamanhos de máquina virtual, consulte [tamanhos de máquina virtual para o Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).<br/><br/>**Para o servidor de banco de dados**: A expansão está disponível por meio de técnicas de particionamento de banco de dados e SQL Server grupos de disponibilidade AlwaysOn.<br/><br/>Para cargas de trabalho de leitura pesadas, você pode usar [grupos de disponibilidade AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx) em vários nós secundários, bem como replicação do SQL Server.<br/><br/>Para cargas de trabalho de gravação pesada, você pode implementar dados de particionamento horizontal em vários servidores físicos para fornecer expansão do aplicativo.<br/><br/>Além disso, você pode implementar uma expansão usando [SQL Server com roteamento dependente de dados](https://technet.microsoft.com/library/cc966448.aspx). Com o roteamento dependente de dados (DDR), você precisa implementar o mecanismo de particionamento no aplicativo cliente, normalmente na camada de camada de negócios, para rotear as solicitações de banco de dados para vários nós de SQL Server. A camada de negócios contém mapeamentos de como os dados são particionados e qual nó contém os dados.<br/><br/>Você pode dimensionar aplicativos que estão executando máquinas virtuais. Para obter mais informações, consulte [como dimensionar um aplicativo](../../../cloud-services/cloud-services-how-to-scale-portal.md).<br/><br/>**Observação importante**: O recurso de **dimensionamento automático** no Azure permite que você aumente ou diminua automaticamente as máquinas virtuais que são usadas pelo seu aplicativo. Esse recurso garante que a experiência do usuário final não seja afetada negativamente durante períodos de pico e as VMs sejam desligadas quando a demanda for baixa. É recomendável que você não defina a opção de dimensionamento automático para seu serviço de nuvem se ele incluir SQL Server VMs. O motivo é que o recurso de dimensionamento automático permite que o Azure ative uma máquina virtual quando o uso da CPU na VM é maior do que um limite e desative uma máquina virtual quando o uso da CPU for menor do que o valor. O recurso de dimensionamento automático é útil para aplicativos sem estado, como servidores Web, em que qualquer VM pode gerenciar a carga de trabalho sem nenhuma referência a nenhum estado anterior. No entanto, o recurso de dimensionamento automático não é útil para aplicativos com estado, como SQL Server, em que apenas uma instância permite gravar no banco de dados. |A expansão está disponível usando várias funções da Web e de trabalho. Para obter mais informações sobre tamanhos de máquina virtual para funções Web e funções de trabalho, consulte [configurar tamanhos para serviços de nuvem](../../../cloud-services/cloud-services-sizes-specs.md).<br/><br/>Ao usar os **serviços de nuvem**, você pode definir várias funções para distribuir o processamento e também obter um dimensionamento flexível do seu aplicativo. Cada serviço de nuvem inclui uma ou mais funções Web e/ou funções de trabalho, cada uma com seus próprios arquivos de aplicativo e configuração. Você pode escalar verticalmente um serviço de nuvem aumentando o número de instâncias de função (máquinas virtuais) implantadas para uma função e reduzir um serviço de nuvem diminuindo o número de instâncias de função. Para obter informações detalhadas, consulte [modelos de execução do Azure](../../../cloud-services/cloud-services-choose-me.md).<br/><br/>A expansão está disponível por meio do suporte interno de alta disponibilidade do Azure por meio de [serviços de nuvem, máquinas virtuais e rede virtual contrato de nível de serviço](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) e Load Balancer.<br/><br/>Para um aplicativo de várias camadas, recomendamos que você conecte o aplicativo de funções Web/de trabalho a VMs do servidor de banco de dados por meio da rede virtual do Azure. Além disso, o Azure fornece balanceamento de carga para VMs no mesmo serviço de nuvem, distribuindo solicitações de usuário entre elas. As máquinas virtuais conectadas dessa maneira podem se comunicar diretamente umas com as outras pela rede local em uma data center do Azure.<br/><br/>Você pode configurar o **dimensionamento automático** no portal do Azure, bem como os horários de agendamento. Para obter mais informações, consulte [como configurar o dimensionamento automático para um serviço de nuvem no portal](../../../cloud-services/cloud-services-how-to-scale-portal.md). |**Escalar e reduzir verticalmente**: Você pode aumentar/diminuir o tamanho da instância (VM) reservada para seu site da Web.<br/><br/>Escalar horizontalmente: Você pode adicionar mais instâncias reservadas (VMs) ao seu site.<br/><br/>Você pode configurar o **dimensionamento automático** no portal, bem como os horários de agendamento. Para obter mais informações, consulte [como dimensionar aplicativos Web](../../../app-service/manage-scale-up.md). |

Para obter mais informações sobre como escolher entre esses métodos de [programação, consulte aplicativos Web do Azure, serviços de nuvem e VMs: Quando usar o que](/azure/architecture/guide/technology-choices/compute-decision-tree).

## <a name="next-steps"></a>Próximos Passos
Para obter mais informações sobre como executar SQL Server em máquinas virtuais do Azure, consulte a [visão geral de SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

