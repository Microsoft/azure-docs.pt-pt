---
title: Aceder recursos indexantes de forma segura
titleSuffix: Azure Cognitive Search
description: Visão geral conceptual das opções de segurança ao nível da rede para o acesso de dados Azure por indexadores em Azure Cognitive Search.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 85446847e8ad77bc83eea657ab17268839e0b231
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91949824"
---
# <a name="indexer-access-to-data-sources-using-azure-network-security-features"></a>Indexer acesso a fontes de dados usando funcionalidades de segurança da rede Azure

Os indexantes de Pesquisa Cognitiva Azure podem fazer chamadas de saída para vários recursos Azure durante a execução. Este artigo explica os conceitos por trás do acesso do indexante aos recursos quando esses recursos são protegidos por firewalls IP, pontos finais privados e outros mecanismos de segurança ao nível da rede. Os possíveis tipos de recursos a que um indexante pode aceder numa corrida típica estão listados na tabela abaixo.

| Recurso | Finalidade dentro do indexante executado |
| --- | --- |
| Armazenamento Azure (bolhas, mesas, ADLS Gen 2) | Origem de dados |
| Armazenamento Azure (bolhas, mesas) | Skillsets (caching documentos enriquecidos e armazenar projeções de lojas de conhecimento) |
| Azure Cosmos DB (várias APIs) | Origem de dados |
| Base de Dados SQL do Azure | Origem de dados |
| Servidor SQL em Azure IaaS VMs | Origem de dados |
| Instâncias geridas SQL | Origem de dados |
| Funções do Azure | Anfitrião para habilidades de api web personalizadas |
| Serviços Cognitivos | Anexado ao skillset que será usado para faturar enriquecimento para além do limite de 20 documentos gratuitos |

> [!NOTE]
> O recurso de serviço cognitivo ligado a um skillset é utilizado para faturação, com base nos enriquecimentos realizados e escritos no índice de pesquisa. Não é utilizado para aceder às APIs dos Serviços Cognitivos. O acesso do oleoduto de enriquecimento de um indexante às APIs de Serviços Cognitivos ocorre através de um canal de comunicação seguro, onde os dados são fortemente encriptados em trânsito e nunca são armazenados em repouso.

Os clientes podem garantir estes recursos através de vários mecanismos de isolamento de rede oferecidos pela Azure. Com exceção do recurso de serviço cognitivo, os indexantes têm capacidade limitada para aceder a todos os outros recursos, mesmo que estejam isolados da rede delineados na tabela abaixo.

| Recurso | Restrição IP | Ponto final privado |
| --- | --- | ---- |
| Armazenamento azul (bolhas, mesas, ADLS Gen 2) | Suportado apenas se a conta de armazenamento e o serviço de pesquisa estiverem em diferentes regiões | Suportado |
| Azure Cosmos DB - SQL API | Suportado | Suportado |
| Azure Cosmos DB - Cassandra, Mongo e Gremlin API | Suportado | Não suportado |
| Base de Dados SQL do Azure | Suportado | Suportado |
| Servidor SQL em Azure IaaS VMs | Suportado | N/D |
| Instâncias geridas SQL | Suportado | N/D |
| Funções do Azure | Suportado | Suportado, apenas para certas funções SKUs de Azure |

