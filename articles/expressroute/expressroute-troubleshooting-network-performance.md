---
title: 'Desempenho da ligação da rede de resolução de problemas: Azure'
description: Esta página fornece um método padronizado de testar o desempenho da ligação de rede Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 01/07/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 35e080e0fe45c18ad6a6d5392e0c78b116853c3e
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98027473"
---
# <a name="troubleshooting-network-performance"></a>Resolver problemas de desempenho da rede
## <a name="overview"></a>Descrição geral
O Azure fornece uma forma estável e rápida de ligar da sua rede no local a Azure. Métodos como VPN Site a Site e ExpressRoute são utilizados com êxito por clientes grandes e pequenos para a gestão dos seus negócios no Azure. Mas o que acontece quando o desempenho não corresponde às suas expectativas ou experiência anteriores? Este artigo pode ajudar a normalizar a forma como testa e faz a linha de base do seu ambiente específico.

Você vai aprender a testar facilmente e consistentemente a latência da rede e a largura de banda entre dois anfitriões. Também lhe serão fornecidos alguns conselhos sobre formas de olhar para a rede Azure para ajudar a isolar pontos problemáticos. O script e as ferramentas do PowerShell discutidos requerem dois anfitriões na rede (em cada extremidade da ligação que está a ser testada). Um dos anfitriões deve ser um Servidor do Windows ou um Ambiente de Trabalho, o outro pode ser Windows ou Linux. 

>[!NOTE]
>A abordagem à resolução de problemas, as ferramentas e os métodos utilizados são preferências pessoais. Este documento descreve a abordagem e as ferramentas que costumo tomar. A tua abordagem provavelmente vai diferir, não há nada de errado com diferentes abordagens para a resolução de problemas. No entanto, se não tiver uma abordagem estabelecida, este documento pode começar a construir os seus próprios métodos, ferramentas e preferências para resolver problemas de rede.
>
>

## <a name="network-components"></a>Componentes de rede
Antes de investigar a resolução de problemas, vamos discutir alguns termos e componentes comuns. Esta discussão garante que estamos a pensar em cada componente da cadeia de ponta a ponta que permite a conectividade em Azure.
![1][1]

Ao mais alto nível, descrevo três grandes domínios de encaminhamento de rede;

- a rede Azure (nuvem azul à direita)
- a Internet ou WAN (nuvem verde no centro)
- a Rede Corporativa (nuvem de pêssego à esquerda)

Olhando para o diagrama da direita para a esquerda, vamos discutir brevemente cada componente:
 - **Máquina Virtual** - O servidor pode ter vários NICs. Certifique-se de que quaisquer rotas estáticas, rotas predefinitivas e definições do Sistema Operativo estão a enviar e a receber o tráfego da forma que pensa. Além disso, cada VM SKU tem uma restrição de largura de banda. Se estiver a utilizar um VM SKU menor, o seu tráfego é limitado pela largura de banda disponível para o NIC. Costumo usar um DS5v2 para testes (e depois apagar uma vez feito com testes para economizar dinheiro) para garantir uma largura de banda adequada no VM.
 - **NIC** - Certifique-se de que conhece o IP privado que é atribuído ao NIC em questão.
 - **NIC NSG** - Pode haver NSGs específicos aplicados ao nível de NIC, certifique-se de que o conjunto de regras NSG é apropriado para o tráfego que você está tentando passar. Por exemplo, certifique-se de que as portas 5201 para iPerf, 3389 para RDP ou 22 para SSH estão abertas para permitir a passagem do tráfego de teste.
 - **Sub-rede VNet** - O NIC é atribuído a uma sub-rede específica, certifique-se de que sabe qual e as regras associadas a essa sub-rede.
 - **Subnet NSG** - Tal como o NIC, os NSGs também podem ser aplicados na sub-rede. Certifique-se de que a regra da NSG é adequada para o tráfego que está a tentar passar. (Para o tráfego de entrada para o NIC, a sub-rede NSG aplica-se primeiro, em seguida, o NIC NSG. Quando o tráfego está a sair do VM, o NIC NSG aplica-se primeiro, em seguida, a Subnet NSG é aplicada).
 - **Sub-rede UDR** - User-Defined Rotas podem direcionar o tráfego para um lúpulo intermédio (como uma firewall ou um equilibrador de carga). Certifique-se de que sabe se há um UDR no local para o seu tráfego. Se assim for, entenda onde vai e o que o próximo salto fará ao seu tráfego. Por exemplo, uma firewall poderia passar algum tráfego e negar outro tráfego entre os mesmos dois anfitriões.
 - **Sub-rede gateway / NSG / UDR** - Tal como a sub-rede VM, a sub-rede gateway pode ter NSGs e UDRs. Certifique-se de saber se eles estão lá e quais os efeitos que eles têm no seu tráfego.
 - **VNet Gateway (ExpressRoute)** - Uma vez que o espremiamento (ExpressRoute) ou VPN está ativado, não há muitas configurações que possam afetar como ou se as rotas de tráfego. Se tiver um Gateway VNet ligado a vários circuitos ExpressRoute ou túneis VPN, deve estar ciente das definições de peso da ligação. O peso da ligação afeta a preferência de ligação e determina o caminho que o seu tráfego toma.
 - **Filtro de rota** (não mostrado) - É necessário um filtro de rota quando utilizar o Microsoft Peering através do ExpressRoute. Se não estiver a receber rotas, verifique se o filtro de rota está configurado e aplicado corretamente no circuito.

