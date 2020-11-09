---
title: Definições de DNS de Firewall Azure Firewall
description: Pode configurar o Azure Firewall com as definições de servidor DNS e procuração de DNS.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/06/2020
ms.author: victorh
ms.openlocfilehash: ad0ac040b510783656617ddbf2063cd94c80aae7
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380951"
---
# <a name="azure-firewall-dns-settings"></a>Definições de DNS de Firewall Azure Firewall

Pode configurar um servidor DNS personalizado e ativar o proxy DNS para Azure Firewall. Pode configurar estas definições quando implementar a firewall ou mais tarde a partir da página de **definições DE DNS.**

## <a name="dns-servers"></a>Servidores DNS

Um servidor DNS mantém e resolve nomes de domínio para endereços IP. Por padrão, o Azure Firewall utiliza o Azure DNS para resolução de nomes. A definição **do servidor DNS** permite configurar os seus próprios servidores DNS para resolução de nomes Azure Firewall. Pode configurar um único ou vários servidores.

> [!NOTE]
> Para a Azure Firewalls geridas utilizando o Azure Firewall Manager, as definições de DNS estão configuradas na política de firewall do Azure associado.

### <a name="configure-custom-dns-servers---azure-portal"></a>Configurar servidores DNS personalizados - Portal Azure

1. Nas **Definições** de Firewall Azure , selecione **Definições DNS**.
2. Nos **servidores DNS,** pode escrever ou adicionar servidores DNS existentes que tenham sido previamente especificados na sua Rede Virtual.
3. Selecione **Guardar**.
4. A firewall agora direciona o tráfego DNS para o(s) servidor(s) especificado para resolução de nomes.

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="Servidores DNS":::

### <a name="configure-custom-dns-servers---azure-cli"></a>Configurar servidores DNS personalizados - Azure CLI

O exemplo a seguir atualiza o Azure Firewall com servidores DNS personalizados utilizando o Azure CLI.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --dns-servers 10.1.0.4 10.1.0.5
```

> [!IMPORTANT]
> O comando `az network firewall` requer que seja instalada a extensão Azure CLI. `azure-firewall` Pode ser instalado com o `az extension add --name azure-firewall` comando. 

### <a name="configure-custom-dns-servers---azure-powershell"></a>Configurar servidores DNS personalizados - Azure PowerShell

O exemplo a seguir atualiza o Azure Firewall com servidores DNS personalizados utilizando a Azure PowerShell.

```azurepowershell
$dnsServers = @("10.1.0.4", "10.1.0.5")
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSServer = $dnsServers

