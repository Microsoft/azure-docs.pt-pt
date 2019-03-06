---
title: Exemplo de rede de perímetro – criar uma rede de perímetro a proteger aplicações com um firewall e NSGs | Documentos da Microsoft
description: Criar uma rede de perímetro com uma firewall e de grupos de segurança de rede (NSGs)
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: c78491c7-54ac-4469-851c-b35bfed0f528
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: c1c64945aaa0bc4cd83cc769dab1c2a755896c01
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57442483"
---
# <a name="example-2-build-a-perimeter-network-to-protect-applications-with-a-firewall-and-nsgs"></a>Exemplo 2: Criar uma rede de perímetro a proteger aplicações com um firewall e NSGs
[Retornar à página de segurança de rede e serviços cloud da Microsoft][HOME]

Este exemplo mostra como criar uma rede de perímetro (também conhecido como *DMZ*, *zona desmilitarizada*, e *sub-rede filtrada*) com uma firewall, quatro computadores do Windows Server, e grupos de segurança de rede (NSGs). Ele inclui detalhes sobre cada um dos comandos relevantes para fornecer uma compreensão mais aprofundada de cada passo. A secção "Cenários de tráfego" fornece uma explicação passo a passo de como o tráfego passa pelas camadas de defesa na rede de perímetro. Por fim, a seção "Referências" fornece o código completo e instruções sobre como criar este ambiente de teste e experimente com diferentes cenários.

![Rede de perímetro com NSGs e o NVA de entrada][1]

## <a name="environment"></a>Ambiente 
Este exemplo se baseia num cenário com uma subscrição do Azure que contém estes itens:

* Dois serviços de cloud: FrontEnd001 e BackEnd001.
* Uma rede virtual com o nome CorpNetwork com duas sub-redes: Front-end e back-end.
* Um único NSG aplicado a ambas as sub-redes.
* Uma aplicação virtual de rede: Um Barracuda NextGen Firewall ligada à sub-rede de front-end.
* Um computador Windows Server que representa um servidor de web de aplicações: IIS01.
* Dois computadores de servidor do Windows que representam servidores de back-end de aplicação: AppVM01 e AppVM02.
* Um computador Windows Server que representa um servidor DNS: DNS01.

> [!NOTE]
> Embora este exemplo utiliza um Barracuda NextGen Firewall, muitos dispositivos de rede virtual podem ser utilizados.
> 
> 

O script do PowerShell na secção "Referências" deste artigo baseia-se a maioria do ambiente descrito aqui. As VMs e redes virtuais são criadas pelo script, mas esses processos não estão descritos em detalhe neste documento.

Para criar o ambiente:

1. Guarde o ficheiro XML de configuração de rede incluído na secção "Referências" (atualizado com os nomes, locais e IP endereços para corresponder ao seu cenário).
2. Atualize as variáveis definidas pelo utilizador no script do PowerShell para corresponder ao ambiente de que o script será executado contra (subscrições, os nomes de serviço e assim por diante).
3. Execute o script do PowerShell.

> [!NOTE]
> A região especificada no script do PowerShell têm de corresponder a região especificada no ficheiro XML de configuração de rede.
>
>

Depois do script é executado com êxito, pode concluir estes passos:

1. Configure as regras de firewall. Consulte a secção "Regras de Firewall" deste artigo.
2. Se quiser, pode configurar o servidor web e o servidor de aplicações com uma aplicação web simples para permitir que o teste com a configuração de rede de perímetro. Pode usar os scripts de duas aplicações fornecidos na seção "Referências".

A secção seguinte explica a maioria das declarações do script que se relacionam com NSGs.

## <a name="nsgs"></a>NSGs
Neste exemplo, um grupo NSG é criado e, em seguida, é carregado com regras de seis.

> [!TIP]
> Em geral, deve criar as regras específicas de "Permitir de" primeiro e último as regras mais genéricas de "Negar". Os controles de prioridade atribuída que as regras são avaliadas primeiro. Após encontra o tráfego que aplica-se a uma regra específica, não existem regras adicionais são avaliadas. As regras do NSG podem aplicar a entrada ou a direção de saída (a partir do ponto de vista da sub-rede).
> 
> 

