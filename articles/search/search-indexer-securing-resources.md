---
title: Indexante acesso a recursos protegidos
titleSuffix: Azure Cognitive Search
description: Visão geral conceptual das opções de segurança ao nível da rede para o acesso de dados Azure por indexadores em Azure Cognitive Search.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: bcb6e91bba367363385214806077146b1a24fe7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92503492"
---
# <a name="indexer-access-to-content-protected-by-azure-network-security-features-azure-cognitive-search"></a>Indexante acesso a conteúdos protegidos por funcionalidades de segurança da rede Azure (Azure Cognitive Search)

Os indexantes de Pesquisa Cognitiva Azure podem fazer chamadas de saída para vários recursos Azure durante a execução. Este artigo explica os conceitos por trás do acesso do indexante a conteúdos protegidos por firewalls IP, pontos finais privados ou outros mecanismos de segurança ao nível da rede Azure. Um indexante faz chamadas de saída em duas situações: ligar-se a fontes de dados durante a indexação e ligar-se ao código encapsulado através de um skillset. Uma lista de todos os tipos de recursos possíveis a que um indexante pode aceder numa corrida típica estão listados na tabela abaixo.

| Recurso | Finalidade dentro do indexante executado |
| --- | --- |
| Armazenamento Azure (bolhas, mesas, ADLS Gen 2) | Origem de dados |
| Armazenamento Azure (bolhas, mesas) | Skillsets (caching documentos enriquecidos e armazenar projeções de lojas de conhecimento) |
| Azure Cosmos DB (várias APIs) | Origem de dados |
| Base de Dados SQL do Azure | Origem de dados |
| SQL Server em máquinas virtuais do Azure | Origem de dados |
| Instância Gerida do SQL | Origem de dados |
| Funções do Azure | Anfitrião para habilidades de api web personalizadas |
| Serviços Cognitivos | Anexado ao skillset que será usado para faturar enriquecimento para além do limite de 20 documentos gratuitos |

> [!NOTE]
> O recurso do Serviço Cognitivo ligado a um skillset é utilizado para faturação, com base nos enriquecimentos realizados e escritos no índice de pesquisa. Não é utilizado para aceder às APIs dos Serviços Cognitivos. O acesso do oleoduto de enriquecimento de um indexante às APIs de Serviços Cognitivos ocorre através de um canal de comunicação interno seguro, onde os dados são fortemente encriptados em trânsito e nunca são armazenados em repouso.

Os clientes podem garantir estes recursos através de vários mecanismos de isolamento de rede oferecidos pela Azure. Com exceção de um recurso de Serviço Cognitivo, os indexantes têm capacidade limitada para aceder a todos os outros recursos, mesmo que estejam isolados em rede, delineados na tabela abaixo.

| Recurso | Restrição IP | Ponto final privado |
| --- | --- | ---- |
| Armazenamento Azure (bolhas, mesas, ADLS Gen 2) | Suportado apenas se a conta de armazenamento e o serviço de pesquisa estiverem em diferentes regiões | Suportado |
| Azure Cosmos DB - SQL API | Suportado | Suportado |
| Azure Cosmos DB - Cassandra, Mongo e Gremlin API | Suportado | Não suportado |
| Base de Dados SQL do Azure | Suportado | Suportado |
| SQL Server em máquinas virtuais do Azure | Suportado | N/D |
| Instância Gerida do SQL | Suportado | N/D |
| Funções do Azure | Suportado | Suportado, apenas para certos níveis de funções Azure |

