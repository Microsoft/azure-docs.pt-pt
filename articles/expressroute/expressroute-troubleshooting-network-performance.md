---
title: 'Desempenho da ligação de rede de resolução de problemas: Azure'
description: Esta página fornece um método padronizado de testar o desempenho da ligação de rede Azure.
services: expressroute
author: tracsman
ms.service: expressroute
ms.topic: article
ms.date: 12/20/2017
ms.author: jonor
ms.custom: seodec18
ms.openlocfilehash: bb68919fba731caa32dcca3f4c991b8881afc6f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74869651"
---
# <a name="troubleshooting-network-performance"></a>Resolver problemas de desempenho da rede
## <a name="overview"></a>Descrição geral
O Azure fornece formas estáveis e rápidas para se ligar ao Azure a partir da rede no local. Métodos como VPN Site a Site e ExpressRoute são utilizados com êxito por clientes grandes e pequenos para a gestão dos seus negócios no Azure. Mas o que acontece quando o desempenho não corresponde às suas expectativas ou experiência anterior? Este documento pode ajudar a normalizar a forma como testa e a linha de base do seu ambiente específico.

Este documento mostra como pode testar de forma fácil e consistente a latência da rede e a largura de banda entre dois anfitriões. Este documento também fornece alguns conselhos sobre formas de olhar para a rede Azure e ajudar a isolar pontos problemáticos. O script powerShell e as ferramentas discutidas requerem dois anfitriões na rede (em cada extremidade do link que está a ser testado). Um anfitrião deve ser um Windows Server ou Desktop, o outro pode ser Windows ou Linux. 

>[!NOTE]
>A abordagem para a resolução de problemas, as ferramentas e os métodos utilizados são preferências pessoais. Este documento descreve a abordagem e as ferramentas que costumo tomar. A tua abordagem provavelmente vai diferir, não há nada de errado com diferentes abordagens para resolver problemas. No entanto, se não tiver uma abordagem estabelecida, este documento pode fazê-lo começar no caminho para construir os seus próprios métodos, ferramentas e preferências para resolver problemas de rede.
>
>

## <a name="network-components"></a>Componentes de rede
Antes de investigar mossa, vamos discutir alguns termos e componentes comuns. Esta discussão garante que estamos a pensar em cada componente na cadeia de ponta a ponta que permite a conectividade em Azure.
![1][1]

Ao mais alto nível, descrevo três grandes domínios de encaminhamento de rede;

- rede Azure (nuvem azul à direita)
- a Internet ou WAN (nuvem verde no centro)
- a Rede Corporativa (nuvem de pêssego à esquerda)

Olhando para o diagrama da direita para a esquerda, vamos discutir brevemente cada componente:
 - **Máquina Virtual** - O servidor pode ter vários NICs, garantir que quaisquer rotas estáticas, rotas predefinidas e definições do Sistema Operativo estão a enviar e a receber tráfego da forma que pensa que é. Além disso, cada VM SKU tem uma restrição de largura de banda. Se estiver a utilizar um VM SKU mais pequeno, o seu tráfego é limitado pela largura de banda disponível para o NIC. Costumo usar um DS5v2 para testar (e depois apagar uma vez feito com testes para economizar dinheiro) para garantir uma largura de banda adequada no VM.
 - **NIC** - Certifique-se de que conhece o IP privado que é atribuído ao NIC em questão.
 - **NIC NSG** - Pode haver NSGs específicos aplicados ao nível nic, certifique-se de que o conjunto de regras NSG é apropriado para o tráfego que você está tentando passar. Por exemplo, certifique-se de que as portas 5201 para iPerf, 3389 para RDP ou 22 para SSH estão abertas para permitir a passagem do tráfego de ensaios.
 - **VNet Subnet** - O NIC é atribuído a uma subnet específica, certifique-se de saber qual e as regras associadas a essa sub-rede.
 - **Subnet NSG** - Tal como o NIC, os NSGs também podem ser aplicados na sub-rede. Certifique-se de que o conjunto de regras nsg é apropriado para o tráfego que está a tentar passar. (para a entrada de tráfego no NIC, a sub-rede NSG aplica-se primeiro, depois o NIC NSG, inversamente para a saída de tráfego do VM o NIC NSG aplica-se primeiro, depois entra em jogo o NSG subnet).
 - **Subnet UDR** - As rotas definidas pelo utilizador podem direcionar o tráfego para um lúpulo intermédio (como uma firewall ou um equilibrador de carga). Certifique-se de que sabe se existe um UDR no local para o seu tráfego e, em caso afirmativo, para onde vai e o que o próximo salto fará ao seu tráfego. (por exemplo, uma firewall poderia passar algum tráfego e negar outros tráfegos entre os mesmos dois anfitriões).
 - **Sub-rede gateway / NSG / UDR** - Tal como a sub-rede VM, a sub-rede gateway pode ter NSGs e UDRs. Certifique-se de saber se estão lá e que efeitos eles têm no seu tráfego.
 - **VNet Gateway (ExpressRoute)** - Uma vez ativado o peering (ExpressRoute) ou VPN, não existem muitas definições que possam afetar como ou se as rotas de tráfego. Se tiver vários circuitos ExpressRoute ou túneis VPN ligados ao mesmo VNet Gateway, deve estar ciente das definições de peso de ligação, uma vez que esta definição afeta a preferência de ligação e afeta o caminho que o seu tráfego toma.
 - **Filtro de rota** (Não mostrado) - Um filtro de rota só se aplica ao Microsoft Peering no ExpressRoute, mas é fundamental verificar se não está a ver as rotas que espera no Microsoft Peering. 

