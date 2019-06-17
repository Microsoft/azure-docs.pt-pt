---
title: Gerir registos DNS no DNS do Azure com a CLI do Azure | Documentos da Microsoft
description: Gerir conjuntos de registos DNS e registos no DNS do Azure ao alojar o seu domínio no DNS do Azure.
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 05/15/2018
ms.author: victorh
ms.openlocfilehash: 4864a46b91b4e243ce6a2ae3d9d36df28fe74d8d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61293359"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-the-azure-cli"></a>Gerir registos DNS e conjuntos de registros no DNS do Azure com a CLI do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-operations-recordsets-portal.md)
> * [CLI do Azure](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Este artigo mostra-lhe como gerir registos DNS para a sua zona DNS ao utilizar a CLI do Azure para várias plataformas, que está disponível para Windows, Mac e Linux. Também pode gerir os registos DNS com [do Azure PowerShell](dns-operations-recordsets.md) ou o [portal do Azure](dns-operations-recordsets-portal.md).

Os exemplos neste artigo partem do princípio de que já [instalado a CLI do Azure, iniciou sessão e criou uma zona DNS](dns-operations-dnszones-cli.md).

## <a name="introduction"></a>Introdução

Antes de criar registos DNS no DNS do Azure, terá primeiro de compreender como o DNS do Azure organiza os registos DNS em conjuntos de registos DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Para obter mais informações sobre os registos DNS no DNS do Azure, veja [Zonas e registos DNS](dns-zones-records.md).

## <a name="create-a-dns-record"></a>Criar um registo DNS

Para criar um registo DNS, utilize o `az network dns record-set <record-type> add-record` comando (onde `<record-type>` é o tipo de registo, ou seja um, srv, txt, etc.) Para obter ajuda, consulte `az network dns record-set --help`.

Ao criar um registo, tem de especificar o nome do grupo de recursos, o nome da zona, o nome do conjunto de recursos, o tipo de registo e os detalhes do registo que está a ser criado. O nome do conjunto de registos fornecido tem de ser um *relativo* nome, que significa que tem de excluir o nome da zona.

Se o conjunto de registos ainda não existir, este comando cria-o por si. Se o conjunto de registos já existir, este comando adiciona o registo que especificar ao conjunto de registos existente.

Se for criado um novo conjunto de registos, é utilizado um TTL (time-to-live) predefinido de 3600. Para obter instruções sobre como utilizar TTLs diferentes, consulte [criar um conjunto de registos de DNS](#create-a-dns-record-set).

O exemplo seguinte cria um registo A denominado *www* na zona *contoso.com* do grupo de recursos *MyResourceGroup*. O endereço IP do registo A é *1.2.3.4*.

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Para criar um conjunto de registos no vértice da zona (neste caso, "contoso.com"), utilize o nome do registo "\@", incluindo as aspas:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>Criar um conjunto de registos de DNS

Nos exemplos acima, o registo DNS ou foi adicionado a um conjunto de registo existente, ou foi criado o conjunto de registos *implicitamente*. Também é possível criar o conjunto de registos *explicitamente* antes de adicionar registos ao mesmo. O DNS do Azure suporta conjuntos de registos 'empty', que podem atuar como um marcador de posição para reservar um nome DNS antes de criar registos DNS. Conjuntos de registos vazios são visíveis no painel de controlo de DNS do Azure, mas não aparecem nos servidores de nome DNS do Azure.

Conjuntos de registos são criados usando o `az network dns record-set <record-type> create` comando. Para obter ajuda, consulte `az network dns record-set <record-type> create --help`.

Criar o registo de definir explicitamente permite-lhe especificar o conjunto de registos propriedades, tais como o [Time-To-Live (TTL)](dns-zones-records.md#time-to-live) e metadados. [Metadados do conjunto de registos](dns-zones-records.md#tags-and-metadata) pode ser utilizado para associar dados específicos a cada conjunto de registos, como pares chave-valor.

O exemplo seguinte cria um conjunto de registos vazio do tipo "A" com um valor de TTL de 60 segundos, utilizando o `--ttl` parâmetro (krátká forma `-l`):

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --ttl 60
```

O exemplo seguinte cria um conjunto de registos com duas entradas de metadados, "departamento = finance" e "ambiente = produção", ao utilizar o `--metadata` parâmetro:

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --metadata "dept=finance" "environment=production"
```

Tendo criado um conjunto de registos vazio, registos podem ser adicionados usando `azure network dns record-set <record-type> add-record` conforme descrito na [criar um registo DNS](#create-a-dns-record).

## <a name="create-records-of-other-types"></a>Criar registos de outros tipos

Depois de ter visto em detalhe como criar registos de "A", os exemplos seguintes mostram como criar registo de outros tipos de registos suportados pelo DNS do Azure.

Os parâmetros utilizados para especificar os dados de registo variam consoante o tipo de registo. Por exemplo, para um registo do tipo "A", especifica o endereço IPv4 com o parâmetro `--ipv4-address <IPv4 address>`. Os parâmetros para cada tipo de registo podem ser indicados, utilizando `az network dns record-set <record-type> add-record --help`.

Em cada caso, vamos mostrar como criar um único registo. O registo é adicionado ao conjunto de registos existente ou um conjunto de registos criadas implicitamente. Para obter mais informações sobre como criar conjuntos de registos e a definição de registo defina o parâmetro explicitamente, consulte [criar um conjunto de registos de DNS](#create-a-dns-record-set).

Não podemos dar um exemplo para criar um conjunto de registos SOA, uma vez que SOAs são criados e eliminado com cada zona DNS e não pode ser criada ou eliminada separadamente. No entanto, [pode ser modificado a SOA, como mostra um exemplo posterior](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record"></a>Criar um registo AAAA

```azurecli
az network dns record-set aaaa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-aaaa --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-an-caa-record"></a>Criar um registo CAA

```azurecli
az network dns record-set caa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-caa --flags 0 --tag "issue" --value "ca1.contoso.com"
```

### <a name="create-a-cname-record"></a>Criar um registo CNAME

> [!NOTE]
> As normas DNS não permitem registos CNAME no vértice da zona de um (`--Name "@"`), nem que permitem a conjuntos de registos que contêm mais de um registo.
> 
> Para obter mais informações, consulte [registos CNAME](dns-zones-records.md#cname-records).

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>Criar um registro MX

Neste exemplo, utilizamos o nome de conjunto de registos "\@" para criar o registo MX no vértice da zona (neste caso, "contoso.com").

```azurecli
az network dns record-set mx add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>Criar um registo NS

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-ns --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>Criar um registo PTR

Neste caso, "meu-arpa-My-arpa-Zone.com" representa a zona ARPA, que representa o seu intervalo de IP. Cada conjunto de registos PTR nesta zona corresponde a um endereço IP neste intervalo de IPs.  O nome do registo "10" é o último octeto do endereço IP neste intervalo de IP representado por este registo.

```azurecli
az network dns record-set ptr add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name my-arpa.zone.com --ptrdname myservice.contoso.com
```

### <a name="create-an-srv-record"></a>Criar um registo SRV

Ao criar uma [conjunto de registos SRV](dns-zones-records.md#srv-records), especifique a  *\_serviço* e  *\_protocolo* no nome do conjunto de registos. Não é necessário incluir "\@" no nome do conjunto de registos quando criar um registo SRV definido no vértice da zona.

```azurecli
az network dns record-set srv add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name _sip._tls --priority 10 --weight 5 --port 8080 --target sip.contoso.com
```

### <a name="create-a-txt-record"></a>Criar um registo TXT

O exemplo seguinte mostra como criar um registo TXT. Para obter mais informações sobre o comprimento da cadeia máximo suportado em registos TXT, consulte [registos TXT](dns-zones-records.md#txt-records).

```azurecli
az network dns record-set txt add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-txt --value "This is a TXT record"
```

## <a name="get-a-record-set"></a>Obter um conjunto de registos

Para obter um conjunto de registos existente, utilize `az network dns record-set <record-type> show`. Para obter ajuda, consulte `az network dns record-set <record-type> show --help`.

Ao criar um registo ou um conjunto de registos, o nome do conjunto de registos atribuído deve ter uma *relativo* nome, que significa que tem de excluir o nome da zona. Também tem de especificar o tipo de registo, a zona que contém o conjunto de registos e o grupo de recursos que contém a zona.

O exemplo seguinte obtém o registo *www* do tipo A partir da zona *contoso.com* no grupo de recursos *MyResourceGroup*:

```azurecli
az network dns record-set a show --resource-group myresourcegroup --zone-name contoso.com --name www
```

## <a name="list-record-sets"></a>Lista os conjuntos de registos

Pode listar todos os registos numa zona DNS ao utilizar o `az network dns record-set list` comando. Para obter ajuda, consulte `az network dns record-set list --help`.

Este exemplo retorna todos os registo define na zona *contoso.com*, no grupo de recursos *MyResourceGroup*, independentemente do nome ou tipo de registo:

```azurecli
az network dns record-set list --resource-group myresourcegroup --zone-name contoso.com
```

Este exemplo retorna todos os conjuntos de registos que correspondem o determinado tipo de registo (no caso, "A" registos):

```azurecli
az network dns record-set a list --resource-group myresourcegroup --zone-name contoso.com 
```

## <a name="add-a-record-to-an-existing-record-set"></a>Adicionar um registro a um conjunto de registos existente

Pode usar `az network dns record-set <record-type> add-record` ambos para criar um registo num novo conjunto de registos ou para adicionar um registo existente já um conjunto de registos.

Para obter mais informações, consulte [criar um registo DNS](#create-a-dns-record) e [criar registos de outros tipos de](#create-records-of-other-types) acima.

## <a name="remove-a-record-from-an-existing-record-set"></a>Remova um registo de um conjunto de registos existente.

Para remover um registo DNS de um conjunto de registos existente, utilize `az network dns record-set <record-type> remove-record`. Para obter ajuda, consulte `az network dns record-set <record-type> remove-record -h`.

Este comando elimina um registo DNS de um conjunto de registos. Se for eliminado o último registo num conjunto de registos, o conjunto próprio de registos também é eliminado. Para manter o registo vazio definido em vez disso, utilize o `--keep-empty-record-set` opção.

Tem de especificar o registo a ser eliminado e a zona deve ser eliminada, com os mesmos parâmetros ao criar um registo utilizando `az network dns record-set <record-type> add-record`. Esses parâmetros são descritos nas [criar um registo DNS](#create-a-dns-record) e [criar registos de outros tipos de](#create-records-of-other-types) acima.

O exemplo seguinte elimina o registo com o valor "1.2.3.4" do registo com o conjunto nomeado *www* na zona *contoso.com*, no grupo de recursos *MyResourceGroup*.

```azurecli
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "www" --ipv4-address 1.2.3.4
```

## <a name="modify-an-existing-record-set"></a>Modificar um conjunto de registos existente

Cada conjunto de registos contém um [time-to-live (TTL)](dns-zones-records.md#time-to-live), [metadados](dns-zones-records.md#tags-and-metadata)e registos DNS. As secções seguintes explicam como modificar cada uma dessas propriedades.

### <a name="to-modify-an-a-aaaa-caa-mx-ns-ptr-srv-or-txt-record"></a>Para modificar um registo A, AAAA, CAA, MX, NS, PTR, SRV ou TXT

Para modificar um registo existente de tipo A, AAAA, CAA, MX, NS, PTR, SRV ou TXT, deve primeiro adicionar um novo registo e, em seguida, elimine o registo existente. Para obter instruções detalhadas sobre como eliminar e adicionar registos, consulte as secções anteriores deste artigo.

O exemplo seguinte mostra como modificar um registo "A, de endereço IP 1.2.3.4 para o endereço IP 5.6.7.8":

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 5.6.7.8
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Não é possível adicionar, remover ou modificar os registros o criados automaticamente NS conjunto de registos no vértice da zona (`--Name "@"`, incluindo o entre aspas duplas). Para este conjunto de registos, as únicas alterações permitidas são para modificar o registo de definir o TTL e metadados.

### <a name="to-modify-a-cname-record"></a>Para modificar um registo CNAME

Ao contrário da maioria dos outros tipos de registo, um conjunto de registos CNAME só pode conter um único registo.  Por conseguinte, não é possível substituir o valor atual ao adicionar um novo registo e remover o registo existente, como para outros tipos de registo.

Em vez disso, para modificar um registo CNAME, utilize `az network dns record-set cname set-record`. Para obter ajuda, consulte `az network dns record-set cname set-record --help`

O exemplo modifica o conjunto de registos CNAME *www* na zona *contoso.com*, no grupo de recursos *MyResourceGroup*, para que apontem para "www.fabrikam.net" em vez de seu existente valor:

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>Para modificar um registo SOA

Ao contrário da maioria dos outros tipos de registo, um conjunto de registos CNAME só pode conter um único registo.  Por conseguinte, não é possível substituir o valor atual ao adicionar um novo registo e remover o registo existente, como para outros tipos de registo.

Em vez disso, para modificar o registo SOA, utilize `az network dns record-set soa update`. Para obter ajuda, consulte `az network dns record-set soa update --help`.

O exemplo seguinte mostra como definir a propriedade "email" do registo SOA da zona *contoso.com* no grupo de recursos *MyResourceGroup*:

```azurecli
az network dns record-set soa update --resource-group myresourcegroup --zone-name contoso.com --email admin.contoso.com
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Para modificar registos NS no vértice da zona

O conjunto no vértice da zona de registos NS é criado automaticamente com cada zona DNS. Contém os nomes dos servidores de nome DNS do Azure atribuídos à zona.

Pode adicionar servidores para este registo NS definidos, para oferecer suporte a domínios co-hospedagem com mais de um fornecedor DNS de nome adicionais. Também pode modificar o valor de TTL e os metadados para este conjunto de registos. No entanto, não é possível remover ou modificar os servidores de nomes DNS do Azure preenchidos previamente.

Tenha em atenção que isto aplica-se apenas para o conjunto no vértice da zona de registos NS. Outros conjuntos de registos NS na sua zona (como utilizado para delegar zonas subordinadas) podem ser modificados sem restrição.

O exemplo seguinte mostra como adicionar um servidor de nome adicionais para o conjunto no vértice da zona de registos NS:

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>Para modificar o valor de TTL de um conjunto de registos existente

Para modificar o valor de TTL de um conjunto de registos existente, utilize `azure network dns record-set <record-type> update`. Para obter ajuda, consulte `azure network dns record-set <record-type> update --help`.

O exemplo seguinte mostra como modificar um conjunto de registos de TTL, neste caso como 60 segundos:

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set ttl=60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>Para modificar os metadados de um conjunto de registos existente

[Metadados do conjunto de registos](dns-zones-records.md#tags-and-metadata) pode ser utilizado para associar dados específicos a cada conjunto de registos, como pares chave-valor. Para modificar os metadados de um conjunto de registos existente, utilize `az network dns record-set <record-type> update`. Para obter ajuda, consulte `az network dns record-set <record-type> update --help`.

O exemplo seguinte mostra como modificar um conjunto de registos com duas entradas de metadados, "departamento = finance" e "ambiente de produção de =". Tenha em atenção que quaisquer metadados existentes são *substituído* pelos valores tendo em conta.

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set metadata.dept=finance metadata.environment=production
```

## <a name="delete-a-record-set"></a>Eliminar um conjunto de registos

Conjuntos de registos podem ser eliminados ao utilizar o `az network dns record-set <record-type> delete` comando. Para obter ajuda, consulte `azure network dns record-set <record-type> delete --help`. Eliminar um conjunto de registos também elimina a todos os registos nesse conjunto de registos.

> [!NOTE]
> Não é possível eliminar a SOA e conjuntos de registos NS no vértice da zona (`--name "@"`).  Estes são criados automaticamente quando a zona foi criada e são eliminados automaticamente quando a zona for eliminada.

O exemplo seguinte elimina o conjunto nomeado de registos *www* do tipo A partir da zona *contoso.com* no grupo de recursos *MyResourceGroup*:

```azurecli
az network dns record-set a delete --resource-group myresourcegroup --zone-name contoso.com --name www
```

São-lhe pedido para confirmar a operação de eliminação. Para suprimir esta linha de comandos, utilize o `--yes` mudar.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [zonas e registos no DNS do Azure](dns-zones-records.md).
<br>
Saiba como [proteger suas zonas e registos](dns-protect-zones-recordsets.md) ao utilizar o DNS do Azure.
