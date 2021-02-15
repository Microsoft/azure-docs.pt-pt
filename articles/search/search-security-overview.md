---
title: Descrição geral da segurança
titleSuffix: Azure Cognitive Search
description: Conheça as funcionalidades de segurança na Azure Cognitive Search para proteger pontos finais, conteúdos e operações.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/04/2021
ms.custom: references_regions
ms.openlocfilehash: 46f2035e5f8409cd38faeb9c327b88b06fc7d7a0
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100097641"
---
# <a name="security-overview-for-azure-cognitive-search"></a>Visão geral de segurança para Azure Cognitive Search

Este artigo descreve as funcionalidades de segurança na Azure Cognitive Search que protegem o conteúdo e as operações.

Para pedidos de entrada feitos a um serviço de pesquisa, há uma progressão de medidas de segurança que protegem o ponto final do serviço de pesquisa: desde chaves API no pedido, a regras de entrada na firewall, a pontos finais privados que protegem totalmente o seu serviço da internet pública.

Para pedidos de saída feitos a outros serviços, o pedido predominante é feito por indexantes que lêem conteúdos de fontes externas. Pode fornecer credenciais na cadeia de ligação. Ou, você pode configurar uma identidade gerida para fazer pesquisa de um serviço de confiança ao aceder a dados de Azure Storage, Azure SQL, Cosmos DB, ou outras fontes de dados Azure. Uma identidade gerida é um substituto de credenciais ou chaves de acesso na ligação. Para obter mais informações sobre esta capacidade, consulte [Connect a uma fonte de dados utilizando uma identidade gerida.](search-howto-managed-identities-data-sources.md)

As operações de escrita para serviços externos são poucas: um serviço de pesquisa escreve para registar ficheiros, e escreverá para o Azure Storage ao criar lojas de conhecimento, enriquecimentos em cache persistentes e sessões de depuração persistentes. Outras chamadas de serviço ao serviço, como serviços cognitivos, são feitas na rede interna.

Veja este vídeo de ritmo acelerado para uma visão geral da arquitetura de segurança e de cada categoria de funcionalidades.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

## <a name="network-security"></a>Segurança da rede

<a name="service-access-and-authentication"></a>

As funcionalidades de segurança de entrada protegem o ponto final do serviço de pesquisa através do aumento dos níveis de segurança e complexidade. Em primeiro lugar, todos os pedidos requerem uma chave API para acesso autenticado. Em segundo lugar, pode configurar opcionalmente regras de firewall que limitam o acesso a endereços IP específicos. Para uma proteção avançada, uma terceira opção é permitir ao Azure Private Link proteger o seu ponto final de serviço de todo o tráfego de internet.

### <a name="public-access-using-api-keys"></a>Acesso público usando chaves API

Por predefinição, um serviço de pesquisa é acedido através da nuvem pública, utilizando a autenticação baseada em chaves para administração ou acesso de consulta ao ponto final do serviço de pesquisa. A submissão de uma chave válida é considerada prova de que o pedido é originário de uma entidade de confiança. A autenticação baseada em chaves é abrangida pela secção seguinte.

### <a name="configure-ip-firewalls"></a>Configure firewalls IP

Para controlar ainda mais o acesso ao seu serviço de pesquisa, pode criar regras de firewall de entrada que permitam o acesso a endereços IP específicos ou a uma série de endereços IP. Todas as ligações com o cliente devem ser feitas através de um endereço IP permitido, ou a ligação é negada.

:::image type="content" source="media/search-security-overview/inbound-firewall-ip-restrictions.png" alt-text="diagrama de arquitetura amostra para acesso restrito ip":::

Pode utilizar o portal para [configurar](service-configure-firewall.md)o acesso à entrada .

