---
title: Segurança e privacidade de dados
titleSuffix: Azure Cognitive Search
description: O Azure Pesquisa Cognitiva é compatível com SOC 2, HIPAA e outras certificações. Conexão e criptografia de dados, autenticação e acesso de identidade por meio de identificadores de segurança de usuário e grupo em expressões de filtro.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 44d5edd7b5808b6c212a832dd95de7a9cb4b7c08
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978593"
---
# <a name="security-and-data-privacy-in-azure-cognitive-search"></a>Segurança e privacidade de dados no Azure Pesquisa Cognitiva

Recursos de segurança abrangentes e controles de acesso são criados no Azure Pesquisa Cognitiva para garantir que o conteúdo privado permaneça dessa forma. Este artigo enumera os recursos de segurança e a conformidade de padrões criados no Azure Pesquisa Cognitiva.

A arquitetura de segurança do Azure Pesquisa Cognitiva abrange segurança física, transmissões criptografadas, armazenamento criptografado e conformidade de padrões em toda a plataforma. Operacionalmente, o Azure Pesquisa Cognitiva aceita apenas solicitações autenticadas. Opcionalmente, você pode adicionar controles de acesso por usuário ao conteúdo por meio de filtros de segurança. Este artigo aborda a segurança em cada camada, mas se concentra principalmente em como os dados e as operações são protegidos no Azure Pesquisa Cognitiva.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Conformidade com padrões: ISO 27001, SOC 2, HIPAA

