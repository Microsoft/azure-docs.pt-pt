---
title: Hospedar zonas de pesquisa de DNS inversa no DNS do Azure | Microsoft Docs
description: Saiba como usar o DNS do Azure para hospedar as zonas de pesquisa inversa de DNS para seus intervalos de IP
services: dns
documentationcenter: na
author: asudbring
manager: jeconnoc
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: allensu
ms.openlocfilehash: aeefb523b401effe4dedb21a8284f296948bbef9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74211253"
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>Hospedar zonas de pesquisa de DNS inversa no DNS do Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este artigo explica como hospedar as zonas de pesquisa inversa de DNS para seus intervalos de IP atribuídos no DNS do Azure. Os intervalos de IP representados pelas zonas de pesquisa inversa devem ser atribuídos à sua organização, normalmente pelo seu ISP.

Para configurar o DNS reverso para um endereço IP de Propriedade do Azure atribuído ao serviço do Azure, consulte [Configurar o DNS reverso para serviços hospedados no Azure](dns-reverse-dns-for-azure-services.md).

Antes de ler este artigo, você deve estar familiarizado com a [visão geral do DNS reverso e do suporte no Azure](dns-reverse-dns-overview.md).

Este artigo orienta você pelas etapas para criar sua primeira zona DNS de pesquisa inversa e registro usando o portal do Azure, Azure PowerShell, CLI clássica do Azure ou CLI do Azure.

## <a name="create-a-reverse-lookup-dns-zone"></a>Criar uma zona DNS de pesquisa inversa

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. No menu **Hub** , selecione **novo** > **rede**e, em seguida, selecione **zona DNS**.

   ![Seleção de "zona DNS"](./media/dns-reverse-dns-hosting/figure1.png)

