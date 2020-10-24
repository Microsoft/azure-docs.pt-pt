---
title: Host zonas de pesquisa de DNS inversas em Azure DNS
description: Saiba como usar o Azure DNS para hospedar as zonas de pesquisa de DNS inversas para as suas gamas IP
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 6a0aebc727233cdd838f3e1bf8eeb5cd247b9836
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489681"
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>Host zonas de pesquisa de DNS inversas em Azure DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este artigo explica como hospedar as zonas de pesquisa de DNS inversas para as gamas IP atribuídas no Azure DNS. As gamas IP representadas pelas zonas de procura inversa devem ser atribuídas à sua organização, tipicamente pelo seu ISP.

Para configurar o DNS invertido para um endereço IP de propriedade Azure que seja atribuído ao seu serviço Azure, consulte [o Configure reverse DNS para serviços hospedados no Azure](dns-reverse-dns-for-azure-services.md).

Antes de ler este artigo, deve estar familiarizado com a [visão geral do DNS invertido e do suporte em Azure](dns-reverse-dns-overview.md).

Este artigo percorre os passos para criar a sua primeira zona de DNS de pesquisa inversa e gravar utilizando o portal Azure PowerShell, Azure Classic CLI ou Azure CLI.

## <a name="create-a-reverse-lookup-dns-zone"></a>Criar uma zona de DNS de pesquisa inversa

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No menu **Hub,** selecione **New**  >  **Networking**e, em seguida, selecione **a zona DNS**.

   ![Seleção "zona DNS"](./media/dns-reverse-dns-hosting/figure1.png)