Neste momento, estás na parte WAN do link. Este domínio de encaminhamento pode ser o seu prestador de serviços, o seu WAN corporativo, ou a Internet. Muitos lúpulos, tecnologias e empresas envolvidas com estas ligações podem dificultar um pouco a resolução de problemas. Muitas vezes, trabalha para excluir tanto o Azure como as suas Redes Corporativas antes de entrar nesta coleção de empresas e lúpulo.

No diagrama anterior, na extrema esquerda está a sua rede corporativa. Dependendo do tamanho da sua empresa, este domínio de encaminhamento pode ser alguns dispositivos de rede entre si e o WAN ou várias camadas de dispositivos numa rede campus/empresa.

Dadas as complexidades destes três diferentes ambientes de rede de alto nível, é muitas vezes ideal começar pelas bordas e tentar mostrar onde o desempenho é bom, e onde se degrada. Esta abordagem pode ajudar a identificar o domínio de encaminhamento de problemas dos três e, em seguida, concentrar a sua resolução de problemas nesse ambiente específico.

## <a name="tools"></a>Ferramentas
A maioria dos problemas de rede podem ser analisados e isolados usando ferramentas básicas como ping e traceroute. É raro que precises de ir tão fundo como uma análise de pacotes como o Wireshark. Para ajudar na resolução de problemas, o Azure Connectivity Toolkit (AzureCT) foi desenvolvido para colocar algumas destas ferramentas num pacote fácil. Para testes de desempenho, gosto de usar iPerf e PSPing. IPerf é uma ferramenta comumente usada e funciona na maioria dos sistemas operativos. IPerf é bom para testes básicos de desempenho e é bastante fácil de usar. PSPing é uma ferramenta de ping desenvolvida pela SysInternals. PsPing é uma maneira fácil de executar pings ICMP e TCP em um também fácil de usar comando. Ambas as ferramentas são leves e são "instaladas" simplesmente fazendo com que os ficheiros sejam de um diretório no hospedeiro.

Envolvi todas estas ferramentas e métodos num módulo PowerShell (AzureCT) que pode instalar e utilizar.

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT - o Kit de Ferramentas de Conectividade Azure
O módulo PowerShell AzureCT tem dois componentes [Teste de disponibilidade][Availability Doc] e teste de [desempenho.][Performance Doc] Este documento está apenas relacionado com os testes de Desempenho, por isso vamos focar-nos nos dois comandos de Desempenho de Ligação neste módulo PowerShell.

Existem três passos básicos para utilizar este kit de ferramentas para testes de desempenho. 1) Instale o módulo PowerShell, 2) Instale as aplicações de suporte iPerf e PSPing 3) Executar o teste de desempenho.

1. Instalação do Módulo PowerShell

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    Este comando descarrega o módulo PowerShell e instala-o localmente.

2. Instalar as aplicações de suporte
    ```powershell
    Install-LinkPerformance
    ```
    Este comando AzureCT instala o iPerf e o PSPing num novo diretório "C:\ACTTools", que também abre as portas Windows Firewall para permitir o tráfego iCMP e porta 5201 (iPerf).

3. Executar o teste de desempenho

    Em primeiro lugar, no hospedeiro remoto tem de instalar e executar iPerf no modo de servidor. Certifique-se também de que o hospedeiro remoto está a ouvir o 3389 (RDP para Windows) ou o 22 (SSH para o Linux) e a permitir o tráfego na porta 5201 para iPerf. Se o hospedeiro remoto for windows, instale o AzureCT e execute o comando Instalação-LinkPerformance para configurar o iPerf e as regras de firewall necessárias para iniciar o iPerf no modo servidor com sucesso. 
    
    Assim que a máquina remota estiver pronta, abra a PowerShell na máquina local e inicie o teste:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    Este comando executa uma série de testes simultâneos de carga e latência para ajudar a estimar a capacidade de largura de banda e a latência da sua ligação de rede.

