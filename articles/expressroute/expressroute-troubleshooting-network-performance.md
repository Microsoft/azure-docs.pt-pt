---
title: 'Solucionar problemas de desempenho de link de rede: Azure'
description: Esta página fornece um método padronizado de teste do desempenho de link de rede do Azure.
services: expressroute
author: tracsman
ms.service: expressroute
ms.topic: article
ms.date: 12/20/2017
ms.author: jonor
ms.custom: seodec18
ms.openlocfilehash: bb68919fba731caa32dcca3f4c991b8881afc6f9
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74869651"
---
# <a name="troubleshooting-network-performance"></a>Solucionando problemas de desempenho de rede
## <a name="overview"></a>Visão geral
O Azure fornece formas estáveis e rápidas para se ligar ao Azure a partir da rede no local. Métodos como VPN Site a Site e ExpressRoute são utilizados com êxito por clientes grandes e pequenos para a gestão dos seus negócios no Azure. Mas o que acontece quando o desempenho não atende à sua expectativa ou experiência anterior? Este documento pode ajudar a padronizar a maneira como você testa e linha de base de seu ambiente específico.

Este documento mostra como você pode testar a latência e a largura de banda de rede de forma fácil e consistente entre dois hosts. Este documento também fornece conselhos sobre maneiras de examinar a rede do Azure e ajudar a isolar pontos de problema. O script do PowerShell e as ferramentas abordadas exigem dois hosts na rede (em qualquer extremidade do link que está sendo testado). Um host deve ser um Windows Server ou uma área de trabalho, o outro pode ser o Windows ou o Linux. 

>[!NOTE]
>A abordagem para solução de problemas, as ferramentas e os métodos usados são as preferências pessoais. Este documento descreve a abordagem e as ferramentas que costumo usar. Sua abordagem provavelmente será diferente, não há nada de errado com abordagens diferentes para a solução de problemas. No entanto, se você não tiver uma abordagem estabelecida, este documento poderá começar a usar o caminho para criar seus próprios métodos, ferramentas e preferências para solucionar problemas de rede.
>
>

## <a name="network-components"></a>Componentes de rede
Antes de se aprofundar na solução de problemas, vamos discutir alguns termos e componentes comuns. Essa discussão garante que estamos pensando em cada componente na cadeia de ponta a ponta que permite a conectividade no Azure.
![1][1]

No nível mais alto, descrevo três principais domínios de roteamento de rede;

- a rede do Azure (nuvem azul à direita)
- Internet ou WAN (nuvem verde no centro)
- a rede corporativa (nuvem pêssego à esquerda)

Olhando o diagrama da direita para a esquerda, vamos discutir brevemente cada componente:
 - **Máquina virtual** -o servidor pode ter várias NICs, garantir que rotas estáticas, rotas padrão e configurações do sistema operacional estejam enviando e recebendo tráfego da maneira que você imagina. Além disso, cada SKU da VM tem uma restrição de largura de banda. Se você estiver usando uma SKU de VM menor, o tráfego será limitado pela largura de banda disponível para a NIC. Eu geralmente uso um DS5v2 para teste (e, em seguida, exclua uma vez com o teste para economizar dinheiro) para garantir a largura de banda adequada na VM.
 - **NIC** – Verifique se você conhece o IP privado atribuído à NIC em questão.
 - **NIC NSG** -pode haver um NSGs específico aplicado no nível de NIC, verifique se o conjunto de regras NSG é apropriado para o tráfego que você está tentando passar. Por exemplo, verifique se as portas 5201 para iPerf, 3389 para RDP ou 22 para SSH estão abertas para permitir que o tráfego de teste passe.
 - **Sub-rede VNet** -a NIC é atribuída a uma sub-rede específica, verifique se você sabe qual delas e as regras associadas a essa sub-rede.
 - **Sub-rede NSG** -assim como a NIC, NSGs também pode ser aplicada na sub-rede. Verifique se o conjunto de regras NSG é apropriado para o tráfego que você está tentando passar. (para o tráfego de entrada para a NIC, a sub-rede NSG aplica-se primeiro e, em seguida, a NIC NSG, por outro lado, para o tráfego de saída da VM a NIC NSG é aplicada primeiro, a sub-rede NSG entra em jogo).
 - **Sub-rede UDR** -as rotas definidas pelo usuário podem direcionar o tráfego para um salto intermediário (como um firewall ou balanceador de carga). Certifique-se de que você sabe se há um UDR em vigor para seu tráfego e se, para onde ele vai e o que o próximo salto fará em seu tráfego. (por exemplo, um firewall pode passar algum tráfego e negar outro tráfego entre os mesmos dois hosts).
 - **Sub-rede de gateway/NSG/UDR** -assim como a sub-rede VM, a sub-rede de gateway pode ter NSGs e UDRs. Certifique-se de que você sabe se eles estão lá e quais efeitos eles têm sobre o tráfego.
 - **Gateway de VNet (expressroute)** – uma vez que o emparelhamento (expressroute) ou VPN está habilitado, não há muitas configurações que podem afetar como ou se as rotas de tráfego. Se você tiver vários circuitos de ExpressRoute ou túneis de VPN conectados ao mesmo gateway de VNet, você deve estar ciente das configurações de peso de conexão, pois essa configuração afeta a preferência de conexão e afeta o caminho que o tráfego leva.
 - **Filtro de rota** (não mostrado)-um filtro de rota só se aplica ao emparelhamento da Microsoft no ExpressRoute, mas é essencial para verificar se você não está vendo as rotas esperadas no emparelhamento da Microsoft. 

