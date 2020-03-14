---
title: Importar e exportar um ficheiro de zona de domínio - Azure CLI
titleSuffix: Azure DNS
description: Saiba importar e exportar um ficheiro de zona DNS para o Azure DNS utilizando o Azure CLI
services: dns
author: rohinkoul
ms.service: dns
ms.date: 4/3/2019
ms.author: rohink
ms.topic: conceptual
ms.openlocfilehash: a5c2fdde564eba2d95e7f14f4d47e4d381739d5d
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79365173"
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>Importar e exportar um ficheiro de zona DNS utilizando o Azure CLI

Este artigo acompanha-o a importar e exportar ficheiros de zona DNS para O DNS Azure utilizando o Azure CLI.

## <a name="introduction-to-dns-zone-migration"></a>Introdução à migração da zona dNS

Um ficheiro de zona DNS é um ficheiro de texto que contém detalhes de todos os registos do Sistema de Nome de Domínio (DNS) na zona. Segue um formato padrão, tornando-o adequado para a transferência de registos DNS entre sistemas DNS. Usar um ficheiro de zona é uma forma rápida, fiável e conveniente de transferir uma zona DNS para dentro ou para fora do Azure DNS.

O Azure DNS suporta ficheiros de zonas de importação e exportação utilizando a interface da linha de comando Azure (CLI). A importação de ficheiros de zona **não** é suportada atualmente através do Azure PowerShell ou do portal Azure.