> [!NOTE]
> Além das opções listadas acima, para contas de armazenamento Azure seguras na rede, os clientes podem aproveitar o facto de que a Azure Cognitive Search é um [serviço de confiança da Microsoft.](../storage/common/storage-network-security.md#trusted-microsoft-services) Isto significa que um serviço de pesquisa específico pode contornar as restrições de rede virtual ou IP na conta de armazenamento e pode aceder aos dados na conta de armazenamento, se o controlo de acesso baseado em funções adequado estiver ativado na conta de armazenamento. Os detalhes estão disponíveis no [como orientar.](search-indexer-howto-access-trusted-service-exception.md) Esta opção pode ser utilizada em vez da rota de restrição IP, caso a conta de armazenamento ou o serviço de pesquisa não possam ser transferidos para uma região diferente.

Ao escolher qual o mecanismo de acesso seguro que um indexante deve utilizar, considere os seguintes constrangimentos:

- [Os pontos finais de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) não serão suportados para qualquer recurso Azure.
- Um serviço de pesquisa não pode ser ateado numa rede virtual específica - esta funcionalidade não será oferecida pela Azure Cognitive Search.
- Quando os indexantes utilizam pontos finais privados (de saída) para aceder a recursos, podem ser aplicados [encargos adicionais de ligação privada.](https://azure.microsoft.com/pricing/details/search/)

## <a name="indexer-execution-environment"></a>Ambiente de execução do indexante

Os indexantes de Pesquisa Cognitiva Azure são capazes de extrair eficientemente conteúdo de fontes de dados, adicionando enriquecimentos ao conteúdo extraído, gerando projeções opcionalmente antes de escrever os resultados para o índice de pesquisa. Dependendo do número de responsabilidades atribuídas a um indexante, pode funcionar num de dois ambientes:

- Um ambiente privado para um serviço de pesquisa específico. Os indexantes que correm nesses ambientes partilham recursos com outras cargas de trabalho (como outros clientes iniciados indexando ou consultando carga de trabalho). Normalmente, apenas os indexantes que não requerem muitos recursos (por exemplo, não usam um skillset) são executados neste ambiente.
- Um ambiente multi-inquilino que acolhe indexantes com fome de recursos, como os que têm um skillset. Os recursos esfomeados de recursos funcionam neste ambiente para oferecer um desempenho ideal, garantindo ao mesmo tempo que os recursos do serviço de pesquisa estão disponíveis para outras cargas de trabalho. Este ambiente multi-inquilino é gerido e protegido pela Azure Cognitive Search, sem custos adicionais para o cliente.

Para qualquer execução indexante, a Azure Cognitive Search determina o melhor ambiente para executar o indexante.

## <a name="granting-access-to-indexer-ip-ranges"></a>Concessão de acesso a gamas IP indexantes

Se o recurso a que o seu indexante está a tentar aceder se limita a apenas um determinado conjunto de gamas de IP, então precisa expandir o conjunto para incluir as possíveis gamas de IP a partir das quais um pedido indexante pode ter origem. Como acima referido, existem dois ambientes possíveis em que os indexantes funcionam e a partir dos quais os pedidos de acesso podem ter origem. Terá de adicionar os endereços IP de __ambos os__ ambientes para o acesso do indexante ao trabalho.

- Para obter o endereço IP do serviço de pesquisa ambiente privado específico, `nslookup` (ou `ping` ) o nome de domínio totalmente qualificado (FQDN) do seu serviço de pesquisa. O FQDN de um serviço de pesquisa na nuvem pública, por exemplo, seria `<service-name>.search.windows.net` . Esta informação está disponível no portal Azure.
- Os endereços IP dos ambientes multi-inquilinos estão disponíveis através da `AzureCognitiveSearch` etiqueta de serviço. [As tags de serviço Azure](../virtual-network/service-tags-overview.md) têm uma gama publicada de endereços IP para cada serviço - este está disponível através de uma [API de descoberta (pré-visualização)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) ou um [ficheiro JSON descarregado.](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) Em qualquer dos casos, as gamas IP são discriminadas por região - só pode escolher as gamas IP atribuídas para a região em que o seu serviço de pesquisa é austetado.

Para determinadas fontes de dados, a própria etiqueta de serviço pode ser utilizada diretamente em vez de enumerar a lista de intervalos IP (o endereço IP do serviço de pesquisa ainda precisa de ser usado explicitamente). Estas fontes de dados restringem o acesso através da criação de uma regra do Grupo de Segurança de [Rede,](../virtual-network/network-security-groups-overview.md)que suporta de forma nativa a adição de uma etiqueta de serviço, ao contrário das regras ip, como as oferecidas pelo Azure Storage, CosmosDB, Azure SQL, etc., As fontes de dados que suportam a capacidade de utilizar `AzureCognitiveSearch` a etiqueta de serviço diretamente para além do endereço IP do serviço de pesquisa são:

- [Servidor SQL em IaaS VMs](./search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md#restrict-access-to-the-azure-cognitive-search)

- [SQL geriu instâncias](./search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md#verify-nsg-rules)

Os detalhes são descritos no [como guiar.](search-indexer-howto-access-ip-restricted.md)

## <a name="granting-access-via-private-endpoints"></a>Concessão de acesso através de pontos finais privados

Os indexantes podem utilizar [pontos finais privados](../private-link/private-endpoint-overview.md) para aceder a recursos, acesso aos quais estão bloqueados quer para selecionar redes virtuais, quer para não terem qualquer acesso público.
Esta funcionalidade apenas se encontra disponível para serviços pagos, com limites no número de pontos finais privados que serão criados. Os detalhes sobre os limites estão documentados na [página de limites de pesquisa do Azure](search-limits-quotas-capacity.md).

### <a name="step-1-create-a-private-endpoint-to-the-secure-resource"></a>Passo 1: Criar um ponto final privado para o recurso seguro

Os clientes devem ligar para a operação de gestão de pesquisa, [criar ou atualizar recursos de *ligação privada partilhada* API](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) para criar uma ligação de ponto final privado ao seu recurso seguro (por exemplo, uma conta de armazenamento). O tráfego que passa por esta ligação de ponto final privado (outbound) terá origem apenas na rede virtual que está no ambiente específico de execução do indexante "privado" do serviço de pesquisa.

A Azure Cognitive Search validará que os chamadores desta API têm permissões para aprovar pedidos de ligação de ponto final privado ao recurso seguro. Por exemplo, se solicitar uma ligação de ponto final privado a uma conta de armazenamento a que não tem acesso, esta chamada será rejeitada.

### <a name="step-2-approve-the-private-endpoint-connection"></a>Passo 2: Aprovar a ligação de ponto final privado

Quando a operação (assíncronos) que cria um recurso de ligação privada partilhada estiver concluída, uma ligação de ponto final privado será criada num estado "pendente". Ainda não há tráfego sobre a ligação.
Espera-se então que o cliente localize este pedido no seu recurso seguro e o "aprove". Normalmente, isto pode ser feito através do Portal ou através da [API REST](/rest/api/virtualnetwork/privatelinkservices/updateprivateendpointconnection).

### <a name="step-3-force-indexers-to-run-in-the-private-environment"></a>Passo 3: Os indexantes da força a funcionar em ambiente "privado"

Um ponto final privado aprovado permite que chamadas de saída do serviço de pesquisa para um recurso que tenha alguma forma de restrições de acesso ao nível da rede (por exemplo, uma fonte de dados de conta de armazenamento que está configurada para ser acedida apenas a partir de determinadas redes virtuais) para ter sucesso.
Isto significa que qualquer indexante que seja capaz de chegar a essa fonte de dados sobre o ponto final privado será bem sucedido.
Se o ponto final privado não for aprovado, ou se o indexante não utilizar a ligação de ponto final privado, a execução do indexante acabará em `transientFailure` .

Para permitir que os indexantes acedam aos recursos através de ligações privadas de ponto final, é obrigatório definir `executionEnvironment` o indexante `"Private"` para garantir que todas as execuções indexantes serão capazes de utilizar o ponto final privado. Isto porque os pontos finais privados são aprovisionados dentro do ambiente específico do serviço de pesquisa privado.

```json
    {
      "name" : "myindexer",
      ... other indexer properties
      "parameters" : {
          ... other parameters
          "configuration" : {
            ... other configuration properties
            "executionEnvironment": "Private"
          }
        }
    }
```

Estes passos são descritos com maior detalhe na [forma de orientar.](search-indexer-howto-access-private.md)
Uma vez que tenha um ponto final privado aprovado para um recurso, os indexantes que estão definidos para serem *privados* tentam obter acesso através da ligação de ponto final privado.

### <a name="limits"></a>Limites

Para garantir o melhor desempenho e estabilidade do serviço de pesquisa, são impostas restrições (pelo serviço de pesquisa SKU) nas seguintes dimensões:

- Os tipos de indexantes que podem ser definidos como *privados.*
- O número de recursos de ligação privada partilhados que podem ser criados.
- O número de tipos de recursos distintos para os quais podem ser criados recursos de ligação privada partilhados.

Estes limites estão documentados nos [limites de serviço.](search-limits-quotas-capacity.md)