---
title: Importar e exportar um arquivo de zona de domínio-CLI do Azure
titleSuffix: Azure DNS
description: Saiba como importar e exportar um arquivo de zona DNS para o DNS do Azure usando CLI do Azure
services: dns
author: asudbring
ms.service: dns
ms.date: 4/3/2019
ms.author: allensu
ms.topic: conceptual
ms.openlocfilehash: 036486ed15c9d6502b5e1655bdab4643128bca4b
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082906"
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>Importar e exportar um arquivo de zona DNS usando o CLI do Azure

Este artigo explica como importar e exportar arquivos de zona DNS para o DNS do Azure usando o CLI do Azure.

## <a name="introduction-to-dns-zone-migration"></a>Introdução à migração de zona DNS

Um arquivo de zona DNS é um arquivo de texto que contém detalhes de cada registro DNS (sistema de nomes de domínio) na zona. Ele segue um formato padrão, tornando-o adequado para a transferência de registros DNS entre sistemas DNS. O uso de um arquivo de zona é uma maneira rápida, confiável e conveniente de transferir uma zona DNS para dentro ou fora do DNS do Azure.

O DNS do Azure dá suporte à importação e exportação de arquivos de zona usando a CLI (interface de linha de comando) do Azure. No momento, **não** há suporte para importação de arquivo de zona via Azure PowerShell ou portal do Azure.

