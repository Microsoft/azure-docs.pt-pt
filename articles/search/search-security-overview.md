---
title: Segurança e privacidade de dados
titleSuffix: Azure Cognitive Search
description: A Azure Cognitive Search está em conformidade com o SOC 2, HIPAA e outras certificações. Encriptação de ligação e dados, autenticação e acesso de identidade através de identificadores de segurança do utilizador e do grupo em expressões de filtro.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 4db9624fbc71e48fcc10ae1d9a1d700d301248a2
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759541"
---
# <a name="security-and-data-privacy-in-azure-cognitive-search"></a>Segurança e privacidade de dados na Pesquisa Cognitiva Azure

As funcionalidades de segurança abrangentes e os controlos de acesso são incorporados na Pesquisa Cognitiva Azure para garantir que o conteúdo privado permanece assim. Este artigo enumera as funcionalidades de segurança e a conformidade com as normas incorporadas na Pesquisa Cognitiva Azure.

A arquitetura de segurança da Pesquisa Cognitiva Azure abrange a segurança física, as transmissões encriptadas, o armazenamento encriptado e a conformidade dos padrões em toda a plataforma. Operacionalmente, a Pesquisa Cognitiva Azure apenas aceita pedidos autenticados. Opcionalmente, pode adicionar controlos de acesso por utilizador ao conteúdo através de filtros de segurança. Este artigo toca na segurança em cada camada, mas está principalmente focado na forma como os dados e operações são protegidos na Pesquisa Cognitiva Azure.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Conformidade das normas: ISO 27001, SOC 2, HIPAA

