---
title: Cenários comuns do Catálogo de Dados do Azure
description: Uma visão geral dos cenários comuns do catálogo de dados do Azure, incluindo o registro e a descoberta de fontes de dados de alto valor, a habilitação de business intelligence de autoatendimento e a captura de conhecimento existente sobre fontes de dados e processos.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: aeae505b510f563a6640726c384ea358983eb24f
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736461"
---
# <a name="azure-data-catalog-common-scenarios"></a>Cenários comuns do Catálogo de Dados do Azure
Este artigo apresenta cenários comuns em que o catálogo de dados do Azure pode ajudar sua organização a obter mais valor de suas fontes de dados existentes.

## <a name="scenario-1-registration-of-central-data-sources"></a>Cenário 1: Registro de fontes de dados centrais
As organizações geralmente têm muitas fontes de dados de alto valor. Essas fontes de dados incluem sistemas OLTP (linha de negócios, processamento de transações online), data warehouses e bancos de dados business intelligence/Analytics. O número de sistemas, e a sobreposição entre eles, normalmente cresce ao longo do tempo conforme as necessidades de negócios evoluem e a própria empresa evolui por meio de, por exemplo, fusões e aquisições.

Pode ser difícil para os membros da organização saber onde localizar os dados dentro dessas fontes de dados. Perguntas como as seguintes são muito comuns:

* Dos três sistemas de RH usados dentro da empresa, que devo usar para criar esse tipo de relatório?
* Onde devo ir para obter os números de vendas certificados do ano fiscal que acabou de terminar?
* Quem devo perguntar ou qual é o processo que devo usar para obter acesso ao data warehouse?
* Não sei se esses números estão corretos. Quem posso pedir informações sobre como esses dados devem ser usados antes de compartilhar esse painel com minha equipe?

Para essas e outras perguntas, o catálogo de dados do Azure pode fornecer respostas. As fontes de dados centrais, de alto valor e gerenciadas por ti que são usadas em organizações são geralmente o ponto de partida lógico para popular o catálogo. Embora qualquer usuário possa registrar uma fonte de dados, fazer com que o catálogo inicie com as fontes de dados que têm mais probabilidade de fornecer valor para o maior número de usuários ajuda a impulsionar a adoção e o uso do sistema. 

Se você estiver começando a usar o catálogo de dados do Azure, identificar e registrar as principais fontes de dados que são usadas por muitas equipes diferentes de consumidores de dados pode ser sua primeira etapa para o sucesso.

Esse cenário também apresenta uma oportunidade de anotar as fontes de dados de alto valor para torná-las mais fáceis de entender e acessar. Um aspecto fundamental desse esforço é incluir informações sobre como os usuários podem solicitar acesso à fonte de dados. Com o catálogo de dados do Azure, você pode fornecer o endereço de email do usuário ou da equipe responsável pelo controle do acesso à fonte de dados, links para ferramentas ou documentação existentes ou texto livre que descreve o processo de solicitação de acesso. Essas informações ajudam os membros que detectam fontes de dados registradas, mas que ainda não têm permissões para acessar os dados para solicitar acesso facilmente usando os processos definidos e controlados pelos proprietários da fonte de dados.

## <a name="scenario-2-self-service-business-intelligence"></a>Cenário 2: business intelligence de autoatendimento
Embora as soluções tradicionais de inteligência comercial corporativa continuem a ser uma parte valiosa de cenários de dados de muitas organizações, o ritmo mutável dos negócios tornou o BI de autoatendimento cada vez mais importante. Usando o BI de autoatendimento, operadores de informações e analistas podem criar seus próprios relatórios, pastas de trabalho e painéis sem depender de uma equipe de ti central ou ser restrito pelo cronograma e pela disponibilidade da equipe de ti.

Em cenários de BI de autoatendimento, os usuários normalmente combinam dados de várias fontes, muitas das quais podem não ter sido usadas anteriormente para BI e análise. Embora algumas dessas fontes de dados possam já ser conhecidas, pode ser desafiador descobrir o que fazer para localizar e avaliar possíveis fontes de dados para uma determinada tarefa.

Tradicionalmente, esse processo de descoberta é um manual: os analistas usam suas conexões de rede de mesmo nível para identificar outras pessoas que trabalham com os dados que estão sendo procurados. Depois que uma fonte de dados é encontrada e usada, o processo se repete novamente para cada esforço de BI de autoatendimento subsequente, com vários usuários executando um processo manual redundante de descoberta.

Com o catálogo de dados do Azure, sua organização pode interromper esse ciclo de esforço. Depois de descobrir uma fonte de dados por meio de meios tradicionais, um analista pode registrá-la para torná-la mais facilmente detectável por outros usuários no futuro. Embora o analista possa adicionar mais valor anotando os ativos de dados registrados, essa anotação não precisa ocorrer ao mesmo tempo que o registro. Os usuários podem contribuir ao longo do tempo, à medida que suas agendas permitem, adicionando gradualmente valor às fontes de dados registradas no catálogo.

Esse crescimento orgânica do conteúdo do catálogo é um complemento natural para o registro antecipado de fontes de dados centrais. Preencher previamente o catálogo com dados de que muitos usuários precisarão pode ser um motivador para uso inicial e descoberta. Permitir que os usuários registrem e anotem fontes adicionais pode ser uma maneira de mantê-los e outros membros da organização envolvidos.

Vale a pena observar que, embora esse cenário se concentre especificamente no BI de autoatendimento, os mesmos padrões e desafios também se aplicam a projetos de BI corporativos de grande escala. Usando o catálogo de dados, sua organização pode melhorar qualquer esforço que envolva um processo manual de descoberta de fonte de dados.

## <a name="scenario-3-capturing-tribal-knowledge"></a>Cenário 3: Capturando o conhecimento do tribal
Como saber quais dados você precisa para fazer seu trabalho e onde encontrá-los?

Se você esteve em seu trabalho por algum tempo, provavelmente só sabe. Você passou por um processo de aprendizado gradual e, ao longo do tempo, aprendeu sobre as fontes de dados que são fundamentais para o seu trabalho diário.

Quando um novo funcionário ingressa em sua equipe, como essa pessoa sabe quais dados são necessários para o trabalho e onde encontrá-lo?

É provável que a nova pessoa venha a você com essas perguntas.

Essa transferência contínua do conhecimento do tribal faz parte do processo de descoberta da fonte de dados em organizações grandes e pequenas. Os membros da equipe mais experiente e experientes têm conhecimento sobre os anos e os membros mais recentes da equipe aprenderam a perguntar quando eles têm dúvidas. As informações mais vitais geralmente existem apenas nos cabeçotes de algumas pessoas importantes e quando essas pessoas estão em férias ou saem da equipe, a organização sofre.

Os especialistas em dados normalmente fazem um esforço para documentar seu conhecimento, compartilhá-lo por email ou em documentos do Word em um site de equipe do SharePoint. Embora essa abordagem possa ser valiosa, ela apresenta um novo problema de descoberta: como as pessoas sabem qual documentação existe e onde encontrá-la?

Com o catálogo de dados do Azure, sua organização tem um único local central para armazenar e compartilhar esse conhecimento de tribal e para torná-lo facilmente detectável. No catálogo de dados, seus especialistas de dados podem anotar ativos de dados diretamente e fornecer links para a documentação existente. Quando os membros da organização usam o catálogo para descobrir uma fonte de dados, eles acharão não apenas a origem em si, mas também o conhecimento que anteriormente existia apenas nas mentes dos especialistas da sua organização.
