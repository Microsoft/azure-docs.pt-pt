---
title: Descrição geral da segurança
titleSuffix: Azure Cognitive Search
description: A Azure Cognitive Search está em conformidade com SOC 2, HIPAA, e outras certificações. Ligação e encriptação de dados, autenticação e acesso à identidade através de identificadores de segurança do utilizador e do grupo em expressões de filtro.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/30/2020
ms.openlocfilehash: 9fe9a431d7bbc3b0d3b4b95d9883ed8b5a1f4704
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475435"
---
# <a name="security-in-azure-cognitive-search---overview"></a>Segurança em Azure Cognitive Search - visão geral

Este artigo descreve as principais funcionalidades de segurança na Azure Cognitive Search que podem proteger conteúdos e operações. 

+ Na camada de armazenamento, a encriptação em repouso é um dado adquirido ao nível da plataforma, mas a Cognitive Search também oferece chaves geridas pelo cliente através do Azure Key Vault para uma camada de encriptação adicional.

+ A segurança de entrada protege o ponto final do serviço de pesquisa em níveis crescentes de segurança: desde as chaves da API a pedido, às regras de entrada na firewall, aos pontos finais privados que protegem totalmente o seu serviço da internet pública.

+ A segurança de saída aplica-se aos indexantes que retiram conteúdo de fontes externas. Para pedidos de saída, crie uma identidade gerida para fazer uma pesquisa de um serviço de confiança ao aceder a dados da Azure Storage, Azure SQL, Cosmos DB ou outras fontes de dados Azure. Uma identidade gerida é um substituto de credenciais ou chaves de acesso na ligação. A segurança de saída não está abrangida por este artigo. Para obter mais informações sobre esta capacidade, consulte [Connect a uma fonte de dados utilizando uma identidade gerida.](search-howto-managed-identities-data-sources.md)

Veja este vídeo de ritmo acelerado para uma visão geral da arquitetura de segurança e de cada categoria de funcionalidades.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

## <a name="encrypted-transmissions-and-storage"></a>Transmissões encriptadas e armazenamento

A encriptação é generalizada na Pesquisa Cognitiva do Azure, começando com ligações e transmissões, estendendo-se a conteúdos armazenados no disco. Para serviços de pesquisa na internet pública, a Azure Cognitive Search ouve na porta HTTPS 443. Todas as ligações cliente-a-serviço utilizam encriptação TLS 1.2. As versões anteriores (1.0 ou 1.1) não são suportadas.

### <a name="data-encryption-at-rest"></a>Encriptação inativa de dados

Azure Cognitive Search armazena definições e conteúdos de índices, definições de fonte de dados, definições de indexante, definições de skillset e mapas de sinónimo.

Em toda a camada de armazenamento, os dados são encriptados no disco usando chaves geridas pela Microsoft. Não é possível ligar ou desligar a encriptação ou ver definições de encriptação no portal ou programaticamente. A encriptação é totalmente interiorizada, sem impacto mensurável na indexação do tamanho do tempo de conclusão ou do índice. Ocorre automaticamente em todos os indexantes, incluindo em atualizações incrementais a um índice que não está totalmente encriptado (criado antes de janeiro de 2018).

