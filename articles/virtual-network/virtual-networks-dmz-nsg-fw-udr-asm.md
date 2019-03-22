---
title: Exemplo de rede de Perímetro – criar uma rede de Perímetro para proteger redes com uma Firewall, UDR e NSG | Documentos da Microsoft
description: Crie uma rede de Perímetro com uma Firewall, definidas pelo utilizador (UDR) encaminhamento e grupos de segurança de rede (NSG)
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
ms.openlocfilehash: 9632bd339956aff7558461ed391cdd21c92f06ad
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57995162"
---
# <a name="example-3--build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg"></a>Exemplo 3 – criar uma rede de Perímetro para proteger redes com uma Firewall, UDR e NSG
[Regressar à página de práticas recomendada de segurança limites][HOME]

Neste exemplo irá criar uma rede de Perímetro com uma firewall, quatro servidores windows, encaminhamento de definido pelo utilizador, o reencaminhamento IP e grupos de segurança de rede. Ele irá também explicar cada um dos comandos relevantes para fornecer uma compreensão mais aprofundada de cada passo. Também há uma seção de cenário de tráfego para fornecer um detalhado passo a passo de como o tráfego passa pelas camadas de defesa na rede de Perímetro. Finalmente, as referências de seção é o código completo e a instrução para criar este ambiente de teste e experimente com vários cenários. 

![Rede de Perímetro bidirecional com NVA, um NSG e UDR][1]

## <a name="environment-setup"></a>Configuração do ambiente
Neste exemplo, existe uma subscrição que contém o seguinte:

* Três serviços de cloud: "SecSvc001", "FrontEnd001" e "BackEnd001"
* Uma rede Virtual "CorpNetwork", com três sub-redes: "SecNet", "FrontEnd" e "Back-end"
* Uma aplicação virtual de rede, neste exemplo, um firewall, ligado à subrede SecNet
* Windows Server que representa um servidor de web de aplicações ("IIS01")
* Servidores de duas janelas que representem o aplicativo novamente terminam servidores ("AppVM01", "AppVM02")
* Um servidor do Windows que representa um servidor DNS ("DNS01")

A seção de referências abaixo é um script do PowerShell que criará a maior parte do ambiente descrito acima. A criar as VMs e redes virtuais, embora são efetuados pelo script de exemplo, não estão descritas em detalhe neste documento.

Para criar o ambiente:

1. Guarde o ficheiro de xml de configuração de rede incluído na seção referências (atualizado com os nomes, a localização e o IP endereços de acordo com o cenário determinado)
2. Atualize as variáveis de utilizador no script para corresponder ao ambiente de que script está a ser executado em (subscrições, os nomes de serviço, etc.)
3. Execute o script do PowerShell

**Nota**: A região demonstrada no script do PowerShell têm de corresponder a região demonstrada no ficheiro xml de configuração de rede.

Assim que o script é executado com êxito os passos seguintes do script posterior podem ser aumentados:

1. Configurar as regras de firewall, isto é explicado na seção abaixo: Descrição da regra de firewall.
2. Opcionalmente, na seção referências são dois scripts para configurar o servidor web e o servidor de aplicações com uma aplicação web simples para permitir que o teste com esta configuração de rede de Perímetro.

Assim que o script é executado com êxito a firewall, regras, terá de ser concluída, isto é explicado na seção intitulada: Regras de firewall.

## <a name="user-defined-routing-udr"></a>Encaminhamento (UDR) definido pelo utilizador
Por predefinição, as seguintes rotas de sistema são definidas como:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

O VNETLocal é sempre o s endereços definidos da VNet para essa rede específica (ie, será alterada de VNet para VNet dependendo de como cada VNet específico é definido). As rotas de sistema restantes são estáticas e predefinido, conforme apresentado acima.

Quanto à prioridade, as rotas são processadas por meio do método de correspondência de prefixo mais longo (LPM), assim, a rota mais específica na tabela aplicaria para um determinado endereço de destino.

Por conseguinte, o tráfego (por exemplo, para o servidor de DNS01, 10.0.2.4) se destina a rede local (10.0.0.0/16) seria encaminhado entre a VNet para o destino devido a rota 10.0.0.0/16. Em outras palavras, para 10.0.2.4, a rota de 10.0.0.0/16 é a rota mais específica, apesar dos 10.0.0.0/8 e 0.0.0.0/0 também poderiam se aplicam, mas uma vez que elas são menos específicas não afetam este tráfego. Portanto, o tráfego para 10.0.2.4 seria ter um salto seguinte da VNet em modo de local e simplesmente encaminhar para o destino.

Se o tráfego foi destinado para 10.1.1.1, por exemplo, não aplica a rota de 10.0.0.0/16, mas o 10.0.0.0/8 seria mais específica, e isso seria o tráfego removido ("preto holed"), uma vez que o próximo salto é Null. 

Se o destino não foi aplicado a qualquer um dos prefixos Null ou os prefixos de VNETLocal, então, cumprir, pelo menos, específicos encaminhar, 0.0.0.0/0 e ser encaminhado para a Internet como salto seguinte e, portanto, o limite de internet do Azure.

Se existirem dois prefixos idênticos na tabela de rotas, segue-se a ordem de preferência com base no atributo de "origem" rotas:

1. "VirtualAppliance" = uma rota de definidas de utilizador adicionadas manualmente à tabela
2. "O VPNGateway" = uma rota de dinâmicos do protocolo BGP (quando utilizado com redes híbridas), adicionado por um protocolo de rede dinâmica, estas rotas podem ser alterados ao longo do tempo como o protocolo dinâmico automaticamente reflete as alterações na rede em modo de peering
3. "Padrão" = as rotas de sistema, local VNet e as entradas estáticas, como mostrado na tabela de rotas acima.

> [!NOTE]
> Agora, pode utilizar definida pelo utilizador encaminhamento (UDR) com o ExpressRoute e Gateways de VPN para forçar a saída e entrada em vários locais tráfego seja roteado para uma aplicação virtual de rede (NVA).
> 
> 

#### <a name="creating-the-local-routes"></a>Criar as rotas locais
Neste exemplo, são necessárias duas tabelas de roteamento, um de cada para as sub-redes de front-end e back-end. Cada tabela é carregada com rotas estáticas adequadas para a sub-rede especificada. Para efeitos deste exemplo, cada tabela tem três formas:

1. Tráfego de sub-rede local com nenhum próximo salto definido para permitir o tráfego de sub-rede local ignorar a firewall
2. Tráfego de rede virtual com um salto seguinte definido como firewall, esta ação substitui a regra predefinida que permita o tráfego de VNet local encaminhar diretamente
3. Todos os tráfego restante (0/0) com um salto seguinte definido como o firewall

Assim que as tabelas de encaminhamento são criadas eles estão ligados a suas sub-redes. Para a sub-rede de front-end tabela de roteamento, uma vez criado e associado à sub-rede deve ter um aspeto semelhante a esta:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active


Neste exemplo, os comandos seguintes são utilizados para criar a tabela de rotas, adicione uma rota definida pelo utilizador e, em seguida, ligar a tabela de rotas a uma sub-rede (nota; todos os itens abaixo que começa com um sinal de dólar (p. ex.: $BESubnet) são variáveis definidas pelo utilizador do script no seção referência deste documento):

