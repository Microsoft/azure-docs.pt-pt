---
title: Transcrição do Call Center - Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Um cenário comum para o discurso-a-texto é transcrever grandes volumes de dados de telefonia que vêm de vários sistemas, como a Interactive Voice Response (IVR). Utilizando o serviço de Fala e o modelo de discurso unificado, um negócio pode obter transcrições de alta qualidade com sistemas de captura de áudio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: d959f4948d6b848f3b399c1310add06991d72012
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806325"
---
# <a name="speech-service-for-telephony-data"></a>Serviço de fala para dados de telefonia

Os dados de telefonia que são gerados através de telefones fixos, telemóveis e rádios são tipicamente de baixa qualidade, e estreitafaixa na gama de 8 KHz, o que cria desafios na conversão da fala-a-texto. Os mais recentes modelos de reconhecimento da fala do serviço da Fala primam pela transcrição destes dados de telefonia, mesmo nos casos em que os dados são difíceis de entender por um ser humano. Estes modelos são treinados com grandes volumes de dados de telefonia e têm a melhor precisão de reconhecimento no mercado, mesmo em ambientes ruidosos.

Um cenário comum para o discurso-a-texto é transcrever grandes volumes de dados de telefonia que podem vir de vários sistemas, como a Interactive Voice Response (IVR). O áudio que estes sistemas fornecem pode ser estéreo ou mono, e cru com pouco ou nenhum processamento pós-processamento feito no sinal. Utilizando o serviço de Fala e o modelo de discurso unificado, uma empresa pode obter transcrições de alta qualidade, quaisquer que sejam os sistemas utilizados para capturar áudio.

Os dados de telefonia podem ser usados para entender melhor as necessidades dos seus clientes, identificar novas oportunidades de marketing ou avaliar o desempenho dos agentes de call center. Depois de transcrever os dados, uma empresa pode utilizar a saída para fins como a telemetria melhorada, identificar frases-chave ou analisar o sentimento do cliente.

As tecnologias descritas nesta página são da Microsoft internamente para vários serviços de processamento de chamadas de suporte, tanto em tempo real como em modo de lote.

Vamos rever algumas das tecnologias e funcionalidades relacionadas que o serviço speech oferece.

> [!IMPORTANT]
> O modelo Unificado do serviço de fala é treinado com dados diversos e oferece uma solução de modelo único para uma série de cenários, desde dictação a análise de telefonia.

## <a name="azure-technology-for-call-centers"></a>Tecnologia Azure para Call Centers

Para além do aspeto funcional das funcionalidades do serviço Da Fala, o seu principal objetivo – quando aplicado ao call center – é melhorar a experiência do cliente. Existem três domínios claros a este respeito:

- Análise pós-chamada, que é essencialmente processamento de lotes de gravações de chamadas após a chamada.
- Análise em tempo real, que está a processar o sinal áudio para extrair vários insights à medida que a chamada está a ocorrer (com o sentimento sendo um caso de uso proeminente).
- Assistentes de voz (bots), quer impulsionando o diálogo entre o cliente e o bot numa tentativa de resolver o problema do cliente sem participação de agente, ou sendo a aplicação de protocolos de inteligência artificial (IA) para ajudar o agente.

Um diagrama típico de arquitetura da implementação de ![um cenário de lote é representado na imagem abaixo da arquitetura de transcrição do Call Center](media/scenarios/call-center-transcription-architecture.png)

## <a name="speech-analytics-technology-components"></a>Componentes tecnológicos de análise de discurso

Quer o domínio seja pós-chamada ou em tempo real, o Azure oferece um conjunto de tecnologias maduras e emergentes para melhorar a experiência do cliente.

### <a name="speech-to-text-stt"></a>Discurso ao texto (STT)

[O discurso ao texto](speech-to-text.md) é a característica mais procurada em qualquer solução de call center. Como muitos dos processos de análise a jusante dependem de texto transcrito, a taxa de erro da palavra _(WER)_ é da maior importância. Um dos principais desafios na transcrição do call center é o ruído que prevalece no call center (por exemplo, outros agentes que falam em segundo plano), a rica variedade de locais e dialetos linguísticos, bem como a baixa qualidade do sinal telefónico real. O WER está altamente correlacionado com o quão bem os modelos acústicos e linguísticos são treinados para um determinado local, pelo que a capacidade de personalizar o modelo ao seu local é importante. Os nossos mais recentes modelos unificados de versão 4.x são a solução tanto para a precisão da transcrição como para a latência. Treinados com dezenas de milhares de horas de dados acústicos e milhares de milhões de informação lexical, os modelos unificados são os modelos mais precisos do mercado para transcrever dados de call center.

