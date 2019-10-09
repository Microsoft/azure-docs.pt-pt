---
title: Perguntas frequentes sobre o serviço de fala para texto no Azure
titleSuffix: Azure Cognitive Services
description: Obtenha respostas para as perguntas mais populares sobre o serviço de fala para texto.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: bde68a70ac047433e86b7e06bc5f4a56bdd28595
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028507"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Perguntas frequentes sobre fala para texto

Se você não encontrar respostas para suas perguntas nestas perguntas frequentes, confira [outras opções de suporte](support.md).

## <a name="general"></a>Geral

**P: Qual é a diferença entre um modelo de linha de base e uma fala personalizada para o modelo de texto?**

**A**: Um modelo de linha de base foi treinado com o uso de dados de propriedade da Microsoft e já está implantado na nuvem.  Você pode usar um modelo personalizado para adaptar um modelo para se adequar melhor a um ambiente específico que tenha um ruído ou linguagem de ambiente específico. Os andares de fábrica, carros ou ruas ruidosas exigirão um modelo acústico adaptado. Tópicos como biologia, física, Radiologia, nomes de produtos e acrônimos personalizados exigirão um modelo de idioma adaptado.

**P: Onde devo iniciar se quiser usar um modelo de linha de base?**

**A**: Primeiro, obtenha uma [chave de assinatura](get-started.md). Se você quiser fazer chamadas REST para os modelos de linha de base preimplantados, consulte as [APIs REST](rest-apis.md). Se você quiser usar Websockets, [Baixe o SDK](speech-sdk.md).

**P: Sempre preciso criar um modelo de fala personalizado?**

**A**: Não. Se seu aplicativo usar o idioma genérico, do dia a dia, você não precisará personalizar um modelo. Se seu aplicativo for usado em um ambiente em que há pouco ou nenhum ruído de fundo, você não precisará personalizar um modelo.

Você pode implantar a linha de base e os modelos personalizados no portal e, em seguida, executar testes de precisão em relação a eles. Você pode usar esse recurso para medir a precisão de um modelo de linha de base versus um modelo personalizado.

**P: Como saber quando o processamento do meu conjunto de um ou meu modelo está concluído?**

**A**: Atualmente, o status do modelo ou do conjunto de uma tabela é a única maneira de saber. Quando o processamento for concluído, o status será com **êxito**.

**P: Posso criar mais de um modelo?**

**A**: Não há limite para o número de modelos que você pode ter em sua coleção.

**P: Percebi que eu fiz um erro. Como fazer cancelar minha importação de dados ou criação de modelo em andamento?**

**A**: No momento, não é possível reverter um processo acústico ou de adaptação de linguagem. Você pode excluir os dados e modelos importados quando estiverem em um estado terminal.

**P: Qual é a diferença entre o modelo de pesquisa e de ditado e o modelo de conversação?**

**A**: Você pode escolher entre mais de um modelo de linha de base no serviço de fala. O modelo de conversação é útil para reconhecer a fala que é falada em um estilo de conversação. Esse modelo é ideal para transcrever chamadas telefônicas. O modelo de pesquisa e de ditado é ideal para aplicativos disparados por voz. O modelo Universal é um novo modelo que visa lidar com ambos os cenários. O modelo Universal está no momento ou acima do nível de qualidade do modelo de conversação na maioria das localidades.

**P: Posso atualizar meu modelo existente (empilhamento de modelos)?**

**A**: Não é possível atualizar um modelo existente. Como solução, combine o conjunto de antigos com o novo conjunto de e readaptação.

O DataSet antigo e o novo conjunto de dados devem ser combinados em um único arquivo. zip (para dados acústicos) ou em um arquivo. txt (para dados de idioma). Quando a adaptação é concluída, o novo modelo atualizado precisa ser reimplantado para obter um novo ponto de extremidade

**P: Quando uma nova versão de uma linha de base está disponível, minha implantação é atualizada automaticamente?**

**A**: As implantações não serão atualizadas automaticamente.

Se você tiver adaptado e implantado um modelo com a linha de base V 1.0, essa implantação permanecerá como está. Os clientes podem desativar o modelo implantado, adaptar-se novamente usando a versão mais recente da linha de base e reimplantar.

**P: E se eu precisar de uma simultaneidade maior para meu modelo implantado do que o que é oferecido no portal?**

**A**: Você pode escalar verticalmente seu modelo em incrementos de 20 solicitações simultâneas.

Contate o [suporte de fala](mailto:speechsupport@microsoft.com?subject=Request%20for%20higher%20concurrency%20for%20Speech-to-text) se você precisar de uma escala mais alta.

**P: Posso baixar meu modelo e executá-lo localmente?**

**A**: Os modelos não podem ser baixados e executados localmente.

**P: Minhas solicitações estão registradas?**

**A**: Você tem uma opção ao criar uma implantação para desativar o rastreamento. Nesse ponto, nenhum áudio ou transcrição será registrado. Caso contrário, as solicitações normalmente são registradas no Azure no armazenamento seguro.

**P: Minhas solicitações estão limitadas?**

**A**: A API REST limita as solicitações a 25 por 5 segundos. Os detalhes podem ser encontrados em nossas páginas de [fala para texto](speech-to-text.md).

**P: Como sou cobrado por áudio de canal duplo?**

