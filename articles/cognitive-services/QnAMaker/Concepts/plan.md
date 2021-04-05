---
title: Planeie a sua aplicação - QnA Maker
description: Saiba como planear a sua aplicação QnA Maker. Entenda como a QnA Maker funciona e interage com outros serviços Azure e alguns conceitos de base de conhecimento.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: e20679c3999f7ece1f6d3ed47a241cfd9dab9236
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102214750"
---
# <a name="plan-your-qna-maker-app"></a>Planeie a sua app QnA Maker

Para planear a sua aplicação QnA Maker, precisa entender como a QnA Maker funciona e interage com outros serviços Azure. Você também deve ter uma compreensão sólida dos conceitos de base de conhecimento.

## <a name="azure-resources"></a>Recursos do Azure

Cada [recurso Azure](azure-resources.md#resource-purposes) criado com o QnA Maker tem um propósito específico. Cada recurso tem o seu próprio propósito, limites e [nível de preços.](azure-resources.md#pricing-tier-considerations) É importante compreender a função destes recursos para que possa usar esse conhecimento no seu processo de planeamento.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

| Recurso | Objetivo |
|--|--|
| [Recurso do Fabricante QnA](azure-resources.md#qna-maker-resource) | Previsão de autoria e consulta |
| [Recurso de Pesquisa Cognitiva](azure-resources.md#cognitive-search-resource) | Armazenamento e pesquisa de dados |
| [Recurso de serviço de aplicações e](azure-resources.md#app-service-and-app-service-plan) recurso de serviço de plano de aplicações | Ponto final de predição da consulta |
| [Recurso de Insights de Aplicação](azure-resources.md#application-insights) | Telemetria de previsão de consulta |

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

| Recurso | Objetivo |
|--|--|
| [Recurso do Fabricante QnA](azure-resources.md#qna-maker-resource) | Autoria, previsão de consulta e telemetria|
| [Recurso de Pesquisa Cognitiva](azure-resources.md#cognitive-search-resource) | Armazenamento e pesquisa de dados |

---
### <a name="resource-planning"></a>Planeamento de recursos

O nível `F0` livre, de cada recurso funciona e pode fornecer tanto a experiência de autoria como a experiência de previsão de consulta. Você pode usar este nível para aprender a autorizar e consultar a previsão. Quando se muda para um cenário de produção ou ao vivo, reavalie a sua seleção de recursos.

#### <a name="qna-maker-resource"></a>Recurso do Fabricante QnA

Um único recurso QnA Maker pode acolher mais do que uma base de conhecimento. O número de bases de conhecimento é determinado pela quantidade de índices suportados pelo nível de preços da Pesquisa Cognitiva. Saiba mais sobre a [relação dos índices com bases de conhecimento.](azure-resources.md#index-usage)

#### <a name="knowledge-base-size-and-throughput"></a>Tamanho e produção da base do conhecimento

Quando construir uma aplicação real, planeie recursos suficientes para o tamanho da sua base de conhecimento e para os seus pedidos de previsão de consulta esperada.

Um tamanho de base de conhecimento é controlado pelo:
* [Limites](../../../search/search-limits-quotas-capacity.md) de níveis de preços dos recursos de pesquisa cognitiva
* [Limites do Fabricante QnA](../limits.md)

O pedido de previsão de consulta de base de conhecimento é controlado pelo plano de aplicações web e aplicação web. Consulte as [definições recomendadas](azure-resources.md#recommended-settings) para planear o seu nível de preços.

### <a name="resource-sharing"></a>Partilha de recursos

Se já tem alguns destes recursos em uso, pode considerar a partilha de recursos. Veja quais os recursos que [podem ser partilhados](azure-resources.md#share-services-with-qna-maker) com a compreensão de que a partilha de recursos é um cenário avançado.

Todas as bases de conhecimento criadas no mesmo recurso QnA Maker partilham o mesmo ponto final de previsão de consulta de **teste.**

### <a name="understand-the-impact-of-resource-selection"></a>Compreender o impacto da seleção de recursos

A seleção adequada de recursos significa que a sua base de conhecimento responde com sucesso às previsões de consulta.

Se a sua base de conhecimento não está a funcionar corretamente, é tipicamente uma questão de gestão de recursos inadequada.

A seleção inadequada de recursos requer uma investigação para determinar que [recurso precisa de ser alterado](azure-resources.md#when-to-change-a-pricing-tier).

## <a name="knowledge-bases"></a>Bases de conhecimento

Uma base de conhecimento está diretamente ligada ao seu recurso QnA Maker. Contém os pares de perguntas e respostas (QnA) que são usados para responder a pedidos de previsão de consulta.

### <a name="language-considerations"></a>Considerações linguísticas

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

A primeira base de conhecimento criada no seu recurso QnA Maker define o idioma para o recurso. Só pode ter uma língua para um recurso QnA Maker.

Pode estruturar os seus recursos QnA Maker por idioma ou pode usar [o Tradutor](../../translator/translator-info-overview.md) para alterar uma consulta de outra língua para a linguagem da base de conhecimento antes de enviar a consulta para o ponto final de previsão de consulta.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

Agora pode ter bases de conhecimento em diferentes idiomas dentro do mesmo recurso QnA Maker. Quando criar a primeira base de conhecimento, pode escolher se pretende utilizar o recurso para bases de conhecimento numa única língua ou em várias línguas.

![O QnA Maker geriu (Preview) a seleção de base de conhecimento multilingue](../media/concept-plan-your-knowledge-base/qnamaker-v2-select-multilanguage-knowledge-base.png)

> [!NOTE]
> Se ativar as definições de idiomas por base de conhecimento, não pode criar tantas bases de conhecimento no seu recurso QnA Maker. Para [obter mais detalhes sobre as limitações de definições de idiomas](./azure-resources.md).

---

### <a name="ingest-data-sources"></a>Ingerir fontes de dados

Pode utilizar uma das [seguintes fontes](../Concepts/data-sources-and-content.md) de dados ingeridas para criar uma base de conhecimento:

* URL público
* Private SharePoint URL
* Ficheiro

O processo de ingestão converte [tipos de conteúdo suportados](../reference-document-format-guidelines.md) para marcação. Toda a edição adicional da *resposta* é feita com marcação. Depois de criar uma base de conhecimento, pode editar [pares QnA](question-answer-set.md) no portal QnA Maker com [uma rica autoria de texto.](../how-to/edit-knowledge-base.md#rich-text-editing-for-answer)

### <a name="data-format-considerations"></a>Considerações de formato de dados

Como o formato final de um par QnA é marcado, é importante entender o suporte de [marcação](../reference-markdown-format.md).

As imagens ligadas devem estar disponíveis a partir de um URL público para serem exibidas no painel de teste do portal QnA Maker ou numa aplicação ao cliente. O QnA Maker não fornece autenticação para conteúdos, incluindo imagens.

### <a name="bot-personality"></a>Personalidade bot

Adicione uma personalidade bot à sua base de conhecimento com [chit-chat](../how-to/chit-chat-knowledge-base.md). Esta personalidade surge com respostas fornecidas num certo tom de conversação, como *profissional* e *amigável.* Este chit-chat é fornecido como um conjunto de conversação, que você tem controlo total para adicionar, editar e remover.

Recomenda-se uma personalidade bot se o seu bot se ligar à sua base de conhecimento. Você pode optar por usar o chit-chat na sua base de conhecimento, mesmo que também se ligue a outros serviços, mas deve rever como o serviço de bot interage para saber se esse é o design arquitetônico correto para o seu uso.

### <a name="conversation-flow-with-a-knowledge-base"></a>Fluxo de conversação com uma base de conhecimento

O fluxo de conversação geralmente começa com uma saudação de um utilizador, como `Hi` ou `Hello` . A sua base de conhecimentos pode responder com uma resposta geral, `Hi, how can I help you` como, por exemplo, e também pode fornecer uma seleção de pedidos de acompanhamento para continuar a conversa.

Deve desenhar o seu fluxo de conversação com um loop em mente para que um utilizador saiba usar o seu bot e não seja abandonado pelo bot na conversação. [As instruções de seguimento](../how-to/multiturn-conversation.md) fornecem ligações entre pares QnA, que permitem o fluxo de conversação.

### <a name="authoring-with-collaborators"></a>Autoria com colaboradores

Os colaboradores podem ser outros desenvolvedores que partilham a pilha completa de desenvolvimento da aplicação base de conhecimento ou podem limitar-se a apenas autorizar a base de conhecimento.

A autoria da base de conhecimento suporta várias permissões de acesso baseadas em funções que aplica no portal Azure para limitar o âmbito das capacidades de um colaborador.

## <a name="integration-with-client-applications"></a>Integração com aplicações de clientes

A integração com as aplicações dos clientes é conseguida enviando uma consulta para o ponto final de tempo de previsão. Uma consulta é enviada para a sua base de conhecimentos específica com um pedido baseado em SDK ou REST para o ponto final da aplicação web do seu QnA Maker.

Para autenticar corretamente um pedido de cliente, a aplicação do cliente deve enviar as credenciais corretas e o ID da base de conhecimento. Se estiver a utilizar um Serviço Azure Bot, configufique estas definições como parte da configuração do bot no portal Azure.

### <a name="conversation-flow-in-a-client-application"></a>Fluxo de conversação em uma aplicação de cliente

O fluxo de conversação numa aplicação do cliente, como um bot Azure, pode exigir funcionalidade antes e depois de interagir com a base de conhecimento.

O fluxo de conversação da sua aplicação do cliente, quer fornecendo meios alternativos para lidar com as solicitações de seguimento, quer incluindo chit-chit? Em caso afirmativo, desenhe-os precocemente e certifique-se de que a consulta de aplicação do cliente é tratada corretamente por outro serviço ou quando enviada para a sua base de conhecimentos.

### <a name="dispatch-between-qna-maker-and-language-understanding-luis"></a>Despacho entre o Criador QnA e a Compreensão da Língua (LUIS)

Uma aplicação de cliente pode fornecer várias funcionalidades, apenas uma das quais é respondida por uma base de conhecimento. Outras funcionalidades ainda precisam entender o texto de conversação e extrair significado dele.

Uma arquitetura comum de aplicações de cliente é usar tanto qnA Maker e [Language Understanding (LUIS)](../../LUIS/what-is-luis.md) juntos. A LUIS fornece a classificação e extração de texto para qualquer consulta, incluindo a outros serviços. A QnA Maker fornece respostas a partir da sua base de conhecimento.

Num cenário de [arquitetura partilhada,](../choose-natural-language-processing-service.md) o despacho entre os dois serviços é realizado pela ferramenta [Despacho](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) da Bot Framework.

### <a name="active-learning-from-a-client-application"></a>Aprendizagem ativa a partir de uma aplicação de cliente

O QnA Maker utiliza _a aprendizagem ativa_ para melhorar a sua base de conhecimento, sugerindo perguntas alternativas a uma resposta. A aplicação do cliente é responsável por uma parte desta [aprendizagem ativa.](../How-To/use-active-learning.md) Através de solicitações de conversação, a aplicação do cliente pode determinar que a base de conhecimento devolveu uma resposta que não é útil para o utilizador, e pode determinar uma melhor resposta. A aplicação do cliente precisa enviar essa informação de volta para a base de conhecimento para melhorar a qualidade da previsão.

### <a name="providing-a-default-answer"></a>Fornecendo uma resposta padrão

Se a sua base de conhecimento não encontrar uma resposta, ela devolve a _resposta por defeito_. Esta resposta é configurável na página **Definições** no portal QnA Maker ou nas [APIs](/rest/api/cognitiveservices/qnamaker/knowledgebase/update#request-body).

Esta resposta padrão é diferente da resposta padrão do bot Azure. Configura a resposta padrão para o seu bot Azure no portal Azure como parte das definições de configuração. É devolvido quando o limiar de pontuação não é cumprido.

## <a name="prediction"></a>Predição

A previsão é a resposta da sua base de conhecimento, e inclui mais informação do que apenas a resposta. Para obter uma resposta de previsão de consulta, utilize a [API GenerateAnswer](query-knowledge-base.md).

### <a name="prediction-score-fluctuations"></a>Flutuações da pontuação da previsão

Uma pontuação pode mudar com base em vários fatores:

* Número de respostas que solicitou em resposta a [GenerateAnswer](query-knowledge-base.md) com `top` propriedade
* Variedade de perguntas alternativas disponíveis
* Filtragem para metadados
* Consulta enviada para `test` ou `production` base de conhecimento

Há um [ranking de resposta em duas fases:](query-knowledge-base.md#how-qna-maker-processes-a-user-query-to-select-the-best-answer)
- Pesquisa Cognitiva - primeira patente. Desagreie o número de _respostas permitidas_ suficientemente alto para que as melhores respostas sejam devolvidas pela Cognitive Search e depois passadas para o ranker do QnA Maker.
- QnA Maker - segundo escalão. Aplicar a caracterização e machine learning para determinar a melhor resposta.

### <a name="service-updates"></a>Atualizações de serviço

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

Aplique as [atualizações de tempo de execução mais recentes](../how-to/configure-QnA-Maker-resources.md#get-the-latest-runtime-updates) para gerir automaticamente as atualizações de serviço.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

Na gestão do QnA Maker (Preview), o tempo de execução é gerido pelo próprio serviço QnA Maker. Assim, as atualizações de serviço não são aplicáveis.

---

### <a name="scaling-throughput-and-resiliency"></a>Escala, produção e resiliência

A escala, a produção e a resiliência são determinadas pelos [recursos Azure,](../how-to/set-up-qnamaker-service-azure.md)os seus níveis de preços e qualquer arquitetura circundante, como [o Traffic Manager.](../how-to/configure-QnA-Maker-resources.md#business-continuity-with-traffic-manager)

### <a name="analytics-with-application-insights"></a>Analytics com Insights de Aplicações

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

Todas as consultas à sua base de conhecimentos são armazenadas em Application Insights. Use [as nossas principais consultas](../how-to/get-analytics-knowledge-base.md) para entender as suas métricas.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

Na implantação gerida, a telemetria é oferecida através do [serviço Azure Monitor](../../../azure-monitor/index.yml). Use [as nossas principais consultas](../how-to/get-analytics-knowledge-base.md) para entender as suas métricas.


---

## <a name="development-lifecycle"></a>Ciclo de vida de desenvolvimento

O ciclo de vida de [desenvolvimento](development-lifecycle-knowledge-base.md) de uma base de conhecimento está em curso: edição, teste e publicação da sua base de conhecimentos.

### <a name="knowledge-base-development-of-qna-maker-pairs"></a>Desenvolvimento de base de conhecimento dos pares QnA Maker

Os seus pares QnA devem ser projetados e desenvolvidos com base no uso da aplicação do seu cliente.

Cada par pode conter:
* Metadados - filtrados ao consultar para permitir marcar os seus pares QnA com informações adicionais sobre a origem, conteúdo, formato e finalidade dos seus dados.
* Pedidos de acompanhamento - ajuda a determinar um caminho através da sua base de conhecimento para que o utilizador chegue à resposta correta.
* Perguntas alternativas - importante para permitir que a pesquisa corresponda à sua resposta de diferentes formas de pergunta. [Sugestões de aprendizagem ativa](../How-To/use-active-learning.md) transformam-se em questões alternativas.

### <a name="devops-development"></a>Desenvolvimento de DevOps

O desenvolvimento de uma base de conhecimento para inserir num gasoduto DevOps requer que a base de conhecimento seja isolada durante os testes de lote.

Uma base de conhecimento partilha o índice de Pesquisa Cognitiva com todas as outras bases de conhecimento no recurso QnA Maker. Enquanto a base de conhecimento é isolada por partição, a partilha do índice pode causar uma diferença na pontuação quando comparada com a base de conhecimento publicada.

Para ter a _mesma pontuação_ nas `test` bases e `production` conhecimento, isole um recurso QnA Maker para uma única base de conhecimento. Nesta arquitetura, o recurso só precisa de viver enquanto o teste de lote isolado.

## <a name="next-steps"></a>Passos seguintes

* [Recursos do Azure](../how-to/set-up-qnamaker-service-azure.md)
* [Pares de perguntas e respostas](question-answer-set.md)
