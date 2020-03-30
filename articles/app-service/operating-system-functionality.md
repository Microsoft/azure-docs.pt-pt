---
title: Funcionalidade do sistema operativo
description: Conheça a funcionalidade OS no Serviço de Aplicações Azure no Windows. Descubra que tipos de ficheiros, rede e registo acedem à sua aplicação.
ms.assetid: 39d5514f-0139-453a-b52e-4a1c06d8d914
ms.topic: article
ms.date: 10/30/2018
ms.custom: seodec18
ms.openlocfilehash: ed84cb2b0cb8d98b12fe787e49c400ba47e4e38a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671613"
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Funcionalidade do sistema operativo no Serviço de Aplicações Azure
Este artigo descreve a funcionalidade comum do sistema operativo de base que está disponível para todas as aplicações do Windows que funcionam no Serviço de [Aplicações Azure](https://go.microsoft.com/fwlink/?LinkId=529714). Esta funcionalidade inclui acesso a ficheiros, rede e registo, e registos e eventos de diagnóstico. 

> [!NOTE] 
> As [aplicações Linux](containers/app-service-linux-intro.md) no App Service funcionam nos seus próprios contentores. Não é permitido o acesso ao sistema operativo do hospedeiro, tem acesso à raiz do recipiente. Da mesma forma, para [aplicações em execução em contentores Windows,](app-service-web-get-started-windows-container.md)tem acesso administrativo ao contentor, mas não tem acesso ao sistema operativo anfitrião. 
>

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>Níveis de plano de aplicativos
O App Service executa aplicações de clientes num ambiente de hospedagem multi-inquilino. As aplicações implementadas nos níveis **Free** e **Shared** funcionam em processos de trabalhador estoireiro em máquinas virtuais partilhadas, enquanto as aplicações implementadas nos níveis **Standard** e **Premium** funcionam em máquinas virtuais dedicadas especificamente às aplicações associadas a um único cliente.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Uma vez que o App Service suporta uma experiência de escala perfeita entre diferentes níveis, a configuração de segurança aplicada para aplicações do App Service continua a ser a mesma. Isto garante que as aplicações não se comportam de forma diferente, falhando de formainesperada, quando o plano do App Service muda de um nível para outro.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>Quadros de desenvolvimento
Os níveis de preços do Serviço de Aplicações controlam a quantidade de recursos computacionais (CPU, armazenamento de discos, memória e saída de rede) disponíveis para apps. No entanto, a amplitude da funcionalidade-quadro disponível para as aplicações continua a ser a mesma, independentemente dos níveis de escala.

O App Service suporta uma variedade de estruturas de desenvolvimento, incluindo ASP.NET, asas clássicas, node.js, PHP e Python - todos os quais funcionam como extensões dentro do IIS. De forma a simplificar e normalizar a configuração de segurança, as aplicações do App Service normalmente executam os vários quadros de desenvolvimento com as suas definições padrão. Uma abordagem para configurar aplicações poderia ter sido personalizar a área de superfície da API e a funcionalidade para cada quadro de desenvolvimento individual. Em vez disso, o App Service tem uma abordagem mais genérica, permitindo uma linha de base comum da funcionalidade do sistema operativo, independentemente do quadro de desenvolvimento de uma aplicação.

As seguintes secções resumem os tipos gerais de funcionalidade do sistema operativo disponíveis para aplicações do App Service.

<a id="FileAccess"></a>

## <a name="file-access"></a>Acesso a ficheiros
Existem várias unidades dentro do Serviço de Aplicações, incluindo unidades locais e unidades de rede.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>Unidades locais
No seu cerne, o App Service é um serviço que funciona em cima da infraestrutura Azure PaaS (plataforma como serviço). Como resultado, as unidades locais que estão "ligadas" a uma máquina virtual são os mesmos tipos de unidade disponíveis para qualquer função de trabalhador que esteja em funcionamento em Azure. Isto inclui:

- Uma unidade do sistema operativo (o D:\ unidade)
- Uma unidade de aplicação que contém ficheiros Azure Package cspkg utilizados exclusivamente pelo App Service (e inacessíveis aos clientes)
- Uma unidade "utilizador" (o C:\ unidade), cujo tamanho varia dependendo do tamanho do VM. 

É importante monitorizar a utilização do disco à medida que a sua aplicação cresce. Se a quota do disco for atingida, pode ter efeitos adversos na sua aplicação. Por exemplo: 

- A aplicação pode lançar um erro indicando que não há espaço suficiente no disco.
- Pode ver erros no disco ao navegar na consola Kudu.
- A implantação de Azure DevOps `ERROR_NOT_ENOUGH_DISK_SPACE: Web deployment task failed. (Web Deploy detected insufficient space on disk)`ou Visual Studio pode falhar com .
- A sua aplicação pode sofrer um desempenho lento.

<a id="NetworkDrives"></a>

### <a name="network-drives-aka-unc-shares"></a>Unidades de rede (também conhecidas como ações da CNU)
Um dos aspetos únicos do App Service que torna a implementação e manutenção de aplicações simples é que todos os conteúdos dos utilizadores são armazenados num conjunto de ações da UNC. Este modelo mapeia bem o padrão comum de armazenamento de conteúdo usado por ambientes de hospedagem web no local que têm múltiplos servidores equilibrados em carga. 

Dentro do Serviço de Aplicações, há uma série de ações da UNC criadas em cada centro de dados. Uma percentagem do conteúdo do utilizador para todos os clientes em cada centro de dados é atribuída a cada ação da CNU. Além disso, todos os conteúdos de ficheiros para a subscrição de um único cliente são sempre colocados na mesma parte da CNU. 

Devido ao funcionar dos serviços azure, a máquina virtual específica responsável pelo alojamento de uma parte da CNU mudará ao longo do tempo. É garantido que as ações da CNU serão montadas por diferentes máquinas virtuais, uma vez que são trazidas para cima e para baixo durante o curso normal das operações do Azure. Por esta razão, as aplicações nunca devem fazer pressupostos codificados de que a informação da máquina num caminho de ficheiro sem fim permanecerá estável ao longo do tempo. Em vez disso, devem usar o conveniente caminho *absoluto* **d:\home\site** que o Serviço de Aplicações fornece. Este caminho absoluto falso fornece um método portátil, app-e-user-agnóstico para se referir à própria app. Ao utilizar o **D:\home\site,** pode-se transferir ficheiros partilhados de app para app sem ter de configurar um novo caminho absoluto para cada transferência.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>Tipos de acesso a ficheiros concedidos a uma app
A subscrição de cada cliente tem uma estrutura de diretório reservada numa participação específica da UNC dentro de um centro de dados. Um cliente pode ter várias aplicações criadas dentro de um centro de dados específico, pelo que todos os diretórios pertencentes a uma única subscrição de cliente são criados na mesma quota da CNU. A parte pode incluir diretórios como os de registos de conteúdo, erro e diagnóstico, e versões anteriores da app criada pelo controlo de fontes. Como seria de esperar, os diretórios de aplicações de um cliente estão disponíveis para leitura e acesso de escrita em tempo de execução pelo código de aplicação da aplicação da aplicação.

Nas unidades locais anexadas à máquina virtual que executa uma app, o App Service reserva um pedaço de espaço no C:\ unidade para armazenamento local temporário específico de aplicativo. Embora uma aplicação tenha acesso completo ao seu próprio armazenamento local temporário, esse armazenamento não se destina a ser utilizado diretamente pelo código de aplicação. Pelo contrário, a intenção é fornecer armazenamento temporário de ficheiros para quadros de iis e aplicações web. O Serviço de Aplicações também limita a quantidade de armazenamento local temporário disponível para cada aplicação para evitar que as aplicações individuais consumam quantidades excessivas de armazenamento de ficheiros locais.

Dois exemplos de como o Serviço de Aplicações utiliza armazenamento local temporário são o diretório para ficheiros de ASP.NET temporários e o diretório para ficheiros comprimidos IIS. O sistema de compilação ASP.NET utiliza o diretório "Ficheiros de ASP.NET Temporários" como localização temporária de cache de compilação. O IIS utiliza o diretório "IIS Temporary ComprimE Files" para armazenar a saída de resposta comprimido. Ambos os tipos de utilização de ficheiros (assim como outros) são remapeados no Serviço de Aplicações para armazenamento local temporário por app. Este remapping garante que a funcionalidade continua como esperado.

Cada aplicação no App Service funciona como uma identidade de processo de trabalho de [https://www.iis.net/learn/manage/configuring-security/application-pool-identities](https://www.iis.net/learn/manage/configuring-security/application-pool-identities)baixo privilégio aleatório chamada "identidade de piscina de aplicações", descrita aqui: . O código de aplicação utiliza esta identidade para o acesso básico apenas à unidade do sistema operativo (o D:\ unidade). Isto significa que o código de aplicação pode listar estruturas comuns de diretório e ler ficheiros comuns sobre a unidade do sistema operativo. Embora este possa parecer um nível de acesso um pouco amplo, os mesmos diretórios e ficheiros são acessíveis quando você presta um papel de trabalhador em um serviço hospedado Azure e lê o conteúdo da unidade. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>Acesso de ficheiros em várias instâncias
O diretório doméstico contém o conteúdo de uma aplicação, e o código de aplicação pode escrever-lhe. Se uma aplicação for executado em várias instâncias, o diretório doméstico é partilhado entre todos os casos para que todos os casos vejam o mesmo diretório. Assim, por exemplo, se uma aplicação guardar ficheiros carregados para o diretório inicial, esses ficheiros estão imediatamente disponíveis para todas as instâncias. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>Acesso à rede
O código de aplicação pode utilizar protocolos baseados em TCP/IP e UDP para efelo ligações de rede de saída a pontos finais acessíveis à Internet que expõem serviços externos. As aplicações podem utilizar estes mesmos protocolos para se conectarem a serviços dentro do Azure, por exemplo, estabelecendo ligações HTTPS à Base de Dados SQL.

Existe também uma capacidade limitada para as apps estabelecerem uma ligação de loopback local, e ter uma app a ouvir naquela tomada de loopback local. Esta funcionalidade existe principalmente para permitir aplicações que ouvem as tomadas de loopback locais como parte da sua funcionalidade. Cada aplicação vê uma ligação "privada". A aplicação "A" não pode ouvir uma tomada de loopback local estabelecida pela app "B".

Os tubos nomeados também são suportados como um mecanismo de comunicação inter-processo (IPC) entre diferentes processos que executam coletivamente uma app. Por exemplo, o módulo IIS FastCGI baseia-se em tubos nomeados para coordenar os processos individuais que executam páginas PHP.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>Execução de código, processos e memória
Como notado anteriormente, as aplicações funcionam dentro de processos de trabalhadores de baixo privilégio usando uma identidade de piscina de aplicação aleatória. O código de aplicação tem acesso ao espaço de memória associado ao processo do trabalhador, bem como a quaisquer processos infantis que possam ser gerados por processos CGI ou outras aplicações. No entanto, uma aplicação não pode aceder à memória ou dados de outra aplicação mesmo que esteja na mesma máquina virtual.

As aplicações podem executar scripts ou páginas escritas com quadros de desenvolvimento web suportados. O Serviço de Aplicações não configura quaisquer definições de enquadramento web para modos mais restritos. Por exemplo, ASP.NET aplicações que funcionam no App Service funcionam em "plena" confiança em oposição a um modo de confiança mais restrito. As estruturas web, incluindo as asp clássicas e ASP.NET, podem ligar para componentes com (mas não fora dos componentes com do processo) como o ADO (ActiveX Data Objects) que são registados por padrão no sistema operativo Windows.

As aplicações podem desovar e executar código arbitrário. É permitido que uma aplicação faça coisas como gerar uma concha de comando ou executar um script PowerShell. No entanto, embora códigos e processos arbitrários possam ser gerados a partir de uma app, os programas e scripts executáveis ainda estão restritos aos privilégios concedidos ao conjunto de aplicações dos pais. Por exemplo, uma aplicação pode gerar um executável que faz uma chamada http de saída, mas esse mesmo executável não pode tentar desvincular o endereço IP de uma máquina virtual do seu NIC. Fazer uma chamada de rede de saída é permitido a um código de baixo privilégio, mas tentar reconfigurar as definições de rede numa máquina virtual requer privilégios administrativos.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>Registos e eventos de diagnóstico
A informação de log é outro conjunto de dados que algumas apps tentam aceder. Os tipos de informação de registo disponíveis para o código que funciona no App Service incluem informações de diagnóstico e registo geradas por uma aplicação que também é facilmente acessível à aplicação. 

Por exemplo, os registos W3C HTTP gerados por uma aplicação ativa estão disponíveis num diretório de registo na localização da partilha de rede criada para a app, ou disponíveis no armazenamento blob se um cliente tiver configurado o registo w3C para armazenamento. Esta última opção permite recolher grandes quantidades de troncos sem o risco de exceder os limites de armazenamento de ficheiros associados a uma parte da rede.

Na mesma linha, as informações de diagnóstico em tempo real das aplicações .NET também podem ser registadas utilizando a infraestrutura de rastreio e diagnóstico .NET, com opções para escrever as informações de rastreio para a partilha de rede da aplicação, ou, em alternativa, para um local de armazenamento blob.

As áreas de registo de diagnósticos e rastreios que não estão disponíveis para apps são eventos Windows ETW e registos comuns de eventos windows (por exemplo, registos de eventos system, application e segurança). Uma vez que as informações sobre vestígios de ETW podem potencialmente ser visível em toda a máquina (com os ACLs certos), ler e escrever o acesso aos eventos DeTW está bloqueado. Os desenvolvedores podem notar que as chamadas da API para ler e escrever eventos ETW e registos comuns de eventos windows parecem funcionar, mas isso porque o Serviço de Aplicações está a "fingir" as chamadas para que pareçam ter sucesso. Na realidade, o código de aplicação não tem acesso aos dados deste evento.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>Acesso ao registo
As aplicações têm acesso apenas a muito (embora não a toda) do registo da máquina virtual em que estão a funcionar. Na prática, isto significa que as chaves de registo que permitem o acesso apenas a leitura ao grupo de Utilizadores locais são acessíveis por apps. Uma área do registo que não é suportada atualmente para ler\_\_ou escrever acesso é a colmeia HKEY CURRENT USER.

O acesso por escrito ao registo está bloqueado, incluindo o acesso a quaisquer chaves de registo por utilizador. Do ponto de vista da aplicação, o acesso ao registo nunca deve ser invocado no ambiente Azure, uma vez que as aplicações podem (e fazem) ser migradas através de diferentes máquinas virtuais. O único armazenamento recedível persistente que pode ser dependente de uma aplicação é a estrutura de diretório de conteúdos por app armazenada nas ações da App Service UNC. 

## <a name="remote-desktop-access"></a>Acesso remoto ao ambiente de trabalho

O Serviço de Aplicações não fornece acesso remoto aos casos vm.

## <a name="more-information"></a>Mais informações

[Caixa de areia azure App Service](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) - As informações mais atualizadas sobre o ambiente de execução do Serviço de Aplicações. Esta página é mantida diretamente pela equipa de desenvolvimento do Serviço de Aplicações.