**A**: Se você enviar cada canal separadamente (cada canal em seu próprio arquivo), você será cobrado de acordo com a duração de cada arquivo. Se você enviar um único arquivo com cada canal multiplexado, você será cobrado pela duração do único arquivo.

> [!IMPORTANT]
> Se você tiver outras preocupações de privacidade que proíbam de usar o serviço de fala personalizado, entre em contato com um dos canais de suporte.

## <a name="importing-data"></a>Importar dados

**P: Qual é o limite do tamanho de um conjunto de um e por que ele é o limite?**

**A**: O limite atual para um conjunto de um DataSet é 2 GB. O limite é devido à restrição do tamanho de um arquivo para carregamento HTTP. 

**P: Posso compactar meus arquivos de texto para poder carregar um arquivo de texto maior?** 

**A**: Não. Atualmente, somente arquivos de texto não compactados são permitidos.

**P: O relatório de dados diz que houve falha em declarações. Qual é o problema?**

**A**: Falha ao carregar 100 por cento do declarações em um arquivo não é um problema. Se a grande maioria dos declarações em um conjunto de informações acústico ou de linguagem (por exemplo, mais de 95 por cento) for importada com êxito, o conjunto de informações poderá ser usado. No entanto, recomendamos que você tente entender por que a declarações falhou e corrigiu os problemas. Os problemas mais comuns, como erros de formatação, são fáceis de corrigir. 

## <a name="creating-an-acoustic-model"></a>Criando um modelo acústico

**P: Quanto de dados acústicos preciso?**

**A**: É recomendável começar com entre 30 minutos e uma hora de dados acústicos.

**P: Quais dados devo coletar?**

**A**: Colete dados que estejam mais próximos do cenário de aplicativo e use o caso de uso possível. A coleta de dados deve corresponder ao aplicativo de destino e aos usuários em termos de dispositivos, ambientes e tipos de alto-falantes. Em geral, você deve coletar dados do maior número possível de alto-falantes. 

**P: Como devo coletar dados acústicos?**

**A**: Você pode criar um aplicativo de coleta de dados autônomo ou usar o software de gravação de áudio fora do mercado. Você também pode criar uma versão do seu aplicativo que registra os dados de áudio e, em seguida, usa os dados. 

**P: Eu preciso transcrever dados de adaptação?**

**A**: Sim! Você mesmo pode transcrever ou usar um serviço de transcrição profissional. Alguns usuários preferem que os assinantes profissionais e outros usam crowdsourcing ou fazem as transcrições em si.

## <a name="accuracy-testing"></a>Teste de precisão

**P: Posso executar testes offline do meu modelo acústico personalizado usando um modelo de linguagem personalizado?**

**A**: Sim, basta selecionar o modelo de idioma personalizado no menu suspenso ao configurar o teste offline.

**P: Posso executar testes offline do meu modelo de linguagem personalizado usando um modelo acústico personalizado?**

**A**: Sim, basta selecionar o modelo acústico personalizado no menu suspenso ao configurar o teste offline.

**P: O que é a taxa de erros do Word (WER) e como ela é computada?**

**A**: WER é a métrica de avaliação para o reconhecimento de fala. O WER é contado como o número total de erros, que inclui inserções, exclusões e substituições, dividida pelo número total de palavras na transcrição de referência. Para obter mais informações, consulte [taxa de erros do Word](https://en.wikipedia.org/wiki/Word_error_rate).

**P: Como fazer determinar se os resultados de um teste de precisão são bons?**

**A**: Os resultados mostram uma comparação entre o modelo de linha de base e o modelo que você personalizou. Você deve visar superar o modelo de linha de base para tornar a personalização válida.

**P: Como fazer determinar o WER de um modelo base para que eu possa ver se houve um aperfeiçoamento?** 

**A**: Os resultados do teste offline mostram a precisão da linha de base do modelo personalizado e a melhoria em relação à linha de base.

## <a name="creating-a-language-model"></a>Criando um modelo de linguagem

**P: Quanto de dados de texto eu preciso carregar?**

**A**: Depende de quão diferentes o vocabulário e as frases usadas em seu aplicativo são dos modelos de linguagem iniciais. Para todas as novas palavras, é útil fornecer o máximo possível de exemplos do uso dessas palavras. Para frases comuns que são usadas em seu aplicativo, incluindo frases nos dados de linguagem também é útil porque diz ao sistema para também escutar esses termos. É comum ter pelo menos 100, e normalmente várias centenas ou mais declarações no conjunto de informações de linguagem. Além disso, se espera-se que alguns tipos de consultas sejam mais comuns do que outros, você pode inserir várias cópias das consultas comuns no conjunto de informações.

**P: Posso simplesmente carregar uma lista de palavras?**

**A**: Carregar uma lista de palavras adicionará as palavras ao vocabulário, mas não ensinará ao sistema como as palavras normalmente são usadas. Ao fornecer declarações totais ou parciais (frases ou frases de coisas que os usuários têm probabilidade de dizer), o modelo de linguagem pode aprender as novas palavras e como elas são usadas. O modelo de linguagem personalizado é bom não apenas para adicionar novas palavras ao sistema, mas também para ajustar a probabilidade de palavras conhecidas para seu aplicativo. Fornecer declarações completo ajuda o sistema a aprender melhor. 

## <a name="next-steps"></a>Passos seguintes

* [Resolução de problemas](troubleshooting.md)
* [Notas de versão](releasenotes.md)
