---
title: Funcionalidade do sistema operativo
description: Conheça a funcionalidade OS no Serviço de Aplicações Azure no Windows. Descubra quais os tipos de ficheiros, rede e acesso ao registo que a sua aplicação obtém.
ms.assetid: 39d5514f-0139-453a-b52e-4a1c06d8d914
ms.topic: article
ms.date: 10/30/2018
ms.custom: seodec18
ms.openlocfilehash: 11798db483f0ba370f73340489c17f38c87ede41
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88080203"
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Funcionalidade do sistema operativo no Azure App Service
Este artigo descreve a funcionalidade comum do sistema operativo de base que está disponível para todas as aplicações do Windows em execução no [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Esta funcionalidade inclui acesso a ficheiros, redes e registos e registos e registos e eventos de diagnóstico. 

> [!NOTE] 
> [As aplicações Linux](overview.md#app-service-on-linux) no Serviço de Aplicações funcionam nos seus próprios contentores. Não é permitido acesso ao sistema operativo hospedeiro, tem acesso à raiz do recipiente. Da mesma forma, para [aplicações em execução em contentores windows,](quickstart-custom-container.md?pivots=container-windows)tem acesso administrativo ao contentor, mas sem acesso ao sistema operativo anfitrião. 
>

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>Níveis de plano de serviço de aplicações
O App Service executa aplicativos de clientes num ambiente de hospedagem multi-inquilino. As aplicações implementadas nos níveis **Free** and **Shared** funcionam em processos de trabalhadores em máquinas virtuais partilhadas, enquanto as aplicações implementadas nos níveis **Standard** e **Premium** funcionam em máquinas virtuais dedicadas especificamente às aplicações associadas a um único cliente.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Como o App Service suporta uma experiência de escala perfeita entre diferentes níveis, a configuração de segurança aplicada para aplicações do App Service permanece a mesma. Isto garante que as aplicações não se comportam de forma diferente, falhando de formas inesperadas, quando o plano do Serviço de Aplicações muda de um nível para outro.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>Quadros de desenvolvimento
Os níveis de preços do Serviço de Aplicações controlam a quantidade de recursos computacional (CPU, armazenamento de discos, memória e saída de rede) disponíveis para aplicações. No entanto, a amplitude da funcionalidade-quadro disponível para as aplicações permanece a mesma, independentemente dos escalões de escala.

O App Service suporta uma variedade de quadros de desenvolvimento, incluindo ASP.NET, ASP clássico, node.js, PHP e Python - todos os quais funcionam como extensões dentro do IIS. De forma a simplificar e normalizar a configuração de segurança, as aplicações do App Service normalmente executam as várias estruturas de desenvolvimento com as suas definições padrão. Uma abordagem para configurar aplicações poderia ter sido personalizar a área de superfície e funcionalidade da API para cada quadro de desenvolvimento individual. Em vez disso, o App Service tem uma abordagem mais genérica, permitindo uma linha de base comum da funcionalidade do sistema operativo, independentemente do enquadramento de desenvolvimento de uma aplicação.

As secções seguintes resumem os tipos gerais de funcionalidade do sistema operativo disponíveis para aplicações do Serviço de Aplicações.

<a id="FileAccess"></a>

## <a name="file-access"></a>Acesso a ficheiros
Existem várias unidades dentro do Serviço de Aplicações, incluindo unidades locais e unidades de rede.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>Unidades locais
Na sua essência, o App Service é um serviço em execução em cima da infraestrutura Azure PaaS (plataforma como serviço). Como resultado, as unidades locais que estão "ligadas" a uma máquina virtual são os mesmos tipos de unidade disponíveis para qualquer papel de trabalhador em execução em Azure. O que está incluído:

- Uma unidade do sistema operativo (o D:\ unidade)
- Uma unidade de aplicação que contém ficheiros cspkg Azure Package usados exclusivamente pelo App Service (e inacessíveis aos clientes)
- Uma unidade "user" (o C:\ unidade), cujo tamanho varia consoante o tamanho do VM. 

É importante monitorizar a utilização do disco à medida que a sua aplicação cresce. Se a quota de disco for alcançada, pode ter efeitos adversos na sua aplicação. Por exemplo: 

- A aplicação pode lançar um erro indicando que não há espaço suficiente no disco.
- Pode ver erros de disco ao navegar na consola Kudu.
- A implementação de Azure DevOps ou Visual Studio pode falhar com `ERROR_NOT_ENOUGH_DISK_SPACE: Web deployment task failed. (Web Deploy detected insufficient space on disk)` .
- A sua aplicação pode sofrer um desempenho lento.

<a id="NetworkDrives"></a>

### <a name="network-drives-unc-shares"></a>Unidades de rede (ações da UNC)
Um dos aspetos únicos do Serviço de Aplicações que torna a implementação e manutenção de aplicações simples é que todos os conteúdos do utilizador são armazenados num conjunto de ações da UNC. Este modelo mapeia bem o padrão comum de armazenamento de conteúdo usado por ambientes de hospedagem web no local que têm vários servidores equilibrados em carga. 

Dentro do Serviço de Aplicações, há uma série de ações da UNC criadas em cada centro de dados. Uma percentagem do conteúdo do utilizador para todos os clientes em cada centro de dados é atribuída a cada ação da UNC. Além disso, todo o conteúdo do ficheiro para a subscrição de um único cliente é sempre colocado na mesma parte da UNC. 

Devido ao funcionamento dos serviços Azure, a máquina virtual específica responsável pela hospedagem de uma quota da UNC mudará ao longo do tempo. É garantido que as ações da UNC serão montadas por diferentes máquinas virtuais, uma vez que são criadas para cima e para baixo durante o curso normal das operações do Azure. Por esta razão, as aplicações nunca devem fazer pressupostos codificados de que as informações da máquina num caminho de ficheiros da UNC permanecerão estáveis ao longo do tempo. Em vez disso, devem usar o *conveniente* caminho falso absoluto **D:\home\site** que o App Service fornece. Este caminho falso absoluto fornece um método portátil, app-e-utilizador-agnóstico para se referir à própria app. Ao utilizar **o D:\home\site,** pode-se transferir ficheiros partilhados de app para app sem ter de configurar um novo caminho absoluto para cada transferência.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>Tipos de acesso a ficheiros concedidos a uma aplicação
A subscrição de cada cliente tem uma estrutura de diretório reservada numa parte específica da UNC dentro de um centro de dados. Um cliente pode ter várias aplicações criadas dentro de um centro de dados específico, pelo que todos os diretórios pertencentes a uma única subscrição de clientes são criados na mesma parte do UNC. A partilha pode incluir diretórios como os de conteúdos, erros e registos de diagnóstico, e versões anteriores da app criada por controlo de origem. Como esperado, os diretórios de aplicações de um cliente estão disponíveis para leitura e escrita de acesso no tempo de execução pelo código de aplicação da aplicação.

Nas unidades locais anexadas à máquina virtual que executa uma aplicação, o App Service reserva um pedaço de espaço no C:\ unidade para armazenamento local temporário específico de aplicações. Apesar de uma aplicação ter acesso total de leitura/escrita ao seu próprio armazenamento local temporário, esse armazenamento realmente não se destina a ser usado diretamente pelo código de aplicação. Pelo contrário, a intenção é fornecer armazenamento temporário de ficheiros para iis e quadros de aplicações web. O Serviço de Aplicações também limita a quantidade de armazenamento local temporário disponível para cada aplicação para evitar que aplicações individuais consumam quantidades excessivas de armazenamento de ficheiros locais.

Dois exemplos de como o App Service utiliza armazenamento local temporário são o diretório para ficheiros de ASP.NET temporárias e o diretório para ficheiros comprimidos IIS. O sistema de compilação ASP.NET utiliza o diretório "Ficheiros temporários de ASP.NET" como localização temporária de cache de compilação. O IIS utiliza o diretório "Ficheiros Comprimidos Temporários IIS" para armazenar a saída de resposta comprimido. Ambos os tipos de utilização de ficheiros (assim como outros) são rempepeitados no Serviço de Aplicações para armazenamento local temporário por aplicação. Este remapping garante que a funcionalidade continua como esperado.

Cada aplicação no Serviço de Aplicações funciona como uma identidade de processo de trabalhadoridade única e privilegiada aleatória chamada "identidade do conjunto de aplicações", descrita mais adiante aqui: [https://www.iis.net/learn/manage/configuring-security/application-pool-identities](https://www.iis.net/learn/manage/configuring-security/application-pool-identities) . O código de aplicação utiliza esta identidade para o acesso básico apenas à unidade do sistema operativo (o D:\ unidade). Isto significa que o código de aplicação pode listar estruturas comuns de diretório e ler ficheiros comuns na unidade do sistema operativo. Embora este possa parecer um nível de acesso um pouco amplo, os mesmos diretórios e ficheiros são acessíveis quando fornece um papel de trabalhador num serviço hospedado no Azure e lê o conteúdo da unidade. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>Acesso a ficheiros em várias instâncias
O diretório doméstico contém conteúdo de uma aplicação, e o código de aplicação pode escrever-lhe. Se uma aplicação for executado em várias instâncias, o diretório doméstico é partilhado entre todos os casos para que todos os casos vejam o mesmo diretório. Assim, por exemplo, se uma aplicação guardar ficheiros enviados para o diretório doméstico, esses ficheiros estão imediatamente disponíveis para todas as instâncias. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>Acesso à rede
O código de aplicação pode utilizar protocolos baseados em TCP/IP e UDP para fazer ligações de rede de saída a pontos finais acessíveis à Internet que expõem serviços externos. As aplicações podem utilizar estes mesmos protocolos para se conectarem a serviços dentro do Azure, por exemplo, estabelecendo ligações HTTPS à Base de Dados SQL.

Existe também uma capacidade limitada para as aplicações estabelecerem uma ligação de backback local, e ter uma aplicação a ouvir sobre a tomada de backback local. Esta funcionalidade existe principalmente para permitir que as aplicações que ouçam as tomadas de backback locais como parte da sua funcionalidade. Cada aplicação vê uma ligação loopback "privada". A aplicação "A" não pode ouvir uma tomada de backback local estabelecida pela aplicação "B".

Os tubos nomeados também são suportados como um mecanismo de comunicação inter-processo (IPC) entre diferentes processos que executam coletivamente uma aplicação. Por exemplo, o módulo IIS FastCGI baseia-se em tubos nomeados para coordenar os processos individuais que executam páginas PHP.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>Execução de código, processos e memória
Como já foi notado, as aplicações são executadas dentro de processos de trabalhadores pouco privilegiados usando uma identidade de conjunto de aplicações aleatória. O código de aplicação tem acesso ao espaço de memória associado ao processo do trabalhador, bem como a quaisquer processos infantis que possam ser gerados por processos CGI ou outras aplicações. No entanto, uma aplicação não consegue aceder à memória ou dados de outra app, mesmo que se desempenhe na mesma máquina virtual.

As aplicações podem executar scripts ou páginas escritas com quadros de desenvolvimento web suportados. O Serviço de Aplicações não configura quaisquer configurações de estrutura web para modos mais restritos. Por exemplo, ASP.NET aplicações em execução no Serviço de Aplicações funcionam em confiança "completa", em oposição a um modo de confiança mais restrito. As estruturas web, incluindo as asp clássicas e ASP.NET, podem chamar componentes COM in-process (mas não fora dos componentes COM do processo) como o ADO (ActiveX Data Objects) que são registados por padrão no sistema operativo Windows.

As aplicações podem desovar e executar código arbitrário. É permitido que uma aplicação faça coisas como desovar uma concha de comando ou executar um script PowerShell. No entanto, apesar de códigos e processos arbitrários poderem ser gerados a partir de uma aplicação, os programas e scripts executáveis ainda estão restritos aos privilégios concedidos ao conjunto de aplicações dos pais. Por exemplo, uma aplicação pode gerar uma chamada executável que faz uma chamada HTTP de saída, mas essa mesma executável não pode tentar desvincular o endereço IP de uma máquina virtual a partir do seu NIC. Fazer uma chamada de rede de saída é permitido a um código privilegiado baixo, mas tentar reconfigurar as definições de rede numa máquina virtual requer privilégios administrativos.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>Registos e eventos de diagnóstico
A informação de registo é outro conjunto de dados que algumas aplicações tentam aceder. Os tipos de informação de registo disponível para código em execução no Serviço de Aplicações incluem informações de diagnóstico e registo geradas por uma aplicação que também é facilmente acessível à aplicação. 

Por exemplo, os registos W3C HTTP gerados por uma aplicação ativa estão disponíveis num diretório de registos na localização da partilha de rede criada para a aplicação, ou disponíveis no armazenamento de blobs se um cliente tiver configurado o registo W3C para armazenamento. Esta última opção permite recolher grandes quantidades de troncos sem o risco de exceder os limites de armazenamento de ficheiros associados a uma partilha de rede.

Na mesma linha, as informações de diagnóstico em tempo real de aplicações .NET também podem ser registadas utilizando a infraestrutura de rastreio e diagnóstico .NET, com opções para escrever a informação de rastreio para a partilha de rede da app, ou alternativamente para um local de armazenamento de bolhas.

Áreas de registo e rastreio de diagnósticos que não estão disponíveis para aplicações são eventos Do Windows ETW e registos comuns de eventos windows (por exemplo, registos de eventos de sistema, aplicação e segurança). Uma vez que as informações sobre vestígios da ETW podem potencialmente ser visualizadas em toda a máquina (com os ACLs certos), ler e escrever o acesso a eventos ETW são bloqueados. Os desenvolvedores podem notar que a API chama para ler e escrever eventos ETW e registos comuns de eventos windows parecem funcionar, mas isso é porque o Serviço de Aplicações está a "fingir" as chamadas para que pareçam ter sucesso. Na realidade, o código de aplicação não tem acesso a estes dados do evento.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>Acesso ao registo
As aplicações têm acesso apenas a muito (embora nem todos) do registo da máquina virtual em que estão a funcionar. Na prática, isto significa que as chaves de registo que permitem o acesso apenas à leitura do grupo de Utilizadores locais são acessíveis por apps. Uma área do registo que não é suportada para o acesso lido ou escrito é a colmeia HKEY \_ CURRENT \_ USER.

O acesso por escrito ao registo está bloqueado, incluindo o acesso a quaisquer chaves de registo por utilizador. Do ponto de vista da aplicação, o acesso ao registo nunca deve ser confiado no ambiente Azure, uma vez que as aplicações podem (e fazem) ser migradas através de diferentes máquinas virtuais. O único armazenamento escrito persistente que pode ser dependente de uma aplicação é a estrutura de diretório de conteúdos por aplicação armazenada nas ações do Serviço de Aplicações UNC. 

## <a name="remote-desktop-access"></a>Acesso remoto ao ambiente de trabalho

O Serviço de Aplicações não fornece acesso remoto ao ambiente de trabalho às instâncias VM.

## <a name="more-information"></a>Mais informações

Caixa de [areia do Azure App Service](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) - A informação mais atualizada sobre o ambiente de execução do Serviço de Aplicações. Esta página é mantida diretamente pela equipa de desenvolvimento do Serviço de Aplicações.