1. Primeiro tem de ser criada a tabela de encaminhamento de base. Este fragmento mostra a criação da tabela para a sub-rede de back-end. No script, uma tabela correspondente também é criada para a sub-rede de front-end.

   ```powershell
   New-AzureRouteTable -Name $BERouteTableName `
       -Location $DeploymentLocation `
       -Label "Route table for $BESubnet subnet"
   ```

2. Depois de criar a tabela de rotas, é possível adicionar rotas definidas pelo utilizador de específicos. Desta abreviada, todo o tráfego (0.0.0.0/0) será encaminhado através da mesma (uma variável, $VMIP [0], é usada para transmitir o endereço IP atribuído quando a aplicação virtual foi criada anteriormente no script). No script, uma regra correspondente também é criada na tabela de front-end.

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
       -NextHopType VirtualAppliance `
       -NextHopIpAddress $VMIP[0]
   ```

3. A entrada de rota acima irão substituir a "0.0.0.0/0" rota predefinida, mas a regra predefinida do 10.0.0.0/16 ainda existentes que permitiria que o tráfego VNet para encaminhar diretamente para o destino e não para a aplicação de rede Virtual. Para correto esse comportamento, a regra de siga tem de ser adicionado.

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
       -NextHopType VirtualAppliance `
       -NextHopIpAddress $VMIP[0]
   ```

4. Neste momento, há uma opção de ser feitas. Com as rotas de dois acima todo o tráfego irá encaminhar para a firewall para avaliação, até mesmo tráfego numa única sub-rede. Isso pode ser desejável, no entanto, para permitir o tráfego dentro de uma sub-rede para encaminhar a localmente, sem o envolvimento do firewall, um terceiro, pode ser adicionada a regra muito específica. Esta rota Estados destine de qualquer endereço de sub-rede local pode simplesmente lá encaminham diretamente (NextHopType = VNETLocal).

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
           -NextHopType VNETLocal
   ```

5. Por fim, com a tabela de roteamento criado e preenchido com um rotas definidas pelo utilizador, a tabela tem agora de ser vinculada a uma sub-rede. No script, a tabela de rotas de front-end também está vinculada à sub-rede de front-end. Aqui está o script de enlace para a sub-rede de back-end.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
       -SubnetName $BESubnet `
       -RouteTableName $BERouteTableName
   ```

## <a name="ip-forwarding"></a>Reencaminhamento IP
Um recurso de complementar a UDR, é o reencaminhamento IP. Esta é uma definição de uma aplicação Virtual que permite que ele receba tráfego endereçado não especificamente para a aplicação e, em seguida, reencaminhe que o tráfego para um destino final.

Por exemplo, se o tráfego de AppVM01 faz um pedido para o servidor de DNS01, UDR seria encaminhar isso para o firewall. Com o reencaminhamento IP ativado, o tráfego para o destino de DNS01 (10.0.2.4) serão aceites pela aplicação (10.0.0.4) e, depois, reencaminhado para um destino final (10.0.2.4). Sem o reencaminhamento IP ativada na Firewall, o tráfego não será Aceito ou não pela aplicação, apesar da tabela de rotas tem o firewall como salto seguinte. 

> [!IMPORTANT]
> É essencial lembrar-se ativar o reencaminhamento de IP em conjunto com o encaminhamento de definido pelo utilizador.
> 
> 

Configurar o reencaminhamento IP é um comando único e pode ser feito no momento da criação de VM. Para o fluxo de neste exemplo, o trecho de código é até o final do script e agrupados com os comandos UDR:

1. Chamar a instância VM que é a sua aplicação virtual, o firewall nesse caso e ativar o reencaminhamento de IP (Observe; qualquer item no início vermelho com um sinal de dólar (p. ex.: $VMName[0]) é uma variável de definidas pelo utilizador a partir do script na seção referência deste documento. Zero entre parênteses Retos, [0], representa a primeira VM na matriz de VMs, para que o script de exemplo funcionar sem modificações, a primeira VM (VM 0) têm de ser o firewall):

    ```powershell
    Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] | `
        Set-AzureIPForwarding -Enable
    ```

## <a name="network-security-groups-nsg"></a>Grupos de Segurança de Rede (NSG)
Neste exemplo, um grupo NSG é criado e, em seguida, é carregado com uma única regra. Este grupo, em seguida, está associado apenas para as sub-redes de front-end e back-end (não o SecNet). Declarativamente a seguinte regra está sendo criada:

1. Qualquer tráfego (todas as portas) da Internet para a VNet completa (todas as sub-redes) é negado

Embora os NSGs são utilizados neste exemplo, o objetivo principal é como uma camada secundária de defesa contra má configuração manual. Queremos bloquear o tráfego de toda a entrada da internet a um front-end ou sub-redes de back-end, o tráfego apenas devem fluir através da sub-rede SecNet o firewall (e, em seguida, se apropriado para o front-end ou back-end sub-redes). Além disso, com as regras UDR em vigor, qualquer tráfego que passou a fazer para as sub-redes de front-end ou back-end seria direcionado horizontalmente para a firewall (graças à UDR). O firewall teria visto como um fluxo de assimétrico e seria remover o tráfego de saída. Assim, há três camadas de segurança protegendo as sub-redes de front-end e back-end; 1) não existem pontos finais abertos no FrontEnd001 e BackEnd001 serviços cloud, 2) NSGs negar o tráfego da Internet, 3) o tráfego de assimétrico da colocar de firewall.

Um ponto interessante sobre o grupo de segurança de rede neste exemplo é que ele contém apenas uma regra, conforme mostrada abaixo, que é negar o tráfego de internet para toda a rede virtual que inclui a sub-rede de segurança. 

```powershell
Get-AzureNetworkSecurityGroup -Name $NSGName | `
    Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
    from the Internet" `
    -Type Inbound -Priority 100 -Action Deny `
    -SourceAddressPrefix INTERNET -SourcePortRange '*' `
    -DestinationAddressPrefix VIRTUAL_NETWORK `
    -DestinationPortRange '*' `
    -Protocol *
```

No entanto, como o NSG está ligado apenas para as sub-redes de front-end e back-end, a regra não é processada no tráfego de entrada para a sub-rede de segurança. Como resultado, mesmo que a regra NSG diz que não existe tráfego de Internet para qualquer endereço na VNet, porque o NSG nunca foi associado à sub-rede de segurança, o tráfego irá fluir para a sub-rede de segurança.

```powershell
Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
    -SubnetName $FESubnet -VirtualNetworkName $VNetName

Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
    -SubnetName $BESubnet -VirtualNetworkName $VNetName
