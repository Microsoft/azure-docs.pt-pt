---
title: Gerir os registos dNS em DNS Azure utilizando o Azure CLI
description: Gerir os recordes e registos do DNS do DNS do Azure ao hospedar o seu domínio no Azure DNS.
author: rohinkoul
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: azurecli
ms.topic: conceptual
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 05/15/2018
ms.author: rohink
ms.openlocfilehash: 4e017dc940e1d32888ff279904e44d34db1fd5c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76936894"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-the-azure-cli"></a>Gerir registos dNS e recordes em DNS Azure utilizando o Azure CLI

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-operations-recordsets-portal.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Este artigo mostra-lhe como gerir os registos DNS para a sua zona DNS utilizando o Azure CLI cross-platform, que está disponível para Windows, Mac e Linux. Também pode gerir os seus registos DNS utilizando o [Azure PowerShell](dns-operations-recordsets.md) ou o [portal Azure](dns-operations-recordsets-portal.md).

Os exemplos deste artigo assumem que já [instalou o Azure CLI, assinou e criou uma zona DNS](dns-operations-dnszones-cli.md).

## <a name="introduction"></a>Introdução

Antes de criar registos DNS no DNS do Azure, terá primeiro de compreender como o DNS do Azure organiza os registos DNS em conjuntos de registos DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Para obter mais informações sobre os registos DNS no DNS do Azure, veja [Zonas e registos DNS](dns-zones-records.md).

## <a name="create-a-dns-record"></a>Criar um registo DNS

Para criar um registo DNS, utilize o `az network dns record-set <record-type> add-record` comando (onde `<record-type>` está o tipo de registo, ou seja, a, srv, txt, etc.) Para ajuda, `az network dns record-set --help`veja.

Ao criar um registo, tem de especificar o nome do grupo de recursos, o nome da zona, o nome do conjunto de recursos, o tipo de registo e os detalhes do registo que está a ser criado. O nome do conjunto de registos dado deve ser um nome *relativo,* o que significa que deve excluir o nome da zona.

Se o conjunto de registos ainda não existir, este comando cria-o por si. Se o conjunto de registos já existir, este comando adiciona o registo que especificar ao conjunto de registos existente.

