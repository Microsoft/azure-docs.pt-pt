---
title: 'Tutorial: Execute a captura de pacotes nas ligações site-to-site da Azure Virtual WAN'
description: Neste tutorial, aprenda a realizar a captura de pacotes no Portal VPN local do Local de WAN Virtual.
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: wellee
Customer intent: As someone with a networking background using Virtual WAN, I want to perform a packet capture on my Site-to-site VPN Gateway.
ms.openlocfilehash: dbe7e06484797063ed4122ee3fdde625dc66c41f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879171"
---
# <a name="perform-packet-capture-on-the-azure-virtual-wan-site-to-site-vpn-gateway"></a>Execute a captura de pacotes no Gateway VPN local do Azure Virtual WAN 

A conectividade e os problemas relacionados com o desempenho são muitas vezes complexos. Pode levar tempo e esforço significativos apenas para reduzir a causa do problema. A captura de pacotes pode ajudá-lo a reduzir o âmbito de um problema a certas partes da rede. Pode ajudá-lo a determinar se o problema está do lado do cliente da rede, do lado Azure da rede, ou algures no meio. Depois de reduzir o problema, é mais eficiente depurar e tomar medidas corretivas.

O seguinte artigo descreve como iniciar e impedir a captura de um pacote no Azure Virtual WAN Site-to-site VPN Gateway. Atualmente, esta funcionalidade apenas se encontra disponível através do PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar os passos deste artigo, tem de ter a seguinte configuração já configurada no seu ambiente:

* Um WAN virtual, um hub virtual e um Gateway VPN site-to-site implantado no Centro Virtual. Também pode ter ligações que liguem sites VPN ao seu Portal VPN Site-to-site.


### <a name="working-with-azure-powershell"></a>Trabalhar com a Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="set-up-the-environment"></a>Configurar o ambiente

Certifique-se de que está no contexto de subscrição certo executando o seguinte script no PowerShell. Isto garante que está registado num utilizador que tem permissões para realizar a captura de pacotes no Portal VPN Site-to-site.

   ```azurepowershell-interactive
    $subid = “<insert Virtual WAN subscription ID here>”
    Set-AzContext -SubscriptionId $subid
   ```

## <a name="create-a-storage-account"></a><a name="createstorage"></a> Criar uma conta de armazenamento

Tem de criar uma conta de armazenamento sob a sua assinatura Azure para armazenar os resultados da captura do seu pacote. Por favor, consulte este [documento](.././storage/common/storage-account-create.md) para obter instruções sobre como criar uma conta de armazenamento.

Depois de criar a sua conta, execute os seguintes comandos para gerar um URL de assinatura de acesso partilhado (SAS). Os resultados da captura do seu pacote serão armazenados através deste URL.
   ```azurepowershell-interactive
  $rgname = “<resource group name containing storage account>” 
$storeName = “<name of storage account> “
$containerName = “<name of container you want to store packet capture in>
$key = Get-AzStorageAccountKey -ResourceGroupName $rgname -Name $storeNAme
$context = New-AzStorageContext -StorageAccountName  $storeName -StorageAccountKey $key[0].value
New-AzStorageContainer -Name $containerName -Context $context
$container = Get-AzStorageContainer -Name $containerName  -Context $context
$now = get-date
$sasurl = New-AzureStorageContainerSASToken -Name $containerName -Context $context -Permission "rwd" -StartTime $now.AddHours(-1) -ExpiryTime $now.AddDays(1) -FullUri
   ```

## <a name="start-the-packet-capture"></a>Inicie a captura de pacotes

Para iniciar a captura do pacote, tem duas opções, capturando pacotes numa única ligação VPN ou em todo o Portal VPN local. Note que se optar por realizar capturas em ligações VPN, só poderá efetuar capturas em 6 ligações ao mesmo tempo.

### <a name="packet-capture-on-a-site-to-site-vpn-gateway-all-connections"></a>Captura de pacotes num Gateway VPN local (todas as ligações)

Por favor, verifique os seguintes comandos. O nome do Portal VPN local pode ser encontrado navegando no seu Hub Virtual, clicando em VPN (Site-to-site) em conectividade.

:::image type="content" source="./media/virtual-wan-pcap-screenshots/vpn-gateway-name.png" alt-text="Imagem do nome de gateway VIRTUAL WAN." lightbox="./media/virtual-wan-pcap-screenshots/vpn-gateway-name.png":::

   ```azurepowershell-interactive
Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>" -Sasurl $sasurl
   ```

### <a name="packet-capture-on-specific-site-to-site-vpn-connections"></a>Captura de pacotes em ligações VPN site-to-site específicas

>[!NOTE]
> Por favor, note que só pode executar uma captura de pacote em 5 ligações VPN simultaneamente.


Por favor, verifique os seguintes comandos. O Nome das Ligações VPN site-to-site pode ser encontrado navegando no seu Hub Virtual, clicando em VPN (Site-to-site) em conectividade. Em seguida, navegue no Site VPN onde pretende realizar a captura do pacote e clique nos três pontos à direita. Clique em **Editar Ligação VPN** no menu que aparece.

:::image type="content" source="./media/virtual-wan-pcap-screenshots/sample-connection.png" alt-text="Imagem de como encontrar nomes de ligação VPN." lightbox="./media/virtual-wan-pcap-screenshots/sample-connection.png":::

O nome dos links ligados a um site específico da VPN pode ser encontrado clicando no site VPN a partir da secção anterior e olhando para a tabela **Links.** 

