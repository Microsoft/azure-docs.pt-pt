---
title: Estender Azure Sentinel através de espaços de trabalho e inquilinos Microsoft Docs
description: Como trabalhar com vários inquilinos para a Azure Sentinel para prestadores de serviços MSSP.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/11/2020
ms.author: yelevin
ms.openlocfilehash: d76f8e2d750b8ab2d82e9424f929d8b8353ac25a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84816458"
---
# <a name="extend-azure-sentinel-across-workspaces-and-tenants"></a>Alargar o Azure Sentinel através de áreas de trabalho e inquilinos

## <a name="the-need-to-use-multiple-azure-sentinel-workspaces"></a>A necessidade de usar vários espaços de trabalho do Azure Sentinel

Azure Sentinel é construído em cima de um espaço de trabalho Log Analytics. Você vai notar que o primeiro passo para embarcar Azure Sentinel é selecionar o espaço de trabalho Log Analytics que deseja usar para o efeito.

Você pode obter o benefício total da experiência Azure Sentinel ao usar um único espaço de trabalho. Mesmo assim, existem algumas circunstâncias que podem exigir que você tenha vários espaços de trabalho. O quadro que se segue enumera algumas destas situações e, quando possível, sugere como a exigência pode ser satisfeita com um único espaço de trabalho:

