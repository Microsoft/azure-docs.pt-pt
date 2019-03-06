---
title: Perguntas mais frequentes sobre a conversão de voz em serviço de texto no Azure
titleSuffix: Azure Cognitive Services
description: Obtenha respostas às perguntas mais populares sobre a conversão de voz em serviço de texto.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 29ec50a1d2e0aebb1aeb73b38c662f4a18624754
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57431993"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Conversão de voz em texto perguntas mais frequentes

Se não conseguir encontrar respostas para suas perguntas neste FAQ, confira [outras opções de suporte](support.md).

## <a name="general"></a>Geral

**P: O que é a diferença entre um modelo de linha de base e uma conversão de voz personalizada ao modelo de texto?**

**A**: Um modelo de linha de base foi treinado, utilizando dados pertencentes à Microsoft e já está implementado na cloud.  Pode utilizar um modelo personalizado para adaptar um modelo para se adequar melhor um ambiente específico que tenha o ruído do específico ou de idioma. Chão de fábrica, em carros ou streets desnecessárias exigiriam um modelo acústico adaptado. Tópicos como biologia, física, radiology, nomes de produtos e acrônimos personalizados requer um modelo de idioma adaptado.

**P: Por onde começar se eu quiser usar um modelo de linha de base?**

**A**: Primeiro, obtenha uma [chave de subscrição](get-started.md). Se quiser fazer chamadas REST para os modelos de linha de base predeployed, consulte a [REST APIs](rest-apis.md). Se pretender utilizar WebSockets, [transferir o SDK](speech-sdk.md).

**P: É sempre necessário criar um modelo de voz personalizada?**

**A**: Não. Se a sua aplicação utiliza genérico, linguagem diária, não precisa de personalizar um modelo. Se seu aplicativo é usado num ambiente onde há pouco ou nenhum barulho de fundo, não precisa personalizar um modelo.

Pode implementar a linha de base e modelos personalizados no portal e, em seguida, executar testes de precisão contra eles. Pode utilizar esta funcionalidade para medir a precisão de um modelo de linha de base em comparação com um modelo personalizado.

**P: Como posso saber quando o processamento de meu conjunto de dados ou o modelo for concluído?**

**A**: Atualmente, o estado do modelo ou do conjunto de dados na tabela é a única forma de saber. Quando o processamento estiver concluído, o estado é **bem-sucedido**.

**P: Pode criar mais de um modelo?**

**A**: Não existe nenhum limite no número de modelos, que pode ter em sua coleção.

**P: Percebi que cometi um erro. Como cancelar a minha importação de dados ou modelo de criação está em curso?**

**A**: Atualmente, não é possível reverter um processo de adaptação acústicos ou linguagem. Pode eliminar os dados importados e modelos quando estiverem num Estado terminal.

**P: O que é a diferença entre o modelo de pesquisa e ditado e o modelo de conversação?**

**A**: Pode escolher entre mais de um modelo de linha de base no serviço de voz. O modelo de conversação é útil para reconhecimento de voz que é falado num estilo de conversação. Esse modelo é ideal para fotografar chamadas telefónicas. O modelo de pesquisa e ditado é ideal para aplicações acionadas por voz. O modelo Universal é um novo modelo que pretende resolver ambos os cenários. O modelo Universal está atualmente no ou acima do nível de qualidade do modelo de conversação na maioria das localidades.

**P: Posso atualizar o meu modelo existente (empilhamento de modelo)?**

**A**: Não é possível atualizar um modelo existente. Como uma solução, combine o conjunto de dados antigo com o novo conjunto de dados e readapt.

O conjunto de dados antigo e novo conjunto de dados devem ser combinadas num único ficheiro. zip (para dados acústicos) ou num arquivo. txt (para dados de idioma). Quando a adaptação estiver concluída, o modelo de novo e atualizado tem de ser implementado novamente para obter um novo ponto final

**P: Quando estiver disponível uma nova versão de uma linha de base é a minha implementação atualizada automaticamente?**

**A**: As implementações não serão atualizadas automaticamente.

Se tiver adaptada e implementado um modelo com a linha de base V1.0, essa implementação permanecerá como está. Os clientes podem desativar o modelo implementado, novamente se adaptar a utilizar a versão mais recente da linha de base e voltar a implementar.

**P: E se eu precisar uma simultaneidade mais elevada para meu modelo implementado que o que está disponível no portal?**

**A**: Pode aumentar verticalmente o seu modelo em incrementos de 20 pedidos simultâneos.

Contacte-nos se necessitar de uma escala maior.

**P: Pode transferir o meu modelo e executá-lo localmente?**

**A**: Modelos não podem ser transferidos e executados localmente.

**P: Os meus pedidos são registrados?**

**A**: Tem uma opção quando cria uma implementação de optar por desativar o rastreio. Nessa altura, sem áudio ou transcrições serão registadas. Caso contrário, pedidos, normalmente, são registados no Azure no armazenamento seguro.

**P: Os meus pedidos limitados?**

**A**: A API REST limita os pedidos para 25 por 5 segundos. Pode encontrar detalhes em nossas páginas para [conversão de voz em texto](speech-to-text.md).