Se for criado um novo conjunto de registos, é utilizado um TTL (time-to-live) predefinido de 3600. Para obter instruções sobre como utilizar diferentes TTLs, consulte Criar um conjunto de [registos DNS](#create-a-dns-record-set).

O exemplo seguinte cria um registo A denominado *www* na zona *contoso.com* do grupo de recursos *MyResourceGroup*. O endereço IP do registo A é *1.2.3.4*.

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Para criar um recorde estabelecido no ápice da zona (neste caso,\@"contoso.com"), utilize o nome de registo ", incluindo as aspas:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>Criar um conjunto de registos DNS

Nos exemplos acima referidos, o registo dNS foi adicionado a um recorde existente, ou o conjunto de registos foi criado *implicitamente.* Também pode criar o conjunto de registos *explicitamente* antes de adicionar registos. O Azure DNS suporta conjuntos de discos "vazios", que podem funcionar como um espaço reservado para reservar um nome DNS antes de criar registos DNS. Os conjuntos de registos vazios são visíveis no plano de controlo DoNS Azure, mas não aparecem nos servidores de nome SN Do Azure.

Os conjuntos de `az network dns record-set <record-type> create` registo saem criados usando o comando. Para obter ajuda, consulte `az network dns record-set <record-type> create --help`.

A criação do conjunto de registos permite-lhe especificar propriedades de séries de discos como o [Time-To-Live (TTL)](dns-zones-records.md#time-to-live) e metadados. [Os metadados de conjuntos](dns-zones-records.md#tags-and-metadata) de registo podem ser usados para associar dados específicos da aplicação a cada conjunto de registos, como pares de valor-chave.

O exemplo seguinte cria um conjunto de registovazio do tipo 'A' com `--ttl` um TTL de 60 segundos, utilizando o parâmetro (forma `-l`curta):

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --ttl 60
```

O exemplo seguinte cria um conjunto de registos com duas entradas de metadados, `--metadata` "dept=finance" e "environment=production", utilizando o parâmetro:

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --metadata "dept=finance" "environment=production"
```

Tendo criado um conjunto de registos `azure network dns record-set <record-type> add-record` vazio, os registos podem ser adicionados usando como descrito no [Create a DNS record](#create-a-dns-record).

## <a name="create-records-of-other-types"></a>Criar registos de outros tipos

Tendo visto em detalhe como criar registos 'A', os seguintes exemplos mostram como criar registos de outros tipos de discos suportados pelo Azure DNS.

Os parâmetros utilizados para especificar os dados de registo variam consoante o tipo de registo. Por exemplo, para um registo do tipo "A", especifica o endereço IPv4 com o parâmetro `--ipv4-address <IPv4 address>`. Os parâmetros de cada tipo `az network dns record-set <record-type> add-record --help`de registo podem ser listados utilizando .

Em cada caso, mostramos como criar um único disco. O recorde é adicionado ao recorde existente, ou um conjunto de recordes criado implicitamente. Para obter mais informações sobre a criação de séries de discos e definindo explicitamente o parâmetro de série de discos, consulte Criar um conjunto de [registos DNS](#create-a-dns-record-set).

Não damos um exemplo para criar um conjunto de registos SOA, uma vez que os SOAs são criados e eliminados com cada zona DNS e não podem ser criados ou eliminados separadamente. No entanto, [o SOA pode ser modificado, como mostra um exemplo posterior](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record"></a>Criar um recorde aAAA

```azurecli
az network dns record-set aaaa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-aaaa --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-an-caa-record"></a>Criar um recorde caa

```azurecli
az network dns record-set caa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-caa --flags 0 --tag "issue" --value "ca1.contoso.com"
```

### <a name="create-a-cname-record"></a>Criar um disco CNAME

> [!NOTE]
> As normas DNS não permitem registos CNAME`--Name "@"`no ápice de uma zona , nem permitem estabelecidos recordes que contenham mais de um recorde.
> 
> Para mais informações, consulte [os registos do CNAME](dns-zones-records.md#cname-records).

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>Criar um disco MX

Neste exemplo, usamos o nome\@do disco " para criar o registo MX no ápice da zona (neste caso, "contoso.com").

```azurecli
az network dns record-set mx add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>Criar um registo ns

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-ns --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>Criar um disco PTR

Neste caso, o 'my-arpa-zone.com' representa a zona ARPA que representa a sua gama IP. Cada conjunto de registos PTR nesta zona corresponde a um endereço IP neste intervalo de IPs.  O nome de registo '10' é o último octeto do endereço IP dentro desta gama IP representado por este registo.

```azurecli
az network dns record-set ptr add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name my-arpa.zone.com --ptrdname myservice.contoso.com
```

### <a name="create-an-srv-record"></a>Criar um recorde de SRV

Ao criar um conjunto de [registos SRV,](dns-zones-records.md#srv-records)especifique o * \_serviço* e * \_* o protocolo no nome do conjunto de registos. Não há necessidade de\@incluir " no nome do recorde ao criar um recorde SRV estabelecido no ápice da zona.

```azurecli
az network dns record-set srv add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name _sip._tls --priority 10 --weight 5 --port 8080 --target sip.contoso.com
```

### <a name="create-a-txt-record"></a>Criar um disco TXT

O exemplo que se segue mostra como criar um disco TXT. Para obter mais informações sobre o comprimento máximo da corda suportado nos registos TXT, consulte [os registos TXT](dns-zones-records.md#txt-records).

```azurecli
az network dns record-set txt add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-txt --value "This is a TXT record"
```

## <a name="get-a-record-set"></a>Obter um recorde

Para recuperar um conjunto de `az network dns record-set <record-type> show`registos existente, utilize . Para obter ajuda, consulte `az network dns record-set <record-type> show --help`.

Como quando se cria um recorde ou um recorde, o nome de conjunto de registos dado deve ser um nome *relativo,* o que significa que deve excluir o nome da zona. Também precisa especificar o tipo de registo, a zona que contém o conjunto de registos e o grupo de recursos que contém a zona.

O exemplo seguinte recupera o registo *www do* tipo A da zona *contoso.com* no grupo de recursos *MyResourceGroup:*

```azurecli
az network dns record-set a show --resource-group myresourcegroup --zone-name contoso.com --name www
```

## <a name="list-record-sets"></a>Conjuntos de registos de listas

Pode listar todos os registos numa `az network dns record-set list` zona DNS utilizando o comando. Para obter ajuda, consulte `az network dns record-set list --help`.

Este exemplo devolve todos os conjuntos de registos na zona *contoso.com*, no grupo de recursos *MyResourceGroup,* independentemente do nome ou do tipo de gravação:

```azurecli
az network dns record-set list --resource-group myresourcegroup --zone-name contoso.com
```

Este exemplo devolve todos os conjuntos de registos que correspondem ao tipo de registo dado (neste caso, registos 'A'):

```azurecli
az network dns record-set a list --resource-group myresourcegroup --zone-name contoso.com 
```

## <a name="add-a-record-to-an-existing-record-set"></a>Adicione um recorde a um recorde existente

Pode usar `az network dns record-set <record-type> add-record` ambos para criar um recorde num novo recorde, ou para adicionar um recorde a um recorde existente.

Para obter mais informações, consulte [Criar um registo DNS](#create-a-dns-record) e [criar registos de outros tipos](#create-records-of-other-types) acima.

## <a name="remove-a-record-from-an-existing-record-set"></a>Remova um recorde de um conjunto de registos existente.

Para remover um registo DNS de um `az network dns record-set <record-type> remove-record`conjunto de registos existente, utilize . Para obter ajuda, consulte `az network dns record-set <record-type> remove-record -h`.

Este comando elimina um registo DNS de um conjunto de registos. Se o último disco de um conjunto de registos for eliminado, o próprio disco também é eliminado. Para manter o conjunto de `--keep-empty-record-set` registos vazios, utilize a opção.

É necessário especificar o registo a eliminar e a zona de onde deve ser eliminada, utilizando os mesmos parâmetros que quando se cria um registo utilizando `az network dns record-set <record-type> add-record`. Estes parâmetros são descritos em [Criar um registo DNS](#create-a-dns-record) e [criar registos de outros tipos](#create-records-of-other-types) acima.

O exemplo seguinte elimina o registo A com o valor '1.2.3.4' do conjunto de registos denominado *saqueado www* na zona *contoso.com*, no grupo de recursos *MyResourceGroup*.

```azurecli
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "www" --ipv4-address 1.2.3.4
```

## <a name="modify-an-existing-record-set"></a>Modificar um conjunto de registos existente

Cada conjunto de registos contém um [tempo de vida (TTL),](dns-zones-records.md#time-to-live) [metadados](dns-zones-records.md#tags-and-metadata)e registos DNS. As seguintes secções explicam como modificar cada uma destas propriedades.

### <a name="to-modify-an-a-aaaa-caa-mx-ns-ptr-srv-or-txt-record"></a>Para modificar um disco A, AAAA, CAA, MX, NS, PTR, SRV ou TXT

Para modificar um registo existente de tipo A, AAAA, CAA, MX, NS, PTR, SRV ou TXT, deve primeiro adicionar um novo disco e, em seguida, apagar o registo existente. Para obter instruções detalhadas sobre como eliminar e adicionar registos, consulte as secções anteriores deste artigo.

O exemplo seguinte mostra como modificar um registo 'A', desde o endereço IP 1.2.3.4 até ao endereço IP 5.6.7.8:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 5.6.7.8
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Não é possível adicionar, remover ou modificar os registos no registo`--Name "@"`NS criado automaticamente no ápice da zona ( incluindo marcas de cotação). Para este conjunto de registos, as únicas alterações permitidas são modificar o conjunto de registos TTL e metadados.

### <a name="to-modify-a-cname-record"></a>Para modificar um registo CNAME

Ao contrário da maioria dos outros tipos de discos, um conjunto de registos CNAME só pode conter um único disco.  Portanto, não é possível substituir o valor atual adicionando um novo recorde e removendo o registo existente, como para outros tipos de discos.

Em vez disso, para modificar `az network dns record-set cname set-record`um registo CNAME, utilize . Para obter ajuda, consulte `az network dns record-set cname set-record --help`

O exemplo modifica o registo CNAME estabelecido *www* na zona *contoso.com*, no grupo de recursos *MyResourceGroup*, para apontar para 'www.fabrikam.net' em vez do seu valor existente:

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>Para modificar um registo SOA

Ao contrário da maioria dos outros tipos de discos, um conjunto de registos CNAME só pode conter um único disco.  Portanto, não é possível substituir o valor atual adicionando um novo recorde e removendo o registo existente, como para outros tipos de discos.

Em vez disso, para modificar `az network dns record-set soa update`o registo SOA, utilize . Para obter ajuda, consulte `az network dns record-set soa update --help`.

O exemplo que se segue mostra como definir a propriedade 'email' do registo SOA para a zona *contoso.com* no grupo de recursos *MyResourceGroup:*

```azurecli
az network dns record-set soa update --resource-group myresourcegroup --zone-name contoso.com --email admin.contoso.com
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Para modificar os registos de NS no ápice da zona

O registo NS estabelecido no ápice da zona é automaticamente criado com cada zona DNS. Contém os nomes dos servidores de nome SN Azure atribuídos à zona.

Pode adicionar servidores de nome adicionais a este conjunto de registos NS, para suportar domínios de co-hospedagem com mais de um fornecedor DNS. Também pode modificar o TTL e os metadados para este conjunto de registos. No entanto, não é possível remover ou modificar os servidores de nome DNS Do DNS do Azure pré-povoados.

Note que isto se aplica apenas ao registo nS estabelecido no ápice da zona. Outros conjuntos de registos de NS na sua zona (como usado para delegar zonas infantis) podem ser modificados sem restrições.

O exemplo seguinte mostra como adicionar um servidor de nome adicional ao registo NS estabelecido no ápice da zona:

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>Modificar o TTL de um conjunto de recordes existente

Para modificar o TTL de um `azure network dns record-set <record-type> update`conjunto de registos existente, utilize . Para obter ajuda, consulte `azure network dns record-set <record-type> update --help`.

O exemplo que se segue mostra como modificar um conjunto de registos TTL, neste caso para 60 segundos:

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set ttl=60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>Para modificar os metadados de um conjunto de registos existente

[Os metadados de conjuntos](dns-zones-records.md#tags-and-metadata) de registo podem ser usados para associar dados específicos da aplicação a cada conjunto de registos, como pares de valor-chave. Para modificar os metadados de um `az network dns record-set <record-type> update`conjunto de registos existente, utilize . Para obter ajuda, consulte `az network dns record-set <record-type> update --help`.

O exemplo seguinte mostra como modificar um conjunto de registos com duas entradas de metadados, "dept=finance" e "environment=production". Note que quaisquer metadados existentes são *substituídos* pelos valores dados.

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set metadata.dept=finance metadata.environment=production
```

## <a name="delete-a-record-set"></a>Apagar um conjunto de registos

Os conjuntos de registopodem `az network dns record-set <record-type> delete` ser eliminados utilizando o comando. Para obter ajuda, consulte `azure network dns record-set <record-type> delete --help`. A eliminação de um conjunto de registos também elimina todos os registos dentro do conjunto de registos.

> [!NOTE]
> Não é possível eliminar os conjuntos de registoS`--name "@"`SOA e NS no ápice da zona ( ).  Estes são criados automaticamente quando a zona foi criada, e são eliminadas automaticamente quando a zona é eliminada.

O exemplo seguinte elimina o conjunto de registos denominado *www do* tipo A da zona *contoso.com* no grupo de recursos *MyResourceGroup:*

```azurecli
az network dns record-set a delete --resource-group myresourcegroup --zone-name contoso.com --name www
```

É solicitado que confirme a operação de eliminação. Para suprimir esta solicitação, utilize o `--yes` interruptor.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [zonas e registos em Azure DNS.](dns-zones-records.md)
<br>
Aprenda a [proteger as suas zonas e registos](dns-protect-zones-recordsets.md) ao utilizar o Azure DNS.
