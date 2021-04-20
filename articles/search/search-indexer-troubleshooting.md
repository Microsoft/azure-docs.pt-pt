---
title: Resolução de problemas problemas problemas comuns de indexação de pesquisa
titleSuffix: Azure Cognitive Search
description: Corrija erros e problemas comuns com indexadores na Pesquisa Cognitiva Azure, incluindo ligação de fonte de dados, firewall e documentos em falta.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: efdd9666c8876ddaf12b9555fa66beb62c56e93e
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107740079"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Problemas de resolução de problemas problemas de indexante comum na Pesquisa Cognitiva do Azure

Os indexantes podem encontrar uma série de problemas ao indexar dados na Pesquisa Cognitiva do Azure. As principais categorias de insucesso incluem:

* [Ligação a uma fonte de dados ou outros recursos](#connection-errors)
* [Processamento de documentos](#document-processing-errors)
* [Ingestão de documentos a um índice](#index-errors)

## <a name="connection-errors"></a>Erros de ligação

> [!NOTE]
> Os indexantes têm um suporte limitado para aceder a fontes de dados e outros recursos que são protegidos pelos mecanismos de segurança da rede Azure. Atualmente, os indexantes só podem aceder a fontes de dados através de mecanismos de restrição de intervalo de endereço IP correspondentes ou regras NSG quando aplicável. Os detalhes para aceder a cada fonte de dados suportado podem ser encontrados abaixo.
>
> Pode descobrir o endereço IP do seu serviço de pesquisa, verificando o seu nome de domínio totalmente qualificado (por exemplo, `<your-search-service-name>.search.windows.net` ).
>
> Pode descobrir a gama de endereços IP da tag de `AzureCognitiveSearch` [serviço](../virtual-network/service-tags-overview.md#available-service-tags) utilizando [ficheiros JSON transferíveis](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) ou através da [API de Descoberta de Marca de Serviço.](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) O intervalo de endereços IP é atualizado semanalmente.

### <a name="configure-firewall-rules"></a>Configurar as regras de firewall

Azure Storage, CosmosDB e Azure SQL fornecem uma firewall configurável. Não há nenhuma mensagem de erro específica quando a firewall está ativada. Normalmente, os erros de firewall são genéricos e parecem `The remote server returned an error: (403) Forbidden` `Credentials provided in the connection string are invalid or have expired` ou.

Existem 2 opções para permitir que os indexantes acedam a estes recursos em tal caso:

* Desative a firewall, permitindo o acesso a partir de **Todas as Redes** (se possível).
* Em alternativa, pode permitir o acesso ao endereço IP do seu serviço de pesquisa e ao intervalo de identificação IP da etiqueta de `AzureCognitiveSearch` [serviço](../virtual-network/service-tags-overview.md#available-service-tags) nas regras de firewall do seu recurso (restrição de intervalo de endereço IP).

Os detalhes para configurar as restrições de intervalo de endereços IP para cada tipo de fonte de dados podem ser encontrados a partir dos seguintes links:

* [Armazenamento do Azure](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [BD do Cosmos](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [SQL do Azure](../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)

**Limitação**: Conforme indicado na documentação acima para o Armazenamento Azure, as restrições de intervalo de endereços IP só funcionarão se o seu serviço de pesquisa e a sua conta de armazenamento estiverem em diferentes regiões.

As funções Azure (que poderiam ser usadas como [uma habilidade Custom Web Api](cognitive-search-custom-skill-web-api.md)) também suportam [restrições de endereço IP](../azure-functions/ip-addresses.md#ip-address-restrictions). A lista de endereços IP para configurar seria o endereço IP do seu serviço de pesquisa e o intervalo de endereço IP da tag de `AzureCognitiveSearch` serviço.

Os detalhes para aceder aos dados no servidor SQL num VM Azure estão delineados [aqui](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

### <a name="configure-network-security-group-nsg-rules"></a>Configure regras do grupo de segurança da rede (NSG)

Ao aceder a dados num caso gerido pelo SQL, ou quando um Azure VM é usado como o serviço web URI para uma [habilidade Personalizada Web Api,](cognitive-search-custom-skill-web-api.md)os clientes não precisam de se preocupar com endereços IP específicos.

Nesses casos, o Azure VM, ou a instância gerida pelo SQL pode ser configurado para residir dentro de uma rede virtual. Em seguida, um grupo de segurança de rede pode ser configurado para filtrar o tipo de tráfego de rede que pode fluir dentro e fora das sub-redes de rede virtuais e interfaces de rede.

A `AzureCognitiveSearch` etiqueta de serviço pode ser usada diretamente nas [regras NSG](../virtual-network/manage-network-security-group.md#work-with-security-rules) de entrada sem precisar de procurar a sua gama de endereços IP.

Mais detalhes para aceder a dados em um exemplo gerido sql são delineados [aqui](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

### <a name="cosmosdb-indexing-isnt-enabled"></a>CosmosDB "Indexing" não está habilitado

A Azure Cognitive Search tem uma dependência implícita na indexação de Cosmos DB. Se desativar a indexação automática em Cosmos DB, a Azure Cognitive Search retorna um estado de sucesso, mas não consegue indexar o conteúdo do contentor. Para obter instruções sobre como verificar as definições e ligar a indexação, consulte [Gerir a indexação em Azure Cosmos DB](../cosmos-db/how-to-manage-indexing-policy.md#use-the-azure-portal).

### <a name="sharepoint-online-conditional-access-policies"></a>Políticas de acesso condicional online sharePoint

Ao criar um indexador SharePoint Online, passará por um passo que o obriga a iniciar sessão na sua aplicação AAD depois de fornecer um código de dispositivo. Se receber uma mensagem que diz "O seu sing-in foi bem sucedido, mas o seu administrador requer que o dispositivo que solicita o acesso seja gerido" o indexante está provavelmente bloqueado de aceder à biblioteca de documentos Online SharePoint devido a uma política [de Acesso Condicional.](https://review.docs.microsoft.com/azure/active-directory/conditional-access/overview)

Para atualizar a política para permitir o acesso do indexante à biblioteca do documento, siga os passos abaixo:

1. Abra o portal Azure e procure **acesso condicionado Azure AD** e, em seguida, selecione **Políticas** no menu esquerdo. Se não tiver acesso para ver esta página, terá de encontrar alguém que tenha acesso ou tenha acesso.

1. Determine qual a política que bloqueia o indexante SharePoint Online de aceder à biblioteca de documentos. A política que poderá estar a bloquear o indexante incluirá a conta de utilizador que usou para autenticar durante o passo de criação do indexante na secção **Utilizadores e grupos.** A política também pode ter **condições** que:
    * Restringir as plataformas **do Windows.**
    * Restringir **aplicações móveis e clientes de desktop**.
    * Ter **o estado do dispositivo** configurado para **Sim**.

1. Uma vez confirmado que há uma política que está bloqueando o indexante, você precisa fazer uma isenção para o indexante. Recupere o endereço IP do serviço de pesquisa.

    1. Obtenha o nome de domínio totalmente qualificado (FQDN) do seu serviço de pesquisa. Isto vai `<search-service-name>.search.windows.net` parecer. Você pode descobrir o FQDN procurando o seu serviço de pesquisa no portal Azure.

   ![Obtenha serviço FQDN](media\search-indexer-howto-secure-access\search-service-portal.png "Obtenha serviço FQDN")

    O endereço IP do serviço de pesquisa pode ser obtido através da realização de um `nslookup` (ou `ping` a) do FQDN. No exemplo abaixo, adicionaria "150.0.0.1" a uma regra de entrada na firewall de armazenamento Azure. Pode demorar até 15 minutos após a atualização das definições de firewall para que o indexante do serviço de pesquisa possa aceder à conta de Armazenamento Azure.

    ```azurepowershell

    nslookup contoso.search.windows.net
    Server:  server.example.org
    Address:  10.50.10.50
    
    Non-authoritative answer:
    Name:    <name>
    Address:  150.0.0.1
    Aliases:  contoso.search.windows.net
    ```

1. Obtenha os intervalos de endereço IP para o ambiente de execução do indexante para a sua região.

    São utilizados endereços IP adicionais para pedidos originários do [ambiente de execução multi-inquilino](search-indexer-securing-resources.md#indexer-execution-environment)do indexante. Pode obter esta gama de endereços IP a partir da etiqueta de serviço.

    Os intervalos de endereço IP para a `AzureCognitiveSearch` etiqueta de serviço podem ser obtidos através da descoberta [API (pré-visualização)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) ou do [ficheiro JSON transferível](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

    Para esta passagem, assumindo que o serviço de pesquisa é a nuvem pública do Azure, o [ficheiro Azure Public JSON](https://www.microsoft.com/download/details.aspx?id=56519) deve ser descarregado.

   ![Baixar ficheiro JSON](media\search-indexer-troubleshooting\service-tag.png "Baixar ficheiro JSON")

    A partir do ficheiro JSON, assumindo que o serviço de pesquisa está no Centro-Oeste dos EUA, a lista de endereços IP para o ambiente de execução indexante multi-inquilinos são listados abaixo.

    ```json
        {
          "name": "AzureCognitiveSearch.WestCentralUS",
          "id": "AzureCognitiveSearch.WestCentralUS",
          "properties": {
            "changeNumber": 1,
            "region": "westcentralus",
            "platform": "Azure",
            "systemService": "AzureCognitiveSearch",
            "addressPrefixes": [
              "52.150.139.0/26",
              "52.253.133.74/32"
            ]
          }
        }
    ```

1. Voltar à página de Acesso Condicional no portal Azure **selecione localizações nomeadas** do menu à esquerda e, em seguida, selecione **+ localização das gamas IP**. Dê um nome à sua nova localização e adicione as gamas IP para o seu serviço de pesquisa e ambientes de execução indexante que recolheu nos dois últimos passos.
    * Para o seu endereço IP do seu serviço de pesquisa poderá ter de adicionar "/32" ao final do endereço IP, uma vez que apenas aceita gamas IP válidas.
    * Lembre-se que para os intervalos IP do ambiente de execução do indexante, basta adicionar as gamas IP para a região em que o seu serviço de pesquisa está.

1. Excluir a nova localização nomeada da apólice. 
    1. Selecione **Políticas** no menu à esquerda. 
    1. Selecione a política que está bloqueando o indexante.
    1. Selecione **Condições**.
    1. Selecione **Locais**.
    1. Selecione **Excluir** em seguida, adicione a nova localização nomeada.
    1. **Guarde** as alterações.

1. Aguarde alguns minutos para que a política atualize e aplique as novas regras políticas.

1. Tente criar o indexante novamente
    1. Envie um pedido de atualização para o objeto de origem de dados que criou.
    1. Reencaende o indexante criar pedido. Use o novo código para iniciar sessão e, em seguida, envie outro pedido de criação de indexante após o login bem sucedido.

## <a name="document-processing-errors"></a>Erros de processamento de documentos

### <a name="unprocessable-or-unsupported-documents"></a>Documentos não processáveis ou não suportados

O indexante blob [documenta quais os formatos de documentos são explicitamente suportados.](search-howto-indexing-azure-blob-storage.md#SupportedFormats). . Às vezes, um recipiente de armazenamento de bolhas contém documentos não suportados. Outras vezes pode haver documentos problemáticos. Pode evitar parar o seu indexante nestes documentos [alterando opções de configuração:](search-howto-indexing-azure-blob-storage.md#DealingWithErrors)

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Conteúdo de documento em falta

O indexante blob [encontra e extrai texto de bolhas num recipiente](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Alguns problemas com a extração de texto incluem:

* O documento contém apenas imagens digitalizadas. As bolhas PDF que têm conteúdo não texto, como imagens digitalizadas (JPGs), não produzem resultados num pipeline de indexação de blob padrão. Se tiver conteúdo de imagem com elementos de texto, pode utilizar a [pesquisa cognitiva](cognitive-search-concept-image-scenarios.md) para encontrar e extrair o texto.
* O indexante blob é configurado apenas para metadados de índice. Para extrair o conteúdo, o indexante blob deve ser configurado para [extrair tanto o conteúdo como os metadados:](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex)

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Erros de índice

### <a name="missing-documents"></a>Documentos em falta

Os indexantes encontram documentos a partir de uma fonte de [dados.](/rest/api/searchservice/create-data-source) Por vezes, um documento da fonte de dados que deveria ter sido indexado parece estar ausente de um índice. Há algumas razões comuns para estes erros acontecerem:

* O documento não foi indexado. Verifique se o portal tem um indexer bem sucedido.
* Verifique o valor [de rastreio de alterações.](/rest/api/searchservice/create-data-source#data-change-detection-policies) Se o seu alto valor de marca de água for uma data definida para um futuro, então quaisquer documentos que tenham uma data inferior a esta serão ignorados pelo indexante. Pode compreender o estado de rastreio de alterações do seu indexante utilizando os campos "InitialTrackingState" e "FinalTrackingState" no estado do [indexante](/rest/api/searchservice/get-indexer-status#indexer-execution-result).
* O documento foi atualizado após a execução do indexante. Se o seu indexante estiver na [programação,](/rest/api/searchservice/create-indexer#indexer-schedule)irá eventualmente repetir e recolher o documento.
* A [consulta](/rest/api/searchservice/create-data-source) especificada na fonte de dados exclui o documento. Os indexantes não podem indexar documentos que não fazem parte da fonte de dados.
* [Os mapeamentos de campo](/rest/api/searchservice/create-indexer#fieldmappings) ou [o enriquecimento de IA](./cognitive-search-concept-intro.md) alteraram o documento e parece diferente do que se espera.
* Utilize o [documento de procura API](/rest/api/searchservice/lookup-document) para encontrar o seu documento.