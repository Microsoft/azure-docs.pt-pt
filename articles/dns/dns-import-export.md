---
title: Importar e exportar um ficheiro de zona de domínio - Azure CLI
titleSuffix: Azure DNS
description: Saiba como importar e exportar um ficheiro de zona DNS para O Azure DNS utilizando o Azure CLI
services: dns
author: rohinkoul
ms.service: dns
ms.date: 7/30/2020
ms.author: rohink
ms.topic: how-to
ms.openlocfilehash: e2b998432f6c4417da0242d86347ed43acb5071a
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968235"
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>Importar e exportar um ficheiro de zona DNS com a CLI do Azure

Este artigo explica-lhe como importar e exportar ficheiros de zonas DNS para O Azure DNS usando o Azure CLI.

## <a name="introduction-to-dns-zone-migration"></a>Introdução à migração da zona de DNS

Um ficheiro de zona DNS é um ficheiro de texto que contém detalhes de todos os registos do Sistema de Nome de Domínio (DNS) na zona. Segue um formato padrão, tornando-o adequado para a transferência de registos DNS entre sistemas DNS. A utilização de um ficheiro de zona é uma forma rápida, fiável e conveniente de transferir uma zona DNS para dentro ou para fora do Azure DNS.

O Azure DNS suporta a importação e exportação de ficheiros de zonas utilizando a interface de linha de comando Azure (CLI). A importação de ficheiros de **zona não** é suportada atualmente através do Azure PowerShell ou do portal Azure.

