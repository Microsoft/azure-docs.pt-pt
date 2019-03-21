---
title: Alojar zonas de pesquisa DNS inversas no DNS do Azure | Documentos da Microsoft
description: Saiba como utilizar o DNS do Azure para alojar zonas de pesquisa DNS inversas para os intervalos de IP
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: victorh
ms.openlocfilehash: cb2f04c692d4b5f385a89ba6a3071c20ef1bdf21
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58118260"
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>Anfitrião zonas DNS inversas pesquisa no DNS do Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este artigo explica como alojar zonas de pesquisa DNS inversas para os intervalos IP atribuídos no DNS do Azure. Os intervalos IP representados por zonas de pesquisa inversa devem ser atribuídos à sua organização, normalmente pelo seu ISP.

Para configurar DNS inverso para um endereço IP pertencente ao Azure que é atribuído ao seu serviço do Azure, consulte [configurar DNS inverso para os serviços alojados no Azure](dns-reverse-dns-for-azure-services.md).

Antes de ler este artigo, deve estar familiarizado com o [descrição geral do DNS inverso e de suporte no Azure](dns-reverse-dns-overview.md).

Este artigo orienta-o pelos passos para criar sua primeira zona DNS de pesquisa inversa e registar com o portal do Azure, Azure PowerShell, CLI clássica do Azure ou da CLI do Azure.

## <a name="create-a-reverse-lookup-dns-zone"></a>Criar uma zona DNS de pesquisa inversa

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Sobre o **Hub** menu, selecione **New** > **redes**e, em seguida, selecione **zona DNS**.

   ![Seleção de "Zona DNS"](./media/dns-reverse-dns-hosting/figure1.png)