De forma declarativa, estas regras são criadas para o tráfego de entrada:

1. O tráfego DNS interno (porta 53) é permitido.
2. Tráfego RDP (porta 3389) da internet para qualquer VM é permitido.
3. Tráfego HTTP (porta 80) da internet para a NVA (firewall) é permitido.
4. Todo o tráfego (todas as portas) de IIS01 AppVM01 é permitido.
5. É negado todo o tráfego (todas as portas) da internet para a rede virtual inteira (ambas as sub-redes).
6. Todo o tráfego (todas as portas) partir da sub-rede FrontEnd para a sub-rede de back-end é negado.

Com estas regras vinculadas a cada sub-rede, se um pedido HTTP foram entrado da internet para o servidor web, ambos da regra 3 (permitir) e regra 5 (negar) parece se aplicam, mas como a regra 3 tem uma prioridade mais alta, ele será aplicado apenas. Regra 5 não entram em cena. Portanto, a solicitação HTTP terá permissão para o firewall.

Se esse mesmo tráfego estava a tentar aceder ao servidor DNS01, regra 5 (negar) seria o primeiro a aplicar, para que o tráfego não ter permissão para passar para o servidor. Regra 6 (negar) bloqueia a sub-rede de front-end de comunicar com a sub-rede de back-end (exceto para o tráfego permitido nas regras 1 e 4). Esta ação protege a rede de back-end, no caso de um invasor compromete a aplicação web no front-end. Nesse caso, o invasor teria tem acesso limitado à rede de back-end "protegido". (O invasor seria capaz de acessar apenas os recursos expostos no servidor AppVM01.)

Existe uma regra de saída predefinida que permita o tráfego de saída à internet. Neste exemplo, estamos a permitir o tráfego de saída e sem modificar quaisquer regras de saída. Para bloquear o tráfego em ambas as direções, terá de encaminhamento definido pelo utilizador. Pode aprender sobre essa técnica num exemplo diferente no [documento de limites de segurança principal][HOME].

As regras do NSG descritas aqui são semelhantes às regras NSG no [exemplo 1 - criar um DMZ simple com NSGs][Example1]. Reveja a descrição de NSG nesse artigo para uma visão detalhada de cada regra NSG e seus atributos.

## <a name="firewall-rules"></a>Regras da firewall
Tem de instalar um cliente de gestão num computador para gerir a firewall e criar as configurações necessárias. Veja a documentação do seu firewall (ou outra NVA) do fornecedor sobre como gerir o dispositivo. O resto desta secção descreve a configuração do firewall em si, através do cliente de gestão do fornecedor (não o portal do Azure ou o PowerShell).

