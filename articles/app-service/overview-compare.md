---
title: Comparar o serviço de aplicações, VM, o Service Fabric e serviços Cloud - Azure | Documentos da Microsoft
description: Saiba como escolher entre o Serviço de Aplicações do Azure, as Máquinas Virtuais, o Service Fabric e os Serviços Cloud para alojar aplicações Web.
services: app-service\web, virtual-machines, cloud-services
documentationcenter: ''
author: cephalin
manager: jeconnoc
ms.assetid: 7d346a23-532a-42a9-98a8-23b7286d32a8
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/07/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: aac2a0b102d50c8d3f0506c2cc1469a838706703
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793860"
---
# <a name="azure-app-service-virtual-machines-service-fabric-and-cloud-services-comparison"></a>Comparação do Serviço de Aplicações do Azure, das Máquinas Virtuais, do Service Fabric e dos Serviços Cloud

O Azure oferece várias formas para alojar sites: [Serviço de aplicações do Azure][Azure App Service], [máquinas virtuais][Virtual Machines], [do Service Fabric][Service Fabric], e [Serviços cloud][Cloud Services]. Este artigo ajuda-o a compreender as opções e a fazer a escolha certa para a sua aplicação Web.

O Serviço de Aplicações do Azure é a melhor escolha para a maioria das aplicações Web. A implementação e a gestão estão integradas na plataforma, os sites podem ser dimensionados rapidamente para lidar com cargas elevadas de tráfego e o balanceamento de carga e o gestor de tráfego incorporados proporcionam elevada disponibilidade. Pode mover facilmente sites existentes para o Serviço de Aplicações do Azure com uma [ferramenta de migração online][migrate-tool], utilize uma aplicação de código aberto da Galeria de Aplicações Web ou crie um novo site com as ferramentas e a arquitetura da sua preferência. A funcionalidade [WebJobs][WebJobs] possibilita adicionar de forma fácil processamento de trabalhos em segundo plano à sua aplicação Web do Serviço de Aplicações.

O Service Fabric é uma boa opção se pretender criar uma aplicação nova ou reescrever uma já existente para utilizar uma arquitetura de microsserviço. As aplicações, que são executadas num conjunto partilhado de máquinas, podem começar por ser pequenas e dimensionar-se para escalas massivas com centenas ou milhares de máquinas, consoante as suas necessidades. Com os serviços com estado, é fácil armazenar de forma consistente e fiável o estado das aplicações e o Service Fabric gere automaticamente a criação de partições, o dimensionamento e a disponibilidade do serviço.  O Service Fabric também suporta WebAPI com Open Web Interface para .NET (OWIN) e ASP.NET Core.  Comparando com o Serviço de Aplicações, o Service Fabric também proporciona acesso direto ou mais controlo sobre a infraestrutura subjacente. Pode aceder remotamente aos seus servidores ou configurar tarefas de arranque de servidores. Os Serviços Cloud são semelhantes ao Service Fabric quanto ao nível de controlo versus facilidade de utilização, mas são agora um serviço legado, sendo que se recomenda o Service Fabric para implementações novas.

Se tiver uma aplicação existente que precise de alterações substanciais para ser executada no Serviço de Aplicações ou no Service Fabric, pode escolher as Máquinas Virtuais para simplificar a migração para a cloud. No entanto, configurar, proteger e manter VMs corretamente exige muito mais tempo e conhecimentos de TI em comparação com o Serviço de Aplicações do Azure e o Service Fabric. Se estiver a considerar as Máquinas Virtuais do Azure, é muito importante ter em conta os esforços de manutenção contínuos necessários para aplicar correções, atualizar e gerir o seu ambiente de VMs. As Máquinas Virtuais do Azure são uma Infraestrutura como Serviço (IaaS), ao passo que o Serviço de Aplicações e o Service Fabric são Plataforma como Serviço (PaaS).