O Azure Pesquisa Cognitiva é certificado para os seguintes padrões, conforme [anunciado em junho de 2018](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/):

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [Conformidade com SoC 2 tipo 2](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) Para o relatório completo, vá para o [relatório do Azure-e do Azure governamental SOC 2 tipo II](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports). 
+ [Lei de responsabilidade e portabilidade de seguro de saúde (HIPAA)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP (21 CFR parte 11)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST](https://en.wikipedia.org/wiki/HITRUST)
+ [Nível de PCI DSS 1](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)
+ [Austrália IRAP não classificada, DLM](https://asd.gov.au/infosec/irap/certified_clouds.htm)

Conformidade com padrões aplica-se aos recursos disponíveis para o público geral. Os recursos de visualização são certificados quando fazem a transição para a disponibilidade geral e não devem ser usados em soluções com requisitos estritos de padrões. A certificação de conformidade está documentada em [visão geral da conformidade Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) e da [central de confiabilidade](https://www.microsoft.com/en-us/trustcenter). 

## <a name="encrypted-transmission-and-storage"></a>Transmissão e armazenamento criptografados

A criptografia se estende durante todo o pipeline de indexação: de conexões, por transmissão e até dados indexados armazenados no Azure Pesquisa Cognitiva.

| Camada de segurança | Descrição |
|----------------|-------------|
| Encriptação em circulação <br>(HTTPS/SSL/TLS) | O Azure Pesquisa Cognitiva escuta na porta HTTPS 443. Em toda a plataforma, as conexões com os serviços do Azure são criptografadas. <br/><br/>Todas as interações do Azure Pesquisa Cognitiva de cliente para serviço são compatíveis com SSL/TLS 1,2.  Certifique-se de usar o TLSv 1.2 para conexões SSL com seu serviço.|
| Encriptação inativa <br>Chaves gerenciadas pela Microsoft | A criptografia é totalmente internalizada no processo de indexação, sem impacto mensurável na indexação do tempo de conclusão ou do tamanho do índice. Ele ocorre automaticamente em toda a indexação, incluindo atualizações incrementais em um índice que não está totalmente criptografado (criado antes de janeiro de 2018).<br><br>Internamente, a criptografia é baseada no [Azure criptografia do serviço de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-service-encryption), usando a [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits.<br><br> A criptografia é interna à Pesquisa Cognitiva do Azure, com certificados e chaves de criptografia gerenciadas internamente pela Microsoft e aplicada universalmente. Você não pode ativar ou desativar a criptografia, gerenciar ou substituir suas próprias chaves ou exibir configurações de criptografia no portal ou programaticamente.<br><br>A criptografia em repouso foi anunciada em 24 de janeiro de 2018 e se aplica a todas as camadas de serviço, incluindo a camada gratuita, em todas as regiões. Para criptografia completa, os índices criados antes dessa data devem ser descartados e recriados para que a criptografia ocorra. Caso contrário, somente os novos dados adicionados após 24 de Janeiro serão criptografados.|
| Encriptação inativa <br>Chaves geridas pelo cliente | A criptografia com chaves gerenciadas pelo cliente agora está disponível para os serviços de pesquisa criados em ou após janeiro de 2019. Não há suporte para serviços gratuitos (compartilhados).<br><br>Os índices de Pesquisa Cognitiva do Azure e os mapas de sinônimos agora podem ser criptografados em repouso com chaves gerenciadas por chaves do cliente no Azure Key Vault. Para saber mais, confira [gerenciar chaves de criptografia no Azure pesquisa cognitiva](search-security-manage-encryption-keys.md).<br><br>Esse recurso não substitui a criptografia padrão em repouso, mas sim aplicado além dela.<br><br>Habilitar esse recurso aumentará o tamanho do índice e diminuirá o desempenho da consulta. Com base nas observações até a data, você pode esperar um aumento de 30%-60% nos tempos de consulta, embora o desempenho real varie dependendo da definição de índice e dos tipos de consultas. Devido a esse impacto no desempenho, recomendamos que você habilite apenas esse recurso em índices que realmente o exigem.

## <a name="azure-wide-user-access-controls"></a>Controles de acesso de usuário de todo o Azure

Vários mecanismos de segurança estão disponíveis para todo o Azure e, portanto, ficam automaticamente disponíveis para os recursos de Pesquisa Cognitiva do Azure que você criar.

+ [Bloqueios na assinatura ou no nível de recurso para impedir a exclusão](../azure-resource-manager/management/lock-resources.md)
+ [RBAC (controle de acesso baseado em função) para controlar o acesso a informações e operações administrativas](../role-based-access-control/overview.md)

Todos os serviços do Azure dão suporte a RBAC (controles de acesso baseado em função) para definir níveis de acesso consistentemente em todos os serviços. Por exemplo, a exibição de dados confidenciais, como a chave de administração, é restrita às funções de proprietário e colaborador, enquanto a exibição do status do serviço está disponível para os membros de qualquer função. O RBAC fornece funções de proprietário, colaborador e leitor. Por padrão, todos os administradores de serviço são membros da função de proprietário.

<a name="service-access-and-authentication"></a>

## <a name="service-access-and-authentication"></a>Acesso e autenticação de serviço

Embora o Azure Pesquisa Cognitiva herde as proteções de segurança da plataforma Azure, ele também fornece sua própria autenticação baseada em chave. Uma chave de API é uma cadeia de caracteres composta de números e letras gerados aleatoriamente. O tipo de chave (administrador ou consulta) determina o nível de acesso. O envio de uma chave válida é considerado uma prova de que a solicitação provém de uma entidade confiável. 

Há dois níveis de acesso ao serviço de pesquisa, habilitados por dois tipos de chaves:

* Acesso de administrador (válido para qualquer operação de leitura/gravação no serviço)
* Acesso à consulta (válido para operações somente leitura, como consultas, em relação à coleção de documentos de um índice)

*As chaves de administração* são criadas quando o serviço é provisionado. Há duas chaves de administração, designadas como *primárias* e *secundárias* , para mantê-las diretas, mas, de fato, são intercambiáveis. Cada serviço tem duas chaves de administração para que você possa reverter uma sem perder o acesso ao seu serviço. Você pode [regenerar a chave de administração](search-security-api-keys.md#regenerate-admin-keys) periodicamente por práticas recomendadas de segurança do Azure, mas não pode adicionar à contagem total de chaves de administração. Há um máximo de duas chaves de administração por serviço de pesquisa.

*As chaves de consulta* são criadas conforme necessário e são projetadas para aplicativos cliente que emitem consultas. Você pode criar até 50 chaves de consulta. No código do aplicativo, você especifica a URL de pesquisa e uma chave de API de consulta para permitir acesso somente leitura à coleção de documentos de um índice específico. Juntos, o ponto de extremidade, uma chave de API para acesso somente leitura e um índice de destino definem o nível de acesso e escopo da conexão do seu aplicativo cliente.

A autenticação é necessária em cada solicitação, em que cada solicitação é composta por uma chave obrigatória, uma operação e um objeto. Quando encadeados, os dois níveis de permissão (completo ou somente leitura) mais o contexto (por exemplo, uma operação de consulta em um índice) são suficientes para fornecer segurança de espectro completo em operações de serviço. Para obter mais informações sobre chaves, consulte [criar e gerenciar API-Keys](search-security-api-keys.md).

## <a name="index-access"></a>Acesso ao índice

No Azure Pesquisa Cognitiva, um índice individual não é um objeto protegível. Em vez disso, o acesso a um índice é determinado na camada de serviço (acesso de leitura ou gravação), juntamente com o contexto de uma operação.

Para acesso do usuário final, você pode estruturar solicitações de consulta para se conectar usando uma chave de consulta, que faz com que qualquer solicitação seja somente leitura e inclua o índice específico usado pelo seu aplicativo. Em uma solicitação de consulta, não há nenhum conceito de unir índices ou acessar vários índices simultaneamente para que todas as solicitações direcionem um único índice por definição. Dessa forma, a construção da solicitação de consulta em si (uma chave e um índice de destino único) define o limite de segurança.

O acesso de administrador e desenvolvedor a índices não é diferenciado: ambos precisam de acesso de gravação para criar, excluir e atualizar objetos gerenciados pelo serviço. Qualquer pessoa com uma chave de administração para seu serviço pode ler, modificar ou excluir qualquer índice no mesmo serviço. Para proteção contra exclusão acidental ou mal-intencionada de índices, seu controle de origem interno para ativos de código é a solução para reverter uma modificação ou exclusão de índice indesejado. O Azure Pesquisa Cognitiva tem um failover no cluster para garantir a disponibilidade, mas não armazena ou executa o código proprietário usado para criar ou carregar índices.

Para soluções multilocação que exigem limites de segurança no nível de índice, essas soluções normalmente incluem uma camada intermediária, que os clientes usam para lidar com isolamento de índice. Para obter mais informações sobre o caso de uso multilocatário, consulte [padrões de design para aplicativos SaaS multilocatários e pesquisa cognitiva do Azure](search-modeling-multitenant-saas-applications.md).

## <a name="admin-access"></a>Acesso de administrador

O [RBAC (acesso baseado em função)](https://docs.microsoft.com/azure/role-based-access-control/overview) determina se você tem acesso a controles sobre o serviço e seu conteúdo. Se você for um proprietário ou colaborador em um serviço de Pesquisa Cognitiva do Azure, poderá usar o portal ou o módulo **AZ. Search** do PowerShell para criar, atualizar ou excluir objetos no serviço. Você também pode usar a [API REST de gerenciamento de pesquisa cognitiva do Azure](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

## <a name="user-access"></a>Acesso do utilizador

Por padrão, o acesso do usuário a um índice é determinado pela chave de acesso na solicitação de consulta. A maioria dos desenvolvedores cria e atribui [*chaves de consulta*](search-security-api-keys.md) para solicitações de pesquisa no lado do cliente. Uma chave de consulta concede acesso de leitura a todo o conteúdo dentro do índice.

Se você precisar de controle granular por usuário sobre o conteúdo, poderá criar filtros de segurança em suas consultas, retornando documentos associados a uma determinada identidade de segurança. Em vez de funções predefinidas e atribuições de função, o controle de acesso baseado em identidade é implementado como um *filtro* que corta os resultados da pesquisa de documentos e conteúdo com base em identidades. A tabela a seguir descreve duas abordagens para aparar resultados de pesquisa de conteúdo não autorizado.

| Abordagem | Descrição |
|----------|-------------|
|[Remoção de segurança com base em filtros de identidade](search-security-trimming-for-azure-search.md)  | Documenta o fluxo de trabalho básico para implementar o controle de acesso de identidade do usuário. Ele aborda a adição de identificadores de segurança a um índice e explica a filtragem em relação a esse campo para cortar os resultados de conteúdo proibido. |
|[Remoção de segurança com base em identidades Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | Este artigo se expande no artigo anterior, fornecendo etapas para recuperar identidades de Azure Active Directory (AAD), um dos [serviços gratuitos](https://azure.microsoft.com/free/) na plataforma de nuvem do Azure. |

## <a name="table-permissioned-operations"></a>Tabela: operações de permissão

A tabela a seguir resume as operações permitidas no Azure Pesquisa Cognitiva e qual chave desbloqueia o acesso a uma determinada operação.

| Operação | Permissões |
|-----------|-------------------------|
| Criar um serviço | Titular da assinatura do Azure|
| Dimensionar um serviço | Chave de administração, proprietário do RBAC ou colaborador no recurso  |
| Excluir um serviço | Chave de administração, proprietário do RBAC ou colaborador no recurso |
| Crie, modifique e exclua objetos no serviço: <br>Índices e partes de componentes (incluindo definições do analisador, perfis de pontuação, opções de CORS), indexadores, fontes de dados, sinônimos, sugestores. | Chave de administração, proprietário do RBAC ou colaborador no recurso  |
| Consultar um índice | Chave de administrador ou de consulta (RBAC não aplicável) |
| Consultar informações do sistema, como retornar estatísticas, contagens e listas de objetos. | Chave de administração, RBAC no recurso (proprietário, colaborador, leitor) |
| Gerenciar chaves de administrador | Chave de administração, proprietário do RBAC ou colaborador no recurso. |
| Gerenciar chaves de consulta |  Chave de administração, proprietário do RBAC ou colaborador no recurso.  |

## <a name="physical-security"></a>Segurança física

Os data centers da Microsoft fornecem segurança física líder do setor e estão em conformidade com um amplo portfólio de padrões e regulamentos. Para saber mais, vá para a página [data centers globais](https://www.microsoft.com/cloud-platform/global-datacenters) ou assista a um breve vídeo sobre Data Center segurança.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>Ver também

+ [Introdução ao .NET (demonstra o uso de uma chave de administração para criar um índice)](search-create-index-dotnet.md)
+ [Introdução ao REST (demonstra o uso de uma chave de administração para criar um índice)](search-create-index-rest-api.md)
+ [Controle de acesso baseado em identidade usando filtros de Pesquisa Cognitiva do Azure](search-security-trimming-for-azure-search.md)
+ [Active Directory controle de acesso baseado em identidade usando filtros de Pesquisa Cognitiva do Azure](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtros no Azure Pesquisa Cognitiva](search-filters.md)