> [!NOTE]
> Além das opções listadas acima, para contas de Armazenamento Azure seguras em rede, os clientes podem aproveitar o facto de que a Azure Cognitive Search é um [serviço microsoft de confiança.](../storage/common/storage-network-security.md#trusted-microsoft-services) Isto significa que um serviço de pesquisa específico pode contornar as restrições de rede virtual ou IP na conta de armazenamento e pode aceder aos dados na conta de armazenamento, se o controlo de acesso adequado baseado em funções estiver ativado na conta de armazenamento. Para obter mais informações, consulte [as ligações Indexer utilizando a exceção de serviço fidedigno](search-indexer-howto-access-trusted-service-exception.md). Esta opção pode ser utilizada em vez da rota de restrição IP, caso a conta de armazenamento ou o serviço de pesquisa não possam ser transferidos para uma região diferente.

Ao escolher qual o mecanismo de acesso seguro que um indexante deve utilizar, considere os seguintes constrangimentos:

- Um indexante não pode ligar-se a um [ponto final de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md). Os pontos finais públicos com credenciais, pontos finais privados, serviço de confiança e endereçamento ip são as únicas metodologias suportadas para ligações indexantes.
- Um serviço de pesquisa não pode ser a ser ademedido numa rede virtual específica, funcionando de forma nativa numa máquina virtual. Esta funcionalidade não será oferecida pela Azure Cognitive Search.
- Quando os indexantes utilizam pontos finais privados (de saída) para aceder a recursos, podem ser aplicados [encargos adicionais de ligação privada.](https://azure.microsoft.com/pricing/details/search/)

## <a name="indexer-execution-environment"></a>Ambiente de execução do indexante

Os indexantes de Pesquisa Cognitiva Azure são capazes de extrair eficientemente conteúdo de fontes de dados, adicionando enriquecimentos ao conteúdo extraído, gerando projeções opcionalmente antes de escrever os resultados para o índice de pesquisa. Dependendo do número de responsabilidades atribuídas a um indexante, pode funcionar num de dois ambientes:

- Um ambiente privado para um serviço de pesquisa específico. Os indexantes que correm nesses ambientes partilham recursos com outras cargas de trabalho (tais como outras cargas de trabalho de indexação ou consulta iniciadas pelo cliente). Normalmente, apenas os indexantes que executam a indexação baseada em texto (por exemplo, não usam um skillset) funcionam neste ambiente.
- Um ambiente multi-inquilino que acolhe indexantes que são intensivos em recursos, como aqueles com habilidades. Este ambiente é utilizado para descarregar o processamento computacionalmente intensivo, deixando recursos específicos do serviço disponíveis para operações de rotina. Este ambiente multi-inquilino é gerido e garantido pela Microsoft, sem custos adicionais para o cliente.

Para qualquer execução indexante, a Azure Cognitive Search determina o melhor ambiente para executar o indexante. Se estiver a utilizar uma firewall IP para controlar o acesso aos recursos do Azure, conhecer ambientes de execução irá ajudá-lo a criar uma gama de IP que inclua ambos.

## <a name="granting-access-to-indexer-ip-ranges"></a>Concessão de acesso a gamas IP indexantes

Se o recurso a que o seu indexante está a tentar aceder se limita a apenas um determinado conjunto de gamas de IP, então precisa expandir o conjunto para incluir as possíveis gamas de IP a partir das quais um pedido indexante pode ter origem. Como acima referido, existem dois ambientes possíveis em que os indexantes funcionam e a partir dos quais os pedidos de acesso podem ter origem. Terá de adicionar os endereços IP de **ambos os** ambientes para o acesso do indexante ao trabalho.

- Para obter o endereço IP do serviço de pesquisa ambiente privado específico, `nslookup` (ou `ping` ) o nome de domínio totalmente qualificado (FQDN) do seu serviço de pesquisa. Por exemplo, o FQDN de um serviço de pesquisa na nuvem pública seria `<service-name>.search.windows.net` . Esta informação está disponível no portal Azure.
- Os endereços IP dos ambientes multi-inquilinos estão disponíveis através da `AzureCognitiveSearch` etiqueta de serviço. [As tags de serviço Azure](../virtual-network/service-tags-overview.md) têm uma gama publicada de endereços IP para cada serviço - este está disponível através de uma [API de descoberta (pré-visualização)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) ou um [ficheiro JSON descarregado.](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) Em qualquer dos casos, as gamas IP são discriminadas por região - só pode escolher as gamas IP atribuídas para a região em que o seu serviço de pesquisa é austetado.

Para determinadas fontes de dados, a própria etiqueta de serviço pode ser utilizada diretamente em vez de enumerar a lista de intervalos IP (o endereço IP do serviço de pesquisa ainda precisa de ser usado explicitamente). Estas fontes de dados restringem o acesso através da criação de uma [regra do Grupo](../virtual-network/network-security-groups-overview.md)de Segurança de Rede , que suporta de forma nativa a adição de uma etiqueta de serviço, ao contrário das regras ip, como as oferecidas pela Azure Storage, Cosmos DB, Azure SQL, etc. As fontes de dados que suportam a capacidade de utilizar a etiqueta de `AzureCognitiveSearch` serviço diretamente para além do endereço IP do serviço de pesquisa são:

- [Servidor SQL em máquinas virtuais Azure](./search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md#restrict-access-to-the-azure-cognitive-search)

- [Instâncias Geridas SQL](./search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md#verify-nsg-rules)

Para obter mais informações sobre esta opção de conectividade, consulte [as ligações indexantes através de uma firewall IP](search-indexer-howto-access-ip-restricted.md).

## <a name="granting-access-via-private-endpoints"></a>Concessão de acesso através de pontos finais privados

Os indexantes podem utilizar [pontos finais privados](../private-link/private-endpoint-overview.md) para aceder a recursos, acesso aos quais estão bloqueados quer para selecionar redes virtuais, quer para não terem qualquer acesso público.
Esta funcionalidade apenas se encontra disponível em serviços de pesquisa faturada, com limites no número de pontos finais privados que serão criados. Para mais informações, consulte [os limites de Serviço.](search-limits-quotas-capacity.md#shared-private-link-resource-limits)

### <a name="step-1-create-a-private-endpoint-to-the-secure-resource"></a>Passo 1: Criar um ponto final privado para o recurso seguro

Os clientes devem ligar para a operação de gestão de pesquisa, [a CreateOrUpdate API](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) num **recurso de ligação privada partilhada,** de forma a criar uma ligação privada ao seu recurso seguro (por exemplo, uma conta de armazenamento). O tráfego que passa por esta ligação de ponto final privado (outbound) terá origem apenas na rede virtual que está no ambiente específico de execução do indexante "privado" do serviço de pesquisa.

A Azure Cognitive Search validará que os chamadores desta API têm permissões Azure RBAC para aprovar pedidos de ligação de ponto final privado ao recurso seguro. Por exemplo, se solicitar uma ligação de ponto final privado a uma conta de armazenamento com permissões apenas de leitura, esta chamada será rejeitada.

### <a name="step-2-approve-the-private-endpoint-connection"></a>Passo 2: Aprovar a ligação de ponto final privado

Quando a operação (assíncronos) que cria um recurso de ligação privada partilhada estiver concluída, uma ligação de ponto final privado será criada num estado "pendente". Ainda não há tráfego sobre a ligação.
Espera-se então que o cliente localize este pedido no seu recurso seguro e o "aprove". Normalmente, isto pode ser feito através do portal Azure ou através da [API REST](/rest/api/virtualnetwork/privatelinkservices/updateprivateendpointconnection).

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

Estes passos são descritos com maior detalhe nas [ligações indexantes através de um ponto final privado](search-indexer-howto-access-private.md).
Uma vez que tenha um ponto final privado aprovado para um recurso, os indexantes que estão definidos para serem *privados* tentam obter acesso através da ligação de ponto final privado.

### <a name="limits"></a>Limites

Para garantir o melhor desempenho e estabilidade do serviço de pesquisa, são impostas restrições (por nível de serviço de pesquisa) nas seguintes dimensões:

- Os tipos de indexantes que podem ser definidos como *privados.*
- O número de recursos de ligação privada partilhados que podem ser criados.
- O número de tipos de recursos distintos para os quais podem ser criados recursos de ligação privada partilhados.

Estes limites estão documentados nos [limites de serviço.](search-limits-quotas-capacity.md)

## <a name="next-steps"></a>Passos seguintes

- [Ligações indexantes através de firewalls IP](search-indexer-howto-access-ip-restricted.md)
- [Ligações indexantes utilizando a exceção de serviço fidedigno](search-indexer-howto-access-trusted-service-exception.md)
- [Ligações indexantes a um ponto final privado](search-indexer-howto-access-private.md)