:::image type="content" source="./media/virtual-wan-pcap-screenshots/link-name-sample.png" alt-text="Imagem de como encontrar o nome de link VPN." lightbox="./media/virtual-wan-pcap-screenshots/link-name-sample.png":::

   ```azurepowershell-interactive
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links eg. "link1,link2">” -Sasurl $sasurl 
   ```

## <a name="optional-specifying-filters"></a>Opcional: Especificar filtros

Existem algumas ferramentas de captura de pacotes geralmente disponíveis. Obter capturas de pacotes relevantes com estas ferramentas pode ser complicado, especialmente em cenários de tráfego de grande volume. Para simplificar as capturas do seu pacote, pode especificar filtros na captura do seu pacote para se concentrar em comportamentos específicos. Abaixo estão os parâmetros disponíveis:

>[!NOTE]
> Para TracingFlags e TCPFlags, pode especificar vários protocolos adicionando os valores numéricos para os protocolos que pretende capturar (o mesmo que um OR lógico). Por exemplo, se quisesse capturar apenas pacotes ESP e OPVN, especificaria um valor TracingFlag de 8+1 = 9.  

| Parâmetro | Descrição | Valores predefinidos | Valores disponíveis|
|--- |--- | --- | ---|
| TracingFlags | Inteiro que determina que tipos de pacotes são capturados | 11 (ESP, IKE, OVPN) | ESP = 1 IKE = 2 OPVN = 8 |
| TCPFlags | Inteiro que determina quais tipos de pacotes TCP são capturados | 0 (nenhum) | FIN = 1, SYN = 2, RST = 4, PSH = 8, ACK = 16,URG = 32, ECE = 64, CWR = 128| 
| MaxPacketBuffersize|Tamanho máximo de um pacote capturado em bytes. Os pacotes são truncados se forem maiores do que o valor fornecido. |120|Qualquer|
| MaxFileSize |Tamanho máximo do ficheiro de captura em Mb. As capturas são armazenadas num tampão circular para que o transbordo seja manuseado de forma FIFO (pacotes mais antigos removidos primeiro)|100|Qualquer|
|FonteSubnets|Os pacotes das gamas CIDR especificadas são capturados. Especificado como uma matriz.|[] (todos os endereços IPv4)|Matriz de sub-redes IPV4 separadas por vírgula|
| DestinationSubnets |Os pacotes destinados às gamas CIDR especificadas são capturados. Especificado como uma matriz. |[] (todos os endereços IPv4)|Matriz de sub-redes IPV4 separadas por vírgula|
|FontePort |Os pacotes com origem nas gamas especificadas são capturados. Especificado como uma matriz.|[] (todas as portas)|Matriz de portas separadas por vírgulas|
|DestinationPort |Os pacotes com destino nas gamas especificadas são capturados. Especificado como uma matriz.|[] (todas as portas)|Matriz de portas separadas por vírgulas|
| CapturaSingleDirectionTrafficOnly |Se for verdade, apenas uma direção de um fluxo bidirecional aparecerá na captura do pacote. Isto irá capturar todas as combinações possíveis de IP e portas.|Verdadeiro|Verdade, Falso|
|Protocolo|Uma série de inteiros que correspondem aos protocolos da IANA. |[] (todos os protocolos)| Quaisquer protocolos encontrados [aqui](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml) |

Abaixo está uma captura de pacote de exemplo usando uma corda de filtro. Pode alterar os parâmetros de acordo com as suas necessidades com base na tabela anterior.  

   ```azurepowershell-interactive
$filter="{`"TracingFlags`":11,`"MaxPacketBufferSize`":120,`"MaxFileSize`":500,`"Filters`":[{`"SourceSubnets`":[`"10.19.0.4/32`",`"10.20.0.4/32`"],`"DestinationSubnets`":[`"10.20.0.4/32`",`"10.19.0.4/32`"],`"TcpFlags`":9,`"CaptureSingleDirectionTrafficOnly`":true}]}"
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links>” -Sasurl $sasurl -FilterData $filter

Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>" -Sasurl $sasurl -FilterData $filter
   ```

## <a name="stopping-the-packet-capture"></a>Parar a captura do pacote
Recomenda-se que deixe a captura do pacote funcionar durante pelo menos 600 segundos. Devido a problemas de sincronização entre vários componentes no caminho, capturas de pacotes mais curtos podem não fornecer dados completos. Assim que estiver pronto para parar a captura do pacote, executar o seguinte comando.

Os parâmetros são semelhantes aos da secção iniciar uma secção de captura de pacotes. O URL SAS foi gerado na secção [Criar uma Conta de Armazenamento.](#createstorage)

### <a name="gateway-level-packet-capture"></a>Captura de pacotes de nível gateway

   ```azurepowershell-interactive
Stop-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name <GatewayName> -SasUrl $sas
   ```

### <a name="connection-level-packet-captures"></a>Capturas de pacotes de nível de ligação

   ```azurepowershell-interactive
Stop-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name <name of the VPN connection> -ParentResourceName "<name of VPN Gateway>" -LinkConnectionName <comma separated list of links e.g. "link1,link2">-SasUrl $sas
   ```

## <a name="viewing-your-packet-capture"></a>Visualizando a captura do seu pacote

No portal Azure, navegue na Conta de Armazenamento criada em "Criar uma Conta de Armazenamento" Clique no seu contentor e descarregue o ficheiro. Os ficheiros de dados de captura de pacotes são gerados no formato PCAP. Pode utilizar o Wireshark ou outra aplicação habitualmente disponível para abrir ficheiros PCAP.

## <a name="next-steps"></a>Passos seguintes

Em seguida, para saber mais sobre O WAN Virtual, consulte:

> [!div class="nextstepaction"]
> * [FAQ da WAN Virtual](virtual-wan-faq.md)