### <a name="sentiment"></a>Sentimento

Avaliar se o cliente teve uma boa experiência é uma das áreas mais importantes da análise da Fala quando aplicada ao espaço do call center. A nossa API de [Transcrição](batch-transcription.md) de Lote oferece análise de sentimento por expressão. Pode agregar o conjunto de valores obtidos como parte de uma transcrição de chamada para determinar o sentimento da chamada tanto para os seus agentes como para o cliente.

### <a name="silence-non-talk"></a>Silêncio (não-falante)

Não é incomum que 35% de uma chamada de apoio seja aquilo a que chamamos tempo de não-conversação. Alguns cenários para os quais ocorre a não-conversação são: agentes que procuram o histórico de casos anteriores com um cliente, agentes que usam ferramentas que lhes permitem aceder ao ambiente de trabalho do cliente e executar funções, clientes sentados à espera de uma transferência, e assim por diante. É extremamente importante avaliar quando o silêncio ocorre numa chamada, uma vez que há um número de sensibilidades importantes do cliente que ocorrem em torno deste tipo de cenários e onde ocorrem na chamada.

### <a name="translation"></a>Tradução

Algumas empresas estão a experimentar fornecer transcrições traduzidas de chamadas de apoio a línguas estrangeiras para que os gestores de entregas possam compreender a experiência mundial dos seus clientes. As nossas capacidades de [tradução](translation.md) são insuperáveis. Podemos traduzir áudio-a-áudio ou áudio-a-texto para um grande número de locais.

### <a name="text-to-speech"></a>Conversão de Texto em Voz

[O texto-a-falar](text-to-speech.md) é outra área importante na implementação de bots que interagem com os clientes. O caminho típico é que o cliente fala, a sua voz é transcrita para texto, o texto é analisado para intenções, uma resposta é sintetizada com base na intenção reconhecida, e então um ativo é emergido ao cliente ou uma resposta de voz sintetizada é gerado. É claro que tudo isto tem de ocorrer rapidamente – por isso a baixa latência é uma componente importante no sucesso destes sistemas.

