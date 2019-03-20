---
title: Exemplo de rede de Perímetro do Azure – criar um DMZ Simple com NSGs | Documentos da Microsoft
description: Criar um DMZ com grupos de segurança de rede (NSG)
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: f8622b1d-c07d-4ea6-b41c-4ae98d998fff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: 115a459c6a9e4ea96931c89272a49396f0656258
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993334"
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-classic-powershell"></a>Exemplo 1 – criar um DMZ simple com NSGs com o PowerShell clássico
[Regressar à página de práticas recomendada de segurança limites][HOME]

> [!div class="op_single_selector"]
> * [Modelo do Resource Manager](virtual-networks-dmz-nsg.md)
> * [Clássica – PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

Este exemplo cria uma rede de Perímetro primitivo com quatro servidores do Windows e os grupos de segurança de rede. Este exemplo descreve cada um dos comandos do PowerShell relevantes para fornecer uma compreensão mais aprofundada de cada passo. Também há uma seção de cenário de tráfego para fornecer um detalhado passo a passo de como o tráfego passa pelas camadas de defesa na rede de Perímetro. Finalmente, as referências de seção é o código completo e a instrução para criar este ambiente de teste e experimente com vários cenários. 

![Rede de Perímetro com NSG de entrada][1]

## <a name="environment-description"></a>Descrição de ambiente
Neste exemplo, uma subscrição contém os seguintes recursos:

* Dois serviços de cloud: "FrontEnd001" e "BackEnd001"
* Uma rede Virtual, "CorpNetwork", com duas sub-redes; "Front-end" e "Back-end"
* Um grupo de segurança de rede que é aplicado a ambas as sub-redes
* Windows Server que representa um servidor de web de aplicações ("IIS01")
* Servidores de duas janelas que representam servidores de back-end de aplicação ("AppVM01", "AppVM02")
* Um servidor do Windows que representa um servidor DNS ("DNS01")

Na seção referências, há um script do PowerShell que cria a maior parte do ambiente descrito neste exemplo. A criar as VMs e redes virtuais, embora são efetuados pelo script de exemplo, não estão descritas em detalhe neste documento. 

Para criar o ambiente;

1. Guarde o ficheiro de xml de configuração de rede incluído na seção referências (atualizado com os nomes, a localização e o IP endereços de acordo com o cenário determinado)
2. Atualize as variáveis de utilizador no script para corresponder ao ambiente de que script está a ser executado em (subscrições, os nomes de serviço, etc.)
3. Execute o script do PowerShell

>[!Note]
>A região demonstrada no script do PowerShell têm de corresponder a região demonstrada no ficheiro xml de configuração de rede.
>
>

Assim que o script é executado com êxito adicional passos opcionais pode ser demorado, na seção referências são dois scripts para configurar o servidor web e o servidor de aplicações com uma aplicação web simples para permitir que o teste com esta configuração de rede de Perímetro.

As secções seguintes fornecem uma descrição detalhada dos grupos de segurança de rede e como eles funcionam para este exemplo examinando as principais linhas de script do PowerShell.

## <a name="network-security-groups-nsg"></a>Grupos de Segurança de Rede (NSG)
Neste exemplo, um grupo NSG é criado e, em seguida, é carregado com regras de seis. 

> [!TIP]
> Em termos gerais, deve criar as regras específicas de "Permitir de" primeiro e, em seguida, as regras mais genéricas de "Negar" último. Define a prioridade atribuída que as regras são avaliadas primeiro. Assim que o tráfego for encontrado para aplicar a uma regra específica, não existem regras adicionais são avaliadas. As regras do NSG podem aplicar em qualquer um na direção de entrada ou saída (a partir do ponto de vista da sub-rede).
> 
> 

De forma declarativa, as seguintes regras estão sendo criadas para tráfego de entrada:

1. É permitido o tráfego DNS interno (porta 53)
2. É permitido o tráfego RDP (porta 3389) da Internet a qualquer VM
3. O tráfego HTTP (porta 80) da Internet para o servidor web (IIS01) é permitido
4. Qualquer tráfego (todas as portas) de IIS01 para AppVM1 é permitido
5. Qualquer tráfego (todas as portas) da Internet para toda a VNet (ambas as sub-redes) é negado
6. Qualquer tráfego (todas as portas) partir da sub-rede Frontend para a sub-rede de back-end é negado

Com estas regras vinculado a cada sub-rede, se um pedido de HTTP foi entrado da Internet para o servidor web, ambas as regras 3 (permitir) e 5 (negar) aplicaria, mas uma vez que a regra 3 tem uma prioridade mais alta apenas seriam aplicáveis e regra 5 não seria entram em cena. Assim, a solicitação HTTP teria permissão para o servidor web. Se esse mesmo tráfego estava a tentar aceder ao servidor DNS01, regra 5 (negar) seria o primeiro a aplicar e o tráfego que não permitia a passar para o servidor. Regra 6 (negar) bloqueia a sub-rede de front-end ao falar com a sub-rede de back-end (exceto para o tráfego permitido nas regras 1 e 4), este conjunto de regras protege a rede de back-end, no caso de comprometimentos um atacante a aplicação web no front-end, o invasor teria limitada acesso para o back-end "protegido" rede (apenas para recursos expostos no servidor AppVM01).

Existe uma regra de saída predefinida que permita o tráfego de saída à internet. Neste exemplo, estamos a permitir o tráfego de saída e sem modificar quaisquer regras de saída. Para bloquear o tráfego em ambas as direções, encaminhamento de definido pelo utilizador é necessária e é explorada "Exemplo 3" no [página de práticas recomendadas do Security limites][HOME].

Cada regra é abordada em mais detalhes, da seguinte forma (**nota**: qualquer item na lista seguinte, começando com um sinal de dólar (por exemplo: $NSGName) é uma variável definida pelo utilizador do script na seção referência deste documento):

1. Em primeiro lugar, um grupo de segurança de rede deve ser criado para conter as regras:

    ```PowerShell
    New-AzureNetworkSecurityGroup -Name $NSGName `
        -Location $DeploymentLocation `
        -Label "Security group for $VNetName subnets in $DeploymentLocation"
    ```

2. Neste exemplo, a primeira regra permite o tráfego DNS entre todas as redes internas para o servidor DNS na sub-rede de back-end. A regra tem alguns parâmetros importantes:
   
   * "Tipo" significa que em que direção do fluxo de tráfego entra em vigor a esta regra. É a direção da perspetiva da sub-redes ou máquinas virtuais (dependendo de onde está vinculada este NSG). Portanto, se é de tipo "Entrada" e tráfego está a entrar na sub-rede, a regra aplicaria e tráfego que sai da sub-rede não seria afetado por esta regra.
   * "Prioridade" define a ordem em que um fluxo de tráfego é avaliado. Menor o número maior será a prioridade. Quando uma regra se aplica a um fluxo de tráfego específico, não existem mais regras são processadas. Portanto, se uma regra com prioridade 1 permite o tráfego e uma regra com prioridade 2 recusa o tráfego e ambas as regras são aplicadas ao tráfego, em seguida, o tráfego teria permissão para o fluxo (uma vez que a regra 1 tinha uma prioridade mais alta demorou efeito e não mais regras foram aplicadas).
   * "Action" significa se o tráfego afetado por esta regra é bloqueado ou permitido.

     ```PowerShell    
     Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" `
        -Type Inbound -Priority 100 -Action Allow `
        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[4] `
        -DestinationPortRange '53' `
        -Protocol *
     ```

3. Esta regra permite o tráfego RDP para o fluxo da internet para a porta RDP em qualquer servidor na sub-rede vinculada. Esta regra utiliza dois tipos especiais de prefixos de endereço "VIRTUAL_NETWORK" e "INTERNET". Estas etiquetas são uma forma fácil de abordar uma categoria maior de prefixos de endereço.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" `
         -Type Inbound -Priority 110 -Action Allow `
         -SourceAddressPrefix INTERNET -SourcePortRange '*' `
         -DestinationAddressPrefix VIRTUAL_NETWORK `
         -DestinationPortRange '3389' `
         -Protocol *
    ```

4. Esta regra permite o tráfego de internet de entrada para atingir o servidor web. Esta regra não altera o comportamento do encaminhamento. A regra permite apenas tráfego destinado a IIS01 passar. Portanto, se o tráfego da Internet tinha o servidor web como respectivo destino esta regra seria permitir que ele e parar o processamento ainda mais regras. (Na regra com prioridade 140 todos os outro internet tráfego de entrada é bloqueado). Se estiver apenas a processar o tráfego HTTP, esta regra pode ser mais restrito para permitir apenas a porta 80 de destino.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
         Set-AzureNetworkSecurityRule -Name "Enable Internet to $VMName[0]" `
         -Type Inbound -Priority 120 -Action Allow `
         -SourceAddressPrefix Internet -SourcePortRange '*' `
         -DestinationAddressPrefix $VMIP[0] `
         -DestinationPortRange '*' `
         -Protocol *
    ```

5. Esta regra permite o tráfego para passar do servidor IIS01 para o servidor de AppVM01, um posterior blocos de regra a todos os outro front-end para back-end o tráfego. Para melhorar esta regra, se a porta é conhecida que devem ser adicionados. Por exemplo, se o servidor IIS está atingindo apenas SQL Server em AppVM01, o intervalo de portas de destino deve ser alterado de "*" (qualquer) 1433 (a porta do SQL), permitindo assim que uma menor superfície de ataque de entrada em AppVM01 a aplicação web já estiver comprometida.

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Enable $VMName[1] to $VMName[2]" `
        -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[2] `
        -DestinationPortRange '*' `
        -Protocol *
    ```

6. Esta regra nega o tráfego da internet para todos os servidores na rede. Com as regras de prioridade 110 e 120, o efeito é permitir que apenas internet tráfego de entrada para o firewall e as portas RDP em servidores e blocos de todo o resto. Esta regra é uma regra "isento de falhas" para bloquear todos os fluxos inesperados.
    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule `
        -Name "Isolate the $VNetName VNet from the Internet" `
        -Type Inbound -Priority 140 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *
    ```
7. A regra final recusa o tráfego da sub-rede do front-end para a sub-rede de back-end. Uma vez que esta regra é uma única regra de entrada, o tráfego inverso é permitido (a partir do back-end para o front-end).

    ```PowerShell
    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule `
        -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" `
        -Type Inbound -Priority 150 -Action Deny `
        -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
        -DestinationAddressPrefix $BEPrefix `
        -DestinationPortRange '*' `
        -Protocol * 
    ```

## <a name="traffic-scenarios"></a>Cenários de tráfego
#### <a name="allowed-internet-to-web-server"></a>(*Permitidos*) Internet para o servidor web
1. Um utilizador de internet solicita uma página HTTP do FrontEnd001.CloudApp.Net (Internet do serviço da nuvem com acesso à)
2. Tráfego de passes do serviço por meio de pontos finais abertos na porta 80 em direção à IIS01 da cloud (o servidor web)
3. A sub-rede de front-end começa o processamento da regra de entrada:
   1. 1 de regra de NSG (DNS) não se aplicam, mudar para a próxima regra
   2. 2 de regra de NSG (RDP) não se aplicam, mudar para a próxima regra
   3. Aplicar a regra de NSG 3 (Internet para IIS01), o tráfego é o processamento da regra permitidos, pare
4. O tráfego chega o endereço IP interno do servidor web IIS01 (10.0.1.5N)
5. IIS01 está à escuta de tráfego da web, recebe essa solicitação e começa a processar o pedido
6. IIS01 pede-lhe o SQL Server no AppVM01 informações
7. Uma vez que não existem regras de saída na sub-rede de front-end, o tráfego é permitido
8. A sub-rede de back-end começa o processamento da regra de entrada:
   1. 1 de regra de NSG (DNS) não se aplicam, mudar para a próxima regra
   2. 2 de regra de NSG (RDP) não se aplicam, mudar para a próxima regra
   3. 3 de regra de NSG (Internet à Firewall) não se aplicam, mudar para a próxima regra
   4. 4 de regra de NSG (IIS01 para AppVM01) é aplicável, o tráfego é permitido, parar o processamento da regra
9. AppVM01 recebe a consulta SQL e responde
10. Uma vez que não existem regras de saída da sub-rede de back-end, a resposta é permitida
11. A sub-rede de front-end começa o processamento da regra de entrada:
    1. Não existe nenhuma regra NSG aplica-se a entrada tráfego da sub-rede de back-end para a sub-rede de front-end, para que nenhuma do NSG de regras aplicam-se
    2. A regra predefinida do sistema a permitir o tráfego entre sub-redes permitiria que este tráfego para que o tráfego é permitido.
12. O servidor IIS recebe a resposta SQL e conclui a resposta HTTP e envia para o requerente
13. Uma vez que existem regras de saída da sub-rede de front-end é permitida a resposta e internet o utilizador recebe a página da web solicitado.

#### <a name="allowed-rdp-to-backend"></a>(*Permitidos*) RDP para back-end
1. Administrador do servidor na internet solicita a sessão do RDP para AppVM01 no BackEnd001.CloudApp.Net:xxxxx onde xxxxx é o número de porta aleatoriamente atribuída para RDP para AppVM01 (a porta atribuída pode ser encontrada no portal do Azure ou através do PowerShell)
2. A sub-rede de back-end começa o processamento da regra de entrada:
   1. 1 de regra de NSG (DNS) não se aplicam, mudar para a próxima regra
   2. Aplicar a regra de NSG 2 (RDP), o tráfego é o processamento da regra permitidos, pare
3. Com não existem regras de saída, aplicam-se de regras predefinidas e tráfego de retorno é permitido
4. Sessão RDP está ativada
5. AppVM01 pede-lhe o nome de utilizador e palavra-passe

#### <a name="allowed-web-server-dns-look-up-on-dns-server"></a>(*Permitidos*) consulta DNS do servidor Web no servidor DNS
1. Web Server, IIS01, necessidades de dados de um feed em www.data.gov, mas tem de resolver o endereço.
2. A configuração de rede para as listas de VNet DNS01 (10.0.2.4 na sub-rede de back-end) que o servidor DNS primário, IIS01 envia o pedido DNS para DNS01
3. Não existem regras de saída na sub-rede de front-end, o tráfego é permitido
4. A sub-rede de back-end começa o processamento da regra de entrada:
   * Aplicar a regra de NSG 1 (DNS), o tráfego é o processamento da regra permitidos, pare
5. Servidor DNS recebe o pedido
6. Servidor DNS não tem o endereço em cache e pede-lhe um servidor DNS de raiz na internet
7. Não existem regras de saída na sub-rede de back-end, o tráfego é permitido
8. Servidor DNS de Internet responde, uma vez que esta sessão foi iniciada internamente, a resposta é permitida
9. Servidor DNS coloca a resposta em cache e responde à solicitação inicial para IIS01
10. Não existem regras de saída na sub-rede de back-end, o tráfego é permitido
11. A sub-rede de front-end começa o processamento da regra de entrada:
    1. Não existe nenhuma regra NSG aplica-se a entrada tráfego da sub-rede de back-end para a sub-rede de front-end, para que nenhuma do NSG de regras aplicam-se
    2. A regra predefinida do sistema a permitir o tráfego entre sub-redes permitiria que este tráfego para que o tráfego é permitido
12. IIS01 recebe a resposta do DNS01

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(*Permitidos*) ficheiro de acesso do servidor Web no AppVM01
1. IIS01 solicita um ficheiro num AppVM01
2. Não existem regras de saída na sub-rede de front-end, o tráfego é permitido
3. A sub-rede de back-end começa o processamento da regra de entrada:
   1. 1 de regra de NSG (DNS) não se aplicam, mudar para a próxima regra
   2. 2 de regra de NSG (RDP) não se aplicam, mudar para a próxima regra
   3. Regra de NSG 3 (Internet para IIS01) não se aplicam, mova a seguinte regra
   4. 4 de regra de NSG (IIS01 para AppVM01) é aplicável, o tráfego é permitido, parar o processamento da regra
4. AppVM01 recebe a solicitação e responde com o ficheiro (partindo do princípio de acesso está autorizado)
5. Uma vez que não existem regras de saída da sub-rede de back-end, a resposta é permitida
6. A sub-rede de front-end começa o processamento da regra de entrada:
   1. Não existe nenhuma regra NSG aplica-se a entrada tráfego da sub-rede de back-end para a sub-rede de front-end, para que nenhuma do NSG de regras aplicam-se
   2. A regra predefinida do sistema a permitir o tráfego entre sub-redes permitiria que este tráfego para que o tráfego é permitido.
7. O servidor IIS recebe o ficheiro.

#### <a name="denied-web-to-backend-server"></a>(*Negado*) Web para o servidor back-end
1. Um utilizador de internet tentar acessar um arquivo no AppVM01 através do serviço de BackEnd001.CloudApp.Net
2. Uma vez que não existem não existem pontos finais aberta para a partilha de ficheiros, este tráfego não passa o serviço em nuvem e não alcançar o servidor
3. Se os pontos de extremidade foram abertos por algum motivo, a regra NSG 5 (Internet a VNet) bloquearia deste tráfego

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*Negado*) consulta de DNS de Web no servidor DNS
1. Um utilizador de internet tenta procurar um registo DNS interno no DNS01 através do serviço de BackEnd001.CloudApp.Net
2. Uma vez que não existem não existem pontos finais aberta para DNS, este tráfego não passa o serviço em nuvem e não alcançar o servidor
3. Se os pontos de extremidade foram abertos por algum motivo, a regra NSG 5 (Internet a VNet) bloquearia este tráfego (Nota: essa regra 1 (DNS) não seriam aplicadas por dois motivos, primeiro o endereço de origem é a internet, esta regra aplica-se apenas a VNet local como origem também esta regra é uma regra de permissão, para que ele nunca seria a negar o tráfego)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(*Negado*) Web para o acesso através da firewall do SQL
1. Um utilizador de internet solicita dados SQL FrontEnd001.CloudApp.Net (Internet do serviço da nuvem com acesso à)
2. Uma vez que não existem não existem pontos finais aberta para SQL, este tráfego não passa o serviço em nuvem e não alcançar o firewall
3. Se os pontos de extremidade foram abertos por algum motivo, a sub-rede de front-end começa o processamento da regra de entrada:
   1. 1 de regra de NSG (DNS) não se aplicam, mudar para a próxima regra
   2. 2 de regra de NSG (RDP) não se aplicam, mudar para a próxima regra
   3. Aplicar a regra de NSG 3 (Internet para IIS01), o tráfego é o processamento da regra permitidos, pare
