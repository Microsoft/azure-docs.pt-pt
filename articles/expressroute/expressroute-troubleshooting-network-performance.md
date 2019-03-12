---
title: 'Resolver problemas de desempenho de rede virtual: Azure | Microsoft Docs'
description: Esta página fornece um método padronizado de testes de desempenho de ligação de rede do Azure.
services: expressroute
author: tracsman
ms.service: expressroute
ms.topic: article
ms.date: 12/20/2017
ms.author: jonor
ms.custom: seodec18
ms.openlocfilehash: 17f239dc3ba9714c01f55a88419df2d3416908fa
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57539705"
---
# <a name="troubleshooting-network-performance"></a>Resolução de problemas de desempenho da rede
## <a name="overview"></a>Descrição geral
O Azure fornece estáveis e rápidas formas de ligar ao Azure a partir da sua rede no local. Métodos, como VPN Site a Site e ExpressRoute com êxito são utilizados pelos clientes grandes e pequeno porte para executar seus negócios no Azure. Mas o que acontece quando o desempenho não atender às suas expectativas ou experiência anterior? Este documento pode ajudar a padronizar a linha de base e a forma como testar seu ambiente específico.

Este documento mostra como pode facilmente e consistente de teste latência de rede e largura de banda entre dois anfitriões. Este documento também fornece alguns conselhos sobre as formas de examinar a rede do Azure e ajudar a isolar pontos problemáticos. O script do PowerShell e ferramentas mencionadas exigem dois anfitriões na rede (em cada extremidade da ligação que está sendo testada). Um anfitrião tem de ser um Windows Server ou ambiente de trabalho, o outro pode ser Windows ou Linux. 

>[!NOTE]
>A abordagem da resolução de problemas, as ferramentas e os métodos utilizados são preferências pessoais. Este documento descreve a abordagem e ferramentas que, muitas vezes, demorar. Sua abordagem provavelmente serão diferentes, não há nada errado com abordagens diferentes para a resolução de problemas. No entanto, se não tiver uma abordagem estabelecida, este documento pode ajudá-lo a trabalhar com o caminho para a criação de seus próprios métodos, ferramentas e as preferências de resolução de problemas de rede.
>
>

## <a name="network-components"></a>Componentes de rede
Antes de aprofundarmos a resolução de problemas, vamos discutir alguns termos e componentes comuns. Esta discussão garante que estiver pensando em cada componente da cadeia de ponto-a-ponto que permite a conectividade no Azure.
[![1]][1]

Com o nível mais alto, descrevo três domínios de encaminhamento de rede principais;

- a rede do Azure (nuvem azul à direita)
- a Internet ou WAN (verde na cloud no Centro de)
- a rede corporativa (cor na cloud à esquerda)

