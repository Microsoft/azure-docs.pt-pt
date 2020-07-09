---
title: Planeie a sua aplicação - QnA Maker
description: Planear a sua aplicação QnA Maker requer compreender como funciona a QnA Maker e interage com outros serviços Azure, bem como com alguns conceitos de base de conhecimento.
ms.topic: conceptual
ms.date: 07/2/2020
ms.openlocfilehash: d19ec51aec7e71b6f040a03543f72af3aed09556
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85875717"
---
# <a name="plan-your-qna-maker-app"></a>Planeie a sua app QnA Maker

Planear a sua aplicação QnA Maker requer compreender como funciona a QnA Maker e interage com outros serviços Azure, bem como com alguns conceitos de base de conhecimento.

## <a name="azure-resources"></a>Recursos do Azure

Cada [recurso Azure](azure-resources.md#resource-purposes) criado com o QnA Maker tem um propósito específico. Como cada recurso tem o seu próprio propósito, limites e [nível de preços,](azure-resources.md#pricing-tier-considerations)é importante entender o que estes recursos fazem como parte do seu processo de planeamento.

|Recurso|Objetivo|
|--|--|
| [Recurso do Fabricante QnA](azure-resources.md#qna-maker-resource)|Previsão de autoria e consulta|
| [Recurso de Pesquisa Cognitiva](azure-resources.md#cognitive-search-resource)|Armazenamento e pesquisa de dados|
| [Recurso de serviço de aplicações e](azure-resources.md#app-service-and-app-service-plan) recurso de serviço de plano de aplicações|Ponto final de predição da consulta|
| [Recurso de Insights de Aplicação](azure-resources.md#application-insights)|Telemetria de previsão de consulta|

### <a name="resource-planning"></a>Planeamento de recursos

Enquanto estiver a aprender a autorizar e a consultar a previsão, utilizando o nível `F0` livre, de cada recurso funciona e fornecerá tanto a experiência de autoria como a previsão de consulta. Quando se muda para uma produção, ao vivo, cenário, reavalie a sua seleção de recursos.

#### <a name="qna-maker-resource"></a>Recurso do Fabricante QnA

Um único recurso QnA Maker pode acolher mais do que uma base de conhecimento. O número de bases de conhecimento é determinado pela quantidade de índices suportados pelo nível de preços da Pesquisa Cognitiva. Saiba mais sobre a [relação dos índices com bases de conhecimento.](azure-resources.md#index-usage)

#### <a name="knowledge-base-size-and-throughput"></a>Tamanho e produção da base do conhecimento

Quando planeia construir uma aplicação real, planeie os seus recursos para o tamanho da sua base de conhecimentos e os pedidos de previsão de consulta que espera.

Um tamanho de base de conhecimento é controlado pelo:
* [Limites](../../../search/search-limits-quotas-capacity.md) de níveis de preços dos recursos de pesquisa cognitiva
* [Limites do Fabricante QnA](../limits.md)

O pedido de previsão de consulta de base de conhecimento é controlado pelo plano de aplicações Web e aplicação web. Consulte as [definições recomendadas](azure-resources.md#recommended-settings) para planear o seu nível de preços.

### <a name="resource-sharing"></a>Partilha de recursos

Se já tem alguns destes recursos em uso, pode considerar a partilha de recursos. Embora alguns recursos [possam ser partilhados](azure-resources.md#share-services-with-qna-maker), este é um cenário avançado.

Todas as bases de conhecimento criadas no mesmo recurso QnA Maker partilham o mesmo ponto final de previsão de consulta de **teste.**

### <a name="understanding-impact-of-resource-selection"></a>Compreender o impacto da seleção de recursos

A seleção adequada de recursos significa que a sua base de conhecimento responde com sucesso às previsões de consulta.

Se a sua base de conhecimento não está a funcionar corretamente, normalmente o problema é uma gestão de recursos inadequada.

A seleção inadequada de recursos requer uma investigação para determinar que [recurso precisa de ser alterado](azure-resources.md#when-to-change-a-pricing-tier).

## <a name="knowledge-bases"></a>Bases de conhecimento

Uma base de conhecimento está diretamente ligada ao seu recurso QnA Maker e contém os pares de perguntas e respostas (QnA) usados para responder a pedidos de previsão de consulta.

### <a name="language-considerations"></a>Considerações linguísticas

A primeira base de conhecimento criada no seu recurso QnA Maker define o idioma para o recurso. Só pode ter uma língua para um recurso QnA Maker.

Estruça os seus recursos QnA Maker por linguagem ou use [o Tradutor](../../translator/translator-info-overview.md) para alterar uma consulta de outra língua para a linguagem da base de conhecimento antes de enviar a consulta para o ponto final de previsão de consulta.

### <a name="ingesting-data-sources"></a>Ingerir fontes de dados

Fontes [de dados ingeridas, utilizadas](knowledge-base.md)para criar uma base de conhecimento, podem ser uma das seguintes:

* URL público
* Private SharePoint URL
* Ficheiro

O processo de ingestão converte [tipos de conteúdo suportados](content-types.md) para marcação. Toda a edição adicional da *resposta* é feita com marcação. Depois de criar a sua base de conhecimento, pode editar [pares QnA](question-answer-set.md) no portal QnA Maker com [uma rica autoria de textos.](../how-to/edit-knowledge-base.md#rich-text-editing-for-answer)

### <a name="data-format-considerations"></a>Considerações de formato de dados

Como o formato final de um par QnA é marcado, entender o [suporte de marcação](../reference-markdown-format.md) é importante.

As imagens ligadas devem estar disponíveis a partir de um URL público para serem exibidas no painel de teste do portal QnA Maker, bem como em qualquer aplicação ao cliente, uma vez que a QnA Maker não fornece autenticação para conteúdos, incluindo imagens.

### <a name="bot-personality"></a>Personalidade bot

Adicione uma personalidade bot à sua base de conhecimento com [chit-chat](../how-to/chit-chat-knowledge-base.md). Esta personalidade surge com respostas fornecidas num certo tom de conversação, como *profissional* e *amigável.* Este chit-chat é fornecido como um conjunto de conversação, que você tem controlo total para adicionar, editar e remover.

Recomenda-se uma personalidade bot se o seu bot se ligar à sua base de conhecimento. Se estiver a ligar-se a vários serviços, um dos quais é a sua base de conhecimentos, pode optar por continuar a usar o chit-chat na sua base de conhecimentos, mas deve rever como o serviço de bot interage para saber se esse é o design arquitetónico correto para a sua utilização.

### <a name="conversation-flow-with-a-knowledge-base"></a>Fluxo de conversação com uma base de conhecimento

O fluxo de conversação geralmente começa com uma saudação de um utilizador, como `Hi` ou `Hello` . A sua base de conhecimentos pode responder com uma resposta geral, `Hi, how can I help you` como, por exemplo, e também pode fornecer uma seleção de pedidos de acompanhamento, para continuar a conversa.

Deve desenhar o seu fluxo de conversação com um loop em mente para que um utilizador saiba usar o seu bot e não seja abandonado pelo bot na conversação. [As instruções de seguimento](../how-to/multiturn-conversation.md) fornecem ligações entre pares QnA, que permitem o fluxo de conversação.

### <a name="authoring-with-collaborators"></a>Autoria com colaboradores

Os colaboradores podem ser outros desenvolvedores que partilham a pilha completa de desenvolvimento da aplicação base de conhecimento ou podem limitar-se a apenas autorizar a base de conhecimento.

A autoria da base de conhecimento suporta várias [permissões de acesso baseadas](../how-to/collaborate-knowledge-base.md) em funções que aplica no portal Azure para limitar o âmbito das capacidades de um colaborador.

## <a name="integration-with-client-applications"></a>Integração com aplicações de clientes

Integração com [aplicações de clientes](integration-with-other-applications.md) significa enviar uma consulta para o ponto final de tempo de previsão. Uma consulta é enviada para a sua base de conhecimentos específica com um pedido baseado em SDK ou REST para o ponto final da aplicação web do seu QnA Maker.

Para autenticar corretamente um pedido de cliente, a aplicação do cliente deve enviar as credenciais corretas e o ID da base de conhecimento. Se estiver a utilizar um Serviço Azure Bot, configufique a definição como parte da configuração do bot no portal Azure.

### <a name="conversation-flow-in-a-client-application"></a>Fluxo de conversação em uma aplicação de cliente

O fluxo de conversação numa [aplicação do cliente](integration-with-other-applications.md), como um bot Azure, pode exigir funcionalidade antes e depois de interagir com a base de conhecimento.

Se a sua aplicação ao cliente suportar o fluxo de conversação, fornecendo meios alternativos para lidar com as solicitações de seguimento ou chit-chit, desenhe-as cedo e certifique-se de que a consulta utilizando a aplicação do cliente é manuseada corretamente, seja por outro serviço ou enviada para a sua base de conhecimento.

### <a name="dispatching-between-qna-maker-and-language-understanding-luis"></a>Despacho entre o Criador QnA e a Compreensão linguística (LUIS)

Uma aplicação de cliente pode fornecer várias funcionalidades, apenas uma das quais é respondida por uma base de conhecimento. Outras funcionalidades ainda precisariam de compreender o texto de conversação, e extrair significado dele.

Uma arquitetura comum de aplicações de cliente é usar tanto qnA Maker e [Language Understanding (LUIS)](../../LUIS/what-is-luis.md) juntos. A LUIS fornece a classificação e extração de texto para qualquer consulta, incluindo a outros serviços, enquanto a QnA Maker fornece respostas a partir da sua base de conhecimento.

Numa [arquitetura partilhada,](../choose-natural-language-processing-service.md)o despacho entre os dois serviços é feito com a ferramenta [Despacho](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) da Bot Framework.

### <a name="active-learning-from-a-client-application"></a>Aprendizagem ativa a partir de uma aplicação de cliente

O QnA Maker utiliza _a aprendizagem ativa_ para melhorar a sua base de conhecimento, sugerindo perguntas alternativas a uma resposta. A aplicação do cliente é responsável por uma parte desta [aprendizagem ativa.](active-learning-suggestions.md) A aplicação do cliente, através de solicitações de conversação, pode determinar que a resposta devolvida da base de conhecimento não era a resposta que o utilizador procurava, e determinar a melhor resposta. A aplicação do cliente precisa [enviar essa informação de volta para a base de conhecimento](active-learning-suggestions.md#how-you-give-explicit-feedback-with-the-train-api) para melhorar a qualidade da previsão.

### <a name="providing-a-default-answer"></a>Fornecendo uma resposta padrão

Se a sua base de conhecimento não encontrar uma resposta, ela devolve a _resposta por defeito_. Esta resposta é configurável a partir do portal QnA Maker, na página **Definições** ou nas [APIs](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#request-body).

Esta resposta padrão é diferente da resposta padrão do bot Azure. A resposta padrão do bot Azure está configurada no portal Azure, para o seu bot, como parte das definições de configuração e é devolvida quando o limiar de pontuação não é cumprido.

## <a name="prediction"></a>Previsão

A previsão é a resposta da sua base de conhecimento e inclui mais informação do que apenas a resposta. Para obter uma resposta de previsão de consulta, use a [GeneateAnswer API](query-knowledge-base.md).

### <a name="prediction-score-fluctuations"></a>Flutuações da pontuação da previsão

Uma pontuação pode mudar com base em vários fatores:

* Número de respostas que solicitou em resposta a [GenerateAnswer](query-knowledge-base.md) com `top` propriedade
* Variedade de perguntas alternativas disponíveis
* Filtragem para metadados
* Consulta enviada para `test` ou `production` base de conhecimento

Há um [ranking de resposta em duas fases:](query-knowledge-base.md#how-qna-maker-processes-a-user-query-to-select-the-best-answer)
* Pesquisa Cognitiva - primeira classificação - Para que uma resposta regresse da Pesquisa Cognitiva, o número de _respostas permitidas_ precisa ser alto o suficiente para que as melhores respostas sejam devolvidas pela Pesquisa Cognitiva para que possam passar para o ranker do QnA Maker
* QnA Maker - segundo rank - aplicar a caracterização e machine learning para determinar a melhor resposta.

### <a name="service-updates"></a>Atualizações de serviço

As atualizações do serviço são geridas automaticamente aplicando as [atualizações de tempo de execução mais recentes](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

### <a name="scaling-throughput-and-resiliency"></a>Escala, produção e resiliência

A escala, a produção e a resiliência são determinadas pelos [recursos Azure,](../how-to/set-up-qnamaker-service-azure.md)os seus níveis de preços e qualquer arquitetura circundante, como [o Traffic Manager.](../how-to/set-up-qnamaker-service-azure.md#business-continuity-with-traffic-manager)

### <a name="analytics-with-application-insights"></a>Analytics com Insights de Aplicações

Todas as consultas à sua base de conhecimentos são armazenadas em Application Insights. Use [as nossas principais consultas](../how-to/get-analytics-knowledge-base.md) para entender as suas métricas.

## <a name="development-lifecycle"></a>Ciclo de vida de desenvolvimento

O ciclo de vida de [desenvolvimento](development-lifecycle-knowledge-base.md) de uma base de conhecimento está em curso: edição, teste e publicação da sua base de conhecimentos.

### <a name="knowledge-base-development-of-qna-maker-pairs"></a>Desenvolvimento de base de conhecimento dos pares QnA Maker

Os seus [pares QnA](question-answer-set.md) devem ser projetados e desenvolvidos com base no uso da aplicação do seu cliente.

Cada par pode conter:
* Metadados - filtrado ao consultar. Isto permite-lhe marcar os seus pares QnA com informações adicionais sobre a origem, conteúdo, formato e finalidade dos seus dados.
* Follow-up prompts - determine um caminho através da sua base de conhecimento para que o utilizador chegue à resposta correta.
* Perguntas alternativas - perguntas alternativas são importantes para permitir que a pesquisa corresponda à sua resposta a partir de uma variedade de formas de pergunta. [Sugestões de aprendizagem ativa](active-learning-suggestions.md) transformam-se em questões alternativas.

### <a name="devops-development"></a>Desenvolvimento de DevOps

O desenvolvimento de uma base de conhecimento para inserir num gasoduto DevOps requer que a base de conhecimento seja isolada durante os [testes de lote](../quickstarts/batch-testing.md).

Uma base de conhecimento partilha o índice de Pesquisa Cognitiva com todas as outras bases de conhecimento no recurso QnA Maker. Enquanto a base de conhecimento é isolada por partição, a partilha do índice pode causar uma diferença na pontuação quando comparada com a base de conhecimento publicada.

Para ter a _mesma pontuação_ nas `test` bases e `production` conhecimentos, isole um recurso QnA Maker para uma única base de conhecimento. Nesta arquitetura, o recurso só precisa de viver até ao comprimento do teste de lote isolado.

## <a name="next-step"></a>Passo seguinte

* [Recursos do Azure](../how-to/set-up-qnamaker-service-azure.md)
* [Pares de perguntas e respostas](question-answer-set.md)