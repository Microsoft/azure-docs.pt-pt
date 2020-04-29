---
title: Hospedeira inverte zonas de procura dNS em DNS Azure
description: Saiba como usar o Azure DNS para acolher as zonas de procura de DNS inversas para as suas gamas IP
author: rohinkoul
ms.service: dns
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 78fc3428274be5e1998abe9189bea996f15e278c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79454266"
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>Hospedeira inverte zonas de procura dNS em DNS Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este artigo explica como hospedar as zonas de procura dNS inversas para as suas gamas IP atribuídas em Azure DNS. As gamas IP representadas pelas zonas de procura inversadevem devem ser atribuídas à sua organização, tipicamente pelo seu ISP.

Para configurar o DNS invertido para um endereço IP propriedade do Azure que é atribuído ao seu serviço Azure, consulte [Configure reverter DNS para serviços hospedados em Azure](dns-reverse-dns-for-azure-services.md).

Antes de ler este artigo, deve estar familiarizado com a [visão geral do DNS invertido e](dns-reverse-dns-overview.md)do apoio em Azure .

Este artigo percorre-o através dos passos para criar a sua primeira zona dNS de procura inversa e gravar utilizando o portal Azure, Azure PowerShell, Azure classic CLI ou Azure CLI.

## <a name="create-a-reverse-lookup-dns-zone"></a>Criar uma zona dNS de procura inversa

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No menu **Hub,** selecione **New** > **Networking**, e, em seguida, selecione **a zona DNS**.

   ![Seleção "zona DNS"](./media/dns-reverse-dns-hosting/figure1.png)