Ver [Barracuda NG administrador](https://techlib.barracuda.com/NG61/NGAdmin) para obter instruções de transferência do cliente e ligar-se a firewall do Barracuda utilizado neste exemplo.

Tem de criar regras de reencaminhamento na firewall. Uma vez que o cenário neste exemplo apenas encaminha o tráfego de internet de entrada para o firewall e, em seguida, para o servidor web, só tem de reencaminhamento de uma regra NAT. Na firewall de Barracuda utilizado neste exemplo, a regra seria uma regra NAT de destino (horário de Verão NAT) para passar este tráfego.

Para criar a regra seguinte (ou para verificar as regras existentes do padrão), conclua estes passos:
1. No painel de cliente do administrador de NG Barracuda, na guia configuração, no **configuração operacionais** secção, selecione **Ruleset**. 

   Uma grade chamado **Main regras** mostra existente Active Directory e desativado regras na firewall.

2. Para criar uma nova regra, selecione o pequeno verde **+** botão no canto superior direito dessa grade. (A firewall pode estar bloqueada. Se vir um botão marcado **bloqueio** e não é possível criar ou editar regras, selecione o botão para desbloquear o conjunto de regras e permitir a edição.)
  
3. Para editar uma regra existente, selecione a regra, o botão direito do mouse e a em seguida, selecione **Editar regra de**.

Criar uma nova regra com nomes como **WebTraffic.** 

O ícone de regra NAT de destino tem esta aparência: ![Ícone NAT de destino][2]

A regra em si será algo parecido com isto:

![Regra de firewall][3]

Qualquer endereço de entrada que chegue o firewall tentando alcançar HTTP (porta 80 ou 443 para HTTPS) será enviado para fora da interface de DHCP1 de IP Local do firewall e redirecionado para o servidor web com o endereço IP 10.0.1.5N. Uma vez que o tráfego é entram na porta 80 e vai para o servidor web na porta 80, não é necessária nenhuma alteração de porta. Mas a lista de destino poderia ter sido 10.0.1.5:8080 se o servidor web escutámos na porta 8080, que converte a entrada na porta 80 na firewall para a porta de entrada 8080 no servidor web.

Também deve especificar um método de ligação. Para a regra de destino da internet, SNAT dinâmico é o método mais apropriado.

Mesmo que apenas criar uma regra, é importante definir corretamente sua prioridade. Na grelha de todas as regras na firewall, se esta nova regra é na parte inferior (abaixo a regra BLOCKALL), ele será nunca entram em cena. Certificar-se de que a nova regra para o tráfego da web for superior a regra BLOCKALL.

Depois de criar a regra, precisa enviá-la para o firewall e, em seguida, ativá-lo. Se não considerar estes passos, a alteração de regra não entre em vigor. A secção seguinte descreve o processo de ativação e de push.

## <a name="rule-activation"></a>Ativação de regra
Agora que a regra é adicionada para o conjunto de regras, terá de carregar o conjunto de regras do firewall e ativá-lo.

![Ativação de regra de firewall][4]

No canto superior direito do cliente de gestão, verá um grupo de botões. Selecione **enviar alterações** para enviar o conjunto de regras de modificação para a firewall e, em seguida, selecione **ativar**.

Agora que ativou o conjunto de regras de firewall, o ambiente está concluído. Se quiser, pode executar os scripts de aplicativo de exemplo na secção "Referências" para adicionar uma aplicação no ambiente. Se adicionar um aplicativo, pode testar os cenários de tráfego descritos na secção seguinte.

> [!IMPORTANT]
> Deve ter percebido que não haverá diretamente o servidor web. Quando um navegador solicita uma página HTTP do FrontEnd001.CloudApp.Net, o ponto final de HTTP (porta 80) passa o tráfego para o firewall, não para o servidor web. O firewall, em seguida, devido a regra que criou anteriormente, utiliza NAT para mapear a solicitação ao servidor web.
> 
> 

## <a name="traffic-scenarios"></a>Cenários de tráfego
#### <a name="allowed-web-to-web-server-through-the-firewall"></a>(Permitido) Web para servidor web através da firewall
1. Um utilizador de internet solicita uma página HTTP FrontEnd001.CloudApp.Net (um serviço em nuvem com acesso à internet).
2. O serviço em nuvem passa o tráfego através de um ponto de extremidade aberto na porta 80 à interface local da firewall no 10.0.1.4:80.
3. A sub-rede de front-end é iniciado o processamento da regra de entrada:
   1. Regra NSG 1 (DNS) não se aplica. Mover para a regra seguinte.
   2. Regra NSG 2 (RDP) não se aplica. Mover para a regra seguinte.
   3. Aplicam-se a regra NSG 3 (internet à firewall). Permitir o tráfego. Pare o processamento da regra.
4. O tráfego chega o endereço IP interno do firewall (10.0.1.4).
5. Uma regra de encaminhamento de firewall determina que este é o tráfego de porta 80 e redireciona-o para o servidor de web IIS01.
6. IIS01 está à escuta de tráfego da web, recebe o pedido e começa a processar o pedido.
7. IIS01 solicita as informações de instância do SQL Server no AppVM01.
8. Não existem regras de saída da sub-rede de front-end, para que o tráfego é permitido.
9. A sub-rede de back-end é iniciado o processamento da regra de entrada:
   1. Regra NSG 1 (DNS) não se aplica. Mover para a regra seguinte.
   2. Regra NSG 2 (RDP) não se aplica. Mover para a regra seguinte.
   3. Regra NSG 3 (internet à firewall) não se aplica. Mover para a regra seguinte.
   4. Regra NSG 4 (IIS01 para AppVM01) é aplicável. Permitir o tráfego. Pare o processamento da regra.
10. A instância do SQL Server no AppVM01 recebe a solicitação e responde.
11. Como não há nenhuma regra de saída da sub-rede de back-end, a resposta é permitida.
12. A sub-rede de front-end é iniciado o processamento da regra de entrada:
    1. Não existe nenhuma regra NSG aplica-se a entrada de tráfego da sub-rede de back-end para a sub-rede de front-end, para que nenhuma das regras do NSG aplicam-se.
    2. A regra predefinida do sistema a permitir o tráfego entre sub-redes permite que este tráfego, para que o tráfego é permitido.
13. IIS01 recebe a resposta do AppVM01, conclui a resposta HTTP e envia-os para o requerente.
14. Uma vez que esta é uma sessão NAT do firewall, o destino de resposta é inicialmente para o firewall.
15. O firewall recebe a resposta do servidor web e reencaminha-o para o usuário de internet.
16. Como não há nenhuma regra de saída da sub-rede de front-end, a resposta é permitida e o utilizador de internet recebe a página da web.

#### <a name="allowed-rdp-to-backend"></a>(Permitido) RDP para back-end
1. Um administrador do servidor na internet solicita uma sessão do RDP para AppVM01 no BackEnd001.CloudApp.Net:*xxxxx*, onde *xxxxx* é o número de porta aleatoriamente atribuída para RDP para AppVM01. (Pode encontrar a porta atribuída no portal do Azure ou através do PowerShell.)
2. Uma vez que a firewall está a escutar apenas o endereço de FrontEnd001.CloudApp.Net, não está envolvida com este fluxo de tráfego.
3. A sub-rede de back-end é iniciado o processamento da regra de entrada:
   1. Regra NSG 1 (DNS) não se aplica. Mover para a regra seguinte.
   2. Aplicar a regra NSG 2 (RDP). Permitir o tráfego. Pare o processamento da regra.
4. Como não há nenhuma regra de saída, as regras predefinidas aplicam-se e retornam o tráfego é permitido.
5. A sessão do RDP está ativada.
6. AppVM01 solicita um nome de utilizador e palavra-passe.

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Permitido) Pesquisa de DNS do servidor Web no servidor DNS
1. As necessidades de servidor, IIS01, web, dados de um feed de cada www.data.gov, mas tem de resolver o endereço.
2. A configuração de rede para mostra a rede virtual DNS01 (10.0.2.4 na sub-rede de back-end) que o servidor DNS principal. IIS01 envia o pedido DNS para DNS01.
3. Como não há nenhuma regra de saída da sub-rede de front-end, o tráfego é permitido.
4. A sub-rede de back-end é iniciado o processamento da regra de entrada:
   1. Se aplica a regra NSG 1 (DNS). Permitir o tráfego. Pare o processamento da regra.