Internamente, a encriptação baseia-se na [encriptação do serviço de armazenamento Azure,](../storage/common/storage-service-encryption.md)utilizando [encriptação AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits .

> [!NOTE]
> A encriptação em repouso foi anunciada em 24 de janeiro de 2018 e aplica-se a todos os escalões de serviço, incluindo o nível gratuito, em todas as regiões. Para a encriptação completa, os índices criados antes dessa data devem ser eliminados e reconstruídos para que a encriptação ocorra. Caso contrário, apenas novos dados adicionados após 24 de janeiro são encriptados.

### <a name="customer-managed-key-cmk-encryption"></a>Encriptação da chave gerida pelo cliente (CMK)

Os clientes que pretendam uma proteção adicional de armazenamento podem encriptar dados e objetos antes de serem armazenados e encriptados no disco. Esta abordagem baseia-se numa chave de propriedade do utilizador, gerida e armazenada através do Azure Key Vault, independentemente da Microsoft. Encriptar o conteúdo antes de ser encriptado no disco é referido como "encriptação dupla". Atualmente, pode dobrar seletivamente índices de encriptação e mapas de sinónimo. Para obter mais informações, consulte [as chaves de encriptação geridas pelo Cliente na Pesquisa Cognitiva Azure](search-security-manage-encryption-keys.md).

> [!NOTE]
> A encriptação CMK está geralmente disponível para serviços de pesquisa criados após janeiro de 2019. Não é suportado em serviços gratuitos (partilhados). 
>
>Ativar esta funcionalidade aumentará o tamanho do índice e irá degradar o desempenho da consulta. Com base em observações até à data, pode esperar um aumento de 30%-60% nos tempos de consulta, embora o desempenho real varie dependendo da definição de índice e tipos de consultas. Devido a este impacto de desempenho, recomendamos que apenas ative esta funcionalidade em índices que realmente o exijam.

<a name="service-access-and-authentication"></a>

## <a name="inbound-security-and-endpoint-protection"></a>Segurança de entrada e proteção de pontos finais

As funcionalidades de segurança de entrada protegem o ponto final do serviço de pesquisa através do aumento dos níveis de segurança e complexidade. Em primeiro lugar, todos os pedidos requerem uma chave API para acesso autenticado. Em segundo lugar, pode configurar opcionalmente regras de firewall que limitam o acesso a endereços IP específicos. Para uma proteção avançada, uma terceira opção é permitir ao Azure Private Link proteger o seu ponto final de serviço de todo o tráfego de internet.

### <a name="public-access-using-api-keys"></a>Acesso público usando chaves API

Por predefinição, um serviço de pesquisa é acedido através da nuvem pública, utilizando a autenticação baseada em chaves para administração ou acesso de consulta ao ponto final do serviço de pesquisa. Uma chave api é uma cadeia composta por números e letras gerados aleatoriamente. O tipo de chave (administração ou consulta) determina o nível de acesso. A submissão de uma chave válida é considerada prova de que o pedido é originário de uma entidade de confiança. 

Existem dois níveis de acesso ao seu serviço de pesquisa, habilitados pelas seguintes teclas API:

+ Tecla de administração (permite o acesso de leitura-escrita para criar operações [de atualização de leitura-apagar](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) no serviço de pesquisa)

+ Chave de consulta (permite o acesso apenas à recolha de documentos de um índice)

*As teclas de administração* são criadas quando o serviço é prestado. Existem duas teclas de administração, designadas como *primárias* e *secundárias* para mantê-las direitas, mas na verdade são permutáveis. Cada serviço tem duas teclas de administração para que possa rolar uma sem perder o acesso ao seu serviço. Pode [regenerar a chave de administração](search-security-api-keys.md#regenerate-admin-keys) periodicamente por boas práticas de segurança Azure, mas não pode adicionar à contagem total de chave de administração. Há um máximo de duas chaves de administração por serviço de pesquisa.

*As chaves de consulta* são criadas conforme necessário e são concebidas para aplicações de clientes que emitem consultas. Pode criar até 50 chaves de consulta. No código de aplicação, especifica o URL de pesquisa e uma chave api de consulta para permitir o acesso apenas à recolha de documentos de um índice específico. Juntos, o ponto final, uma chave API para acesso apenas de leitura, e um índice-alvo definem o âmbito e o nível de acesso da ligação a partir da aplicação do seu cliente.

A autenticação é necessária em cada pedido, quando cada pedido é composto por uma chave obrigatória, uma operação e um objeto. Quando acorrentados, os dois níveis de autorização (completos ou apenas de leitura) mais o contexto (por exemplo, uma operação de consulta num índice) são suficientes para proporcionar uma segurança de todo o espectro nas operações de serviço. Para obter mais informações sobre as teclas, consulte [Criar e gerir as teclas api](search-security-api-keys.md).

### <a name="ip-restricted-access"></a>Acesso restrito ao IP

Para controlar ainda mais o acesso ao seu serviço de pesquisa, pode criar regras de firewall de entrada que permitam o acesso a endereços IP específicos ou a uma série de endereços IP. Todas as ligações com o cliente devem ser feitas através de um endereço IP permitido, ou a ligação é negada.

Pode utilizar o portal para [configurar](service-configure-firewall.md)o acesso à entrada . 

Em alternativa, pode utilizar as APIs de gestão REST. A versão API 2020-03-13, com o parâmetro [IpRule,](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service#IpRule) permite-lhe restringir o acesso ao seu serviço identificando endereços IP, individualmente ou num intervalo, que pretende conceder acesso ao seu serviço de pesquisa. 

### <a name="private-endpoint-no-internet-traffic"></a>Ponto final privado (sem tráfego de Internet)

Um [ponto final privado](../private-link/private-endpoint-overview.md) para a Azure Cognitive Search permite que um cliente numa rede [virtual](../virtual-network/virtual-networks-overview.md) aceda de forma segura a dados num índice de pesquisa sobre um Link [Privado](../private-link/private-link-overview.md). 

O ponto final privado utiliza um endereço IP a partir do espaço de endereço de rede virtual para ligações ao seu serviço de pesquisa. O tráfego de rede entre o cliente e o serviço de pesquisa atravessa a rede virtual e uma ligação privada na rede de espinha dorsal da Microsoft, eliminando a exposição da internet pública. Um VNET permite uma comunicação segura entre os recursos, com a sua rede no local, bem como a Internet. 

Embora esta solução seja a mais segura, usar serviços adicionais é um custo adicional, por isso certifique-se de ter uma compreensão clara dos benefícios antes de mergulhar. ou mais informações sobre os custos, consulte [a página de preços.](https://azure.microsoft.com/pricing/details/private-link/) Para obter mais informações sobre como estes componentes funcionam em conjunto, veja o vídeo no topo deste artigo. A cobertura da opção de ponto final privado começa às 5:48 no vídeo. Para obter instruções sobre como configurar o ponto final, consulte [Criar um ponto de terminação privado para a pesquisa cognitiva do Azure](service-create-private-endpoint.md).

## <a name="index-access"></a>Acesso a índices

Na Pesquisa Cognitiva Azure, um índice individual não é um objeto securável. Em vez disso, o acesso a um índice é determinado na camada de serviço (ler ou escrever acesso ao serviço), juntamente com o contexto de uma operação.

Para o acesso ao utilizador final, pode estruturar pedidos de consulta para se conectar usando uma chave de consulta, que faz qualquer pedido apenas de leitura, e incluir o índice específico usado pela sua app. Num pedido de consulta, não existe qualquer conceito de adesão de índices ou acesso a múltiplos índices simultaneamente, pelo que todos os pedidos visam um único índice por definição. Como tal, a construção do próprio pedido de consulta (uma chave mais um único índice-alvo) define o limite de segurança.

O acesso do administrador e do desenvolvedor aos índices é indiferenciado: ambos precisam de escrever acesso para criar, eliminar e atualizar objetos geridos pelo serviço. Qualquer pessoa com uma chave de administração do seu serviço pode ler, modificar ou eliminar qualquer índice no mesmo serviço. Para proteção contra a eliminação acidental ou maliciosa de índices, o seu controlo de origem interna para os ativos de código é o remédio para reverter uma eliminação ou modificação de índices indesejados. A Azure Cognitive Search tem falhas dentro do cluster para garantir a disponibilidade, mas não armazena ou executa o seu código proprietário usado para criar ou carregar índices.

Para soluções multitenancy que requerem limites de segurança ao nível do índice, essas soluções normalmente incluem um nível médio, que os clientes usam para lidar com o isolamento de índices. Para obter mais informações sobre o caso de utilização multitenante, consulte [padrões de design para aplicações SaaS multitenant e Pesquisa Cognitiva Azure](search-modeling-multitenant-saas-applications.md).

## <a name="user-access"></a>Acesso do utilizador

A forma como um utilizador acede a um índice e a outros objetos é determinada pelo tipo de chave API no pedido. A maioria dos desenvolvedores cria e atribui chaves de [*consulta*](search-security-api-keys.md) para pedidos de pesquisa do lado do cliente. Uma chave de consulta concede acesso apenas de leitura a conteúdos pesmáveis dentro do índice.

Se necessitar de controlo granular por utilizador sobre os resultados da pesquisa, pode construir filtros de segurança nas suas consultas, devolvendo documentos associados a uma determinada identidade de segurança. Em vez de papéis predefinidos e atribuições de papéis, o controlo de acesso baseado na identidade é implementado como um *filtro* que apara os resultados de pesquisa de documentos e conteúdos baseados em identidades. A tabela seguinte descreve duas abordagens para aparar os resultados da pesquisa de conteúdo não autorizado.

| Abordagem | Descrição |
|----------|-------------|
|[Aparar segurança com base em filtros de identidade](search-security-trimming-for-azure-search.md)  | Documenta o fluxo de trabalho básico para a implementação do controlo de acesso à identidade do utilizador. Cobre a adição de identificadores de segurança a um índice e, em seguida, explica a filtragem contra esse campo para cortar os resultados do conteúdo proibido. |
|[Aparar segurança com base nas identidades do Azure Ative Directory](search-security-trimming-for-azure-search-with-aad.md)  | Este artigo expande-se sobre o artigo anterior, fornecendo passos para recuperar identidades do Azure Ative Directory (AAD), um dos [serviços gratuitos](https://azure.microsoft.com/free/) na plataforma cloud Azure. |

## <a name="administrative-rights"></a>Direitos administrativos

[O acesso baseado em funções (RBAC)](../role-based-access-control/overview.md) é um sistema de autorização construído com [o Azure Resource Manager](../azure-resource-manager/management/overview.md) para o fornecimento de recursos Azure. Na Pesquisa Cognitiva Azure, o Gestor de Recursos é utilizado para criar ou eliminar o serviço, gerir as teclas API e escalar o serviço. Como tal, as atribuições de funções da Azure determinarão quem pode executar essas tarefas, independentemente de estarem a utilizar o [portal](search-manage.md), [PowerShell,](search-manage-powershell.md)ou as [APIs de Rest de Gestão.](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api)

Em contrapartida, os direitos de administração sobre os conteúdos alojados no serviço, tais como a capacidade de criar ou eliminar um índice, são conferidos através de teclas API, conforme descrito na [secção anterior](#index-access).

> [!TIP]
> Utilizando mecanismos azures, pode bloquear uma subscrição ou recurso para evitar a eliminação acidental ou não não autorizada do seu serviço de pesquisa por utilizadores com direitos de administração. Para obter mais informações, consulte [os recursos de bloqueio para evitar a eliminação inesperada.](../azure-resource-manager/management/lock-resources.md)

## <a name="certifications-and-compliance"></a>Certificações e conformidade

A Azure Cognitive Search foi certificada em conformidade com vários padrões globais, regionais e específicos da indústria tanto para a nuvem pública como para o Governo de Azure. Para a lista completa, descarregue o papel branco [ **microsoft Azure Compliance Offers** ](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) da página oficial dos relatórios da Auditoria.

Para o cumprimento, pode utilizar a [Azure Policy](../governance/policy/overview.md) para o ajudar a implementar as melhores práticas de alta segurança do [Azure Security Benchmark](../security/benchmarks/introduction.md). A Azure Security Benchmark é uma coleção de recomendações de segurança, codificadas em controlos de segurança que mapeiam para ações-chave que deve tomar para mitigar ameaças a serviços e dados. Existem atualmente 11 controlos de segurança, incluindo [Segurança de Rede,](../security/benchmarks/security-control-network-security.md) [Registo e Monitorização,](../security/benchmarks/security-control-logging-monitoring.md)e Proteção de [Dados](../security/benchmarks/security-control-data-protection.md) para citar alguns.

A Azure Policy é uma capacidade incorporada no Azure que o ajuda a gerir o cumprimento de vários padrões, incluindo os da Azure Security Benchmark. Para referências bem conhecidas, a Azure Policy fornece definições incorporadas para que possa criar políticas mais facilmente. Para a Azure Cognitive Search, existe atualmente uma definição incorporada para a exploração de diagnóstico, o que significa que pode atribuir uma política que identifique e corrija qualquer serviço de pesquisa que não esteja em conformidade com o controlo de segurança de registo e monitorização. Para obter mais informações, consulte [os controlos de conformidade regulamentar da Política Azure para a pesquisa cognitiva Azure](security-controls-policy.md).

## <a name="see-also"></a>Veja também

+ [Noções básicas da segurança do Azure](../security/fundamentals/index.yml)
+ [Segurança Azure](https://azure.microsoft.com/overview/security)
+ [Centro de Segurança do Azure](../security-center/index.yml)