Vamos analisar o diagrama da direita para a esquerda, discuta brevemente cada componente:
 - **Máquina virtual** - o servidor de pode ter várias NICs, certifique-se de que quaisquer rotas estáticas, as rotas predefinidas e definições do sistema operativo estão a enviar e receber tráfego a forma como acha que ele é. Além disso, cada SKU de VM tem uma restrição de largura de banda. Se estiver a utilizar um SKU de VM mais pequeno, o tráfego é limitado pela largura de banda disponível para o NIC. Normalmente uso um DS5v2 para teste (e, em seguida, eliminar depois de teste para poupar dinheiro) para garantir que a largura de banda suficiente na VM.
 - **NIC** -Certifique-se de que sabe que o IP privado que é atribuído ao NIC em questão.
 - **O NSG de NIC** - lá pode ser específicos NSGs aplicados ao nível do NIC, certifique-se de que o conjunto de regras do NSG é adequado para o tráfego que está a tentar passar. Por exemplo, certifique-se as portas 5201 para iPerf, 3389 para RDP, ou 22 para SSH estão abertas para permitir o tráfego de teste passar.
 - **VNet Subnet** -a NIC está atribuído a uma sub-rede específica, certifique-se de que sabe que um deles e as regras associadas com essa sub-rede.
 - **NSG de sub-rede** - tal como a NIC, os NSGs podem ser aplicadas em também a sub-rede. Certifique-se de que o conjunto de regras do NSG é adequado para o tráfego que está a tentar passar. (para o tráfego de entrada para o NIC na sub-rede que NSG aplica-se em primeiro lugar, em seguida, o NSG de NIC, por outro lado para o tráfego de saída da VM NIC NSG aplica-se em primeiro lugar, em seguida, o NSG de sub-rede entra em ação).
 - **Sub-rede UDR** -rotas definidas pelo utilizador pode direcionar o tráfego para um salto intermédio (como uma firewall ou Balanceador de carga). Certifique-se de que sabe se há um UDR para o tráfego e se, por isso, onde elas forem e o que irá fazer esse próximo salto para o tráfego. (por exemplo, uma firewall poderia passar algum tráfego e negar outro tráfego entre os dois anfitriões mesmo).
 - **A sub-rede de gateway / NSG / UDR** -tal como a sub-rede VM, a sub-rede do gateway pode ter NSGs e as UDRs. Certifique-se de que sabe se eles estão lá e que efeitos têm em seu tráfego.
 - **Gateway de VNet (ExpressRoute)** -depois de peering (ExpressRoute) ou a VPN estiver ativada, não há muitas configurações que podem afetar como ou se as rotas de tráfego. Se tiver vários circuitos do ExpressRoute ou os túneis VPN ligados ao mesmo Gateway de VNet, deve estar ciente das definições de peso de ligação como essa preferência de ligação de afeta de definição e afeta o caminho que o tráfego segue.
 - **Filtro de rota** (não mostrado) - um filtro de rota só se aplica ao Peering da Microsoft no ExpressRoute, mas é crítico para verificar se não estiver a ver as rotas que espera no Peering da Microsoft. 

Neste momento, está na parte da ligação WAN. Este domínio de encaminhamento pode ser o fornecedor de serviços, sua WAN corporativa ou na Internet. Muitos saltos, tecnologias e empresas envolvidas com estas ligações podem torná-lo um pouco difícil resolução de problemas. Muitas vezes, trabalha para excluir o Azure e suas redes empresariais primeiro antes de passar para esta coleção de empresas e saltos.

No diagrama anterior, na extremidade esquerda é sua rede empresarial. Dependendo do tamanho da sua empresa, este domínio de encaminhamento pode ser alguns dispositivos de rede entre e a WAN ou várias camadas de dispositivos numa rede campus/enterprise.

Devido às complexidades um desses ambientes de rede de alto nível de diferentes três, muitas vezes, é ideal para iniciar as extremidades e tente mostrar onde o desempenho é bom e, em que ela degrada. Essa abordagem pode ajudar a identificar o domínio de encaminhamento do problema dos três e, em seguida, concentrar-se a resolução de problemas no ambiente específico.

## <a name="tools"></a>Ferramentas
A maioria dos problemas de rede podem ser analisados e com as ferramentas básicas, como o ping e traceroute em ambiente isolado. É raro que tem de rever as uma análise do pacote, como Wireshark. Para ajudar na resolução de problemas, o Kit de ferramentas de conectividade do Azure (AzureCT) foi desenvolvida para colocar algumas dessas ferramentas num pacote de fácil. Para testes de desempenho, eu gosto de usar iPerf e PSPing. iPerf é uma ferramenta frequentemente utilizada e é executado na maioria dos sistemas operacionais. iPerf é bom para testes dos desempenhos básica e é bastante fácil de usar. PSPing é uma ferramenta de ping, desenvolvida pelo SysInternals. PSPing é uma forma fácil de efetuar os pings ICMP e TCP num comando também fácil de usar. Essas duas ferramentas são leves e são "instaladas" simplesmente ao lidar os ficheiros para um diretório no anfitrião.