4. O tráfego chega o endereço IP interno do IIS01 (10.0.1.5N)
5. IIS01 não está a escutar na porta 1433, então, não há resposta ao pedido

## <a name="conclusion"></a>Conclusão
Neste exemplo é uma maneira relativamente simples e direta de isolar a sub-rede de back-end do tráfego de entrada.

Mais exemplos e uma descrição geral dos limites de segurança de rede podem ser encontradas [aqui][HOME].

## <a name="references"></a>Referências
### <a name="main-script-and-network-config"></a>Configuração de script e de rede principal
Guarde o Script completo num arquivo de script do PowerShell. Guardar a configuração de rede para um ficheiro denominado "NetworkConf1.xml."
Modifique as variáveis definidas pelo utilizador, conforme necessário e executar o script.

#### <a name="full-script"></a>Script completo
Este script irá, com base em variáveis definidas pelo utilizador;

1. Ligar a uma subscrição do Azure
2. Criar uma conta de armazenamento
3. Criar uma VNet e duas sub-redes, conforme definido no ficheiro de configuração de rede
4. Criar quatro windows VMs de servidor
5. Configure o NSG incluindo:
   * Criar um NSG
   * Preenchendo-o com as regras
   * O NSG de ligação para as sub-redes apropriadas

Este script do PowerShell deve ser executado localmente num que Internet ligado PC ou servidor.

