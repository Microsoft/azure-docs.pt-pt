---
title: Introdução à Visão Azure (pré-visualização)
description: Este artigo fornece uma visão geral do Azure Purview, incluindo as suas características e os problemas que aborda. O Azure Purview permite que qualquer utilizador registe, descubra, compreenda e consuma fontes de dados.
author: hophan
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 11/30/2020
ms.openlocfilehash: e7ad561cf9ce9983f41fa166eb744c90f126e01e
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555962"
---
# <a name="what-is-azure-purview"></a>O que é Azure Purview?

> [!IMPORTANT]
> A Azure Purview está atualmente em PREVIEW. Os [Termos Complementares de Utilização para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluem termos legais adicionais que se aplicam às funcionalidades do Azure que estão em versão beta, pré-visualização ou ainda não lançadas para a disponibilidade geral.

O Azure Purview é um novo serviço de cloud para utilização pelos utilizadores de dados. Você usa a Azure Purview para gerir centralmente a governação de dados em toda a sua propriedade de dados, abrangendo ambientes em nuvem e on-prem. Este serviço permite que os analistas de negócios procurem dados relevantes utilizando termos de negócio significativos.

Com a UI de Purview ou a Apache Atlas API, os utilizadores técnicos podem ver metadados e a linhagem de ativos de dados num catálogo central.

Os peritos em matéria de assuntos, proprietários de dados e administradores de segurança podem anotar ativos de dados para partilhar os seus conhecimentos comuns. Fazem-no com a UI ou em escala através de políticas automatizadas de classificação e rotulagem automática, com base em metadados e inspeção de conteúdos.

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

## <a name="next-steps"></a>Passos seguintes

Para começar com o Azure Purview, consulte [Criar uma conta Azure Purview](create-catalog-portal.md).