A Azure Cognitive Search é certificada para os seguintes padrões, conforme [anunciado em junho de 2018:](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/)

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [Conformidade SOC 2 Tipo 2](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) Para o relatório completo, vá ao [Relatório Azure - e ao Governo Azure SOC 2 Tipo II.](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports) 
+ [Lei de Portabilidade e Responsabilização dos Seguros de Saúde (HIPAA)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP (21 CFR Parte 11)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST](https://en.wikipedia.org/wiki/HITRUST)
+ [PCI DSS de Nível 1](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)

A conformidade das normas aplica-se às funcionalidades geralmente disponíveis. As funcionalidades de pré-visualização são certificadas quando transitam para a disponibilidade geral, e não devem ser utilizadas em soluções com requisitos rigorosos de normas. A certificação de conformidade está documentada na [visão geral da conformidade do Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) e do Trust [Center.](https://www.microsoft.com/en-us/trustcenter) 

## <a name="encrypted-transmission-and-storage"></a>Transmissão e armazenamento encriptados

A encriptação estende-se por todo o pipeline de indexação: desde ligações, através de transmissão, e até dados indexados armazenados na Pesquisa Cognitiva Azure.

| Camada de segurança | Descrição |
|----------------|-------------|
| Encriptação de dados em circulação <br>(HTTPS/SSL/TLS) | A Pesquisa Cognitiva Azure ouve na porta HTTPS 443. Do outro lado da plataforma, as ligações aos serviços do Azure são encriptadas. <br/><br/>Todas as interações de pesquisa cognitiva Azure de cliente-a-serviço utilizam encriptação SSL/TLS 1.2. Versões anteriores (1.0 ou 1.1) não são suportadas.|
| Encriptação inativa <br>Chaves geridas pela Microsoft | A encriptação é totalmente internalizada no processo de indexação, sem impacto mensurável na indexação do tempo-a-conclusão ou no tamanho do índice. Ocorre automaticamente em todas as indexações, incluindo em atualizações incrementais para um índice que não está totalmente encriptado (criado antes de janeiro de 2018).<br><br>Internamente, a encriptação baseia-se na encriptação do Serviço de [Armazenamento Azure,](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)utilizando [encriptação AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits.<br><br> A encriptação é interna da Pesquisa Cognitiva Azure, com certificados e chaves de encriptação geridas internamente pela Microsoft, e aplicadas universalmente. Não é possível ligar ou desligar a encriptação, gerir ou substituir as suas próprias chaves, nem visualizar as definições de encriptação no portal ou programaticamente.<br><br>A encriptação em repouso foi anunciada em 24 de janeiro de 2018 e aplica-se a todos os níveis de serviço, incluindo o nível livre, em todas as regiões. Para uma encriptação completa, os índices criados antes dessa data devem ser retirados e reconstruídos para que a encriptação ocorra. Caso contrário, apenas novos dados adicionados após 24 de janeiro são encriptados.|
| Encriptação inativa <br>Chaves geridas pelo cliente | A encriptação com chaves geridas pelo cliente está agora geralmente disponível para serviços de pesquisa criados em ou depois de janeiro de 2019. Não é suportado em serviços Gratuitos (partilhados).<br><br>Os índices de pesquisa cognitiva azure e os mapas de sinónimo podem agora ser encriptados em repouso com as chaves geridas pelo cliente no Cofre chave Azure. Para saber mais, consulte [Gerir chaves de encriptação em Pesquisa Cognitiva Azure](search-security-manage-encryption-keys.md).<br><br>Esta funcionalidade não está a substituir a encriptação predefinida em repouso, mas sim aplicada além dela.<br><br>Ativar esta funcionalidade aumentará o tamanho do índice e degradará o desempenho da consulta. Com base em observações até à data, pode esperar ver um aumento de 30%-60% em tempos de consulta, embora o desempenho real varie dependendo da definição de índice e dos tipos de consultas. Devido a este impacto de desempenho, recomendamos que apenas ative esta funcionalidade em índices que realmente o exijam.

## <a name="azure-wide-user-access-controls"></a>Controlos de acesso ao utilizador em toda a Azur

Vários mecanismos de segurança estão disponíveis em todo o Azure, e assim automaticamente disponíveis para os recursos de Pesquisa Cognitiva Azure que cria.

+ [Fechaduras ao nível de subscrição ou de recursos para evitar a supressão](../azure-resource-manager/management/lock-resources.md)
+ [Controlo de Acesso baseado em funções (RBAC) para controlar o acesso à informação e operações administrativas](../role-based-access-control/overview.md)

Todos os serviços Azure suportam controlos de acesso baseados em funções (RBAC) para estabelecer níveis de acesso consistentemente em todos os serviços. Por exemplo, a visualização de dados sensíveis, como a chave de administração, é restrita às funções de Proprietário e Contribuinte. No entanto, o estado do serviço de visualização está disponível para os membros de qualquer função. O RBAC fornece funções de Proprietário, Colaborador e Leitor. Por predefinição, todos os administradores de serviços são membros da função Proprietário.

<a name="service-access-and-authentication"></a>

## <a name="endpoint-access"></a>Acesso endpoint

### <a name="public-access"></a>Acesso público

A Azure Cognitive Search herda as salvaguardas de segurança da plataforma Azure e fornece a sua própria autenticação baseada em chaves. Uma chave api é uma cadeia composta por números e letras gerados aleatoriamente. O tipo de chave (administrador ou consulta) determina o nível de acesso. A apresentação de uma chave válida é considerada prova de que o pedido provém de uma entidade de confiança. 

Existem dois níveis de acesso ao seu serviço de pesquisa, ativados por dois tipos de teclas:

* Acesso à administração (válido para qualquer operação de leitura-escrita contra o serviço)
* Acesso à consulta (válido para operações apenas de leitura, tais como consultas, contra a recolha de documentos de um índice)

*As chaves de administrador* são criadas quando o serviço é prestado. Há duas chaves de administração, designadas como *primárias* e *secundárias* para mantê-las direitas, mas na verdade são permutáveis. Cada serviço tem duas chaves de administração para que possa rolar uma sem perder acesso ao seu serviço. Pode [regenerar](search-security-api-keys.md#regenerate-admin-keys) periodicamente a chave de administração de acordo com as melhores práticas de segurança do Azure, mas não pode adicionar à contagem total de chaves de administração. Há um máximo de duas chaves de administração por serviço de pesquisa.

*As chaves* de consulta são criadas conforme necessário e são projetadas para aplicações de clientes que emitem consultas. Pode criar até 50 chaves de consulta. No código de aplicação, especifica o URL de pesquisa e uma chave api-consulta para permitir o acesso apenas de leitura à recolha de documentos de um índice específico. Juntos, o ponto final, uma chave api para acesso apenas para leitura, e um índice-alvo definem o âmbito e o nível de acesso da ligação a partir da sua aplicação cliente.

A autenticação é exigida em cada pedido, sempre que cada pedido seja composto por uma chave obrigatória, uma operação e um objeto. Quando acorrentados em conjunto, os dois níveis de permissão (completos ou apenas de leitura) mais o contexto (por exemplo, uma operação de consulta num índice) são suficientes para proporcionar segurança de todo o espectro nas operações de serviço. Para obter mais informações sobre as teclas, consulte [Criar e gerir as teclas api.](search-security-api-keys.md)

### <a name="restricted-access"></a>Acesso restrito

Quando tiver um serviço público e pretender restringir a utilização do serviço, pode utilizar a regra de restrição IP na versão Management REST API: 2020-03-13, [IpRule](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service#IpRule). O IpRule permite-lhe restringir o acesso ao seu serviço identificando endereços IP, individualmente ou de uma gama, que pretende conceder acesso ao seu serviço de pesquisa. 

### <a name="private-access"></a>Acesso privado

[Os pontos finais privados](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) para a Pesquisa Cognitiva Azure permitem a um cliente numa rede virtual aceder de forma segura a dados num índice de pesquisa sobre um [Link Privado](https://docs.microsoft.com/azure/private-link/private-link-overview). O ponto final privado utiliza um endereço IP do espaço de endereço de rede virtual para o seu serviço de pesquisa. O tráfego de rede entre o cliente e o serviço de pesquisa atravessa a rede virtual e um link privado na rede de espinha dorsal da Microsoft, eliminando a exposição da internet pública.

A [Rede Virtual Azure (VNet)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) permite uma comunicação segura entre os recursos, com a sua rede no local, bem como a Internet. 

## <a name="index-access"></a>Acesso ao índice

Na Pesquisa Cognitiva Azure, um índice individual não é um objeto securável. Em vez disso, o acesso a um índice é determinado na camada de serviço (ler ou escrever acesso), juntamente com o contexto de uma operação.

Para acesso ao utilizador final, pode estruturar pedidos de consulta para se conectar usando uma chave de consulta, que faz qualquer pedido apenas de leitura, e incluir o índice específico usado pela sua app. Num pedido de consulta, não existe nenhum conceito de adesão de índices ou acesso a múltiplos índices simultaneamente, pelo que todos os pedidos visam um único índice por definição. Como tal, a construção do pedido de consulta em si (uma chave mais um único índice de alvo) define o limite de segurança.

O acesso do administrador e do programador aos índices é indiferenciado: ambos precisam de acesso por escrito para criar, eliminar e atualizar objetos geridos pelo serviço. Qualquer pessoa com uma chave de administração do seu serviço pode ler, modificar ou eliminar qualquer índice no mesmo serviço. Para proteção contra a eliminação acidental ou maliciosa de índices, o seu controlo de fonte interna para os ativos do código é o remédio para inverter uma eliminação ou modificação de índice indesejado. A Pesquisa Cognitiva Azure tem falhas dentro do cluster para garantir a disponibilidade, mas não armazena ou executa o seu código proprietário usado para criar ou carregar índices.

Para soluções multi-atentivas que requerem limites de segurança ao nível do índice, essas soluções normalmente incluem um nível médio, que os clientes usam para lidar com o isolamento do índice. Para obter mais informações sobre o caso de utilização multiarrendatária, consulte [os padrões de design para aplicações SaaS multiarrendatárias e pesquisa cognitiva azure](search-modeling-multitenant-saas-applications.md).

## <a name="authentication"></a>Autenticação

### <a name="admin-access"></a>Acesso à administração

[O acesso baseado em funções (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) determina se tem acesso a controlos sobre o serviço e o seu conteúdo. Se for proprietário ou colaborador num serviço de Pesquisa Cognitiva Azure, pode utilizar o portal ou o módulo PowerShell **Az.Search** para criar, atualizar ou eliminar objetos no serviço. Também pode utilizar a API de [Gestão de Pesquisa Cognitiva Azure](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

### <a name="user-access"></a>Acesso do utilizador

Por predefinição, o acesso do utilizador a um índice é determinado pela chave de acesso no pedido de consulta. A maioria dos desenvolvedores cria e atribui [*chaves de consulta*](search-security-api-keys.md) para pedidos de pesquisa do lado do cliente. Uma consulta chave grants lê acesso a todos os conteúdos dentro do índice.

Se necessitar de controlo granular por utilizador sobre o conteúdo, pode construir filtros de segurança nas suas consultas, devolvendo documentos associados a uma determinada identidade de segurança. Em vez de funções predefinidas e atribuições de papéis, o controlo de acesso baseado na identidade é implementado como um *filtro* que apara resultados de pesquisa de documentos e conteúdos com base em identidades. A tabela seguinte descreve duas abordagens para aparar resultados de pesquisa de conteúdo não autorizado.

| Abordagem | Descrição |
|----------|-------------|
|[Aparação de segurança com base em filtros de identidade](search-security-trimming-for-azure-search.md)  | Documenta o fluxo de trabalho básico para a implementação do controlo de acesso à identidade do utilizador. Cobre a adição de identificadores de segurança a um índice e, em seguida, explica a filtragem contra esse campo para aparar os resultados do conteúdo proibido. |
|[Aparação de segurança com base em identidades do Diretório Ativo Azure](search-security-trimming-for-azure-search-with-aad.md)  | Este artigo expande-se no artigo anterior, fornecendo medidas para recuperar identidades do Azure Ative Directory (AAD), um dos [serviços gratuitos](https://azure.microsoft.com/free/) na plataforma cloud Azure. |

## <a name="table-permissioned-operations"></a>Tabela: Operações permissas

A tabela seguinte resume as operações permitidas na Pesquisa Cognitiva de Azure e qual a chave desbloqueia o acesso a uma determinada operação.

| Operação | Permissões |
|-----------|-------------------------|
| Criar um serviço | Titular de subscrição Azure|
| Escala ré seleção de um serviço | Chave de administrador, Proprietário RBAC ou Contribuinte no recurso  |
| Apagar um serviço | Chave de administrador, Proprietário RBAC ou Contribuinte no recurso |
| Criar, modificar, eliminar objetos no serviço: <br>Índices e componentes (incluindo definições de analisadores, perfis de pontuação, opções CORS), indexadores, fontes de dados, sinónimos, sugestionantes. | Chave de administrador, Proprietário RBAC ou Contribuinte no recurso  |
| Consulta de um índice | Chave de administrador ou consulta (RBAC não aplicável) |
| Informações do sistema de consulta, tais como estatísticas de devolução, contagens e listas de objetos. | Chave de administrador, RBAC sobre o recurso (Proprietário, Colaborador, Leitor) |
| Gerir as chaves de administração | Chave de administrador, Proprietário RBAC ou Colaborador no recurso. |
| Gerir chaves de consulta |  Chave de administrador, Proprietário RBAC ou Colaborador no recurso.  |

## <a name="physical-security"></a>Segurança física

Os centros de dados da Microsoft fornecem segurança física líder no setor e estão em conformidade com um vasto portfólio de padrões e regulamentos. Para saber mais, vá à página dos centros de [dados da Global](https://www.microsoft.com/cloud-platform/global-datacenters) ou veja um pequeno vídeo sobre a segurança do data center.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>Consulte também

+ [Iniciar .NET (demonstra usar uma chave de administração para criar um índice)](search-create-index-dotnet.md)
+ [Iniciar O REST (demonstra o uso de uma chave de administração para criar um índice)](search-create-index-rest-api.md)
+ [Controlo de acesso baseado na identidade utilizando filtros de pesquisa cognitiva Azure](search-security-trimming-for-azure-search.md)
+ [Controlo de acesso baseado na identidade do Diretório Ativo utilizando filtros de pesquisa cognitiva Azure](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtros em Pesquisa Cognitiva Azure](search-filters.md)