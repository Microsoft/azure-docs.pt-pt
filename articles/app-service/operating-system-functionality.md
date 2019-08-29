---
title: Funcionalidade do sistema operacional no serviço de aplicativo – Azure
description: Saiba mais sobre a funcionalidade do sistema operacional disponível para aplicativos Web, back-ends de aplicativo móvel e aplicativos de API no serviço Azure App
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: 39d5514f-0139-453a-b52e-4a1c06d8d914
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/30/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: b108814caaace83cd417dc8858e27ed01d54c39e
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066772"
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Funcionalidade do sistema operacional no serviço de Azure App
Este artigo descreve a funcionalidade comum do sistema operacional de linha de base que está disponível para todos os aplicativos do Windows em execução no [serviço Azure app](https://go.microsoft.com/fwlink/?LinkId=529714). Essa funcionalidade inclui acesso de arquivo, rede e registro e logs e eventos de diagnóstico. 

> [!NOTE] 
> Os [aplicativos do Linux](containers/app-service-linux-intro.md) no serviço de aplicativo são executados em seus próprios contêineres. Nenhum acesso ao sistema operacional do host é permitido, você tem acesso à raiz para o contêiner. Da mesma forma, para [aplicativos em execução em contêineres do Windows](app-service-web-get-started-windows-container.md), você tem acesso administrativo ao contêiner, mas não tem acesso ao sistema operacional do host. 
>

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>Camadas do plano do serviço de aplicativo
O serviço de aplicativo executa aplicativos de cliente em um ambiente de hospedagem multilocatário. Os aplicativos implantados nas camadas **gratuita** e **compartilhada** são executados em processos de trabalho em máquinas virtuais compartilhadas, enquanto os aplicativos implantados nas camadas **Standard** e **Premium** são executados em máquinas virtuais dedicadas especificamente para os aplicativos associados com um único cliente.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Como o serviço de aplicativo dá suporte a uma experiência de dimensionamento contínuo entre diferentes camadas, a configuração de segurança imposta para aplicativos do serviço de aplicativo permanece a mesma. Isso garante que os aplicativos não tenham um comportamento de repente diferente, falhando de maneiras inesperadas, quando o plano do serviço de aplicativo alternar de uma camada para outra.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>Estruturas de desenvolvimento
Os tipos de preço do serviço de aplicativo controlam a quantidade de recursos de computação (CPU, armazenamento em disco, memória e saída de rede) disponíveis para aplicativos. No entanto, a amplitude da funcionalidade de estrutura disponível para aplicativos permanece a mesma, independentemente das camadas de dimensionamento.

O serviço de aplicativo dá suporte a uma variedade de estruturas de desenvolvimento, incluindo ASP.NET, ASP clássico, Node. js, PHP e Python – todas as extensões executar como no IIS. Para simplificar e normalizar a configuração de segurança, os aplicativos do serviço de aplicativo normalmente executam as várias estruturas de desenvolvimento com suas configurações padrão. Uma abordagem para configurar aplicativos poderia ter sido personalizar a área de superfície da API e a funcionalidade para cada estrutura de desenvolvimento individual. Em vez disso, o serviço de aplicativo usa uma abordagem mais genérica habilitando uma linha de base comum de funcionalidade do sistema operacional, independentemente da estrutura de desenvolvimento de um aplicativo.

As seções a seguir resumem os tipos gerais de funcionalidade do sistema operacional disponíveis para aplicativos do serviço de aplicativo.

<a id="FileAccess"></a>

## <a name="file-access"></a>Acesso a arquivos
Existem várias unidades no serviço de aplicativo, incluindo unidades locais e unidades de rede.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>Unidades locais
Em seu núcleo, o serviço de aplicativo é um serviço em execução sobre a infraestrutura de PaaS do Azure (plataforma como serviço). Como resultado, as unidades locais que estão "anexadas" a uma máquina virtual são os mesmos tipos de unidade disponíveis para qualquer função de trabalho em execução no Azure. Isto inclui:

- Uma unidade do sistema operacional (o D:\ disquete
- Uma unidade de aplicativo que contém arquivos cspkg de pacote do Azure usados exclusivamente pelo serviço de aplicativo (e inacessíveis para os clientes)
- Uma unidade de "usuário" (o C:\ ), cujo tamanho varia dependendo do tamanho da VM. 

É importante monitorar a utilização do disco à medida que seu aplicativo cresce. Se a cota de disco for atingida, ela poderá ter efeitos adversos para seu aplicativo. Por exemplo: 

- O aplicativo pode gerar um erro indicando que não há espaço suficiente no disco.
- Você pode ver erros de disco ao navegar até o console do kudu.
- A implantação do Azure DevOps ou do Visual Studio pode `ERROR_NOT_ENOUGH_DISK_SPACE: Web deployment task failed. (Web Deploy detected insufficient space on disk)`falhar com.
- Seu aplicativo pode sofrer um desempenho lento.

<a id="NetworkDrives"></a>

### <a name="network-drives-aka-unc-shares"></a>Unidades de rede (também conhecido como compartilhamentos UNC)
Um dos aspectos exclusivos do serviço de aplicativo que torna a implantação e manutenção do aplicativo simples é que todo o conteúdo do usuário é armazenado em um conjunto de compartilhamentos UNC. Esse modelo é mapeado bem para o padrão comum de armazenamento de conteúdo usado por ambientes de hospedagem na Web locais que têm vários servidores com balanceamento de carga. 

No serviço de aplicativo, há vários compartilhamentos UNC criados em cada data center. Um percentual do conteúdo do usuário para todos os clientes em cada data center é alocado para cada compartilhamento UNC. Além disso, todo o conteúdo do arquivo para uma assinatura de um único cliente sempre é colocado no mesmo compartilhamento UNC. 

Devido à forma como os serviços do Azure funcionam, a máquina virtual específica responsável por hospedar um compartilhamento UNC mudará ao longo do tempo. É garantido que os compartilhamentos UNC serão montados por diferentes máquinas virtuais à medida que são ativadas e inativas durante o curso normal das operações do Azure. Por esse motivo, os aplicativos nunca devem tomar suposições embutidas em código que as informações do computador em um caminho de arquivo UNC permanecerão estáveis ao longo do tempo. Em vez disso, eles devem usar o caminho absoluto *falso* conveniente **D:\home\site** que o serviço de aplicativo fornece. Esse caminho absoluto falso fornece um método portátil, independente de aplicativo e de usuário para se referir a um aplicativo próprio. Usando o **D:\home\site**, é possível transferir arquivos compartilhados do aplicativo para o aplicativo sem precisar configurar um novo caminho absoluto para cada transferência.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>Tipos de acesso de arquivo concedidos a um aplicativo
A assinatura de cada cliente tem uma estrutura de diretório reservada em um compartilhamento UNC específico dentro de um data center. Um cliente pode ter vários aplicativos criados em um data center específico, portanto, todos os diretórios pertencentes a uma única assinatura de cliente são criados no mesmo compartilhamento UNC. O compartilhamento pode incluir diretórios como os de conteúdo, erros e logs de diagnóstico e versões anteriores do aplicativo criado pelo controle do código-fonte. Conforme esperado, os diretórios de aplicativo de um cliente estão disponíveis para acesso de leitura e gravação em tempo de execução pelo código do aplicativo do aplicativo.

Nas unidades locais conectadas à máquina virtual que executa um aplicativo, o serviço de aplicativo reserva uma parte do espaço em C:\ unidade para armazenamento local temporário específico do aplicativo. Embora um aplicativo tenha acesso completo de leitura/gravação ao seu próprio armazenamento local temporário, esse armazenamento não se destina a ser usado diretamente pelo código do aplicativo. Em vez disso, a intenção é fornecer armazenamento temporário de arquivos para as estruturas do aplicativo Web e do IIS. O serviço de aplicativo também limita a quantidade de armazenamento local temporário disponível para cada aplicativo para impedir que aplicativos individuais consumam quantidades excessivas de armazenamento de arquivos local.

Dois exemplos de como o serviço de aplicativo usa o armazenamento local temporário são o diretório para arquivos ASP.NET temporários e o diretório para arquivos compactados do IIS. O sistema de compilação ASP.NET usa o diretório "Temporary ASP.NET Files" como um local de cache de compilação temporário. O IIS usa o diretório "arquivos compactados temporários do IIS" para armazenar a saída de resposta compactada. Esses dois tipos de uso de arquivo (bem como outros) são remapeados no serviço de aplicativo para o armazenamento local temporário por aplicativo. Esse remapeamento garante que a funcionalidade continue conforme o esperado.

Cada aplicativo no serviço de aplicativo é executado como uma identidade de processo de trabalho de baixo privilégio aleatória exclusiva chamada "identidade do pool de aplicativos" [https://www.iis.net/learn/manage/configuring-security/application-pool-identities](https://www.iis.net/learn/manage/configuring-security/application-pool-identities), descrita mais adiante aqui:. O código do aplicativo usa essa identidade para acesso básico somente leitura à unidade do sistema operacional (o D:\ unidade). Isso significa que o código do aplicativo pode listar estruturas de diretório comuns e ler arquivos comuns na unidade do sistema operacional. Embora isso possa parecer ser um nível de acesso um pouco amplo, os mesmos diretórios e arquivos podem ser acessados quando você provisiona uma função de trabalho em um serviço hospedado do Azure e lê o conteúdo da unidade. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>Acesso a arquivos entre várias instâncias
O diretório base contém o conteúdo de um aplicativo e o código do aplicativo pode gravar nele. Se um aplicativo for executado em várias instâncias, o diretório base será compartilhado entre todas as instâncias para que todas as instâncias vejam o mesmo diretório. Portanto, por exemplo, se um aplicativo salvar arquivos carregados no diretório base, esses arquivos estarão imediatamente disponíveis para todas as instâncias. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>Acesso à rede
O código do aplicativo pode usar protocolos TCP/IP e baseados em UDP para fazer conexões de rede de saída com pontos de extremidade acessíveis pela Internet que exponham serviços externos. Os aplicativos podem usar esses mesmos protocolos para se conectarem aos serviços no Azure, por exemplo, estabelecendo conexões HTTPS com o banco de dados SQL.

Também há uma capacidade limitada para os aplicativos estabelecerem uma conexão de loopback local e fazer com que um aplicativo escute esse soquete de loopback local. Esse recurso existe principalmente para habilitar aplicativos que escutam em soquetes de loopback locais como parte de sua funcionalidade. Cada aplicativo vê uma conexão de auto-retorno "privada". O aplicativo "A" não pode escutar um soquete de loopback local estabelecido pelo aplicativo "B".

Os pipes nomeados também têm suporte como um mecanismo de comunicação entre processos (IPC) entre processos diferentes que executam coletivamente um aplicativo. Por exemplo, o módulo FastCGI do IIS depende de pipes nomeados para coordenar os processos individuais que executam páginas PHP.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>Execução de código, processos e memória
Conforme observado anteriormente, os aplicativos são executados dentro de processos de trabalho com poucos privilégios usando uma identidade de pool de aplicativos aleatória. O código do aplicativo tem acesso ao espaço de memória associado ao processo de trabalho, bem como a processos filho que podem ser gerados por processos CGI ou outros aplicativos. No entanto, um aplicativo não pode acessar a memória ou os dados de outro aplicativo, mesmo se ele estiver na mesma máquina virtual.

Os aplicativos podem executar scripts ou páginas escritas com estruturas de desenvolvimento na Web com suporte. O serviço de aplicativo não define nenhuma configuração de estrutura da Web para modos mais restritos. Por exemplo, os aplicativos ASP.NET em execução no serviço de aplicativo são executados em confiança "completa", em oposição a um modo de confiança mais restrito. As estruturas da Web, incluindo o ASP clássico e o ASP.NET, podem chamar componentes COM em processo (mas não fora do processo), como o ADO (ActiveX Data Objects) que são registrados por padrão no sistema operacional Windows.

Os aplicativos podem gerar e executar código arbitrário. É permitido que um aplicativo faça coisas como gerar um shell de comando ou executar um script do PowerShell. No entanto, embora códigos e processos arbitrários possam ser gerados de um aplicativo, programas e scripts executáveis ainda são restritos aos privilégios concedidos ao pool de aplicativos pai. Por exemplo, um aplicativo pode gerar um executável que faz uma chamada HTTP de saída, mas esse mesmo executável não pode tentar desassociar o endereço IP de uma máquina virtual de sua NIC. Fazer uma chamada de rede de saída é permitido para código com poucos privilégios, mas tentar reconfigurar as definições de rede em uma máquina virtual requer privilégios administrativos.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>Eventos e logs de diagnóstico
Informações de log são outro conjunto de dados que alguns aplicativos tentam acessar. Os tipos de informações de log disponíveis para código em execução no serviço de aplicativo incluem informações de diagnóstico e log geradas por um aplicativo que também pode ser facilmente acessado para o aplicativo. 

Por exemplo, os logs HTTP do W3C gerados por um aplicativo ativo estão disponíveis em um diretório de log no local de compartilhamento de rede criado para o aplicativo ou disponíveis no armazenamento de BLOBs se um cliente tiver configurado o log do W3C para o armazenamento. A última opção permite que grandes quantidades de logs sejam coletadas sem o risco de exceder os limites de armazenamento de arquivos associados a um compartilhamento de rede.

Em um sentido semelhante, as informações de diagnóstico em tempo real dos aplicativos .NET também podem ser registradas usando a infraestrutura de rastreamento e diagnóstico do .NET, com opções para gravar as informações de rastreamento no compartilhamento de rede do aplicativo ou como alternativa a um local de armazenamento de BLOBs.

As áreas de log e rastreamento de diagnóstico que não estão disponíveis para aplicativos são eventos ETW do Windows e logs de eventos comuns do Windows (por exemplo, sistema, aplicativo e logs de eventos de segurança). Como as informações de rastreamento ETW podem ser potencialmente visíveis em todo o computador (com as ACLs certas), o acesso de leitura e gravação aos eventos ETW são bloqueados. Os desenvolvedores podem perceber que as chamadas à API para ler e gravar eventos ETW e logs de eventos comuns do Windows parecem funcionar, mas isso ocorre porque o serviço de aplicativo é "falsificando" as chamadas para que pareçam ter sucesso. Na realidade, o código do aplicativo não tem acesso a esses dados de evento.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>Acesso ao registro
Os aplicativos têm acesso somente leitura a muito (embora não todos) do registro da máquina virtual em que estão sendo executados. Na prática, isso significa que as chaves do registro que permitem acesso somente leitura ao grupo de usuários local são acessíveis por aplicativos. Uma área do registro que atualmente não tem suporte para acesso de leitura ou gravação é o hive hKey\_Current\_User.

O acesso de gravação ao registro é bloqueado, incluindo o acesso a qualquer chave de registro por usuário. Da perspectiva do aplicativo, o acesso de gravação ao registro nunca deve ser confiado no ambiente do Azure, pois os aplicativos podem (e fazem) ser migrados entre diferentes máquinas virtuais. O único armazenamento gravável persistente que pode ser dependente por um aplicativo é a estrutura de diretório de conteúdo por aplicativo armazenada nos compartilhamentos UNC do serviço de aplicativo. 

## <a name="remote-desktop-access"></a>Acesso à área de trabalho remota

O serviço de aplicativo não fornece acesso à área de trabalho remota às instâncias de VM.

## <a name="more-information"></a>Mais informações

[Área restrita do serviço de Azure app](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) -as informações mais atualizadas sobre o ambiente de execução do serviço de aplicativo. Essa página é mantida diretamente pela equipe de desenvolvimento do serviço de aplicativo.