1. No painel de **zona Create DNS,** nomeie a sua zona DNS. O nome da zona é elaborado de forma diferente para os prefixos IPv4 e IPv6. Utilize as instruções para [iPv4](#ipv4) ou [IPv6](#ipv6) para nomear a sua zona. Quando terminar, selecione **Criar** para criar a zona.

### <a name="ipv4"></a>IPv4

O nome de uma zona de procura inversa IPv4 baseia-se na gama IP que representa. Deve estar no seguinte `<IPv4 network prefix in reverse order>.in-addr.arpa`formato: . Por exemplo, consulte [a visão geral do DNS invertido e suporte em Azure](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> Quando estiver a criar zonas de procura dNS inversas sem classe em`-`DNS Azure,`/`deve utilizar um hífen ( ) em vez de um corte para a frente ( ) no nome da zona.
>
> Por exemplo, para a gama IP 192.0.2.2.128/26, deve utilizar `128-26.2.0.192.in-addr.arpa` como nome de zona em vez de `128/26.2.0.192.in-addr.arpa`.
>
> Embora as normas DNS suportem ambos os métodos, o Azure DNS não suporta nomes de zona DNS que contenham para o caráter de barra avançada (`/`).

O exemplo que se segue mostra como criar `2.0.192.in-addr.arpa` uma zona DNS inversa classe C nomeada em DNS Azure através do portal Azure:

 ![Painel "Create DNS zone", com caixas preenchidas](./media/dns-reverse-dns-hosting/figure2.png)

**A localização** do grupo de recursos define a localização para o grupo de recursos. Não tem impacto na zona DNS. A localização da zona DNS é sempre “global” e não está apresentada.

Os seguintes exemplos mostram como completar esta tarefa utilizando o Azure PowerShell e o Azure CLI.

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

O nome de uma zona de procura inversa IPv6 deve estar na seguinte forma: `<IPv6 network prefix in reverse order>.ip6.arpa`.  Por exemplo, consulte [a visão geral do DNS invertido e suporte em Azure](dns-reverse-dns-overview.md#ipv6).


O exemplo que se segue mostra como criar uma `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` zona de procuração DNS inversa IPv6 chamada em Azure DNS através do portal Azure:

 ![Painel "Create DNS zone", com caixas preenchidas](./media/dns-reverse-dns-hosting/figure3.png)

**A localização** do grupo de recursos define a localização para o grupo de recursos. Não tem impacto na zona DNS. A localização da zona DNS é sempre “global” e não está apresentada.

Os seguintes exemplos mostram como completar esta tarefa utilizando o Azure PowerShell e o Azure CLI.

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

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Delege uma zona de procuração dNS inversa

Agora que criou a sua zona de procura ção DNS inversa, tem de garantir que a zona é delegada a partir da zona dos pais. A delegação do DNS permite que o processo de resolução de nomes DNS encontre os servidores de nome que acolhem a sua zona de procura dNS inversa. Esses servidores de nome podem então responder a consultas inversas dNS para os endereços IP no seu intervalo de endereços.

Para zonas de procura ção para a frente, o processo de delegação de uma zona DNS é descrito em [Delegar o seu domínio para O DNS Azure](dns-delegate-domain-azure-dns.md). A delegação para zonas de procura inversa funciona da mesma forma. A única diferença é que precisa de configurar os servidores de nome com o ISP que forneceu o seu intervalo IP, em vez do registo do seu nome de domínio.

## <a name="create-a-dns-ptr-record"></a>Criar um disco DNS PTR

### <a name="ipv4"></a>IPv4

O exemplo seguinte acompanha-o através do processo de criação de um registo PTR numa zona DNS inversa em DNS Azure. Para outros tipos de registo e para modificar registos existentes, consulte [Manage DNS records and record sets by using the Azure portal (Gerir registos DNS e conjuntos de registos com o portal do Azure)](dns-operations-recordsets-portal.md).

1. Na parte superior do painel de **zona DNS,** selecione **+ Conjunto de registo** para abrir o painel de conjunto de registo **saqueado.**

   ![Botão para criar um conjunto de discos](./media/dns-reverse-dns-hosting/figure4.png)

1. O nome do registo estabelecido para um disco PTR tem de ser o resto do endereço IPv4 por ordem inversa. 

   Neste exemplo, os três primeiros octetos já estão povoados como parte do nome da zona (.2.0.192). Portanto, apenas o último octeto é fornecido na caixa **nome.** Por exemplo, pode nomear o seu conjunto de **registos 15** para um recurso cujo endereço IP é 192.0.2.15.  
1. Para **Tipo,** selecione **PTR**.  
1. Para **NOME DE DOMÍNIO,** introduza o nome de domínio totalmente qualificado (FQDN) do recurso que utiliza o IP.
1. Selecione **OK** na parte inferior do painel para criar o registo DNS.

   ![Painel "Adicionar recorde", com caixas preenchidas](./media/dns-reverse-dns-hosting/figure5.png)

Os seguintes exemplos mostram como completar esta tarefa utilizando o PowerShell ou o Azure CLI.

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

O exemplo que se segue acompanha-o através do processo de criação de um novo disco PTR. Para outros tipos de registo e para modificar registos existentes, consulte [Manage DNS records and record sets by using the Azure portal (Gerir registos DNS e conjuntos de registos com o portal do Azure)](dns-operations-recordsets-portal.md).

1. Na parte superior do painel de **zona DNS,** selecione **+ Conjunto de registo** para abrir o painel de conjunto de registo **saqueado.**

   ![Botão para criar um conjunto de discos](./media/dns-reverse-dns-hosting/figure6.png)

2. O nome do registo estabelecido para um disco PTR tem de ser o resto do endereço IPv6 por ordem inversa. Não deve incluir qualquer compressão zero. 

   Neste exemplo, os primeiros 64 bits do IPv6 já estão povoados como parte do nome da zona (0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa). Portanto, apenas os últimos 64 bits são fornecidos na caixa **nome.** Os últimos 64 bits do endereço IP são introduzidos por ordem inversa, com um período como delimitador entre cada número hexadecimal. Por exemplo, pode nomear o seu recorde estabelecido **e.5.0.4.9.f.a.1.c.c.b.0.4.4.4.5.f** para um recurso cujo endereço IP é 2001:0db8:abdc:0000:f524:10bc:1af9:405e.  
3. Para **Tipo,** selecione **PTR**.  
4. Para **NOME DE DOMÍNIO,** introduza o FQDN do recurso que utiliza o IP.
5. Selecione **OK** na parte inferior do painel para criar o registo DNS.

![Painel "Adicionar recorde", com caixas preenchidas](./media/dns-reverse-dns-hosting/figure7.png)

Os seguintes exemplos mostram como completar esta tarefa utilizando o PowerShell ou o Azure CLI.

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

Para ver os registos que criou, navegue até à sua zona DNS no portal Azure. Na parte inferior do painel de **zona DNS,** pode ver os registos da zona DNS. Devias ver os registos padrão da NS e da SOA, mais quaisquer novos registos que tenhas criado. Os registos da NS e da SOA são criados em todas as zonas. 

### <a name="ipv4"></a>IPv4

O painel **de zona DNS** mostra os registos de PTR IPv4:

![Painel "DNS zone" com registos IPv4](./media/dns-reverse-dns-hosting/figure8.png)

Os exemplos seguintes mostram como visualizar os registos de PTR utilizando powerShell ou Azure CLI.

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

O painel **de zona DNS** mostra os registos de PTR IPv6:

![Painel "DNS zone" com registos IPv6](./media/dns-reverse-dns-hosting/figure9.png)

Os seguintes exemplos mostram como visualizar os registos utilizando powerShell ou Azure CLI.

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

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Posso alojar zonas de procura de DNS inversas para os meus blocos IP atribuídos ao ISP no Azure DNS?

Sim. O alojamento das zonas de procura inversa (ARPA) para as suas próprias gamas IP em Azure DNS está totalmente suportado.

Crie a zona de procura inversa no DNS Azure, como explicado neste artigo, e depois trabalhe com o seu ISP para [delegar a zona](dns-domain-delegation.md). Em seguida, pode gerir os registos de PTR para cada procura inversa da mesma forma que outros tipos de discos.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>Quanto custa a hospedagem da minha zona de procuração DNS inversa?

Hospedar a zona de procura ção DNS inversa para o seu bloco IP atribuído a ISP em Azure DNS é cobrado às [tarifas padrão de DNS Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Posso alojar zonas de procura de DNS inversas para endereços IPv4 e IPv6 em DNS Azure?

Sim. Este artigo explica como criar tanto as zonas de procura de DNS IPv4 como IPv6 em DNS Azure.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Posso importar uma zona de procura ção dNS inversa?

Sim. Pode utilizar o Azure CLI para importar zonas DNS existentes em DNS Azure. Este método funciona tanto para zonas de procura para a frente como para zonas de procura inversa.

Para mais informações, consulte [importar e exportar um ficheiro de zona DNS utilizando o Azure CLI](dns-import-export.md).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre dNS invertidos, consulte a [procura inversa de DNS na Wikipédia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Saiba como [gerir registos dNS invertidos para os seus serviços Azure](dns-reverse-dns-for-azure-services.md).
