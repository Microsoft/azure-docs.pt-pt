---
title: Importar e exportar um ficheiro de zona do domínio ao DNS do Azure com a CLI do Azure | Documentos da Microsoft
description: Saiba como importar e exportar um ficheiro de zona DNS ao DNS do Azure com CLI do Azure
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.assetid: f5797782-3005-4663-a488-ac0089809010
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: victorh
ms.openlocfilehash: d41ad3232fef57d1008f1e15d5d7d5ee1e106e9b
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57312654"
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>Importar e exportar um ficheiro de zona DNS com a CLI do Azure 

Este artigo explica como importar e exportar ficheiros de zona DNS para o Azure DNS com a CLI do Azure.

## <a name="introduction-to-dns-zone-migration"></a>Introdução à migração de zona DNS

Um ficheiro de zona DNS é um arquivo de texto que contém os detalhes de todos os registos de sistema de nomes de domínio (DNS) na zona. Ela segue um formato padrão, tornando-o adequado para a transferência de registos DNS entre sistemas DNS. Através de um ficheiro de zona é uma forma rápida, fiável e conveniente para transferir uma zona DNS dentro ou fora do DNS do Azure.

O DNS do Azure oferece suporte a importar e exportar ficheiros de zona ao utilizar a interface de linha de comandos (CLI) do Azure. Importação do ficheiro de zona está **não** atualmente suportadas através do Azure PowerShell ou o portal do Azure.