```

## <a name="firewall-rules"></a>Regras da Firewall
Na firewall, as regras de reencaminhamento tem de ser criado. Uma vez que a firewall está bloqueando ou reencaminhamento toda a entrada, saído e intra-VNet tráfego muitas regras de firewall são necessários. Além disso, todo o tráfego de entrada atingirá o endereço IP público do serviço de segurança (em portas diferentes), para serem processados pelo firewall. Uma prática recomendada é para os fluxos de lógicos antes de configurar as sub-redes de diagrama e regras de firewall para evitar retrabalho mais tarde. A figura a seguir é uma visão lógica das regras de firewall para este exemplo:

![Vista lógica das regras de Firewall][2]

> [!NOTE]
> Com base na aplicação Virtual de rede utilizado, as portas de gestão irão variar. Neste exemplo que é referenciado um Barracuda NextGen Firewall que utiliza as portas 22, 801 e 807. Consulte a documentação do fornecedor da aplicação para encontrar as portas exatas utilizadas para a gestão do dispositivo a ser utilizado.
> 
> 

### <a name="logical-rule-description"></a>Descrição da regra de lógica
A sub-rede de segurança não é apresentada no diagrama lógico acima, uma vez que o firewall é o único recurso em que a sub-rede e este diagrama mostra as regras de firewall e como eles logicamente permitem ou negar fluxos de tráfego e não o caminho real roteado. Além disso, as portas externas selecionadas para o tráfego RDP são superior ranged portas (8014 – 8026) e foram selecionadas para alinhar um pouco com os dois últimos octetos do endereço IP local para facilitar a leitura (por exemplo, o endereço de servidor local 10.0.1.4 está associado a porta externa 8014), no entanto podem ser utilizadas qualquer portas não conflitantes superior.

Neste exemplo, precisamos 7 tipos de regras, esses tipos de regra são descritos da seguinte forma:

* Regras de externas (para o tráfego de entrada):
  1. Regra de firewall de gestão: Esta regra de redirecionamento da aplicação permite que o tráfego passar para as portas de gestão da aplicação virtual de rede.
  2. Regras de RDP (para cada servidor do windows): Estes quatro regras (um para cada servidor) irão permitir a gestão dos servidores individuais através de RDP. Também poderia ser agrupado numa regra consoante as capacidades do dispositivo Virtual de rede a ser utilizado.
  3. Regras de tráfego de aplicações: Há duas regras de tráfego de aplicativo, o primeiro para o tráfego da web de front-end e o segundo para o tráfego de back-end (por exemplo, servidor web para a camada de dados). A configuração dessas regras irá depender da arquitetura de rede (em que os servidores são colocados) e fluxos de tráfego (a direção são utilizados os fluxos de tráfego e que as portas).
     * A primeira regra permitirá que o tráfego de aplicativo real alcançar o servidor de aplicação. Apesar de permitir que outras regras de segurança, gestão, etc., as regras de aplicação são o que permitem que os utilizadores externos ou serviços aceder as aplicações. Neste exemplo, existe um servidor web único na porta 80 e, portanto, uma única aplicação regra de firewall irá redirecionar o tráfego de entrada para o IP externo, para o endereço IP da interno de servidores web. A sessão de tráfego redirecionado poderia ser NAT tinha de servidor interno.
     * A segunda regra de tráfego de aplicativo é a regra de back-end para permitir que o servidor Web comunicar com o servidor de AppVM01 (mas não AppVM02) por meio de qualquer porta.
* Regras internas (para o tráfego de intra-VNet)
  1. Saída para a regra de Internet: Esta regra permite o tráfego de qualquer rede para passar para as redes selecionadas. Esta regra é, normalmente, uma regra predefinida já na firewall, mas num Estado desabilitado. Esta regra deve ser ativada para este exemplo.
  2. Regra DNS: Esta regra permite apenas tráfego de DNS (porta 53) passar para o servidor DNS. Para este ambiente que maioria do tráfego do front-end para o back-end está bloqueada, esta regra permite especificamente DNS a partir de qualquer sub-rede local.
  3. Regra de sub-rede à sub-rede: Esta regra é permitir que qualquer servidor na sub-rede de back-end para ligar a qualquer servidor na sub-rede de front-end (mas não o inverso).
* Obter regra isento de falhas (para o tráfego que não cumpre nenhum dos acima):
  1. Todos os de tráfego de regra de negação: Isso deve ser sempre a regra final (em termos de prioridade) e como tal falha se o tráfego flui de acordo com qualquer uma das regras anteriores que é largado por esta regra. Esta é uma regra predefinida e, normalmente, ativado, sem modificações em geral, são necessárias.

> [!TIP]
> A segunda regra de tráfego de aplicativo, qualquer porta é permitida para fácil deste exemplo, num cenário real a porta mais específica e intervalos de endereço devem ser usados para reduzir a superfície de ataque desta regra.
> 
> 

<br />

> [!IMPORTANT]
> Depois de todas as regras acima são criadas, é importante revisar a prioridade de cada regra para garantir que o tráfego será permitido ou negado conforme pretendido. Neste exemplo, as regras são por ordem de prioridade. É fácil ficará impedido de aceder a firewall devido às regras de enganássemos ordenadas. No mínimo, certifique-se de que a gestão para o firewall em si é sempre a regra de prioridade mais alta absoluto.
> 
> 

### <a name="rule-prerequisites"></a>Pré-requisitos de regra
Um pré-requisito para a Máquina Virtual que executa o firewall são pontos finais públicos. Para o firewall processar o tráfego os pontos de extremidade apropriados públicos tem de estar abertos. Existem três tipos de tráfego neste exemplo; Tráfego RDP 1) tráfego de gestão para o controle do firewall e regras de firewall, 2) para controlar os servidores do windows e o tráfego da aplicação de 3). Estas são as três colunas de tipos de tráfego no canto superior metade da visão lógica das regras de firewall acima.

> [!IMPORTANT]
> Um takeway chave aqui é lembrar-se de que **todos os** tráfego virão através da firewall. Então, para o ambiente de trabalho remoto para o servidor de IIS01, mesmo que se no serviço de Cloud de Front-End e da sub-rede de Front-End, para aceder a este servidor iremos precisa fazer o RDP para a firewall na porta 8014 e, em seguida, permitir que a firewall para encaminhar a solicitação RDP internamente para Por RDP IIS01 t. Botão de "Ligar" do portal do Azure não irão funcionar porque não existe nenhum caminho direto de RDP para IIS01 (como ver o portal). Isso significa que todas as ligações da internet será o serviço de segurança e uma porta, por exemplo, secscv001.cloudapp.net:xxxx (referência o diagrama acima para o mapeamento de porta externa e interna de IP e porta).
> 
> 

Um ponto de extremidade pode ser aberto um no momento da criação da VM ou após a compilação, à medida que é feito no script de exemplo e mostrado neste trecho de código a seguir (Observe; qualquer item que começa com um sinal de dólar (p. ex.: $VMName[$i]) é uma variável de definidas pelo utilizador do script no sectio de referência n deste documento. O "$i" entre parênteses Retos, [$i], representa o número de matriz de uma VM específica numa matriz de VMs):

```powershell
Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
    -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
    Update-AzureVM