4. Reveja a produção dos testes

    O formato de saída PowerShell é semelhante a:

    ![4][4]

    Os resultados detalhados de todos os testes iPerf e PSPing estão em ficheiros de texto individuais no diretório de ferramentas AzureCT em "C:\ACTTools".

## <a name="troubleshooting"></a>Resolução de problemas
Se o teste de desempenho não lhe está a dar resultados esperados, descubra por que deve ser um processo passo a passo progressivo. Dado o número de componentes no caminho, uma abordagem sistemática geralmente fornece um caminho mais rápido para a resolução do que saltar e potencialmente fazer o mesmo teste várias vezes.

>[!NOTE]
>O cenário aqui é uma questão de desempenho, não uma questão de conectividade. Os passos seriam diferentes se o tráfego não passasse.
>
>

Primeiro, desafie as suas suposições. A sua expectativa é razoável? Por exemplo, se tiver um circuito ExpressRoute de 1-Gbps e 100 ms de latência, não é razoável esperar os 1 Gbps completos de tráfego dadas as características de desempenho do TCP sobre ligações de latência elevadas. Consulte a [secção Referências](#references) para mais informações sobre as suposições de desempenho.

Em seguida, recomendo começar pelas bordas entre os domínios de encaminhamento e tentar isolar o problema a um único domínio de encaminhamento; a Rede Corporativa, a WAN ou a Rede Azure. Muitas vezes as pessoas culpam a "caixa negra" no caminho, enquanto culpar a caixa preta é fácil de fazer, podendo atrasar significativamente a resolução, especialmente se o problema estiver realmente numa área em que você tem a capacidade de fazer alterações. Certifique-se de que faz as suas diligências antes de entregar ao seu prestador de serviços ou ao ISP.

Uma vez identificado o principal domínio de encaminhamento que parece conter o problema, deve criar um diagrama da área em questão. Seja num quadro, bloco de notas ou visio como diagrama fornece um "mapa de batalha" concreto para permitir uma abordagem metódica para isolar ainda mais o problema. Pode planear pontos de teste e atualizar o mapa à medida que limpa áreas ou cava mais fundo à medida que os testes progridem.

Agora que tem um diagrama, comece a dividir a rede em segmentos e reduza o problema. Descubra onde funciona e onde não funciona. Continue a mover os seus pontos de teste para isolar até ao componente ofensivo.

Além disso, não se esqueça de olhar para outras camadas do modelo OSI. É fácil focar-se na rede e nas camadas 1 - 3 (camadas físicas, de dados e rede), mas os problemas também podem estar na Camada 7 na camada de aplicação. Mantenha a mente aberta e verifique as suposições.

## <a name="advanced-expressroute-troubleshooting"></a>Resolução avançada de problemas da ExpressRoute
Se não tem certeza de onde está realmente a borda da nuvem, isolar os componentes Azure pode ser um desafio. Quando o ExpressRoute é utilizado, a borda é um componente de rede chamado Microsoft Enterprise Edge (MSEE). Ao utilizar o **ExpressRoute,** o MSEE é o primeiro ponto de contacto na rede da Microsoft e o último salto a sair da rede da Microsoft. Quando cria um objeto de ligação entre o gateway VNet e o circuito ExpressRoute, está realmente a fazer uma ligação ao MSEE. Reconhecer o MSEE como o primeiro ou último salto (dependendo do rumo que vai) é crucial para isolar os problemas da Rede Azure para provar que o problema está em Azure ou mais a jusante na WAN ou na Rede Corporativa. 

![2][2]

>[!NOTE]
> Note que o MSEE não está na nuvem azure. O ExpressRoute está, na verdade, no limite da rede da Microsoft que não está realmente no Azure. Uma vez ligado ao ExpressRoute a um MSEE, está ligado à rede da Microsoft, a partir daí pode ir a qualquer um dos serviços na nuvem, como o Office 365 (com o Microsoft Peering) ou o Azure (com Private e/ou Microsoft Peering).
>
>

Se dois VNets (VNets A e B no diagrama) estiverem ligados ao **mesmo** circuito ExpressRoute, pode realizar uma série de testes para isolar o problema em Azure (ou provar que não está em Azure)
 
### <a name="test-plan"></a>Plano de teste
1. Faça o teste Get-LinkPerformance entre VM1 e VM2. Este teste fornece uma visão para se o problema é local ou não. Se este teste produzir resultados aceitáveis de latência e largura de banda, pode marcar a rede VNet local como boa.
2. Assumindo que o tráfego vnet local é bom, faça o teste Get-LinkPerformance entre VM1 e VM3. Este teste exercita a ligação através da rede Microsoft até ao MSEE e volta ao Azure. Se este teste produzir resultados aceitáveis de latência e largura de banda, pode marcar a rede Azure como boa.
3. Se o Azure for excluído, pode realizar uma sequência semelhante de testes na sua Rede Corporativa. Se isso também testar bem, é hora de trabalhar com o seu prestador de serviços ou ISP para diagnosticar a sua ligação WAN. Exemplo: Execute este teste entre duas agências, ou entre a sua secretária e um servidor de data center. Dependendo do que está a testar, encontre pontos finais (servidores, computadores, etc.) que possam exercer esse caminho.

>[!IMPORTANT]
> É fundamental que para cada teste marque a hora do dia e registe os resultados numa localização comum (gosto do OneNote ou do Excel). Cada ensaio deve ter uma saída idêntica para que possa comparar os dados resultantes através de ensaios e não ter "buracos" nos dados. Consistência em vários testes é a principal razão pela qual uso o AzureCT para resolução de problemas. A magia não está nos cenários exatos de carga que corro, mas em vez disso, a *magia* é o facto de eu ter um *teste consistente e saída* de dados de cada teste. Registar o tempo e ter dados consistentes cada vez é especialmente útil se mais tarde descobrir que o problema é esporádico. Seja diligente com a sua recolha de dados na frente e evitará horas de retestar os mesmos cenários (aprendi isto de forma difícil há muitos anos).
>
>

## <a name="the-problem-is-isolated-now-what"></a>O problema está isolado, e agora?
Quanto mais se pode isolar o problema, mais fácil é corrigir, por mais vezes que se chega ao ponto em que não se pode ir mais fundo ou mais longe com a sua resolução de problemas. Exemplo: você vê a ligação através do seu prestador de serviços tomando lúpulo pela Europa, mas o seu caminho esperado é tudo na Ásia. Este ponto é quando deve pedir ajuda. Quem você pergunta é dependente do domínio de encaminhamento para o qual isolou a questão, ou ainda melhor se você é capaz de reduzi-lo a um componente específico.

Para problemas de rede corporativa, o seu departamento de TI ou prestador de serviços interno que suporte a sua rede (que pode ser o fabricante de hardware) poderá ajudar na configuração do dispositivo ou na reparação de hardware.

Para o WAN, partilhar os resultados dos seus testes com o seu Fornecedor de Serviços ou ISP pode ajudar a iniciá-los e evitar cobrir alguns dos mesmos terrenos que já testou. No entanto, não se ofenda se quiserem verificar os seus resultados. "Confiança mas verificar" é um bom lema quando se resolução de problemas com base nos resultados reportados de outras pessoas.

Com o Azure, uma vez que isola o problema com o máximo de detalhes possível, é hora de rever a [Documentação da Rede Azure][Network Docs] e, em seguida, se ainda precisar [abrir um bilhete][Ticket Link]de apoio .

## <a name="references"></a>Referências
### <a name="latencybandwidth-expectations"></a>Expectativas de latência/largura de banda
>[!TIP]
> Latência geográfica (milhas ou quilómetros) entre os pontos finais que está a testar é de longe o maior componente da latência. Embora exista latência de equipamentos (componentes físicos e virtuais, número de lúpulo, etc.) envolvidos, a geografia provou ser o maior componente da latência geral ao lidar com ligações WAN. Também é importante notar que a distância é a distância da fibra correr não a distância de linha reta ou de mapa da estrada. Esta distância é incrivelmente difícil de obter com precisão. Como resultado, uso geralmente uma calculadora de distância urbana na internet e sei que este método é uma medida grosseiramente imprecisa, mas é suficiente para definir uma expectativa geral.
>
>

Tenho uma instalação da ExpressRoute em Seattle, Washington, nos EUA. A tabela que se segue mostra a latência e largura de banda que vi a testar em vários locais do Azure. Estimei a distância geográfica entre cada extremidade do teste.

Configuração do teste:
 - Um servidor físico que executa o Windows Server 2016 com um NIC de 10 Gbps, ligado a um circuito ExpressRoute.
 - Um circuito De 10Gbps Premium ExpressRoute no local identificado com Private Peering habilitado.
 - Um Azure VNet com uma porta de entrada UltraPerformance na região especificada.
 - Um VM DS5v2 a executar o Windows Server 2016 na VNet. O VM não foi integrado, construído a partir da imagem padrão do Azure (sem otimização ou personalização) com o AzureCT instalado.
 - Todos os testes foram utilizados pelo comando AzureCT Get-LinkPerformance com um teste de carga de 5 minutos para cada um dos seis ensaios. Por exemplo:

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - O fluxo de dados para cada teste tinha a carga fluindo do servidor físico no local (cliente iPerf em Seattle) até ao Azure VM (servidor iPerf na região azure listada).
 - Os dados da coluna "Latência" são do teste No Load (um teste de latência TCP sem execução iPerf).
 - Os dados da coluna "Max Bandwidth" são do teste de carga de fluxo de 16 TCP com um tamanho de janela de 1-Mb.

![3][3]

### <a name="latencybandwidth-results"></a>Resultados da latência/largura de banda
>[!IMPORTANT]
> Estes números são apenas para referência geral. Muitos fatores afetam a latência, e embora estes valores sejam geralmente consistentes ao longo do tempo, as condições dentro do Azure ou da rede de Prestadores de Serviços podem enviar tráfego através de caminhos diferentes a qualquer momento, pelo que a latência e a largura de banda podem ser afetadas. Geralmente, os efeitos destas alterações não resultam em diferenças significativas.
>
>

| | | | | | |
|-|-|-|-|-|-|
|ExpressRoute<br/>Localização|Azure<br/>Região|Estimado<br/>Distância (km)|Latência|1 Sessão<br/>Largura de banda|Máximo<br/>Largura de banda|
| Seattle | E.U.A.Oeste 2        |    191 km |   5 ms | 262.0 Mbits/seg |  3.74 Gbits/seg |
| Seattle | E.U.A. Oeste          |  1.094 km |  18 ms |  82.3 Mbits/seg |  3.70 Gbits/seg |
| Seattle | E.U.A. Central       |  2.357 km |  40 ms |  38.8 Mbits/seg |  2.55 Gbits/seg |
| Seattle | E.U.A. Centro-Sul |  2.877 km |  51 ms |  30,6 Mbits/seg |  2.49 Gbits/seg |
| Seattle | E.U.A. Centro-Norte |  2.792 km |  55 ms |  27.7 Mbits/seg |  2.19 Gbits/seg |
| Seattle | E.U.A. Leste 2        |  3.769 km |  73 ms |  21.3 Mbits/seg |  1.79 Gbits/seg |
| Seattle | E.U.A. Leste          |  3.699 km |  74 ms |  21.1 Mbits/seg |  1.78 Gbits/seg |
| Seattle | Leste do Japão       |  7.705 km | 106 ms |  14,6 Mbits/seg |  1.22 Gbits/seg |
| Seattle | Sul do Reino Unido         |  7.708 km | 146 ms |  10,6 Mbits/seg |   896 Mbits/seg |
| Seattle | Europa ocidental      |  7.834 km | 153 ms |  10.2 Mbits/seg |   761 Mbits/seg |
| Seattle | Leste da Austrália   | 12.484 km | 165 ms |   9.4 Mbits/seg |   794 Mbits/seg |
| Seattle | Ásia Sudeste   | 12.989 km | 170 ms |   9.2 Mbits/seg |   756 Mbits/seg |
| Seattle | Brasil Sul *   | 10.930 km | 189 ms |   8.2 Mbits/seg |   699 Mbits/seg |
| Seattle | Sul da Índia      | 12.918 km | 202 ms |   7.7 Mbits/seg |   634 Mbits/seg |

\*A latência para o Brasil é um bom exemplo onde a distância em linha reta difere significativamente da distância de corrida de fibra. Eu esperaria que a latência fosse na vizinhança de 160 ms, mas na verdade são 189 ms. Esta diferença face à minha expectativa pode indicar um problema de rede em algum lugar, mas o mais provável é que a corrida de fibra não vá para o Brasil em linha reta e tenha mais 1.000 km de viagem para chegar ao Brasil a partir de Seattle.

## <a name="next-steps"></a>Passos seguintes
1. Descarregue o Kit de Ferramentas de Conectividade Azure do GitHub em[https://aka.ms/AzCT][ACT]
2. Siga as instruções para o teste de desempenho da [ligação][Performance Doc]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "Componentes da rede Azure"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "Resolução de problemas expressRoute"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "Perf Test Environment"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "Saída PowerShell"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: https://docs.microsoft.com/azure/index
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: https://aka.ms/AzCT