> [!IMPORTANT]
> Quando esse script for executado, pode haver avisos ou outras mensagens de informações que exibida no PowerShell. Apenas mensagens de erro em vermelho são o motivo de preocupação.
> 
>

```PowerShell
<# 
 .SYNOPSIS
  Example of Network Security Groups in an isolated network (Azure only, no hybrid connections)

 .DESCRIPTION
  This script will build out a sample DMZ setup containing:
   - A default storage account for VM disks
   - Two new cloud services
   - Two Subnets (FrontEnd and BackEnd subnets)
   - One server on the FrontEnd Subnet
   - Three Servers on the BackEnd Subnet
   - Network Security Groups to allow/deny traffic patterns as declared

  Before running script, ensure the network configuration file is created in
  the directory referenced by $NetworkConfigFile variable (or update the
  variable to reflect the path and file name of the config file being used).

 .Notes
  Security requirements are different for each use case and can be addressed in a
  myriad of ways. Please be sure that any sensitive data or applications are behind
  the appropriate layer(s) of protection. This script serves as an example of some
  of the techniques that can be used, but should not be used for all scenarios. You
  are responsible to assess your security needs and the appropriate protections
  needed, and then effectively implement those protections.

  FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
   IIS01      - 10.0.1.5

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

# User-Defined Global Variables
  # These should be changes to reflect your subscription and services
  # Invalid options will fail in the validation section

  # Subscription Access Details
    $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

  # VM Account, Location, and Storage Details
    $LocalAdmin = "theAdmin"
    $DeploymentLocation = "Central US"
    $StorageAccountName = "vmstore02"

  # Service Details
    $FrontEndService = "FrontEnd001"
    $BackEndService = "BackEnd001"

  # Network Details
    $VNetName = "CorpNetwork"
    $FESubnet = "FrontEnd"
    $FEPrefix = "10.0.1.0/24"
    $BESubnet = "BackEnd"
    $BEPrefix = "10.0.2.0/24"
    $NetworkConfigFile = "C:\Scripts\NetworkConf1.xml"

  # VM Base Disk Image Details
    $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

  # NSG Details
    $NSGName = "MyVNetSG"

# User-Defined VM Specific Configuration
    # Note: To ensure proper NSG Rule creation later in this script:
    #       - The Web Server must be VM 0
    #       - The AppVM1 Server must be VM 1
    #       - The DNS server must be VM 3
    #
    #       Otherwise the NSG rules in the last section of this
    #       script will need to be changed to match the modified
    #       VM array numbers ($i) so the NSG Rule IP addresses
    #       are aligned to the associated VM IP addresses.

    # VM 0 - The Web Server
      $VMName += "IIS01"
      $ServiceName += $FrontEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $FESubnet
      $VMIP += "10.0.1.5"

    # VM 1 - The First Application Server
      $VMName += "AppVM01"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.5"

    # VM 2 - The Second Application Server
      $VMName += "AppVM02"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.6"

    # VM 3 - The DNS Server
      $VMName += "DNS01"
      $ServiceName += $BackEndService
      $VMFamily += "Windows"
      $img += $SrvImg
      $size += "Standard_D3"
      $SubnetName += $BESubnet
      $VMIP += "10.0.2.4"

# ----------------------------- #
# No User-Defined Variables or  #
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

If (Test-AzureName -Service -Name $FrontEndService) { 
    Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
    $FatalError = $true}
Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

If (Test-AzureName -Service -Name $BackEndService) { 
    Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
    $FatalError = $true}
Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

If (-Not (Test-Path $NetworkConfigFile)) { 
    Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
    $FatalError = $true}
Else { Write-Host "The network configuration file was found" -ForegroundColor Green
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
    New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
    New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

# Build VMs
    $i=0
    $VMName | Foreach {
        Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
        New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
            Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
            Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
            Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
            Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
            Remove-AzureEndpoint -Name "PowerShell" | `
            New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
            # Note: A Remote Desktop endpoint is automatically created when each VM is created.
        $i++
    }
    # Add HTTP Endpoint for IIS01
    Get-AzureVM -ServiceName $ServiceName[0] -Name $VMName[0] | Add-AzureEndpoint -Name HTTP -Protocol tcp -LocalPort 80 -PublicPort 80 | Update-AzureVM

