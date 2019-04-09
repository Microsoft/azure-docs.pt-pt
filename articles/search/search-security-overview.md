---
title: Dados de segurança e privacidade - Azure Search
description: O Azure Search está em conformidade com o SOC 2, HIPAA e outras certificações. Filtra a ligação e encriptação, autenticação e identidade acesso a dados por meio de utilizador e identificadores de grupo de segurança no Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/06/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 11b2fb5a246dfa8f5b1295a11cc57de36120898e
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59269559"
---
# <a name="security-and-data-privacy-in-azure-search"></a>Dados de segurança e privacidade no Azure Search

Recursos abrangentes de segurança e controlos de acesso são criados para o Azure Search para garantir que o conteúdo privado permanece dessa forma. Este artigo enumera a conformidade de recursos e padrões de segurança incorporada no Azure Search.

Arquitetura de segurança de pesquisa do Azure abrange segurança física, as transmissões de encriptados, armazenamento encriptado e conformidade com os padrões de toda a plataforma. Operacionalmente, o Azure Search só aceita pedidos autenticados. Opcionalmente, pode adicionar controlos de acesso de usuário no conteúdo por meio de filtros de segurança. Este artigo aborda a segurança em cada camada, mas principalmente se concentra em como os dados e as operações são protegidas no Azure Search.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Conformidade de padrões: ISO 27001, SOC 2, HIPAA

