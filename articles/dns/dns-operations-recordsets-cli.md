---
title: Gerir registos DNS em Azure DNS usando o Azure CLI
description: Gerir os recordes de DNS e os registos no Azure DNS ao hospedar o seu domínio no Azure DNS.
author: rohinkoul
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: azurecli
ms.topic: how-to
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 05/15/2018
ms.author: rohink
ms.openlocfilehash: 4bf3ee75c9445856fb8a2ce789a3f2f345e720fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84701669"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-the-azure-cli"></a>Gerir registos e registos DNS em Azure DNS usando o Azure CLI

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-operations-recordsets-portal.md)
> * [CLI do Azure](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Este artigo mostra-lhe como gerir os registos DNS para a sua zona de DNS utilizando o Azure CLI, que está disponível para Windows, Mac e Linux. Também pode gerir os seus registos DNS utilizando [o Azure PowerShell](dns-operations-recordsets.md) ou o [portal Azure](dns-operations-recordsets-portal.md).

Os exemplos deste artigo assumem que já [instalou o CLI Azure, assinou e criou uma zona de DNS.](dns-operations-dnszones-cli.md)

## <a name="introduction"></a>Introdução

Antes de criar registos DNS no DNS do Azure, terá primeiro de compreender como o DNS do Azure organiza os registos DNS em conjuntos de registos DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Para obter mais informações sobre os registos DNS no DNS do Azure, veja [Zonas e registos DNS](dns-zones-records.md).

## <a name="create-a-dns-record"></a>Criar um registo DNS

Para criar um registo DNS, utilize o `az network dns record-set <record-type> add-record` comando (onde `<record-type>` está o tipo de registo, ou seja, a, srv, txt, etc.) Para pedir `az network dns record-set --help` ajuda, vê.

Ao criar um registo, tem de especificar o nome do grupo de recursos, o nome da zona, o nome do conjunto de recursos, o tipo de registo e os detalhes do registo que está a ser criado. O nome de conjunto de registos dado deve ser um nome *relativo,* o que significa que deve excluir o nome da zona.

Se o conjunto de registos ainda não existir, este comando cria-o por si. Se o conjunto de registos já existir, este comando adiciona o registo que especificar ao conjunto de registos existente.

Se for criado um novo conjunto de registos, é utilizado um TTL (time-to-live) predefinido de 3600. Para obter instruções sobre como utilizar diferentes TTLs, consulte [Criar um conjunto de registos DNS](#create-a-dns-record-set).

O exemplo seguinte cria um registo A denominado *www* na zona *contoso.com* do grupo de recursos *MyResourceGroup*. O endereço IP do registo A é *1.2.3.4*.

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Para criar um recorde estabelecido no ápice da zona (neste caso, "contoso.com"), utilize o nome de registo " \@ ", incluindo as aspas:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>Crie um conjunto de registos DNS

Nos exemplos acima, o registo de DNS foi adicionado a um recorde existente, ou o recorde foi criado *implicitamente*. Também pode criar o conjunto de *registos explicitamente* antes de lhe adicionar registos. O Azure DNS suporta conjuntos de registos 'vazios', que podem funcionar como um espaço reservado para reservar um nome DNS antes de criar registos DNS. Os conjuntos de registos vazios são visíveis no plano de controlo Azure DNS, mas não aparecem nos servidores de nomes Azure DNS.

Os conjuntos de discos são criados usando o `az network dns record-set <record-type> create` comando. Para obter ajuda, consulte `az network dns record-set <record-type> create --help`.

A criação do conjunto de registos permite especificar propriedades definidas como o [Time-To-Live (TTL)](dns-zones-records.md#time-to-live) e os metadados. [Os metadados de conjunto de registos](dns-zones-records.md#tags-and-metadata) podem ser usados para associar dados específicos da aplicação a cada conjunto de registos, como pares de valor-chave.

O exemplo a seguir cria um conjunto de registo vazio do tipo 'A' com um TTL de 60 segundos, utilizando o `--ttl` parâmetro (forma `-l` curta):

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --ttl 60
```

O exemplo a seguir cria um conjunto de registos com duas entradas de metadados, "dept=finance" e "environment=production", utilizando o `--metadata` parâmetro :

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --metadata "dept=finance" "environment=production"
```

Tendo criado um conjunto de registos vazio, os registos podem ser adicionados `azure network dns record-set <record-type> add-record` usando como descrito no [Create a DNS record](#create-a-dns-record).

## <a name="create-records-of-other-types"></a>Criar registos de outros tipos

Tendo visto em detalhe como criar registos 'A', os seguintes exemplos mostram como criar registo de outros tipos de discos suportados pelo Azure DNS.

Os parâmetros utilizados para especificar os dados de registo variam consoante o tipo de registo. Por exemplo, para um registo do tipo "A", especifica o endereço IPv4 com o parâmetro `--ipv4-address <IPv4 address>`. Os parâmetros de cada tipo de registo podem ser listados utilizando `az network dns record-set <record-type> add-record --help` .

Em cada caso, mostramos como criar um único disco. O recorde é adicionado ao recorde existente, ou a um conjunto de recordes criado implicitamente. Para obter mais informações sobre a criação de conjuntos de registos e definição de parâmetros de fixação de registos explicitamente, consulte [Criar um conjunto de registos DNS](#create-a-dns-record-set).

Não damos um exemplo para criar um conjunto de registos SOA, uma vez que os SOAs são criados e eliminados com cada zona de DNS e não podem ser criados ou eliminados separadamente. No entanto, [o SOA pode ser modificado, como mostra um exemplo posterior.](#to-modify-an-soa-record)

### <a name="create-an-aaaa-record"></a>Criar um registo AAAA

```azurecli
az network dns record-set aaaa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-aaaa --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-an-caa-record"></a>Criar um registo caa

```azurecli
az network dns record-set caa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-caa --flags 0 --tag "issue" --value "ca1.contoso.com"
```

### <a name="create-a-cname-record"></a>Criar um registo CNAME

> [!NOTE]
> As normas DNS não permitem registos CNAME no ápice de uma zona ( `--Name "@"` ), nem permitem conjuntos de recordes que contenham mais de um recorde.
> 
> Para mais informações, consulte [os registos da CNAME.](dns-zones-records.md#cname-records)

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>Criar um disco MX

Neste exemplo, usamos o nome de recorde " \@ " para criar o disco MX no ápice da zona (neste caso, "contoso.com").

```azurecli
az network dns record-set mx add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>Criar um registo NS

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-ns --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>Criar um registo PTR

Neste caso, o 'my-arpa-zone.com' representa a zona ARPA que representa a sua gama IP. Cada conjunto de registos PTR nesta zona corresponde a um endereço IP neste intervalo de IPs.  O nome de registo '10' é o último octeto do endereço IP dentro deste intervalo IP representado por este registo.

```azurecli
az network dns record-set ptr add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name my-arpa.zone.com --ptrdname myservice.contoso.com
```

### <a name="create-an-srv-record"></a>Criar um registo SRV

Ao criar um [conjunto de registos SRV,](dns-zones-records.md#srv-records)especifique o * \_ serviço* e o * \_ protocolo* no nome do conjunto de registos. Não há necessidade de incluir " \@ " no nome recorde ao criar um recorde DEV estabelecido no ápice da zona.

```azurecli
az network dns record-set srv add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name _sip._tls --priority 10 --weight 5 --port 8080 --target sip.contoso.com
```

### <a name="create-a-txt-record"></a>Criar um registo TXT

O exemplo a seguir mostra como criar um registo TXT. Para obter mais informações sobre o comprimento máximo da corda suportado nos registos TXT, consulte [os registos TXT](dns-zones-records.md#txt-records).

```azurecli
az network dns record-set txt add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-txt --value "This is a TXT record"
```

## <a name="get-a-record-set"></a>Obter um recorde definido

Para recuperar um recorde existente, `az network dns record-set <record-type> show` utilize. Para obter ajuda, consulte `az network dns record-set <record-type> show --help`.

Como quando se cria um recorde ou um recorde, o nome de recorde dado deve ser um nome *relativo,* o que significa que deve excluir o nome da zona. Também é necessário especificar o tipo de gravação, a zona que contém o conjunto de registos e o grupo de recursos que contém a zona.

O exemplo a seguir recupera o registo *www do* tipo A da zona *contoso.com* no grupo de recursos *MyResourceGroup:*

```azurecli
az network dns record-set a show --resource-group myresourcegroup --zone-name contoso.com --name www
```

## <a name="list-record-sets"></a>Conjuntos de recordes de listas

Pode listar todos os registos numa zona DE DNS utilizando o `az network dns record-set list` comando. Para obter ajuda, consulte `az network dns record-set list --help`.

Este exemplo devolve todos os recordes na zona *contoso.com,* no grupo de recursos *MyResourceGroup,* independentemente do nome ou tipo de gravação:

```azurecli
az network dns record-set list --resource-group myresourcegroup --zone-name contoso.com
```

Este exemplo devolve todos os recordes que correspondem ao tipo de registo dado (neste caso, 'A' recordes):

```azurecli
az network dns record-set a list --resource-group myresourcegroup --zone-name contoso.com 
```

## <a name="add-a-record-to-an-existing-record-set"></a>Adicione um recorde a um conjunto de recordes existente

Pode usar `az network dns record-set <record-type> add-record` ambos para criar um recorde num novo recorde, ou para adicionar um recorde a um conjunto de recordes existente.

Para obter mais informações, consulte [Criar um registo DNS](#create-a-dns-record) e [Criar registos de outros tipos](#create-records-of-other-types) acima.

## <a name="remove-a-record-from-an-existing-record-set"></a>Remova um registo de um conjunto de registos existente.

Para remover um registo DNS de um conjunto de registos existente, utilize `az network dns record-set <record-type> remove-record` . Para obter ajuda, consulte `az network dns record-set <record-type> remove-record -h`.

Este comando elimina um registo DNS de um conjunto de registos. Se o último recorde de um conjunto de recordes for eliminado, o próprio recorde também é eliminado. Para manter o recorde vazio, utilize a `--keep-empty-record-set` opção.

É necessário especificar o registo a eliminar e a zona da qual deve ser eliminado, utilizando os mesmos parâmetros que ao criar um registo utilizando `az network dns record-set <record-type> add-record` . Estes parâmetros são descritos em [Criar um registo DNS](#create-a-dns-record) e [criar registos de outros tipos](#create-records-of-other-types) acima.

O exemplo a seguir elimina o registo A com o valor '1.2.3.4' do conjunto de discos denominado *www na* zona *contoso.com*, no grupo de recursos *MyResourceGroup*.

```azurecli
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "www" --ipv4-address 1.2.3.4
```

## <a name="modify-an-existing-record-set"></a>Modificar um conjunto de recordes existente

Cada conjunto de registos contém registos [time-to-live (TTL),](dns-zones-records.md#time-to-live) [metadados](dns-zones-records.md#tags-and-metadata)e DNS. As seguintes secções explicam como modificar cada uma destas propriedades.

### <a name="to-modify-an-a-aaaa-caa-mx-ns-ptr-srv-or-txt-record"></a>Para modificar um registo A, AAA, CAA, MX, NS, PTR, SRV ou TXT

Para modificar um registo existente do tipo A, AAAA, CAA, MX, NS, PTR, SRV ou TXT, deve primeiro adicionar um novo registo e depois apagar o registo existente. Para obter instruções detalhadas sobre como eliminar e adicionar registos, consulte as secções anteriores deste artigo.

O exemplo a seguir mostra como modificar um registo 'A', do endereço IP 1.2.3.4 para endereço IP 5.6.7.8:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 5.6.7.8
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Não é possível adicionar, remover ou modificar os registos no gravador NS automaticamente estabelecido no ápice da zona `--Name "@"` (, incluindo as marcas de cotação). Para este conjunto de recordes, as únicas alterações permitidas são modificar o recorde de TTL e metadados.

### <a name="to-modify-a-cname-record"></a>Para modificar um registo CNAME

Ao contrário da maioria dos outros tipos de discos, um conjunto de discos CNAME só pode conter um único registo.  Por isso, não é possível substituir o valor atual adicionando um novo registo e removendo o registo existente, como para outros tipos de registo.

Em vez disso, para modificar um registo CNAME, utilize `az network dns record-set cname set-record` . Para obter ajuda, consulte `az network dns record-set cname set-record --help`

O exemplo modifica o recorde CNAME *estabelecido* www na zona *contoso.com*, no grupo de recursos *MyResourceGroup*, para apontar para "www.fabrikam.net" em vez do seu valor existente:

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>Para modificar um registo SOA

Ao contrário da maioria dos outros tipos de discos, um conjunto de discos CNAME só pode conter um único registo.  Por isso, não é possível substituir o valor atual adicionando um novo registo e removendo o registo existente, como para outros tipos de registo.

Em vez disso, para modificar o registo SOA, use `az network dns record-set soa update` . Para obter ajuda, consulte `az network dns record-set soa update --help`.

O exemplo a seguir mostra como definir a propriedade 'email' do registo SOA para a zona *contoso.com* no grupo de recursos *MyResourceGroup*:

```azurecli
az network dns record-set soa update --resource-group myresourcegroup --zone-name contoso.com --email admin.contoso.com
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Para modificar os registos NS no ápice da zona

O recorde NS estabelecido no ápice da zona é automaticamente criado com cada zona DE DNS. Contém os nomes dos servidores de nomeS DNS Azure atribuídos à zona.

Pode adicionar servidores de nome adicionais a este conjunto de registos NS, para suportar domínios de co-hospedagem com mais de um fornecedor de DNS. Também pode modificar o TTL e metadados para este conjunto de registos. No entanto, não é possível remover ou modificar os servidores de nomeS DNS pré-povoados.

Note que isto se aplica apenas ao recorde NS estabelecido no ápice da zona. Outros conjuntos de registos NS na sua zona (como usado para delegar zonas infantis) podem ser modificados sem restrições.

O exemplo a seguir mostra como adicionar um servidor de nome adicional ao registo NS estabelecido no ápice da zona:

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>Para modificar o TTL de um conjunto de recordes existente

Para modificar o TTL de um conjunto de registos existente, utilize `azure network dns record-set <record-type> update` . Para obter ajuda, consulte `azure network dns record-set <record-type> update --help`.

O exemplo a seguir mostra como modificar um TTL de recorde, neste caso a 60 segundos:

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set ttl=60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>Para modificar os metadados de um conjunto de registos existente

[Os metadados de conjunto de registos](dns-zones-records.md#tags-and-metadata) podem ser usados para associar dados específicos da aplicação a cada conjunto de registos, como pares de valor-chave. Para modificar os metadados de um conjunto de registos existente, utilize `az network dns record-set <record-type> update` . Para obter ajuda, consulte `az network dns record-set <record-type> update --help`.

O exemplo a seguir mostra como modificar um conjunto de registos com duas entradas de metadados, "dept=finance" e "environment=production". Note que quaisquer metadados existentes são *substituídos* pelos valores dados.

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set metadata.dept=finance metadata.environment=production
```

## <a name="delete-a-record-set"></a>Apagar um conjunto de recordes

Os conjuntos de registos podem ser eliminados utilizando o `az network dns record-set <record-type> delete` comando. Para obter ajuda, consulte `azure network dns record-set <record-type> delete --help`. A eliminação de um recorde também elimina todos os recordes dentro do recorde estabelecido.

> [!NOTE]
> Não é possível eliminar os conjuntos de registos SOA e NS no ápice da zona `--name "@"` ().  Estes são criados automaticamente quando a zona foi criada, e são eliminados automaticamente quando a zona é eliminada.

O exemplo a seguir elimina o conjunto de registos denominado *www do* tipo A da zona *contoso.com* no grupo de recursos *MyResourceGroup*:

```azurecli
az network dns record-set a delete --resource-group myresourcegroup --zone-name contoso.com --name www
```

É-lhe pedido que confirme a operação de eliminação. Para suprimir este pedido, utilize o `--yes` interruptor.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [zonas e registos em Azure DNS](dns-zones-records.md).
<br>
Aprenda a [proteger as suas zonas e registos](dns-protect-zones-recordsets.md) ao utilizar o Azure DNS.
