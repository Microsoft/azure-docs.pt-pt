---
title: Discurso ao Texto frequentemente feito perguntas
titleSuffix: Azure Cognitive Services
description: Obtenha respostas a perguntas frequentes sobre o serviço De Discurso ao Texto.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/4/2019
ms.author: panosper
ms.openlocfilehash: a279aebdd19ebd3a41ddad0c1c279937e00838c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77168451"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Discurso ao Texto frequentemente feito perguntas

Se não encontrar respostas para as suas perguntas neste FAQ, confira [outras opções](support.md)de suporte .

## <a name="general"></a>Geral

**P: Qual é a diferença entre um modelo de linha de base e um modelo personalizado de Discurso ao Texto?**

**R**: Um modelo de base foi treinado utilizando dados da Microsoft e já está implantado na nuvem. Você pode usar um modelo personalizado para adaptar um modelo para melhor se adaptar a um ambiente específico que tenha ruído ou linguagem ambiente específico. Pisos de fábrica, carros ou ruas barulhentas exigiriam um modelo acústico adaptado. Temas como biologia, física, radiologia, nomes de produtos e siglas personalizadas exigiriam um modelo de linguagem adaptado.

**P: Por onde começo se quero usar um modelo de base?**

**R**: Primeiro, obtenha uma [chave de subscrição](get-started.md). Se quiser fazer chamadas REST para os modelos de base pré-implantados, consulte as [APIs rest](rest-apis.md). Se quiser utilizar WebSockets, [descarregue o SDK](speech-sdk.md).

**P: Preciso sempre de construir um modelo de fala personalizado?**

**A:** Não. Se a sua aplicação utilizar uma linguagem genérica, dia-a-dia, não precisa de personalizar um modelo. Se a sua aplicação for usada num ambiente onde há pouco ou nenhum ruído de fundo, não precisa de personalizar um modelo.

Pode implementar modelos de base e personalizados no portal e, em seguida, executar testes de precisão contra eles. Pode utilizar esta funcionalidade para medir a precisão de um modelo de base contra um modelo personalizado.

**P: Como saberei quando o processamento do meu conjunto de dados ou modelo estiver completo?**

**R**: Atualmente, o estado do modelo ou conjunto de dados na tabela é a única forma de saber. Quando o processamento estiver concluído, o estado é **bem sucedido**.

**P: Posso criar mais do que um modelo?**

**R:** Não há limite para o número de modelos que pode ter na sua coleção.

**P: Apercebi-me que cometi um erro. Como posso cancelar a importação de dados ou criação de modelos que está em andamento?**

**R:** Atualmente, não pode reverter um processo de adaptação acústica ou linguística. Pode eliminar dados e modelos importados quando estão em estado terminal.

**P: Qual é a diferença entre o modelo de Pesquisa e Dictação e o modelo Conversacional?**

**R:** Pode escolher entre mais do que um modelo de base no serviço da Fala. O modelo Conversational é útil para reconhecer o discurso que é falado num estilo de conversação. Este modelo é ideal para transcrever chamadas telefónicas. O modelo Search and Dictation é ideal para aplicações ativadas por voz. O modelo Universal é um novo modelo que visa abordar ambos os cenários. O modelo Universal está atualmente no nível de qualidade do modelo Conversacional na maioria dos locais.

**P: Posso atualizar o meu modelo existente (model stacking)?**

**R:** Não é possível atualizar um modelo existente. Como solução, combine o conjunto de dados antigo com o novo conjunto de dados e readapte-se.

O conjunto de dados antigo e o novo conjunto de dados devem ser combinados num único ficheiro .zip (para dados acústicos) ou num ficheiro .txt (para dados linguísticos). Quando a adaptação estiver concluída, o novo modelo atualizado precisa de ser reimplantado para obter um novo ponto final

**P: Quando uma nova versão de uma linha de base está disponível, a minha implementação é atualizada automaticamente?**

**R:** As implementações NÃO serão atualizadas automaticamente.

Se tiver adaptado e implementado um modelo com v1.0 de base, essa implantação permanecerá tal como está. Os clientes podem desativar o modelo implantado, readaptar-se utilizando a versão mais recente da linha de base e reimplantando.a.

**P: Posso baixar o meu modelo e executá-lo localmente?**

