---
title: Discurso ao Texto frequentemente feito perguntas
titleSuffix: Azure Cognitive Services
description: Obtenha respostas para perguntas frequentes sobre o serviço Discurso a Texto.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: panosper
ms.openlocfilehash: 32f6a9dae1a5b0be604b53d814ebc85cb7813b91
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353770"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Discurso ao Texto frequentemente feito perguntas

Se não conseguir encontrar respostas para as suas perguntas nestas PERGUNTAS, consulte [outras opções de suporte.](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)

## <a name="general"></a>Geral

**P: Qual é a diferença entre um modelo de base e um modelo personalizado de Discurso a Texto?**

**R**: Um modelo de base foi treinado utilizando dados da Microsoft e já está implantado na nuvem. Pode utilizar um modelo personalizado para adaptar um modelo para melhor se adaptar a um ambiente específico que tenha ruído ou linguagem ambiente específico. Pisos de fábrica, carros ou ruas barulhentas exigiriam um modelo acústico adaptado. Tópicos como biologia, física, radiologia, nomes de produtos e siglas personalizadas exigiriam um modelo de linguagem adaptado.

**P: Por onde começo se quero usar um modelo de base?**

**R**: Primeiro, obtenha uma [chave de subscrição](overview.md#try-the-speech-service-for-free). Se pretender fazer chamadas REST para os modelos de base pré-deplorados, consulte as [APIs REST](./overview.md#reference-docs). Se quiser utilizar webSockets, [descarregue o SDK](speech-sdk.md).

**P: Preciso sempre de construir um modelo de fala personalizado?**

**A:** Não. Se a sua aplicação utilizar linguagem genérica e quotidiana, não precisa de personalizar um modelo. Se a sua aplicação for usada num ambiente onde há pouco ou nenhum ruído de fundo, não precisa de personalizar um modelo.

Pode implementar modelos de linha de base e personalizados no portal e, em seguida, realizar testes de precisão contra eles. Pode utilizar esta funcionalidade para medir a precisão de um modelo de base em comparação com um modelo personalizado.

**P: Como saberei quando o processamento para o meu conjunto de dados ou modelo estiver completo?**

**R**: Atualmente, o estado do modelo ou conjunto de dados na tabela é a única forma de saber. Quando o processamento estiver concluído, o estado é **bem sucedido**.

**P: Posso criar mais do que um modelo?**

**R:** Não há limite para o número de modelos que pode ter na sua coleção.

**P: Percebi que cometi um erro. Como cancelo a importação de dados ou a criação de modelos que está em curso?**

**R**: Atualmente, não é possível reverter um processo de adaptação acústica ou linguística. Pode eliminar dados e modelos importados quando estão em estado terminal.

**P: Qual é a diferença entre o modelo De Pesquisa e Ditado e o modelo Conversador?**

**R**: Pode escolher entre mais de um modelo de base no serviço Discurso. O modelo Conversador é útil para reconhecer o discurso que é falado em estilo conversacional. Este modelo é ideal para transcrever chamadas telefónicas. O modelo Search and Ditam é ideal para aplicações ativadas por voz. O modelo Universal é um novo modelo que pretende abordar ambos os cenários. O modelo Universal encontra-se atualmente ao nível de qualidade do modelo Conversational na maioria dos locais.

**P: Posso atualizar o meu modelo existente (empilhamento de modelos)?**

**R:** Não é possível atualizar um modelo existente. Como solução, combine o antigo conjunto de dados com o novo conjunto de dados e readapto.

O conjunto de dados antigo e o novo conjunto de dados devem ser combinados num único ficheiro .zip (para dados acústicos) ou num ficheiro .txt (para dados linguísticos). Quando a adaptação estiver concluída, o novo modelo atualizado precisa de ser redistribuído para obter um novo ponto final

**P: Quando uma nova versão de uma linha de base está disponível, a minha implementação é atualizada automaticamente?**

**A**: As implementações NÃO serão atualizadas automaticamente.

Se tiver adaptado e implementado um modelo com V1.0 de base, essa implantação permanecerá como está. Os clientes podem desativar o modelo implantado, readaptar-se utilizando a versão mais recente da linha de base e redistribuição.

**P: Posso descarregar o meu modelo e executá-lo localmente?**

**R:** Os modelos não podem ser descarregados e executados localmente.

**P: Os meus pedidos estão registados?**

**A**: Por defeito, os pedidos não são registados (nem áudio, nem transcrição). Se necessário, poderá selecionar *o conteúdo de Log a partir desta* opção de ponto final quando criar um ponto final [personalizado](./how-to-custom-speech-train-model.md) para ativar o rastreio. Em seguida, os pedidos serão registados em Azure em armazenamento seguro.

**P: Os meus pedidos são acelerados?**

**R**: Ver [Quotas e Limites de Serviços de Fala.](speech-services-quotas-and-limits.md)

**P: Como sou cobrado por áudio de dois canais?**

**R**: Se submeter cada canal separadamente (cada canal no seu próprio ficheiro), será cobrado durante a duração de cada ficheiro. Se submeter um único ficheiro a cada canal multiplexed em conjunto, será cobrado durante a duração do ficheiro único. Para mais detalhes sobre os preços consulte a página de preços dos [Serviços Cognitivos Azure](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Se tiver mais preocupações de privacidade que o proíbam de utilizar o serviço de discurso personalizado, contacte um dos canais de suporte.

## <a name="increasing-concurrency"></a>Aumento da concordância
Ver [Quotas e Limites de Serviços de Fala.](speech-services-quotas-and-limits.md)


## <a name="importing-data"></a>Importar dados

**P: Qual é o limite do tamanho de um conjunto de dados, e por que é o limite?**

**R**: O limite deve-se à restrição do tamanho de um ficheiro para upload HTTP. Consulte [quotas e limites de serviços de](speech-services-quotas-and-limits.md) fala para o limite real.

**P: Posso fechar os meus ficheiros de texto para poder carregar um ficheiro de texto maior?**

**A:** Não. Atualmente, apenas são permitidos ficheiros de texto não comprimidos.

**P: O relatório de dados diz que houve declarações falhadas. Qual é o problema?**

**R**: Não conseguir carregar 100% das expressões num ficheiro não é um problema. Se a grande maioria das expressões num conjunto de dados acústico ou linguístico (por exemplo, mais de 95 por cento) forem importadas com sucesso, o conjunto de dados pode ser utilizável. No entanto, recomendamos que tente compreender por que razão as expressões falharam e corrigir os problemas. Os problemas mais comuns, como erros de formatação, são fáceis de corrigir.

## <a name="creating-an-acoustic-model"></a>Criar um modelo acústico

**P: De quantos dados acústicos preciso?**

**R**: Recomendamos que comece com entre 30 minutos e uma hora de dados acústicos.

**P: Que dados devo recolher?**

**R**: Recolher dados que se aproximem o cenário da aplicação e utilize o caso possível. A recolha de dados deve corresponder à aplicação-alvo e aos utilizadores em termos de dispositivos ou dispositivos, ambientes e tipos de altifalantes. Em geral, deve recolher dados do mais amplo alcance possível de alto-falantes.

**P: Como devo recolher dados acústicos?**

**R**: Pode criar uma aplicação autónoma de recolha de dados ou utilizar software de gravação áudio off-the-shelf. Também pode criar uma versão da sua aplicação que regista os dados áudio e depois utiliza os dados.

**P: Preciso de transcrever dados de adaptação?**

**A:** Sim! Pode transcrevê-lo por si mesmo ou utilizar um serviço profissional de transcrição. Alguns utilizadores preferem transcridores profissionais e outros usam crowdsourcing ou fazem as próprias transcrições.

## <a name="accuracy-testing"></a>Testes de precisão

**P: Posso realizar testes offline do meu modelo acústico personalizado utilizando um modelo de linguagem personalizada?**

**R:** Sim, basta selecionar o modelo de linguagem personalizado no menu suspenso quando configurar o teste offline.

**P: Posso realizar testes offline do meu modelo de linguagem personalizada utilizando um modelo acústico personalizado?**

**R:** Sim, basta selecionar o modelo acústico personalizado no menu suspenso quando configurar o teste offline.

**P: O que é a taxa de erro de palavra (WER) e como é calculada?**

**R**: WER é a métrica de avaliação para reconhecimento de voz. O WER é contado como o número total de erros, que inclui inserções, supressões e substituições, divididos pelo número total de palavras na transcrição de referência. Para obter mais informações, consulte [a taxa de erro de palavras](https://en.wikipedia.org/wiki/Word_error_rate).

**P: Como determino se os resultados de um teste de precisão são bons?**

**R**: Os resultados mostram uma comparação entre o modelo de base e o modelo que personalizou. Deve ter como objetivo vencer o modelo de base para que a personalização valha a pena.

**P: Como determino o WER de um modelo base para que eu possa ver se houve uma melhoria?**

**R**: Os resultados dos testes offline mostram a precisão da linha de base do modelo personalizado e a melhoria acima da linha de base.

## <a name="creating-a-language-model"></a>Criar um modelo linguístico

**P: Quantos dados de texto preciso para carregar?**

**R**: Depende de quão diferentes são os vocabulários e frases utilizados na sua aplicação dos modelos linguísticos iniciais. Para todas as novas palavras, é útil fornecer o maior número possível de exemplos do uso dessas palavras. Para frases comuns que são usadas na sua aplicação, incluindo frases nos dados linguísticos também é útil porque diz ao sistema para também ouvir estes termos. É comum ter pelo menos 100, e tipicamente várias centenas ou mais de expressões no conjunto de dados linguísticos. Além disso, se alguns tipos de consultas forem mais comuns do que outras, pode inserir várias cópias das consultas comuns no conjunto de dados.

**P: Posso fazer o upload de uma lista de palavras?**

**R**: Carregar uma lista de palavras irá adicionar as palavras ao vocabulário, mas não ensinará ao sistema como as palavras são normalmente usadas. Ao fornecer expressões completas ou parciais (frases ou frases de coisas que os utilizadores são suscetíveis de dizer), o modelo de idioma pode aprender as novas palavras e como são usadas. O modelo de linguagem personalizada é bom não só para adicionar novas palavras ao sistema, mas também para ajustar a probabilidade de palavras conhecidas para a sua aplicação. Fornecer expressões completas ajuda o sistema a aprender melhor.

## <a name="tenant-model-custom-speech-with-microsoft-365-data"></a>Modelo de inquilino (Discurso personalizado com dados da Microsoft 365)

**P: Que informação está incluída no Modelo de Inquilino, e como é criada?**

**A:** Um Modelo de Inquilino é construído usando e-mails de [grupo público](https://support.microsoft.com/office/learn-about-microsoft-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2) e documentos que podem ser vistos por qualquer pessoa na sua organização.

**P: Que experiências de fala são melhoradas pelo Modelo de Inquilino?**

**A:** Quando o Modelo de Inquilino está habilitado, criado e publicado, é utilizado para melhorar o reconhecimento de quaisquer aplicações empresariais construídas através do serviço Speech; que também passam um token AD do utilizador Azure indicando a adesão à empresa.

As experiências de fala incorporadas na Microsoft 365, tais como O Ditado e a Legendagem de PowerPoint, não são alteradas quando cria um Modelo de Inquilino para as suas aplicações de serviço de Discurso.

## <a name="next-steps"></a>Passos seguintes

- [Resolução de problemas](troubleshooting.md)
- [Notas de versão](releasenotes.md)