5. O servidor DNS recebe o pedido.
6. O servidor DNS não tem o endereço em cache e consulta um servidor DNS de raiz na internet.
7. Como não há nenhuma regra de saída da sub-rede de back-end, o tráfego é permitido.
8. O DNS da internet servidor responde. Uma vez que a sessão foi iniciada internamente, a resposta é permitida.
9. O servidor DNS coloca a resposta em cache e responde ao pedido de IIS01.
10. Como não há nenhuma regra de saída da sub-rede de back-end, o tráfego é permitido.
11. A sub-rede de front-end é iniciado o processamento da regra de entrada:
    1. Não existe nenhuma regra NSG aplica-se a entrada de tráfego da sub-rede de back-end para a sub-rede de front-end, para que nenhuma das regras do NSG aplicam-se.
    2. A regra predefinida do sistema a permitir o tráfego entre sub-redes permite que este tráfego, para que o tráfego é permitido.
12. IIS01 recebe a resposta do DNS01.

#### <a name="allowed-web-server-file-access-on-appvm01"></a>(Permitido) Acesso de arquivo de servidor Web em AppVM01
1. IIS01 solicita um ficheiro no AppVM01.
2. Como não há nenhuma regra de saída da sub-rede de front-end, o tráfego é permitido.
3. A sub-rede de back-end é iniciado o processamento da regra de entrada:
   1. Regra NSG 1 (DNS) não se aplica. Mover para a regra seguinte.
   2. Regra NSG 2 (RDP) não se aplica. Mover para a regra seguinte.
   3. Regra NSG 3 (internet à firewall) não se aplica. Mover para a regra seguinte.
   4. Regra NSG 4 (IIS01 para AppVM01) é aplicável. Permitir o tráfego. Pare o processamento da regra.
