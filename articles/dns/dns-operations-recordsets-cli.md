---
title: Gerenciar registros DNS no DNS do Azure usando o CLI do Azure | Microsoft Docs
description: Gerenciando registros e conjuntos de registros DNS no DNS do Azure ao hospedar seu domínio no DNS do Azure.
services: dns
documentationcenter: na
author: asudbring
manager: jeconnoc
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 05/15/2018
ms.author: allensu
ms.openlocfilehash: a0316710f78afc8810f5f65e108638b08fae3da2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74211641"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-the-azure-cli"></a>Gerenciar registros DNS e conjuntos de registros no DNS do Azure usando o CLI do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-operations-recordsets-portal.md)
> * [CLI do Azure](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Este artigo mostra como gerenciar registros DNS para sua zona DNS usando o CLI do Azure de plataforma cruzada, que está disponível para Windows, Mac e Linux. Você também pode gerenciar seus registros DNS usando [Azure PowerShell](dns-operations-recordsets.md) ou o [portal do Azure](dns-operations-recordsets-portal.md).

Os exemplos neste artigo pressupõem que você já [instalou o CLI do Azure, entrou e criou uma zona DNS](dns-operations-dnszones-cli.md).

## <a name="introduction"></a>Introdução

Antes de criar registos DNS no DNS do Azure, terá primeiro de compreender como o DNS do Azure organiza os registos DNS em conjuntos de registos DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Para obter mais informações sobre os registos DNS no DNS do Azure, veja [Zonas e registos DNS](dns-zones-records.md).

## <a name="create-a-dns-record"></a>Criar um registo DNS

Para criar um registro DNS, use o comando `az network dns record-set <record-type> add-record` (em que `<record-type>` é o tipo de registro, ou seja, a, SRV, txt, etc.) Para obter ajuda, consulte `az network dns record-set --help`.

Ao criar um registo, tem de especificar o nome do grupo de recursos, o nome da zona, o nome do conjunto de recursos, o tipo de registo e os detalhes do registo que está a ser criado. O nome do conjunto de registros fornecido deve ser um nome *relativo* , o que significa que ele deve excluir o nome da zona.

Se o conjunto de registos ainda não existir, este comando cria-o por si. Se o conjunto de registos já existir, este comando adiciona o registo que especificar ao conjunto de registos existente.

Se for criado um novo conjunto de registos, é utilizado um TTL (time-to-live) predefinido de 3600. Para obter instruções sobre como usar o TTLs diferente, consulte [criar um conjunto de registros DNS](#create-a-dns-record-set).

O exemplo seguinte cria um registo A denominado *www* na zona *contoso.com* do grupo de recursos *MyResourceGroup*. O endereço IP do registo A é *1.2.3.4*.

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Para criar um conjunto de registros no Apex da zona (nesse caso, "contoso.com"), use o nome do registro "\@", incluindo as aspas:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>Criar um conjunto de registros DNS

Nos exemplos acima, o registro DNS foi adicionado a um conjunto de registros existente ou o conjunto de registros foi criado *implicitamente*. Você também pode criar o conjunto de registros *explicitamente* antes de adicionar registros a ele. O DNS do Azure dá suporte a conjuntos de registros ' vazios ', que podem atuar como um espaço reservado para reservar um nome DNS antes de criar registros DNS. Os conjuntos de registros vazios são visíveis no plano de controle de DNS do Azure, mas não aparecem nos servidores de nome DNS do Azure.

Os conjuntos de registros são criados usando o comando `az network dns record-set <record-type> create`. Para obter ajuda, consulte `az network dns record-set <record-type> create --help`.

Criar o conjunto de registros explicitamente permite que você especifique propriedades do conjunto de registros, como o [TTL (vida útil)](dns-zones-records.md#time-to-live) e os metadados. Os [metadados do conjunto de registros](dns-zones-records.md#tags-and-metadata) podem ser usados para associar dados específicos do aplicativo a cada conjunto de registros, como pares chave-valor.

O exemplo a seguir cria um conjunto de registros vazio do tipo ' A ' com um TTL de 60 segundos, usando o parâmetro `--ttl` (forma abreviada `-l`):

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --ttl 60
```

O exemplo a seguir cria um conjunto de registros com duas entradas de metadados, "Dept = Finance" e "Environment = Production", usando o parâmetro `--metadata`:

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --metadata "dept=finance" "environment=production"
```

Com a criação de um conjunto de registros vazio, os registros podem ser adicionados usando `azure network dns record-set <record-type> add-record` conforme descrito em [criar um registro DNS](#create-a-dns-record).

## <a name="create-records-of-other-types"></a>Criar registros de outros tipos

Tendo visto em detalhes como criar registros ' A ', os exemplos a seguir mostram como criar um registro de outros tipos de registro com suporte pelo DNS do Azure.

Os parâmetros utilizados para especificar os dados de registo variam consoante o tipo de registo. Por exemplo, para um registo do tipo "A", especifica o endereço IPv4 com o parâmetro `--ipv4-address <IPv4 address>`. Os parâmetros para cada tipo de registro podem ser listados usando `az network dns record-set <record-type> add-record --help`.

Em cada caso, mostramos como criar um único registro. O registro é adicionado ao conjunto de registros existente ou um conjunto de registros criado implicitamente. Para obter mais informações sobre como criar conjuntos de registros e definir explicitamente o parâmetro do conjunto de registros, consulte [criar um conjunto de registros DNS](#create-a-dns-record-set).

Não damos um exemplo para criar um conjunto de registros SOA, já que SOAs são criados e excluídos com cada zona DNS e não podem ser criados ou excluídos separadamente. No entanto, [a soa pode ser modificada, conforme mostrado em um exemplo posterior](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record"></a>Criar um registro AAAA

```azurecli
az network dns record-set aaaa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-aaaa --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-an-caa-record"></a>Criar um registro CAA

```azurecli
az network dns record-set caa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-caa --flags 0 --tag "issue" --value "ca1.contoso.com"
```

### <a name="create-a-cname-record"></a>Criar um registro CNAME

> [!NOTE]
> Os padrões de DNS não permitem registros CNAME no Apex de uma zona (`--Name "@"`), nem permitem conjuntos de registros que contenham mais de um registro.
> 
> Para obter mais informações, consulte [CNAME Records](dns-zones-records.md#cname-records).

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>Criar um registro MX

Neste exemplo, usamos o nome do conjunto de registros "\@" para criar o registro MX no Apex da zona (neste caso, "contoso.com").

```azurecli
az network dns record-set mx add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>Criar um registro NS

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-ns --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>Criar um registro PTR

Nesse caso, ' my-arpa-zone.com ' representa a zona ARPA que representa o intervalo de IP. Cada conjunto de registos PTR nesta zona corresponde a um endereço IP neste intervalo de IPs.  O nome do registro ' 10 ' é o último octeto do endereço IP nesse intervalo de IP representado por esse registro.

```azurecli
az network dns record-set ptr add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name my-arpa.zone.com --ptrdname myservice.contoso.com
```

### <a name="create-an-srv-record"></a>Criar um registro SRV

Ao criar um [conjunto de registros SRV](dns-zones-records.md#srv-records), especifique o *serviço de\_* e o *protocolo\_* no nome do conjunto de registros. Não é necessário incluir "\@" no nome do conjunto de registros ao criar um conjunto de registros SRV definido no Apex da zona.

```azurecli
az network dns record-set srv add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name _sip._tls --priority 10 --weight 5 --port 8080 --target sip.contoso.com
```

### <a name="create-a-txt-record"></a>Criar um registro TXT

O exemplo a seguir mostra como criar um registro TXT. Para obter mais informações sobre o comprimento máximo de cadeia de caracteres com suporte em registros TXT, consulte [registros txt](dns-zones-records.md#txt-records).

```azurecli
az network dns record-set txt add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-txt --value "This is a TXT record"
```

## <a name="get-a-record-set"></a>Obter um conjunto de registros

Para recuperar um conjunto de registros existente, use `az network dns record-set <record-type> show`. Para obter ajuda, consulte `az network dns record-set <record-type> show --help`.

Como ao criar um registro ou conjunto de registros, o nome do conjunto de registros fornecido deve ser um nome *relativo* , o que significa que ele deve excluir o nome da zona. Você também precisa especificar o tipo de registro, a zona que contém o conjunto de registros e o grupo de recursos que contém a zona.

O exemplo a seguir recupera o registro *www* do tipo A da zona *contoso.com* no grupo de recursos *MyResource*Group:

```azurecli
az network dns record-set a show --resource-group myresourcegroup --zone-name contoso.com --name www
```

## <a name="list-record-sets"></a>Listar conjuntos de registros

Você pode listar todos os registros em uma zona DNS usando o comando `az network dns record-set list`. Para obter ajuda, consulte `az network dns record-set list --help`.

Este exemplo retorna todos os conjuntos de registros na zona *contoso.com*, no grupo de recursos *MyResource*Group, independentemente do nome ou tipo de registro:

```azurecli
az network dns record-set list --resource-group myresourcegroup --zone-name contoso.com
```

Este exemplo retorna todos os conjuntos de registros que correspondem ao tipo de registro determinado (nesse caso, registros ' A '):

```azurecli
az network dns record-set a list --resource-group myresourcegroup --zone-name contoso.com 
```

## <a name="add-a-record-to-an-existing-record-set"></a>Adicionar um registro a um conjunto de registros existente

Você pode usar `az network dns record-set <record-type> add-record` tanto para criar um registro em um novo conjunto de registros quanto para adicionar um registro a um conjunto de registros existente.

Para obter mais informações, consulte [criar um registro DNS](#create-a-dns-record) e [criar registros de outros tipos](#create-records-of-other-types) acima.

## <a name="remove-a-record-from-an-existing-record-set"></a>Remover um registro de um conjunto de registros existente.

Para remover um registro DNS de um conjunto de registros existente, use `az network dns record-set <record-type> remove-record`. Para obter ajuda, consulte `az network dns record-set <record-type> remove-record -h`.

Este comando exclui um registro DNS de um conjunto de registros. Se o último registro em um conjunto de registros for excluído, o próprio conjunto de registros também será excluído. Para manter o conjunto de registros vazio em vez disso, use a opção `--keep-empty-record-set`.

Você precisa especificar o registro a ser excluído e a zona da qual ele deve ser excluído, usando os mesmos parâmetros que ao criar um registro usando `az network dns record-set <record-type> add-record`. Esses parâmetros são descritos em [criar um registro DNS](#create-a-dns-record) e [criar registros de outros tipos](#create-records-of-other-types) acima.

O exemplo a seguir exclui o registro a com o valor ' 1.2.3.4 ' do conjunto de registros denominado *www* na zona *contoso.com*, no grupo de recursos *MyResource*Group.

```azurecli
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "www" --ipv4-address 1.2.3.4
```

## <a name="modify-an-existing-record-set"></a>Modificar um conjunto de registros existente

Cada conjunto de registros contém um [tempo de vida (TTL)](dns-zones-records.md#time-to-live), [metadados](dns-zones-records.md#tags-and-metadata)e registros DNS. As seções a seguir explicam como modificar cada uma dessas propriedades.

### <a name="to-modify-an-a-aaaa-caa-mx-ns-ptr-srv-or-txt-record"></a>Para modificar um registro A, AAAA, CAA, MX, NS, PTR, SRV ou TXT

Para modificar um registro existente do tipo A, AAAA, CAA, MX, NS, PTR, SRV ou TXT, primeiro você deve adicionar um novo registro e, em seguida, excluir o registro existente. Para obter instruções detalhadas sobre como excluir e adicionar registros, consulte as seções anteriores deste artigo.

O exemplo a seguir mostra como modificar um registro ' A ', do endereço IP 1.2.3.4 para o endereço IP 5.6.7.8:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 5.6.7.8
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Você não pode adicionar, remover ou modificar os registros no conjunto de registros NS criado automaticamente no Apex da zona (`--Name "@"`, incluindo aspas). Para esse conjunto de registros, as únicas alterações permitidas são modificar o TTL do conjunto de registros e os metadados.

### <a name="to-modify-a-cname-record"></a>Para modificar um registro CNAME

Ao contrário da maioria dos outros tipos de registro, um conjunto de registros CNAME pode conter apenas um único registro.  Portanto, você não pode substituir o valor atual adicionando um novo registro e removendo o registro existente, como para outros tipos de registro.

Em vez disso, para modificar um registro CNAME, use `az network dns record-set cname set-record`. Para obter ajuda, consulte `az network dns record-set cname set-record --help`

O exemplo modifica o conjunto de registros CNAME *www* na zona *contoso.com*, no grupo de recursos *MyResource*group, para apontar para ' www.fabrikam.net ' em vez de seu valor existente:

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>Para modificar um registro SOA

Ao contrário da maioria dos outros tipos de registro, um conjunto de registros CNAME pode conter apenas um único registro.  Portanto, você não pode substituir o valor atual adicionando um novo registro e removendo o registro existente, como para outros tipos de registro.

Em vez disso, para modificar o registro SOA, use `az network dns record-set soa update`. Para obter ajuda, consulte `az network dns record-set soa update --help`.

O exemplo a seguir mostra como definir a propriedade ' email ' do registro SOA para a zona *contoso.com* no grupo de recursos *MyResource*Group:

```azurecli
az network dns record-set soa update --resource-group myresourcegroup --zone-name contoso.com --email admin.contoso.com
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Para modificar os registros NS no Apex da zona

O conjunto de registros NS no Apex da zona é criado automaticamente com cada zona DNS. Ele contém os nomes dos servidores de nome DNS do Azure atribuídos à zona.

Você pode adicionar servidores de nomes adicionais a esse conjunto de registros NS para dar suporte a domínios de hospedagem com mais de um provedor de DNS. Você também pode modificar o TTL e os metadados para este conjunto de registros. No entanto, não é possível remover ou modificar os servidores de nomes DNS do Azure populados previamente.

Observe que isso se aplica somente ao conjunto de registros NS no Apex da zona. Outros conjuntos de registros NS em sua zona (como usados para delegar zonas filhas) podem ser modificados sem restrição.

O exemplo a seguir mostra como adicionar um servidor de nome adicional ao conjunto de registros NS no Apex da zona:

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>Para modificar a TTL de um conjunto de registros existente

Para modificar a TTL de um conjunto de registros existente, use `azure network dns record-set <record-type> update`. Para obter ajuda, consulte `azure network dns record-set <record-type> update --help`.

O exemplo a seguir mostra como modificar um TTL do conjunto de registros, neste caso, para 60 segundos:

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set ttl=60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>Para modificar os metadados de um conjunto de registros existente

Os [metadados do conjunto de registros](dns-zones-records.md#tags-and-metadata) podem ser usados para associar dados específicos do aplicativo a cada conjunto de registros, como pares chave-valor. Para modificar os metadados de um conjunto de registros existente, use `az network dns record-set <record-type> update`. Para obter ajuda, consulte `az network dns record-set <record-type> update --help`.

O exemplo a seguir mostra como modificar um conjunto de registros com duas entradas de metadados, "Dept = Finance" e "Environment = Production". Observe que todos os metadados existentes são *substituídos* pelos valores fornecidos.

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set metadata.dept=finance metadata.environment=production
```

## <a name="delete-a-record-set"></a>Excluir um conjunto de registros

Os conjuntos de registros podem ser excluídos usando o comando `az network dns record-set <record-type> delete`. Para obter ajuda, consulte `azure network dns record-set <record-type> delete --help`. Excluir um conjunto de registros também exclui todos os registros dentro do conjunto de registros.

> [!NOTE]
> Não é possível excluir os conjuntos de registros SOA e NS no Apex da zona (`--name "@"`).  Eles são criados automaticamente quando a zona foi criada e são excluídos automaticamente quando a zona é excluída.

O exemplo a seguir exclui o conjunto de registros denominado *www* do tipo a da zona *contoso.com* no grupo de recursos *MyResource*Group:

```azurecli
az network dns record-set a delete --resource-group myresourcegroup --zone-name contoso.com --name www
```

Você será solicitado a confirmar a operação de exclusão. Para suprimir esse prompt, use a opção `--yes`.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [zonas e registros no DNS do Azure](dns-zones-records.md).
<br>
Saiba como [proteger suas zonas e registros](dns-protect-zones-recordsets.md) ao usar o DNS do Azure.
