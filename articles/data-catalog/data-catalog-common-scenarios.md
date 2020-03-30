---
title: Cenários comuns do Catálogo de Dados do Azure
description: Uma visão geral dos cenários comuns para o Catálogo de Dados Do Azure, incluindo o registo e descoberta de fontes de dados de alto valor, permitindo a inteligência empresarial de autosserviço, e capturando conhecimentos existentes sobre fontes e processos de dados.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: aeae505b510f563a6640726c384ea358983eb24f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736461"
---
# <a name="azure-data-catalog-common-scenarios"></a>Cenários comuns do Catálogo de Dados do Azure
Este artigo apresenta cenários comuns onde o Azure Data Catalog pode ajudar a sua organização a obter mais valor a partir das fontes de dados existentes.

## <a name="scenario-1-registration-of-central-data-sources"></a>Cenário 1: Registo de fontes de dados centrais
As organizações têm frequentemente muitas fontes de dados de alto valor. Estas fontes de dados incluem sistemas de linha de negócio, processamento de transações online (OLTP), armazéns de dados e bases de dados de inteligência/análise de negócios. O número de sistemas, e a sobreposição entre eles, cresce tipicamente ao longo do tempo à medida que as necessidades empresariais evoluem e o próprio negócio evolui através, por exemplo, de fusões e aquisições.

Pode ser difícil para os membros da organização saber onde localizar os dados dentro destas fontes de dados. Perguntas como as seguintes são demasiado comuns:

* Dos três sistemas de RH utilizados na empresa, que devo utilizar para criar este tipo de relatório?
* Onde devo ir buscar os números de vendas certificados para o ano fiscal que acabou de terminar?
* A quem devo perguntar, ou qual é o processo que devo usar para ter acesso ao armazém de dados?
* Não sei se estes números estão certos. Quem posso pedir para saber como é que estes dados devem ser usados antes de eu partilhar este painel com a minha equipa?

A estas e outras questões, o Azure Data Catalog pode fornecer respostas. As fontes de dados geridas por TI centrais, de alto valor, que são utilizadas entre organizações, são muitas vezes o ponto de partida lógico para a povoação do catálogo. Embora qualquer utilizador possa registar uma fonte de dados, ter o catálogo iniciado com as fontes de dados que são mais propensos a fornecer valor ao maior número de utilizadores ajuda a impulsionar a adoção e utilização do sistema. 

Se está a começar com o Azure Data Catalog, identificar e registar fontes de dados fundamentais que são usadas por muitas equipas diferentes de consumidores de dados pode ser o seu primeiro passo para o sucesso.

Este cenário também apresenta uma oportunidade para anotar as fontes de dados de alto valor para torná-las mais fáceis de entender e aceder. Um aspeto fundamental deste esforço é incluir informações sobre como os utilizadores podem solicitar o acesso à fonte de dados. Com o Catálogo de Dados Azure, pode fornecer o endereço de e-mail do utilizador ou equipa responsável pelo controlo do acesso à fonte de dados, links para ferramentas ou documentação existentes ou texto gratuito que descreva o processo de pedido de acesso. Esta informação ajuda os membros que descobrem fontes de dados registadas mas que ainda não têm permissão para aceder aos dados para solicitar facilmente o acesso utilizando os processos que são definidos e controlados pelos proprietários de fontes de dados.

## <a name="scenario-2-self-service-business-intelligence"></a>Cenário 2: Inteligência empresarial de autosserviço
Embora as soluções tradicionais de inteligência empresarial corporativa continuem a ser uma parte inestimável das paisagens de dados de muitas organizações, o ritmo de mudança de negócio tornou o BI autosserviço cada vez mais importante. Ao utilizar em autosserviço bi, os trabalhadores da informação e os analistas podem criar os seus próprios relatórios, livros de trabalho e dashboards sem depender de uma equipa central de TI ou de serem restringidos pelo horário e disponibilidade dessa equipa de TI.