Neste momento, estás na parte wan da ligação. Este domínio de encaminhamento pode ser o seu fornecedor de serviços, o seu WAN corporativo ou a Internet. Existem muitos lúpulo, dispositivos e empresas envolvidas nestas ligações, o que pode dificultar a resolução de problemas. Primeiro tens de excluir o Azure e as tuas redes corporativas antes de poderes investigar o lúpulo no meio.

No diagrama anterior, na extrema esquerda está a sua rede corporativa. Dependendo do tamanho da sua empresa, este domínio de encaminhamento pode ser alguns dispositivos de rede entre si e o WAN ou várias camadas de dispositivos numa rede campus/empresa.

Dada a complexidade destes três diferentes ambientes de rede de alto nível. É muitas vezes ideal começar nas bordas e tentar mostrar onde o desempenho é bom, e onde se degrada. Esta abordagem pode ajudar a identificar o domínio de encaminhamento de problemas dos três. Então podes concentrar a tua resolução de problemas nesse ambiente específico.

## <a name="tools"></a>Ferramentas
A maioria dos problemas de rede podem ser analisados e isolados usando ferramentas básicas como ping e traceroute. É raro ir tão fundo como uma análise de pacotes usando ferramentas como o Wireshark. 

Para ajudar na resolução de problemas, o Azure Connectivity Toolkit (AzureCT) foi desenvolvido para colocar algumas destas ferramentas num pacote fácil. Para testes de desempenho, ferramentas como iPerf e PSPing podem fornecer-lhe informações sobre a sua rede. O iPerf é uma ferramenta comumente usada para testes básicos de desempenho e é bastante fácil de usar. PSPing é uma ferramenta de ping desenvolvida pela SysInternals. PsPing pode fazer pings ICMP e TCP para chegar a um hospedeiro remoto. Ambas as ferramentas são leves e são "instaladas" simplesmente copiando os ficheiros para um diretório no anfitrião.

Estas ferramentas e métodos estão embrulhados num módulo PowerShell (AzureCT) que pode instalar e utilizar.

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT - o Azure Connectivity Toolkit
O módulo AzureCT PowerShell tem dois componentes [De Teste de Disponibilidade][Availability Doc] e Teste de [Desempenho.][Performance Doc] Este documento diz apenas respeito aos testes de desempenho, pelo que permite focar-se nos dois comandos Link Performance neste módulo PowerShell.

Existem três passos básicos para utilizar este conjunto de ferramentas para testes de desempenho. 1) Instale o módulo PowerShell, 2) Instale as aplicações de suporte iPerf e PSPing 3) Escoda o teste de desempenho.

1. Instalação do Módulo PowerShell

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    Este comando descarrega o módulo PowerShell e instala-o localmente.

2. Instalar as aplicações de suporte
    ```powershell
    Install-LinkPerformance
    ```
    Este comando AzureCT instala iPerf e PSPing num novo diretório "C:\ACTTools", abre também as portas windows Firewall para permitir o tráfego do ICMP e da porta 5201 (iPerf).

3. Executar o teste de desempenho

    Primeiro, no anfitrião remoto tem de instalar e executar o iPerf no modo servidor. Certifique-se também de que o anfitrião remoto está a ouvir o 3389 (RDP para Windows) ou o 22 (SSH para Linux) e permitir o tráfego na porta 5201 para o iPerf. Se o anfitrião remoto for o Windows, instale o AzureCT e execute o comando Install-LinkPerformance. O comando configurará o iPerf e as regras de firewall necessárias para iniciar o iPerf no modo servidor com sucesso. 
    
    Assim que a máquina remota estiver pronta, abra o PowerShell na máquina local e inicie o teste:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    Este comando executa uma série de testes simultâneos de carga e latência para ajudar a estimar a capacidade de largura de banda e a latência da sua ligação de rede.