## <a name="features"></a>Comparação de Funcionalidades
A tabela seguinte compara as capacidades do Serviço de Aplicações, dos Serviços Cloud, das Máquinas Virtuais e do Service Fabric para o ajudar a tomar a melhor decisão. Para obter informações atuais sobre o SLA de cada opção, veja [Contratos de Nível de Serviço do Azure](https://azure.microsoft.com/support/legal/sla/).

| Funcionalidade | Serviço de Aplicações (Aplicações Web) | Serviços Cloud (funções da Web) | Virtual Machines | Service Fabric | Notas |
| --- | --- | --- | --- | --- | --- |
| Implementação quase imediata |X | | |X |A implementação de aplicações ou de atualizações a implementações num Serviço Cloud ou a criação de VMs demora, no mínimo, vários minutos; a implementação de aplicações numa aplicação Web demora segundos. |
| Aumentar verticalmente para máquinas maiores sem reimplementar |X | | |X | |
| As instâncias dos servidores Web partilham conteúdos e a configuração, o que significa que não tem de reimplementar nem reconfigurar quando dimensionar. |X | | |X | |
| Vários ambientes de implementação (produção e teste) |X |X | |X |O Service Fabric permite-lhe ter vários ambientes para as suas aplicações ou implementar diferentes versões das mesmas lado a lado. |
| Gestão de atualizações do SO automática |X |X | | |Parcialmente através de Patch Orchestration Application (POA) e integralmente no futuro. |
| Alternância de plataformas simples (alternar entre 32 bits e 64 bits facilmente) |X |X | | | |
| Implementar código com GIT, FTP |X | |X | | |
| Implementar código com Web Deploy |X | |X | |Os Serviços Cloud suportam a utilização da Implementação Web para implementar atualizações em instâncias de função individuais. No entanto, não pode utilizá-lo para a implementação inicial de uma função e, se utilizar o Web Deploy para uma atualização, tem de implementar separadamente em cada instância de uma função. Para elegibilidade para o SLA dos Serviços Cloud para ambientes de produção, são necessárias múltiplas instâncias. |
| Suporte de WebMatrix |X | |X | | |
| Acesso a serviços, como Service Bus, Armazenamento e Base de Dados SQL |X |X |X |X | |
| Alojar a camada Web ou a camada dos serviços Web de uma arquitetura com várias camadas |X |X |X |X | |
| Alojar a camada média de uma arquitetura com várias camadas |X |X |X |X |As aplicações Web do Serviço de Aplicações podem alojar facilmente uma camada intermédia da API REST e a funcionalidade [WebJobs](https://go.microsoft.com/fwlink/?linkid=390226) pode alojar trabalhos de processamento em segundo plano. Pode executar o WebJobs num site dedicado para obter escalabilidade independente para a camada. |
| Suporte integrado para MySQL como serviço |X |X | | | |
| Suporte para ASP.NET, ASP clássico, Node.js, PHP, Python |X |X |X |X |O Service Fabric suporta a criação de front-ends da Web através de [ASP.NET 5](../service-fabric/service-fabric-reliable-services-communication-aspnetcore.md) ou a implementação de qualquer tipo de aplicação (Node.js, Java, etc.) como [executável convidado](../service-fabric/service-fabric-guest-executables-introduction.md). |
| Aumentar horizontalmente para várias instâncias sem reimplementar |X |X |X |X |As Máquinas Virtuais podem ser aumentadas horizontalmente para várias instâncias, mas os serviços que estão em execução nas mesmas têm de ser escritos de forma a suportar esse aumento horizontal. Tem de configurar um balanceador de carga para encaminhar os pedidos entre as máquinas e certifique-se de que tem mais de uma instância de VM no [conjunto de disponibilidade](../virtual-machines/windows/manage-availability.md). |
| Suporte para SSL |X |X |X |X |Nas aplicações Web do Serviço de Aplicações, o SSL para nomes de domínios personalizados só é suportado nos modos Básico e Standard. Para obter informações sobre como utilizar o SSL com as aplicações Web, veja [Configuring an SSL certificate for an Azure Website](app-service-web-tutorial-custom-ssl.md) (Configurar certificados SSL para sites do Azure). |
| Integração com o Visual Studio |X |X |X |X | |
| Depuração Remota |X |X |X |X | |
| Implementar código com TFS |X |X |X |X | |
| Isolamento de rede com a [Rede Virtual do Azure](/azure/virtual-network/) |X |X |X |X |Veja também [Azure Websites Virtual Network Integration](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/) (Integração da Rede Virtual dos Web Sites do Azure) |
| Suporte para o [Gestor de Tráfego do Azure](/azure/traffic-manager/) |X |X |X |X | |
| Monitorização de pontos finais integrada |X |X |X | | |
| Acesso de ambiente de trabalho remoto aos servidores | |X |X |X | |
| Instalar qualquer MSI personalizado | |X |X |X |O Service Fabric permite-lhe alojar qualquer ficheiro executável como [executável convidado](../service-fabric/service-fabric-guest-executables-introduction.md) ou pode instalar qualquer aplicação nas VMs. |
| Capacidade de definir/executar tarefas de arranque | |X |X |X | |
| Pode escutar eventos do ETW | |X |X |X | |

## <a name="scenarios"></a>Cenários e recomendações
Eis alguns cenários de aplicações comuns com recomendações sobre qual a opção de alojamento na Web do Azure mais adequada para cada um.

* [Preciso de um front-end da Web com processamento em segundo plano e de um back-end de base de dados para executar aplicações empresariais integradas em recursos no local.](#onprem)
* [Preciso de uma forma fiável para alojar o meu site empresarial e que se dimensione bem e ofereça um alcance global.](#corp)
* [Tenho uma aplicação de IIS6 em execução no Windows Server 2003.](#iis6)
* [Sou proprietário de uma pequena empresa e preciso de uma forma económica para alojar o meu site, mas com crescimento futuro em mente.](#smallbusiness)
* [Sou Web designer ou designer gráfico e quero desenhar e criar sites para os meus clientes.](#designer)
* [Estou a migrar a minha aplicação multicamadas com um front-end da Web para a Cloud.](#multitier)
* [A minha aplicação depende de ambientes do Windows ou do Linux altamente personalizados e quero movê-la para a cloud.](#custom)
* [O meu site utiliza software de código aberto e quero alojá-lo no Azure.](#oss)
* [Tenho uma aplicação de linha de negócio que tem de se ligar à rede empresarial.](#lob)
* [Quero alojar uma API REST ou um serviço Web para clientes móveis.](#mobile)

### <a id="onprem"></a>Preciso de um front-end da Web com processamento em segundo plano e de um back-end de base de dados para executar aplicações empresariais integradas em recursos no local.
O Serviço de Aplicações do Azure é uma ótima solução para aplicações empresariais complexas. Permite-lhe desenvolver aplicações que se dimensionam automaticamente numa plataforma com balanceamento de carga, que são protegidas com o Active Directory e que se ligam aos seus recursos no local. Faz com que gerir estas aplicações seja fácil através de um portal e de APIs de qualidade superior e permite-lhe obter informações sobre de que forma é que os clientes as estão a utilizar mediante a utilização de ferramentas de informações de aplicações. O [Webjobs] [ Webjobs] funcionalidade permite-lhe executar processos em segundo plano e fazer uma cópia de tarefas como parte da sua camada da web, embora a conectividade híbrida e funcionalidades de VNet simplificam a ligar a recursos no local. O Serviço de Aplicações do Azure oferece SLAs com garantia de 99,9% de tempo de atividade para as aplicações Web e permite-lhe:

* Executar as aplicações de forma fiável numa plataforma cloud de recuperação e aplicação de correções automáticas.
* Dimensionar automaticamente numa rede global de datacenters.
* Criar cópias de segurança e restaurar, para recuperação após desastre.
* Estar em conformidade com ISO, SOC2 e PCI
* Integrar no Active Directory

### <a id="corp"></a>Preciso de uma forma fiável para alojar o meu site empresarial e que se dimensione bem e ofereça um alcance global.
O Serviço de Aplicações do Azure é uma excelente solução para o alojamento de sites empresariais. Permite que as aplicações Web se dimensionem de forma rápida e fácil para satisfazer a procura numa rede global de datacenters. Oferece alcance local, tolerância a falhas e gestão de tráfego inteligente. E tudo isto numa plataforma que disponibiliza ferramentas de gestão de qualidade superior, através das quais pode obter informações sobre o estado de funcionamento e o tráfego do site rápida e facilmente. O Serviço de Aplicações do Azure oferece SLAs com garantia de 99,9% de tempo de atividade para as aplicações Web e permite-lhe:

* Executar os seus sites de forma fiável numa plataforma cloud de recuperação e aplicação de correções automáticas.
* Dimensionar automaticamente numa rede global de datacenters.
* Criar cópias de segurança e restaurar, para recuperação após desastre.
* Gerir registos e tráfego com ferramentas integradas.
* Estar em conformidade com ISO, SOC2 e PCI
* Integrar no Active Directory

### <a id="iis6"></a>Tenho uma aplicação IIS6 em execução no Windows Server 2003.
Com o Serviço de Aplicações do Azure, é fácil evitar os custos de infraestrutura associados à migração de aplicações IIS6. A Microsoft criou [ferramentas de migração fáceis de utilizar e orientações de migração detalhadas][migrate-tool] que lhe permitem verificar a compatibilidade e identificar eventuais alterações que tenham de ser feitas. A integração no Visual Studio, o TFS e as ferramentas de CMS comuns fazem com que seja mais fácil implementar aplicações IIS6 diretamente na cloud. Depois de implementadas, o portal do Azure proporciona ferramentas de gestão robustas que lhe permitem reduzir verticalmente, para gerir os custos, e aumentar verticalmente, para satisfazer a procura, conforme necessário. Com a ferramenta de migração, pode:

* Migrar, de forma rápida e fácil, a sua aplicação Web legada do Windows Server 2003 para a cloud.
* Optar por deixar a base de dados SQL ligada no local, para criar uma aplicação híbrida.
* Mover automaticamente a base de dados SQL juntamente com a aplicação legada.

### <a id="smallbusiness"></a>Sou proprietário de uma pequena empresa e preciso de uma forma económica para alojar o meu site, mas com crescimento futuro em mente.
O Serviço de Aplicações do Azure é uma excelente solução para este cenário, porque pode começar a utilizá-la gratuitamente e, em seguida, adicionar mais capacidades quando precisar. Cada aplicação Web gratuita inclui um domínio fornecido pelo Azure (*a_sua_empresa*. azurewebsites.net) e a plataforma inclui ferramentas de implementação e gestão integradas, bem como uma galeria de aplicações que faz com que seja mais fácil começar a utilizá-las. Existem muitos outros serviços e opções de dimensionamento que possibilitam a evolução dos sites à medida que a procura dos utilizadores aumenta. Com o Serviço de Aplicações do Azure, pode:

* Começar com o escalão gratuito e, depois, aumentar verticalmente, conforme necessário.
* Utilizar a Galeria de Aplicações para configurar rapidamente aplicações Web populares, como o WordPress.
* Adicionar mais funcionalidades e serviços do Azure à aplicação, conforme necessário.
* Proteger a sua aplicação Web com HTTPS.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a id="designer"></a>Sou Web designer ou designer gráfico e quero desenhar e criar sites para os meus clientes.
Para os programadores e Web designers, o Serviço de Aplicações do Azure integra-se facilmente com diversas arquiteturas e ferramentas, inclui suporte de implementação para Git e FTP e oferece uma estreita integração com ferramentas e serviços, como o Visual Studio e a Base de Dados SQL. Com o Serviço de aplicações, pode:

* Utilizar as ferramentas da linha de comandos para [tarefas automatizadas][scripting].
* Trabalhar com linguagens populares, tais como [.Net][dotnet], [PHP][PHP], [Node.js][nodejs] e [Python][Python].
* Selecionar três níveis de dimensionamento diferentes para aumentar verticalmente para capacidades muito elevadas.
* Integrar noutros serviços do Azure, como a [Base de Dados SQL][sqldatabase], o [Service Bus][servicebus] e o [Armazenamento][Storage], ou em ofertas de parceiros da [Loja Azure][azurestore], como MySQL e MongoDB.
* Integrar em ferramentas como Visual Studio, Git, WebMatrix, WebDeploy, TFS e FTP.

### <a id="multitier"></a>Estou a migrar a minha aplicação multicamadas com um front-end da Web para a Cloud.
Se estiver a executar uma aplicação multicamadas, como um servidor Web que se liga a uma base de dados, o Serviço de Aplicações do Azure é uma boa opção que oferece uma integração sólida com a Base de Dados SQL do Azure. E pode utilizar a funcionalidade WebJobs para executar processos de back-end.

Escolha o Service Fabric para uma ou mais das suas camadas se precisar de ter mais controlo sobre o ambiente de servidores, como a capacidade de aceder remotamente aos servidores ou de configurar tarefas de arranque dos mesmos.

Escolha as Máquinas Virtuais para uma ou mais das suas camadas se quiser utilizar a sua própria imagem de máquina ou executar software de servidor ou serviços que não possam ser configurados no Service Fabric.

### <a id="custom"></a>A minha aplicação depende de ambientes do Windows ou do Linux altamente personalizados e quero movê-la para a cloud.
Se a sua aplicação exigir instalações ou configurações complexas de software e do sistema operativo, as Máquinas Virtuais são, provavelmente, a melhor solução. Com as Máquinas Virtuais, pode:

* Utilizar a galeria da Máquina Virtual para começar com um sistema operativo, como Windows ou Linux, e depois personalizá-lo de acordo com os requisitos da aplicação.
* Criar e carregar uma imagem personalizada de um servidor no local existente para execução numa máquina virtual no Azure.

### <a id="oss"></a>O meu site utiliza software de código aberto e quero alojá-lo no Azure.
Se a sua arquitetura de código aberto for suportada no Serviço de Aplicações, as linguagens e as arquiteturas de que a aplicação precisa são configuradas automaticamente. O Serviço de Aplicações permite-lhe:

* Utilizar muitas linguagens de código aberto populares, tais como [.NET][dotnet], [PHP][PHP], [Node.js][nodejs] e [Python][Python].
* Configurar WordPress, Drupal, Umbraco, DNN e muitas outras aplicações Web de terceiros.
* Migrar uma aplicação existente ou criar uma nova a partir da Galeria de Aplicações.

Se o Serviço de Aplicações não suportar a sua arquitetura de código aberto, pode recorrer a uma das outras opções de alojamento Web do Azure. Com as Máquinas Virtuais, o software é instalado e configurado na imagem da máquina, que pode ser baseada no Windows ou no Linux.

### <a id="lob"></a>Tenho uma aplicação de linha de negócio que tem de se ligar à rede empresarial.
Se pretender criar uma aplicação de linha de negócio, o seu site poderá precisar de acesso direto aos serviços ou dados na rede empresarial. Isto é possível no Serviço de Aplicações, no Service Fabric e nas Máquinas Virtuais mediante a utilização do [serviço de Rede Virtual do Azure](/azure/virtual-network/). No serviço de aplicações, pode utilizar o [funcionalidade de integração de VNet](/azure/app-service/web-sites-integrate-with-vnet), que permite que as aplicações do Azure ser executado como se estivessem na sua rede empresarial.

### <a id="mobile"></a>Quero alojar uma API REST ou um serviço Web para clientes móveis.
Com os serviços Web baseados em HTTP, pode suportar uma grande variedade de clientes, incluindo clientes móveis. Algumas arquiteturas, como a API Web ASP.NET, integram-se no Visual Studio para permitir criar e consumir serviços REST mais facilmente.  Estes serviços são expostos a partir de um ponto final da Web, pelo que é possível utilizar qualquer técnica de alojamento Web no Azure, de modo a suportar este cenário. No entanto, o Serviço de Aplicações é uma escolha ideal para alojar APIs REST. Com o Serviço de aplicações, pode:

* Criar rapidamente uma [aplicação móvel](../app-service-mobile/app-service-mobile-value-prop.md) ou uma aplicação API para alojar o serviço Web HTTP num dos datacenters globalmente distribuídos do Azure.
* Migrar os serviços existentes ou criar novos.
* Obter o SLA para disponibilidade com uma única instância ou aumentar horizontalmente para várias máquinas dedicadas.
* Utilizar o site publicado para fornecer APIs REST para clientes HTTP, incluindo clientes móveis.

> [!NOTE]
> Se quiser começar a utilizar o Serviço de Aplicações do Azure antes de se inscrever numa conta, aceda a<a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, onde pode criar imediatamente uma aplicação de início de curta duração grátis no Serviço de Aplicações do Azure. Sem necessidade de cartões de crédito, sem compromissos.

## <a id="nextsteps"></a>Passos Seguintes
Para obter mais informações sobre as três opções de alojamento Web, veja [Introducing Azure](../fundamentals-introduction-to-azure.md) (Introdução ao Azure).

Para começar a utilizar as opções que escolheu para a sua aplicação, veja os recursos seguintes:

* [Serviço de Aplicações do Azure](/azure/app-service/)
* [Azure Cloud Services](/azure/cloud-services/)
* [Máquinas Virtuais do Azure](/azure/virtual-machines/)
* [Service Fabric](/azure/service-fabric/)

<!-- URL List -->

[Azure App Service]: /azure/app-service/
[Cloud Services]: /azure/cloud-services/
[Virtual Machines]: /azure/virtual-machines/
[Service Fabric]: /azure/service-fabric/
[WebJobs]: https://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
[Configuring an SSL certificate for an Azure Website]: app-service-web-tutorial-custom-ssl.md
[azurestore]: https://azuremarketplace.microsoft.com/en-us/marketplace/apps
[scripting]: https://azure.microsoft.com/documentation/scripts/?services=web-sites
[dotnet]: https://azure.microsoft.com/develop/net/
[nodejs]: https://azure.microsoft.com/develop/nodejs/
[PHP]: https://azure.microsoft.com/develop/php/
[Python]: https://azure.microsoft.com/develop/python/
[servicebus]: /azure/service-bus/
[sqldatabase]: /azure/sql-database/
[Storage]: /azure/storage/

<!-- IMG List -->

[ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
[migrate-tool]: https://www.movemetothecloud.net/