O Azure Search está certificado para os seguintes padrões, como [anunciado em Junho de 2018](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/):

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [Conformidade SOC 2 tipo 2](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) para o relatório completo, aceda a [Azure - e o Azure Government SOC 2 tipo II relatório](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports). 
+ [Health Insurance Portability and Accountability Act (HIPAA)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP (21 CFR parte 11)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST](https://en.wikipedia.org/wiki/HITRUST)
+ [PCI DSS Nível 1](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)
+ [Austrália IRAP DLM não classificado](https://asd.gov.au/infosec/irap/certified_clouds.htm)

Conformidade com os padrões aplicam-se às funcionalidades de disponibilidade geral. Funcionalidades de pré-visualização são certificadas quando eles fazem a transição para disponibilidade geral e não pode ser utilizados em soluções com requisitos rígidos padrões. Certificação de conformidade é documentada em [conformidade de descrição geral do Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) e o [Centro de fidedignidade](https://www.microsoft.com/en-us/trustcenter). 

## <a name="encrypted-transmission-and-storage"></a>Transmissão encriptada e o armazenamento

Encriptação estende-se ao longo de todo o pipeline de indexação: partir das ligações, por meio de transmissão e para baixo para dados indexados armazenados no Azure Search.

| Camada de segurança | Descrição |
|----------------|-------------|
| Encriptação em trânsito <br>(HTTPS/SSL/TLS) | O Azure Search escuta na porta HTTPS 443. Em toda a plataforma, as ligações aos serviços do Azure são encriptadas. <br/><br/>Todas as interações de Azure Search do serviço de cliente são compatíveis com o SSL/TLS 1.2.  Certifique-se de que utilizar TLSv1.2 para ligações de SSL ao seu serviço.|
| Encriptação inativa | Encriptação é totalmente internalizada no processo de indexação, sem afetar mensuráveis indexação tempo de conclusão ou tamanho do índice. Ocorre automaticamente na indexação de todos os, incluindo as atualizações incrementais para um índice que não está totalmente encriptado (criada antes de Janeiro de 2018).<br><br>Internamente, a encriptação é baseada em [do Azure Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption), através de 256 bits [encriptação AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard).|

A criptografia é interna para o Azure Search, com certificados e chaves de encriptação gerida internamente pela Microsoft e aplicadas universalmente. Não é possível ativar ou desativar a encriptação, gerir ou substituir as suas próprias chaves ou ver as definições de encriptação no portal ou através de programação. 

Encriptação em repouso foi anunciada em 24 de Janeiro de 2018 e aplica-se a todos os escalões de serviço, incluindo serviços (gratuitos) partilhados, em todas as regiões. Para a encriptação completa, índices criados antes dessa data tem de ser removidos e reconstruídos para que encriptação para ocorrer. Caso contrário, apenas os novos dados adicionados após 24 de Janeiro são encriptados.

## <a name="azure-wide-user-access-controls"></a>Controlos de acesso de utilizador de todo o Azure

Vários mecanismos de segurança estão disponível ao nível do Azure e criar automaticamente, portanto, estão disponíveis para os recursos do Azure Search.

+ [Bloqueios no nível de recursos para impedir a eliminação ou subscrição](../azure-resource-manager/resource-group-lock-resources.md)
+ [Com base em função de controlo de acesso (RBAC) para controlar o acesso a informações e as operações administrativas](../role-based-access-control/overview.md)

Todos os serviços do Azure suportam controlos de acesso baseado em funções (RBAC) para definir níveis de acesso de forma consistente em todos os serviços. Por exemplo, visualização de dados confidenciais, como a chave de administrador, está limitado às funções de proprietário e contribuinte, ao passo que ver o estado do serviço está disponível para membros de qualquer função. RBAC fornece funções de proprietário, Contribuidor e leitor. Por predefinição, todos os administradores de serviço são membros da função de proprietário.

<a name="service-access-and-authentication"></a>

## <a name="service-access-and-authentication"></a>Autenticação e acesso de serviço

Enquanto o Azure Search herda as salvaguardas de segurança da plataforma do Azure, também fornece sua própria autenticação baseada em chave. Uma chave de api é uma cadeia de caracteres composta por letras e números gerados aleatoriamente. O tipo de chave (administrador ou consulta) determina o nível de acesso. Envio de uma chave válida é considerado uma prova do pedido provém de uma entidade fidedigna. 

Existem dois níveis de acesso ao seu serviço de pesquisa, ativado por dois tipos de chaves:

* Acesso de administrador (válido por qualquer operação de leitura / escrita para o serviço)
* Acesso de consulta (válido para operações só de leitura, como consultas, a coleção de documentos de um índice)

*Chaves de administração* são criadas quando o serviço é aprovisionado. Existem duas chaves de administração, designadas como *primário* e *secundário* para mantê-los diretamente, mas na verdade eles são intercambiáveis. Cada serviço tem duas chaves administrativas para que pode implementá-um sem perderem o acesso ao seu serviço. Pode [chave de administração de voltar a gerar](search-security-api-keys.md#regenerate-admin-keys) periodicamente por segurança do Azure as práticas recomendadas, mas não é possível adicionar à contagem de chave de administrador total. Há um máximo de duas chaves de administração por serviço de pesquisa.

*Chaves de consulta* são criadas conforme necessário e foram concebidos para aplicações cliente que emitem consultas. Pode criar até 50 chaves de consulta. No código da aplicação, especifique o URL de pesquisa e uma chave de api de consulta para permitir o acesso só de leitura para a coleção de documentos de um índice específico. Juntos, o ponto final, uma chave de api para acesso só de leitura e um índice de destino definem o nível de acesso e o âmbito da ligação da sua aplicação de cliente.

Autenticação é necessária em cada pedido, em que cada solicitação é composta por uma chave obrigatória, uma operação e um objeto. Quando encadeadas, os dois níveis de permissão (completos ou somente leitura) e o contexto (por exemplo, uma operação de consulta num índice) é suficiente para fornecer segurança de espectro completo sobre as operações de serviço. Para obter mais informações sobre chaves, consulte [criar e gerir as chaves de api](search-security-api-keys.md).

## <a name="index-access"></a>Acesso de índice

No Azure Search, um índice individual não é um objeto com capacidade de segurança. Em vez disso, o acesso a um índice é determinado na camada de serviço (acesso de leitura ou escrita), juntamente com o contexto de uma operação.

Para obter acesso de utilizador final, é possível estruturar a pedidos de consulta para se conectar usando uma chave de consulta, o que torna todas as solicitações de só de leitura e incluir o índice específico utilizado pela sua aplicação. Numa solicitação de consulta, não há nenhum conceito de índices de associação ou aceder a vários índices em simultâneo para que todos os pedidos de um único índice de destino por definição. Como tal, a construção do pedido de consulta em si (uma chave mais um índice de destino único) define o limite de segurança.

Acesso de administrador e programador a índices é indiferenciado: ambos precisam de acesso de escrita para criar, eliminar e atualizar objetos geridos pelo serviço. Qualquer pessoa com uma chave de administração ao seu serviço pode ler, modificar ou eliminar qualquer índice no mesmo serviço. Para proteção contra a eliminação acidental ou maliciosa de índices, o seu controle de origem internas para ativos de código é a solução para inverter uma eliminação de índice indesejados ou a modificação. O Azure Search tem ativação pós-falha no cluster para garantir a disponibilidade, mas não armazenar ou executar o seu código proprietário utilizado para criar ou carregar índices.

Para soluções de arquitetura "multitenancy" exigir que os limites de segurança ao nível do índice, essas soluções incluem, geralmente, uma camada intermediária, que os clientes utilizam para lidar com isolamento de índice. Para obter mais informações sobre o caso de utilização do multi-inquilino, consulte [padrões de Design para aplicações SaaS multi-inquilino e Azure Search](search-modeling-multitenant-saas-applications.md).

## <a name="admin-access"></a>Acesso de administrador

[Acesso baseado em funções (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) determina se tem acesso aos controles sobre o serviço e o respetivo conteúdo. Se for um proprietário ou contribuinte num serviço Azure Search, pode utilizar o portal ou o PowerShell **Az.Search** módulo para criar, atualizar ou eliminar objetos no serviço. Também pode utilizar o [API do REST de gestão do Azure Search](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

## <a name="user-access"></a>Acesso do utilizador

Por predefinição, o acesso de utilizador para um índice é determinado pela chave de acesso na solicitação de consulta. A maioria dos desenvolvedores criar e atribuir [ *chaves de consulta* ](search-security-api-keys.md) para pedidos de pesquisa do lado do cliente. Uma chave de consulta concede acesso de leitura a todos os conteúdos no índice.

Se necessitar de granular, por utilizador controlo sobre o conteúdo, pode criar filtros de segurança em suas consultas, retornando documentos associados com uma identidade de segurança especificado. Em vez de funções predefinidas e as atribuições de funções, controle de acesso com base na identidade é implementado como um *filtro* que torta os resultados de documentos e conteúdo da pesquisa com base em identidades. A tabela seguinte descreve as duas abordagens para os resultados da pesquisa de remoção de conteúdo não autorizado.

| Abordagem | Descrição |
|----------|-------------|
|[Remoção de segurança com base nos filtros de identidade](search-security-trimming-for-azure-search.md)  | Documenta o fluxo de trabalho básico para a implementação de controlo de acesso de identidade do utilizador. Ele cobre adicionar identificadores de segurança para um índice e, em seguida, explica a filtragem em relação a esse campo para cortar os resultados de conteúdo proibido. |
|[Remoção de segurança com base em identidades do Azure Active Directory](search-security-trimming-for-azure-search-with-aad.md)  | Este artigo traz mais no artigo anterior, fornecendo passos de recuperação de identidades do Azure Active Directory (AAD), um da [serviços gratuitos](https://azure.microsoft.com/free/) na plataforma cloud do Azure. |

## <a name="table-permissioned-operations"></a>Tabela: Recebendo operações

A tabela seguinte resume as operações permitidas no Azure Search e qual a chave desbloqueia o acesso de uma determinada operação.

| Operação | Permissões |
|-----------|-------------------------|
| Criar um serviço | Proprietário da subscrição do Azure|
| Dimensionar um serviço | Chave de administrador, RBAC proprietário ou Contribuidor do recurso  |
| Eliminar um serviço | Chave de administrador, RBAC proprietário ou Contribuidor do recurso |
| Criar, modificar e eliminar objetos do serviço: <br>Índices e partes de componentes (incluindo definições de analisador, perfis de classificação, opções CORS), indexadores, origens de dados, sinónimos, sugestores. | Chave de administrador, RBAC proprietário ou Contribuidor do recurso  |
| Consultar um índice | Chave de administrador ou de consulta (RBAC não aplicável) |
| Consultar as informações de sistema, como retornando estatísticas, contagens e listas de objetos. | Chave de administrador, RBAC do recurso (proprietário, Contribuidor, leitor) |
| Gerir chaves de administração | Chave de administrador, RBAC proprietário ou contribuinte no recurso. |
| Gerir chaves de consulta |  Chave de administrador, RBAC proprietário ou contribuinte no recurso.  |

## <a name="physical-security"></a>Segurança física

Datacenters da Microsoft fornecem segurança físicos líderes do setor e estão em conformidade com um portefólio abrangente de normas e regulamentos. Para obter mais informações, vá para o [centros de dados globais](https://www.microsoft.com/cloud-platform/global-datacenters) página ou assista a um breve vídeo nos dados de centro de segurança.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>Consulte também

+ [Introdução ao .NET (demonstra como utilizar uma chave de administração para criar um índice)](search-create-index-dotnet.md)
+ [Introdução à REST (demonstra como utilizar uma chave de administração para criar um índice)](search-create-index-rest-api.md)
+ [Controle de acesso com base na identidade usando filtros de pesquisa do Azure](search-security-trimming-for-azure-search.md)
+ [Controlo de acesso com base na identidade do Active Directory a utilizar os filtros de pesquisa do Azure](search-security-trimming-for-azure-search-with-aad.md)
+ [Filtros no Azure Search](search-filters.md)