Encapsulei toda uma dessas ferramentas e métodos num módulo do PowerShell (AzureCT) que pode instalar e utilizar.

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT - o Toolkit de conectividade do Azure
O módulo do AzureCT PowerShell tem dois componentes [testes de disponibilidade] [ Availability Doc] e [testes de desempenho][Performance Doc]. Este documento só está preocupado com testes de desempenho, pelo que vamos concentrar-se em dois comandos de desempenho de ligação neste módulo do PowerShell.

Existem três passos básicos para usar este kit de ferramentas para testes de desempenho. 1) instale o módulo do PowerShell, 2) instale o suporte iPerf de aplicativos e o PSPing 3) execute o teste de desempenho.

1. Instalar o módulo do PowerShell

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    Este comando transfere o módulo do PowerShell e instala-o localmente.

2. Instalar as aplicações de suporte
    ```powershell
    Install-LinkPerformance
    ```
    Este comando AzureCT instala iPerf e PSPing num novo diretório "C:\ACTTools", ele também abre as portas de Firewall do Windows para permitir o ICMP e 5201 (iPerf) tráfego de porta.

3. Execute o teste de desempenho

    Em primeiro lugar, no anfitrião remoto tem de instalar e executar iPerf no modo de servidor. Certifique-se também de 22 (SSH para Linux) e a permitir o tráfego na porta 5201 para iPerf ou do anfitrião remoto está a escutar qualquer um dos 3389 (RDP para Windows). Se o anfitrião remoto for windows, instale o AzureCT e execute o comando de instalação LinkPerformance configurar iPerf e as regras de firewall necessárias para iniciar iPerf no modo de servidor com êxito. 
    
    Quando o computador remoto estiver pronto, abra o PowerShell no computador local e iniciar o teste:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    Este comando executa uma série de carga simultânea e testes de latência para ajudar a estimar a capacidade de largura de banda e latência da sua ligação de rede.

4. Reveja o resultado dos testes

    O formato de saída do PowerShell é semelhante a:

    [![4]][4]

    Os resultados detalhados de todos os iPerf e PSPing testes estão em arquivos de texto individuais no diretório de ferramentas AzureCT em "C:\ACTTools."

## <a name="troubleshooting"></a>Resolução de problemas
Se o teste de desempenho não esteja lhe oferecendo os resultados esperados, descobrir por que deve ser um processo passo a passo progressivo. Uma abordagem sistemática, geralmente, tendo em conta o número de componentes no caminho, fornece um caminho mais rápido de resolução que facilitavam e potencialmente desnecessariamente fazer o mesmo teste várias vezes.

>[!NOTE]
>O cenário aqui é um problema de desempenho, não é um problema de conectividade. Os passos seria diferentes se o tráfego não estava passando de todo.
>
>

