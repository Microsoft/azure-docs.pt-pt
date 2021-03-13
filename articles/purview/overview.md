---
title: Introdução à Visão Azure (pré-visualização)
description: Este artigo fornece uma visão geral do Azure Purview, incluindo as suas características e os problemas que aborda. O Azure Purview permite que qualquer utilizador registe, descubra, compreenda e consuma fontes de dados.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 11/30/2020
ms.openlocfilehash: 3fadfa9d4467209ddbe997afabba27a3752f685d
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419937"
---
# <a name="what-is-azure-purview"></a>O que é o Azure Purview?

> [!IMPORTANT]
> A Azure Purview está atualmente em PREVIEW. Os [Termos Complementares de Utilização para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluem termos legais adicionais que se aplicam às funcionalidades do Azure que estão em versão beta, pré-visualização ou ainda não lançadas para a disponibilidade geral.

O Azure Purview é um serviço unificado de governação de dados que o ajuda a gerir e a governar os seus dados no local, multi-cloud e software-as-a-service (SaaS). Facilmente crie um mapa holístico e atualizado da sua paisagem de dados com descoberta automatizada de dados, classificação de dados sensíveis e linhagem de dados de ponta a ponta. Capacitar os consumidores de dados para encontrar dados valiosos e fidedignas.

O Azure Purview Data Map fornece a base para a descoberta de dados e uma efetiva governação de dados. O Purview Data Map é um serviço PaaS nativo da nuvem que captura metadados sobre dados da empresa presentes em sistemas de análise e operação no local e na nuvem. O Mapa de Dados de Purview é automaticamente mantido atualizado com o sistema de digitalização e classificação automatizado incorporado. Os utilizadores empresariais podem configurar e usar o Mapa de Dados de Purview através de uma UI intuitiva e os desenvolvedores podem interagir programáticamente com o Mapa de Dados usando APIs Apache Atlas 2.0 de código aberto.

O Azure Purview Data Map alimenta o Catálogo de Dados de Purview e insights de dados de purview como experiências unificadas dentro do Estúdio De Purview.
 
Com o Catálogo de Dados da Purview, tanto os utilizadores empresariais como os técnicos podem rapidamente & encontrar dados relevantes utilizando uma experiência de pesquisa com filtros baseados em várias lentes como termos glossários, classificações, etiquetas de sensibilidade e muito mais. Para especialistas em assuntos, administradores de dados e oficiais, o Catálogo de Dados da Purview fornece funcionalidades de cura de dados como gestão glossária de negócios e capacidade de automatizar a marcação de ativos de dados com termos glossários. Os consumidores e produtores de dados também podem rastrear visualmente a linhagem de ativos de dados a partir dos sistemas operacionais no local, através de movimento, transformação & enriquecimento com vários sistemas de armazenamento de dados & sistemas de processamento na nuvem para consumo num sistema de análise como o Power BI.

Com os dados de Purview, os oficiais de dados e os oficiais de segurança podem obter uma visão de um pássaro e, de um modo geral, entender que dados são digitalizados ativamente, onde os dados sensíveis são e como se move.

## <a name="discovery-challenges-for-data-consumers"></a>Desafios de deteção para os consumidores de dados

Tradicionalmente, descobrir fontes de dados empresariais tem sido um processo orgânico baseado no conhecimento comum. Para as empresas que mais valorizam os seus ativos de informação, esta abordagem apresenta muitos desafios:

* Como não existe uma localização central para registar fontes de dados, os utilizadores podem desconhecer uma fonte de dados a menos que entrem em contacto com ele como parte de outro processo.
* A menos que os utilizadores saibam a localização de uma fonte de dados, não podem ligar-se aos dados utilizando uma aplicação do cliente. As experiências de consumo de dados requerem que os utilizadores saibam qual é o caminho ou cadeia de ligação.
* A utilização pretendida dos dados é ocultada aos utilizadores, a menos que saibam a localização da documentação de uma fonte de dados. Fontes de dados e documentação podem viver em vários locais e ser consumidas através de diferentes tipos de experiências.
* Se os utilizadores tiverem perguntas sobre um recurso de informações, têm de localizar o especialista ou equipa responsável pelos dados e contactá-lo offline. Não há nenhuma ligação explícita entre os dados e os especialistas que têm perspetivas sobre a sua utilização.
* A menos que os utilizadores compreendam o processo de pedido de acesso à fonte de dados, descobrir a fonte de dados e a sua documentação não os ajudará a aceder aos dados.

## <a name="discovery-challenges-for-data-producers"></a>Desafios de deteção para os produtores de dados

Embora os consumidores de dados enfrentem os desafios que apresentámos, os utilizadores responsáveis pela produção e manutenção dos recursos de informações enfrentam os seus próprios desafios:

* Anotar origens de dados com metadados descritivos é, geralmente, um desperdício de esforço. As aplicações cliente tendem a ignorar descrições armazenadas na origem de dados.
* Criar documentação para fontes de dados pode ser difícil e é uma responsabilidade permanente manter a documentação sincronizada com fontes de dados. Os utilizadores podem não confiar na documentação que é entendida como estando desatualizada.
* Criar e manter documentação para origens de dados é complexo e moroso. Assegurar a disponibilidade imediata dessa documentação para todos os utilizadores da origem de dados é ainda mais complexo.
* Restringir o acesso às origens de dados e garantir que os consumidores de dados sabem como pedir acesso é um desafio contínuo.

Quando estes desafios são combinados, apresentam uma barreira significativa para as empresas que querem incentivar e promover o uso e compreensão dos dados empresariais.

## <a name="discovery-challenges-for-security-administrators"></a>Desafios de descoberta para administradores de segurança

Os utilizadores responsáveis por garantir a segurança dos dados da sua organização podem ter qualquer um dos desafios acima enumerados como consumidores e produtores de dados, bem como os seguintes desafios adicionais:

* Os dados de uma organização estão constantemente a crescer, armazenados e partilhados em novas direções. A tarefa de descobrir, proteger e governar os seus dados sensíveis é uma tarefa que nunca termina. Você quer ter certeza de que o conteúdo da sua organização está sendo compartilhado com as pessoas corretas, aplicações e com as permissões corretas.
* Compreender os níveis de risco nos dados da sua organização requer mergulhar profundamente no seu conteúdo, procurando palavras-chave, padrões RegEx e/ou tipos de dados sensíveis. Os tipos de dados sensíveis podem incluir números de Cartão de Crédito, números de Segurança Social ou números de Conta Bancária, para citar alguns. Deve monitorizar constantemente todas as fontes de dados para conteúdo sensível, uma vez que mesmo a menor quantidade de perda de dados pode ser crítica para a sua organização.
* Garantir que a sua organização continua a cumprir as políticas de segurança corporativa é uma tarefa desafiante à medida que o seu conteúdo cresce e muda, e à medida que esses requisitos e políticas são atualizados para mudar realidades digitais. Os administradores de segurança são muitas vezes incumbidos de garantir a segurança dos dados o mais rapidamente possível.

## <a name="azure-purview-advantages"></a>Vantagens do Azure Purview

O Azure Purview destina-se a abordar as questões mencionadas nas secções anteriores e a ajudar as empresas a obter o maior valor dos seus ativos de informação existentes. O catálogo torna as fontes de dados facilmente detetáveis e compreensíveis pelos utilizadores que gerem os dados.

O Azure Purview fornece um serviço baseado na nuvem no qual pode registar fontes de dados. Durante o registo, os dados permanecem na sua localização existente, mas uma cópia dos seus metadados é adicionada à Azure Purview, juntamente com uma referência à localização da fonte de dados. Os metadados também são indexados para tornar cada origem de dados facilmente detetável através da pesquisa e compreensível para os utilizadores que a detetarem.

Depois de registar uma fonte de dados, pode enriquecer os seus metadados. Ou o utilizador que registou a fonte de dados ou outro utilizador na empresa adiciona os metadados. Qualquer utilizador pode anotar uma fonte de dados fornecendo descrições, etiquetas ou outros metadados para solicitar o acesso à fonte de dados. Este metadados descritivos complementa os metadados estruturais, tais como nomes de colunas e tipos de dados, que são registados a partir da fonte de dados.

Detetar e compreender origens de dados e a sua utilização são o objetivo principal de registar as origens. Os utilizadores empresariais poderão precisar de dados para business intelligence, programação de aplicações, ciência de dados ou para qualquer outra tarefa em que sejam necessários os dados certos. Eles usam a experiência de descoberta do catálogo de dados para encontrar rapidamente dados que correspondam às suas necessidades, entender os dados para avaliar a sua aptidão para o final, e consumir os dados abrindo a fonte de dados na sua ferramenta de escolha.

Ao mesmo tempo, os utilizadores podem contribuir para o catálogo ao identificar, documentar e anotar origens de dados que já tenham sido registadas. Podem também registar novas fontes de dados, que são depois descobertas, compreendidas e consumidas pela comunidade de utilizadores de catálogos.

## <a name="in-region-data-residency"></a>Residência de dados na região
O Azure Purview não move nem armazena os dados dos clientes para fora da região em que é implantado.

## <a name="next-steps"></a>Passos seguintes

Para começar com o Azure Purview, consulte [Criar uma conta Azure Purview](create-catalog-portal.md).