A CLI do Azure é uma ferramenta de linha de comandos para várias plataformas utilizada para gerir serviços do Azure. Está disponível para as plataformas Windows, Mac e Linux a partir da [página de downloads do Azure](https://azure.microsoft.com/downloads/). Suporte de várias plataformas é importante para importar e exportar ficheiros de zona, porque o software de servidor de nome mais comuns, [VINCULAR](https://www.isc.org/downloads/bind/), normalmente, é executada no Linux.


## <a name="obtain-your-existing-dns-zone-file"></a>Obter o ficheiro de zona DNS existente

Antes de importar um ficheiro de zona DNS no DNS do Azure, terá de obter uma cópia do ficheiro de zona. A origem deste ficheiro depende de onde se encontra atualmente hospedada a zona DNS.

* Se a sua zona DNS é alojada por um serviço do parceiro (por exemplo, um registo de domínios, o fornecedor de alojamento DNS dedicado ou o fornecedor de cloud alternativo), esse serviço deve fornecer a capacidade de transferir o ficheiro de zona DNS.
* Se a sua zona DNS está alojada no DNS do Windows, a pasta predefinida para os ficheiros de zona é **%systemroot%\system32\dns**. O caminho completo para cada ficheiro de zona também mostra a **gerais** separador da consola DNS.
* Se a sua zona DNS é hospedada pelo utilizando a ligação, a localização do ficheiro de zona para cada zona está especificada no ficheiro de configuração de ENLACE **named.conf**.

> [!NOTE]
> Ficheiros de zona transferidos a partir da GoDaddy tem um formato ligeiramente fora do padrão. Precisa corrigir este problema antes de importar esses arquivos de zona no DNS do Azure.
>
> Os nomes DNS no RDATA de cada registo DNS são especificados como nomes totalmente qualificados, mas não precisam se acabar "." Isso significa que eles são interpretados por outros sistemas DNS como nomes relativos. Tem de editar o ficheiro de zona para acrescentar a terminação "." para os respetivos nomes, antes de importá-los para o DNS do Azure.
>
> Por exemplo, o registo CNAME "www 3600 em CNAME contoso.com" deve ser alterado para "www" 3600 em CNAME contoso.com.
> (com acabar ".").

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Importar um ficheiro de zona DNS para o DNS do Azure

Importar um ficheiro de zona cria uma nova zona no DNS do Azure, se ainda não existir. Se a zona já existir, os conjuntos de registos no ficheiro de zona devem ser mesclados com conjuntos de registos existentes.

### <a name="merge-behavior"></a>Comportamento de intercalação

* Por predefinição, os conjuntos de registos de novos e existentes são mesclados. Os registos idênticos dentro de um conjunto de registos Unido são anular duplicados.
* Quando são mesclados conjuntos de registos, é utilizada a hora para live (TTL) preexistentes de conjuntos de registos.
* Início de parâmetros de autoridade (SOA) (exceto `host`) sempre são obtidas a partir do ficheiro de zona importados. Da mesma forma, para o nome servidor conjunto de registos no vértice da zona, o valor de TTL é sempre tirada do ficheiro de zona importados.
* Um registo CNAME importado não substitui um registo CNAME existente com o mesmo nome.  
* Quando surge um conflito entre um registo CNAME e a outro registo do mesmo nome mas um tipo diferente (independentemente de qual é a existente ou nova), o registo existente é mantido. 

### <a name="additional-information-about-importing"></a>Informações adicionais sobre a importação

As notas de seguintes fornecem detalhes técnicos adicionais sobre a zona de importar o processo.

* O `$TTL` diretiva é opcional e é suportado. Quando não existe nenhum `$TTL` diretiva é fornecida, os registos sem um valor de TTL explícita são importado definido como um TTL predefinido de 3600 segundos. Quando dois registos no mesmo conjunto de registo especificar TTLs diferentes, é utilizado o valor mais baixo.
* O `$ORIGIN` diretiva é opcional e é suportado. Quando não existe nenhum `$ORIGIN` é definido, o valor predefinido utilizado é o nome da zona conforme especificado na linha de comando (além da terminação ".").
* O `$INCLUDE` e `$GENERATE` diretivas não são suportadas.
* Estes tipos de registo são suportados: A, AAAA, CAA, CNAME, MX, NS, SOA, SRV, and TXT.
* O registo SOA é criado automaticamente pelo DNS do Azure quando é criada uma zona. Quando importa um ficheiro de zona, todos os parâmetros SOA são obtidos a partir do ficheiro de zona *, exceto* o `host` parâmetro. Este parâmetro utiliza o valor fornecido pelo DNS do Azure. Isso é porque este parâmetro tem de referenciar o nome principal do servidor fornecido pelo DNS do Azure.
* Também, o nome servidor conjunto de registos no vértice da zona é criado automaticamente pelo DNS do Azure quando a zona é criada. Apenas o valor de TTL deste conjunto de registos é importado. Estes registos incluem os nomes de servidor de nome fornecidos pelo DNS do Azure. Os dados de registo não são substituídos pelos valores contidos no ficheiro de zona importados.
* Durante a pré-visualização pública, o DNS do Azure suporta apenas os registos TXT cadeia única. Os registos TXT multistring são ser concatenado e truncados de 255 carateres.

### <a name="cli-format-and-values"></a>Formato CLI e valores

O formato do comando da CLI do Azure para importar uma zona DNS é:

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Valores:

* `<resource group>` é o nome do grupo de recursos para a zona no DNS do Azure.
* `<zone name>` é o nome da zona.
* `<zone file name>` é o caminho/nome do ficheiro de zona para ser importado.

Se uma zona com este nome não existe no grupo de recursos, é criada para. Se a zona já existir, conjuntos de registos de importados são mesclados com conjuntos de registos existentes. 


### <a name="step-1-import-a-zone-file"></a>Passo 1. Importar um ficheiro de zona

Para importar um ficheiro de zona para a zona **contoso.com**.

1. Se ainda não tiver um, terá de criar um grupo de recursos do Resource Manager.

    ```azurecli
    az group create --group myresourcegroup -l westeurope
    ```

2. Para importar a zona **contoso.com** do ficheiro **contoso.com.txt** numa nova zona DNS no grupo de recursos **myresourcegroup**, irá executar o comando `az network dns zone import` .<BR>Este comando carrega o ficheiro de zona e o analisa. O comando executa uma série de comandos no serviço para criar a zona DNS do Azure e conjuntos de todo o registo na zona. O progresso de relatórios de comando na janela da consola, juntamente com quaisquer erros ou avisos. Uma vez que os conjuntos de registos são criados em série, poderá demorar alguns minutos para importar um ficheiro de zona grandes.

    ```azurecli
    az network dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>Passo 2. Certifique-se a zona

Para verificar a zona DNS depois de importar o ficheiro, pode utilizar qualquer um dos seguintes métodos:

* Pode listar os registos ao utilizar o seguinte comando da CLI do Azure:

    ```azurecli
    az network dns record-set list -g myresourcegroup -z contoso.com
    ```

* Pode listar os registos com o comando da CLI do Azure `az network dns record-set ns list`.
* Pode usar `nslookup` para verificar a resolução de nomes para os registos. Uma vez que a zona não é delegada ainda, terá de especificar explicitamente os servidores de nomes DNS do Azure corretos. O exemplo a seguir mostra como recuperar os nomes dos servidores de nome atribuídos à zona. Isso mostra também como consultar o registo "www" ao utilizar `nslookup`.

    ```azurecli
    az network dns record-set ns list -g myresourcegroup -z contoso.com  --output json 
    ```

    ```json
    [
      {
       .......
       "name": "@",
        "nsRecords": [
          {
            "additionalProperties": {},
            "nsdname": "ns1-03.azure-dns.com."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns2-03.azure-dns.net."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns3-03.azure-dns.org."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns4-03.azure-dns.info."
          }
        ],
        "resourceGroup": "myresourcegroup",
        "ttl": 86400,
        "type": "Microsoft.Network/dnszones/NS"
      }
    ]
    ```

    ```cmd
    nslookup www.contoso.com ns1-03.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221
    ```

### <a name="step-3-update-dns-delegation"></a>Passo 3. Atualizar delegação de DNS

Após ter verificado que a zona foram importada corretamente, tem de atualizar a delegação de DNS para apontar para os servidores de nomes do DNS do Azure. Para obter mais informações, consulte o artigo [atualizar a delegação de DNS](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Exportar um ficheiro de zona DNS do DNS do Azure

O formato do comando da CLI do Azure para importar uma zona DNS é:

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

Valores:

* `<resource group>` é o nome do grupo de recursos para a zona no DNS do Azure.
* `<zone name>` é o nome da zona.
* `<zone file name>` é o caminho/nome do ficheiro de zona ser exportada.

Como com a importação de zona, primeiro precisa para iniciar sessão, escolha a sua subscrição e configurar a CLI do Azure para utilizar o modo do Resource Manager.

### <a name="to-export-a-zone-file"></a>Para exportar um ficheiro de zona

Para exportar a zona DNS do Azure existente **contoso.com** no grupo de recursos **myresourcegroup** para o ficheiro **contoso.com.txt** (na pasta atual), execute `azure network dns zone export`. Esse comando chama o serviço de DNS do Azure para enumerar conjuntos de registos na zona e exportar os resultados para um ficheiro de zona compatível com o ENLACE.

```
az network dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
```
