---
title: Exemplo de rede de perímetro – proteger redes com a rede de perímetro constituída por uma firewall, UDR e NSGs | Documentos da Microsoft
description: Crie uma rede de perímetro (também conhecida como DMZ) com uma firewall, o encaminhamento definido pelo utilizador (UDR) e grupos de segurança de rede (NSGs).
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: dc01ccfb-27b0-4887-8f0b-2792f770ffff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: 104eae7c8d18c32fd5b9b84147df2bf121345c22
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59277425"
---
# <a name="example-3-build-a-perimeter-network-to-protect-networks-with-a-firewall-udr-and-nsgs"></a>Exemplo 3: Criar uma rede de perímetro para proteger redes com uma firewall, UDR e NSGs

[Regressar à página de práticas recomendada de segurança limites][HOME]

Neste exemplo, vai criar uma rede de perímetro (também conhecido como um DMZ, zona desmilitarizada e sub-rede filtrada). O exemplo implementa uma firewall, quatro servidores do Windows, encaminhamento de definido pelo utilizador (UDR), o reencaminhamento de IPs e grupos de segurança de rede (NSGs). Este artigo orienta-o através de cada um dos comandos relevantes para fornecer uma compreensão mais aprofundada de cada passo. A secção de cenário de tráfego também explica em detalhes como o tráfego passa pelas camadas de defesa na rede de perímetro. Por fim, a seção de referências contém todo o código e as instruções para criar este ambiente, para que possa testar e experimentar a vários cenários.

![Rede de perímetro bidirecional com NVA, um NSG e UDR][1]

## <a name="environment-setup"></a>Configuração do ambiente

Este exemplo utiliza uma subscrição que contém os seguintes componentes:

* Três serviços de cloud: SecSvc001 FrontEnd001 e BackEnd001
* Uma rede virtual (CorpNetwork) com três sub-redes: SecNet, front-end e back-end
* Uma aplicação virtual de rede: uma firewall ligada à sub-rede de SecNet
* Um servidor do Windows que representa um servidor de web de aplicações: IIS01
* Dois servidores de Windows que representam servidores de back-end de aplicação: AppVM01, AppVM02
* Um servidor do Windows que representa um servidor DNS: DNS01