A nossa latência de ponta a ponta é consideravelmente baixa para as várias tecnologias envolvidas, tais como [o Discurso-a-texto,](speech-to-text.md) [LUIS,](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) [Bot Framework](https://dev.botframework.com/), [Texto-a-discurso.](text-to-speech.md)

As nossas novas vozes também são indistinguíveis das vozes humanas. Pode usar as nossas vozes para dar ao seu bot a sua personalidade única.

### <a name="search"></a>Pesquisa

Outro fundamento da análise é identificar interações onde ocorreu um evento ou experiência específico. Isto é normalmente feito com uma de duas abordagens; ou uma pesquisa ad hoc onde o utilizador simplesmente escreve uma frase e o sistema responde, ou uma consulta mais estruturada onde um analista pode criar um conjunto de declarações lógicas que identificam um cenário numa chamada, e então cada chamada pode ser indexada contra esse conjunto de consultas. Um bom exemplo de pesquisa é a declaração de conformidade ubíqua "esta chamada deve ser registada para fins de qualidade... ". Muitas empresas querem certificar-se de que os seus agentes estão a fornecer esta declaração de isenção aos clientes antes de a chamada ser realmente gravada. A maioria dos sistemas de análise tem a capacidade de tendência dos comportamentos encontrados por algoritmos de consulta/pesquisa, e este relato de tendências é, em última análise, uma das funções mais importantes de um sistema de análise. Através do [diretório de serviços cognitivos,](https://azure.microsoft.com/services/cognitive-services/directory/search/) a sua solução de ponta a ponta pode ser significativamente melhorada com capacidades de indexação e pesquisa.

### <a name="key-phrase-extraction"></a>Extração de Expressões-Chave

Esta área é uma das aplicações de análise mais desafiantes e que está a beneficiar da aplicação de IA e machine learning. O cenário principal neste caso é inferir a intenção do cliente. Porque é que o cliente está a ligar? Qual é o problema do cliente? Por que o cliente teve uma experiência negativa? O nosso serviço de análise de [texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/) fornece um conjunto de análises fora da caixa para atualizar rapidamente a sua solução de ponta a ponta para extrair essas palavras-chave ou frases importantes.

Vamos agora ver o processamento do lote e os oleodutos em tempo real para reconhecimento da fala com um pouco mais de detalhes.

## <a name="batch-transcription-of-call-center-data"></a>Transcrição de lote de dados de call center

Para transcrever áudio a granel desenvolvemos a API de [Transcrição](batch-transcription.md)de Lote . A API de Transcrição de Lote foi desenvolvida para transcrever grandes quantidades de dados áudio sincronicamente. No que diz respeito à transcrição de dados do call center, a nossa solução baseia-se nestes pilares:

- **Precisão** - Com modelos unificados de quarta geração, oferecemos uma qualidade de transcrição inigualável.
- **Latência** - Entendemos que, ao fazer transcrições a granel, as transcrições são necessárias rapidamente. Os trabalhos de transcrição iniciados através da API de [Transcrição](batch-transcription.md) de Lote serão feitos em fila imediatamente, e assim que o trabalho começar a funcionar, é executado mais rápido do que a transcrição em tempo real.
- **Segurança** - Entendemos que as chamadas podem conter dados sensíveis. Tenha certeza de que a segurança é uma das nossas maiores prioridades. O nosso serviço obteve certificações ISO, SOC, HIPAA, PCI.

Os call centers geram grandes volumes de dados áudio diariamente. Se o seu negócio armazenar dados de telefonia num local central, como o Armazenamento Azure, pode utilizar a API de [Transcrição](batch-transcription.md) de Lote para solicitar assincronicamente e receber transcrições.

Uma solução típica utiliza estes serviços:

- O serviço de Fala é usado para transcrever o discurso ao texto. É necessária uma subscrição padrão (S0) para o serviço Defala para utilizar a API de Transcrição de Lote. As subscrições gratuitas (F0) não funcionarão.
- [O Armazenamento Azure](https://azure.microsoft.com/services/storage/) é utilizado para armazenar dados de telefonia e as transcrições devolvidas pela API de Transcrição de Lote. Esta conta de armazenamento deve utilizar notificações, especificamente para quando forem adicionados novos ficheiros. Estas notificações são usadas para desencadear o processo de transcrição.
- [As Funções Azure](https://docs.microsoft.com/azure/azure-functions/) são utilizadas para criar as assinaturas de acesso partilhado (SAS) URI para cada gravação e desencadear o pedido HTTP POST para iniciar uma transcrição. Além disso, as Funções Azure são usadas para criar pedidos para recuperar e eliminar transcrições utilizando a API de Transcrição do Lote.

Internamente estamos a usar as tecnologias acima para suportar as chamadas de clientes da Microsoft no modo Batch.
![Arquitetura de lote](media/scenarios/call-center-batch-pipeline.png)

## <a name="real-time-transcription-for-call-center-data"></a>Transcrição em tempo real para dados de call center

Algumas empresas são obrigadas a transcrever conversas em tempo real. A transcrição em tempo real pode ser usada para identificar palavras-chave e desencadear pesquisas de conteúdos e recursos relevantes para a conversa, para monitorizar o sentimento, para melhorar a acessibilidade, ou para fornecer traduções para clientes e agentes que não são nativos falantes.

Para cenários que requerem transcrição em tempo real, recomendamos a utilização do [SDK](speech-sdk.md)do Discurso . Atualmente, o discurso-a-texto está disponível em mais de [20 idiomas](language-support.md), e o SDK está disponível em C++, C#, Java, Python, Node.js, Objective-C e JavaScript. As amostras estão disponíveis em cada idioma no [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk) Para obter as últimas novidades e atualizações, consulte [as notas de lançamento.](releasenotes.md)

Internamente estamos a usar as tecnologias acima para analisar as chamadas de clientes da Microsoft em tempo real à medida que acontecem, como ilustrado no diagrama seguinte.

![Arquitetura de lote](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>Uma palavra sobre ivrs

O serviço de Fala pode ser facilmente integrado em qualquer solução utilizando o [SDK](speech-sdk.md) da Fala ou a [API REST](rest-apis.md). No entanto, a transcrição do call center pode requerer tecnologias adicionais. Tipicamente, é necessária uma ligação entre um sistema IVR e o Azure. Embora não ofereçamos tais componentes, aqui está uma descrição do que uma ligação a um IVR implica.

Vários produtos de serviço iVR ou de telefonia (como Genesys ou AudioCodes) oferecem capacidades de integração que podem ser alavancadas para permitir a entrada e saída de áudio pass-through para um serviço Azure. Basicamente, um serviço Azure personalizado pode fornecer uma interface específica para definir sessões de chamadas telefónicas (como Call Start ou Call End) e expor um WebSocket API para receber áudio de fluxo de entrada que é usado com o serviço De Fala. As respostas de saída, tais como transcrição de conversas ou ligações com o Quadro Bot, podem ser sintetizadas com o serviço de texto-a-fala da Microsoft e devolvidas ao IVR para reprodução.

Outro cenário é a integração direta com o Protocolo de Iniciação de Sessão (SIP). Um serviço Azure liga-se a um SIP Server, obtendo assim um fluxo de entrada e um fluxo de saída, que é usado para as fases de fala-a-texto e texto-a-fala. Para ligar a um SIP Server existem ofertas de software comercial, como o Ozeki SDK, ou [as Equipas que ligam e encontram a API](/graph/api/resources/communications-api-overview) (atualmente em versão beta), que são projetadas para suportar este tipo de cenário para chamadas de áudio.

## <a name="customize-existing-experiences"></a>Personalize as experiências existentes

 O serviço da Fala funciona bem com modelos embutidos. No entanto, poderá querer personalizar e afinar ainda mais a experiência para o seu produto ou ambiente. As opções de personalização vão desde a sintonização do modelo acústico até fontes de voz únicas para a sua marca. Depois de ter construído um modelo personalizado, pode usá-lo com qualquer uma das funcionalidades do serviço Speech em tempo real ou em modo lote.

| Serviço de voz | Modelo | Descrição |
| -------------- | ----- | ----------- |
| Conversão de voz em texto | [Modelo acústico](how-to-customize-acoustic-models.md) | Crie um modelo acústico personalizado para aplicações, ferramentas ou dispositivos que sejam utilizados em ambientes particulares, como num carro ou no chão de uma fábrica, cada um com condições de gravação específicas. Exemplos incluem fala acentuada, ruídos de fundo específicos, ou usar um microfone específico para gravação. |
|                | [Modelo de linguagem](how-to-customize-language-model.md) | Crie um modelo de linguagem personalizado para melhorar a transcrição do vocabulário e gramática específicos da indústria, como terminologia médica, ou jargão de TI. |
|                | [Modelo de pronúncia](how-to-customize-pronunciation.md) | Com um modelo de pronúncia personalizado, pode definir o formulário fonético e exibir por uma palavra ou termo. É útil para manusear termos personalizados, como nomes de produtos ou siglas. Tudo o que precisa para começar é um `.txt` ficheiro de pronúncia, que é um simples arquivo. |
| Conversão de texto em voz | [Tipo de voz](how-to-customize-voice-font.md) | As fontes de voz personalizadas permitem criar uma voz única e reconhecível para a sua marca. Basta uma pequena quantidade de dados para começar. Quanto mais dados fornecer, mais natural e humano será a sua fonte de voz. |

## <a name="sample-code"></a>Código de exemplo

O código da amostra está disponível no GitHub para cada uma das funcionalidades do serviço Speech. Estas amostras cobrem cenários comuns como ler áudio de um ficheiro ou stream, reconhecimento contínuo e de tiro único, e trabalhar com modelos personalizados. Utilize estes links para visualizar amostras de SDK e REST:

- [Amostras de tradução de discurso soneato e fala (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Amostras de transcrição de lote (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Amostras de texto para a fala (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Doutorados de referência

- [API de Voz](speech-sdk-reference.md)
- [SDK de Dispositivos de Voz](speech-devices-sdk.md)
- [REST API: Discurso-a-texto](rest-speech-to-text.md)
- [REST API: Texto-a-falar](rest-text-to-speech.md)
- [REST API: Transcrição e personalização do lote](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Obtenha uma chave de subscrição do serviço Speech gratuitamente](get-started.md)
