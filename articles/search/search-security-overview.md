---
title: Descrição geral da segurança
titleSuffix: Azure Cognitive Search
description: Conheça as funcionalidades de segurança na Azure Cognitive Search para proteger pontos finais, conteúdos e operações.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/22/2020
ms.custom: references_regions
ms.openlocfilehash: 49364681f0c5b4b6cc4d5f20778edb61e9f6f5b3
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98695785"
---
# <a name="security-in-azure-cognitive-search---overview"></a>Segurança em Azure Cognitive Search - visão geral

Este artigo descreve as funcionalidades de segurança na Azure Cognitive Search que protegem o conteúdo e as operações.

+ Na camada de armazenamento, a encriptação de dados é incorporada para todos os conteúdos geridos pelo serviço guardados no disco, incluindo índices, mapas de sinónimo, e as definições de indexadores, fontes de dados e skillsets. Opcionalmente, pode adicionar chaves geridas pelo cliente (CMK) para encriptação suplementar de conteúdo indexado. Para os serviços criados após 1 de agosto de 2020, a encriptação CMK estende-se a dados em discos temporários, para uma "dupla encriptação" de conteúdo indexado.

+ A segurança de entrada refere-se a proteções no ponto final do serviço de pesquisa em níveis crescentes de segurança: desde chaves API a pedido, a regras de entrada na firewall, a pontos finais privados que protegem totalmente o seu serviço da internet pública.

+ A segurança de saída diz respeito a indexantes que retiram conteúdo de fontes externas. Para pedidos de saída, crie uma identidade gerida para fazer uma pesquisa de um serviço de confiança ao aceder a dados da Azure Storage, Azure SQL, Cosmos DB ou outras fontes de dados Azure. Uma identidade gerida é um substituto de credenciais ou chaves de acesso na ligação. A segurança de saída não está abrangida por este artigo. Para obter mais informações sobre esta capacidade, consulte [Connect a uma fonte de dados utilizando uma identidade gerida.](search-howto-managed-identities-data-sources.md)

Veja este vídeo de ritmo acelerado para uma visão geral da arquitetura de segurança e de cada categoria de funcionalidades.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

<a name="encryption"></a>

## <a name="encrypted-transmissions-and-storage"></a>Transmissões encriptadas e armazenamento

Na Pesquisa Cognitiva do Azure, a encriptação começa com ligações e transmissões, e estende-se a conteúdos armazenados no disco. Para serviços de pesquisa na internet pública, a Azure Cognitive Search ouve na porta HTTPS 443. Todas as ligações cliente-a-serviço utilizam encriptação TLS 1.2. As versões anteriores (1.0 ou 1.1) não são suportadas.

:::image type="content" source="media/search-security-overview/encryption-at-rest-cmk.png" alt-text="diagrama que retrata diferentes tipos de segurança em cada nível de envolvimento de serviço":::

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

Em Azure Cognitive Search, a dupla encriptação é uma extensão de CMK. Entende-se que seja uma encriptação dupla (uma vez por CMK, e novamente por chaves geridas pelo serviço), e abrangente no âmbito, abrangendo o armazenamento de longo prazo que é escrito para um disco de dados, e armazenamento de curto prazo escrito em discos temporários. A diferença entre a CMK antes de 1 de agosto de 2020 e depois, e o que faz da CMK uma dupla funcionalidade de encriptação na Azure Cognitive Search, é a encriptação adicional de dados em repouso em discos temporários.

A dupla encriptação está atualmente disponível em novos serviços que são criados nestas regiões após 1 de agosto:

+ E.U.A. Oeste 2
+ E.U.A. Leste
+ E.U.A. Centro-Sul
+ US Gov - Virginia
+ US Gov - Arizona

<a name="service-access-and-authentication"></a>

## <a name="inbound-security-and-endpoint-protection"></a>Segurança de entrada e proteção de pontos finais