Em primeiro lugar, desafie suas suposições. É sua expetativa razoável? Por exemplo, se tiver um circuito do ExpressRoute de 1 Gbps e 100 ms de latência é irracional esperar que o total de 1 Gbps de tráfego, dado as características de desempenho do TCP através de ligações de alta latência. Consulte a [faz referência a seção](#references) para mais on suposições de desempenho.

Em seguida, recomendamos a partir das extremidades entre domínios de encaminhamento e tentar isolar o problema para um único domínio de encaminhamento principais; a rede empresarial, a WAN ou a rede do Azure. As pessoas muitas vezes, a culpa "caixa preta" no caminho, ao mesmo tempo culpando à caixa negra é fácil fazê-lo, significativamente pode atrasar resolução especialmente se o problema é, na verdade, numa área que tem a capacidade de fazer alterações. Certifique-se de que fazer sua auditoria detalhada antes signifique entregar ao seu fornecedor de serviços ou ISP.

Depois de identificar o domínio de encaminhamento principais que pareçam conter o problema, deve criar um diagrama da área em questão. Qualquer um num quadro de comunicações, o bloco de notas ou o Visio como um diagrama fornece um concreto "mapear batalha" para permitir uma abordagem metódica para isolar ainda mais o problema. Pode planear pontos de testes e atualizar o mapa limpar áreas ou aprofunde conforme o andamento do teste.

Agora que tem um diagrama, comece a dividir a rede em segmentos e restringir o problema. Saiba em que ele funciona e onde não. Manter a mover seus pontos de testes para isolar para baixo até o componente incorreto.

Além disso, não se esqueça de examinar outras camadas do modelo OSI. É fácil se concentrem na rede e as camadas 1 a 3 (camadas físicas, dados e rede), mas os problemas também podem ser cópia de segurança na camada 7 na camada de aplicativo. Mantenha a mente aberta e verificar as suposições.

## <a name="advanced-expressroute-troubleshooting"></a>Solução avançada de problemas de ExpressRoute
Se não tiver a certeza onde é, na verdade, o limite da cloud, a isolar os componentes do Azure pode ser um desafio. Quando é utilizado o ExpressRoute, o edge é um componente de rede chamado do Microsoft Enterprise Edge (MSEE). **Ao utilizar o ExpressRoute**, o MSEE é o primeiro ponto de contacto para a rede da Microsoft e o último salto sair da rede da Microsoft. Quando cria um objeto de conexão entre o gateway de VNet e o circuito do ExpressRoute, na verdade estamos fazendo uma ligação para o MSEE. RECONHECENDO do MSEE como o salto primeiro ou último (consoante a direção vai) é crucial para isolar problemas de rede do Azure a qualquer uma provar que o problema está no Azure, ou ainda mais longo do caminho na WAN ou à rede empresarial. 

[![2]][2]

>[!NOTE]
> Tenha em atenção que o MSEE não está na cloud do Azure. O ExpressRoute, na verdade, é na borda da rede da Microsoft, na verdade não no Azure. Assim que estiver ligado com o ExpressRoute para uma MSEE, está ligado à rede da Microsoft, a partir daí, em seguida, pode aceder a qualquer um dos serviços cloud, como o Office 365 (com Peering da Microsoft) ou o Azure (com o Peering da Microsoft e/ou privada).
>
>

Se duas VNets (VNets A e B no diagrama) estão ligados para o **mesmo** circuito do ExpressRoute, pode realizar uma série de testes para isolar o problema no Azure (ou provar que não está no Azure)
 
### <a name="test-plan"></a>Plano do teste
1. Execute o teste de Get-LinkPerformance entre VM1 e VM2. Este teste fornece uma visão para se o problema é local ou não. Se este teste produz latência aceitável e resultados de largura de banda, é possível marcar a rede de VNet local como bom.
2. Partindo do princípio de local VNet o tráfego é boa, execute o teste de Get-LinkPerformance entre VM1 e VM3. Este teste exercita a ligação através da rede da Microsoft para baixo até o MSEE e para o Azure. Se este teste produz latência aceitável e resultados de largura de banda, é possível marcar a rede do Azure como bom.
3. Se o Azure é descartado, pode executar uma seqüência semelhante de testes em sua rede corporativa. Se de que testa a bem, está na altura de trabalhar com o seu ISP ou fornecedor de serviços para diagnosticar a sua ligação WAN. Exemplo: Execute este teste entre duas filiais, ou entre seu suporte técnico e de um servidor de centro de dados. Consoante o que estiver testando, localizar pontos de extremidade (servidores, PCs, etc.) que podem exercer esse caminho.

>[!IMPORTANT]
> É fundamental que para cada teste marca a hora do dia, que execute o teste e registe os resultados num local comum (gosto OneNote ou o Excel). Cada execução de teste deve ter saída idêntica para que possa comparar os dados resultantes em execuções de teste e não têm "buracos" nos dados. A consistência em vários testes é a principal razão de que usar o AzureCT para resolução de problemas. Não é a mágica nos cenários de carga exata executo, mas em vez disso, o *mágica* é o fato de que tenho um *saída de dados e teste consistente* de todos os testes. O tempo de gravação e ter dados consistentes única cada vez são especialmente útil se encontrar posteriormente que o problema é esporádico. Ser diligentes com sua coleção de dados com antecedência e evitará horas de novos testes os mesmos cenários (aprendi rígido assim há muitos anos).
>
>

## <a name="the-problem-is-isolated-now-what"></a>O problema é isolado, e agora?
Quanto mais pode isolar o problema mais fácil é corrigir, no entanto, muitas vezes, atingir o ponto em que não é possível ir mais aprofundada ou ainda mais com a sua solução de problemas. Exemplo: veja a ligação entre o fornecedor de serviços tirar saltos por meio da Europa, mas seu caminho esperado é tudo na Ásia. Neste ponto é quando deve entrar em contacto para obter ajuda. É por que pede depende de tivesse isolado o problema para o domínio de encaminhamento, ou ainda melhor se for capaz de restringi-la para baixo para um componente específico.

Para problemas de rede empresarial, o departamento de TI interno ou o fornecedor de serviços, suporte a sua rede (que pode ser o fabricante de hardware) pode ser capaz de ajudar na configuração do dispositivo ou a reparação de hardware.

Para o WAN, os resultados do testes de partilha com o seu fornecedor de serviços ou ISP pode ajudar a obtê-los ao e evitar que abrangem alguns dos mesmo zero que tiver testado já. No entanto, a não ser offended se pretende verificar os resultados propriamente ditas. "Confiar, mas certifique-se" é uma boa lema quando a resolução de problemas com base nos resultados de comunicada de outras pessoas.

Com o Azure, uma vez que isolar o problema da forma mais detalhada de como pode, chegou a hora para rever o [documentação de rede do Azure] [ Network Docs] e, em seguida, se ainda necessário [abrir um pedido de suporte][Ticket Link].

## <a name="references"></a>Referências
### <a name="latencybandwidth-expectations"></a>Expectativas de latência/largura de banda
>[!TIP]
> Latência geográfica (quilómetros ou quilômetros) entre os pontos finais que está a testar é, de longe o maior componente da latência. Embora haja latência de equipamentos (componentes físicos e virtuais, o número de saltos, etc.) envolvidos, geografia provou para ser o maior componente da latência geral ao lidar com ligações WAN. Também é importante observar que a distância é a distância de fibra executar não o em linha reta ou a distância de mapa da estrada. Essa distância é incrivelmente difícil com qualquer precisão. Assim, eu geralmente usa uma calculadora de distância da cidade na internet e sabe que este método é uma medida grosseiramente incorreta, mas é suficiente para definir uma expectativa geral.
>
>

Tenho uma configuração de ExpressRoute em Seattle, Washington, nos EUA. A tabela seguinte mostra a latência e largura de banda vi testes em várias localizações do Azure. Eu já estima a distância geográfica entre cada final do teste.

Configuração de teste:
 - Um servidor físico com o Windows Server 2016 com um 10 Gbps NIC, ligado a um circuito do ExpressRoute.
 - Um circuito do Premium ExpressRoute de 10 Gbps na localização identificada com o Peering privado ativado.
 - Uma VNet do Azure com um gateway de UltraPerformance na região especificada.
 - Uma VM de DS5v2 executar o Windows Server 2016 na VNet. A VM foi sem domínio associado, criadas, da predefinição, uma imagem do Azure (nenhuma otimização ou personalização) com AzureCT instalado.
 - Todos os testes estava a utilizar o comando AzureCT Get-LinkPerformance com um teste de carga de 5 minutos para cada uma das seis execuções de teste. Por exemplo:

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - O fluxo de dados para cada teste tinha a carga transmitidos do servidor físico até a VM do Azure (iPerf server na região do Azure listado) no local (cliente iPerf em Seattle).
 - Os dados da coluna "Latência" são do teste de carga não (um teste de latência TCP sem iPerf em execução).
 - Os dados da coluna "Máxima da largura de banda" são a partir de 16 teste de carga de fluxo TCP com um tamanho de janela de 1 Mb.

[![3]][3]

### <a name="latencybandwidth-results"></a>Resultados de latência/largura de banda
>[!IMPORTANT]
> Esses números são geral apenas para referência. Muitos fatores afetam a latência e, embora estes valores são geralmente consistentes ao longo do tempo, condições dentro da rede de fornecedores de serviços do Azure ou podem enviar o tráfego através de caminhos diferentes em qualquer altura, assim, latência e largura de banda podem ser afetados. Em geral, os efeitos destas alterações não resultarem numa diferenças significativas.
>
>

| | | | | | |
|-|-|-|-|-|-|
|ExpressRoute<br/>Localização|Azure<br/>Região|Estimado<br/>Distância (km)|Latência|1 sessão<br/>Largura de Banda|Máximo<br/>Largura de Banda|
| Seattle | EUA Oeste 2        |    191 km |   5 ms | 262.0 Mbits/s |  3.74 Gbits/seg | 21
| Seattle | EUA Oeste          |  1,094 km de distância |  18 ms |  82.3 Mbits/s |  3.70 Gbits/seg | 20
| Seattle | EUA Central       |  2,357 km de distância |  40 ms |  38.8 Mbits/s |  2.55 Gbits/seg | 17
| Seattle | EUA Centro-Sul |  2,877 km de distância |  51 ms |  30.6 Mbits/s |  2,49 Gbits/seg | 19
| Seattle | EUA Centro-Norte |  2,792 km de distância |  55 ms |  27.7 Mbits/s |  2.19 Gbits/seg | 18
| Seattle | EUA Leste 2        |  3,769 km de distância |  73 ms |  21.3 Mbits/s |  1.79 Gbits/seg | 16
| Seattle | EUA Leste          |  3,699 km de distância |  74 ms |  21.1 Mbits/s |  1.78 Gbits/seg | 15
| Seattle | Leste do Japão       |  7,705 km de distância | 106 ms |  14.6 Mbits/s |  1.22 Gbits/seg | 28
| Seattle | Reino Unido Sul         |  7,708 km de distância | 146 ms |  10.6 Mbits/s |   896 Mbits/s | 24
| Seattle | Europa Ocidental      |  7,834 km de distância | 153 ms |  10.2 Mbits/s |   761 Mbits/s | 23
| Seattle | Leste da Austrália   | 12,484 km de distância | 165 ms |   9.4 Mbits/s |   794 Mbits/s | 26
| Seattle | Sudeste Asiático   | 12,989 km de distância | 170 ms |   9.2 Mbits/s |   756 Mbits/s | 25
| Seattle | Sul do Brasil *   | 10,930 km de distância | 189 ms |   8.2 Mbits/s |   699 Mbits/s | 22
| Seattle | Sul da Índia      | 12,918 km de distância | 202 ms |   7.7 Mbits/s |   634 Mbits/s | 27

\* A latência para Brasil é um bom exemplo em que a distância em linha reta é significativamente diferente do fibra executar distância. Eu esperava que a latência deve ser cerca de 160 ms, mas é realmente 189 ms. Essa diferença em relação a minha expectativa pode indicar um problema de rede em algum lugar, mas é muito provável que tenha que executar o fiber não passa para Brasil numa linha reta e tem um km 1.000 extra mais ou menos de deslocação para chegar ao Brasil de Seattle.

## <a name="next-steps"></a>Passos Seguintes
1. Transferir o Toolkit de conectividade do Azure a partir do GitHub em [https://aka.ms/AzCT][ACT]
2. Siga as instruções para [ligar um teste de desempenho][Performance Doc]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "componentes de rede do azure"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "resolução de problemas do ExpressRoute"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "ambiente de teste de desempenho"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "saída do PowerShell"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: https://docs.microsoft.com/azure/index
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: https://aka.ms/AzCT











