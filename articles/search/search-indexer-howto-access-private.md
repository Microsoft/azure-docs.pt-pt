---
title: Indexantes que acedem a recursos seguros através de pontos finais privados
titleSuffix: Azure Cognitive Search
description: Como orientar isso descreve a criação de pontos finais privados para os indexantes comunicarem com recursos seguros
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 94763cee852893057348f8eea1fa74fa742f62a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91534731"
---
# <a name="accessing-secure-resources-via-private-endpoints"></a>Acesso a recursos seguros através de pontos finais privados

Os recursos azure (como as contas de armazenamento que são usadas como fontes de dados), podem ser configurados de modo a que só possam ser acedidos a partir de uma lista específica de redes virtuais. Também podem ser configurados para desativar qualquer acesso "rede pública".
Os clientes podem solicitar à Azure Cognitive Search que crie uma [ligação de ponto final privado](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) (de saída) para aceder de forma segura a dados dessas fontes de dados através de indexadores.

## <a name="shared-private-link-resources-management-apis"></a>APIs de Gestão de Recursos de Ligação Privada Partilhada

Os pontos finais privados que são criados pela Azure Cognitive Search a pedido do cliente, para aceder a recursos "seguros" são referidos como *recursos de ligação privada partilhados.* O cliente está a "partilhar" o acesso a um recurso (como uma conta de armazenamento), que tem embarcado no [serviço Azure Private Link](https://azure.microsoft.com/services/private-link/).

A Azure Cognitive Search oferece através da API de gestão de pesquisa, a capacidade de [criar ou atualizar recursos de ligação privada partilhada.](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) Você usará esta API juntamente com outras APIs de gestão *de recursos de ligação privada partilhada* para configurar o acesso a um recurso seguro a partir de um indexante de Pesquisa Cognitiva Azure.

As ligações privadas de ponto final a alguns recursos só podem ser criadas através da versão de pré-visualização da gestão de pesquisa API ( `2020-08-01-Preview` ), indicada com a etiqueta "pré-visualização" na tabela abaixo. Os recursos sem a tag "pré-visualização" podem ser criados através da API de pré-visualização, bem como da API GA ( `2020-08-01` )

Segue-se a lista de recursos Azure para os quais os pontos finais privados de saída podem ser criados a partir da Pesquisa Cognitiva Azure. `groupId` listado no quadro abaixo deve ser usado exatamente (sensível a caso) na API para criar um recurso de ligação privada compartilhado.

| Recurso do Azure | ID do Grupo |
| --- | --- |
| Armazenamento Azure - Blob (ou) ADLS Gen 2 | `blob`|
| Armazenamento Azure - Mesas | `table`|
| Azure Cosmos DB - SQL API | `Sql`|
| Base de Dados SQL do Azure | `sqlServer`|
| Base de Dados Azure para MySQL (pré-visualização) | `mysqlServer`|
| Azure Key Vault | `vault` |
| Funções Azure (pré-visualização) | `sites` |

A lista de recursos Azure para os quais as ligações de ponto final privados de saída são suportadas também pode ser consultada através da [Lista Suportada API](https://docs.microsoft.com/rest/api/searchmanagement/privatelinkresources/listsupported).

Para efeitos deste guia, uma mistura de [ARMClient](https://github.com/projectkudu/ARMClient) e [Carteiro](https://www.postman.com/) são usadas para demonstrar as chamadas REST API.

> [!NOTE]
> Ao longo deste guia, vamos assumir que o nome do serviço de pesquisa é __contoso-search__ que existe no grupo de recursos __contoso__ de uma subscrição com iD de assinatura ID __0000000-0000-0000-0000-0000000000000000000000000000000000000000000000000000000000000000000000-0000000000.__ O ID de recursos deste serviço de pesquisa será `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search`

O resto do guia mostrará como o serviço __de pesquisa de contoso__ pode ser configurado para que os seus indexantes possam aceder aos dados a partir da conta de armazenamento segura `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage`

## <a name="securing-your-storage-account"></a>Segurança da sua conta de armazenamento

Configure a conta de armazenamento para [permitir o acesso apenas a partir de sub-redes específicas](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network). Através do portal Azure, se verificar esta opção e deixar o conjunto vazio, significa que não é permitido tráfego de qualquer rede virtual.

   ![Acesso virtual à rede](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png "Acesso virtual à rede")

> [!NOTE]
> A [abordagem confiável do serviço microsoft](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services) pode ser usada para contornar as restrições de rede virtual ou IP em tal conta de armazenamento e pode permitir ao serviço de pesquisa aceder a dados na conta de armazenamento, tal como descrito na forma de [orientar.](search-indexer-howto-access-trusted-service-exception.md) No entanto, ao utilizar esta comunicação de abordagem entre a Azure Cognitive Search e a conta de armazenamento ocorre através do endereço IP público da conta de armazenamento, através da rede segura da espinha dorsal da Microsoft.

## <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>Passo 1: Criar um recurso de ligação privada partilhado para a conta de armazenamento

Faça a seguinte chamada da API para solicitar a Azure Cognitive Search para criar uma ligação de ponto final privado de saída à conta de armazenamento

`armclient PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 create-pe.json`

O conteúdo do `create-pe.json` ficheiro (que representa o órgão de pedido à API) é o seguinte:

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve"
      }
}
```

Uma `202 Accepted` resposta é devolvida com sucesso - o processo de criação de um ponto final privado de saída é uma operação de longa duração (assíncrona). Envolve a implantação dos seguintes recursos -

1. Um ponto final privado atribuído com um endereço IP privado num `"Pending"` estado. O endereço IP privado é obtido a partir do espaço de endereço atribuído à rede virtual do serviço de pesquisa ambiente específico de execução de indexante privado. Após a aprovação do ponto final privado, qualquer comunicação da Azure Cognitive Search para a conta de armazenamento tem origem no endereço IP privado e num canal de ligação privado seguro.
2. Uma zona privada de DNS para o tipo de recurso, com base no `groupId` . Isto garantirá que qualquer procura de DNS para o recurso privado utilize o endereço IP associado ao ponto final privado.

Certifique-se de especificar o correto `groupId` para o tipo de recurso para o qual está a criar o ponto final privado. Qualquer incompatibilidade resultará numa mensagem de resposta não sucedida.

Como todas as operações assíncronas do Azure, a `PUT` chamada devolve um valor de `Azure-AsyncOperation` cabeçalho que será o seguinte:

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

Este URI pode ser inquirido periodicamente para obter o estado da operação. Recomendamos esperar até que o estado de funcionamento de recursos de ligação privada partilhado chegue a um estado terminal (isto é, `succeeded` ) antes de prosseguir.

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

```json
{
    "status": "running" | "succeeded" | "failed"
}
```

## <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>Passo 2a: Aprovar a ligação de ponto final privado para a conta de armazenamento

> [!NOTE]
> Esta secção utiliza o portal Azure para percorrer o fluxo de aprovação para um ponto final privado para armazenamento. A [API REST](https://docs.microsoft.com/rest/api/storagerp/privateendpointconnections) disponível através do fornecedor de recursos de armazenamento (RP) também pode ser utilizada.
>
> Outros fornecedores, como cosmosDB, servidor Azure SQL, etc., também oferecem APIs semelhantes para gerir ligações privadas de ponto final.

Navegue para o separador "**Ligações de ponto final**privado " da conta de armazenamento no portal Azure. Deve haver um pedido de ligação privada ao ponto final, com a mensagem de pedido da chamada anterior da API (uma vez que a operação assíncrono __tenha sido bem sucedida).__

   ![Aprovação de ponto final privado](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png "Aprovação de ponto final privado")

Selecione o ponto final privado criado pela Azure Cognitive Search (use a coluna "Private endpoint" para identificar a ligação de ponto final privado pelo nome especificado na API anterior) e escolha "Aprovar", com uma mensagem adequada (a mensagem não é significativa). Certifique-se de que a ligação de ponto final privado aparece da seguinte forma (pode ir de 1 a 2 minutos para que o estado seja atualizado no portal)

![Ponto final privado aprovado](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png "Ponto final privado aprovado")

Após a aprovação do pedido de ligação de ponto final privado, significa que o tráfego é *capaz* de fluir através do ponto final privado. Uma vez aprovado o ponto final privado Azure Cognitive Search criará os mapeamentos necessários da zona de DNS na zona DNS criada para o mesmo.

## <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>Passo 2b: Consultar o estado do recurso de ligação privada partilhada

 Para confirmar que o recurso de ligação privada partilhada foi atualizado após aprovação, obtenha o seu estatuto através da [API GET](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/get).

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

Se o `properties.provisioningState` recurso for `Succeeded` e `properties.status` `Approved` for, significa que o recurso de ligação privada partilhado é funcional e os indexantes podem ser configurados para comunicar através do ponto final privado.

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve",
        "status": "Approved",
        "resourceRegion": null,
        "provisioningState": "Succeeded"
      }
}

```

## <a name="step-3-configure-indexer-to-run-in-the-private-environment"></a>Passo 3: Indexador de configuração para funcionar em ambiente privado

> [!NOTE]
> Este passo pode ser realizado mesmo antes da ligação do ponto final privado ser aprovada. Até que a ligação de ponto final privado seja aprovada, qualquer indexante que tente comunicar com um recurso seguro (como a conta de armazenamento), acabará num estado de falha transitória. Novos indexantes não serão criados. Assim que a ligação de ponto final privado for aprovada, os indexantes poderão aceder à conta de armazenamento privado.

1. [Crie uma fonte de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source) que aponte para a conta de armazenamento segura e um recipiente apropriado dentro da conta de armazenamento. O seguinte mostra este pedido realizado através do Carteiro.
![Criar Origem de Dados](media\search-indexer-howto-secure-access\create-ds.png "Criação de Fonte de Dados")

2. Da mesma [forma, criar um índice](https://docs.microsoft.com/rest/api/searchservice/create-index) e criar opcionalmente [um skillset](https://docs.microsoft.com/rest/api/searchservice/create-skillset) usando a API REST.

3. [Crie um indexante](https://docs.microsoft.com/rest/api/searchservice/create-indexer) que aponte para a fonte de dados, índice e skillset criado acima. Além disso, force o indexante a funcionar no ambiente de execução privada, definindo a propriedade de configuração indexante `executionEnvironment` para `"Private"` .
![Criar Indexer](media\search-indexer-howto-secure-access\create-idr.png "Criação de indexante")

O indexante deve ser criado com sucesso, e deve estar a progredir - indexando o conteúdo da conta de armazenamento sobre a ligação ao ponto final privado. O estado do indexante pode ser monitorizado através da [API do estado do Indexer](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

> [!NOTE]
> Se já tiver indexantes existentes, pode simplesmente atualizá-los através da [API PUT](https://docs.microsoft.com/rest/api/searchservice/create-indexer) para definir o `executionEnvironment` `"Private"` .

## <a name="troubleshooting-issues"></a>Resolução de problemas

- Ao criar um indexador, se a criação falhar com uma mensagem de erro semelhante a "As credenciais de origem de dados são inválidas", significa que ou a ligação de ponto final privado não foi *aprovada* ou não está funcional.
Obtenha o estado do recurso de ligação privada partilhado utilizando a [API GET](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/get). Se tiver sido *aprovado,* verifique o `properties.provisioningState` recurso. Se for `Incomplete` , isto significa que algumas das dependências subjacentes ao recurso não foram prestadas - reeditam o pedido de `PUT` "recriar" o recurso de ligação privada partilhada que deve corrigir o problema. Poderá ser necessária uma nova aprovação - verifique mais uma vez o estado do recurso para verificar.
- Se o indexante for criado sem definir o seu `executionEnvironment` , a criação do indexante pode ter sucesso, mas o seu histórico de execução mostrará que as execuções do indexante não são bem sucedidas. Deve [atualizar o indexante](https://docs.microsoft.com/rest/api/searchservice/update-indexer) para especificar o ambiente de execução.
- Se o indexador for criado sem definir o `executionEnvironment` e executado com sucesso, significa que a Azure Cognitive Search decidiu que o seu ambiente de execução é o ambiente "privado" específico do serviço de pesquisa. No entanto, isto pode mudar com base numa variedade de fatores (recursos consumidos pelo indexante, a carga no serviço de pesquisa, e assim por diante) e pode falhar num ponto posterior - recomendamos vivamente que estabeleça o `executionEnvironment` ponto de garantir que não `"Private"` falhará no futuro.
- [Quotas e limites](search-limits-quotas-capacity.md) determinam quantos recursos de ligação privada partilhados podem ser criados e dependem do SKU do serviço de pesquisa.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os pontos finais privados:

- [O que são pontos finais privados?](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)
- [Configurações de DNS necessárias para pontos finais privados](https://docs.microsoft.com/azure/private-link/private-endpoint-dns)