As funcionalidades de segurança de entrada protegem o ponto final do serviço de pesquisa através do aumento dos níveis de segurança e complexidade. Em primeiro lugar, todos os pedidos requerem uma chave API para acesso autenticado. Em segundo lugar, pode configurar opcionalmente regras de firewall que limitam o acesso a endereços IP específicos. Para uma proteção avançada, uma terceira opção é permitir ao Azure Private Link proteger o seu ponto final de serviço de todo o tráfego de internet.

### <a name="public-access-using-api-keys"></a>Acesso público usando chaves API

Por predefinição, um serviço de pesquisa é acedido através da nuvem pública, utilizando a autenticação baseada em chaves para administração ou acesso de consulta ao ponto final do serviço de pesquisa. Uma [chave API](search-security-rbac.md) é uma cadeia composta por números e letras gerados aleatoriamente. O tipo de chave (administração ou consulta) determina o nível de acesso. A submissão de uma chave válida é considerada prova de que o pedido é originário de uma entidade de confiança.

Existem dois níveis de acesso ao seu serviço de pesquisa, habilitados pelas seguintes teclas API:

+ Tecla de administração (permite o acesso de leitura-escrita para criar operações [de atualização de leitura-apagar](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) no serviço de pesquisa)

+ Chave de consulta (permite o acesso apenas à recolha de documentos de um índice)