Neste ponto, você está na parte da WAN do link. Esse domínio de roteamento pode ser seu provedor de serviços, sua WAN corporativa ou a Internet. Muitos saltos, tecnologias e empresas envolvidas com esses links podem dificultar um pouco a solução de problemas. Muitas vezes, você trabalha para solucionar o Azure e suas redes corporativas primeiro antes de entrar nessa coleção de empresas e saltos.

No diagrama anterior, na extrema esquerda está sua rede corporativa. Dependendo do tamanho da sua empresa, esse domínio de roteamento pode ser de alguns dispositivos de rede entre você e a WAN ou várias camadas de dispositivos em uma rede de campus/empresa.

Considerando as complexidades desses três diferentes ambientes de rede de alto nível, geralmente é ideal começar pelas bordas e tentar mostrar onde o desempenho é bom e onde ele degrada. Essa abordagem pode ajudar a identificar o domínio de roteamento de problemas dos três e, em seguida, concentrar sua solução de problemas nesse ambiente específico.

## <a name="tools"></a>Ferramentas
A maioria dos problemas de rede pode ser analisada e isolada usando ferramentas básicas como ping e traceroute. É raro que você precise se aprofundar como uma análise de pacotes como o Wireshark. Para ajudar na solução de problemas, o kit de ferramentas de conectividade do Azure (AzureCT) foi desenvolvido para colocar algumas dessas ferramentas em um pacote fácil. Para testes de desempenho, gosto de usar iPerf e PSPing. o iPerf é uma ferramenta comumente usada e é executado na maioria dos sistemas operacionais. o iPerf é bom para testes básicos de desempenho e é razoavelmente fácil de usar. PSPing é uma ferramenta de ping desenvolvida pela SysInternals. O PSPing é uma maneira fácil de executar pings de ICMP e TCP em um comando também fácil de usar. Ambas as ferramentas são leves e são "instaladas" simplesmente por meio da coping dos arquivos em um diretório no host.

Encapsula todas essas ferramentas e métodos em um módulo do PowerShell (AzureCT) que você pode instalar e usar.

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT – o kit de ferramentas de conectividade do Azure
O módulo AzureCT do PowerShell tem dois componentes de teste de [disponibilidade][Availability Doc] e teste de [desempenho][Performance Doc]. Este documento está preocupado apenas com o teste de desempenho, portanto, vamos nos concentrar nos dois comandos de desempenho de link neste módulo do PowerShell.

Há três etapas básicas para usar esse kit de ferramentas para testes de desempenho. 1) instalar o módulo do PowerShell, 2) instalar os aplicativos de suporte iPerf e PSPing 3) execute o teste de desempenho.

1. Instalando o módulo do PowerShell

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    Esse comando baixa o módulo do PowerShell e o instala localmente.

2. Instalar os aplicativos de suporte
    ```powershell
    Install-LinkPerformance
    ```
    Esse comando AzureCT instala iPerf e PSPing em um novo diretório "C:\ACTTools", ele também abre as portas do firewall do Windows para permitir o tráfego ICMP e porta 5201 (iPerf).

3. Executar o teste de desempenho

    Primeiro, no host remoto, você deve instalar e executar o iPerf no modo de servidor. Verifique também se o host remoto está escutando em 3389 (RDP para Windows) ou 22 (SSH para Linux) e permitindo o tráfego na porta 5201 para iPerf. Se o host remoto for Windows, instale o AzureCT e execute o comando install-LinkPerformance para configurar o iPerf e as regras de firewall necessárias para iniciar o iPerf no modo de servidor com êxito. 
    
    Quando o computador remoto estiver pronto, abra o PowerShell no computador local e inicie o teste:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    Esse comando executa uma série de testes simultâneos de carga e latência para ajudar a estimar a capacidade de largura de banda e a latência de seu link de rede.