4. AppVM01 recebe a solicitação e responde com o ficheiro (partindo do princípio de acesso está autorizado).
5. Como não há nenhuma regra de saída da sub-rede de back-end, a resposta é permitida.
6. A sub-rede de front-end é iniciado o processamento da regra de entrada:
   1. Não existe nenhuma regra NSG aplica-se a entrada de tráfego da sub-rede de back-end para a sub-rede de front-end, para que nenhuma das regras do NSG aplicam-se.
   2. A regra predefinida do sistema a permitir o tráfego entre sub-redes permite que este tráfego, para que o tráfego é permitido.
7. IIS01 recebe o ficheiro.

#### <a name="denied-web-direct-to-web-server"></a>(Negado) Web direto para o servidor web
Uma vez que o servidor de web IIS01 e o firewall estiverem no mesmo serviço cloud, eles compartilham o mesmo endereço IP destinado ao público. Portanto, qualquer tráfego HTTP é direcionado para o firewall. Enquanto um pedido deve ser servido com êxito, não é possível ir diretamente para o servidor web. Ele passa, conforme projetado, através da firewall pela primeira vez. Ver o primeiro cenário nesta secção para o fluxo de tráfego.

#### <a name="denied-web-to-backend-server"></a>(Negado) Web para o servidor back-end
1. Um utilizador de internet tenta acessar um arquivo no AppVM01 através do serviço de BackEnd001.CloudApp.Net.
2. Como não há nenhum pontos de extremidade aberta para a partilha de ficheiros, isto não passa o serviço em nuvem e não alcançar o servidor.
3. Se os pontos finais estiverem abertos por algum motivo, a regra NSG 5 (internet para a rede virtual) bloqueia o tráfego.

#### <a name="denied-web-dns-lookup-on-the-dns-server"></a>(Negado) Pesquisa de DNS da Web no servidor DNS
1. Um utilizador de internet tenta procurar um registo DNS interno no DNS01 através do serviço de BackEnd001.CloudApp.Net.
2. Como não há nenhum pontos de extremidade aberta para DNS, isso não passa o serviço em nuvem e não alcançar o servidor.
3. Se os pontos finais estiverem abertos por algum motivo, a regra NSG 5 (internet para a rede virtual) bloqueia o tráfego. (Regra 1 [DNS] não se aplica por dois motivos. Em primeiro lugar, o endereço de origem é a internet, e esta regra aplica-se apenas quando a rede virtual local é a origem. Em segundo lugar, esta regra é uma regra de permissão, para que ele nunca recusa o tráfego.)

#### <a name="denied-web-to-sql-access-through-the-firewall"></a>(Negado) Web para acesso SQL através da firewall
1. Um utilizador de internet solicita dados do SQL FrontEnd001.CloudApp.Net (um serviço cloud de acesso à internet).
2. Como não há nenhum pontos de extremidade aberta para SQL, isso não passa o serviço em nuvem e não atingirá o firewall.
3. Se os pontos finais estiverem abertos por algum motivo, a sub-rede de front-end é iniciado o processamento de regra de entrada:
   1. Regra NSG 1 (DNS) não se aplica. Mover para a regra seguinte.
   2. Regra NSG 2 (RDP) não se aplica. Mover para a regra seguinte.
   3. Aplicam-se a regra NSG 3 (internet à firewall). Permitir o tráfego. Pare o processamento da regra.
4. O tráfego chega o endereço IP interno do firewall (10.0.1.4).
5. O firewall tem não existem regras de reencaminhamento para SQL e ignora o tráfego.

## <a name="conclusion"></a>Conclusão
Este exemplo mostra uma maneira relativamente simples para proteger a sua aplicação com um firewall e isolar a sub-rede de back-end do tráfego de entrada.