O Azure CLI é uma ferramenta de comando cross-platform utilizada para gerir os serviços Azure. Está disponível para as plataformas Windows, Mac e Linux a partir da página de [downloads do Azure](https://azure.microsoft.com/downloads/). O suporte transversal é importante para a importação e exportação de ficheiros de zonas, porque o software de servidor de nome mais comum, [BIND,](https://www.isc.org/downloads/bind/)normalmente funciona no Linux.

## <a name="obtain-your-existing-dns-zone-file"></a>Obtenha o seu ficheiro de zona DNS existente

Antes de importar um ficheiro de zona DNS para o DNS Azure, precisa de obter uma cópia do ficheiro de zona. A origem deste ficheiro depende do local onde a zona DNS está atualmente alojada.

* Se a sua zona DNS for hospedada por um serviço parceiro (como um registrador de domínio, fornecedor dedicado de hospedagem dNS ou fornecedor de nuvem alternativa), esse serviço deve fornecer a capacidade de descarregar o ficheiro de zona DNS.
* Se a sua zona DNS estiver alojada no Windows DNS, a pasta predefinida para os ficheiros de zona é **%systemroot%\systemroot%\system32\dns**. O caminho completo para cada ficheiro de zona também mostra no separador **Geral** da consola DNS.
* Se a sua zona DNS estiver hospedada utilizando BIND, a localização do ficheiro de zona para cada zona é especificada no ficheiro de configuração BIND **nomeado.conf**.

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Importar um ficheiro de zona DNS em DNS Azure

Importar um ficheiro de zona cria uma nova zona em DNS Azure se não existir já existente. Se a zona já existir, os conjuntos de registo sintetizadores no ficheiro de zona devem ser fundidos com os conjuntos de registo existentes.

### <a name="merge-behavior"></a>Fundir comportamentos

* Por padrão, os conjuntos de recordes existentes e novos são fundidos. Os registos idênticos dentro de um conjunto de registos fundidos são desduplicados.
* Quando os conjuntos de recordes são fundidos, é utilizado o tempo de viver (TTL) de conjuntos de discos pré-existentes.
* Os parâmetros de início da Autoridade (SOA) (exceto `host`) são sempre retirados do ficheiro da zona importada. Da mesma forma, para o registo do servidor de nome definido no ápice da zona, o TTL é sempre retirado do ficheiro da zona importada.
* Um registo CNAME importado não substitui um registo CNAME existente com o mesmo nome.  
* Quando surge um conflito entre um registo CNAME e outro registo com o mesmo nome, mas de tipo diferente (independentemente do qual exista ou novo), o registo existente é mantido. 

### <a name="additional-information-about-importing"></a>Informações adicionais sobre a importação

As seguintes notas fornecem detalhes técnicos adicionais sobre o processo de importação de zonas.

* A diretiva `$TTL` é facultativa e é apoiada. Quando não for dada nenhuma diretiva `$TTL`, os registos sem um TTL explícito são importados definidos para um TTL padrão de 3600 segundos. Quando dois registos no mesmo conjunto de registos especificam diferentes TTLs, o valor mais baixo é utilizado.
* A diretiva `$ORIGIN` é facultativa e é apoiada. Quando não for definido qualquer `$ORIGIN`, o valor predefinido utilizado é o nome de zona especificado na linha de comando (mais o terminando ".").
* As diretivas `$INCLUDE` e `$GENERATE` não são apoiadas.
* Estes tipos de discos são suportados: A, AAAA, CAA, CNAME, MX, NS, SOA, SRV e TXT.
* O registo SOA é criado automaticamente pelo Azure DNS quando uma zona é criada. Quando se importa um ficheiro de zona, todos os parâmetros SOA são retirados do ficheiro da zona, *exceto* o parâmetro `host`. Este parâmetro utiliza o valor fornecido pelo Azure DNS. Isto porque este parâmetro deve referir-se ao servidor de nome primário fornecido pelo Azure DNS.
* O registo do servidor de nome definido no ápice da zona também é criado automaticamente pelo Azure DNS quando a zona é criada. Apenas o TTL deste recorde é importado. Estes registos contêm os nomes do servidor fornecidos pelo Azure DNS. Os dados de registo não são subscritos pelos valores contidos no ficheiro da zona importada.
* Durante a pré-visualização pública, o Azure DNS suporta apenas discos TXT de corda única. Os registos TXT multistring são concatenados e truncados a 255 caracteres.

### <a name="cli-format-and-values"></a>Formato e valores CLI

O formato do comando Azure CLI para importar uma zona DNS é:

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Valores:

* `<resource group>` é o nome do grupo de recursos para a zona em Azure DNS.
* `<zone name>` é o nome da zona.
* `<zone file name>` é o caminho/nome do ficheiro da zona a importar.

Se uma zona com este nome não existir no grupo de recursos, é criada para si. Se a zona já existir, os recordes importados são fundidos com os estabelecidos de recorde existentes. 

### <a name="step-1-import-a-zone-file"></a>Passo 1. Importar um ficheiro de zona

Para importar um ficheiro de zona para a zona **contoso.com**.

1. Se ainda não tem um, precisa de criar um grupo de recursos do Gestor de Recursos.

    ```azurecli
    az group create --group myresourcegroup -l westeurope
    ```

2. Para importar a zona **contoso.com** do ficheiro **contoso.com.txt** para uma nova zona DNS no grupo de recursos **myresourcegroup,** você executará o comando `az network dns zone import`.<BR>Este comando carrega o ficheiro da zona e analisa-o. O comando executa uma série de comandos no serviço Azure DNS para criar a zona e todos os recordes estabelecidos na zona. O comando relata o progresso na janela da consola, juntamente com quaisquer erros ou avisos. Como os conjuntos de recordes são criados em série, pode levar alguns minutos para importar um ficheiro de grande zona.

    ```azurecli
    az network dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>Passo 2. Verifique a zona

Para verificar a zona DNS depois de importar o ficheiro, pode utilizar qualquer um dos seguintes métodos:

* Pode enumerar os registos utilizando o seguinte comando Azure CLI:

    ```azurecli
    az network dns record-set list -g myresourcegroup -z contoso.com
    ```

* Pode enumerar os registos utilizando o comando Azure CLI `az network dns record-set ns list`.
* Pode utilizar `nslookup` para verificar a resolução de nomes dos registos. Como a zona ainda não foi delegada, precisa especificar explicitamente os servidores de nome DNS Do DNS do Azure corretos. A amostra que se segue mostra como recuperar os nomes do servidor atribuídos à zona. Isto também mostra como consultar o registo "www" utilizando `nslookup`.

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

### <a name="step-3-update-dns-delegation"></a>Passo 3. Atualizar a delegação do DNS

Depois de ter verificado que a zona foi importada corretamente, precisa de atualizar a delegação dNS para apontar para os servidores de nome DNS Azure. Para mais informações, consulte o artigo [Atualizar a delegação do DNS.](dns-domain-delegation.md)

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Exportar um ficheiro de zona DNS a partir de DNS Azure

O formato do comando Azure CLI para exportar uma zona DNS é:

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

Valores:

* `<resource group>` é o nome do grupo de recursos para a zona em Azure DNS.
* `<zone name>` é o nome da zona.
* `<zone file name>` é o caminho/nome do ficheiro de zona a exportar.

Tal como acontece com a importação de zona, primeiro precisa de iniciar sessão, escolher a sua subscrição e configurar o Azure CLI para utilizar o modo Gestor de Recursos.

### <a name="to-export-a-zone-file"></a>Para exportar um ficheiro de zona

Para exportar a atual zona de DNS Azure **contoso.com** no grupo de recursos **myresourcegroup** para o ficheiro **contoso.com.txt** (na pasta atual), executar `azure network dns zone export`. Este comando chama o serviço Azure DNS para enumerar os recordes na zona e exportar os resultados para um ficheiro de zona compatível com BIND.

```azurecli
az network dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
```

## <a name="next-steps"></a>Passos seguintes

* Aprenda a [gerir recordes e recordes](dns-getstarted-create-recordset-cli.md) na sua zona de DNS.

* Aprenda a [delegar o seu domínio no Azure DNS.](dns-domain-delegation.md)