| Requisito | Descrição | Formas de reduzir a contagem de espaço de trabalho |
|-------------|-------------|--------------------------------|
| Soberania e conformidade regulamentar | Um espaço de trabalho está ligado a uma região específica. Se os dados tão devem ser mantidos em [diferentes geografias Azure](https://azure.microsoft.com/global-infrastructure/geographies/) para satisfazer os requisitos regulamentares, devem ser divididos em espaços de trabalho separados. |  |
| Propriedade de dados | Os limites da propriedade dos dados, por exemplo, por subsidiárias ou empresas afiliadas, são melhor delineados utilizando espaços de trabalho separados. |  |
| Vários inquilinos azuis | O Azure Sentinel suporta a recolha de dados dos recursos da Microsoft e da Azure SaaS apenas dentro do seu próprio limite de inquilinos Azure Ative (Azure AD). Portanto, cada inquilino AZure AD requer um espaço de trabalho separado. |  |
| Controlo de acesso a dados granulares | Uma organização pode precisar de permitir que diferentes grupos, dentro ou fora da organização, acedam a alguns dos dados recolhidos pelo Azure Sentinel. Por exemplo:<br><ul><li>Acesso dos proprietários de recursos aos dados relativos aos seus recursos</li><li>Acesso dos SOCs regionais ou subsidiadores aos dados relevantes para as suas partes da organização</li></ul> | Utilize [o recurso RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/controlling-access-to-azure-sentinel-data-resource-rbac/ba-p/1301463) ou [o nível de mesa RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) |
| Definições de retenção granular | Historicamente, vários espaços de trabalho eram a única forma de definir diferentes períodos de retenção para diferentes tipos de dados. Isto já não é necessário em muitos casos, graças à introdução de definições de retenção ao nível da tabela. | Utilize [definições de retenção de nível de tabela](https://techcommunity.microsoft.com/t5/azure-sentinel/new-per-data-type-retention-is-now-available-for-azure-sentinel/ba-p/917316) ou automatize a [eliminação de dados](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data) |
| Faturação dividida | Ao colocar espaços de trabalho em subscrições separadas, podem ser faturados a diferentes partes. | Relatórios de utilização e cobrança cruzada |
| Arquitetura antiga | O uso de múltiplos espaços de trabalho pode decorrer de um design histórico que tenha em conta limitações ou boas práticas que já não são verdadeiras. Também pode ser uma escolha de design arbitrária que pode ser modificada para acomodar melhor O Azure Sentinel.<br><br>Os exemplos incluem:<br><ul><li>Utilizar um espaço de trabalho por subscrição padrão ao implementar o Centro de Segurança Azure</li><li>A necessidade de definições de controlo ou retenção de acesso granular, as soluções para as quais são relativamente novas</li></ul> | Espaços de trabalho re-arquitetos |

### <a name="managed-security-service-provider-mssp"></a>Prestador de Serviços de Segurança Gerido (MSSP)

Um caso de uso particular que determina vários espaços de trabalho é um serviço MSSP Azure Sentinel. Neste caso, muitos, se não todos os requisitos acima referidos, se aplicam, fazendo com que múltiplos espaços de trabalho, entre inquilinos, as melhores práticas. O MSSP pode usar [o Farol Azure](../lighthouse/overview.md) para estender as capacidades de espaço cross-work de Azure Sentinel entre os inquilinos.

## <a name="azure-sentinel-multiple-workspace-architecture"></a>Azure Sentinel múltipla arquitetura de espaço de trabalho

Tal como implícito nos requisitos acima referidos, existem casos em que vários espaços de trabalho do Azure Sentinel, potencialmente através dos inquilinos do Azure Ative Directory (Azure AD), precisam de ser monitorizados centralmente e geridos por um único SOC.

- Um serviço MSSP Azure Sentinel.

- Um SOC global que serve várias subsidiárias, cada uma com o seu próprio SOC local.

- Um SOC monitorizando vários inquilinos da AD Azure dentro de uma organização.

Para responder a este requisito, o Azure Sentinel oferece capacidades de múltiplos espaços de trabalho que permitem monitorização, configuração e gestão central, proporcionando um único painel de vidro em tudo o que é coberto pelo SOC, conforme apresentado no diagrama abaixo.

:::image type="content" source="media/extend-sentinel-across-workspaces-tenants/cross-workspace-architecture.png" alt-text="Arquitetura do Espaço de Trabalho Cruzado":::

Este modelo oferece vantagens significativas sobre um modelo totalmente centralizado no qual todos os dados são copiados para um único espaço de trabalho:

- Atribuição flexível de funções aos SOCs globais e locais, ou aos MSSP seus clientes.

- Menos desafios no que diz respeito à propriedade de dados, privacidade de dados e conformidade regulamentar.

- Latência mínima de rede e taxas.

- Fácil embarque e offboarding de novas subsidiárias ou clientes.

Nas seguintes secções, explicaremos como operar este modelo, e em particular como:

- Monitorize centralmente vários espaços de trabalho, potencialmente através dos inquilinos, proporcionando ao SOC um único painel de vidro.

- Configurar e gerir múltiplos espaços de trabalho, potencialmente através dos inquilinos, utilizando a automação.

## <a name="cross-workspace-monitoring"></a>Monitorização do espaço de trabalho transversal

### <a name="manage-incidents-on-multiple-workspaces"></a>Gerir incidentes em vários espaços de trabalho

Azure Sentinel suporta uma [visão de incidente de vários espaços de trabalho](./multiple-workspace-view.md) facilitando a monitorização e gestão de incidentes centrais em vários espaços de trabalho. A visão centralizada do incidente permite-lhe gerir os incidentes diretamente ou aprofundar-se de forma transparente para os detalhes do incidente no contexto do espaço de trabalho originário.

### <a name="cross-workspace-querying"></a>Consulta de espaço de trabalho transversal

O Azure Sentinel suporta a consulta de [vários espaços de trabalho numa única consulta,](../azure-monitor/log-query/cross-workspace-query.md)permitindo-lhe pesquisar e correlacionar dados de vários espaços de trabalho numa única consulta. 

- Utilize a [expressão espaço de trabalho](../azure-monitor/log-query/workspace-expression.md) para se referir a uma mesa num espaço de trabalho diferente. 
- Utilize o [operador sindical](https://docs.microsoft.com/azure/data-explorer/kusto/query/unionoperator?pivots=azuremonitor) ao lado da expressão do espaço de trabalho para aplicar uma consulta através de tabelas em vários espaços de trabalho.

Pode utilizar [funções](../azure-monitor/log-query/functions.md) guardadas para simplificar consultas de espaço de trabalho transversal. Por exemplo, se uma referência a um espaço de trabalho for longa, é possível que queira guardar a expressão `workspace("customer-A's-hard-to-remember-workspace-name").SecurityEvent` como uma função chamada `SecurityEventCustomerA` . Em seguida, pode escrever consultas como `SecurityEventCustomerA | where ...` .

Uma função também pode simplificar uma união comumente usada. Por exemplo, pode guardar a seguinte expressão como uma função chamada `unionSecurityEvent` :

`union workspace(“hard-to-remember-workspace-name-1”).SecurityEvent, workspace(“hard-to-remember-workspace-name-2”).SecurityEvent`

Em seguida, pode escrever uma consulta em ambos os espaços de trabalho, começando por `unionSecurityEvent | where ...` .

> [!NOTE] 
> Consultar vários espaços de trabalho na mesma consulta pode afetar o desempenho, e portanto é recomendado apenas quando a lógica requer esta funcionalidade.

### <a name="using-cross-workspace-workbooks"></a>Usando livros de espaço de trabalho cruzados

[Os livros fornecem](./overview.md#workbooks) dashboards e aplicativos ao Azure Sentinel. Ao trabalhar com vários espaços de trabalho, eles fornecem monitorização e ações através de espaços de trabalho.

Os livros de trabalho podem fornecer consultas de espaço de trabalho transversal num dos três métodos, cada um dos quais atende a diferentes níveis de experiência do utilizador final:

| Método  | Descrição | Quando devo usar? |
|---------|-------------|--------------------|
| Escreva consultas de espaço cross-work | O criador do livro pode escrever consultas de espaço de trabalho cruzada (descrito acima) no livro. | Esta opção permite que os criadores de livros de trabalho protejam o utilizador inteiramente da estrutura do espaço de trabalho. |
| Adicione um seletor de espaço de trabalho ao livro | O criador do livro pode implementar um seletor de espaço de trabalho como parte do livro, como descrito [aqui.](https://techcommunity.microsoft.com/t5/azure-sentinel/making-your-azure-sentinel-workbooks-multi-tenant-or-multi/ba-p/1402357) | Esta opção proporciona ao utilizador o controlo sobre os espaços de trabalho mostrados pelo livro, através de uma caixa de entrega fácil de usar. |
| Editar o livro interativamente | Um utilizador avançado que modifica um livro existente pode editar as consultas no mesmo, selecionando os espaços de trabalho-alvo utilizando o seletor de espaço de trabalho no editor. | Esta opção permite que um utilizador de energia modifique facilmente os livros de trabalho existentes para trabalhar com vários espaços de trabalho. |
|

## <a name="cross-workspace-management-using-automation"></a>Gestão de espaço de trabalho transversal utilizando automatização

Para configurar e gerir vários espaços de trabalho do Azure Sentinel, terá de automatizar o uso da API de gestão Azure Sentinel. Para obter mais informações sobre como automatizar a implantação de recursos do Azure Sentinel, incluindo regras de alerta, consultas de caça, livros de trabalho e livros de reprodução, consulte [Extending Azure Sentinel: APIs, Integração e automatização de gestão.](https://techcommunity.microsoft.com/t5/azure-sentinel/extending-azure-sentinel-apis-integration-and-management/ba-p/1116885)

Consulte também [a Implementação e Gestão do Azure Sentinel como Código](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928) e [combinando o Farol Azure com as capacidades de DevOps da Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/combining-azure-lighthouse-with-sentinel-s-devops-capabilities/ba-p/1210966) para uma metodologia consolidada e contribuída pela comunidade para gerir o Azure Sentinel como código e para a implantação e configuração de recursos a partir de um repositório privado do GitHub. 


## <a name="whats-not-supported-across-workspaces"></a>O que não é suportado em espaços de trabalho?

As seguintes funcionalidades não são suportadas em espaços de trabalho:

- Uma regra de alerta programada não pode correr através de espaços de trabalho usando uma consulta de espaço de trabalho transversal.

- As consultas de caça não suportam consultas de espaço de trabalho.

## <a name="managing-workspaces-across-tenants-using-azure-lighthouse"></a>Gerir espaços de trabalho em todos os inquilinos usando o Farol de Azure

Como mencionado acima, em muitos cenários, os diferentes espaços de trabalho Azure Sentinel podem ser localizados em diferentes inquilinos AD AZure. Você pode usar [o Farol Azure](../lighthouse/overview.md) para estender todas as atividades do espaço de trabalho transversal através dos limites do inquilino, permitindo que os utilizadores em seu inquilino gerente trabalhem em espaços de trabalho Azure Sentinel em todos os inquilinos. Assim que o Farol Azure estiver [a bordo,](../lighthouse/how-to/onboard-customer.md)utilize o [seletor de subscrição](./multiple-tenants-service-providers.md#how-to-access-azure-sentinel-from-other-tenants) Do diretório + no portal Azure para selecionar todas as subscrições que contenham espaços de trabalho que pretende gerir, de forma a garantir que todos estarão disponíveis nos diferentes seletores de espaço de trabalho do portal.

Ao utilizar o Farol Azure, recomenda-se criar um grupo para cada papel de Azure Sentinel e delegar permissões de cada inquilino para esses grupos.

## <a name="next-steps"></a>Próximos passos
Neste documento, você descobriu como as capacidades de Azure Sentinel podem ser estendidas em vários espaços de trabalho e inquilinos. Para obter orientações práticas sobre a implementação da arquitetura cross-workspace de Azure Sentinel, consulte os seguintes artigos:

- Aprenda a [trabalhar com vários inquilinos](./multiple-tenants-service-providers.md) em Azure Sentinel, usando o Farol de Azure.
- Aprenda a [ver e gerir incidentes em vários espaços de trabalho sem problemas.](./multiple-workspace-view.md)