Pode encontrar mais exemplos e uma descrição geral da rede limites de segurança [aqui][HOME].

## <a name="references"></a>Referências
### <a name="full-script-and-network-config"></a>Configuração de rede e o script completa
Guarde o script completo num arquivo de script do PowerShell. Salve o script de configuração de rede num arquivo chamado NetworkConf2.xml.
Altere as definidas-variáveis de utilizador, conforme necessário. Execute o script e, em seguida, siga as instruções na secção "Regras de Firewall" deste artigo.

#### <a name="full-script"></a>Script completo
Este script, com base em variáveis definidas pelo utilizador, irá concluir os seguintes passos:

1. Ligar a uma subscrição do Azure.
2. Criar uma conta de armazenamento.
3. Crie uma rede virtual e duas sub-redes, conforme definido no ficheiro de configuração de rede.
4. Crie quatro VMs do Windows Server.
5. Configure o NSG. Configuração concluir estes passos:
   * Cria um NSG.
   * Preenche o NSG com regras.
   * Associa o NSG para as sub-redes apropriadas.

Deve executar este script do PowerShell localmente num computador ligado à internet ou servidor.

> [!IMPORTANT]
> Quando executar esse script, avisos e de outras mensagens informativas podem aparecer no PowerShell. Apenas terá de se preocupar com mensagens de erro que aparecem em vermelho.
> 
> 

```powershell
    <# 
     .SYNOPSIS
      Example of a perimeter network and Network Security Groups in an isolated network. (Azure only. No hybrid connections.)

     .DESCRIPTION
      This script will build out a sample perimeter network setup containing:
       - A default storage account for VM disks.
       - Two new cloud services.
       - Two subnets (the FrontEnd and BackEnd subnets).
       - A network virtual appliance (NVA): a Barracuda NextGen Firewall.
       - One server on the FrontEnd subnet (plus the NVA on the FrontEnd subnet).
       - Three servers on the BackEnd subnet.
       - Network Security Groups to allow/deny traffic patterns as declared.

      Before running the script, ensure the network configuration file is created in
      the directory referenced by the $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Security requirements are different for each use case and can be addressed in many ways. Be sure that any sensitive data or applications are behind
      the appropriate layer(s) of protection. This script serves as an example of some
      of the techniques that you can use, but it should not be used for all scenarios. You
      are responsible for assessing your security needs and the appropriate protections
      and then effectively implementing those protections.

      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       myFirewall - 10.0.1.4
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
      # These should be changed to reflect your subscription and services.
      # Invalid options will fail in the validation section.

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
        $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # NSG Details
        $NSGName = "MyVNetSG"

    # User-Defined VM-Specific Config
        # To ensure proper NSG rule creation later in this script:
        #       - The web server must be VM 1.
        #       - The AppVM1 server must be VM 2.
        #       - The DNS server must be VM 4.
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG rule IP addresses
        #       are aligned to the associated VM IP addresses.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $FrontEndService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"

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
    # No user-defined variables or   #
    # configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create storage account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update subscription pointer to new storage account
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
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create virtual network
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create services
        Write-Host "Creating Services" -ForegroundColor Cyan
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
                # Set up all the endpoints we'll need once we're up and running.
                # Note: Web traffic goes through the firewall, so we'll need to set up an HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: An SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                    # Note: A Remote Desktop endpoint is automatically created when each VM is created.
                }
            $i++
        }

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
            -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
            -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
            -Protocol *

        # Assign the NSG to the subnets
            Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-Script Manual Configuration
      # Configure firewall
      # Install test web app (run post-build script on the IIS server)
      # Install back-end resources (run post-build script on AppVM01)
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
Guarde este ficheiro XML com localizações atualizadas e, em seguida, adicionar uma ligação para este ficheiro na variável $NetworkConfigFile no script anterior.

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
Se pretender instalar um aplicativo de exemplo para esse e outros exemplos de rede de perímetro, consulte a [script de aplicação de exemplo][SampleApp].

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "Rede de Perímetro com NSG de entrada"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Ícone NAT de destino"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Regra de firewall"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Ativação de regra de firewall"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