1. No painel **de zona Create DNS,** diga o nome da sua zona DNS. O nome da zona é elaborado de forma diferente para prefixos IPv4 e IPv6. Utilize as instruções para [iPv4](#ipv4) ou [IPv6](#ipv6) para nomear a sua zona. Quando terminar, **selecione Criar** para criar a zona.

### <a name="ipv4"></a>IPv4

O nome de uma zona de procura inversa IPv4 baseia-se no intervalo IP que representa. Deve estar no seguinte formato: `<IPv4 network prefix in reverse order>.in-addr.arpa` . Por exemplo, consulte [a visão geral do DNS invertido e o suporte em Azure](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> Quando estiver a criar zonas de pesquisa de DNS invertidos sem classe em Azure DNS, deve utilizar um hífen ( `-` ) em vez de um corte para a frente ( ) no nome da `/` zona.
>
> Por exemplo, para a gama IP 192.0.2.128/26, deve utilizar `128-26.2.0.192.in-addr.arpa` como nome de zona em vez de `128/26.2.0.192.in-addr.arpa` .
>
> Embora os padrões DNS suportem ambos os métodos, O Azure DNS não suporta nomes de zonas DNS que contenham para o carácter de corte dianteiro `/` .

O exemplo a seguir mostra como criar uma zona DE DNS inversa classe C nomeada `2.0.192.in-addr.arpa` em Azure DNS através do portal Azure:

 ![Screenshot que mostra como criar uma zona DE DNS inversa classe C chamada 2.0.192.in-addr.arpa em Azure DNS através do portal Azure.](./media/dns-reverse-dns-hosting/figure2.png)

**A localização** do grupo de recursos define a localização para o grupo de recursos. Não tem impacto na zona do DNS. A localização da zona DNS é sempre “global” e não está apresentada.

Os exemplos a seguir mostram como completar esta tarefa utilizando a Azure PowerShell e a Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>CLI clássica do Azure

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>CLI do Azure

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

O nome de uma zona de procura inversa IPv6 deve ser da seguinte forma: `<IPv6 network prefix in reverse order>.ip6.arpa` .  Por exemplo, consulte [a visão geral do DNS invertido e o suporte em Azure](dns-reverse-dns-overview.md#ipv6).


O exemplo a seguir mostra como criar uma zona de pesquisa de DNS inversa IPv6 nomeada `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` em Azure DNS através do portal Azure:

 ![Painel "Criar zona DNS", com caixas preenchidas](./media/dns-reverse-dns-hosting/figure3.png)

**A localização** do grupo de recursos define a localização para o grupo de recursos. Não tem impacto na zona do DNS. A localização da zona DNS é sempre “global” e não está apresentada.

Os exemplos a seguir mostram como completar esta tarefa utilizando a Azure PowerShell e a Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>CLI clássica do Azure

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>CLI do Azure

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Delegar uma zona de pesquisa de DNS inversa

Agora que criou a sua zona de pesquisa de DNS inversa, deve garantir que a zona é delegada a partir da zona dos pais. A delegação do DNS permite que o processo de resolução do nome DNS encontre os servidores de nome que acolhem a sua zona de pesquisa de DNS inversa. Esses servidores de nomes podem então responder a consultas inversas dns para os endereços IP na sua gama de endereços.

Para zonas de procura a dianteira, o processo de delegação de uma zona de DNS é descrito em [Delegado do seu domínio para Azure DNS](dns-delegate-domain-azure-dns.md). A delegação para zonas de procura inversa funciona da mesma forma. A única diferença é que precisa configurar os servidores de nome com o ISP que forneceu o seu intervalo de IP, em vez do seu registo de nome de domínio.

## <a name="create-a-dns-ptr-record"></a>Criar um registo DNS PTR

### <a name="ipv4"></a>IPv4

O exemplo que se segue acompanha-o através do processo de criação de um registo PTR numa zona de DNS inversa em Azure DNS. Para outros tipos de registo e para modificar registos existentes, consulte [Manage DNS records and record sets by using the Azure portal (Gerir registos DNS e conjuntos de registos com o portal do Azure)](dns-operations-recordsets-portal.md).

1. No topo do painel de **zonas DNS,** selecione **+ Conjunto de record** para abrir o painel de **recordes Add.**

   ![Screenshot do painel de zona DNS com uma seta apontando para o botão conjunto + Record.](./media/dns-reverse-dns-hosting/figure4.png)

1. O nome do recorde estabelecido para um registo DEPT tem de ser o resto do endereço IPv4 em ordem inversa. 

   Neste exemplo, os três primeiros octetos já estão povoados como parte do nome da zona (.2.0.192). Portanto, apenas o último octeto é fornecido na caixa **Nome.** Por exemplo, pode nomear o seu recorde de **15** para um recurso cujo endereço IP é 192.0.2.15.  
1. Para **Tipo**, selecione **PTR**.  
1. Para **nome de domínio,** insira o nome de domínio totalmente qualificado (FQDN) do recurso que utiliza o IP.
1. Selecione **OK** na parte inferior do painel para criar o registo DNS.

   ![Painel "Adicionar conjunto de recordes", com caixas preenchidas](./media/dns-reverse-dns-hosting/figure5.png)

Os exemplos a seguir mostram como completar esta tarefa utilizando o PowerShell ou o Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azure-classic-cli"></a>CLI clássica do Azure

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azure-cli"></a>CLI do Azure

```azurecli
az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

O exemplo que se segue acompanha-o através do processo de criação de um novo registo PTR. Para outros tipos de registo e para modificar registos existentes, consulte [Manage DNS records and record sets by using the Azure portal (Gerir registos DNS e conjuntos de registos com o portal do Azure)](dns-operations-recordsets-portal.md).

1. No topo do painel de **zonas DNS,** selecione **+ Conjunto de record** para abrir o painel de **recordes Add.**

   ![Botão para criar um conjunto de registos](./media/dns-reverse-dns-hosting/figure6.png)

2. O nome do recorde estabelecido para um registo DEPT tem de ser o resto do endereço IPv6 em ordem inversa. Não deve incluir qualquer compressão zero. 

   Neste exemplo, os primeiros 64 bits do IPv6 já estão povoados como parte do nome da zona (0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa). Portanto, apenas os últimos 64 bits são fornecidos na caixa **Nome.** Os últimos 64 bits do endereço IP são introduzidos em ordem inversa, com um período como o declimiter entre cada número hexadecimal. Por exemplo, pode nomear o seu recorde estabelecido **e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f** para um recurso cujo endereço IP é 2001:0db8:abdc:0000:f524:10bc:1af9:405e.  
3. Para **Tipo**, selecione **PTR**.  
4. Para **NOME DE DOMÍNIO,** introduza o FQDN do recurso que utiliza o IP.
5. Selecione **OK** na parte inferior do painel para criar o registo DNS.

![Screenshot que mostra o painel "Adicionar recorde conjunto" com uma seta apontando para o valor no campo Tipo.](./media/dns-reverse-dns-hosting/figure7.png)

Os exemplos a seguir mostram como completar esta tarefa utilizando o PowerShell ou o Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azure-classic-cli"></a>CLI clássica do Azure

```azurecli
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azure-cli"></a>CLI do Azure

```azurecli
az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>Ver registos

Para ver os registos que criou, navegue pela sua zona DNS no portal Azure. Na parte inferior do painel de **zonas DNS,** pode ver os registos da zona de DNS. Deve ver os registos NS e SOA predefinidos, além de quaisquer novos registos que tenha criado. Os registos NS e SOA são criados em todas as zonas. 

### <a name="ipv4"></a>IPv4

O painel de **zonas DNS** mostra os registos do IPv4 PTR:

![Painel "zona DNS" com registos IPv4](./media/dns-reverse-dns-hosting/figure8.png)

Os exemplos a seguir mostram como visualizar os registos PTR utilizando o PowerShell ou o Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>CLI clássica do Azure

```azurecli
azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>CLI do Azure

```azurecli
az network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

O painel de **zonas DNS** mostra os registos do IPv6 PTR:

![Painel "zona DNS" com registos IPv6](./media/dns-reverse-dns-hosting/figure9.png)

Os exemplos a seguir mostram como visualizar os registos utilizando o PowerShell ou o Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>CLI clássica do Azure

```azurecli
azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>CLI do Azure

```azurecli
az network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>FAQ

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Posso apresentar zonas de pesquisa de DNS inversas para os meus blocos IP atribuídos pelo ISP no Azure DNS?

Sim. A hospedagem das zonas de procura inversa (ARPA) para as suas próprias gamas IP em Azure DNS está totalmente suportada.

Crie a zona de pesquisa inversa em Azure DNS, conforme explicado neste artigo, e, em seguida, trabalhe com o seu ISP para [delegar a zona](dns-domain-delegation.md). Em seguida, pode gerir os registos PTR para cada procura inversa da mesma forma que outros tipos de registo.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>Quanto custa a minha zona de pesquisa de DNS inversa?

A hospedagem da zona de pesquisa de DNS inversa para o seu bloco IP atribuído ao ISP em Azure DNS é cobrada nas [tarifas padrão de DNS do Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Posso hospedar zonas de pesquisa de DNS inversas para endereços IPv4 e IPv6 em DNS de Azure?

Sim. Este artigo explica como criar zonas de pesquisa de DNS iPv4 e IPv6 inversas em Azure DNS.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Posso importar uma zona de pesquisa de DNS inversa existente?

Sim. Você pode usar O Azure CLI para importar as zonas DNS existentes em Azure DNS. Este método funciona tanto para zonas de procura avançada como para zonas de procura inversa.

Para obter mais informações, consulte [Importar e exportar um ficheiro de zona DNS utilizando o Azure CLI](dns-import-export.md).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre DNS invertidos, consulte [a pesquisa reversa do DNS na Wikipédia.](https://en.wikipedia.org/wiki/Reverse_DNS_lookup)
<br>
Saiba como [gerir registos DNS invertidos para os seus serviços Azure.](dns-reverse-dns-for-azure-services.md)