Se tiver mais questões de privacidade que proíbem a utilização do serviço de voz personalizada, entre em contato com um dos canais de suporte.

## <a name="importing-data"></a>Importar dados

**P: O que é o limite no tamanho de um conjunto de dados e, por que é o limite?**

**A**: O limite atual para um conjunto de dados é de 2 GB. O limite é devido à restrição no tamanho de um ficheiro para o upload HTTP. 

**P: Eu zip meus arquivos de texto, de modo que pode carregar um ficheiro de texto maior?** 

**A**: Não. Atualmente, apenas os ficheiros de texto não comprimidos são permitidos.

**P: O relatório de dados diz que havia expressões com falha. O que é o problema?**

**A**: Falha ao carregar a 100 por cento das expressões num arquivo não é um problema. Se a grande maioria das expressões num conjunto de dados acústicos ou idioma (por exemplo, mais de 95 por cento) é importada com êxito, o conjunto de dados pode ser utilizado. No entanto, recomendamos que tente compreender por que as expressões com falha e corrigir os problemas. Problemas mais comuns, como formatação de erros, são fáceis de corrigir. 

## <a name="creating-an-acoustic-model"></a>Criar um modelo acústico

**P: A quantidade de dados acústico é necessário?**

**A**: Recomendamos que comece com entre 30 minutos e uma hora de dados acústicos.

**P: Os dados que posso coletar?**

**A**: Recolher dados que é o mais próximo o cenário de aplicação e utilizar as maiúsculas e minúsculas possível. A recolha de dados deve corresponder a aplicação de destino e os utilizadores em termos de dispositivo ou dispositivos, ambientes e tipos de oradores. Em geral, deve recolher dados a partir de mais amplo um intervalo de oradores possível. 

**P: Como posso coletar os dados acústicos?**

**A**: Pode criar uma aplicação de recolha de dados autónoma ou utilizar o software de gravação de áudio off-the-shelf. Também pode criar uma versão do seu aplicativo que registra os dados de áudio e, em seguida, utiliza os dados. 

**P: Preciso de dados de adaptação de transcrever eu mesmo?**

**A**: Sim! Pode transcrever por conta própria ou utilizar um serviço de transcrição profissional. Alguns usuários preferem transcribers profissionais e outras pessoas utilizam crowdsourcing ou fazer as transcrições propriamente ditas.

## <a name="accuracy-testing"></a>Testes de precisão

**P: Posso executar testes offline de meu modelo acústico personalizado utilizando um modelo de idioma personalizado?**

**A**: Sim, basta selecione o modelo de idioma personalizado no menu pendente quando configurou o teste offline.

**P: Posso executar testes offline de meu modelo de idioma personalizado, utilizando um modelo acústico personalizado?**

**A**: Sim, basta selecione o modelo acústico personalizado no menu pendente quando configurou o teste offline.

**P: O que é a taxa de erros do word (WER) e como é calculada?**

**A**: WER é a métrica de avaliação de reconhecimento de fala. WER é contabilizado como o número total de erros, que inclui inserções, eliminações e substituições, divididas pelo número total de palavras na transcrição de referência. Para obter mais informações, consulte [taxa de erros do word](https://en.wikipedia.org/wiki/Word_error_rate).

**P: Como posso determinar se os resultados de um teste de precisão são bons?**

**A**: Os resultados mostram uma comparação entre o modelo de linha de base e o modelo, que personalizou. Tente atacar o modelo de linha de base para fazer a personalização que vale a pena.

**P: Como posso determinar o WER de um modelo de base para que eu possa ver se havia uma melhoria?** 

**A**: Os resultados de teste offline mostram a precisão de linha de base do modelo personalizado com a melhoria ao longo da linha de base.

## <a name="creating-a-language-model"></a>Criar um modelo de idioma

**P: Quantos dados de texto é necessário carregar?**

**A**: Depende de como diferente o vocabulário e expressões usados em seu aplicativo são provenientes os modelos de linguagem de partida. Para todas as novas palavras, é útil fornecer exemplos tantos possível da utilização dessas palavras. Para expressões comuns que são utilizados na sua aplicação, incluindo expressões nos dados de idioma também é útil porque ele informa ao sistema para escutar também estes termos. É comum ter, pelo menos, 100 e, normalmente, várias centenas ou mais expressões com o conjunto de dados do idioma. Além disso, se alguns tipos de consultas devem ser mais comuns do que outras pessoas, pode inserir várias cópias das consultas comuns no conjunto de dados.

**P: Só posso carregar uma lista de palavras?**

**A**: Carregar uma lista de palavras adicionará as palavras ao vocabulário, mas ele não irá ensinar sobre o sistema como as palavras são normalmente utilizadas. Ao fornecer expressões com total ou parciais (frases ou expressões de coisas que os usuários são provavelmente diria), o modelo de idioma pode aprender novas palavras e como elas são usadas. O modelo de idioma personalizado é bom, não apenas para adicionar novas palavras para o sistema, mas também para ajustar a probabilidade de palavras conhecidas para a sua aplicação. Fornecimento de expressões completo com ajuda o sistema saiba a melhor. 

## <a name="next-steps"></a>Passos Seguintes

* [Resolução de problemas](troubleshooting.md)
* [Notas de versão](releasenotes.md)
