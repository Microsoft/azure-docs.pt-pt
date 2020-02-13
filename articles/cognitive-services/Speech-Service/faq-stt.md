---
title: Perguntas frequentes sobre fala para texto
titleSuffix: Azure Cognitive Services
description: Obtenha respostas para perguntas frequentes sobre o serviço de fala para texto.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/4/2019
ms.author: panosper
ms.openlocfilehash: a279aebdd19ebd3a41ddad0c1c279937e00838c2
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168451"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Perguntas frequentes sobre fala para texto

Se não encontrar respostas para as suas perguntas neste FAQ, confira [outras opções](support.md)de suporte .

## <a name="general"></a>Geral

**P: Qual é a diferença entre um modelo de linha de base e um modelo personalizado de Discurso ao Texto?**

**R**: Um modelo de base foi treinado utilizando dados da Microsoft e já está implantado na nuvem. Você pode usar um modelo personalizado para adaptar um modelo para se adequar melhor a um ambiente específico que tenha um ruído ou linguagem de ambiente específico. Os andares de fábrica, carros ou ruas ruidosas exigirão um modelo acústico adaptado. Tópicos como biologia, física, Radiologia, nomes de produtos e acrônimos personalizados exigirão um modelo de idioma adaptado.

**P: Por onde começo se quero usar um modelo de base?**

**R**: Primeiro, obtenha uma [chave de subscrição](get-started.md). Se quiser fazer chamadas REST para os modelos de base pré-implantados, consulte as [APIs rest](rest-apis.md). Se quiser utilizar WebSockets, [descarregue o SDK](speech-sdk.md).

**P: Preciso sempre de construir um modelo de fala personalizado?**

**A:** Não. Se seu aplicativo usar o idioma genérico, do dia a dia, você não precisará personalizar um modelo. Se seu aplicativo for usado em um ambiente em que há pouco ou nenhum ruído de fundo, você não precisará personalizar um modelo.

Você pode implantar a linha de base e os modelos personalizados no portal e, em seguida, executar testes de precisão em relação a eles. Você pode usar esse recurso para medir a precisão de um modelo de linha de base versus um modelo personalizado.

**P: Como saberei quando o processamento do meu conjunto de dados ou modelo estiver completo?**

**R**: Atualmente, o estado do modelo ou conjunto de dados na tabela é a única forma de saber. Quando o processamento estiver concluído, o estado é **bem sucedido**.

**P: Posso criar mais do que um modelo?**

**R:** Não há limite para o número de modelos que pode ter na sua coleção.

**P: Apercebi-me que cometi um erro. Como posso cancelar a importação de dados ou criação de modelos que está em andamento?**

**R:** Atualmente, não pode reverter um processo de adaptação acústica ou linguística. Você pode excluir os dados e modelos importados quando estiverem em um estado terminal.

**P: Qual é a diferença entre o modelo de Pesquisa e Dictação e o modelo Conversacional?**

**R:** Pode escolher entre mais do que um modelo de base no serviço da Fala. O modelo de conversação é útil para reconhecer a fala que é falada em um estilo de conversação. Esse modelo é ideal para transcrever chamadas telefônicas. O modelo de pesquisa e de ditado é ideal para aplicativos disparados por voz. O modelo Universal é um novo modelo que visa lidar com ambos os cenários. O modelo Universal está no momento ou acima do nível de qualidade do modelo de conversação na maioria das localidades.

**P: Posso atualizar o meu modelo existente (model stacking)?**

**R:** Não é possível atualizar um modelo existente. Como solução, combine o conjunto de antigos com o novo conjunto de e readaptação.

O DataSet antigo e o novo conjunto de dados devem ser combinados em um único arquivo. zip (para dados acústicos) ou em um arquivo. txt (para dados de idioma). Quando a adaptação é concluída, o novo modelo atualizado precisa ser reimplantado para obter um novo ponto de extremidade

**P: Quando uma nova versão de uma linha de base está disponível, a minha implementação é atualizada automaticamente?**

**R:** As implementações NÃO serão atualizadas automaticamente.

Se você tiver adaptado e implantado um modelo com a linha de base V 1.0, essa implantação permanecerá como está. Os clientes podem desativar o modelo implantado, readaptar-se usando a versão mais recente da linha de base e reimplantar.