4. Examinar a saída dos testes

    O formato de saída do PowerShell é semelhante a:

    ![4][4]

    Os resultados detalhados de todos os testes iPerf e PSPing estão em arquivos de texto individuais no diretório AzureCT Tools em "C:\ACTTools."

## <a name="troubleshooting"></a>Resolução de problemas
Se o teste de desempenho não estiver fornecendo resultados esperados, descobrir o motivo deve ser um processo passo a passo progressivo. Considerando o número de componentes no caminho, uma abordagem sistemática geralmente fornece um caminho mais rápido para resolução do que saltar e, potencialmente, fazer o mesmo teste de forma desnecessária várias vezes.

>[!NOTE]
>O cenário aqui é um problema de desempenho, não um problema de conectividade. As etapas seriam diferentes se o tráfego não estivesse passando.
>
>

Primeiro, desafiar suas suposições. Sua expectativa é razoável? Por exemplo, se você tiver um circuito de ExpressRoute de 1 Gbps e 100 ms de latência, é inaceitável esperar o total de 1 Gbps de tráfego, considerando as características de desempenho do TCP em links de alta latência. Consulte a [seção referências](#references) para obter mais informações sobre suposições de desempenho.

Em seguida, recomendo iniciar nas bordas entre os domínios de roteamento e tentar isolar o problema em um único domínio de roteamento principal; a rede corporativa, a WAN ou a rede do Azure. As pessoas geralmente culpam a "caixa preta" no caminho, enquanto culpando a caixa preta é fácil de fazer, ela pode atrasar significativamente a resolução especialmente se o problema estiver na verdade em uma área em que você tenha a capacidade de fazer alterações. Certifique-se de fazer sua auditoria detalhada antes de entregar ao provedor de serviços ou ISP.

Depois de identificar o domínio de roteamento principal que parece conter o problema, você deve criar um diagrama da área em questão. Em um quadro de comunicações, o bloco de notas ou o Visio como um diagrama fornece um "mapa de batalha" concreto para permitir uma abordagem unificada para isolar ainda mais o problema. Você pode planejar pontos de teste e atualizar o mapa ao limpar áreas ou se aprofundar conforme o andamento do teste.

Agora que você tem um diagrama, comece a dividir a rede em segmentos e restringir o problema. Descubra onde ele funciona e onde não. Continue movendo seus pontos de teste para isolar o componente incorreto.

Além disso, não se esqueça de examinar outras camadas do modelo OSI. É fácil concentrar-se na rede e nas camadas 1-3 (camadas físicas, de dados e de rede), mas os problemas também podem ser ativados na camada 7 na camada de aplicativo. Mantenha uma mente aberta e verifique as suposições.

## <a name="advanced-expressroute-troubleshooting"></a>Solução de problemas avançada do ExpressRoute
Se você não tiver certeza de onde a borda da nuvem realmente está, isolar os componentes do Azure pode ser um desafio. Quando o ExpressRoute é usado, a borda é um componente de rede chamado Microsoft Enterprise Edge (MSEE). **Ao usar o ExpressRoute**, o MSEE é o primeiro ponto de contato na rede da Microsoft e o último salto saindo da rede da Microsoft. Quando você cria um objeto de conexão entre o gateway de VNet e o circuito de ExpressRoute, você está, na verdade, fazendo uma conexão com o MSEE. Reconhecer o MSEE como o primeiro ou último salto (dependendo de qual direção você está indo) é crucial para isolar problemas de rede do Azure para provar que o problema está no Azure ou mais downstream na WAN ou na rede corporativa. 

![2][2]

>[!NOTE]
> Observe que o MSEE não está na nuvem do Azure. Na verdade, o ExpressRoute está na borda da rede da Microsoft, na verdade, não no Azure. Quando estiver conectado com o ExpressRoute a um MSEE, você estará conectado à rede da Microsoft, daí você poderá ir para qualquer um dos serviços de nuvem, como o Office 365 (com o emparelhamento da Microsoft) ou o Azure (com emparelhamento privado e/ou da Microsoft).
>
>

Se dois VNets (VNets A e B no diagrama) estiverem conectados ao **mesmo** circuito de ExpressRoute, você poderá executar uma série de testes para isolar o problema no Azure (ou provar que ele não está no Azure)
 
### <a name="test-plan"></a>Plano de teste
1. Execute o teste Get-LinkPerformance entre VM1 e VM2. Esse teste fornece informações sobre se o problema é local ou não. Se esse teste produzir resultados aceitáveis de latência e largura de banda, você poderá marcar a rede VNet local como boa.
2. Supondo que o tráfego de VNet local seja bom, execute o teste Get-LinkPerformance entre VM1 e VM3. Esse teste exercita a conexão por meio da rede da Microsoft para o MSEE e de volta para o Azure. Se esse teste produzir resultados aceitáveis de latência e largura de banda, você poderá marcar a rede do Azure como boa.
3. Se o Azure estiver descartando, você poderá executar uma sequência semelhante de testes em sua rede corporativa. Se isso também testar bem, é hora de trabalhar com seu provedor de serviços ou ISP para diagnosticar sua conexão WAN. Exemplo: Execute este teste entre duas filiais ou entre sua mesa e um servidor data center. Dependendo do que você está testando, encontre pontos de extremidade (servidores, computadores, etc.) que podem exercer esse caminho.

>[!IMPORTANT]
> É essencial que, para cada teste, você marque a hora do dia em que executa o teste e registre os resultados em um local comum (eu gosto do OneNote ou do Excel). Cada execução de teste deve ter uma saída idêntica para que você possa comparar os dados resultantes nas execuções de teste e não ter "buracos" nos dados. A consistência entre vários testes é o principal motivo pelo qual uso o AzureCT para solução de problemas. A mágica não está nos cenários de carga exatos que executo, mas, em vez disso, a *mágica* é o fato de que obtenho um *teste consistente e uma saída de dados* de cada teste. Registrar o tempo e ter dados consistentes a cada vez é especialmente útil se você descobrir mais tarde que o problema é esporádico. Fique à frente com sua coleta de dados antecipada e você evitará horas de teste dos mesmos cenários (aprendi isso de forma difícil há muitos anos).
>
>

## <a name="the-problem-is-isolated-now-what"></a>O problema está isolado, agora?
Quanto mais você puder isolar o problema, mais fácil será corrigir, no entanto, geralmente você chega ao ponto em que não é possível aprofundar ou além da solução de problemas. Exemplo: você vê o link em seu provedor de serviços levando saltos por meio da Europa, mas o caminho esperado está todos na Ásia. Esse ponto é quando você deve entrar em contato com a ajuda. Quem você pergunta é dependente do domínio de roteamento para o qual você isola o problema, ou ainda melhor se for capaz de limitá-lo a um componente específico.

Para problemas de rede corporativa, seu departamento de ti interno ou provedor de serviços que dá suporte à sua rede (que pode ser o fabricante de hardware) pode ajudar com a configuração do dispositivo ou o reparo de hardware.

Para a WAN, compartilhar seus resultados de teste com seu provedor de serviços ou ISP pode ajudar a iniciá-los e evitar a cobertura de algumas das mesmas bases já testadas. No entanto, não seja incomodados se desejarem verificar seus próprios resultados. "Confiar, mas verificar" é um bom lema ao solucionar problemas com base nos resultados relatados por outras pessoas.

Com o Azure, depois de isolar o problema em tantos detalhes quanto for possível, é hora de examinar a [documentação da rede do Azure][Network Docs] e, se ainda for necessário, [abrir um tíquete de suporte][Ticket Link].

## <a name="references"></a>Referências
### <a name="latencybandwidth-expectations"></a>Expectativas de latência/largura de banda
>[!TIP]
> A latência geográfica (milhas ou quilômetros) entre os pontos de extremidade que você está testando é, de longe, o maior componente da latência. Embora haja latência de equipamentos (componentes físicos e virtuais, número de saltos, etc.) envolvidos, a geografia provou ser o maior componente da latência geral ao lidar com conexões WAN. Também é importante observar que a distância é a distância da execução da fibra não é a distância da linha reta ou do mapa de estrada. Essa distância é incrivelmente difícil de obter com qualquer precisão. Como resultado, eu geralmente uso uma calculadora de distância da cidade na Internet e sei que esse método é uma medida grosseiramente imprecisa, mas é suficiente para definir uma expectativa geral.
>
>

Tenho uma configuração do ExpressRoute em Seattle, Washington nos EUA. A tabela a seguir mostra a latência e a largura de banda que vi testes em vários locais do Azure. Eu estimou a distância geográfica entre cada extremidade do teste.

Configuração de teste:
 - Um servidor físico executando o Windows Server 2016 com uma NIC de 10 Gbps, conectado a um circuito do ExpressRoute.
 - Um circuito do ExpressRoute 10 Gbps Premium no local identificado com o emparelhamento privado habilitado.
 - Uma VNet do Azure com um gateway UltraPerformance na região especificada.
 - Uma VM DS5v2 que executa o Windows Server 2016 na VNet. A VM não ingressou no domínio, criada a partir da imagem padrão do Azure (sem otimização ou personalização) com o AzureCT instalado.
 - Todos os testes estavam usando o comando Get-LinkPerformance do AzureCT com um teste de carga de 5 minutos para cada uma das seis execuções de teste. Por exemplo:

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - O fluxo de dados para cada teste tinha a carga fluindo do servidor físico local (iPerf Client em Seattle) até a VM do Azure (servidor iPerf na região do Azure listada).
 - Os dados da coluna "latência" são do teste sem carga (um teste de latência TCP sem iPerf em execução).
 - Os dados da coluna "largura de banda máxima" são do 16 teste de carga de fluxo de TCP com um tamanho de janela de 1 MB.

![3][3]

### <a name="latencybandwidth-results"></a>Latência/resultados de largura de banda
>[!IMPORTANT]
> Esses números são apenas para referência geral. Muitos fatores afetam a latência e, embora esses valores sejam geralmente consistentes ao longo do tempo, as condições no Azure ou na rede de provedores de serviço podem enviar tráfego por meio de caminhos diferentes a qualquer momento, portanto, a latência e a largura de banda podem ser afetadas. Em geral, os efeitos dessas alterações não resultam em diferenças significativas.
>
>

| | | | | | |
|-|-|-|-|-|-|
|ExpressRoute<br/>Localização|Azure<br/>Região|Previsto<br/>Distância (km)|Latência|1 sessão<br/>Largura de Banda|Máximo<br/>Largura de Banda|
| Seattle | E.U.A. Oeste 2        |    191 km |   5 ms | 262,0 Mbits/s |  3,74 Gbits/s |
| Seattle | Oeste dos E.U.A.          |  1\.094 km |  18 ms |  82,3 Mbits/s |  3,70 Gbits/s |
| Seattle | Centro dos E.U.A.       |  2\.357 km |  40 ms |  38,8 Mbits/s |  2,55 Gbits/s |
| Seattle | E.U.A. Centro-Sul |  2\.877 km |  51 ms |  30,6 Mbits/s |  2,49 Gbits/s |
| Seattle | E.U.A. Centro-Norte |  2\.792 km |  55 ms |  27,7 Mbits/s |  2,19 Gbits/s |
| Seattle | Este dos E.U.A. 2        |  3\.769 km |  73 ms |  21,3 Mbits/s |  1,79 Gbits/s |
| Seattle | Este dos E.U.A.          |  3\.699 km |  74 ms |  21,1 Mbits/s |  1,78 Gbits/s |
| Seattle | Este do Japão       |  7\.705 km | 106 ms |  14,6 Mbits/s |  1,22 Gbits/s |
| Seattle | Sul do Reino Unido         |  7\.708 km | 146 ms |  10,6 Mbits/s |   896 Mbits/s |
| Seattle | Europa Ocidental      |  7\.834 km | 153 ms |  10,2 Mbits/s |   761 Mbits/s |
| Seattle | Leste da Austrália   | 12.484 km | 165 ms |   9,4 Mbits/s |   794 Mbits/s |
| Seattle | Sudeste Asiático   | 12.989 km | 170 ms |   9,2 Mbits/s |   756 Mbits/s |
| Seattle | Sul do Brasil *   | 10.930 km | 189 ms |   8,2 Mbits/s |   699 Mbits/s |
| Seattle | Sul da Índia      | 12.918 km | 202 ms |   7,7 Mbits/s |   634 Mbits/s |

\* a latência para o Brasil é um bom exemplo em que a distância de linha reta é significativamente diferente da distância de execução de fibra. Eu esperava que a latência fosse na vizinhança de 160 ms, mas na verdade é 189 MS. Essa diferença em relação à minha expectativa poderia indicar um problema de rede em algum lugar, mas é muito provável que a execução de fibra não vá para o Brasil em uma linha reta e tenha um 1.000 km extra ou então de viajar para chegar ao Brasil de Seattle.

## <a name="next-steps"></a>Passos seguintes
1. Baixe o kit de ferramentas de conectividade do Azure do GitHub em [https://aka.ms/AzCT][ACT]
2. Siga as instruções para [teste de desempenho de link][Performance Doc]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "Componentes de rede do Azure"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "Solução de problemas do ExpressRoute"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "Ambiente de teste perf"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "Saída do PowerShell"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: https://docs.microsoft.com/azure/index
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: https://aka.ms/AzCT