1. Na **criar zona DNS** painel, nomeie a sua zona DNS. O nome da zona é elaborado de forma diferente para os prefixos de IPv4 e IPv6. Utilize as instruções para [IPv4](#ipv4) ou [IPv6](#ipv6) nomear sua zona. Quando tiver terminado, selecione **criar** para criar a zona.

### <a name="ipv4"></a>IPv4

O nome de uma zona de pesquisa inversa IPv4 baseia-se o intervalo IP que ele representa. Deve estar no seguinte formato: `<IPv4 network prefix in reverse order>.in-addr.arpa`. Para obter exemplos, consulte [descrição geral do DNS inverso e de suporte no Azure](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> Quando estiver a criar classless zonas de pesquisa inversas de DNS no DNS do Azure, tem de utilizar um hífen (`-`) em vez de uma barra (`/`) no nome da zona.
>
> Por exemplo, para o 192.0.2.128/26 intervalo IP, tem de utilizar `128-26.2.0.192.in-addr.arpa` como o nome da zona em vez de `128/26.2.0.192.in-addr.arpa`.
>
> Embora as normas DNS suportam a ambos os métodos, o DNS do Azure não suporta nomes de zona DNS que contêm de barra (`/`) caracteres.

O exemplo seguinte mostra como criar uma zona DNS inversa de classe C com o nome `2.0.192.in-addr.arpa` no DNS do Azure através do portal do Azure:

 ![Painel "Criar zona DNS", com caixas de preenchidos](./media/dns-reverse-dns-hosting/figure2.png)

**Localização do grupo de recursos** define o local para o grupo de recursos. Ele não tem qualquer impacto na zona DNS. A localização da zona DNS é sempre “global” e não está apresentada.

Os exemplos seguintes mostram como concluir esta tarefa com o Azure PowerShell e CLI do Azure.

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

O nome de uma zona de pesquisa inversa IPv6 deve ter o seguinte formato: `<IPv6 network prefix in reverse order>.ip6.arpa`.  Para obter exemplos, consulte [descrição geral do DNS inverso e de suporte no Azure](dns-reverse-dns-overview.md#ipv6).


O exemplo seguinte mostra como criar uma IPv6 inversa DNS zona de pesquisa com o nome `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` no DNS do Azure através do portal do Azure:

 ![Painel "Criar zona DNS", com caixas de preenchidos](./media/dns-reverse-dns-hosting/figure3.png)

**Localização do grupo de recursos** define o local para o grupo de recursos. Ele não tem qualquer impacto na zona DNS. A localização da zona DNS é sempre “global” e não está apresentada.

Os exemplos seguintes mostram como concluir esta tarefa com o Azure PowerShell e CLI do Azure.

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

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Delegar uma zona de pesquisa DNS inversa

Agora que criou sua zona de pesquisa inversa DNS, certifique-se de que a zona é delegada de zona principal. Delegação de DNS permite que o processo de resolução de nome DNS localizar os servidores de nomes que alojam a zona de pesquisa DNS inversa. Esses servidores de nome, em seguida, podem responder a consultas DNS inversas para os endereços IP no intervalo de endereços.

Para zonas de pesquisa direta, o processo indesejado de delegar uma zona DNS é descrito em [delegar o domínio ao DNS do Azure](dns-delegate-domain-azure-dns.md). A delegação de zonas de pesquisa inversa funciona da mesma forma. A única diferença é que precisa configurar os servidores de nomes com o ISP que fornecia seu intervalo de IP, em vez de sua entidade de registo de nome de domínio.

## <a name="create-a-dns-ptr-record"></a>Criar um registo DNS PTR

### <a name="ipv4"></a>IPv4

O exemplo a seguir descreve o processo de criação de um registro PTR numa zona DNS inversa no DNS do Azure. Para outros tipos de registo e para modificar registos existentes, consulte [Manage DNS records and record sets by using the Azure portal (Gerir registos DNS e conjuntos de registos com o portal do Azure)](dns-operations-recordsets-portal.md).

1. Na parte superior a **zona DNS** painel, selecione **+ conjunto de registos** para abrir o **adicionar conjunto de registos** painel.

   ![Botão para a criação de um conjunto de registos](./media/dns-reverse-dns-hosting/figure4.png)

1. O nome do registo definido para um registo PTR tem de ser o restante do endereço IPv4 na ordem inversa. 

   Neste exemplo, os primeiros três octetos já são preenchidos como parte do nome de zona (.2.0.192). Por conseguinte, apenas o último octeto é fornecido no **nome** caixa. Por exemplo, pode nomear o conjunto de registos **15** para um recurso cujo endereço IP é 192.0.2.15.  
1. Para **tipo**, selecione **PTR**.  
1. Para **nome de domínio**, introduza o nome de domínio completamente qualificado (FQDN) do recurso que utiliza o IP.
1. Selecione **OK** na parte inferior do painel para criar o DNS de registos.

   ![Painel "Adicionar conjunto de registos", com caixas de preenchidos](./media/dns-reverse-dns-hosting/figure5.png)

Os exemplos seguintes mostram como concluir esta tarefa com o PowerShell ou da CLI do Azure.

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

O exemplo a seguir descreve o processo de criação de novo Registro PTR. Para outros tipos de registo e para modificar registos existentes, consulte [Manage DNS records and record sets by using the Azure portal (Gerir registos DNS e conjuntos de registos com o portal do Azure)](dns-operations-recordsets-portal.md).

1. Na parte superior a **zona DNS** painel, selecione **+ conjunto de registos** para abrir o **adicionar conjunto de registos** painel.

   ![Botão para a criação de um conjunto de registos](./media/dns-reverse-dns-hosting/figure6.png)

2. O nome do registo definido para um registo PTR tem de ser o restante do endereço IPv6 na ordem inversa. Não tem de incluir qualquer zero compressão. 

   Neste exemplo, as primeira 64 bits IPv6 já são preenchidos como parte do nome de zona (0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa). Por conseguinte, apenas os últimos 64 bits são fornecidos na **nome** caixa. Os últimos 64 bits do endereço IP são introduzidos na ordem inversa, com um período como delimitador entre cada número hexadecimal. Por exemplo, pode nomear o conjunto de registos **e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f** para um recurso cujo endereço IP é 2001:0db8:abdc:0000:f524:10bc:1af9:405e.  
3. Para **tipo**, selecione **PTR**.  
4. Para **nome de domínio**, introduza o FQDN do recurso que utiliza o IP.
5. Selecione **OK** na parte inferior do painel para criar o DNS de registos.

![Painel "Adicionar conjunto de registos", com caixas de preenchidos](./media/dns-reverse-dns-hosting/figure7.png)

Os exemplos seguintes mostram como concluir esta tarefa com o PowerShell ou da CLI do Azure.

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

Para ver os registos que criou, navegue para a zona DNS no portal do Azure. Na parte inferior do **zona DNS** painel, pode ver os registos para a zona DNS. Deverá ver a predefinição NS e registos SOA, além de quaisquer novos registos que criou. Os registos NS e SOA são criados em cada zona. 

### <a name="ipv4"></a>IPv4

O **zona DNS** painel mostra os registos IPv4 PTR:

![Painel "Zona DNS" com os registos de IPv4](./media/dns-reverse-dns-hosting/figure8.png)

Os exemplos seguintes mostram como ver os registos PTR com o PowerShell ou da CLI do Azure.

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

O **zona DNS** painel mostra os registos IPv6 PTR:

![Painel "Zona DNS" com os registos de IPv6](./media/dns-reverse-dns-hosting/figure9.png)

Os exemplos seguintes mostram como ver os registos com o PowerShell ou da CLI do Azure.

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

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Pode alojar zonas de pesquisa DNS inversas para meu blocos IP atribuído por ISP no DNS do Azure?

Sim. Que aloja as zonas de pesquisa inversa (ARPA) para seus próprios intervalos de IP no DNS do Azure é totalmente suportado.

Crie a zona de pesquisa inversa no DNS do Azure, conforme explicado neste artigo e, em seguida, trabalhar com o seu ISP para [delegue a zona](dns-domain-delegation.md). Em seguida, pode gerir os registos PTR para cada pesquisa inversa da mesma forma que outros tipos de registo.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>Quanto o meu custo de zona de pesquisa DNS inverso de hospedagem?

Que aloja a zona de pesquisa DNS inversa para o seu bloco IP atribuído por ISP no DNS do Azure é cobrado [taxas padrão do DNS do Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Pode alojar zonas de pesquisa DNS inversas para endereços IPv4 e IPv6 no DNS do Azure?

Sim. Este artigo explica como criar o IPv4 e IPv6 zonas de pesquisa DNS inversas no DNS do Azure.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Pode importar uma zona de pesquisa DNS reversa existente?

Sim. Pode utilizar a CLI do Azure para importar as zonas DNS existentes para o DNS do Azure. Este método funciona para zonas de pesquisa direta e zonas de pesquisa inversa.

Para obter mais informações, consulte [importação e exportação de um ficheiro de zona DNS com a CLI do Azure](dns-import-export.md).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre DNS inverso, consulte [pesquisa reversa de DNS na Wikipédia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Saiba como [gerir registos DNS inversos para seus serviços do Azure](dns-reverse-dns-for-azure-services.md).