O CLI do Azure é uma ferramenta de linha de comando de plataforma cruzada usada para gerenciar os serviços do Azure. Ele está disponível para as plataformas Windows, Mac e Linux na página de [downloads do Azure](https://azure.microsoft.com/downloads/). O suporte a várias plataformas é importante para importar e exportar arquivos de zona, pois o software de servidor de nomes mais comum, o [BIND](https://www.isc.org/downloads/bind/), normalmente é executado no Linux.

## <a name="obtain-your-existing-dns-zone-file"></a>Obter o arquivo de zona DNS existente

Antes de importar um arquivo de zona DNS para o DNS do Azure, você precisa obter uma cópia do arquivo de zona. A origem desse arquivo depende de onde a zona DNS está hospedada no momento.

* Se a zona DNS for hospedada por um serviço de parceiro (como um registrador de domínio, provedor de Hospedagem de DNS dedicado ou provedor de nuvem alternativo), esse serviço deverá fornecer a capacidade de baixar o arquivo de zona DNS.
* Se a zona DNS estiver hospedada no DNS do Windows, a pasta padrão para os arquivos de zona será **%systemroot%\System32\Dns**. O caminho completo para cada arquivo de zona também é mostrado na guia **geral** do console DNS.
* Se a zona DNS for hospedada usando o BIND, o local do arquivo de zona para cada zona será especificado no arquivo de configuração de associação **chamado. conf**.

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Importar um arquivo de zona DNS para o DNS do Azure

A importação de um arquivo de zona cria uma nova zona no DNS do Azure, caso ainda não exista uma. Se a zona já existir, os conjuntos de registros no arquivo de zona deverão ser mesclados com os conjuntos de registros existentes.

### <a name="merge-behavior"></a>Comportamento de mesclagem

* Por padrão, os conjuntos de registros novos e existentes são mesclados. Registros idênticos em um conjunto de registros mesclados são eliminados de duplicação.
* Quando os conjuntos de registros são mesclados, a TTL (vida útil) de conjuntos de registros preexistentes é usada.
* Os parâmetros da SOA (início de autoridade) (exceto `host`) são sempre obtidos do arquivo de zona importado. Da mesma forma, para o registro de servidor de nomes definido no Apex da zona, o TTL é sempre extraído do arquivo de zona importado.
* Um registro CNAME importado não substitui um registro CNAME existente com o mesmo nome.  
* Quando ocorre um conflito entre um registro CNAME e outro registro com o mesmo nome, mas com tipo diferente (independentemente de que seja existente ou novo), o registro existente é retido. 

### <a name="additional-information-about-importing"></a>Informações adicionais sobre como importar

As observações a seguir fornecem detalhes técnicos adicionais sobre o processo de importação de zona.

* A diretiva `$TTL` é opcional e tem suporte. Quando nenhuma diretiva de `$TTL` é fornecida, os registros sem um TTL explícito são importados definidos como um TTL padrão de 3600 segundos. Quando dois registros no mesmo conjunto de registros especificam TTLs diferentes, o valor mais baixo é usado.
* A diretiva `$ORIGIN` é opcional e tem suporte. Quando nenhum `$ORIGIN` é definido, o valor padrão usado é o nome da zona, conforme especificado na linha de comando (mais o "." de encerramento).
* Não há suporte para as diretivas `$INCLUDE` e `$GENERATE`.
* Esses tipos de registro têm suporte: A, AAAA, CAA, CNAME, MX, NS, SOA, SRV e TXT.
* O registro SOA é criado automaticamente pelo DNS do Azure quando uma zona é criada. Quando você importa um arquivo de zona, todos os parâmetros SOA são obtidos do arquivo de zona, *exceto* o parâmetro `host`. Esse parâmetro usa o valor fornecido pelo DNS do Azure. Isso ocorre porque esse parâmetro deve se referir ao servidor de nomes primário fornecido pelo DNS do Azure.
* O conjunto de registros do servidor de nomes no Apex da zona também é criado automaticamente pelo DNS do Azure quando a zona é criada. Somente a TTL desse conjunto de registros é importada. Esses registros contêm os nomes de servidor de nomes fornecidos pelo DNS do Azure. Os dados de registro não são substituídos pelos valores contidos no arquivo de zona importado.
* Durante a visualização pública, o DNS do Azure dá suporte apenas a registros TXT de cadeia de caracteres única. Os registros TXT de cadeia de caracteres são concatenados e truncados para 255 caracteres.

### <a name="cli-format-and-values"></a>Formato e valores da CLI

O formato do comando de CLI do Azure para importar uma zona DNS é:

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Valores:

* `<resource group>` é o nome do grupo de recursos para a zona no DNS do Azure.
* `<zone name>` é o nome da zona.
* `<zone file name>` é o caminho/nome do arquivo de zona a ser importado.

Se uma zona com esse nome não existir no grupo de recursos, ela será criada para você. Se a zona já existir, os conjuntos de registros importados serão mesclados com os conjuntos de registros existentes. 

### <a name="step-1-import-a-zone-file"></a>Passo 1. Importar um arquivo de zona

Para importar um arquivo de zona para a zona **contoso.com**.

1. Se você ainda não tiver um, precisará criar um grupo de recursos do Resource Manager.

    ```azurecli
    az group create --group myresourcegroup -l westeurope
    ```

2. Para importar a zona **contoso.com** do arquivo **contoso. com. txt** para uma nova zona DNS no grupo de recursos **MyResource**Group, você executará o comando `az network dns zone import`.<BR>Esse comando carrega o arquivo de zona e o analisa. O comando executa uma série de comandos no serviço DNS do Azure para criar a zona e todos os conjuntos de registros na zona. O comando relata o progresso na janela do console, juntamente com quaisquer erros ou avisos. Como os conjuntos de registros são criados em série, pode levar alguns minutos para importar um arquivo de zona grande.

    ```azurecli
    az network dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>Passo 2. Verificar a zona

Para verificar a zona DNS depois de importar o arquivo, você pode usar qualquer um dos seguintes métodos:

* Você pode listar os registros usando o seguinte comando de CLI do Azure:

    ```azurecli
    az network dns record-set list -g myresourcegroup -z contoso.com
    ```

* Você pode listar os registros usando o comando CLI do Azure `az network dns record-set ns list`.
* Você pode usar `nslookup` para verificar a resolução de nomes dos registros. Como a zona ainda não está delegada, você precisa especificar os servidores de nome DNS do Azure corretos explicitamente. O exemplo a seguir mostra como recuperar os nomes de servidor de nomes atribuídos à zona. Isso também mostra como consultar o registro "www" usando `nslookup`.

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

### <a name="step-3-update-dns-delegation"></a>Passo 3: Atualizar delegação de DNS

Depois de verificar se a zona foi importada corretamente, você precisa atualizar a delegação de DNS para apontar para os servidores de nome DNS do Azure. Para obter mais informações, consulte o artigo [atualizar a delegação de DNS](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Exportar um arquivo de zona DNS do DNS do Azure

O formato do comando de CLI do Azure para exportar uma zona DNS é:

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

Valores:

* `<resource group>` é o nome do grupo de recursos para a zona no DNS do Azure.
* `<zone name>` é o nome da zona.
* `<zone file name>` é o caminho/nome do arquivo de zona a ser exportado.

Assim como acontece com a importação de zona, primeiro você precisa entrar, escolher sua assinatura e configurar o CLI do Azure para usar o modo do Gerenciador de recursos.

### <a name="to-export-a-zone-file"></a>Para exportar um arquivo de zona

Para exportar a zona DNS do Azure existente **contoso.com** no grupo de recursos **MyResource** Group para o arquivo **contoso. com. txt** (na pasta atual), execute `azure network dns zone export`. Esse comando chama o serviço DNS do Azure para enumerar conjuntos de registros na zona e exportar os resultados para um arquivo de zona compatível com associação.

```
az network dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
```

## <a name="next-steps"></a>Passos seguintes

* Aprenda a [gerenciar conjuntos de registros e registros](dns-getstarted-create-recordset-cli.md) em sua zona DNS.

* Saiba como [delegar seu domínio ao DNS do Azure](dns-domain-delegation.md).
