---
title: Cenários comuns do Catálogo de Dados do Azure
description: Uma visão geral de cenários comuns para o Azure Data Catalog, incluindo o registo e descoberta de fontes de dados de alto valor, permitindo a inteligência empresarial de autosserviço e capturando os conhecimentos existentes sobre fontes e processos de dados.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 4f76a9696d613a4a974c9ae7c7b2578e56f2fcfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674619"
---
# <a name="azure-data-catalog-common-scenarios"></a>Cenários comuns do Catálogo de Dados do Azure

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Este artigo apresenta cenários comuns onde o Azure Data Catalog pode ajudar a sua organização a obter mais valor a partir das suas fontes de dados existentes.

## <a name="scenario-1-registration-of-central-data-sources"></a>Cenário 1: Registo de fontes centrais de dados
As organizações têm muitas fontes de dados de alto valor. Estas fontes de dados incluem sistemas de processamento de transações online (OLTP), armazéns de dados e bases de dados de inteligência/análise de negócios. O número de sistemas, e a sobreposição entre eles, normalmente cresce ao longo do tempo à medida que as necessidades do negócio evoluem e o próprio negócio evolui através, por exemplo, de fusões e aquisições.

Pode ser difícil para os membros da organização saber onde localizar os dados dentro destas fontes de dados. Perguntas como as seguintes são demasiado comuns:

* Dos três sistemas de RH utilizados na empresa, que devo utilizar para criar este tipo de relatório?
* Onde devo ir buscar os números de vendas certificados para o ano fiscal que acabou de terminar?
* A quem devo perguntar, ou qual é o processo que devo usar para ter acesso ao armazém de dados?
* Não sei se estes números estão certos. Quem posso pedir informações sobre como estes dados devem ser usados antes de partilhar este painel com a minha equipa?

A estas e outras questões, o Azure Data Catalog pode fornecer respostas. As fontes de dados geridas por TI centrais, de alto valor, que são usadas entre organizações, são muitas vezes o ponto de partida lógico para a povoação do catálogo. Apesar de qualquer utilizador poder registar uma fonte de dados, tendo o catálogo sido iniciado com as fontes de dados que são mais propensos a fornecer valor ao maior número de utilizadores ajuda a impulsionar a adoção e utilização do sistema. 

Se estiver a começar com o Azure Data Catalog, identificar e registar fontes de dados chave que são usadas por várias equipas diferentes de consumidores de dados pode ser o seu primeiro passo para o sucesso.

Este cenário também apresenta uma oportunidade de anotar as fontes de dados de alto valor para torná-los mais fáceis de entender e aceder. Um aspeto fundamental deste esforço é incluir informações sobre como os utilizadores podem solicitar o acesso à fonte de dados. Com o Azure Data Catalog, pode fornecer o endereço de e-mail do utilizador ou equipa responsável pelo controlo do acesso à fonte de dados, links para ferramentas ou documentação existentes ou texto gratuito que descreve o processo de pedido de acesso. Esta informação ajuda os membros que descobrem fontes de dados registadas mas que ainda não têm permissões para aceder aos dados para solicitar facilmente o acesso utilizando os processos definidos e controlados pelos proprietários de fontes de dados.

## <a name="scenario-2-self-service-business-intelligence"></a>Cenário 2: Inteligência empresarial self-service
Embora as soluções tradicionais de inteligência empresarial continuem a ser uma parte inestimável das paisagens de dados de muitas organizações, o ritmo de mudança do negócio tornou o BI de autosserviço cada vez mais importante. Ao utilizar o BI self-service, os trabalhadores da informação e os analistas podem criar os seus próprios relatórios, livros e dashboards sem depender de uma equipa central de TI ou ser restringidos pelo horário e disponibilidade dessa equipa de TI.

Em cenários de BI de autosserviço, os utilizadores geralmente combinam dados de múltiplas fontes, muitos dos quais podem não ter sido usados anteriormente para BI e análise. Apesar de algumas destas fontes de dados já poderem ser conhecidas, pode ser um desafio descobrir o que fazer para localizar e avaliar potenciais fontes de dados para uma determinada tarefa.

Tradicionalmente, este processo de descoberta é manual: os analistas usam as suas ligações de rede de pares para identificar outros que trabalham com os dados que estão a ser procurados. Depois de uma fonte de dados ser encontrada e usada, o processo repete-se novamente para cada esforço de BI de autosserviço subsequente, com vários utilizadores a realizarem um processo manual redundante de descoberta.

Com o Azure Data Catalog, a sua organização pode quebrar este ciclo de esforço. Depois de descobrir uma fonte de dados através de meios tradicionais, um analista pode registá-lo para torná-lo mais facilmente detetável por outros utilizadores no futuro. Embora o analista possa acrescentar mais valor anotando os ativos de dados registados, esta anotação não precisa de ser efetuada ao mesmo tempo que o registo. Os utilizadores podem contribuir ao longo do tempo, como os seus horários permitem, adicionando gradualmente valor às fontes de dados registadas no catálogo.

Este crescimento orgânico do conteúdo do catálogo é um complemento natural ao registo frontal de fontes centrais de dados. Pré-povoar o catálogo com dados que muitos utilizadores vão precisar pode ser um motivador para o uso inicial e descoberta. Permitir que os utilizadores se registem e anotem fontes adicionais pode ser uma forma de mantê-los e outros membros da organização envolvidos.

Vale a pena notar que, embora este cenário se centre especificamente no BI self-service, os mesmos padrões e desafios aplicam-se também a projetos de BI corporativos em larga escala. Ao utilizar o Data Catalog, a sua organização pode melhorar qualquer esforço que envolva um processo manual de descoberta de fontes de dados.

## <a name="scenario-3-capturing-tribal-knowledge"></a>Cenário 3: Captação do conhecimento tribal
Como sabe quais os dados necessários para fazer o seu trabalho e onde encontrar esses dados?

Se está no seu trabalho há algum tempo, provavelmente sabe. Passou por um processo de aprendizagem gradual, e com o tempo aprendeu sobre as fontes de dados que são fundamentais para o seu trabalho diário.

Quando um novo empregado se junta à sua equipa, como é que essa pessoa sabe que dados são necessários para o trabalho e onde encontrá-lo?

As probabilidades são que a nova pessoa venha ter contigo com estas perguntas.

Esta transferência contínua de conhecimento tribal faz parte do processo de descoberta de fontes de dados em organizações grandes e pequenas. Mais membros da equipa sénior e experientes têm acumulado conhecimento ao longo dos anos, e os membros mais recentes da equipa aprenderam a perguntar-lhes quando têm dúvidas. A informação mais vital muitas vezes existe apenas na cabeça de algumas pessoas-chave, e quando essas pessoas estão de férias ou deixam a equipa, a organização sofre.

Os especialistas em dados normalmente fazem um esforço para documentar os seus conhecimentos, partilhando-os via e-mail ou em documentos do Word num site da equipa SharePoint. Embora esta abordagem possa ser valiosa, introduz um novo problema de descoberta: como é que as pessoas sabem que documentação existe e onde encontrá-la?

Com o Azure Data Catalog, a sua organização tem uma localização única e central para armazenar e partilhar este conhecimento tribal, e para torná-lo facilmente detetável. No Catálogo de Dados, os seus especialistas em dados podem anotar diretamente os ativos de dados e fornecer ligações à documentação existente. Quando os membros da organização usam o catálogo para descobrir uma fonte de dados, eles vão encontrar não só a própria fonte, mas também o conhecimento que anteriormente existia apenas na mente dos especialistas da sua organização.
