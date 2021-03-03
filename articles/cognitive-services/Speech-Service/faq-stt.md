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
ms.date: 02/01/2021
ms.author: panosper
ms.openlocfilehash: 1ebba3231a7b3a86b98bcc14d1257412d1557ff3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738196"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Discurso ao Texto frequentemente feito perguntas

Se não conseguir encontrar respostas para as suas perguntas nestas PERGUNTAS, consulte [outras opções de suporte.](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)

## <a name="general"></a>Geral

**P: Qual é a diferença entre um modelo de base e um modelo personalizado de Discurso a Texto?**

**R**: Um modelo de base foi treinado utilizando dados da Microsoft e já está implantado na nuvem. Pode utilizar um modelo personalizado para adaptar um modelo para melhor se adaptar a um ambiente específico que tenha ruído ou linguagem ambiente específico. Pisos de fábrica, carros ou ruas barulhentas exigiriam um modelo acústico adaptado. Tópicos como biologia, física, radiologia, nomes de produtos e siglas personalizadas exigiriam um modelo de linguagem adaptado. Se treina um modelo personalizado, deve começar com texto relacionado para melhorar o reconhecimento de termos e frases especiais.

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

**P: Obtê vários resultados para cada frase com o formato de saída detalhado. Qual devo usar?**

**R**: Pegue sempre o primeiro resultado, mesmo que outro resultado ("N-Best") possa ter um valor de confiança maior. O serviço de Discurso considera que o primeiro resultado é o melhor. Também pode ser uma corda vazia se nenhum discurso foi reconhecido.

Os outros resultados são provavelmente piores e podem não ter capitalização e pontuação completas aplicadas. Estes resultados são mais úteis em cenários especiais, como dar aos utilizadores a opção de escolher correções de uma lista ou manusear comandos mal reconhecidos.

**P: Por que existem diferentes modelos base?**

**R**: Pode escolher entre mais de um modelo base no serviço Discurso. Cada nome de modelo contém a data em que foi adicionado. Quando começar a treinar um modelo personalizado, use o modelo mais recente para obter a melhor precisão. Os modelos base mais antigos ainda estão disponíveis durante algum tempo quando um novo modelo é disponibilizado. Pode continuar a utilizar o modelo com o qual trabalhou até ser retirado (ver [ciclo de vida modelo).](custom-speech-overview.md#model-lifecycle) Recomenda-se ainda mudar para o modelo base mais recente para uma maior precisão.

**P: Posso atualizar o meu modelo existente (empilhamento de modelos)?**

**R:** Não é possível atualizar um modelo existente. Como solução, combine o antigo conjunto de dados com o novo conjunto de dados e readapto.

O conjunto de dados antigo e o novo conjunto de dados devem ser combinados num único ficheiro .zip (para dados acústicos) ou num ficheiro .txt (para dados linguísticos). Quando a adaptação estiver concluída, o novo modelo atualizado precisa de ser redistribuído para obter um novo ponto final

**P: Quando uma nova versão de um modelo base está disponível, a minha implementação é atualizada automaticamente?**

**A**: As implementações NÃO serão atualizadas automaticamente.

Se tiver adaptado e implementado um modelo, essa implementação permanecerá como está. Pode desativar o modelo implantado, readaptar-se utilizando a versão mais recente do modelo base e recolocar a sua melhor precisão.

Ambos os modelos base e modelos personalizados serão retirados após algum tempo (ver [ciclo de vida modelo).](custom-speech-overview.md#model-lifecycle)

**P: Posso descarregar o meu modelo e executá-lo localmente?**

**R**: Pode executar um modelo personalizado localmente num [recipiente Docker](speech-container-howto.md?tabs=cstt).

**P: Posso copiar ou mover os meus conjuntos de dados, modelos e implementações para outra região ou subscrição?**

**R**: Pode utilizar a [API REST](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription) para copiar um modelo personalizado para outra região ou subscrição. Os conjuntos de dados ou implementações não podem ser copiados. Pode importar um conjunto de dados novamente em outra subscrição e criar pontos finais usando as cópias do modelo.

**P: Os meus pedidos estão registados?**

**A**: Por defeito, os pedidos não são registados (nem áudio, nem transcrição). Se necessário, poderá *selecionar o conteúdo de Log a partir desta* opção de ponto final quando criar um ponto final [personalizado](how-to-custom-speech-train-model.md#deploy-a-custom-model). Também pode ativar o registo áudio no [SDK](how-to-use-logging.md) do discurso numa base de pedido sem criar um ponto final personalizado. Em ambos os casos, os resultados áudio e de reconhecimento dos pedidos serão armazenados em armazenamento seguro. Para as subscrições que utilizam o armazenamento da Microsoft, estarão disponíveis durante 30 dias.

Pode exportar os ficheiros registados na página de implementação no Estúdio de Discurso se utilizar um ponto final personalizado com *conteúdo de Log a partir deste ponto final* ativado. Se o registo áudio estiver ativado através do SDK, ligue para a [API](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModelLogs) para aceder aos ficheiros.

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

**R**: O limite deve-se à restrição do tamanho de um ficheiro para upload HTTP. Consulte [quotas e limites de serviços de](speech-services-quotas-and-limits.md) fala para o limite real. Pode dividir os seus dados em vários conjuntos de dados e selecioná-los todos para treinar o modelo.

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

**A:** Sim. Pode transcrevê-lo por si mesmo ou utilizar um serviço profissional de transcrição. Alguns utilizadores preferem transcridores profissionais e outros usam crowdsourcing ou fazem as próprias transcrições.

**P: Quanto tempo vai demorar a treinar um modelo personalizado com dados áudio?**

**R**: Treinar um modelo com dados áudio pode ser um processo moroso. Dependendo da quantidade de dados, pode levar vários dias para criar um modelo personalizado. Se não puder ser terminado no prazo de uma semana, o serviço poderá abortar a operação de treino e reportar o modelo como falhado.

Utilize uma das [regiões](custom-speech-overview.md#set-up-your-azure-account) onde o hardware dedicado está disponível para treino. O serviço Discurso utilizará até 20 horas de áudio para formação nestas regiões. Noutras regiões, só utilizará até 8 horas.

Em geral, o serviço processa aproximadamente 10 horas de dados áudio por dia em regiões com hardware dedicado. Só pode processar cerca de 1 hora de dados áudio por dia noutras regiões. Pode copiar o modelo totalmente treinado para outra região utilizando a [API REST](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription). Treinar com texto justo é muito mais rápido e normalmente termina em minutos.

Alguns modelos base não podem ser personalizados com dados áudio. Para eles o serviço apenas usará o texto da transcrição para treino e ignorará os dados áudio. A formação será então terminada muito mais rapidamente e os resultados serão os mesmos que treinar com texto justo. Consulte [o suporte linguístico](language-support.md#speech-to-text) para uma lista de modelos base que suportam a formação com dados áudio.

## <a name="accuracy-testing"></a>Testes de precisão

**P: O que é a taxa de erro de palavra (WER) e como é calculada?**

**R**: WER é a métrica de avaliação para reconhecimento de voz. O WER é contado como o número total de erros, que inclui inserções, supressões e substituições, divididos pelo número total de palavras na transcrição de referência. Para obter mais informações, consulte [a precisão da fala personalizada.](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy)

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