O [referencia secção](#references) contém um script do PowerShell que cria a maior parte do ambiente descrito aqui. Este artigo caso contrário, não dá instruções detalhadas para a criação de máquinas virtuais (VMs) e redes virtuais.

Para criar o ambiente:

1. Guarde o ficheiro XML de configuração de rede incluído no [secção de referência](#references). Terá de atualizá-lo com nomes, localização e endereços IP de acordo com o cenário determinado.
1. Atualize as variáveis de utilizador no script completo para corresponder ao seu ambiente específico (por exemplo, as subscrições, os nomes de serviço e assim por diante).
1. Execute o script do PowerShell.

> [!NOTE]
> A região especificada no script do PowerShell têm de corresponder a região especificada no ficheiro XML de configuração de rede.

Depois do script é executado com êxito, siga os passos seguintes:

1. Configure as regras de firewall. Consulte a [regras de firewall](#firewall-rules) secção.
1. Opcionalmente, utilize os dois scripts na seção referências para configurar uma aplicação web no servidor web e servidor de aplicações para permitir testes desta configuração de rede de Perímetro.

## <a name="user-defined-routing"></a>Encaminhamento definido pelo utilizador

Por predefinição, as seguintes rotas de sistema são definidas como:

    Effective routes :
     Address Prefix    Next hop type    Next hop IP address Status   Source
     --------------    -------------    ------------------- ------   ------
     {10.0.0.0/16}     VNETLocal                            Active   Default
     {0.0.0.0/0}       internet                             Active   Default
     {10.0.0.0/8}      Null                                 Active   Default
     {100.64.0.0/10}   Null                                 Active   Default
     {172.16.0.0/12}   Null                                 Active   Default
     {192.168.0.0/16}  Null                                 Active   Default

VNETLocal é sempre o s endereços definidos para essa rede virtual específica. Por exemplo, ele deixará de rede virtual a rede virtual, dependendo de como cada rede virtual específico é definida. As rotas de sistema restantes são estáticas e predefinido, conforme mostrado.

Quanto à prioridade, as rotas são processadas por meio do método de correspondência de prefixo mais longo (LPM). Portanto, a rota mais específica na tabela aplica-se a um determinado endereço de destino.

Por conseguinte, o tráfego destinado a um servidor, como o DNS01 (10.0.2.4) no local (10.0.0.0/16) de rede é encaminhado através da rede virtual devido a rota de 10.0.0.0/16.  Para 10.0.2.4, a rota de 10.0.0.0/16 é a rota mais específica. Esta regra aplica-se mesmo que também podem ser aplicável a 10.0.0.0/8 e 0.0.0.0/0. Eles são menos específicos, no entanto, para que elas não afetem este tráfego. O tráfego para 10.0.2.4 tem a rede virtual local, como o salto seguinte, portanto, é encaminhado para o destino.

Por exemplo, a rota de 10.0.0.0/16 não se aplica ao tráfego que pretende 10.1.1.1. A rota de sistema 10.0.0.0/8 é mais específica para que o tráfego é ignorado ou "preto holed" uma vez que o próximo salto é nulo.

Se o destino não se aplica a nenhum dos prefixos Null ou os prefixos de VNETLocal, em seguida, o tráfego segue a rota menos específica (0.0.0.0/0). É encaminhado para a internet como salto seguinte e para fora da borda de internet do Azure.

Se existirem dois prefixos idênticos na tabela de rotas, a ordem de preferência é baseada no atributo de origem da rota:

1. VirtualAppliance: Uma rota definidas pelo utilizador adicionados manualmente à tabela.
1. VPNGateway: Adicionada uma rota de dinâmico (BGP quando utilizado com redes híbridas) por um protocolo de rede dinâmico. Estas rotas podem ser alterados ao longo do tempo, como o protocolo dinâmico automaticamente reflete as alterações na rede em modo de peering.
1. Predefinição: As rotas de sistema, a rede virtual local e as entradas estáticas, como mostrado na tabela de rotas acima.

> [!NOTE]
> Agora pode utilizar o encaminhamento definido pelo utilizador (UDR) com o ExpressRoute e Gateways de VPN para forçar o tráfego de saída e entrada em vários locais para ser encaminhado para uma aplicação virtual de rede (NVA).

### <a name="create-local-routes"></a>Criar as rotas locais

Este exemplo utiliza a tabelas de roteamento dois, um para as sub-redes de front-end e back-end. Cada tabela é carregada com rotas estáticas adequadas para a sub-rede especificada. Para efeitos deste exemplo, cada tabela tem três formas:

1. Tráfego da sub-rede local não definido de salto seguinte. Esta rota permite tráfego de sub-rede local ignorar a firewall.
2. Tráfego de rede virtual com um salto seguinte definido como o firewall. Esta rota substitui a regra predefinida que permita o tráfego de rede virtual local encaminhar diretamente.
3. Todos os restante tráfego (0/0) com um salto seguinte definido como o firewall.

Depois das tabelas de encaminhamento são criadas, eles estão ligados a suas sub-redes. A tabela de encaminhamento de sub-rede front-end deve ser semelhante:

    Effective routes :
     Address Prefix    Next hop type       Next hop IP address  Status   Source
     --------------    ------------------  -------------------  ------   ------
     {10.0.1.0/24}     VNETLocal                                Active
     {10.0.0.0/16}     VirtualAppliance    10.0.0.4             Active
     {0.0.0.0/0}       VirtualAppliance    10.0.0.4             Active

Este exemplo utiliza os seguintes comandos para criar a tabela de rotas, adicione uma rota definida pelo utilizador e, em seguida, ligar a tabela de rotas a uma sub-rede. Itens que começam com `$`, tais como `$BESubnet`, são definidas pelo utilizador variáveis do script na seção de referência.

1. Primeiro, crie a tabela de encaminhamento de base. O fragmento de código seguinte cria a tabela para a sub-rede de back-end. O script completo também cria uma tabela correspondente para a sub-rede de front-end.

   ```powershell
   New-AzureRouteTable -Name $BERouteTableName `
       -Location $DeploymentLocation `
       -Label "Route table for $BESubnet subnet"
   ```

1. Depois de criar a tabela de rotas, é possível adicionar rotas específicas definidas pelo utilizador. O fragmento de código seguinte especifica que (0.0.0.0/0) todo o tráfego é encaminhado através do dispositivo virtual. Uma variável `$VMIP[0]` é usado para passar o endereço IP atribuído quando a aplicação virtual foi criada anteriormente no script. O script completo também cria uma regra correspondente na tabela de front-end.

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
       -NextHopType VirtualAppliance `
       -NextHopIpAddress $VMIP[0]
   ```

1. A entrada de rota anterior substitui a rota do padrão "0.0.0.0/0", mas a regra predefinida do 10.0.0.0/16 ainda permita o tráfego na rede virtual para encaminhar diretamente para o destino e não para a aplicação de rede virtual. Para corrigir este comportamento, terá de adicionar a seguinte regra:

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
       -NextHopType VirtualAppliance `
       -NextHopIpAddress $VMIP[0]
   ```

1. Neste momento, terá de escolher uma opção. As duas regras anteriores encaminham todo o tráfego para a firewall para avaliação, incluindo o tráfego dentro de uma única sub-rede. Pode desejar esse comportamento. Caso contrário, no entanto, pode permitir o tráfego dentro de uma sub-rede para encaminhar localmente, sem o envolvimento do firewall. Adicionar uma terceiro, a regra específica que encaminha diretamente qualquer endereço destinado a sub-rede local (NextHopType = VNETLocal).

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
           -NextHopType VNETLocal
   ```

1. Por fim, depois da tabela de encaminhamento é criada e preenchida com as rotas definidas pelo utilizador, terá de associar a tabela a uma sub-rede. O fragmento de código seguinte associa a tabela para a sub-rede de back-end. O script completo também vincula a tabela de rotas de front-end para a sub-rede do front-end.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
       -SubnetName $BESubnet `
       -RouteTableName $BERouteTableName
   ```

## <a name="ip-forwarding"></a>Reencaminhamento IP

Reencaminhamento de IP é uma funcionalidade de complementar a UDR. Esta definição de uma aplicação virtual permite que ele receba tráfego endereçado não para a aplicação e, em seguida, reencaminhe que o tráfego para um destino final.

Por exemplo, se o tráfego de AppVM01 faz uma solicitação para o servidor de DNS01, o UDR encaminha o tráfego para o firewall. Com o reencaminhamento IP ativado, o tráfego com o destino de DNS01 (10.0.2.4) é aceite pela aplicação da firewall (10.0.0.4) e, depois, reencaminhado para um destino final (10.0.2.4). Sem reencaminhamento IP está ativado na firewall, o tráfego não é aceite pela aplicação, apesar da tabela de rotas tem o firewall como salto seguinte.

> [!IMPORTANT]
> Lembre-se de ativar o reencaminhamento de IP em conjunto com o encaminhamento definido pelo utilizador.

Reencaminhamento de IP pode ser ativado com um único comando no momento da criação de VM. Chama a instância VM que é a sua aplicação virtual de firewall e o reencaminhamento de IP de ativação. Tenha em atenção que os itens em vermelho que começam com `$`, como `$VMName[0]`, são definidas pelo utilizador variáveis do script na seção referência deste documento. Zero entre parênteses Retos, `[0]`, representa a primeira VM na matriz de VMs. Para o script de exemplo para funcionar sem modificações, a primeira VM (VM 0) tem de ser o firewall. No script completo, o fragmento de código relevante é agrupado com os comandos UDR perto do fim.

```powershell
Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] | `
    Set-AzureIPForwarding -Enable
```

## <a name="network-security-groups"></a>Grupos de segurança de rede

Neste exemplo, crie um grupo de segurança de rede (NSG) e, em seguida, carregá-lo com uma única regra. O exemplo, em seguida, vincula o NSG apenas para as sub-redes de front-end e back-end (não o SecNet). A regra que carrega para o NSG é o seguinte:

* Qualquer tráfego (todas as portas) da internet para a rede virtual inteira (todas as sub-redes) é negado

Embora os NSGs são utilizados neste exemplo, seu principal propósito é como uma camada secundária de defesa contra má configuração manual. Deseja bloquear todo o tráfego de entrada da internet para as sub-redes front-end ou back-end. Tráfego apenas deve fluir através da sub-rede SecNet o firewall, após o qual o tráfego apenas apropriado deve é roteado para as sub-redes de front-end ou back-end. Além disso, as regras UDR encaminham qualquer tráfego que atinge as sub-redes de front-end ou back-end para o firewall. O firewall vê-lo como um fluxo de assimétrico e ignora o tráfego de saída.

Três camadas de segurança protegem as sub-redes de front-end e back-end:

1. Não existem pontos finais abertos nos serviços de cloud FrontEnd001 e BackEnd001
1. NSGs negam o tráfego da internet
1. A firewall remove-tráfego assimétrico

Um ponto interessante sobre o NSG neste exemplo é que ele contém apenas uma regra, mostrada abaixo. Esta regra nega o tráfego da internet para a rede virtual completo, incluindo a sub-rede de segurança.

```powershell
Get-AzureNetworkSecurityGroup -Name $NSGName | `
    Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
    from the internet" `
    -Type Inbound -Priority 100 -Action Deny `
    -SourceAddressPrefix INTERNET -SourcePortRange '*' `
    -DestinationAddressPrefix VIRTUAL_NETWORK `
    -DestinationPortRange '*' `
    -Protocol *
```

Porque o NSG está associado apenas para as sub-redes de front-end e back-end, a regra não é aplicada a entrada para a sub-rede de segurança de tráfego. Como resultado, o tráfego flui para a sub-rede de segurança.

```powershell
Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
    -SubnetName $FESubnet -VirtualNetworkName $VNetName

Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
    -SubnetName $BESubnet -VirtualNetworkName $VNetName
```

## <a name="firewall-rules"></a>Regras da firewall

Tem de criar regras de reencaminhamento na firewall. Uma vez que a firewall bloqueia ou encaminha todo o tráfego de entrada, saído e intra virtual rede, terá de muitas regras de firewall. Além disso, o firewall deve processar todo o tráfego de entrada para o endereço IP público do serviço de segurança (em portas diferentes). Para evitar mais tarde voltar a trabalhar, siga a prática recomendada por fluxos de lógicos antes de configurar as sub-redes e regras de firewall de criação de diagramas. A figura a seguir é uma visão lógica das regras de firewall para este exemplo:

![Vista lógica das regras de Firewall][2]

> [!NOTE]
> Portas de gestão podem variar consoante a aplicação de rede virtual. Este exemplo mostra um Barracuda NextGen Firewall que utiliza as portas 22, 801 e 807. Consulte a documentação do fornecedor da aplicação para encontrar as portas exatas para gerir o dispositivo.

### <a name="logical-rule-description"></a>Descrição da regra de lógica

Diagrama lógico acima não mostra a sub-rede de segurança porque o firewall é o único recurso dessa sub-rede. Este diagrama mostra as regras de firewall, como eles logicamente permitem ou negar fluxos de tráfego, mas não o real encaminhadas caminho. Além disso, as portas externas selecionadas para o tráfego de protocolo de ambiente de trabalho remoto (RDP) são portas ranged superior (8014 – 8026), escolhidas para facilitar a leitura alinhar com os dois últimos octetos dos endereços IP locais. Por exemplo, o endereço de servidor local 10.0.1.4 está associado a porta externa 8014. No entanto, poderia, usar as portas não conflitantes superior.

Terá dos seguintes tipos de regras para este exemplo:

* Regras de externas para tráfego de entrada:
  1. Regra de firewall de gestão: permite que o tráfego passar para as portas de gestão da aplicação virtual de rede.
  2. Regras RDP para cada servidor do windows: permite a gestão de servidores individuais através de RDP.  Consoante as capacidades da sua aplicação virtual de rede, poderá agrupar as regras em um.
  3. Regras de tráfego de aplicações: um para o tráfego da web de front-end e outra para o tráfego de back-end (por exemplo, servidor web para a camada de dados). A configuração dessas regras depende de arquitetura de rede e o tráfego de fluxos.

     * A primeira regra permite o tráfego de aplicativo real alcançar o servidor de aplicação. Ao contrário das regras de segurança, gestão e assim por diante, regras de aplicações permitem que os utilizadores externos ou serviços aceder as aplicações. Neste exemplo tem um servidor web único na porta 80, que permite uma única aplicação regra de firewall redirecionar o tráfego destinado a um endereço IP externo para em vez disso, encaminhar para o endereço IP interno do servidor web. A sessão de tráfego redirecionado é remapeada pelo NAT para o servidor interno.
     * A segunda regra de tráfego de aplicativo é a regra de back-end para permitir que o servidor web utilizar qualquer porta para encaminhar o tráfego para o servidor de AppVM01, mas não para o servidor de AppVM02.

* Regras internas para tráfego de rede do virtual intra:
  1. Saída para a regra de internet: permite que o tráfego de qualquer rede para passar para as redes selecionadas. Esta regra é, normalmente, um padrão na firewall, mas num Estado desabilitado. Ative esta regra para este exemplo.
  2. Regra de DNS: permite apenas tráfego DNS (porta 53) passar para o servidor DNS. Para este ambiente, a maioria do tráfego de front-end para back-end está bloqueado. Esta regra permite especificamente DNS a partir de qualquer sub-rede local.
  3. Regra de sub-rede à sub-rede: permite que qualquer servidor na sub-rede de back-end para ligar a qualquer servidor na sub-rede de front-end, mas não o inverso.

* Regra isento de falhas para o tráfego que não atenderem a qualquer um dos critérios acima:
  1. Todos os de tráfego de regra de negação: sempre a regra final em termos de prioridade. Se o fluxo de tráfego de mensagens em fila não conseguir corresponder a qualquer uma das regras anteriores, esta regra bloqueia-lo. É uma regra predefinida. Uma vez que ele normalmente é ativado, sem modificações são necessários.

> [!TIP]
> Na segunda regra de tráfego do aplicativo, qualquer porta é permitida para manter este exemplo simples. Num cenário real, deve utilizar a porta específica e intervalos de endereços para reduzir a superfície de ataque desta regra.


> [!IMPORTANT]
> Depois de criar as regras, é importante que reveja a prioridade de cada regra para se certificar de que o tráfego é permitido ou negado conforme pretendido. Neste exemplo, as regras são por ordem de prioridade. É fácil fica bloqueado fora da firewall se as regras são enganássemos ordenadas. Certifique-se definir a regra de firewall de gestão como a prioridade mais alta absoluta.

### <a name="rule-prerequisites"></a>Pré-requisitos de regra

Pontos finais públicos são necessários para a máquina virtual que executa o firewall. Estes pontos finais públicos tem de estar abertos para que a firewall pode processar o tráfego. Existem três tipos de tráfego neste exemplo:

1. Tráfego de gestão para controlar o firewall e regras de firewall
1. Tráfego RDP para controlar os servidores do windows
1. Tráfego de aplicativo

Os tipos de tráfego são apresentados na metade superior do firewall regras diagrama lógico acima.

> [!IMPORTANT]
> Lembre-se de que *todos os* tráfego é fornecido através da firewall. Para o ambiente de trabalho remoto para o servidor de IIS01, terá de ligar para o firewall na porta 8014 e, em seguida, permitir que a firewall para encaminhar a solicitação RDP internamente para a porta IIS01 RDP. O portal do Azure **Connect** botão não funcionará, porque não existe nenhum caminho direto de RDP para IIS01 como pode ver o portal. Todas as ligações da internet são o serviço de segurança e uma porta (por exemplo, secscv001.cloudapp.net:xxxx). O diagrama acima para o mapeamento de portas externa e interno de IP e de porta de referência.

É possível abrir um ponto final no momento da criação da VM ou após a compilação. O script de exemplo e o seguinte trecho de código abrem um ponto de extremidade quando a VM é criada.

Tenha em atenção que os itens que começam com `$`, tais como `$VMName[$i]`, são definidas pelo utilizador variáveis do script na seção de referência. O `[$i]` representa o número de matriz de uma VM específica numa matriz de VMs.

```powershell
Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
    -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
    Update-AzureVM
```

Apesar de não claramente é apresentado aqui devido a variáveis, só deve abrir pontos finais de serviço em nuvem da segurança. Esta precaução ajuda a garantir que o firewall processa todo o tráfego de entrada, se foi encaminhado, remapeado pelo NAT ou removida.

Instale um cliente de gestão num PC para gerir a firewall e criar as configurações necessárias. Para obter detalhes sobre como gerir a firewall ou outra NVA, consulte a documentação do fornecedor. O resto desta secção, bem como a **criação de regras de Firewall** secção descrevem a configuração do firewall. Utilize o cliente de gestão do fornecedor, não o portal do Azure ou o PowerShell.

Aceda a [Barracuda NG administrador](https://techlib.barracuda.com/NG61/NGAdmin) para transferir o cliente de gestão e saiba como ligar à firewall do Barracuda.

Depois de que tem sessão iniciada para o firewall, defina os objetos de rede e o serviço antes de criar as regras de firewall. Essas duas classes de objeto pré-requisitos podem tornar a criação de regras mais fácil.

Neste exemplo, defina três objetos com nome de rede para cada:

* Sub-rede front-end
* Sub-rede de back-end
* Endereço IP do servidor DNS

Para criar uma rede com o nome do dashboard de cliente Barracuda NG administrador:

1. Vá para o **separador configuração**.
1. Selecione **Ruleset** no **configuração operacionais** secção
1. Selecione **redes** sob a **Firewall objetos** menu.
1. Selecione **New** no **editar redes** menu.
1. Crie o objeto de rede ao adicionar o nome e o prefixo:

   ![Criar um objeto de rede de front-ed][3]

Os passos anteriores criam uma rede com o nome para a sub-rede do front-end. Crie um objeto semelhante para a sub-rede de back-end também. Agora as sub-redes podem ser referenciadas mais facilmente por nome nas regras da firewall.

Para o objeto de servidor DNS:

![Criar um objeto de servidor DNS][4]

Esta referência de endereço IP única é usada numa regra DNS mais tarde no documento.

A outra classe de objeto inclui objetos de serviços, que representam as portas de ligação de RDP para cada servidor. O objeto de serviço existente do RDP está vinculado à porta RDP padrão, 3389. Portanto, pode criar novos serviços para permitir o tráfego das portas externos (8014 8026). Também pode adicionar as novas portas para o serviço RDP existente. No entanto, para facilitar a demonstração, pode fazer uma regra de individual para cada servidor. Para criar uma nova regra RDP para um servidor a partir do dashboard de cliente da Barracuda NG administrador:

1. Vá para o **separador configuração**.
1. Selecione **Ruleset** no **configuração operacionais** secção.
1. Selecione **serviços** sob a **Firewall objetos** menu.
1. Desloque-se para baixo na lista de serviços e selecione **RDP**.
1. Com o botão direito e selecione cópia, em seguida, clique com botão direito e selecione colar.
1. Agora é um objeto de serviço RDP Copy1 que pode ser editado. Com o botão direito **RDP Copy1** e selecione **editar**.
1. O **editar o objeto de serviço** janela será exibida conforme mostrado aqui:

   ![Cópia da regra predefinida do RDP][5]

1. Edite os valores para representar o serviço RDP para um servidor específico. Para AppVM01, a regra RDP padrão deve ser modificada para um novo serviço de refletir **Name**, **Descrição**e a porta RDP externo utilizado no diagrama da figura 8. Tenha em atenção que as portas são alteradas do padrão de 3389 RDP para a porta externa para esse servidor específico. Por exemplo, a porta externa para AppVM01 é 8025. A regra do serviço modificado é mostrada aqui:

   ![Regra de AppVM01][6]

Repita este processo para criar serviços RDP para os restantes servidores: AppVM02 DNS01 e IIS01. Estes serviços tornam as regras na próxima seção mais simples de criar e mais óbvio.

> [!NOTE]
> Um serviço RDP para a firewall não é necessária porque o firewall VM é uma imagem baseada em Linux, portanto, é utilizado na porta 22 para a gestão de VM, em vez de RDP. Além disso, a porta 22 e duas outras portas são permitidas para a conectividade de gestão. Consulte a **regra de Firewall de gestão** na próxima seção.

### <a name="firewall-rules-creation"></a>Criação de regras de firewall

Existem três tipos de regras de firewall utilizadas neste exemplo, tudo com ícones distintos:

A regra de redirecionamento da aplicação: ![Ícone de redirecionamento da aplicação][7]

A regra NAT de destino: ![Ícone NAT de destino][8]

A regra de pass: ![Ícone de passagem][9]

Obter mais informações sobre estas regras podem ser encontradas no site da Barracuda.

Para criar as seguintes regras ou certifique-se de regras de predefinidas existente:

1. A partir do dashboard de cliente Barracuda NG administrador, vá para o **configuração** separador.
1. Na **configuração operacionais** secção, selecione **Ruleset**.
1. O **Main regras** grid mostra existente Active Directory e desativado regras nesta firewall. Selecione o verde **+** no canto superior direito para criar uma nova regra. Se a firewall está bloqueada para que as alterações, verá um botão marcado **bloqueio** e não é possível criar ou editar regras. Selecione o **bloqueio** botão para desbloquear o conjunto de regras e permitir a edição. Com uma regra que pretende editar e selecione o botão direito **Editar regra de**.

Depois de criar ou modificar quaisquer regras, emiti-las para o firewall e ativá-los. Caso contrário, as alterações de regra não entrem em vigor. O processo de ativação e de push é descrito em [regra ativação](#rule-activation).

Aqui estão as especificidades de cada regra necessário para concluir este exemplo:

* **Regra de firewall de gestão**: Esta regra de redirecionamento da aplicação permite que o tráfego passar para as portas de gestão do dispositivo virtual de rede, neste exemplo, um Barracuda NextGen Firewall. As portas de gestão são 801 807 e, opcionalmente, 22. As portas externas e internas são os mesmos, nenhuma conversão de porta. Esta regra é chamada de configuração-MGMT-acesso. É uma regra predefinida e ativado por predefinição no Barracuda NextGen Firewall, versão 6.1.
  
    ![Regra de firewall de gestão][10]

  > [!TIP]
  > É o espaço de endereços de origem nesta regra **qualquer**. Se os intervalos de endereços IP de gestão são conhecidos, reduzir este âmbito também reduz a superfície de ataque para as portas de gestão.

* **Regras RDP**:  Estas regras NAT de destino permitem a gestão dos servidores individuais através de RDP. Os campos críticos para estas regras são:
  * Origem. Para permitir RDP a partir de qualquer lugar, utilize a referência **qualquer** no campo de origem.
  * Serviço. Use o objeto de serviço RDP que criou anteriormente: **AppVM01 RDP**. As portas externas redirecionar para o endereço IP local do servidor e para a porta predefinida do RDP 3386. Esta regra específica é para acesso do RDP para AppVM01.
  * Destino. Utilize a porta local na firewall: **IP de Local de 1 de DCHP** ou **eth0** se estiver a utilizar IPs estáticos. O número de ordinal (eth0 eth1 e assim por diante) pode ser diferente se a sua aplicação de rede tiver várias interfaces de locais. O firewall utiliza esta porta para o envio e pode ser o mesmo que a porta de recebimento. O destino roteado real está na **lista de destino** campo.
  * Redirecionamento. Configure a informar a aplicação virtual onde, por fim, este tráfego de redirecionamento. O redirecionamento mais simples é colocar o IP no campo de lista de destino. Também pode especificar a porta e NAT redireciona a porta e o endereço IP. Se não especificar uma porta, a aplicação virtual utiliza a porta de destino no pedido de entrada.

    ![Regra de RDP do firewall][11]

    Crie regras RDP quatro:

    | Nome da Regra | Servidor | Serviço | Lista de destino |
    | --- | --- | --- | --- |
    | RDP-to-IIS01 |IIS01 |IIS01 RDP |10.0.1.4:3389 |
    | RDP-to-DNS01 |DNS01 |DNS01 RDP |10.0.2.4:3389 |
    | RDP-to-AppVM01 |AppVM01 |AppVM01 RDP |10.0.2.5:3389 |
    | RDP-to-AppVM02 |AppVM02 |AppVm02 RDP |10.0.2.6:3389 |

  > [!TIP]
  > Restrição o âmbito dos campos de origem e de serviço reduz a superfície de ataque. Utilize o âmbito mais limitado que permite a funcionalidade.

* **As regras de tráfego de aplicativo**: Há duas regras de tráfego de aplicativo. Um é para o tráfego de web de front-end. Abrange o outro tráfego de back-end, como o servidor web para a camada de dados. Estas regras dependem de arquitetura de rede e fluxos de tráfego.
  
  * A regra de front-end para tráfego web:
  
    ![Regra de firewall na Web][12]
  
    Esta regra NAT de destino permite tráfego de aplicativo real alcançar o servidor de aplicação. Ao contrário das regras de segurança, gestão e etcetera, regras de aplicações permitem que os utilizadores externos ou serviços aceder as aplicações. Neste exemplo tem um servidor web único na porta 80, que permite uma única aplicação regra de firewall redirecionar o tráfego destinado a um endereço IP externo para em vez disso, encaminhar para o endereço IP interno do servidor web. A sessão de tráfego redirecionado é remapeada pelo NAT para o servidor interno.

    > [!NOTE]
    > Não existe nenhuma porta atribuída no **lista de destino** campo. Portanto, a porta de entrada 80 (ou 443 para o serviço selecionado) é utilizada no redirecionamento do servidor web. Se o servidor web está à escuta numa porta diferente, como a 8080, pode atualizar o campo de lista de destino para 10.0.1.4:8080 para permitir o redirecionamento de porta também.
  
  * A regra de back-end permite ao servidor de web comunicar com o servidor de AppVM01, mas não AppVM02, via **qualquer** serviço:
  
    ![Regra de AppVM01 de firewall][13]
  
    Esta regra de aprovação permite que qualquer servidor IIS da sub-rede de front-end para alcançar AppVM01 (10.0.2.5) em qualquer porta usando todos os protocolos, para que os dados podem ser acedidos pelo aplicativo web.
  
    Nesta captura de ecrã `<explicit-dest>` é utilizada na **destino** campo para significar 10.0.2.5 como o destino. Pode especificar o endereço IP explicitamente, conforme mostrado na captura de ecrã. Também pode utilizar um objeto de rede nomeado como nos pré-requisitos para o servidor DNS. O administrador de firewall pode escolher o método a utilizar. Para adicionar 10.0.2.5 como um destino explícito, faça duplo clique na primeira linha em branco em `<explicit-dest>` e introduza o endereço na caixa de diálogo que é aberta.
  
    Com esta regra de pass, sem NAT é necessária, porque está a processar o tráfego interno. Pode definir o **método de ligação** para `No SNAT`.
  
    > [!NOTE]
    > A rede de origem nesta regra é qualquer recurso na sub-rede de front-end, se existir apenas um. Se a sua arquitetura Especifica um número conhecido dos servidores web, pode criar um recurso de objeto de rede para ser mais específico para esses endereços IP exatamente em vez de toda a sub-rede de front-end.

    > [!TIP]
    > Esta regra utiliza o serviço **qualquer** para facilitar a aplicação de exemplo configurar e utilizar. Ele permite que ICMPv4 (ping) numa única regra. No entanto, para reduzir o ataque de superfície por esse limite, recomendamos que restringir os serviços de portas e protocolos para o mínimo possível permitir que a operação de aplicação.

    > [!TIP]
    > Embora esta regra de exemplo utiliza `<explicit-dest>` referência, deve usar uma abordagem consistente em toda a configuração de firewall. Recomendamos usando um objeto de rede nomeado para facilitar a leitura mais fácil e de capacidade de suporte. O `<explicit-dest>` mostrado aqui é apenas mostrar um método alternativo de referência. Em geral, não recomendado, especialmente para configurações complexas.

* **Regra de saída à internet**: Esta regra de pass permite tráfego de qualquer rede de origem para passar para as redes de destino selecionadas. A Barracuda NextGen firewall geralmente tem isso esta regra "no" por padrão, mas num Estado desabilitado. Com o botão direito sobre esta regra para aceder a **ativar regra** comando. Modifique a regra, mostrada na captura de ecrã, para adicionar os objetos de rede para sub-redes de back-end e front-end para o atributo de origem desta regra. Estes objetos de rede que criou na secção pré-requisitos deste artigo.
  
    ![Regra de firewall de saída][14]

* **Regra de DNS**: Esta regra de pass permite apenas tráfego de DNS (porta 53) passar para o servidor DNS. Para este ambiente, a maioria do tráfego de front-end para back-end está bloqueado para que esta regra permite especificamente o tráfego DNS.
  
    ![Regra de firewall DNS][15]
  
    > [!NOTE]
    > O **método de ligação** está definida como `No SNAT` porque esta regra é para o IP interno para tráfego de endereço IP interno e não reencaminhar através de NAT é necessária.

* **Regra de sub-rede para sub-rede**: Esta regra de pass predefinida foi ativada e modificada para permitir que qualquer servidor na sub-rede de back-end para ligar a qualquer servidor na sub-rede de front-end. Esta regra coves apenas o tráfego interno por isso, o **método de ligação** pode ser definido como `No SNAT`.

    ![Regra de VNet de dentro do firewall][16]
  
    > [!NOTE]
    > O **bidirecionais** caixa de verificação não estiver selecionada aqui para que esta regra aplica-se em apenas uma direção. Uma ligação pode ser iniciada a partir da sub-rede de back-end para a rede de front-end, mas não o inverso. Se estiver selecionada a caixa de verificação, esta regra permitiria tráfego bidirecional, que especificamos como indesejáveis no diagrama lógico.

* **Negar a regra de tráfego de todos os**: Esta regra deve ser sempre a regra final em termos de prioridade. Se o fluxo de tráfego não corresponder a qualquer uma das regras anteriores, esta regra faz com que ele ser removida. A regra normalmente está ativada por predefinição, para que nenhuma modificação é necessários.
  
    ![Regra de negação de firewall][17]

> [!IMPORTANT]
> Depois de todas as regras anteriores são criadas, reveja a prioridade de cada regra para garantir que o tráfego é permitido ou negado conforme pretendido. Neste exemplo, as regras estão listadas na ordem em que devem aparecer na grelha de principal do cliente de gestão da Barracuda de regras de reencaminhamento.

## <a name="rule-activation"></a>Ativação de regra

Depois de modificar a conjunto de regras para cumprir as especificações do diagrama lógica, terá de carregar o conjunto de regras do firewall e ativá-lo.

![Ativação de regra de firewall][18]

Procure no canto superior direito da janela de cliente de gestão e selecione **enviar alterações** para carregar as regras de modificação para a firewall. Selecione **ativar**.

Quando ativar o conjunto de regras de firewall, este ambiente de exemplo está concluído.

## <a name="traffic-scenarios"></a>Cenários de tráfego

> [!IMPORTANT]
> Lembre-se de que *todos os* tráfego é fornecido através da firewall. Para o ambiente de trabalho remoto para o servidor de IIS01, terá de ligar para o firewall na porta 8014 e, em seguida, permitir que a firewall para encaminhar a solicitação RDP internamente para a porta IIS01 RDP. O portal do Azure **Connect** botão não funcionará, porque não existe nenhum caminho direto de RDP para IIS01 como pode ver o portal. Todas as ligações da internet são o serviço de segurança e uma porta (por exemplo, secscv001.cloudapp.net:xxxx). O diagrama acima para o mapeamento de portas externa e interno de IP e de porta de referência.

Nestes cenários, as seguintes regras de firewall devem estar no local:

1. Gestão da firewall (firewall Mgmt)
2. RDP para IIS01
3. RDP para DNS01
4. RDP para AppVM01
5. RDP para AppVM02
6. Tráfego da aplicação para a Web
7. Tráfego de aplicações para AppVM01
8. Saída à internet
9. Front-end para DNS01
10. Tráfego de sub-rede de dentro (back-end para front-end apenas)
11. Recusar tudo

O conjunto de regras de firewall reais provavelmente exigirá mais regras que aquelas neste exemplo. Há uma grande probabilidades de ter também a números de prioridade diferente. Deve consultar a esta lista e números associados para a prioridade relativa entre si. Por exemplo, a regra "RDP para IIS01" poderá ser a regra de número 5 na firewall de real, mas, desde que ele abaixo a "gestão da Firewall" e acima de "RDP para DNS01" da regra, o conjunto se alinha com a intenção desta lista. Esta lista também ajuda a simplificar as instruções para cenários que se seguem. Por exemplo, "regra de Firewall 9 (DNS)." Lembre-se de que as regras RDP quatro, coletivamente, são chamadas de "as regras RDP" quando o cenário de tráfego não está relacionada com RDP.

Lembre-se também de que os grupos de segurança de rede (NSGs) estão em vigor para tráfego de internet de entrada nas sub-redes de front-end e back-end.

### <a name="allowed-internet-to-web-server"></a>(Permitido) Internet para o servidor web

1. Um utilizador de internet solicita página HTTP SecSvc001.CloudApp.Net (serviço de nuvem com acesso à internet).
1. O serviço em nuvem passa o tráfego através de um ponto de extremidade aberto na porta 80 para a interface de firewall em 10.0.0.4:80.
1. Nenhum NSG é atribuído à sub-rede de segurança para que as regras NSG de sistema de permitir o tráfego para o firewall.
1. O tráfego chega a um endereço IP interno do firewall (10.0.1.4).
1. A firewall realiza o processamento da regra:
   1. Regra de firewall 1 (FW Mgmt) não se aplica. Mover para a próxima regra.
   1. Regras de firewall 2 a 5 (regras RDP) não se aplicam. Mover para a próxima regra.
   1. Regra de firewall 6 (aplicação: É aplicável a Web). O tráfego é permitido. Firewall redireciona o tráfego através do NAT para 10.0.1.4 (IIS01).
1. A sub-rede do front-end executa o processamento da regra de entrada:
   1. Regra NSG 1 (bloco internet) não se aplica. O firewall reencaminhado este tráfego através de NAT para que o endereço de origem está agora a firewall. Uma vez que a firewall está na sub-rede de segurança e é apresentado como o tráfego local para a sub-rede de front-end NSG, o tráfego é permitido. Mover para a próxima regra.
   1. As regras do NSG predefinido de permitir tráfego de sub-rede de sub-rede para que este tráfego é permitido. Pare o processamento da regra NSG.
1. IIS01 está à escuta de tráfego da web. Ele recebe essa solicitação e começa a processar o pedido.
1. IIS01 tenta iniciar uma sessão de FTP AppVM01 na sub-rede de back-end.
1. A rota UDR na sub-rede de front-end torna o firewall, o salto seguinte.
1. Não existem regras de saída na sub-rede de front-end para que o tráfego é permitido.
1. Firewall começa o processamento da regra:
   1. Regra de firewall 1 (FW Mgmt) não se aplica. Mover para a próxima regra.
   1. Regras de firewall 2 a 5 (regras RDP) não se aplicam. Mover para a próxima regra.
   1. Regra de firewall 6 (aplicação: Web) não se aplica. Mover para a próxima regra.
   1. Regra de firewall 7 (aplicação: back-end) é aplicável. O tráfego é permitido. O firewall reencaminha o tráfego para 10.0.2.5 (AppVM01).
1. A sub-rede de back-end executa o processamento da regra de entrada:
    1. Regra NSG 1 (bloco internet) não se aplica. Mover para a próxima regra.
    1. Regras NSG predefinidas permitem o tráfego de sub-rede de sub-rede. O tráfego é permitido. Pare o processamento da regra NSG.
1. AppVM01 recebe o pedido, inicia a sessão e responde.
1. A rota UDR na sub-rede de back-end torna o firewall, o salto seguinte.
1. Não há nenhuma saídas regras do NSG da sub-rede de back-end para que a resposta é permitida.
1. Uma vez que ele está retornando o tráfego numa sessão estabelecida, o firewall passa a resposta de volta para o servidor web (IIS01).
1. Sub-rede front-end executa o processamento da regra de entrada:
    1. Regra NSG 1 (bloco internet) não se aplica. Mover para a próxima regra.
    1. As regras NSG predefinidas de permitir tráfego de sub-rede de sub-rede para que este tráfego é permitido. Pare o processamento da regra NSG.
1. O servidor IIS recebe a resposta e concluir a transação com AppVM01. Em seguida, o servidor conclui a criação de resposta HTTP e envia-os para o requerente.
1. Não há nenhuma saídas regras do NSG da sub-rede de front-end para que a resposta é permitida.
1. A resposta HTTP atinge o firewall. Como é uma resposta a uma sessão NAT estabelecida, o firewall aceita-lo.
1. O firewall redireciona a resposta de volta para o utilizador de internet.
1. Não há regras de NSG de saída ou saltos UDR na sub-rede de front-end para que a resposta é permitida. O utilizador de internet recebe a página da web solicitado.

### <a name="allowed-internet-rdp-to-back-end"></a>(Permitido) RDP da Internet ao back-end

1. Um administrador do servidor na internet solicita uma sessão do RDP para AppVM01 via SecSvc001.CloudApp.Net:8025. 8025 é o número da porta atribuído ao utilizador para a regra de firewall 4 (RDP AppVM01).
1. O serviço em nuvem passa o tráfego através do ponto de final aberto na porta 8025 à interface de firewall no 10.0.0.4:8025.
1. Nenhum NSG é atribuído à sub-rede de segurança para que as regras do NSG sistema permitem o tráfego para o firewall.
1. A firewall realiza o processamento da regra:
   1. Regra de firewall 1 (FW Mgmt) não se aplica. Mover para a próxima regra.
   1. Regra de firewall 2 (RDP IIS) não se aplica. Mover para a próxima regra.
   1. Regra de firewall 3 (RDP DNS01) não se aplica. Mover para a próxima regra.
   1. Regra de firewall 4 (RDP AppVM01) aplicam-se para que o tráfego é permitido. O firewall redireciona-lo através de NAT para 10.0.2.5:3386 (porta RDP no AppVM01).
1. A sub-rede de back-end executa o processamento da regra de entrada:
   1. Regra NSG 1 (bloco internet) não se aplica. O firewall reencaminhado este tráfego através de NAT para que o endereço de origem está agora firewall que está na sub-rede de segurança. Ele é visto por NSG de sub-rede de back-end ser tráfego local e é permitido. Mover para a próxima regra.
   1. As regras do NSG predefinido de permitir tráfego de sub-rede de sub-rede para que este tráfego é permitido. Pare o processamento da regra NSG.
1. AppVM01 está à escuta de tráfego RDP e responde.
1. Não há nenhuma saídas regras do NSG, para aplicam regras predefinidas. Tráfego de retorno é permitido.
1. UDR encaminha o tráfego de saída para o firewall como salto seguinte.
1. Uma vez que ele está retornando o tráfego numa sessão estabelecida, o firewall passa a resposta de volta para o utilizador de internet.
1. Sessão RDP está ativada.
1. AppVM01 solicita a palavra-passe de nome de utilizador.

### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Permitido) Pesquisa de DNS do servidor Web no servidor DNS

1. Servidor de Web IIS01 solicita um alimentação de dados em http\:\/\/www.data.gov, mas tem de resolver o endereço.
1. A configuração de rede para as listas de rede virtual DNS01 (10.0.2.4 na sub-rede de back-end) que o servidor DNS principal. IIS01 envia o pedido DNS para DNS01.
1. UDR encaminha o tráfego de saída para o firewall como salto seguinte.
1. Não existem regras NSG de saída são ligadas à sub-rede de front-end. O tráfego é permitido.
1. A firewall executa o processamento da regra:
   1. Regra de firewall 1 (FW Mgmt) não se aplica. Mover para a próxima regra.
   1. Regra de firewall 2 a 5 (regras de RDP) não se aplicam. Mover para a próxima regra.
   1. Regras de firewall 6 e 7 (regras de aplicações) não se aplicam. Mover para a próxima regra.
   1. A regra de firewall 8 (para internet) não se aplica. Mover para a próxima regra.
   1. Aplicar a regra de firewall 9 (DNS). O tráfego é permitido. Firewall reencaminha o tráfego para 10.0.2.4 (DNS01).
1. A sub-rede de back-end executa o processamento da regra de entrada:
   1. Regra NSG 1 (bloco internet) não se aplica. Mover para a próxima regra.
   1. Regras NSG predefinidas permitem o tráfego de sub-rede de sub-rede. O tráfego é permitido. Pare o processamento da regra NSG.
1. O servidor DNS recebe o pedido.
1. O servidor DNS não tem o endereço em cache e pede-lhe um servidor DNS de raiz na internet.
1. UDR encaminha o tráfego de saída para o firewall como salto seguinte.
1. Existem não existem regras NSG saídas na sub-rede de back-end para o tráfego é permitido.
1. A firewall executa o processamento da regra:
   1. Regra de firewall 1 (FW Mgmt) não se aplica. Mover para a próxima regra.
   1. Regra de firewall 2 a 5 (regras de RDP) não se aplicam. Mover para a próxima regra.
   1. Regras de firewall 6 e 7 (regras de aplicações) não se aplicam. Mover para a próxima regra.
   1. Aplicam-se a regra de firewall 8 (para a internet). O tráfego é permitido. A sessão é redirecionada através do SNAT para o servidor DNS de raiz na internet.
1. O DNS da internet servidor responde. Esta sessão foi iniciada a partir da firewall para que a resposta é aceite pelo firewall.
1. Esta sessão já estiver estabelecida, de modo a firewall reencaminha a resposta para o servidor de início, DNS01.
1. A sub-rede de back-end executa o processamento da regra de entrada:
    1. Regra NSG 1 (bloco internet) não se aplica. Mover para a próxima regra.
    1. Regras NSG predefinidas permitem o tráfego de sub-rede de sub-rede para este tráfego é permitido. Pare o processamento da regra NSG.
1. O servidor DNS recebe e armazena em cache a resposta e, em seguida, responde ao pedido inicial para IIS01.
1. A rota UDR na sub-rede de back-end torna o firewall, o salto seguinte.
1. Não existem regras NSG saídas existem na sub-rede de back-end para que o tráfego é permitido.
1. Esta sessão já é estabelecida na firewall para que a firewall redireciona a resposta de volta para o servidor IIS.
1. A sub-rede do front-end executa o processamento da regra de entrada:
    1. Não existe nenhuma regra NSG para tráfego de entrada da sub-rede de back-end para a sub-rede do front-end para que nenhuma das regras do NSG aplicam-se.
    1. A regra predefinida do sistema permite que o tráfego entre sub-redes. O tráfego é permitido.
1. IIS01 recebe a resposta do DNS01.

### <a name="allowed-back-end-server-to-front-end-server"></a>(Permitido) Servidor de back-end para o servidor front-end

1. Um administrador com sessão iniciado no AppVM02 através de RDP solicita um ficheiro diretamente a partir do servidor de IIS01 através do Explorador de ficheiros do windows.
1. A rota UDR na sub-rede de back-end torna o firewall, o salto seguinte.
1. Não há nenhuma saídas regras do NSG da sub-rede de back-end para que a resposta é permitida.
1. A firewall realiza o processamento da regra:
   1. Regra de firewall 1 (FW Mgmt) não se aplica. Mover para a próxima regra.
   1. Regra de firewall 2 a 5 (regras RDP) não se aplicam. Mover para a próxima regra.
   1. Regras de firewall 6 e 7 (regras de aplicações) não se aplicam. Mover para a próxima regra.
   1. A regra de firewall 8 (para internet) não se aplica. Mover para a próxima regra.
   1. Regra de firewall 9 (DNS) não se aplica. Mover para a próxima regra.
   1. Aplicar a regra de firewall 10 (Intra-sub-rede). O tráfego é permitido. O firewall passa o tráfego para 10.0.1.4 (IIS01).
1. A sub-rede do front-end começa o processamento da regra de entrada:
   1. 1 de regra de NSG (bloco internet) não se aplicam, mudar para a próxima regra
   1. As regras NSG predefinidas de permitir tráfego de sub-rede de sub-rede para que o tráfego é permitido. Pare o processamento da regra NSG.
1. Partindo do princípio de autenticação e autorização adequadas, IIS01 aceita a solicitação e responde.
1. A rota UDR na sub-rede de front-end torna o firewall, o salto seguinte.
1. Não há nenhuma saídas regras do NSG da sub-rede de front-end para que a resposta é permitida.
1. Esta sessão já existe na firewall para que esta resposta é permitida. O firewall retorna a resposta ao AppVM02.
1. A sub-rede de back-end executa o processamento da regra de entrada:
    1. Regra NSG 1 (bloco internet) não se aplica. Mover para a próxima regra.
    2. As regras de NSG predefinidas de permitir tráfego de sub-rede de sub-rede para que o tráfego é permitido. Pare o processamento da regra NSG.
1. AppVM02 recebe a resposta.

### <a name="denied-internet-direct-to-web-server"></a>(Negado) Internet direta para o servidor web

1. Um utilizador de internet tenta aceder ao servidor de web de IIS01 através do serviço de FrontEnd001.CloudApp.Net.
1. Existem não existem pontos finais aberta para tráfego HTTP para que este tráfego não passa através do serviço de nuvem. O tráfego não alcançar o servidor.
1. Se os pontos finais estiverem abertos por algum motivo, o NSG (bloco internet) na sub-rede de front-end bloqueia este tráfego.
1. Por fim, a rota UDR a sub-rede de front-end envia qualquer tráfego de saída de IIS01 à firewall como salto seguinte. O firewall o vê como tráfego assimétrico e ignora a resposta de saída.

>Assim, há, pelo menos, três camadas independentes de defesa entre a internet e IIS01. O serviço em nuvem impede o acesso não autorizado ou inadequado.

### <a name="denied-internet-to-back-end-server"></a>(Negado) Internet para o servidor back-end

1. Um utilizador de internet tenta acessar um arquivo no AppVM01 através do serviço de BackEnd001.CloudApp.Net.
2. Não existem pontos finais estão abertas para a partilha de ficheiros para que este pedido não passa o serviço em nuvem. O tráfego não alcançar o servidor.
3. Se os pontos finais estiverem abertos por algum motivo, o NSG (bloco internet) bloqueia este tráfego.
4. Por fim, a rota UDR envia qualquer tráfego de saída de AppVM01 à firewall como salto seguinte. O firewall o vê como tráfego assimétrico e ignora a resposta de saída.

> Assim, há, pelo menos, três camadas independentes de defesa entre a internet e AppVM01. O serviço em nuvem impede o acesso não autorizado ou inadequado.

### <a name="denied-front-end-server-to-back-end-server"></a>(Negado) Servidor front-end para o servidor back-end

1. IIS01 fica comprometido e está a executar o código mal-intencionado tentar verificar os servidores de sub-rede de back-end.
1. A rota UDR de sub-rede de front-end envia qualquer tráfego de saída de IIS01 à firewall como salto seguinte. A VM comprometida não é possível alterar esse roteamento.
1. O firewall processa o tráfego. Se a solicitação for para AppVM01 ou para o servidor DNS para pesquisas de DNS, a firewall pode potencialmente permitir o tráfego devido a regras de Firewall, 7 e 9. Todos os outros tráfegos está bloqueado pela regra de Firewall 11 (negar todos).
1. Se ativar a deteção de ameaças avançada na firewall, o tráfego que contém as assinaturas conhecidas ou padrões que sinalizar uma regra avançada contra ameaças possível que seja impedido. Esta medida pode trabalhar, mesmo que o tráfego é permitido de acordo com as regras de reencaminhamento básicos discutidos neste artigo. Deteção de ameaças avançada não é abrangida neste documento. Consulte a documentação do fornecedor para a sua aplicação de rede específicas capacidades de ameaças avançadas.

### <a name="denied-internet-dns-lookup-on-dns-server"></a>(Negado) Pesquisa de DNS de Internet no servidor DNS

1. Um utilizador de internet tenta procurar um registo DNS interno no DNS01 através do serviço de BackEnd001.CloudApp.Net.
1. Uma vez que não existem não existem pontos finais aberta para tráfego de DNS, este tráfego não passa através do serviço de nuvem. Ele não atinge o servidor.
1. Se os pontos finais estiverem abertos por algum motivo, a regra NSG (bloco internet) na sub-rede de front-end bloqueia este tráfego.
1. Por fim, a sub-rede de back-end rota UDR envia qualquer tráfego de saída de DNS01 à firewall como salto seguinte. O firewall vê isso como tráfego assimétrico e ignora a resposta de saída.

> Assim, há, pelo menos, três camadas independentes de defesa entre a internet e DNS01. O serviço em nuvem impede o acesso não autorizado ou inadequado.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(Negado) Internet para o acesso através da firewall do SQL

1. Um utilizador de internet solicita dados SQL do serviço de nuvem de acesso à internet SecSvc001.CloudApp.Net.
1. Existem não existem pontos finais aberta para o SQL para que este tráfego não passa o serviço em nuvem. Ele não atinge a firewall.
1. Se pontos de extremidade do SQL estão abertos por algum motivo, a firewall realiza o processamento da regra:
   1. Regra de firewall 1 (FW Mgmt) não se aplica. Mover para a próxima regra.
   1. Regras de firewall 2 a 5 (regras de RDP) não se aplicam. Mover para a próxima regra.
   1. Regras de firewall 6 e 7 (regras de aplicações) não se aplicam. Mover para a próxima regra.
   1. A regra de firewall 8 (para internet) não se aplica. Mover para a próxima regra.
   1. Regra de firewall 9 (DNS) não se aplica. Mover para a próxima regra.
   1. Regra de firewall 10 (Intra-sub-rede) não se aplica. Mover para a próxima regra.
   1. Aplicar a regra de firewall 11 (negar todos). O tráfego é bloqueado. Pare o processamento da regra.

## <a name="references"></a>Referências

Esta secção contém os seguintes itens:

* Script completo. Guarde-o num arquivo de script do PowerShell.
* Configuração de rede. Guarde-o para um ficheiro denominado NetworkConf2.xml.

Modifique as variáveis definidas pelo usuário nos arquivos conforme necessário. Execute o script e, em seguida, siga as instruções de configuração de regra de Firewall apresentadas anteriormente neste artigo.

### <a name="full-script"></a>Script completo

Depois de definir as variáveis definidas pelo utilizador, execute este script para:

1. Ligar a uma subscrição do Azure
1. Criar uma nova conta de armazenamento
1. Criar uma nova rede virtual e três sub-redes, conforme definido no ficheiro de configuração de rede
1. Criar cinco máquinas virtuais: uma firewall e quatro VMs do Windows Server
1. Configure o UDR:
   1. Criar duas novas tabelas de rota
   1. Adicionar rotas para as tabelas
   1. Vincular tabelas para sub-redes apropriadas
1. Ativar o reencaminhamento de IP na NVA
1. Configure o NSG:
   1. Crie um NSG
   1. Adicionar uma regra
   1. Associar o NSG para as sub-redes apropriadas

Execute este PowerShell script localmente num internet ligado PC ou servidor.

> [!IMPORTANT]
> Quando executar esse script, avisos ou outras mensagens informativas poderão ser exibido no PowerShell. Apenas mensagens de erro vermelho são o motivo de preocupação.

```powershell
    <#
     .SYNOPSIS
      Example of DMZ and User Defined Routing in an isolated network (Azure only, no hybrid connections)

     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Three new cloud services
       - Three Subnets (SecNet, FrontEnd, and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - IP Forwarding from the FireWall out to the internet
       - User Defined Routing FrontEnd and BackEnd Subnets to the NVA

      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Everyone's security requirements are different and can be addressed in a myriad of ways.
      Please be sure that any sensitive data or applications are behind the appropriate
      layer(s) of protection. This script serves as an example of some of the techniques
      that can be used, but should not be used for all scenarios. You are responsible to
      assess your security needs and the appropriate protections needed, and then effectively
      implement those protections.

      Security Service (SecNet subnet 10.0.0.0/24)
       myFirewall - 10.0.0.4

      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.4

      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6

    #>

    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()

    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section

      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"

      # Service Details
        $SecureService = "SecSvc001"
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $VNetPrefix = "10.0.0.0/16"
        $SecNet = "SecNet"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf3.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # UDR Details
        $FERouteTableName = "FrontEndSubnetRouteTable"
        $BERouteTableName = "BackEndSubnetRouteTable"

      # NSG Details
        $NSGName = "MyVNetSG"

    # User Defined VM Specific Config
        # Note: To ensure UDR and IP forwarding is setup
        # properly this script requires VM 0 be the NVA.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $SecureService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $SecNet
          $VMIP += "10.0.0.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 2 - The First Application Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Application Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"

        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No User Defined Variables or   #
    # Configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) {
            Write-Host "Fatal Error: This storage account name is already in use, please pick a different name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

    # Validation
    $FatalError = $false

    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}

    If (Test-AzureName -Service -Name $SecureService) { 
        Write-Host "The SecureService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use" -ForegroundColor Green}

    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $SecureService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: All traffic goes through the firewall, so we'll need to set up all ports here.
                #       Also, the firewall will be redirecting traffic to a new IP and Port in a forwarding
                #       rule, so all of these endpoint have the same public and local port and the firewall
                #       will do the mapping, NATing, and/or redirection as declared in the firewall rules.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPWeb"    -Protocol tcp -PublicPort 8014 -LocalPort 8014 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp1"   -Protocol tcp -PublicPort 8025 -LocalPort 8025 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp2"   -Protocol tcp -PublicPort 8026 -LocalPort 8026 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPDNS01"  -Protocol tcp -PublicPort 8024 -LocalPort 8024 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "RemoteDesktop" | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                }
            $i++
        }

    # Configure UDR and IP Forwarding
        Write-Host "Configuring UDR" -ForegroundColor Cyan

      # Create the Route Tables
        Write-Host "Creating the Route Tables" -ForegroundColor Cyan 
        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"
        New-AzureRouteTable -Name $FERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $FESubnet subnet"

      # Add Routes to Route Tables
        Write-Host "Adding Routes to the Route Tables" -ForegroundColor Cyan 
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $FEPrefix `
            -NextHopType VNETLocal

      # Associate the Route Tables with the Subnets
        Write-Host "Binding Route Tables to the Subnets" -ForegroundColor Cyan 
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $BESubnet `
            -RouteTableName $BERouteTableName
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $FESubnet `
            -RouteTableName $FERouteTableName

     # Enable IP Forwarding on the Virtual Appliance
        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] `
            |Set-AzureIPForwarding -Enable

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG Rule
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 100 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *

      # Assign the NSG to two Subnets
        # The NSG is *not* bound to the Security Subnet. The result
        # is that internet traffic flows only to the Security subnet
        # since the NSG bound to the FrontEnd and BackEnd subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install BackEnd resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
```

### <a name="network-config-file"></a>Ficheiro de configuração de rede

Guarde este ficheiro XML com localização atualizada. Alterar o `$NetworkConfigFile` variáveis no script completo acima para ligar ao ficheiro de configuração de rede guardado.

```xml
    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="SecNet">
                <AddressPrefix>10.0.0.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
```

## <a name="next-steps"></a>Passos Seguintes

Pode instalar um aplicativo de exemplo para ajudar com este exemplo de rede de perímetro.

> [!div class="nextstepaction"]
> [Script de aplicação de exemplo](./virtual-networks-sample-app.md)

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "Rede de Perímetro bidirecional com NVA, um NSG e UDR"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "Vista lógica das regras de Firewall"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "Criar um objeto de rede de front-end"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "Criar um objeto de servidor DNS"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "Cópia da regra predefinida do RDP"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "Regra de AppVM01"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "Ícone de redirecionamento da aplicação"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "Ícone NAT de destino"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "Ícone de passagem"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "Regra de firewall de gestão"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "Regra de RDP do firewall"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "Regra de firewall na Web"
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "Regra de AppVM01 de firewall"
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "Regra de firewall de saída"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "Regra de firewall DNS"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "Regra de VNet de dentro do firewall"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "Regra de negação de firewall"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "Ativação de regra de firewall"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