# Configure NSG
    Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

  # Build the NSG
    Write-Host "Building the NSG" -ForegroundColor Cyan
    New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

  # Add NSG Rules
    Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[3] -DestinationPortRange '53' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
        -SourceAddressPrefix Internet -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[0]) to $($VMName[1])" -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[0] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[1] -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
        -Protocol *

    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
        -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
        -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
        -Protocol *

    # Assign the NSG to the Subnets
        Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

# Optional Post-script Manual Configuration
  # Install Test Web App (Run Post-Build Script on the IIS Server)
  # Install Backend resource (Run Post-Build Script on the AppVM01)
  Write-Host
  Write-Host "Build Complete!" -ForegroundColor Green
  Write-Host
  Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
  Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
  Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
  Write-Host
```

#### <a name="network-config-file"></a>Ficheiro de configuração de rede
Guarde este ficheiro xml com localização atualizada e adicionar a ligação a este ficheiro para a variável de $NetworkConfigFile no script anterior.

```XML
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

## <a name="next-steps"></a>Passos Seguintes
* Atualizar e guardar o ficheiro XML
* Execute o script do PowerShell para o ambiente de compilação
* Instalar a aplicação de exemplo
* Testar os fluxos de tráfego diferentes através desta rede de Perímetro

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-asm/example1design.png "Rede de Perímetro com NSG de entrada"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md

