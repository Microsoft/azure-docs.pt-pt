---
title: Funcionalidade do sistema operativo no serviço de aplicações - Azure
description: Saiba mais sobre a funcionalidade do SO disponível para aplicações web, back-ends de aplicações móveis e API apps no App Service do Azure
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: 39d5514f-0139-453a-b52e-4a1c06d8d914
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: e5ab6651503766844b2aeef1849bffff9cf4d7bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835516"
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Funcionalidade do sistema operativo no App Service do Azure
Este artigo descreve a funcionalidade de sistema operativo de linha de base comum, que está disponível para todas as aplicações do Windows em execução no [App Service do Azure](https://go.microsoft.com/fwlink/?LinkId=529714). Esta funcionalidade inclui o ficheiro, rede e acesso ao Registro e os registos de diagnóstico e eventos. 

> [!NOTE] 
> [Aplicações do Linux](containers/app-service-linux-intro.md) no serviço de aplicações executadas em seus próprios contentores. Sem acesso para o sistema operativo anfitrião é permitido, tem acesso de raiz para o contentor. Da mesma forma, para [aplicações em execução nos contentores do Windows](app-service-web-get-started-windows-container.md), tem acesso administrativo para o contentor, mas não existe acesso para o sistema operativo anfitrião. 
>

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>Escalões do plano de serviço de aplicações
Serviço de aplicações executa aplicações de cliente num ambiente de alojamento multi-inquilino. Aplicações implementadas no **gratuito** e **partilhado** escalões executados em processos de trabalho em máquinas virtuais partilhadas, enquanto as aplicações implementadas no **padrão** e **Premium**  camadas é executado em máquinas virtuais dedicadas especificamente para aplicações associadas um único cliente.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Como o serviço de aplicações suporta uma experiência totalmente integrada de dimensionamento entre diferentes camadas, a configuração de segurança imposta para aplicações de serviço de aplicações permanece igual. Isto garante que aplicações não, de repente, ter um comportamento diferente, a falhar de maneiras inesperadas, quando muda de plano de serviço de aplicações de um escalão para outro.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>Estruturas de desenvolvimento
Escalões de preços do serviço de aplicações, controlar a quantidade de recursos de computação (CPU, armazenamento em disco, memória e saída de rede) disponíveis para aplicações. No entanto, o alcance da funcionalidade do framework disponível para aplicações permanece o mesmo, independentemente dos escalões de dimensionamento.

Serviço de aplicações suporta uma variedade de estruturas de desenvolvimento, incluindo ASP.NET, ASP clássico, node. js, PHP e Python – tudo o que executar como extensões dentro do IIS. Para simplificar e normalizar a configuração de segurança, aplicações de serviço de aplicações normalmente executam diversas estruturas de desenvolvimento com suas configurações padrão. Poderia ser uma abordagem à configuração de aplicações personalizar a área de superfície de API e a funcionalidade para cada estrutura de desenvolvimento individuais. Serviço de aplicações adota uma abordagem mais genérica, permitindo que uma linha de base comum de funcionalidade do sistema operativo, independentemente da estrutura de desenvolvimento de uma aplicação.

As secções seguintes resumem os tipos gerais de funcionalidade do sistema operativo disponível para aplicações de serviço de aplicações.

<a id="FileAccess"></a>

## <a name="file-access"></a>Acesso a ficheiros
Várias unidades existem no serviço de aplicações, incluindo unidades locais e unidades de rede.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>Unidades locais
Em seu núcleo, o serviço de aplicações é um serviço em execução sobre a infra-estrutura de Azure PaaS (plataforma como serviço). Como resultado, as unidades locais que estão "ligadas" a uma máquina virtual são os mesmos tipos de unidade disponíveis para qualquer função de trabalho em execução no Azure. Isto inclui:

- Unidade do sistema operativo (a unidade D:\)
- Uma unidade de aplicação que contém ficheiros de cspkg de pacote do Azure utilizados exclusivamente por serviço de aplicações (e inacessível para os clientes)
- Uma unidade de "user" (a unidade C:\), cujo tamanho varia consoante o tamanho da VM. 

É importante monitorizar a sua utilização de disco à medida que aumenta a sua aplicação. Se for atingida a quota de disco, ele pode ter efeitos adversos à sua aplicação. Por exemplo: 

- A aplicação poderão emitir um erro que indica que não existe espaço suficiente no disco.
- Poderá ver erros no disco ao navegar para a consola Kudu.
- Implementação a partir do VSTS ou o Visual Studio pode falhar com `ERROR_NOT_ENOUGH_DISK_SPACE: Web deployment task failed. (Web Deploy detected insufficient space on disk)`.
- Seu aplicativo talvez sofram com um desempenho lento.

<a id="NetworkDrives"></a>

### <a name="network-drives-aka-unc-shares"></a>Unidades de rede (também conhecido como partilhas UNC)
Um dos aspectos exclusivos do serviço de aplicações que facilita a implementação da aplicação e a manutenção simples é que todo o conteúdo de utilizador é armazenado num conjunto de compartilhamentos UNC. Esse modelo é bem mapeado para o padrão comum de armazenamento de conteúdos utilizada pelo ambientes que tenham vários servidores com balanceamento de carga de alojamento na web no local. 

No serviço de aplicações, existe um número de compartilhamentos UNC, criado em cada Datacenter. Uma percentagem do conteúdo de utilizador para todos os clientes em cada Datacenter é alocada para cada partilha UNC. Além disso, todo o ficheiro de conteúdo para a subscrição de um único cliente sempre é colocada sobre o mesmo UNC partilhar. 

Devido ao trabalho de serviços como o Azure, a máquina virtual específica responsável por hospedar um compartilhamento UNC será alterado ao longo do tempo. É garantido que compartilhamentos UNC serão montados por máquinas virtuais diferentes, como são encaminhados e reduzir verticalmente durante o curso normal de operações do Azure. Por esse motivo, aplicações nunca devem fazer suposições embutido em que as informações da máquina num caminho de ficheiro UNC irão permanecer estáveis ao longo do tempo. Em vez disso, deve utilizar o conveniente *faux* caminho absoluto **D:\home\site** que fornece o serviço de aplicações. Este caminho absoluto faux fornece um método portáteis e independente de aplicação e utilizador que se refere a própria aplicação. Usando **D:\home\site**, um pode transferir os ficheiros partilhados da aplicação para aplicações sem ter de configurar um novo caminho absoluto para cada transferência.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>Tipos de acesso a ficheiros concedidas a uma aplicação
Subscrição de cada cliente tem uma estrutura de diretório reservado num compartilhamento UNC específico dentro de um centro de dados. Um cliente pode ter várias aplicações criadas dentro de um centro de dados específicas, para que todos os diretórios que pertencem a uma subscrição de cliente único são criados sobre o mesmo UNC partilhar. A partilha pode incluir diretórios como os de conteúdo, erro e registos de diagnóstico e versões anteriores da aplicação criada pelo controlo de origem. Conforme o esperado, os diretórios de aplicação de um cliente estão disponíveis para leitura e de acesso de escrita em tempo de execução pelo código de aplicação da aplicação.

Nas unidades locais anexadas à máquina virtual que executa uma aplicação, serviço de aplicações, reserva-se uma parte do espaço na unidade C:\ para armazenamento temporário local de aplicações específicas. Embora uma aplicação tem acesso de leitura/escrita total no seu próprio armazenamento local temporário, que o armazenamento realmente não se destina a ser utilizada diretamente pelo código da aplicação. Em vez disso, a intenção é fornecer armazenamento temporário de ficheiros para o IIS e web de estruturas de aplicações. Serviço de aplicações também limita a quantidade de armazenamento local temporário e disponível para cada aplicação para impedir que aplicações individuais consumir quantidades excessivas de armazenamento de ficheiros local.

Dois exemplos de como o serviço de aplicações utiliza um armazenamento temporário local são o diretório para ficheiros temporários do ASP.NET e o diretório para o IIS de ficheiros comprimidos. O sistema de compilação do ASP.NET utiliza o diretório de "Temporary ASP.NET Files" como uma localização da cache de compilação temporário. O IIS utiliza o diretório de "Arquivos compactados temporária de IIS" para armazenar a saída de resposta compactada. Ambos os tipos de arquivo de utilização (bem como outras pessoas) remapeados no serviço de aplicações no armazenamento temporário local de por aplicação. Este remapeamento garante que continua como esperado.

Cada aplicação no App Service é executado como uma identidade de processo de trabalho de baixo privilégio exclusivo aleatório chamada "identidade do conjunto aplicacional", ainda mais descrita aqui: [ https://www.iis.net/learn/manage/configuring-security/application-pool-identities ](https://www.iis.net/learn/manage/configuring-security/application-pool-identities). Código do aplicativo usa esta identidade para acesso só de leitura básico para a unidade de sistema operativo (a unidade D:\). Isso significa que o código da aplicação pode listar as estruturas de diretório comum e ler ficheiros comuns na unidade do sistema operativo. Embora pareça estar num nível abrangente de um pouco de acesso, o mesmo diretórios e ficheiros são acessíveis quando Aprovisiona uma função de trabalho no Azure serviço alojado e ler o conteúdo de unidade. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>Acesso a ficheiros em várias instâncias
O diretório base contém conteúdo de uma aplicação e pode escrever o código da aplicação no mesmo. Se uma aplicação é executada em várias instâncias, o diretório de raiz é partilhado entre todas as instâncias para que todas as instâncias de ver o mesmo diretório. Por isso, por exemplo, se uma aplicação guarda os ficheiros carregados para o diretório principal, esses arquivos estão imediatamente disponíveis para todas as instâncias. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>Acesso à rede
Código da aplicação pode utilizar o TCP/IP e protocolos baseados em UDP para fazer conexões de rede de saída para pontos finais de acessíveis de Internet que expõem serviços externos. As aplicações podem utilizar estes mesmos protocolos para ligar aos serviços do Azure&#151;por exemplo, através do estabelecimento de ligações HTTPS para a base de dados SQL.

Também existe uma capacidade limitada para aplicações estabelecer uma ligação de local de loopback e tem uma aplicação escutar esse soquete de local loopback. Esta funcionalidade existe principalmente para permitir que as aplicações que escutar sockets de local loopback como parte de sua funcionalidade. Cada aplicação vê uma ligação de loopback "private". Aplicação "A" não é possível escutar um soquete de local loopback estabelecido pela aplicação "B".

Pipes nomeados também são suportados como um mecanismo de comunicação entre processos (IPC) entre os processos diferentes que, coletivamente, executar uma aplicação. Por exemplo, o módulo FastCGI do IIS baseia-se nos pipes nomeados para coordenar os processos individuais que são executados páginas PHP.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>Execução de código, processos e memória
Conforme observado anteriormente, as aplicações são executadas dentro de processos de trabalho de baixo privilégio com uma identidade do conjunto aplicacional aleatório. Código da aplicação tem acesso ao espaço de memória associado com o processo de trabalho, bem como quaisquer processos filho que podem ser gerados por processos CGI ou outras aplicações. No entanto, uma aplicação não é possível acessar a memória ou dados de outra aplicação, mesmo se estiver na mesma máquina virtual.

Aplicações podem executar scripts ou páginas escritas com estruturas de desenvolvimento da web compatível. Serviço de aplicações não configure as definições de estrutura de web para modos mais restritas. Por exemplo, os aplicativos do ASP.NET em execução no serviço de aplicações executam confiança "total" em vez de um modo de confiança de mais restrito. Estruturas da Web, incluindo clássico ASP e ASP.NET, podem chamar componentes de COM em processamento (mas não fora de componentes de COM de processo), como o ADO (ActiveX Data Objects), que são registrados por padrão no sistema operacional Windows.

As aplicações podem gerar e executar código arbitrário. É aceitável para uma aplicação para fazer coisas como spawn um shell de comando ou executar um script do PowerShell. No entanto, mesmo que o código arbitrário e processos podem ser gerados a partir de uma aplicação, programas executáveis e scripts são restritas aos privilégios concedidos ao conjunto aplicacional principal. Por exemplo, uma aplicação pode gerar um ficheiro executável que faz uma chamada HTTP de saída, mas que o mesmo executável não é possível tentar desvincular o endereço IP de uma máquina virtual do seu NIC. Fazer uma chamada de rede de saída é permitido para o código de baixo privilégio, mas tentar reconfigurar as definições de rede numa máquina virtual requer privilégios administrativos.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>Eventos e registos de diagnóstico
Informações de registo são outro conjunto de dados que tentam aceder a algumas aplicações. Os tipos de informações de log disponíveis para código em execução no serviço de aplicações inclui diagnóstico e registar informações geradas por uma aplicação que também é fácil de aceder à aplicação. 

Por exemplo, os registos de HTTP de W3C gerados por uma aplicação do Active Directory estão disponíveis num diretório de registo na localização de partilha de rede criada para a aplicação, ou está disponível no armazenamento de BLOBs, se um cliente tiver configurado o registo de W3C para o armazenamento. A última opção permite que grandes quantidades de registos para ser coletada sem o risco de exceder os limites de armazenamento de ficheiros associados a um compartilhamento de rede.

Numa veia semelhante, informações de diagnóstico em tempo real a partir de aplicações de .NET podem também ser registadas com o rastreamento do .NET e a infraestrutura de diagnósticos, com opções para escrever as informações de rastreio para partilha de rede a aplicação, ou, em alternativa para uma localização de armazenamento de Blobs.

Áreas de diagnóstico de registo e o rastreio que não está disponível para as aplicações são eventos ETW do Windows e registos de eventos do Windows comuns (por exemplo, sistema, a aplicação e a segurança registos de eventos). Uma vez que as informações de rastreio ETW podem ser todo o computador podem ser visualizado (com o direito de ACLs), acesso de escrita para eventos do ETW e leitura são bloqueados. Os desenvolvedores podem observar que chamadas à API para ler e escrever ETW, eventos e registos de eventos comuns do Windows parecem funcionar, mas isso é porque o serviço de aplicações é "falsifique" as chamadas para que apareçam com êxito. Na realidade, o código do aplicativo não tem acesso a estes dados de eventos.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>Acesso ao registo
As aplicações têm acesso só de leitura a muitas (embora não todos) do registro da máquina virtual em que são executadas. Na prática, isso significa que as chaves de registo que permitem acesso só de leitura ao grupo de usuários locais podem ser acedidas por aplicações. Uma área do Registro que não é atualmente suportado para leitura ou acesso de gravação é o HKEY\_atual\_seção do USUÁRIO.

Acesso de escrita para o registo é bloqueado, incluindo o acesso a quaisquer chaves de Registro por usuário. Da perspectiva da aplicação, acesso de escrita para o registo deve nunca é confiável no ambiente Azure, uma vez que aplicações podem (e fazer) são migrados entre diferentes máquinas virtuais. O armazenamento gravável apenas persistente que pode ser dependente-se de uma aplicação é a estrutura de diretório de conteúdo por aplicação armazenada nas partilhas de UNC do serviço de aplicações. 

## <a name="remote-desktop-access"></a>Acesso de ambiente de trabalho remoto

Serviço de aplicações não fornece acesso de ambiente de trabalho remoto para as instâncias de VM.

## <a name="more-information"></a>Mais informações

[Sandbox do serviço de aplicações do Azure](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) -as informações mais atualizadas sobre o ambiente de execução do serviço de aplicações. Esta página é mantida diretamente pela equipe de desenvolvimento do serviço de aplicações.