4. Rever a saída dos testes

    O formato de saída PowerShell é semelhante a:

    ![4][4]

    Os resultados detalhados de todos os testes iPerf e PSPing estão em ficheiros de texto individuais no diretório de ferramentas AzureCT em "C:\ACTTools".

## <a name="troubleshooting"></a>Resolução de problemas
Se o teste de desempenho não lhe está a dar resultados esperados, descubra por que deve ser um processo progressivo passo a passo. Dado o número de componentes no caminho, uma abordagem sistemática proporcionará um caminho de resolução mais rápido do que saltar. Ao resolver sistematicamente problemas, pode evitar fazer testes desnecessários várias vezes.

>[!NOTE]
>O cenário aqui é uma questão de desempenho, não uma questão de conectividade. Os passos seriam diferentes se o trânsito não passasse.
>
>

Primeiro, desafie as suas suposições. A sua expectativa é razoável? Por exemplo, se tiver um circuito ExpressRoute de 1 Gbps e 100 ms de latência. Não é razoável esperar os 1 Gbps completos de tráfego dadas as características de desempenho da TCP em relação a ligações de latência elevadas. Consulte a [secção Referências](#references) para obter mais informações sobre os pressupostos de desempenho.

Em seguida, recomendo começar nas bordas entre os domínios de encaminhamento e tentar isolar o problema para um único grande domínio de encaminhamento. Pode começar na Rede Corporativa, na WAN ou na Rede Azure. As pessoas culpam muitas vezes a "caixa negra" no caminho. Embora culpar a caixa preta seja fácil de fazer, pode atrasar significativamente a resolução. Especialmente se o problema estiver numa área em que pode fazer alterações para corrigir o problema. Certifique-se de que faz as suas diligências antes de entregar ao seu prestador de serviços ou ISP.

Uma vez identificado o principal domínio de encaminhamento que parece conter o problema, deve criar um diagrama da área em questão. Ao desenhar um diagrama num quadro, bloco de notas ou Visio, pode trabalhar metodicamente e isolar o problema. Pode planear pontos de teste e atualizar o mapa à medida que limpa áreas ou escavar mais profundamente à medida que o teste progride.

Agora que tem um diagrama, comece a dividir a rede em segmentos e reduza o problema. Descubra onde funciona e onde não funciona. Continue a mover os pontos de teste para isolar o componente ofensivo.

Além disso, não se esqueça de olhar para outras camadas do modelo OSI. É fácil focar-se na rede e nas camadas 1 - 3 (Camadas físicas, de dados e de rede), mas os problemas também podem estar na Camada 7 na camada de aplicação. Mantenha a mente aberta e verifique as suposições.

## <a name="advanced-expressroute-troubleshooting"></a>Resolução avançada de problemas expressRoute
Se não tem certeza de onde está a borda da nuvem, isolar os componentes Azure pode ser um desafio. Quando o ExpressRoute é utilizado, a borda é um componente de rede chamado Microsoft Enterprise Edge (MSEE). Ao utilizar o **ExpressRoute,** o MSEE é o primeiro ponto de contacto na rede da Microsoft e o último salto ao sair da rede Microsoft. Quando cria um objeto de ligação entre o gateway VNet e o circuito ExpressRoute, está a fazer uma ligação ao MSEE. Reconhecendo o MSEE como o primeiro ou último salto, dependendo do sentido em que o tráfego é crucial para isolar um problema de rede Azure. Saber qual direção provará se o problema está em Azure ou mais a jusante na WAN ou na rede corporativa. 

![2][2]

>[!NOTE]
> Note que o MSEE não está na nuvem de Azure. O ExpressRoute encontra-se, na verdade, no limite da rede microsoft que não está realmente no Azure. Uma vez ligado ao ExpressRoute a um MSEE, está ligado à rede da Microsoft, a partir daí pode ir a qualquer um dos serviços na nuvem, como o Microsoft 365 (com o Microsoft Peering) ou o Azure (com Private e/ou Microsoft Peering).
>
>

Se dois VNets estiverem ligados ao **mesmo** circuito ExpressRoute, pode fazer uma série de testes para isolar o problema em Azure.
 
### <a name="test-plan"></a>Plano de teste
1. Escoda o teste de Get-LinkPerformance entre VM1 e VM2. Este teste fornece uma visão se o problema é local ou não. Se este teste produzir resultados aceitáveis de latência e largura de banda, pode marcar a rede VNet local como boa.
2. Assumindo que o tráfego vNet local é bom, executar o teste de Get-LinkPerformance entre VM1 e VM3. Este teste exercita a ligação através da rede Microsoft até ao MSEE e volta ao Azure. Se este teste produzir resultados aceitáveis de latência e largura de banda, pode marcar a rede Azure como boa.
3. Se o Azure for excluído, podes fazer uma sequência de testes semelhante na tua rede corporativa. Se isso também for bem testes, é hora de trabalhar com o seu fornecedor de serviços ou ISP para diagnosticar a sua ligação WAN. Exemplo: Executar este teste entre duas sucursais, ou entre o seu desk e um servidor de data center. Dependendo do que está a testar, encontre pontos finais como servidores e computadores de clientes que possam exercer esse caminho.

>[!IMPORTANT]
> É fundamental que para cada teste marque a hora do dia em que faz o teste e registe os resultados num local comum (gosto de OneNote ou Excel). Cada teste deve ter uma saída idêntica para que possa comparar os dados resultantes em testes e não ter "buracos" nos dados. A consistência em vários testes é a principal razão pela qual uso o AzureCT para resolver problemas. A magia não está nos cenários exatos de carga que faço, mas em vez disso, a *magia* é o facto de eu ter um teste consistente e uma saída de *dados* de cada teste. Registar a hora e ter dados consistentes todas as vezes é especialmente útil se mais tarde descobrir que o problema é esporádico. Seja diligente com a sua recolha de dados na frente e evitará horas de retestes os mesmos cenários (aprendi isto de forma difícil há muitos anos).
>
>

## <a name="the-problem-is-isolated-now-what"></a>O problema está isolado, e agora?
Quanto mais isolar o problema, mais rápido a solução pode ser encontrada. Às vezes chegas a um ponto em que não podes ir mais longe com a tua resolução de problemas. Por exemplo, você vê a ligação através do seu fornecedor de serviços tomando salto pela Europa, mas você espera que o caminho permaneça todo na Ásia. Neste momento, devias pedir ajuda a alguém. A quem perguntar depende do domínio de encaminhamento a que isola a questão. Se conseguir reduzi-lo a um componente específico, seria ainda melhor.

Para problemas de rede corporativa, o seu departamento de TI interno ou prestador de serviços pode ajudar na configuração do dispositivo ou na reparação de hardware.

Para o WAN, partilhar os resultados dos seus testes com o seu Fornecedor de Serviços ou ISP ajudará a iniciá-los. Fazê-lo também evitará duplicar o mesmo trabalho que já fez. Não se ofenda se eles quiserem verificar os seus resultados. "Confiança mas verificar" é um bom lema quando a resolução de problemas com base nos resultados reportados de outras pessoas.

Com o Azure, uma vez que isole o problema com o máximo de detalhes possível, é hora de rever a Documentação da [Rede Azure][Network Docs] e, se ainda assim, precisar [de abrir um bilhete de apoio.][Ticket Link]

## <a name="references"></a>Referências
### <a name="latencybandwidth-expectations"></a>Expectativas de latência/largura de banda
>[!TIP]
> A latência geográfica (milhas ou quilómetros) entre os pontos finais que está a testar é de longe o maior componente de latência. Embora haja latência do equipamento (componentes físicos e virtuais, número de lúpulo, etc.) envolvidos, a geografia provou ser o maior componente da latência geral ao lidar com ligações WAN. Também é importante notar que a distância é a distância da fibra correr não a distância de linha reta ou de roteiro. Esta distância é incrivelmente difícil de obter com qualquer precisão. Como resultado, uso geralmente uma calculadora de distância da cidade na internet e sei que este método é uma medida grosseiramente imprecisa, mas é suficiente para definir uma expectativa geral.
>
>

Tenho uma instalação do ExpressRoute em Seattle, Washington, nos EUA. A tabela seguinte mostra a latência e largura de banda que vi a testar em vários locais do Azure. Estimei a distância geográfica entre cada extremidade do teste.

Configuração do teste:
 - Um servidor físico que executa o Windows Server 2016 com um NIC de 10 Gbps, ligado a um circuito ExpressRoute.
 - Um circuito ExpressRoute Premium de 10Gbps no local identificado com Private Peering ativado.
 - Um Azure VNet com uma porta de entrada UltraPerformance na região especificada.
 - Um DS5v2 VM a executar o Windows Server 2016 no VNet. O VM não era de domínio, construído a partir da imagem padrão de Azure (sem otimização ou personalização) com AzureCT instalado.
 - Todos os testes utilizam o comando AzureCT Get-LinkPerformance com um teste de carga de 5 minutos para cada um dos seis ensaios. Por exemplo:

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - O fluxo de dados para cada teste tinha a carga a fluir do servidor físico no local (cliente iPerf em Seattle) até ao Azure VM (servidor iPerf na região de Azure listada).
 - Os dados da coluna "Latência" são do teste De Carga Sem Carga (um teste de latência TCP sem iPerf em funcionamento).
 - Os dados da coluna "Max Bandwidth" são do teste de carga de fluxo de 16 TCP com um tamanho de janela de 1-Mb.

![3][3]

### <a name="latencybandwidth-results"></a>Resultados da latência/largura de banda
>[!IMPORTANT]
> Estes números são apenas para referência geral. Muitos fatores afetam a latência, e embora estes valores sejam geralmente consistentes ao longo do tempo, as condições dentro do Azure ou da rede de Prestadores de Serviços podem enviar tráfego através de diferentes caminhos a qualquer momento, assim a latência e a largura de banda podem ser afetadas. Geralmente, os efeitos destas alterações não resultam em diferenças significativas.
>
>

| ExpressRoute<br/>A localização|Azure<br/>Região | Estimado<br/>Distância (km) | Latência|1 Sessão<br/>Largura de banda | Máximo<br/>Largura de banda |
| ------------------------------------------ | --------------------------- |  - | - | - | - |
| Seattle | E.U.A. Oeste 2        |    191 km |   5 ms | 262,0 Mbits/seg |  3,74 Gbits/seg |
| Seattle | E.U.A. Oeste          |  1.094 km |  18 ms |  82,3 Mbits/seg |  3,70 Gbits/seg |
| Seattle | E.U.A. Central       |  2.357 km |  40 ms |  38,8 Mbits/seg |  2,55 Gbits/seg |
| Seattle | E.U.A. Centro-Sul |  2.877 km |  51 ms |  30,6 Mbits/seg |  2.49 Gbits/seg |
| Seattle | E.U.A. Centro-Norte |  2.792 km |  55 ms |  27,7 Mbits/seg |  2.19 Gbits/seg |
| Seattle | E.U.A. Leste 2        |  3.769 km |  73 ms |  21,3 Mbits/seg |  1,79 Gbits/seg |
| Seattle | E.U.A. Leste          |  3.699 km |  74 ms |  21,1 Mbits/seg |  1,78 Gbits/seg |
| Seattle | Leste do Japão       |  7.705 km | 106 ms |  14,6 Mbits/seg |  1,22 Gbits/seg |
| Seattle | Sul do Reino Unido         |  7.708 km | 146 ms |  10,6 Mbits/seg |   896 Mbits/seg |
| Seattle | Europa Ocidental      |  7.834 km | 153 ms |  10,2 Mbits/seg |   761 Mbits/seg |
| Seattle | Leste da Austrália   | 12.484 km | 165 ms |   9,4 Mbits/seg |   794 Mbits/seg |
| Seattle | Sudeste Asiático   | 12.989 km | 170 ms |   9,2 Mbits/seg |   756 Mbits/seg |
| Seattle | Brasil Sul *   | 10.930 km | 189 ms |   8,2 Mbits/seg |   699 Mbits/seg |
| Seattle | Sul da Índia      | 12.918 km | 202 ms |   7,7 Mbits/seg |   634 Mbits/seg |

\* A latência para o Brasil é um bom exemplo onde a distância de linha reta difere significativamente da distância de corrida de fibra. A latência esperada seria na vizinhança de 160 ms, mas na verdade é de 189 ms. A diferença de latência parece indicar um problema de rede em algum lugar. Mas a realidade é que a linha de fibra não vai para o Brasil em linha reta. Então você deve esperar mais 1.000 km de viagem para chegar ao Brasil a partir de Seattle.

## <a name="next-steps"></a>Passos seguintes
1. Descarregue o Azure Connectivity Toolkit do GitHub [https://aka.ms/AzCT][ACT]
2. Siga as instruções para testar o [desempenho da ligação][Performance Doc]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "Componentes da rede Azure"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "Resolução de problemas ExpressRoute"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "Ambiente de teste perf"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "Saída PowerShell"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: ../index.yml
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: https://aka.ms/AzCT