Em alternativa, pode utilizar as APIs de gestão REST. Começando pela versão API 2020-03-13, com o parâmetro [IpRule,](/rest/api/searchmanagement/services/createorupdate#iprule) pode restringir o acesso ao seu serviço identificando endereços IP, individualmente ou num intervalo, que pretende conceder acesso ao seu serviço de pesquisa.

### <a name="network-isolation-through-a-private-endpoint-no-internet-traffic"></a>Isolamento da rede através de um ponto final privado (sem tráfego de Internet)

Pode estabelecer um [ponto final privado](../private-link/private-endpoint-overview.md) para a Azure Cognitive Search permite que um cliente numa rede [virtual](../virtual-network/virtual-networks-overview.md) aceda de forma segura a dados num índice de pesquisa sobre um [Link Privado](../private-link/private-link-overview.md).

O ponto final privado utiliza um endereço IP a partir do espaço de endereço de rede virtual para ligações ao seu serviço de pesquisa. O tráfego de rede entre o cliente e o serviço de pesquisa atravessa a rede virtual e uma ligação privada na rede de espinha dorsal da Microsoft, eliminando a exposição da internet pública. Um VNET permite uma comunicação segura entre os recursos, com a sua rede no local, bem como a Internet.

:::image type="content" source="media/search-security-overview/inbound-private-link-azure-cog-search.png" alt-text="diagrama de arquitetura amostra para acesso privado de ponto final":::

Embora esta solução seja a mais segura, usar serviços adicionais é um custo adicional, por isso certifique-se de ter uma compreensão clara dos benefícios antes de mergulhar. ou mais informações sobre os custos, consulte [a página de preços.](https://azure.microsoft.com/pricing/details/private-link/) Para obter mais informações sobre como estes componentes funcionam em conjunto, veja o vídeo no topo deste artigo. A cobertura da opção de ponto final privado começa às 5:48 no vídeo. Para obter instruções sobre como configurar o ponto final, consulte [Criar um ponto de terminação privado para a pesquisa cognitiva do Azure](service-create-private-endpoint.md).

## <a name="authentication"></a>Autenticação

Para pedidos de entrada no serviço de pesquisa, a autenticação é através de uma [chave API obrigatória](search-security-api-keys.md) (uma cadeia composta por números e letras gerados aleatoriamente) que comprova que o pedido é de uma fonte fidedigna. A Pesquisa Cognitiva não suporta atualmente a autenticação do Azure Ative Directory para pedidos de entrada.

Os pedidos de saída feitos por um indexante estão sujeitos à autenticação pelo serviço externo. O subserviço indexante em Pesquisa Cognitiva pode ser feito um serviço de confiança no Azure, conectando-se a outros serviços usando uma identidade gerida. Para obter mais informações, consulte [Configurar uma ligação indexante a uma fonte de dados utilizando uma identidade gerida.](search-howto-managed-identities-data-sources.md)

## <a name="authorization"></a>Autorização

A Cognitive Search fornece diferentes modelos de autorização para gestão de conteúdos e gestão de serviços. 

### <a name="authorization-for-content-management"></a>Autorização para gestão de conteúdos

A autorização para conteúdos e operações relacionadas com o conteúdo é ou o acesso por escrito, conforme conferido através da [chave API](search-security-api-keys.md) fornecida no pedido. A chave API é um mecanismo de autenticação, mas também autoriza o acesso dependendo do tipo de chave API.

+ Tecla de administração (permite o acesso de leitura-escrita para criar operações de atualização de leitura-apagar no serviço de pesquisa), criada quando o serviço é a provisionado

+ Chave de consulta (permite o acesso apenas à recolha de documentos de um índice), criada como necessária e concebida para aplicações de clientes que emitem consultas

No código de aplicação, especifica o ponto final e uma chave API para permitir o acesso ao conteúdo e às opções. Um ponto final pode ser o próprio serviço, a recolha de índices, um índice específico, uma recolha de documentos ou um documento específico. Quando acorrentados, o ponto final, a operação (por exemplo, um pedido de criação ou atualização) e o nível de permissão (direitos completos ou apenas de leitura baseados na chave) constituem a fórmula de segurança que protege o conteúdo e as operações.

### <a name="controlling-access-to-indexes"></a>Controlo do acesso aos índices

Na Pesquisa Cognitiva Azure, um índice individual não é um objeto securável. Em vez disso, o acesso a um índice é determinado na camada de serviço (ler ou escrever acesso com base na chave API que fornece), juntamente com o contexto de uma operação.

Para acesso apenas à leitura, pode estruturar pedidos de consulta para ligar usando uma [chave de consulta](search-security-rbac.md), e incluir o índice específico utilizado pela sua app. Num pedido de consulta, não existe qualquer conceito de adesão de índices ou acesso a múltiplos índices simultaneamente, pelo que todos os pedidos visam um único índice por definição. Como tal, a construção do próprio pedido de consulta (uma chave mais um único índice-alvo) define o limite de segurança.

O acesso do administrador e do desenvolvedor aos índices é indiferenciado: ambos precisam de escrever acesso para criar, eliminar e atualizar objetos geridos pelo serviço. Qualquer pessoa com [uma chave de administração](search-security-rbac.md) do seu serviço pode ler, modificar ou eliminar qualquer índice no mesmo serviço. Para proteção contra a eliminação acidental ou maliciosa de índices, o seu controlo de origem interna para os ativos de código é o remédio para reverter uma eliminação ou modificação de índices indesejados. A Azure Cognitive Search tem falhas dentro do cluster para garantir a disponibilidade, mas não armazena ou executa o seu código proprietário usado para criar ou carregar índices.

Para soluções multitenancy que requerem limites de segurança ao nível do índice, essas soluções normalmente incluem um nível médio, que os clientes usam para lidar com o isolamento de índices. Para obter mais informações sobre o caso de utilização multitenante, consulte [padrões de design para aplicações SaaS multitenant e Pesquisa Cognitiva Azure](search-modeling-multitenant-saas-applications.md).

### <a name="controlling-access-to-documents"></a>Controlo do acesso a documentos

Se necessitar de controlo granular por utilizador sobre os resultados da pesquisa, pode construir filtros de segurança nas suas consultas, devolvendo documentos associados a uma determinada identidade de segurança. 

Conceptualmente equivalente a "segurança ao nível da linha", a autorização para conteúdos dentro do índice não é suportada de forma nativa utilizando funções predefinidas ou atribuições de funções que mapeiem para entidades no Azure Ative Directory. Quaisquer permissões de utilizador em dados em sistemas externos, como o Cosmos DB, não transferem esses dados como sendo indexados pela Pesquisa Cognitiva.

As soluções alternativas para soluções que requerem "segurança ao nível da linha" incluem a criação de um campo na fonte de dados que representa um grupo de segurança ou identidade do utilizador, e depois a utilização de filtros na Pesquisa Cognitiva para aparar seletivamente os resultados de pesquisa de documentos e conteúdos baseados em identidades. A tabela seguinte descreve duas abordagens para aparar os resultados da pesquisa de conteúdo não autorizado.

| Abordagem | Description |
|----------|-------------|
|[Aparar segurança com base em filtros de identidade](search-security-trimming-for-azure-search.md)  | Documenta o fluxo de trabalho básico para a implementação do controlo de acesso à identidade do utilizador. Cobre a adição de identificadores de segurança a um índice e, em seguida, explica a filtragem contra esse campo para cortar os resultados do conteúdo proibido. |
|[Aparar segurança com base nas identidades do Azure Ative Directory](search-security-trimming-for-azure-search-with-aad.md)  | Este artigo expande-se no artigo anterior, fornecendo passos para recuperar identidades do Azure Ative Directory (Azure AD), um dos [serviços gratuitos](https://azure.microsoft.com/free/) na plataforma cloud Azure. |

### <a name="authorization-for-service-management"></a>Autorização para gestão de serviços

As operações de gestão de serviços são autorizadas através do [controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md). O Azure RBAC é um sistema de autorização construído no [Azure Resource Manager](../azure-resource-manager/management/overview.md) para o fornecimento de recursos Azure. 

Na Pesquisa Cognitiva Azure, o Gestor de Recursos é utilizado para criar ou eliminar o serviço, gerir as teclas API e escalar o serviço. Como tal, as atribuições de funções da Azure determinarão quem pode executar essas tarefas, independentemente de estarem a utilizar o [portal](search-manage.md), [PowerShell,](search-manage-powershell.md)ou as [APIs de Rest de Gestão.](/rest/api/searchmanagement/search-howto-management-rest-api)

[Três funções básicas](search-security-rbac.md#management-tasks-by-role) são definidas para a administração do serviço de pesquisa. As atribuições de funções podem ser feitas usando qualquer metodologia suportada (portal, PowerShell, e assim por diante) e são honradas em todo o serviço. As funções de Proprietário e Contribuinte podem desempenhar uma variedade de funções administrativas. Pode atribuir a função Reader a utilizadores que apenas visualizam informações essenciais.

> [!Note]
> Utilizando mecanismos azures, pode bloquear uma subscrição ou recurso para evitar a eliminação acidental ou não não autorizada do seu serviço de pesquisa por utilizadores com direitos de administração. Para obter mais informações, consulte [os recursos de bloqueio para evitar a eliminação inesperada.](../azure-resource-manager/management/lock-resources.md)

<a name="encryption"></a>

## <a name="data-protection"></a>Proteção de dados

Na camada de armazenamento, a encriptação de dados é incorporada para todos os conteúdos geridos pelo serviço guardados no disco, incluindo índices, mapas de sinónimo, e as definições de indexadores, fontes de dados e skillsets. Opcionalmente, pode adicionar chaves geridas pelo cliente (CMK) para encriptação suplementar de conteúdo indexado. Para os serviços criados após 1 de agosto de 2020, a encriptação CMK estende-se a dados em discos temporários, para uma "dupla encriptação" de conteúdo indexado.

### <a name="data-in-transit"></a>Dados em trânsito

Na Pesquisa Cognitiva do Azure, a encriptação começa com ligações e transmissões, e estende-se a conteúdos armazenados no disco. Para serviços de pesquisa na internet pública, a Azure Cognitive Search ouve na porta HTTPS 443. Todas as ligações cliente-a-serviço utilizam encriptação TLS 1.2. As versões anteriores (1.0 ou 1.1) não são suportadas.

### <a name="encrypted-data-at-rest"></a>Dados encriptados em repouso

Para os dados tratados internamente pelo serviço de pesquisa, a tabela seguinte descreve os [modelos de encriptação](../security/fundamentals/encryption-models.md)de dados . Algumas funcionalidades, tais como a loja de conhecimento, o enriquecimento incremental e a indexação baseada em indexantes, lêem ou escrevem para estruturas de dados em outros Serviços Azure. Esses serviços têm os seus próprios níveis de suporte de encriptação separados da Azure Cognitive Search.

| Modelação | Chaves&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Requisitos&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Restrições | Aplica-se a |
|------------------|-------|-------------|--------------|------------|
| encriptação do lado do servidor | Chaves geridas pela Microsoft | Nenhum (incorporado) | Nenhum, disponível em todos os escalões, em todas as regiões, para conteúdos criados após 24 de janeiro de 2018. | Conteúdo (índices e mapas de sinónimo) e definições (indexantes, fontes de dados, skillsets) |
| encriptação do lado do servidor | chaves geridas pelo cliente | Azure Key Vault | Disponível em níveis faturantes, em todas as regiões, para conteúdos criados após janeiro de 2019. | Conteúdo (índices e mapas de sinónimo) em discos de dados |
| encriptação dupla do lado do servidor | chaves geridas pelo cliente | Azure Key Vault | Disponível em níveis faturantes, em regiões selecionadas, em serviços de pesquisa após 1 de agosto de 2020. | Conteúdo (índices e mapas de sinónimo) em discos de dados e discos temporários |

### <a name="service-managed-keys"></a>Chaves geridas pelo serviço

A encriptação gerida pelo serviço é uma operação interna da Microsoft, baseada na [Encriptação do Serviço de Armazenamento Azure,](../storage/common/storage-service-encryption.md)utilizando [encriptação AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits . Ocorre automaticamente em todos os indexantes, incluindo em atualizações incrementais a índices que não estão totalmente encriptados (criados antes de janeiro de 2018).

### <a name="customer-managed-keys-cmk"></a>Chaves geridas pelo cliente (CMK)

As chaves geridas pelo cliente requerem um serviço adicional de faturação, a Azure Key Vault, que pode estar numa região diferente, mas sob a mesma subscrição, que a Azure Cognitive Search. Ativar a encriptação CMK aumentará o tamanho do índice e irá degradar o desempenho da consulta. Com base em observações até à data, pode esperar um aumento de 30%-60% nos tempos de consulta, embora o desempenho real varie dependendo da definição de índice e tipos de consultas. Devido a este impacto de desempenho, recomendamos que apenas ative esta funcionalidade em índices que realmente o exijam. Para obter mais informações, consulte [as chaves de encriptação geridas pelo cliente em Azure Cognitive Search](search-security-manage-encryption-keys.md).

<a name="double-encryption"></a>

### <a name="double-encryption"></a>Encriptação dupla

Em Azure Cognitive Search, a dupla encriptação é uma extensão de CMK. Entende-se que seja encriptação dupla (uma vez por CMK, e novamente por chaves geridas pelo serviço), e abrangente no âmbito, abrangendo o armazenamento a longo prazo que é escrito para um disco de dados, e armazenamento de curto prazo escrito em discos temporários. A diferença entre a CMK antes de 1 de agosto de 2020 e depois, e o que faz da CMK uma dupla funcionalidade de encriptação na Azure Cognitive Search, é a encriptação adicional de dados em repouso em discos temporários.

A dupla encriptação está atualmente disponível em novos serviços que são criados nestas regiões após 1 de agosto:

+ E.U.A. Oeste 2
+ E.U.A. Leste
+ E.U.A. Centro-Sul
+ US Gov - Virginia
+ US Gov - Arizona

## <a name="security-management"></a>Gestão de segurança

### <a name="api-keys"></a>Chaves de API

A dependência da autenticação baseada em chaves da API significa que deve ter um plano para regenerar a chave de administração a intervalos regulares, de acordo com as melhores práticas de segurança da Azure. Há um máximo de duas chaves de administração por serviço de pesquisa. Para obter mais informações sobre a segurança e gestão das teclas API, consulte [Criar e gerir as teclas api.](search-security-api-keys.md)

#### <a name="activity-and-diagnostic-logs"></a>Registos de atividade e de diagnóstico

A Pesquisa Cognitiva não regista as identidades dos utilizadores, pelo que não é possível consultar registos de informações sobre um utilizador específico. No entanto, o serviço regista operações de atualização de leitura-leitura, que poderá ser capaz de se correlacionar com outros registos para compreender a agência de ações específicas.

Utilizando alertas e a infraestrutura de registo em Azure, pode captar picos de volume de consulta ou outras ações que se desviem das cargas de trabalho esperadas. Para obter mais informações sobre a configuração de registos, consulte [recolher e analisar os dados de registo](search-monitor-logs.md) e os [pedidos de consulta do Monitor.](search-monitor-queries.md)

### <a name="certifications-and-compliance"></a>Certificações e conformidade

A Azure Cognitive Search participa em auditorias regulares, e foi certificada contra uma série de padrões globais, regionais e específicos da indústria tanto para a nuvem pública como para o Governo de Azure. Para a lista completa, descarregue o papel branco [ **microsoft Azure Compliance Offers**](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) da página oficial dos relatórios da Auditoria.

Para o cumprimento, pode utilizar [a Azure Policy](../governance/policy/overview.md) para implementar as melhores práticas de alta segurança do [Azure Security Benchmark](../security/benchmarks/introduction.md). A Azure Security Benchmark é uma coleção de recomendações de segurança, codificadas em controlos de segurança que mapeiam para ações-chave que deve tomar para mitigar ameaças a serviços e dados. Existem atualmente 11 controlos de segurança, incluindo [Segurança de Rede,](../security/benchmarks/security-control-network-security.md) [Registo e Monitorização,](../security/benchmarks/security-control-logging-monitoring.md)e Proteção de [Dados](../security/benchmarks/security-control-data-protection.md) para citar alguns.

A Azure Policy é uma capacidade incorporada no Azure que o ajuda a gerir o cumprimento de vários padrões, incluindo os da Azure Security Benchmark. Para referências bem conhecidas, a Azure Policy fornece definições incorporadas que fornecem tanto critérios como uma resposta acccionável que aborda o incumprimento.

Para a pesquisa cognitiva Azure, existe atualmente uma definição incorporada. É para registo de diagnóstico. Com este incorporado, você pode atribuir uma política que identifique qualquer serviço de pesquisa que esteja faltando registo de diagnóstico, e depois liga-o. Para obter mais informações, consulte [os controlos de conformidade regulamentar da Política Azure para a pesquisa cognitiva Azure](security-controls-policy.md).

## <a name="see-also"></a>Ver também

+ [Noções básicas da segurança do Azure](../security/fundamentals/index.yml)
+ [Segurança Azure](https://azure.microsoft.com/overview/security)
+ [Centro de Segurança do Azure](../security-center/index.yml)