*As teclas de administração* são criadas quando o serviço é prestado. Existem duas teclas de administração, designadas como *primárias* e *secundárias* para mantê-las direitas, mas na verdade são permutáveis. Cada serviço tem duas teclas de administração para que possa rolar uma sem perder o acesso ao seu serviço. Pode [regenerar a chave de administração](search-security-api-keys.md#regenerate-admin-keys) periodicamente por boas práticas de segurança Azure, mas não pode adicionar à contagem total de chave de administração. Há um máximo de duas chaves de administração por serviço de pesquisa.

*As chaves de consulta* são criadas conforme necessário e são concebidas para aplicações de clientes que emitem consultas. Pode criar até 50 chaves de consulta. No código de aplicação, especifica o URL de pesquisa e uma chave api de consulta para permitir o acesso apenas à recolha de documentos de um índice específico. Juntos, o ponto final, uma chave API para acesso apenas de leitura, e um índice-alvo definem o âmbito e o nível de acesso da ligação a partir da aplicação do seu cliente.

A autenticação é necessária em cada pedido, quando cada pedido é composto por uma chave obrigatória, uma operação e um objeto. Quando acorrentados, os dois níveis de autorização (completos ou apenas de leitura) mais o contexto (por exemplo, uma operação de consulta num índice) são suficientes para proporcionar uma segurança de todo o espectro nas operações de serviço. Para obter mais informações sobre as teclas, consulte [Criar e gerir as teclas api](search-security-api-keys.md).

### <a name="ip-restricted-access"></a>Acesso restrito ao IP

Para controlar ainda mais o acesso ao seu serviço de pesquisa, pode criar regras de firewall de entrada que permitam o acesso a endereços IP específicos ou a uma série de endereços IP. Todas as ligações com o cliente devem ser feitas através de um endereço IP permitido, ou a ligação é negada.

:::image type="content" source="media/search-security-overview/inbound-firewall-ip-restrictions.png" alt-text="diagrama de arquitetura amostra para acesso restrito ip":::

Pode utilizar o portal para [configurar](service-configure-firewall.md)o acesso à entrada .

Em alternativa, pode utilizar as APIs de gestão REST. Começando pela versão API 2020-03-13, com o parâmetro [IpRule,](/rest/api/searchmanagement/services/createorupdate#iprule) pode restringir o acesso ao seu serviço identificando endereços IP, individualmente ou num intervalo, que pretende conceder acesso ao seu serviço de pesquisa.

### <a name="private-endpoint-no-internet-traffic"></a>Ponto final privado (sem tráfego de Internet)

Um [ponto final privado](../private-link/private-endpoint-overview.md) para a Azure Cognitive Search permite que um cliente numa rede [virtual](../virtual-network/virtual-networks-overview.md) aceda de forma segura a dados num índice de pesquisa sobre um Link [Privado](../private-link/private-link-overview.md).

O ponto final privado utiliza um endereço IP a partir do espaço de endereço de rede virtual para ligações ao seu serviço de pesquisa. O tráfego de rede entre o cliente e o serviço de pesquisa atravessa a rede virtual e uma ligação privada na rede de espinha dorsal da Microsoft, eliminando a exposição da internet pública. Um VNET permite uma comunicação segura entre os recursos, com a sua rede no local, bem como a Internet.

:::image type="content" source="media/search-security-overview/inbound-private-link-azure-cog-search.png" alt-text="diagrama de arquitetura amostra para acesso privado de ponto final":::

Embora esta solução seja a mais segura, usar serviços adicionais é um custo adicional, por isso certifique-se de ter uma compreensão clara dos benefícios antes de mergulhar. ou mais informações sobre os custos, consulte [a página de preços.](https://azure.microsoft.com/pricing/details/private-link/) Para obter mais informações sobre como estes componentes funcionam em conjunto, veja o vídeo no topo deste artigo. A cobertura da opção de ponto final privado começa às 5:48 no vídeo. Para obter instruções sobre como configurar o ponto final, consulte [Criar um ponto de terminação privado para a pesquisa cognitiva do Azure](service-create-private-endpoint.md).

## <a name="index-access"></a>Acesso a índices

Na Pesquisa Cognitiva Azure, um índice individual não é um objeto securável. Em vez disso, o acesso a um índice é determinado na camada de serviço (ler ou escrever acesso ao serviço), juntamente com o contexto de uma operação.

Para o acesso ao utilizador final, pode estruturar pedidos de consulta para se conectar usando uma [chave de consulta](search-security-rbac.md), que faz qualquer pedido apenas de leitura, e incluir o índice específico usado pela sua app. Num pedido de consulta, não existe qualquer conceito de adesão de índices ou acesso a múltiplos índices simultaneamente, pelo que todos os pedidos visam um único índice por definição. Como tal, a construção do próprio pedido de consulta (uma chave mais um único índice-alvo) define o limite de segurança.

O acesso do administrador e do desenvolvedor aos índices é indiferenciado: ambos precisam de escrever acesso para criar, eliminar e atualizar objetos geridos pelo serviço. Qualquer pessoa com [uma chave de administração](search-security-rbac.md) do seu serviço pode ler, modificar ou eliminar qualquer índice no mesmo serviço. Para proteção contra a eliminação acidental ou maliciosa de índices, o seu controlo de origem interna para os ativos de código é o remédio para reverter uma eliminação ou modificação de índices indesejados. A Azure Cognitive Search tem falhas dentro do cluster para garantir a disponibilidade, mas não armazena ou executa o seu código proprietário usado para criar ou carregar índices.

Para soluções multitenancy que requerem limites de segurança ao nível do índice, essas soluções normalmente incluem um nível médio, que os clientes usam para lidar com o isolamento de índices. Para obter mais informações sobre o caso de utilização multitenante, consulte [padrões de design para aplicações SaaS multitenant e Pesquisa Cognitiva Azure](search-modeling-multitenant-saas-applications.md).

## <a name="user-access"></a>Acesso do utilizador

A forma como um utilizador acede a um índice e a outros objetos é determinada pelo tipo de chave API no pedido. A maioria dos desenvolvedores cria e atribui chaves de [consulta](search-security-api-keys.md) para pedidos de pesquisa do lado do cliente. Uma chave de consulta concede acesso apenas de leitura a conteúdos pesmáveis dentro do índice.

Se necessitar de controlo granular por utilizador sobre os resultados da pesquisa, pode construir filtros de segurança nas suas consultas, devolvendo documentos associados a uma determinada identidade de segurança. Em vez de papéis predefinidos e atribuições de papéis, o controlo de acesso baseado na identidade é implementado como um *filtro* que apara os resultados de pesquisa de documentos e conteúdos baseados em identidades. A tabela seguinte descreve duas abordagens para aparar os resultados da pesquisa de conteúdo não autorizado.

| Abordagem | Descrição |
|----------|-------------|
|[Aparar segurança com base em filtros de identidade](search-security-trimming-for-azure-search.md)  | Documenta o fluxo de trabalho básico para a implementação do controlo de acesso à identidade do utilizador. Cobre a adição de identificadores de segurança a um índice e, em seguida, explica a filtragem contra esse campo para cortar os resultados do conteúdo proibido. |
|[Aparar segurança com base nas identidades do Azure Ative Directory](search-security-trimming-for-azure-search-with-aad.md)  | Este artigo expande-se no artigo anterior, fornecendo passos para recuperar identidades do Azure Ative Directory (Azure AD), um dos [serviços gratuitos](https://azure.microsoft.com/free/) na plataforma cloud Azure. |

## <a name="administrative-rights"></a>Direitos administrativos

[O Azure role-based access control (Azure RBAC)](../role-based-access-control/overview.md) é um sistema de autorização construído no [Azure Resource Manager](../azure-resource-manager/management/overview.md) para o fornecimento de recursos Azure. Na Pesquisa Cognitiva Azure, o Gestor de Recursos é utilizado para criar ou eliminar o serviço, gerir as teclas API e escalar o serviço. Como tal, as atribuições de funções da Azure determinarão quem pode executar essas tarefas, independentemente de estarem a utilizar o [portal](search-manage.md), [PowerShell,](search-manage-powershell.md)ou as [APIs de Rest de Gestão.](/rest/api/searchmanagement/search-howto-management-rest-api)

Em contrapartida, os direitos de administração sobre os conteúdos alojados no serviço, tais como a capacidade de criar ou eliminar um índice, são conferidos através de teclas API, conforme descrito na [secção anterior](#index-access).

> [!TIP]
> Utilizando mecanismos azures, pode bloquear uma subscrição ou recurso para evitar a eliminação acidental ou não não autorizada do seu serviço de pesquisa por utilizadores com direitos de administração. Para obter mais informações, consulte [os recursos de bloqueio para evitar a eliminação inesperada.](../azure-resource-manager/management/lock-resources.md)

## <a name="certifications-and-compliance"></a>Certificações e conformidade

A Azure Cognitive Search foi certificada em conformidade com vários padrões globais, regionais e específicos da indústria tanto para a nuvem pública como para o Governo de Azure. Para a lista completa, descarregue o papel branco [ **microsoft Azure Compliance Offers**](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) da página oficial dos relatórios da Auditoria.

Para o cumprimento, pode utilizar [a Azure Policy](../governance/policy/overview.md) para implementar as melhores práticas de alta segurança do [Azure Security Benchmark](../security/benchmarks/introduction.md). A Azure Security Benchmark é uma coleção de recomendações de segurança, codificadas em controlos de segurança que mapeiam para ações-chave que deve tomar para mitigar ameaças a serviços e dados. Existem atualmente 11 controlos de segurança, incluindo [Segurança de Rede,](../security/benchmarks/security-control-network-security.md) [Registo e Monitorização,](../security/benchmarks/security-control-logging-monitoring.md)e Proteção de [Dados](../security/benchmarks/security-control-data-protection.md) para citar alguns.

A Azure Policy é uma capacidade incorporada no Azure que o ajuda a gerir o cumprimento de vários padrões, incluindo os da Azure Security Benchmark. Para referências bem conhecidas, a Azure Policy fornece definições incorporadas que fornecem tanto critérios como uma resposta acccionável que aborda o incumprimento.

Para a pesquisa cognitiva Azure, existe atualmente uma definição incorporada. É para registo de diagnóstico. Com este incorporado, você pode atribuir uma política que identifique qualquer serviço de pesquisa que esteja faltando registo de diagnóstico, e depois liga-o. Para obter mais informações, consulte [os controlos de conformidade regulamentar da Política Azure para a pesquisa cognitiva Azure](security-controls-policy.md).

## <a name="see-also"></a>Ver também

+ [Noções básicas da segurança do Azure](../security/fundamentals/index.yml)
+ [Segurança Azure](https://azure.microsoft.com/overview/security)
+ [Centro de Segurança do Azure](../security-center/index.yml)