$azFw | Set-AzFirewall
```

## <a name="dns-proxy"></a>Proxy DNS

Pode configurar a Azure Firewall para agir como um representante do DNS. Um proxy DNS atua como um intermediário para pedidos dns de máquinas virtuais de clientes para um servidor DNS. Se configurar um servidor DNS personalizado, deve ativar o proxy DNS para evitar a incompatibilidade de resolução de DNS e ativar a filtragem FQDN nas regras de rede.

Se não ativar o proxy DNS, os pedidos de DNS do cliente podem viajar para um servidor DNS num momento diferente ou devolver uma resposta diferente em comparação com a da firewall. O proxy DONS coloca o Azure Firewall no caminho dos pedidos do cliente para evitar inconsistências.

Existem dois tipos de função de caching que acontecem quando o Azure Firewall é um proxy DNS:

- Cache positivo – Resolução DNS é bem sucedida. A firewall utiliza o TTL (tempo de vida) do pacote ou objeto. 

- Cache negativo – Resolução DNS resulta em nenhuma resposta ou nenhuma resolução. A firewall caches esta informação por uma hora.

O proxy DNS armazena todos os endereços IP resolvidos a partir de FQDNs nas regras da rede. Como uma boa prática, use FQDNs que resolvem um endereço IP.  

### <a name="dns-proxy-configuration"></a>Configuração de procuração de DNS

A configuração do DNS Proxy requer três passos:
1. Ativar o proxy DNS nas definições de DNS da Azure Firewall.
2. Configurar opcionalmente o seu servidor DNS personalizado ou utilizar o padrão fornecido.
3. Por fim, tem de configurar o endereço IP privado do Azure Firewall como um endereço DNS personalizado nas definições do servidor DNS da sua rede virtual. Isto garante que o tráfego DNS é direcionado para a Firewall Azure.

#### <a name="configure-dns-proxy---azure-portal"></a>Configure DNS proxy - Portal Azure

Para configurar o proxy DNS, tem de configurar a definição de servidores DNS de rede virtual para utilizar o endereço IP privado de firewall. Em seguida, ative as definições DNS Proxy em Azure Firewall **DNS**.

##### <a name="configure-virtual-network-dns-servers"></a>Configurar servidores DNS de rede virtual 

1. Selecione a rede virtual onde o tráfego DNS será encaminhado através do Azure Firewall.
2. Em **Definições** , selecione **servidores DNS**.
3. Selecione **Custom** em **servidores DNS**.
4. Insira o endereço IP privado da firewall.
5. Selecione **Guardar**.
6. Reinicie os VMs que estão ligados à rede virtual, para que lhes sejam atribuídas as novas definições do servidor DNS. Os VMs continuam a utilizar as definições de DNS atuais até serem reiniciadas.

##### <a name="enable-dns-proxy"></a>Ativar o proxy DNS

1. Selecione o seu Azure Firewall.
2. Em **Definições** , selecione **as definições de DNS**.
3. Por predefinição, **o DNS Proxy** está desativado. Quando ativado, a firewall ouve na porta 53 e encaminha os pedidos dns para os servidores DNS configurados.
4. Reveja a configuração dos **servidores DNS** para se certificar de que as definições são adequadas para o seu ambiente.
5. Selecione **Guardar**.

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="Proxy DNS":::

#### <a name="configure-dns-proxy---azure-cli"></a>Configure DNS proxy - Azure CLI

Pode utilizar o Azure CLI para configurar as definições de procuração de DNS no Azure Firewall e atualizar redes virtuais para utilizar o Azure Firewall como servidor DNS.

##### <a name="configure-virtual-network-dns-servers"></a>Configurar servidores DNS de rede virtual

Este exemplo configura o VNet para utilizar o Azure Firewall como servidor DNS.
 
```azurecli-interactive
az network vnet update \
    --name VNetName \ 
    --resource-group VNetRG \
    --dns-servers <firewall-private-IP>
```

##### <a name="enable-dns-proxy"></a>Ativar o proxy DNS

Este exemplo permite a funcionalidade de procuração DNS no Azure Firewall.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --enable-dns-proxy true
```

#### <a name="configure-dns-proxy---azure-powershell"></a>Configure DNS proxy - Azure PowerShell

Pode utilizar o Azure PowerShell para configurar as definições de procuração de DNS no Azure Firewall e atualizar redes virtuais para utilizar o Azure Firewall como servidor DNS.

##### <a name="configure-virtual-network-dns-servers"></a>Configurar servidores DNS de rede virtual

 Este exemplo configura o VNet para utilizar o Azure Firewall como servidor DNS.

```azurepowershell
$dnsServers = @("<firewall-private-IP>")
$VNet = Get-AzVirtualNetwork -Name "VNetName" -ResourceGroupName "VNetRG"
$VNet.DhcpOptions.DnsServers = $dnsServers

$VNet | Set-AzVirtualNetwork
```

##### <a name="enable-dns-proxy"></a>Ativar o proxy DNS

Este exemplo permite a funcionalidade de procuração DNS no Azure Firewall.

```azurepowershell
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSEnableProxy = $true

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Próximos passos

[Filtragem FQDN nas regras da rede](fqdn-filtering-network-rules.md)