Nos cenários bi self-service, os utilizadores geralmente combinam dados de várias fontes, muitas das quais podem não ter sido usadas anteriormente para BI e análise. Embora algumas destas fontes de dados possam já ser conhecidas, pode ser um desafio descobrir o que fazer para localizar e avaliar potenciais fontes de dados para uma determinada tarefa.

Tradicionalmente, este processo de descoberta é manual: os analistas usam as suas ligações de rede de pares para identificar outros que trabalham com os dados que estão a ser procurados. Depois de encontrar e utilizar uma fonte de dados, o processo repete-se novamente para cada esforço bi subsequente, com vários utilizadores a realizarem um processo manual de descoberta redundante.

Com o Azure Data Catalog, a sua organização pode quebrar este ciclo de esforço. Depois de descobrir uma fonte de dados através de meios tradicionais, um analista pode registá-lo para torná-lo mais facilmente detetável por outros utilizadores no futuro. Embora o analista possa acrescentar mais valor anotando os ativos de dados registados, esta anotação não precisa de ser efetuada ao mesmo tempo que o registo. Os utilizadores podem contribuir ao longo do tempo, conforme os seus horários o permitem, acrescentando gradualmente valor às fontes de dados registadas no catálogo.

Este crescimento orgânico do conteúdo do catálogo é um complemento natural para o registo antecipado de fontes de dados centrais. Pré-povoar o catálogo com dados que muitos utilizadores precisarão pode ser um motivador para o uso e descoberta inicial. Permitir que os utilizadores se registem e anotem fontes adicionais pode ser uma forma de mantê-los e outros membros da organização envolvidos.

Vale a pena notar que, embora este cenário se centre especificamente no BI self-service, os mesmos padrões e desafios se aplicam a projetos de BI corporativos em larga escala também. Ao utilizar o Data Catalog, a sua organização pode melhorar qualquer esforço que envolva um processo manual de descoberta de fontes de dados.

## <a name="scenario-3-capturing-tribal-knowledge"></a>Cenário 3: Captar conhecimentos tribais
Como sabe que dados precisa para fazer o seu trabalho e onde encontrar esses dados?

Se está no seu trabalho há algum tempo, provavelmente sabe. Passou por um processo de aprendizagem gradual, e com o tempo aprendeu sobre as fontes de dados que são fundamentais para o seu trabalho diário.

Quando um novo empregado se junta à sua equipa, como é que essa pessoa sabe quais os dados necessários para o trabalho e onde encontrá-lo?

É provável que a nova pessoa venha ter contigo com estas perguntas.

Esta transferência contínua de conhecimentos tribais faz parte do processo de descoberta de fontes de dados em organizações grandes e pequenas. Mais membros da equipa sénior e experiente saem do conhecimento ao longo dos anos, e os membros da equipa mais recentes aprenderam a perguntar-lhes quando têm perguntas. A informação mais vital muitas vezes só existe na cabeça de algumas pessoas-chave, e quando essas pessoas estão de férias ou deixam a equipa, a organização sofre.

Os especialistas em dados normalmente fazem um esforço para documentar os seus conhecimentos, partilhando-os por e-mail ou em documentos Word num site de partilha de equipas. Embora esta abordagem possa ser valiosa, introduz um novo problema de descoberta: como é que as pessoas sabem que documentação existe e onde encontrá-la?

Com o Azure Data Catalog, a sua organização tem uma localização única e central para armazenar e partilhar este conhecimento tribal, e para torná-lo facilmente detetável. No Data Catalog, os seus especialistas em dados podem anotar diretamente os ativos dos dados e fornecer links para a documentação existente. Quando os membros da organização usam o catálogo para descobrir uma fonte de dados, eles vão encontrar não só a própria fonte, mas também o conhecimento que anteriormente existia apenas na mente dos especialistas da sua organização.