**P: Posso baixar o meu modelo e executá-lo localmente?**

**R:** Os modelos não podem ser descarregados e executados localmente.

**P: Os meus pedidos estão registados?**

**R:** Tem uma escolha quando cria uma implementação para desligar o rastreio. Nesse ponto, nenhum áudio ou transcrição será registrado. Caso contrário, as solicitações normalmente são registradas no Azure no armazenamento seguro.

**P: Os meus pedidos estão acelerados?**

**R:** O REST API limita os pedidos a 25 por 5 segundos. Os detalhes podem ser encontrados nas nossas páginas para [o Discurso para texto](speech-to-text.md).

**P: Como sou cobrado por áudio de canal duplo?**

**R**: Se submeter cada canal separadamente (cada canal no seu próprio ficheiro), será cobrado durante a duração de cada ficheiro. Se você enviar um único arquivo com cada canal multiplexado, você será cobrado pela duração do único arquivo. Para mais informações sobre os preços, consulte a página de preços dos [Serviços Cognitivos Azure](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Se você tiver outras preocupações de privacidade que proíbam de usar o serviço de fala personalizado, entre em contato com um dos canais de suporte.

## <a name="increasing-concurrency"></a>Aumentando a simultaneidade

**P: E se eu precisar de uma moeda mais alta para o meu modelo implantado do que o que é oferecido no portal?**

**R:** Pode aumentar o seu modelo em incrementos de 20 pedidos simultâneos.

Com as informações necessárias, crie um pedido de apoio no portal de [suporte Azure.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) Não publique a informação em nenhum dos canais públicos (GitHub, Stackoverflow, ...) mencionada na página de [suporte](support.md).

Para aumentar a moeda para um ***modelo personalizado,*** precisamos das seguintes informações:

- A região em que o modelo é implantado,
- a ID do ponto de extremidade do modelo implantado:
  - Cheguei ao Portal da [Fala Personalizada,](https://aka.ms/customspeech)
  - entrar (se necessário),
  - Selecione seu projeto e implantação,
  - Selecione o ponto de extremidade para o qual você precisa do aumento de simultaneidade,
  - copiar o `Endpoint ID`.

Para aumentar a moeda para um ***modelo base,*** precisamos das seguintes informações:

- A região do seu serviço,

e qualquer um

- um sinal de acesso para a sua subscrição (ver [aqui),](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#how-to-get-an-access-token)

ou

- a ID de recurso para sua assinatura:
  - Vá ao [portal Azure,](https://portal.azure.com)
  - selecione `Cognitive Services` na caixa de pesquisa,
  - dos serviços exibidos escolha o serviço de Fala que você quer que a concurrency aumentada,
  - exibir o `Properties` para este serviço,
  - copiar o `Resource ID`completo.

## <a name="importing-data"></a>Importar dados

**P: Qual é o limite do tamanho de um conjunto de dados, e por que é o limite?**

**R:** O limite atual para um conjunto de dados é de 2 GB. O limite é devido à restrição do tamanho de um arquivo para carregamento HTTP.

**P: Posso fechar os meus ficheiros de texto para poder carregar um ficheiro de texto maior?**

**A:** Não. Atualmente, somente arquivos de texto não compactados são permitidos.

**P: O relatório de dados diz que houve declarações falhadas. Qual é o problema?**

**R:** Não fazer o upload de 100% das palavras num ficheiro não é um problema. Se a grande maioria dos declarações em um conjunto de informações acústico ou de linguagem (por exemplo, mais de 95 por cento) for importada com êxito, o conjunto de informações poderá ser usado. No entanto, recomendamos que você tente entender por que a declarações falhou e corrigiu os problemas. Os problemas mais comuns, como erros de formatação, são fáceis de corrigir.

## <a name="creating-an-acoustic-model"></a>Criando um modelo acústico

**P: Quantos dados acústicos preciso?**

**R:** Recomendamos começar com entre 30 minutos e uma hora de dados acústicos.

**P: Que dados devo recolher?**

**R:** Recolher dados o mais próximo possível do cenário de aplicação e utilizar o caso. A coleta de dados deve corresponder ao aplicativo de destino e aos usuários em termos de dispositivos, ambientes e tipos de alto-falantes. Em geral, você deve coletar dados do maior número possível de alto-falantes.

**P: Como devo recolher dados acústicos?**

**R:** Pode criar uma aplicação de recolha autónoma de dados ou utilizar um software de gravação áudio off-the-shelf. Você também pode criar uma versão do seu aplicativo que registra os dados de áudio e, em seguida, usa os dados.

**P: Preciso transcrever dados de adaptação?**

**A:** Sim! Você mesmo pode transcrever ou usar um serviço de transcrição profissional. Alguns usuários preferem que os assinantes profissionais e outros usam crowdsourcing ou fazem as transcrições em si.

## <a name="accuracy-testing"></a>Teste de precisão

**P: Posso realizar testes offline do meu modelo acústico personalizado usando um modelo de linguagem personalizada?**

**R:** Sim, basta selecionar o modelo de idioma personalizado no menu suspenso quando configurar o teste offline.

**P: Posso realizar testes offline do meu modelo de idioma personalizado usando um modelo acústico personalizado?**

**R:** Sim, basta selecionar o modelo acústico personalizado no menu suspenso quando configurar o teste offline.

**P: O que é a taxa de erro de palavra (WER) e como é calculada?**

**R:** WER é a métrica de avaliação para o reconhecimento da fala. O WER é contado como o número total de erros, que inclui inserções, exclusões e substituições, dividida pelo número total de palavras na transcrição de referência. Para mais informações, consulte [a taxa de erro da palavra](https://en.wikipedia.org/wiki/Word_error_rate).

**P: Como determino se os resultados de um teste de precisão são bons?**

**R:** Os resultados mostram uma comparação entre o modelo de base e o modelo que personalizou. Você deve visar superar o modelo de linha de base para tornar a personalização válida.

**P: Como determino o WER de um modelo base para que eu possa ver se houve uma melhoria?**

**R:** Os resultados dos testes offline mostram a precisão de base do modelo personalizado e a melhoria em relação à linha de base.

## <a name="creating-a-language-model"></a>Criando um modelo de linguagem

**P: Quantos dados de texto preciso para carregar?**

**R:** Depende da diferença entre o vocabulário e as frases utilizadas na sua aplicação dos modelos linguísticos iniciantes. Para todas as novas palavras, é útil fornecer o máximo possível de exemplos do uso dessas palavras. Para frases comuns que são usadas em seu aplicativo, incluindo frases nos dados de linguagem também é útil porque diz ao sistema para também escutar esses termos. É comum ter pelo menos 100, e normalmente várias centenas ou mais declarações no conjunto de informações de linguagem. Além disso, se espera-se que alguns tipos de consultas sejam mais comuns do que outros, você pode inserir várias cópias das consultas comuns no conjunto de informações.

**P: Posso fazer o upload de uma lista de palavras?**

**R:** Carregar uma lista de palavras irá adicionar as palavras ao vocabulário, mas não ensinará ao sistema como as palavras são normalmente usadas. Ao fornecer declarações totais ou parciais (frases ou frases de coisas que os usuários têm probabilidade de dizer), o modelo de linguagem pode aprender as novas palavras e como elas são usadas. O modelo de linguagem personalizado é bom não apenas para adicionar novas palavras ao sistema, mas também para ajustar a probabilidade de palavras conhecidas para seu aplicativo. Fornecer declarações completo ajuda o sistema a aprender melhor.

## <a name="tenant-model-custom-speech-with-office-365-data"></a>Modelo de locatário (Fala Personalizada com dados do Office 365)

**P: Que informação está incluída no Modelo de Inquilino, e como é criada?**

**A:** Um Modelo de Inquilino é construído usando e-mails de [grupo público](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2) e documentos que podem ser vistos por qualquer pessoa da sua organização.

**P: Que experiências de fala são melhoradas pelo Modelo de Inquilino?**

**A:** Quando o Modelo de Inquilino está habilitado, criado e publicado, é utilizado para melhorar o reconhecimento de quaisquer aplicações empresariais construídas utilizando o serviço De fala; que também passam um token AAD do utilizador indicando a adesão à empresa.

As experiências de fala incorporadas ao Office 365, como ditado e legendas do PowerPoint, não são alteradas quando você cria um modelo de locatário para seus aplicativos de serviço de fala.

## <a name="next-steps"></a>Passos seguintes

- [Resolução de problemas](troubleshooting.md)
- [Notas de versão](releasenotes.md)