**R:** Os modelos não podem ser descarregados e executados localmente.

**P: Os meus pedidos estão registados?**

**R:** Tem uma escolha quando cria uma implementação para desligar o rastreio. Nessa altura, não serão registadas áudio ou transcrições. Caso contrário, os pedidos são normalmente registados em Azure em armazenamento seguro.

**P: Os meus pedidos estão acelerados?**

**R:** O REST API limita os pedidos a 25 por 5 segundos. Os detalhes podem ser encontrados nas nossas páginas para [o Discurso para texto](speech-to-text.md).

**P: Como sou cobrado por áudio de canal duplo?**

**R**: Se submeter cada canal separadamente (cada canal no seu próprio ficheiro), será cobrado durante a duração de cada ficheiro. Se submeter um único ficheiro com cada canal multiplexed em conjunto, será cobrado durante a duração do ficheiro único. Para mais informações sobre os preços, consulte a página de preços dos [Serviços Cognitivos Azure](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Se tiver mais preocupações de privacidade que o proíbam de usar o serviço de Discurso personalizado, contacte um dos canais de suporte.

## <a name="increasing-concurrency"></a>Aumento da conmoeda

**P: E se eu precisar de uma moeda mais alta para o meu modelo implantado do que o que é oferecido no portal?**

**R:** Pode aumentar o seu modelo em incrementos de 20 pedidos simultâneos.

Com as informações necessárias, crie um pedido de apoio no portal de [suporte Azure.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) Não publique a informação em nenhum dos canais públicos (GitHub, Stackoverflow, ...) mencionada na página de [suporte](support.md).

Para aumentar a moeda para um ***modelo personalizado,*** precisamos das seguintes informações:

- A região onde o modelo é implantado,
- a identificação do ponto final do modelo implantado:
  - Cheguei ao Portal da [Fala Personalizada,](https://aka.ms/customspeech)
  - iniciar o seu insaque (se necessário),
  - selecione o seu projeto e implementação,
  - selecione o ponto final para o que precisa do aumento da moeda,
  - copiar `Endpoint ID`o .

Para aumentar a moeda para um ***modelo base,*** precisamos das seguintes informações:

- A região do seu serviço,

e ambos

- um sinal de acesso para a sua subscrição (ver [aqui),](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#how-to-get-an-access-token)

ou

- o ID de recurso para a sua subscrição:
  - Vá ao [portal Azure,](https://portal.azure.com)
  - selecionar `Cognitive Services` na caixa de pesquisa,
  - dos serviços exibidos escolha o serviço de Fala que você quer que a concurrency aumentada,
  - exibir `Properties` o para este serviço,
  - copiar o `Resource ID`completo .

## <a name="importing-data"></a>Importar dados

**P: Qual é o limite do tamanho de um conjunto de dados, e por que é o limite?**

**R:** O limite atual para um conjunto de dados é de 2 GB. O limite deve-se à restrição do tamanho de um ficheiro para upload HTTP.

**P: Posso fechar os meus ficheiros de texto para poder carregar um ficheiro de texto maior?**

**A:** Não. Atualmente, apenas são permitidos ficheiros de texto não comprimidos.

**P: O relatório de dados diz que houve declarações falhadas. Qual é o problema?**

**R:** Não fazer o upload de 100% das palavras num ficheiro não é um problema. Se a grande maioria das expressões num conjunto de dados acústico ou linguístico (por exemplo, mais de 95 por cento) forem importados com sucesso, o conjunto de dados pode ser utilizável. No entanto, recomendamos que tente entender por que as declarações falharam e resolver os problemas. Os problemas mais comuns, como erros de formatação, são fáceis de corrigir.

## <a name="creating-an-acoustic-model"></a>Criar um modelo acústico

**P: Quantos dados acústicos preciso?**

**R:** Recomendamos começar com entre 30 minutos e uma hora de dados acústicos.

**P: Que dados devo recolher?**

**R:** Recolher dados o mais próximo possível do cenário de aplicação e utilizar o caso. A recolha de dados deve corresponder à aplicação-alvo e aos utilizadores em termos de dispositivos ou dispositivos, ambientes e tipos de altifalantes. Em geral, deve recolher dados de um leque mais alargado possível de oradores.

**P: Como devo recolher dados acústicos?**

**R:** Pode criar uma aplicação de recolha autónoma de dados ou utilizar um software de gravação áudio off-the-shelf. Também pode criar uma versão da sua aplicação que regista os dados áudio e depois utiliza os dados.

**P: Preciso transcrever dados de adaptação?**

**A:** Sim! Pode transcrever ou utilizar um serviço profissional de transcrição. Alguns utilizadores preferem transcritores profissionais e outros usam crowdsourcing ou fazem as próprias transcrições.

## <a name="accuracy-testing"></a>Teste de precisão

**P: Posso realizar testes offline do meu modelo acústico personalizado usando um modelo de linguagem personalizada?**

**R:** Sim, basta selecionar o modelo de idioma personalizado no menu suspenso quando configurar o teste offline.

**P: Posso realizar testes offline do meu modelo de idioma personalizado usando um modelo acústico personalizado?**

**R:** Sim, basta selecionar o modelo acústico personalizado no menu suspenso quando configurar o teste offline.

**P: O que é a taxa de erro de palavra (WER) e como é calculada?**

**R:** WER é a métrica de avaliação para o reconhecimento da fala. O WER é contado como o número total de erros, que inclui inserções, supressões e substituições, divididas pelo número total de palavras na transcrição de referência. Para mais informações, consulte [a taxa de erro da palavra](https://en.wikipedia.org/wiki/Word_error_rate).

**P: Como determino se os resultados de um teste de precisão são bons?**

**R:** Os resultados mostram uma comparação entre o modelo de base e o modelo que personalizou. Deve tentar vencer o modelo de base para que a personalização valha a pena.

**P: Como determino o WER de um modelo base para que eu possa ver se houve uma melhoria?**

**R:** Os resultados dos testes offline mostram a precisão de base do modelo personalizado e a melhoria em relação à linha de base.

## <a name="creating-a-language-model"></a>Criar um modelo linguístico

**P: Quantos dados de texto preciso para carregar?**

**R:** Depende da diferença entre o vocabulário e as frases utilizadas na sua aplicação dos modelos linguísticos iniciantes. Para todas as novas palavras, é útil fornecer o maior número possível de exemplos do uso dessas palavras. Para frases comuns que são usadas na sua aplicação, incluindo frases nos dados linguísticos também é útil porque diz ao sistema para ouvir também estes termos. É comum ter pelo menos 100, e tipicamente várias centenas ou mais de expressões no conjunto de dados linguísticos. Além disso, se espera que alguns tipos de consultas sejam mais comuns do que outros, pode inserir várias cópias das consultas comuns no conjunto de dados.

**P: Posso fazer o upload de uma lista de palavras?**

**R:** Carregar uma lista de palavras irá adicionar as palavras ao vocabulário, mas não ensinará ao sistema como as palavras são normalmente usadas. Ao fornecer expressões completas ou parciais (frases ou frases de coisas que os utilizadores são suscetíveis de dizer), o modelo de idioma pode aprender as novas palavras e como são usadas. O modelo de linguagem personalizada é bom não só para adicionar novas palavras ao sistema, mas também para ajustar a probabilidade de palavras conhecidas para a sua aplicação. Fornecer expressões completas ajuda o sistema a aprender melhor.

## <a name="tenant-model-custom-speech-with-office-365-data"></a>Modelo de inquilino (Discurso Personalizado com dados do Office 365)

**P: Que informação está incluída no Modelo de Inquilino, e como é criada?**

**A:** Um Modelo de Inquilino é construído usando e-mails de [grupo público](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2) e documentos que podem ser vistos por qualquer pessoa da sua organização.

**P: Que experiências de fala são melhoradas pelo Modelo de Inquilino?**

**A:** Quando o Modelo de Inquilino está habilitado, criado e publicado, é utilizado para melhorar o reconhecimento de quaisquer aplicações empresariais construídas utilizando o serviço De fala; que também passam um token AAD do utilizador indicando a adesão à empresa.

As experiências de fala incorporadas no Office 365, como dictação e legendagem de PowerPoint, não são alteradas quando cria um Modelo de Inquilino para as suas aplicações de serviço de Fala.

## <a name="next-steps"></a>Passos seguintes

- [Resolução de problemas](troubleshooting.md)
- [Notas de versão](releasenotes.md)
