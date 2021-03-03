---
title: Ligações indexantes através de um ponto final privado
titleSuffix: Azure Cognitive Search
description: Configure as ligações indexantes para aceder a conteúdos de outros recursos Azure que são protegidos através de um ponto final privado.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 0b30cb1767e733861d8418ea29e564bc90a5bc70
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676509"
---
# <a name="make-indexer-connections-through-a-private-endpoint"></a>Faça ligações indexantes através de um ponto final privado

Muitos recursos Azure, como contas de armazenamento Azure, podem ser configurados para aceitar ligações a partir de uma lista de redes virtuais e recusar ligações externas que originuam de uma rede pública. Se estiver a utilizar um indexante para indexar dados na Pesquisa Cognitiva Azure, e a sua fonte de dados estiver numa rede privada, pode criar uma [ligação de ponto final privado](../private-link/private-endpoint-overview.md) de saída para chegar aos dados.

Este método de ligação indexante está sujeito aos dois seguintes requisitos:

+ O recurso Azure que fornece conteúdo ou código deve ser previamente registado no [serviço Azure Private Link](https://azure.microsoft.com/services/private-link/).

+ O serviço de Pesquisa Cognitiva Azure deve estar no nível Básico ou superior. A funcionalidade não está disponível no nível Gratuito. Além disso, se o seu indexante tiver uma habilidade, o nível deve ser Standard 2 (S2) ou superior. Para mais informações, consulte [os limites de Serviço.](search-limits-quotas-capacity.md#shared-private-link-resource-limits)

## <a name="shared-private-link-resources-management-apis"></a>APIs de Gestão de Recursos de Ligação Privada Partilhada

Os pontos finais privados de recursos seguros que são criados através das APIs de Pesquisa Cognitiva Azure são referidos como *recursos de ligação privada partilhados.* Isto porque está a "partilhar" o acesso a um recurso, como uma conta de armazenamento, que foi integrado com o [serviço Azure Private Link.](https://azure.microsoft.com/services/private-link/)

Através da sua API de Gestão REST, a Azure Cognitive Search fornece uma operação [CreateOrUpdate](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) que pode utilizar para configurar o acesso a partir de um indexante de Pesquisa Cognitiva Azure.

Só é possível criar ligações de ponto final privado a alguns recursos utilizando a versão de pré-visualização da API de Gestão de Pesquisa (versão *2020-08-01-pré-visualização* ou posterior), que é designada *pré-visualização* na tabela seguinte. Os recursos sem designação *de pré-visualização* podem ser criados com a versão API de pré-visualização ou geralmente disponível *(2020-08-01* ou posterior).

A tabela que se segue lista os recursos Azure para os quais pode criar pontos finais privados de saída da Azure Cognitive Search. Para criar um recurso de ligação privada partilhada, insira os valores **de ID** do Grupo exatamente como estão escritos na API. Os valores são sensíveis a casos.

| Recurso do Azure | ID do Grupo |
| --- | --- |
| Armazenamento Azure - Blob (ou) ADLS Gen 2 | `blob`|
| Armazenamento Azure - Mesas | `table`|
| Azure Cosmos DB - SQL API | `Sql`|
| Base de Dados SQL do Azure | `sqlServer`|
| Base de Dados Azure para MySQL (pré-visualização) | `mysqlServer`|
| Azure Key Vault | `vault` |
| Funções Azure (pré-visualização) | `sites` |

Também pode consultar os recursos Azure para os quais as ligações de ponto final privado de saída são suportadas utilizando a [lista de APIs suportados.](/rest/api/searchmanagement/privatelinkresources/listsupported)

No restante deste artigo, é utilizada uma mistura do [Azure CLI](/cli/azure/) (ou [ARMClient,](https://github.com/projectkudu/ARMClient) se preferir) e [do Carteiro](https://www.postman.com/) (ou de qualquer outro cliente HTTP como [o curl,](https://curl.se/) se preferir) para demonstrar as chamadas REST API.

> [!NOTE]
> Os exemplos deste artigo baseiam-se nos seguintes pressupostos:
> * O nome do serviço de pesquisa é _contoso-search,_ que existe no grupo de recursos _contoso_ de uma subscrição com iD de assinatura _ID 000000-0000-0000-0000-000000000000000000000000._ 
> * O ID de recursos deste serviço de pesquisa é _/subscrições/0000000-0000-0000-0000-00000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search_.

Os restantes exemplos mostram como o serviço _de pesquisa de contoso_ pode ser configurado para que os seus indexantes possam aceder aos dados a partir da conta de armazenamento seguro _/subscrições/0000000-0000-0000-0000-000000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage._

## <a name="secure-your-storage-account"></a>Proteja a sua conta de armazenamento

Configure a conta de armazenamento para [permitir o acesso apenas a partir de sub-redes específicas](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network). No portal Azure, se selecionar esta opção e deixar o conjunto vazio, significa que não é permitido tráfego de redes virtuais.

   ![Screenshot do painel "Firewalls e redes virtuais", mostrando a opção de permitir o acesso a redes selecionadas. ](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png)

> [!NOTE]
> Pode utilizar a [abordagem de serviço fidedigna](../storage/common/storage-network-security.md#trusted-microsoft-services) da Microsoft para contornar as restrições de rede virtual ou IP numa conta de armazenamento. Também pode ativar o serviço de pesquisa para aceder aos dados na conta de armazenamento. Para tal, consulte [o Indexer acede ao Azure Storage com a exceção de serviço fidedigno](search-indexer-howto-access-trusted-service-exception.md). 
>
> No entanto, quando utiliza esta abordagem, a comunicação entre a Azure Cognitive Search e a sua conta de armazenamento ocorre através do endereço IP público da conta de armazenamento, através da rede de espinha dorsal segura da Microsoft.

### <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>Passo 1: Criar um recurso de ligação privada partilhado para a conta de armazenamento

Para solicitar a Azure Cognitive Search para criar uma ligação de ponto final privado de saída à conta de armazenamento, faça a seguinte chamada API, por exemplo com o [Azure CLI](/cli/azure/): 

`az rest --method put --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 --body @create-pe.json`

Ou se preferir usar [ARMClient:](https://github.com/projectkudu/ARMClient)

`armclient PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 create-pe.json`

O conteúdo do *create-pe.jsem* arquivo, que representa o órgão de pedido à API, são os seguintes:

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

Uma `202 Accepted` resposta é devolvida ao sucesso. O processo de criação de um ponto final privado de saída é uma operação de longa duração (assíncronos). Envolve a implantação dos seguintes recursos:

* Um ponto final privado, atribuído com um endereço IP privado num `"Pending"` estado. O endereço IP privado é obtido a partir do espaço de endereço que é atribuído à rede virtual do ambiente de execução para o indexante privado específico do serviço de pesquisa. Após a aprovação do ponto final privado, qualquer comunicação da Azure Cognitive Search para a conta de armazenamento tem origem no endereço IP privado e num canal de ligação privado seguro.

* Uma zona privada de DNS para o tipo de recurso, com base no `groupId` . Ao implementar este recurso, certifique-se de que qualquer procura de DNS para o recurso privado utiliza o endereço IP associado ao ponto final privado.

Certifique-se de especificar o correto `groupId` para o tipo de recurso para o qual está a criar o ponto final privado. Qualquer incompatibilidade resultará numa mensagem de resposta não sucedida.

Como em todas as operações assíncronas do Azure, a `PUT` chamada devolve um valor de `Azure-AsyncOperation` cabeçalho que se parece com o seguinte:

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

Pode sondar este URI periodicamente para obter o estado da operação. Antes de prosseguir, recomendamos que aguarde até que o estado da operação de recurso de ligação privada partilhada atinja um estado terminal (ou seja, o estado da operação é *bem sucedido).*

`az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01`

Ou usando o ARMClient:

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

```json
{
    "status": "running" | "succeeded" | "failed"
}
```

### <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>Passo 2a: Aprovar a ligação de ponto final privado para a conta de armazenamento

> [!NOTE]
> Nesta secção, você usa o portal Azure para percorrer o fluxo de aprovação para um ponto final privado para armazenamento. Em alternativa, pode utilizar a [API REST](/rest/api/storagerp/privateendpointconnections) disponível através do fornecedor de recursos de armazenamento.
>
> Outros fornecedores, como o Azure Cosmos DB ou o Azure SQL Server, oferecem APIs similares de fornecedores de recursos de armazenamento para gerir ligações privadas de ponto final.

1. No portal Azure, selecione o **separador de ligações de ponto final Privado** da sua conta de armazenamento. Após o sucesso da operação assíncrono, deverá haver um pedido de ligação privada com a mensagem de pedido da chamada anterior da API.

   ![Screenshot do portal Azure, mostrando o painel "Ligações de ponto final privado".](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png)

1. Selecione o ponto final privado que a Azure Cognitive Search criou. Na coluna **private endpoint,** identifique a ligação de ponto final privado pelo nome especificado na API anterior, selecione **Aprovar** e, em seguida, introduza uma mensagem apropriada. O conteúdo da mensagem não é significativo. 

   Certifique-se de que a ligação do ponto final privado aparece como mostrado na imagem a seguir. Pode levar um a dois minutos para que o estado seja atualizado no portal.

   ![Screenshot do portal Azure, mostrando um estado "Aprovado" no painel "Ligações de ponto final privado".](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png)

Após a aprovação do pedido de ligação de ponto final privado, o tráfego é *capaz* de fluir através do ponto final privado. Após a aprovação do ponto final privado, a Azure Cognitive Search cria os mapeamentos necessários da zona de DNS na zona de DNS que é criada para o mesmo.

### <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>Passo 2b: Consultar o estado do recurso de ligação privada partilhada

Para confirmar que o recurso de ligação privada partilhada foi atualizado após aprovação, obtenha o seu estatuto utilizando a [API GET](/rest/api/searchmanagement/sharedprivatelinkresources/get).

`az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

Ou usando o ARMClient:

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

Se `properties.provisioningState` o recurso for `Succeeded` e `properties.status` `Approved` for, significa que o recurso de ligação privada partilhado é funcional e o indexante pode ser configurado para comunicar através do ponto final privado.

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

### <a name="step-3-configure-the-indexer-to-run-in-the-private-environment"></a>Passo 3: Configurar o indexante para funcionar em ambiente privado

> [!NOTE]
> Pode efetuar este passo antes da aprovação da ligação do ponto final privado. Até que a ligação de ponto final privado seja aprovada, qualquer indexante que tente comunicar com um recurso seguro (como a conta de armazenamento) acabará num estado de falha transitória. Novos indexantes não serão criados. Assim que a ligação de ponto final privado for aprovada, os indexantes podem aceder à conta de armazenamento privado.

1. [Crie uma fonte de dados](/rest/api/searchservice/create-data-source) que aponte para a conta de armazenamento segura e um recipiente apropriado dentro da conta de armazenamento. A imagem que se segue mostra este pedido no Carteiro.

   ![Screenshot mostrando a criação de uma fonte de dados na interface do utilizador do Carteiro.](media\search-indexer-howto-secure-access\create-ds.png )

1. Da mesma forma, [crie um índice](/rest/api/searchservice/create-index) e, opcionalmente, crie um [skillset](/rest/api/searchservice/create-skillset) utilizando a API REST.

1. [Crie um indexante](/rest/api/searchservice/create-indexer) que aponte para a fonte de dados, índice e skillset que criou no passo anterior. Além disso, force o indexante a funcionar no ambiente de execução privada, definindo a propriedade de configuração indexante `executionEnvironment` para `private` .

   ![Screenshot mostrando a criação de um indexante na interface de utilizador do Carteiro.](media\search-indexer-howto-secure-access\create-idr.png)

   Após a criação do indexante com sucesso, deverá começar a indexar o conteúdo da conta de armazenamento sobre a ligação ao ponto final privado. Pode monitorizar o estado do indexante utilizando a [API do Estado indexante](/rest/api/searchservice/get-indexer-status).

> [!NOTE]
> Se já tiver indexantes existentes, pode atualizá-los através da [API PUT](/rest/api/searchservice/create-indexer) definindo `executionEnvironment` o `private` .

## <a name="troubleshooting"></a>Resolução de problemas

- Se a sua criação de indexante falhar com uma mensagem de erro como "As credenciais de origem de dados são inválidas", significa que ou o estado da ligação privada do ponto final ainda não está *aprovado* ou a ligação não está funcional. Para remediar a questão: 
  * Obtenha o estado do recurso de ligação privada partilhada utilizando a [API GET](/rest/api/searchmanagement/sharedprivatelinkresources/get). Se o estado *for aprovado,* verifique o `properties.provisioningState` recurso. Se o estado aqui for `Incomplete` , isto significa que algumas das dependências subjacentes ao recurso não foram criadas. A reedição do `PUT` pedido de recriação do recurso de ligação privada partilhada deve corrigir o problema. Um reaprove de reaprovação pode ser necessário. Volte a verificar o estado do recurso para verificar se a questão está corrigida.

- Se criar o indexante sem definir a sua `executionEnvironment` propriedade, a criação pode ter sucesso, mas o seu histórico de execução mostrará que as corridas do indexante não têm sucesso. Para remediar a questão:
   * [Atualize o indexador](/rest/api/searchservice/update-indexer) para especificar o ambiente de execução.

- Se criou o indexante sem definir a `executionEnvironment` propriedade e funciona com sucesso, significa que a Azure Cognitive Search decidiu que o seu ambiente de execução é o ambiente *privado* específico do serviço de pesquisa. Isto pode mudar, dependendo dos recursos consumidos pelo indexante, da carga no serviço de pesquisa, e outros fatores, e pode falhar mais tarde. Para remediar a questão:
  * Recomendamos vivamente que desembarate a `executionEnvironment` propriedade para garantir que não `private` falhe no futuro.

[Quotas e limites](search-limits-quotas-capacity.md) determinam quantos recursos de ligação privada partilhados podem ser criados e dependem do SKU do serviço de pesquisa.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os pontos finais privados:

- [O que são pontos finais privados?](../private-link/private-endpoint-overview.md)
- [Configurações de DNS necessárias para pontos finais privados](../private-link/private-endpoint-dns.md)