O Azure CLI é uma ferramenta de linha de comandos de plataforma cruzada utilizada para gerir os serviços Azure. Está disponível para as plataformas Windows, Mac e Linux a partir da página de downloads do [Azure.](https://azure.microsoft.com/downloads/) O suporte de plataformas cruzadas é importante para importar e exportar ficheiros de zonas, porque o software de servidor de nome mais comum, [o BIND,](https://www.isc.org/downloads/bind/)normalmente funciona no Linux.

## <a name="obtain-your-existing-dns-zone-file"></a>Obtenha o seu ficheiro de zona DNS existente

Antes de importar um ficheiro de zona DNS para O Azure DNS, precisa de obter uma cópia do ficheiro da zona. A origem deste ficheiro depende de onde a zona de DNS está atualmente hospedada.

* Se a sua zona DE DNS for hospedada por um serviço de parceiros (como um registrador de domínio, um fornecedor de hospedagem de DNS dedicado ou um fornecedor de nuvem alternativa), esse serviço deverá fornecer a capacidade de descarregar o ficheiro da zona DNS.
* Se a sua zona DNS estiver hospedada no DNS do Windows, a pasta predefinida para os ficheiros da zona é **%systemroot%\system32\dns**. O percurso completo de cada ficheiro de zona também aparece no separador **Geral** da consola DNS.
* Se a sua zona DNS estiver hospedada utilizando o BIND, a localização do ficheiro de zona para cada zona é especificada no ficheiro de configuração BIND **denominado.conf**.

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Importe um ficheiro de zona DNS para O Azure DNS

Importar um ficheiro de zona cria uma nova zona em Azure DNS se já não existir. Se a zona já existir, os recordes no ficheiro de zona devem ser fundidos com os conjuntos de registos existentes.

### <a name="merge-behavior"></a>Fusão de comportamento

* Por padrão, os conjuntos de discos existentes e novos são fundidos. Os registos idênticos dentro de um conjunto de registos fundidos são desprovados.
* Quando os recordes são fundidos, é utilizado o tempo de vida (TTL) dos conjuntos de discos pré-existentes.
* Os parâmetros de início da Autoridade (SOA) `host` (exceto) são sempre retirados do ficheiro da zona importada. Da mesma forma, para o registo do servidor de nomes estabelecido no ápice da zona, o TTL é sempre retirado do ficheiro de zona importada.
* Um registo CNAME importado não substitui um registo CNAME existente com o mesmo nome.  
* Quando um conflito surge entre um registo CNAME e outro registo com o mesmo nome, mas tipo diferente (independentemente do que exista ou seja novo), o registo existente é mantido. 

### <a name="additional-information-about-importing"></a>Informações adicionais sobre a importação

As seguintes notas fornecem dados técnicos adicionais sobre o processo de importação de zonas.

* A `$TTL` diretiva é facultativa e é apoiada. Quando não é dada nenhuma `$TTL` diretiva, os registos sem um TTL explícito são importados definidos para um TTL padrão de 3600 segundos. Quando dois registos no mesmo conjunto de registos especificam TTLs diferentes, o valor mais baixo é usado.
* A `$ORIGIN` diretiva é facultativa e é apoiada. Quando não `$ORIGIN` é definido, o valor predefinido utilizado é o nome da zona conforme especificado na linha de comando (mais o ".").
* As `$INCLUDE` `$GENERATE` diretivas e as diretivas não são apoiadas.
* Estes tipos de registo são suportados: A, AAAA, CAA, CNAME, MX, NS, SOA, SRV e TXT.
* O registo SOA é criado automaticamente pelo Azure DNS quando uma zona é criada. Quando importa um ficheiro de zona, todos os parâmetros SOA são retirados do ficheiro da *zona, exceto* o `host` parâmetro. Este parâmetro utiliza o valor fornecido pelo Azure DNS. Isto porque este parâmetro deve referir-se ao servidor de nome primário fornecido pelo Azure DNS.
* O registo do servidor de nomes definido no ápice da zona também é criado automaticamente pelo Azure DNS quando a zona é criada. Apenas o TTL deste conjunto de discos é importado. Estes registos contêm os nomes do servidor de nomes fornecidos pelo Azure DNS. Os dados de registo não são substituídos pelos valores contidos no ficheiro da zona importada.
* Durante a visualização pública, o Azure DNS suporta apenas registos TXT de corda única. Os registos TXT multistring são concatenados e truncados a 255 caracteres.

### <a name="cli-format-and-values"></a>Formato e valores CLI

O formato do comando Azure CLI para importar uma zona DNS é:

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Valores:

* `<resource group>` é o nome do grupo de recursos para a zona em Azure DNS.
* `<zone name>` é o nome da zona.
* `<zone file name>` é o caminho/nome do ficheiro de zona a importar.

Se uma zona com este nome não existir no grupo de recursos, é criada para si. Se a zona já existir, os recordes importados são fundidos com os recordes existentes. 

### <a name="step-1-import-a-zone-file"></a>Passo 1. Importar um ficheiro de zona

Para importar um ficheiro de zona para a zona **contoso.com**.

1. Se ainda não tem um, tem de criar um grupo de recursos do Resource Manager.

    ```azurecli
    az group create --resource-group myresourcegroup -l westeurope
    ```

2. Para importar a zona **contoso.com** do ficheiro **contoso.com.txt** para uma nova zona de DNS no grupo de recursos **myresourcegroup,** executará o comando `az network dns zone import` .<BR>Este comando carrega o ficheiro da zona e analisa-o. O comando executa uma série de comandos no serviço Azure DNS para criar a zona e todos os recordes na zona. O comando informa o progresso na janela da consola, juntamente com quaisquer erros ou avisos. Como os conjuntos de recordes são criados em série, pode levar alguns minutos para importar um ficheiro de grande zona.

    ```azurecli
    az network dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>Passo 2. Verifique a zona

Para verificar a zona DE DNS depois de importar o ficheiro, pode utilizar qualquer um dos seguintes métodos:

* Pode listar os registos utilizando o seguinte comando Azure CLI:

    ```azurecli
    az network dns record-set list -g myresourcegroup -z contoso.com
    ```

* Pode listar os registos utilizando o comando Azure CLI `az network dns record-set ns list` .
* Pode usar `nslookup` para verificar a resolução de nomes para os registos. Como a zona ainda não está delegada, precisa especificar explicitamente os servidores de nome Azure DNS corretos. A amostra que se segue mostra como recuperar os nomes do servidor de nomes atribuídos à zona. Isto também mostra como consultar o registo "www" utilizando `nslookup` .

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

### <a name="step-3-update-dns-delegation"></a>Passo 3. Atualizar delegação do DNS

Depois de verificar que a zona foi importada corretamente, precisa de atualizar a delegação do DNS para apontar para os servidores de nomeS DNS do Azure. Para mais informações, consulte o artigo [Atualizar a delegação do DNS](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Exportar um ficheiro de zona DNS a partir de Azure DNS

O formato do comando Azure CLI para exportar uma zona DNS é:

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

Valores:

* `<resource group>` é o nome do grupo de recursos para a zona em Azure DNS.
* `<zone name>` é o nome da zona.
* `<zone file name>` é o caminho/nome do ficheiro de zona a exportar.

Tal como acontece com a importação de zona, primeiro tem de iniciar seduca, escolher a sua subscrição e configurar o CLI Azure para utilizar o modo Gestor de Recursos.

### <a name="to-export-a-zone-file"></a>Para exportar um ficheiro de zona

Para exportar a zona de DNS existente **contoso.com** no grupo de recursos **myresourcegroup** para o ficheiro **contoso.com.txt** (na pasta atual), executar `azure network dns zone export` . Este comando chama o serviço Azure DNS para enumerar os recordes na zona e exportar os resultados para um ficheiro de zona compatível com BIND.

```azurecli
az network dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
```

## <a name="next-steps"></a>Passos seguintes

* Saiba como [gerir recordes e registos](./dns-getstarted-cli.md) na sua zona de DNS.

* Saiba como [delegar o seu domínio no Azure DNS](dns-domain-delegation.md).