1. No painel **criar zona DNS** , nomeie a zona DNS. O nome da zona é criado de forma diferente para prefixos IPv4 e IPv6. Use as instruções para [IPv4](#ipv4) ou [IPv6](#ipv6) para nomear sua zona. Quando tiver terminado, selecione **criar** para criar a zona.

### <a name="ipv4"></a>IPv4

O nome de uma zona de pesquisa inversa de IPv4 é baseado no intervalo de IP que ele representa. Ele deve estar no seguinte formato: `<IPv4 network prefix in reverse order>.in-addr.arpa`. Para obter exemplos, consulte [visão geral de DNS reverso e suporte no Azure](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> Quando você estiver criando zonas de pesquisa de DNS reverso sem classe no DNS do Azure, deverá usar um hífen (`-`) em vez de uma barra (`/`) no nome da zona.
>
> Por exemplo, para o intervalo de IP 192.0.2.128/26, você deve usar `128-26.2.0.192.in-addr.arpa` como o nome da zona em vez de `128/26.2.0.192.in-addr.arpa`.
>
> Embora os padrões de DNS ofereçam suporte a ambos os métodos, o DNS do Azure não dá suporte a nomes de zona DNS que contêm para o caractere de barra (`/`).

O exemplo a seguir mostra como criar uma zona DNS reversa de classe C chamada `2.0.192.in-addr.arpa` no DNS do Azure por meio do portal do Azure:

 ![Painel "criar zona DNS", com caixas preenchidas](./media/dns-reverse-dns-hosting/figure2.png)

O **local do grupo de recursos** define o local do grupo de recursos. Ele não tem impacto sobre a zona DNS. A localização da zona DNS é sempre “global” e não está apresentada.

Os exemplos a seguir mostram como concluir essa tarefa usando Azure PowerShell e CLI do Azure.

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

O nome de uma zona de pesquisa inversa IPv6 deve estar no seguinte formato: `<IPv6 network prefix in reverse order>.ip6.arpa`.  Para obter exemplos, consulte [visão geral de DNS reverso e suporte no Azure](dns-reverse-dns-overview.md#ipv6).


O exemplo a seguir mostra como criar uma zona de pesquisa de DNS reverso IPv6 chamada `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` no DNS do Azure por meio do portal do Azure:

 ![Painel "criar zona DNS", com caixas preenchidas](./media/dns-reverse-dns-hosting/figure3.png)

O **local do grupo de recursos** define o local do grupo de recursos. Ele não tem impacto sobre a zona DNS. A localização da zona DNS é sempre “global” e não está apresentada.

Os exemplos a seguir mostram como concluir essa tarefa usando Azure PowerShell e CLI do Azure.

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

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Delegar uma zona de pesquisa inversa de DNS

Agora que você criou sua zona de pesquisa inversa de DNS, deve garantir que a zona seja delegada a partir da zona pai. A delegação de DNS permite que o processo de resolução de nomes DNS Localize os servidores de nomes que hospedam sua zona de pesquisa inversa de DNS. Esses servidores de nomes podem, então, responder a consultas inversas de DNS para os endereços IP em seu intervalo de endereços.

Para zonas de pesquisa direta, o processo de delegação de uma zona DNS é descrito em [delegar seu domínio para o DNS do Azure](dns-delegate-domain-azure-dns.md). A delegação de zonas de pesquisa inversa funciona da mesma maneira. A única diferença é que você precisa configurar os servidores de nomes com o ISP que forneceu seu intervalo de IP, em vez de seu registrador de nome de domínio.

## <a name="create-a-dns-ptr-record"></a>Criar um registro PTR de DNS

### <a name="ipv4"></a>IPv4

O exemplo a seguir orienta o processo de criação de um registro PTR em uma zona DNS reverso no DNS do Azure. Para outros tipos de registo e para modificar registos existentes, consulte [Manage DNS records and record sets by using the Azure portal (Gerir registos DNS e conjuntos de registos com o portal do Azure)](dns-operations-recordsets-portal.md).

1. Na parte superior do painel **zona DNS** , selecione **+ conjunto de registros** para abrir o painel **Adicionar conjunto de registros** .

   ![Botão para criar um conjunto de registros](./media/dns-reverse-dns-hosting/figure4.png)

1. O nome do conjunto de registros para um registro PTR precisa ser o restante do endereço IPv4 na ordem inversa. 

   Neste exemplo, os três primeiros octetos já estão populados como parte do nome da zona (. 2.0.192). Portanto, apenas o último octeto é fornecido na caixa **nome** . Por exemplo, você pode nomear seu conjunto de registros **15** para um recurso cujo endereço IP é 192.0.2.15.  
1. Para **tipo**, selecione **PTR**.  
1. Para **nome de domínio**, insira o nome de domínio totalmente qualificado (FQDN) do recurso que usa o IP.
1. Selecione **OK** na parte inferior do painel para criar o registro DNS.

   ![Painel "Adicionar conjunto de registros", com caixas preenchidas](./media/dns-reverse-dns-hosting/figure5.png)

Os exemplos a seguir mostram como concluir essa tarefa usando o PowerShell ou CLI do Azure.

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

O exemplo a seguir orienta o processo de criação de um novo registro PTR. Para outros tipos de registo e para modificar registos existentes, consulte [Manage DNS records and record sets by using the Azure portal (Gerir registos DNS e conjuntos de registos com o portal do Azure)](dns-operations-recordsets-portal.md).

1. Na parte superior do painel **zona DNS** , selecione **+ conjunto de registros** para abrir o painel **Adicionar conjunto de registros** .

   ![Botão para criar um conjunto de registros](./media/dns-reverse-dns-hosting/figure6.png)

2. O nome do conjunto de registros para um registro PTR precisa ser o restante do endereço IPv6 na ordem inversa. Ele não deve incluir nenhuma compactação zero. 

   Neste exemplo, os primeiros 64 bits do IPv6 já estão populados como parte do nome da zona (0.0.0.0. c. d. b. a. 8. b. d. 0.1.0.0.2. ip6. arpa). Portanto, somente os últimos 64 bits são fornecidos na caixa **nome** . Os últimos 64 bits do endereço IP são inseridos na ordem inversa, com um ponto como o delimitador entre cada número hexadecimal. Por exemplo, você pode nomear seu conjunto de registros **e. f.a. f. a. 1. c. b. c.b. 0.1.4.2.5. f** para um recurso cujo endereço IP é 2001:0DB8: ABDC: 0000: f524:10BC: 1af9:405e.  
3. Para **tipo**, selecione **PTR**.  
4. Para **nome de domínio**, insira o FQDN do recurso que usa o IP.
5. Selecione **OK** na parte inferior do painel para criar o registro DNS.

![Painel "Adicionar conjunto de registros", com caixas preenchidas](./media/dns-reverse-dns-hosting/figure7.png)

Os exemplos a seguir mostram como concluir essa tarefa usando o PowerShell ou CLI do Azure.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azure-classic-cli"></a>CLI clássica do Azure

```
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azure-cli"></a>CLI do Azure

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>Ver registos

Para exibir os registros que você criou, navegue até a zona DNS na portal do Azure. Na parte inferior do painel **zona DNS** , você pode ver os registros da zona DNS. Você deve ver os registros padrão NS e SOA, além de todos os novos registros que você criou. Os registros NS e SOA são criados em cada zona. 

### <a name="ipv4"></a>IPv4

O painel **zona DNS** mostra os registros PTR de IPv4:

![Painel "zona DNS" com registros IPv4](./media/dns-reverse-dns-hosting/figure8.png)

Os exemplos a seguir mostram como exibir os registros PTR usando o PowerShell ou CLI do Azure.

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
    azure network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

O painel **zona DNS** mostra os registros PTR do IPv6:

![Painel "zona DNS" com registros IPv6](./media/dns-reverse-dns-hosting/figure9.png)

Os exemplos a seguir mostram como exibir os registros usando o PowerShell ou CLI do Azure.

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
    azure network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>FAQ

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Posso hospedar zonas de pesquisa inversa de DNS para meus blocos de IP atribuídos ao ISP no DNS do Azure?

Sim. A hospedagem de zonas de pesquisa inversa (ARPA) para seus próprios intervalos de IP no DNS do Azure é totalmente suportada.

Crie a zona de pesquisa inversa no DNS do Azure, conforme explicado neste artigo e trabalhe com seu ISP para [delegar a zona](dns-domain-delegation.md). Em seguida, você pode gerenciar os registros PTR para cada pesquisa inversa da mesma maneira que outros tipos de registro.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>Quanto custa a minha zona de pesquisa inversa de DNS?

Hospedar a zona de pesquisa inversa de DNS para o bloco de IP atribuído pelo ISP no DNS do Azure é cobrado com [taxas de DNS padrão do Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Posso hospedar zonas de pesquisa inversa de DNS para endereços IPv4 e IPv6 no DNS do Azure?

Sim. Este artigo explica como criar zonas de pesquisa de DNS reverso IPv4 e IPv6 no DNS do Azure.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Posso importar uma zona de pesquisa inversa de DNS existente?

Sim. Você pode usar CLI do Azure para importar as zonas DNS existentes para o DNS do Azure. Esse método funciona para zonas de pesquisa direta e zonas de pesquisa inversa.

Para obter mais informações, consulte [importar e exportar um arquivo de zona DNS usando CLI do Azure](dns-import-export.md).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o DNS reverso, consulte [pesquisa reversa de DNS na Wikipédia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Saiba como [gerenciar registros DNS reversos para seus serviços do Azure](dns-reverse-dns-for-azure-services.md).