```

Embora não é mostrado aqui devido ao uso de variáveis, mas os pontos finais são **apenas** aberta no serviço de nuvem de segurança. Isso serve para garantir que todo o tráfego de entrada é processado (encaminhado, NAT tinha, colocado) pelo firewall.

Um cliente de gestão tem de ser instalada num PC para gerir a firewall e criar as configurações necessárias. Veja a documentação do seu firewall (ou outra NVA) do fornecedor sobre como gerir o dispositivo. O resto desta secção e a secção seguinte, a criação de regras de Firewall, descreve a configuração do firewall em si, através do cliente de gestão de fornecedores (ou seja, não o portal do Azure ou o PowerShell).

Aqui podem encontrar instruções para a transferência do cliente e ligar a Barracuda utilizado neste exemplo: [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin)

Depois de iniciar sessão para o firewall, mas antes de criar regras de firewall, há duas classes de objeto de pré-requisitos que podem tornar a criação de regras mais fácil; Objetos de rede e o serviço.

Neste exemplo, três objetos de rede com o nome devem ser definida (uma para a sub-rede de front-end e a sub-rede de back-end, também um objeto de rede para o endereço IP do servidor DNS). Para criar uma rede com nome; a partir do dashboard de cliente Barracuda NG Admin, navegue até ao separador de configuração, na secção de configuração operacionais clique Ruleset, em seguida, clique em "Redes" no menu de objetos de Firewall, em seguida, clique em novo no menu Editar redes. O objeto de rede pode agora ser criado, ao adicionar o nome e o prefixo:

![Criar um objeto de rede de front-end][3]

Esta ação irá criar uma rede com o nome para a sub-rede de front-end, um objeto semelhante que deve ser criado para a sub-rede de back-end também. Agora as sub-redes podem ser referenciadas mais facilmente por nome nas regras da firewall.

Para o objeto de servidor DNS:

![Criar um objeto de servidor DNS][4]

Esta referência de endereço IP única irá ser utilizada numa regra de DNS mais tarde no documento.

Os objetos de pré-requisitos segundo são objetos de serviços. Será representam as portas de ligação de RDP para cada servidor. Uma vez que o objeto de serviço existente do RDP está vinculado à porta RDP padrão, 3389, novos serviços podem ser criados para permitir o tráfego das portas externos (8014 8026). As novas portas também poderiam ser adicionadas para o serviço RDP existente, mas para facilitar a demonstração, pode ser criada uma regra de individual para cada servidor. Para criar uma nova regra RDP para um servidor; a partir do dashboard de cliente Barracuda NG Admin, navegue até ao separador de configuração, na secção de configuração operacionais clique Ruleset, em seguida, clique em "Serviços" no menu de objetos de Firewall, desloque para baixo na lista de serviços e selecione o serviço de "RDP". Com o botão direito e selecione cópia, em seguida, clique com botão direito e selecione colar. Agora é um objeto de serviço RDP Copy1 que pode ser editado. RDP Copy1 com o botão direito e selecione editar, o objeto de serviço editar janela será exibida conforme mostrado aqui:

![Cópia da regra predefinida do RDP][5]

Os valores, em seguida, podem ser editados para representar o serviço RDP para um servidor específico. Para AppVM01 a regra predefinida do RDP acima deve ser modificada para refletir um novo nome do serviço, a descrição e a porta RDP externa, utilizado no diagrama da figura 8 (Nota: as portas são alteradas do padrão RDP de 3389 para a porta externa a ser utilizada para esse servidor específico no caso de AppVM01 a porta externa é 8025) o serviço modificado é mostrado abaixo:

![Regra de AppVM01][6]

Este processo deve ser repetido para criar serviços de RDP para os restantes servidores; AppVM02 DNS01 e IIS01. A criação desses serviços fará com que a criação da regra mais simples e mais óbvio na próxima seção.

> [!NOTE]
> Um serviço RDP para a Firewall não é necessária por dois motivos; 1) primeiro a firewall VM é uma imagem de baseados em Linux, de modo a SSH seria usado na porta 22 para gestão de VMS em vez de RDP e 2) a porta 22 e duas portas de gestão são permitidas na primeira regra de gestão, descrita abaixo, para permitir a conectividade de gestão.
> 
> 

### <a name="firewall-rules-creation"></a>Criação de regras de firewall
Existem três tipos de regras de firewall utilizadas neste exemplo, todos eles tem ícones distintos:

A regra de redirecionamento da aplicação: ![Ícone de redirecionamento da aplicação][7]

A regra NAT de destino: ![Ícone NAT de destino][8]

A regra de Pass: ![Ícone de passagem][9]

Obter mais informações sobre estas regras podem ser encontradas no site da web Barracuda.

Para criar as seguintes regras (ou verifique se as regras existentes do padrão), a partir do dashboard de cliente do Barracuda NG Admin, navegue até ao separador de configuração, na configuração operacional secção, clique em conjunto de regras. Uma grade chamada, "Regras de principal" mostrará as regras existentes do Active Directory e desativadas nesta firewall. No canto superior direito dessa grade é um pequeno, verde "+" botão, clique nele para criar uma nova regra (Nota: a firewall pode ser "bloqueada" para que as alterações, se vir um botão marcados como "Bloquear" e não é possível criar ou editar regras, clicar neste botão para o conjunto de regras de "desbloquear" e  permitir a edição). Se pretender editar uma regra existente, selecione essa regra, com o botão direito e selecione Editar regra.

Assim que as regras são criadas e/ou modificadas, deve ser enviada por push para o firewall e, em seguida, ativados, se isso não é feito as alterações de regra não irão surtir efeito. O processo de ativação e de push é descrito abaixo as descrições de regra de detalhes.

As especificidades de cada regra necessário para concluir este exemplo são descritas da seguinte forma:

* **Gestão de regra de firewall**: Esta regra de redirecionamento da aplicação permite que o tráfego passar para as portas de gestão do dispositivo virtual de rede, neste exemplo, um Barracuda NextGen Firewall. As portas de gestão são 801, 807 e, opcionalmente, 22. As portas externas e internas são os mesmos (ou seja, sem tradução de porta). Esta regra, o programa de configuração-MGMT-acesso, é uma regra predefinida e ativada por predefinição (na versão de Barracuda NextGen Firewall 6.1).
  
    ![Regra de firewall de gestão][10]

> [!TIP]
> O espaço de endereços de origem nesta regra é qualquer, se os intervalos de endereços IP de gestão são conhecidos, reduzir este âmbito também reduziria a superfície de ataque para as portas de gestão.
> 
> 

* **Regras RDP**:  Estas regras NAT de destino irão permitir a gestão dos servidores individuais através de RDP.
  Existem quatro campos críticos necessários para criar esta regra:
  
  1. Origem – para permitir RDP de qualquer lugar, a referência "Qualquer" é utilizada no campo de origem.
  2. Serviço – utilizar o objeto de serviço apropriado criado anteriormente, neste caso, "AppVM01 RDP", as portas externas redirecionar para o endereço IP local de servidores e a porta 3386 (a porta predefinida do RDP). Esta regra específica é para acesso do RDP para AppVM01.
  3. Deve ser o destino – o *local porta na firewall*, "DCHP 1 Local IP" ou eth0 se utilizar IPs estáticos. O número de ordinal (eth0, eth1, etc.) pode ser diferente se a sua aplicação de rede tiver várias interfaces de locais. Esta é a porta está enviando a firewall de (pode ser o mesmo que a porta de recebimento), o destino de roteados real está no campo de lista de destino.
  4. Redirecionamento – esta secção informa a aplicação virtual para onde redirecionar, por fim, este tráfego. O redirecionamento mais simples é colocar o IP e a porta (opcional) no campo de lista de destino. Se nenhuma porta é utilizada a porta de destino no pedido de entrada será utilizado (ie nenhuma conversão), se uma porta está designada a porta também será NAT, juntamente com o IP o resolveria.
     
     ![Regra de RDP do firewall][11]
     
     Um total de quatro regras RDP têm de ser criados: 
     
     | Nome da Regra | Servidor | Serviço | Lista de destino |
     | --- | --- | --- | --- |
     | RDP-to-IIS01 |IIS01 |IIS01 RDP |10.0.1.4:3389 |
     | RDP-to-DNS01 |DNS01 |DNS01 RDP |10.0.2.4:3389 |
     | RDP-to-AppVM01 |AppVM01 |AppVM01 RDP |10.0.2.5:3389 |
     | RDP-to-AppVM02 |AppVM02 |AppVm02 RDP |10.0.2.6:3389 |

> [!TIP]
> Restringir o âmbito dos campos de origem e o serviço irá reduzir a superfície de ataque. O âmbito mais limitado que permitirá que a funcionalidade deve ser utilizado.
> 
> 

* **As regras de tráfego de aplicativo**: Há duas regras de tráfego de aplicativo, o primeiro para o tráfego da web de front-end e o segundo para o tráfego de back-end (por exemplo, servidor web para a camada de dados). Estas regras irão depender da arquitetura de rede (em que os servidores são colocados) e fluxos de tráfego (a direção são utilizados os fluxos de tráfego e que as portas).
  
    Primeiro discutido é a regra de front-end para o tráfego da web:
  
    ![Regra de firewall na Web][12]
  
    Esta regra NAT de destino permite o tráfego de aplicativo real alcançar o servidor de aplicação. Apesar de permitir que outras regras de segurança, gestão, etc., as regras de aplicação são o que permitem que os utilizadores externos ou serviços aceder as aplicações. Neste exemplo, existe um servidor web único na porta 80 e, portanto, a regra de aplicação de firewall único vai redirecionar o tráfego de entrada para o IP externo, para o endereço IP da interno de servidores web.
  
    **Tenha em atenção**: que não existe nenhuma porta atribuída no campo de lista de destino, assim, a porta de entrada 80 (ou 443 para o serviço selecionado) será utilizada no redirecionamento do servidor web. Se o servidor web está à escuta numa porta diferente, por exemplo para a porta 8080, foi possível atualizar o campo de lista de destino para 10.0.1.4:8080 para permitir o redirecionamento de porta também.
  
    A seguinte regra de tráfego de aplicativo é a regra de back-end para permitir que o servidor Web comunicar com o servidor de AppVM01 (mas não AppVM02) por meio de qualquer serviço:
  
    ![Regra de AppVM01 de firewall][13]
  
    Esta regra de aprovação permite que qualquer servidor IIS na sub-rede de front-end para alcançar o AppVM01 (endereço IP 10.0.2.5) em qualquer porta, usando todos os protocolos para aceder a dados necessários para o aplicativo web.
  
    Nesta captura de ecrã um "\<explícita-dest\>" é usado no campo de destino para indicar 10.0.2.5 como o destino. Isso poderia ser explícito conforme mostrado, ou uma com o nome o objeto de rede (como foi feito nos pré-requisitos para o servidor DNS). Isso fica a cargo do administrador do firewall em relação ao que será utilizado o método. Para adicionar 10.0.2.5 como um destino explícita, faça duplo clique na primeira linha em branco sob \<explícita-dest\> e introduza o endereço na janela que aparece.
  
    Com esta regra de passar nenhuma NAT é necessária uma vez que este é o tráfego interno, pelo que o método de conexão pode ser definido como "Não SNAT".
  
    **Nota**: A rede de origem nesta regra é qualquer recurso na sub-rede de front-end, se houver apenas um ou um número específico conhecido dos servidores web, um recurso de objeto de rede poderia ser criado para ser mais específico para esses endereços IP exatamente em vez de toda a sub-rede do front-end.

> [!TIP]
> Esta regra utiliza o serviço "Qualquer" para facilitar a aplicação de exemplo configurar e utilizar, isso também irá permitir ICMPv4 (ping) numa única regra. No entanto, isso não é uma prática recomendada. As portas e protocolos ("serviços") devem ser reduzidos para o mínimo possível que permite que a operação de aplicação reduzir a superfície de ataque por esse limite.
> 
> 

<br />

> [!TIP]
> Embora esta regra mostra uma referência explícita-dest que está a ser utilizada, uma abordagem consistente deve ser utilizada em toda a configuração de firewall. Recomenda-se que o objeto de rede nomeado ser usadas em todo para facilitar a leitura e a capacidade de suporte. -Dest explícita é utilizado aqui apenas para mostrar um método alternativo de referência e geralmente não é recomendável (principalmente para configurações complexas).
> 
> 

* **Saída para a regra de Internet**: Esta regra de Pass irá permitir o tráfego de qualquer rede de origem para passar para as redes de destino selecionadas. Esta regra é uma regra predefinida normalmente já no Barracuda NextGen firewall, mas está no estado desativado. Clicar com o botão direito sobre esta regra pode acessar o comando de ativar a regra. A regra mostrada aqui foi modificada para adicionar as duas sub-redes locais que foram criadas como referências na secção pré-requisitos deste documento para o atributo de origem desta regra.
  
    ![Regra de firewall de saída][14]
* **Regra de DNS**: Esta regra de Pass permite apenas tráfego de DNS (porta 53) passar para o servidor DNS. Para este ambiente que maioria do tráfego do front-end para o back-end está bloqueada, esta regra permite especificamente DNS.
  
    ![Regra de firewall DNS][15]
  
    **Nota**: Nesta captura de ecrã, o método de ligação está incluído. Uma vez que esta regra destina-se um IP interno para tráfego de endereço IP interno, sem NATing é necessária, o método de ligação está definido como "Não SNAT" para esta regra de Pass.
* **Regra de sub-rede para sub-rede**: Esta regra de Pass é uma regra predefinida que foi ativada e modificada para permitir que qualquer servidor na sub-rede de back-end para ligar a qualquer servidor na sub-rede de front-end. Esta regra é o tráfego interno tudo para que o método de conexão pode ser definido para não SNAT.
  
    ![Regra de VNet de dentro do firewall][16]
  
    **Nota**: A caixa de verificação bidirecionais não estiver marcada (nem é verificado na maioria das regras), isso é significativo para esta regra em que ele faz com que esta regra "um direcional", uma ligação pode ser iniciada a partir da sub-rede de back-end para a rede de front-end, mas não o inverso. Se essa caixa de seleção foi marcada, esta regra permitiria tráfego bidirecional, o que, de nosso diagrama lógico, não for o pretendido.
* **Negar a regra de tráfego de todos os**: Isso deve ser sempre a regra final (em termos de prioridade) e como tal falha se o tráfego flui de acordo com qualquer uma das regras anteriores que é largado por esta regra. Esta é uma regra predefinida e, normalmente, ativado, sem modificações em geral, são necessárias. 
  
    ![Regra de negação de firewall][17]

> [!IMPORTANT]
> Depois de todas as regras acima são criadas, é importante revisar a prioridade de cada regra para garantir que o tráfego será permitido ou negado conforme pretendido. Neste exemplo, as regras estão na ordem que devem ser apresentadas na grelha de principal de reencaminhamento de regras no cliente de gestão do Barracuda.
> 
> 

## <a name="rule-activation"></a>Ativação de regra
Com o conjunto de regras modificado para a especificação do diagrama lógica, o conjunto de regras tem de ser carregado para o firewall e, em seguida, ativado.

![Ativação de regra de firewall][18]

No canto superior direita do cliente de gestão são um cluster de botões. Clique no botão "Enviar alterações" para enviar as regras de modificação para o firewall e, em seguida, clique no botão "Ativar".

Com a ativação de regras de firewall desta versão de ambiente de exemplo está concluída.

## <a name="traffic-scenarios"></a>Cenários de tráfego
> [!IMPORTANT]
> Uma vantagem principal é se lembrar que **todos os** tráfego virão através da firewall. Então, para o ambiente de trabalho remoto para o servidor de IIS01, mesmo que se no serviço de Cloud de Front-End e da sub-rede de Front-End, para aceder a este servidor iremos precisa fazer o RDP para a firewall na porta 8014 e, em seguida, permitir que a firewall para encaminhar a solicitação RDP internamente para Por RDP IIS01 t. Botão de "Ligar" do portal do Azure não irão funcionar porque não existe nenhum caminho direto de RDP para IIS01 (como ver o portal). Isso significa que todas as ligações da internet será o serviço de segurança e uma porta, por exemplo, secscv001.cloudapp.net:xxxx.
> 
> 

Nestes cenários, as seguintes regras de firewall devem estar no local:

1. Gestão da firewall
2. RDP para IIS01
3. RDP para DNS01
4. RDP para AppVM01
5. RDP para AppVM02
6. Tráfego da aplicação para a Web
7. Tráfego de aplicações para AppVM01
8. Saída à Internet
9. Front-end para DNS01
10. Tráfego de sub-rede de dentro (back-end para front-end apenas)
11. Recusar tudo

O conjunto de regras de firewall reais provavelmente terá muitos outras regras além destas, as regras em qualquer determinado firewall também terão os números de prioridade diferentes dos que foram listados aqui. Esta lista e números associados são fornecer a relevância entre apenas essas onze regras e a prioridade relativa entre eles. Em outras palavras; na firewall de real, o "RDP para IIS01" pode ser regra número 5, mas desde que ele é inferior a regra de "Gestão da Firewall" e acima a regra "RDP para DNS01" seria alinhar com a intenção desta lista. A lista também ajudará no abaixo cenários, permitindo que a questão de brevidade; Por exemplo "Regra de firewall 9 (DNS)". Também para fins de brevidade, as regras RDP quatro será coletivamente chamado, ""as RDP regras quando o cenário de tráfego não está relacionada com RDP.

Lembre-se também que grupos de segurança de rede estão no local para o tráfego de internet de entrada nas sub-redes de front-end e back-end.

#### <a name="allowed-internet-to-web-server"></a>(Permitido) Internet para o servidor Web
1. Página da HTTP de pedidos de utilizador de Internet da SecSvc001.CloudApp.Net (Internet do serviço da nuvem com acesso à)
2. Tráfego de passes do serviço de nuvem por meio de pontos finais abertos na porta 80 à interface de firewall de 10.0.0.4:80
3. Nenhum NSG atribuído a sub-rede de segurança, as regras do NSG sistema assim permitem o tráfego para firewall
4. O tráfego chega o endereço IP interno do firewall (10.0.1.4)
5. Firewall começa o processamento da regra:
   1. 1 de regra de firewall (firewall Mgmt) não se aplicam, mudar para a próxima regra
   2. Regras do firewall 2 a 5 (regras de RDP) não se aplicam, mudar para a próxima regra
   3. Regra de firewall 6 (aplicação: É aplicável a Web), o tráfego é permitido, NATs de firewall para 10.0.1.4 (IIS01)
6. A sub-rede de front-end começa o processamento da regra de entrada:
   1. Regra de NSG 1 (bloco de Internet) não se aplica (este tráfego foi NAT seria pelo firewall, portanto, o endereço de origem está agora a firewall que está na sub-rede de segurança e visualizadas pelo NSG de sub-rede de front-end para ser o tráfego de "local" e, portanto, é permitido), mover para a próxima regra
   2. Regras de NSG predefinidas permitir tráfego de sub-rede para sub-rede, o tráfego é permitido, pare o processamento da regra NSG
7. IIS01 está à escuta de tráfego da web, recebe essa solicitação e começa a processar o pedido
8. Tentativas de IIS01 inicia uma sessão de FTP AppVM01 na sub-rede de back-end
9. A rota UDR na sub-rede de front-end torna o firewall o salto seguinte
10. Não existem regras de saída na sub-rede de front-end, o tráfego é permitido
11. Firewall começa o processamento da regra:
    1. 1 de regra de firewall (firewall Mgmt) não se aplicam, mudar para a próxima regra
    2. Regra de firewall 2 a 5 (regras de RDP) não se aplicam, mova a regra seguinte
    3. Regra de firewall 6 (aplicação: Web) não se aplicam, mudar para a próxima regra
    4. Regra de firewall 7 (aplicação: Aplicar o back-end), o tráfego é permitido, firewall reencaminha o tráfego para 10.0.2.5 (AppVM01)
12. A sub-rede de back-end começa o processamento da regra de entrada:
    1. 1 de regra de NSG (bloco de Internet) não se aplicam, mudar para a próxima regra
    2. Regras de NSG predefinidas permitir tráfego de sub-rede para sub-rede, o tráfego é permitido, pare o processamento da regra NSG
13. AppVM01 recebe o pedido e inicia a sessão e responde
14. A rota UDR na sub-rede de back-end torna o firewall o salto seguinte
15. Uma vez que existem não existem regras NSG de saída da sub-rede de back-end, que a resposta é permitida
16. Porque esta está a devolver o tráfego numa sessão estabelecida a firewall passa a resposta de volta para o servidor web (IIS01)
17. A sub-rede de front-end começa o processamento da regra de entrada:
    1. 1 de regra de NSG (bloco de Internet) não se aplicam, mudar para a próxima regra
    2. Regras de NSG predefinidas permitir tráfego de sub-rede para sub-rede, o tráfego é permitido, pare o processamento da regra NSG
18. O servidor IIS recebe a resposta, concluir a transação com AppVM01 e, depois, conclua a criação de resposta HTTP, esta resposta HTTP é enviada para o requerente
19. Uma vez que existem não existem regras NSG de saída da sub-rede de front-end, que a resposta é permitida
20. A resposta HTTP atinge o firewall e porque esta é a resposta para uma sessão NAT estabelecida é aceite pelo firewall
21. O firewall, em seguida, redireciona a resposta de volta para o utilizador de Internet
22. Uma vez que existem não saída regras do NSG ou saltos UDR na sub-rede de front-end é permitida a resposta e o utilizador de Internet recebe a página da web solicitado.

#### <a name="allowed-internet-rdp-to-backend"></a>(Permitido) Internet RDP para back-end
1. Administrador do servidor na internet solicita a sessão do RDP para AppVM01 via SecSvc001.CloudApp.Net:8025, onde 8025 é o número de porta de atribuída ao utilizador para a regra de firewall "RDP para AppVM01"
2. O serviço em nuvem passa o tráfego através de ponto de extremidade aberto na porta 8025 à interface de firewall no 10.0.0.4:8025
3. Nenhum NSG atribuído a sub-rede de segurança, as regras do NSG sistema assim permitem o tráfego para firewall
4. Firewall começa o processamento da regra:
   1. 1 de regra de firewall (firewall Mgmt) não se aplicam, mudar para a próxima regra
   2. 2 de regra de firewall (RDP IIS) não se aplicam, mova a regra seguinte
   3. 3 de regra de firewall (RDP DNS01) não se aplicam, mova a regra seguinte
   4. Aplicar a regra de firewall 4 (RDP AppVM01), o tráfego é permitido, NATs de firewall para 10.0.2.5:3386 (porta RDP no AppVM01)
5. A sub-rede de back-end começa o processamento da regra de entrada:
   1. Regra de NSG 1 (bloco de Internet) não se aplica (este tráfego foi NAT seria pelo firewall, portanto, o endereço de origem está agora a firewall que está na sub-rede de segurança e visualizadas pelo NSG de sub-rede de back-end para ser o tráfego de "local" e, portanto, é permitido), mover para a próxima regra
   2. Regras de NSG predefinidas permitir tráfego de sub-rede para sub-rede, o tráfego é permitido, pare o processamento da regra NSG
6. AppVM01 está à escuta de tráfego RDP e responde
7. Com nenhuma saídas regras do NSG, aplicam-se de regras predefinidas e tráfego de retorno é permitido
8. UDR encaminha o tráfego de saída para o firewall como salto seguinte
9. Porque esta está a devolver o tráfego numa sessão estabelecida a firewall passa a resposta de volta para o utilizador de internet
10. Sessão RDP está ativada
11. AppVM01 solicita a palavra-passe de nome de utilizador

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Permitido) Pesquisa de DNS de servidor da Web no servidor DNS
1. Web às necessidades de servidor, IIS01, dados de um feed em www\.data.gov, mas tem de resolver o endereço.
2. A configuração de rede para as listas de VNet DNS01 (10.0.2.4 na sub-rede de back-end) que o servidor DNS primário, IIS01 envia o pedido DNS para DNS01
3. UDR encaminha o tráfego de saída para o firewall como salto seguinte
4. Não existem regras NSG de saída são ligadas à sub-rede de front-end, o tráfego é permitido
5. Firewall começa o processamento da regra:
   1. 1 de regra de firewall (firewall Mgmt) não se aplicam, mudar para a próxima regra
   2. Regra de firewall 2 a 5 (regras de RDP) não se aplicam, mova a regra seguinte
   3. 6 de regras de firewall e 7 (regras de aplicações) não se aplicam, mova a seguinte regra
   4. 8 de regra de firewall (para Internet) não se aplicam, mudar para a próxima regra
   5. Aplicar a regra de firewall 9 (DNS), o tráfego é permitido, firewall reencaminha o tráfego para 10.0.2.4 (DNS01)
6. A sub-rede de back-end começa o processamento da regra de entrada:
   1. 1 de regra de NSG (bloco de Internet) não se aplicam, mudar para a próxima regra
   2. Regras de NSG predefinidas permitir tráfego de sub-rede para sub-rede, o tráfego é permitido, pare o processamento da regra NSG
7. Servidor DNS recebe o pedido
8. Servidor DNS não tem o endereço em cache e pede-lhe um servidor DNS de raiz na internet
9. UDR encaminha o tráfego de saída para o firewall como salto seguinte
10. Não existem regras de NSG saída na sub-rede de back-end, o tráfego é permitido
11. Firewall começa o processamento da regra:
    1. 1 de regra de firewall (firewall Mgmt) não se aplicam, mudar para a próxima regra
    2. Regra de firewall 2 a 5 (regras de RDP) não se aplicam, mova a regra seguinte
    3. 6 de regras de firewall e 7 (regras de aplicações) não se aplicam, mova a seguinte regra
    4. Aplicar a regra de firewall 8 (para Internet), o tráfego é permitido, a sessão é SNAT de saída para o servidor DNS de raiz na Internet
12. Servidor DNS de Internet responde, uma vez que esta sessão foi iniciada a partir da firewall, a resposta é aceite pelo firewall
13. Como se trata de uma sessão estabelecida, o firewall por sua vez encaminha a resposta para o servidor de início, DNS01
14. A sub-rede de back-end começa o processamento da regra de entrada:
    1. 1 de regra de NSG (bloco de Internet) não se aplicam, mudar para a próxima regra
    2. Regras de NSG predefinidas permitir tráfego de sub-rede para sub-rede, o tráfego é permitido, pare o processamento da regra NSG
15. O servidor DNS recebe e armazena em cache a resposta e, em seguida, responde ao pedido inicial para IIS01
16. A rota UDR na sub-rede de back-end torna o firewall o salto seguinte
17. Não existem regras NSG saídas existem na sub-rede de back-end, o tráfego é permitido
18. Esta é uma sessão estabelecida no firewall, a resposta é reencaminhada pelo firewall no servidor de IIS
19. A sub-rede de front-end começa o processamento da regra de entrada:
    1. Não existe nenhuma regra NSG aplica-se a entrada tráfego da sub-rede de back-end para a sub-rede de front-end, para que nenhuma do NSG de regras aplicam-se
    2. A regra predefinida do sistema a permitir o tráfego entre sub-redes permitiria que este tráfego para que o tráfego é permitido
20. IIS01 recebe a resposta do DNS01

#### <a name="allowed-backend-server-to-frontend-server"></a>(Permitido) Servidor de back-end para o servidor front-end
1. Um administrador com sessão iniciado no AppVM02 através de RDP solicita um ficheiro diretamente a partir do servidor de IIS01 através do Explorador de ficheiros do windows
2. A rota UDR na sub-rede de back-end torna o firewall o salto seguinte
3. Uma vez que existem não existem regras NSG de saída da sub-rede de back-end, que a resposta é permitida
4. Firewall começa o processamento da regra:
   1. 1 de regra de firewall (firewall Mgmt) não se aplicam, mudar para a próxima regra
   2. Regra de firewall 2 a 5 (regras de RDP) não se aplicam, mova a regra seguinte
   3. 6 de regras de firewall e 7 (regras de aplicações) não se aplicam, mova a seguinte regra
   4. 8 de regra de firewall (para Internet) não se aplicam, mudar para a próxima regra
   5. 9 de regra de firewall (DNS) não se aplicam, mudar para a próxima regra
   6. 10 de regra de firewall (Intra-sub-rede) é aplicável, o tráfego é permitido, firewall passa o tráfego para 10.0.1.4 (IIS01)
5. A sub-rede de front-end começa o processamento da regra de entrada:
   1. 1 de regra de NSG (bloco de Internet) não se aplicam, mudar para a próxima regra
   2. Regras de NSG predefinidas permitir tráfego de sub-rede para sub-rede, o tráfego é permitido, pare o processamento da regra NSG
6. Partindo do princípio de autenticação e autorização adequadas, IIS01 aceita a solicitação e responde
7. A rota UDR na sub-rede de front-end torna o firewall o salto seguinte
8. Uma vez que existem não existem regras NSG de saída da sub-rede de front-end, que a resposta é permitida
9. Como se trata de uma sessão existente na firewall esta resposta é permitida e a firewall retorna a resposta ao AppVM02
10. A sub-rede de back-end começa o processamento da regra de entrada:
    1. 1 de regra de NSG (bloco de Internet) não se aplicam, mudar para a próxima regra
    2. Regras de NSG predefinidas permitir tráfego de sub-rede para sub-rede, o tráfego é permitido, pare o processamento da regra NSG
11. AppVM02 recebe a resposta

#### <a name="denied-internet-direct-to-web-server"></a>(Negado) Internet direta para o servidor Web
1. Utilizador de Internet tenta aceder ao servidor de web, IIS01, através do serviço de FrontEnd001.CloudApp.Net
2. Uma vez que não existem não existem pontos finais aberta para tráfego HTTP, isso não transmitia através do serviço de nuvem e não alcançar o servidor
3. Se os pontos de extremidade foram abertos por algum motivo, o NSG (bloco de Internet) na sub-rede de front-end bloquearia deste tráfego
4. Por fim, a rota UDR de sub-rede de front-end poderia enviar qualquer tráfego de saída a partir IIS01 para o firewall como salto seguinte, e o firewall teria visto como tráfego assimétrico e largue a resposta de saída portanto há, pelo menos, três camadas independentes de defesa entre a internet e IIS01 através do respetivo serviço de nuvem impedir o acesso não autorizado/inadequado.

#### <a name="denied-internet-to-backend-server"></a>(Negado) Internet para o servidor back-end
1. O utilizador de Internet tenta acessar um arquivo no AppVM01 através do serviço de BackEnd001.CloudApp.Net
2. Uma vez que não existem não existem pontos finais aberta para a partilha de ficheiros, isto não transmitia o serviço em nuvem e não alcançar o servidor
3. Se os pontos de extremidade foram abertos por algum motivo, o NSG (bloco de Internet) bloquearia deste tráfego
4. Por fim, a rota UDR enviaria qualquer tráfego de saída de AppVM01 à firewall como salto seguinte, e o firewall teria visto como tráfego assimétrico e largue a resposta de saída, portanto, existem, pelo menos, três camadas independentes de defesa entre a internet e AppVM01 através do respetivo serviço de nuvem impedir o acesso não autorizado/inadequado.

#### <a name="denied-frontend-server-to-backend-server"></a>(Negado) Servidor de front-end para o servidor back-end
1. Partem do princípio de IIS01 foi comprometida e que está a executar o código mal-intencionado tentar verificar os servidores de sub-rede de back-end.
2. A rota UDR de sub-rede de front-end enviaria qualquer tráfego de saída de IIS01 à firewall como salto seguinte. Isso não é algo que pode ser alterado pela VM comprometida.
3. O firewall faria processar o tráfego, se o pedido foi para AppVM01 ou para o servidor DNS para pesquisas de DNS que tráfego potencialmente poderia ser permitido pela firewall (devido a 7 de regras de firewall e 9). Todos os outros tráfegos seriam bloqueado pelo 11 de regra de firewall (negar todos).
4. Se a deteção de ameaças avançadas foi ativada na firewall (que não é abrangido neste documento, consulte a documentação do fornecedor para a sua aplicação de rede específicas capacidades de ameaças avançadas), até mesmo tráfego teria permissão pelas regras de reencaminhamento básico discutidos neste documento pode ser impedido de se o tráfego contidos assinaturas conhecidas ou padrões que sinalizar uma regra avançada contra ameaças.

#### <a name="denied-internet-dns-lookup-on-dns-server"></a>(Negado) Pesquisa de DNS de Internet no servidor DNS
1. Utilizador de Internet tenta procurar um registo DNS interno no DNS01 através do serviço de BackEnd001.CloudApp.Net 
2. Uma vez que não existem não existem pontos finais aberta para tráfego de DNS, isso não transmitia através do serviço de nuvem e não alcançar o servidor
3. Se os pontos de extremidade foram abertos por algum motivo, a regra NSG (bloco de Internet) na sub-rede de front-end bloquearia deste tráfego
4. Por fim, a rota UDR de sub-rede de back-end poderia enviar qualquer tráfego de saída a partir DNS01 para o firewall como salto seguinte, e o firewall teria visto como tráfego assimétrico e largue a resposta de saída portanto há, pelo menos, três camadas independentes de defesa entre o Internet e DNS01 através do respetivo serviço de nuvem impedir o acesso não autorizado/inadequado.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(Negado) Internet para o acesso através da Firewall do SQL
1. Utilizador de Internet solicita dados SQL SecSvc001.CloudApp.Net (Internet do serviço da nuvem com acesso à)
2. Uma vez que não existem não existem pontos finais aberta para SQL, isso não transmitia o serviço em nuvem e não alcançar o firewall
3. Se pontos de extremidade do SQL foram abertos por algum motivo, o firewall pode começar o processamento da regra:
   1. 1 de regra de firewall (firewall Mgmt) não se aplicam, mudar para a próxima regra
   2. Regras do firewall 2 a 5 (regras de RDP) não se aplicam, mudar para a próxima regra
   3. Regra de firewall 6 e 7 (regras de aplicações) não aplicar, mova a regra seguinte
   4. 8 de regra de firewall (para Internet) não se aplicam, mudar para a próxima regra
   5. 9 de regra de firewall (DNS) não se aplicam, mudar para a próxima regra
   6. 10 de regra de firewall (Intra-sub-rede) não se aplicam, mudar para a próxima regra
   7. Aplicar a 11 de regra de firewall (negar todos), o tráfego é o processamento da regra bloqueados, pare

## <a name="references"></a>Referências
### <a name="main-script-and-network-config"></a>Script principal e a configuração de rede
Guarde o Script completo num arquivo de script do PowerShell. Guarde a configuração de rede para um ficheiro denominado "NetworkConf2.xml".
Modifique as variáveis definidas pelo utilizador, conforme necessário. Execute o script e, em seguida, siga as instruções de configuração de regra de Firewall acima.

#### <a name="full-script"></a>Script completo
Este script irá, com base nas variáveis definidas pelo utilizador:

1. Ligar a uma subscrição do Azure
2. Criar uma nova conta de armazenamento
3. Criar uma nova VNet e três sub-redes, conforme definido no ficheiro de configuração de rede
4. Criar cinco máquinas virtuais; 1 firewall e servidor de 4 windows VMs
5. Configure o UDR incluindo:
   1. Criar duas novas tabelas de rota
   2. Adicionar rotas para as tabelas
   3. Vincular tabelas para sub-redes apropriadas
6. Ativar o reencaminhamento de IP na NVA
7. Configure o NSG incluindo:
   1. Criar um NSG
   2. Adicionar uma regra
   3. O NSG de ligação para as sub-redes apropriadas

Este script do PowerShell deve ser executado localmente num que Internet ligado PC ou servidor.

> [!IMPORTANT]
> Quando esse script for executado, pode haver avisos ou outras mensagens de informações que exibida no PowerShell. Apenas mensagens de erro em vermelho são o motivo de preocupação.
> 
> 

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
        # since the NSG bound to the Frontend and Backback subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
```

#### <a name="network-config-file"></a>Ficheiro de configuração de rede
Guarde este ficheiro xml com localização atualizada e adicionar a ligação a este ficheiro para a variável de $NetworkConfigFile no script acima.

```xml
    <NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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

#### <a name="sample-application-scripts"></a>Scripts de aplicativo de exemplo
Se pretender instalar um aplicativo de exemplo para este e outros exemplos de rede de Perímetro, foi fornecido na seguinte hiperligação: [Script de aplicação de exemplo][SampleApp]

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
[SampleApp]: ./virtual-networks-sample-app.md
