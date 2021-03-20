---
title: Transcrição do Call Center - Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Um cenário comum para falar-a-texto é transcrever grandes volumes de dados de telefonia que vêm de vários sistemas, como a Interactive Voice Response (IVR). Utilizando o serviço de fala e o modelo de fala unificado, uma empresa pode obter transcrições de alta qualidade com sistemas de captação de áudio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 19d4cc388494e149b7f258a8e9f154041a3dd070
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95021971"
---
# <a name="speech-service-for-telephony-data"></a>Serviço de fala para dados de telefonia

Os dados de telefonia que são gerados através de telefones fixos, telemóveis e rádios são tipicamente de baixa qualidade, e estreitam a faixa na gama de 8 KHz, o que cria desafios na conversão de discurso-a-texto. Os últimos modelos de reconhecimento de voz do serviço Desíofone primam pela transcrição destes dados da telefonia, mesmo nos casos em que os dados são difíceis de entender por um ser humano. Estes modelos são treinados com grandes volumes de dados de telefonia, e têm a melhor precisão de reconhecimento no mercado, mesmo em ambientes ruidosos.

Um cenário comum para falar-a-texto é transcrever grandes volumes de dados de telefonia que podem vir de vários sistemas, como a Interactive Voice Response (IVR). O áudio que estes sistemas fornecem pode ser estéreo ou mono, e cru com pouco ou nenhum pós-processamento feito no sinal. Utilizando o serviço de Discurso e o modelo de fala unificado, uma empresa pode obter transcrições de alta qualidade, quaisquer que sejam os sistemas utilizados para capturar áudio.

Os dados de telefonia podem ser usados para entender melhor as necessidades dos seus clientes, identificar novas oportunidades de marketing ou avaliar o desempenho dos agentes de call center. Após a transcrição dos dados, uma empresa pode usar a produção para fins como telemetria melhorada, identificação de frases-chave ou análise do sentimento do cliente.

As tecnologias descritas nesta página são da Microsoft internamente para vários serviços de processamento de chamadas de suporte, tanto em tempo real como em modo de lote.

Vamos rever algumas das tecnologias e funcionalidades relacionadas que o serviço Speech oferece.

> [!IMPORTANT]
> O modelo unificado do serviço de discurso é treinado com dados diversos e oferece uma solução de um modelo único para uma série de cenários, desde o Ditado à Análise da Telefonia.

## <a name="azure-technology-for-call-centers"></a>Tecnologia Azure para call centers

Para além do aspeto funcional das funcionalidades do serviço Speech, o seu principal objetivo – quando aplicado no call center – é melhorar a experiência do cliente. Neste aspeto, existem três domínios claros:

- Análise pós-chamada, que é essencialmente o processamento de lotes de gravações de chamadas após a chamada.
- Analítica em tempo real, que está a processar o sinal áudio para extrair vários insights à medida que a chamada está a ocorrer (sendo o sentimento um caso de uso proeminente).
- Assistentes de voz (bots), quer impulsionando o diálogo entre o cliente e o bot numa tentativa de resolver o problema do cliente sem participação de agente, ou sendo a aplicação de protocolos de inteligência artificial (IA) para ajudar o agente.

Um diagrama de arquitetura típico da implementação de um cenário de lote é retratado na imagem abaixo ![ da arquitetura de transcrição do Call Center](media/scenarios/call-center-transcription-architecture.png)

## <a name="speech-analytics-technology-components"></a>Componentes da tecnologia de análise da fala

Quer o domínio seja pós-chamada ou em tempo real, o Azure oferece um conjunto de tecnologias maduras e emergentes para melhorar a experiência do cliente.

### <a name="speech-to-text-stt"></a>Discurso ao texto (STT)

[Falar para texto](speech-to-text.md) é a característica mais procurada em qualquer solução de call center. Como muitos dos processos de análise a jusante dependem de texto transcrito, a taxa de erro de palavra _(WER)_ é da maior importância. Um dos principais desafios na transcrição do call center é o ruído que é predominante no call center (por exemplo, outros agentes que falam em segundo plano), a rica variedade de locais e dialetos linguísticos, bem como a baixa qualidade do sinal telefónico real. O WER está altamente correlacionado com a forma como os modelos acústicos e linguísticos são treinados para um determinado local, pelo que a capacidade de personalizar o modelo ao seu local é importante. Os nossos modelos mais recentes da versão Unificada 4.x são a solução tanto para a precisão da transcrição como para a latência. Treinados com dezenas de milhares de horas de dados acústicos e milhares de milhões de informações lexicais, os modelos unificados são os modelos mais precisos do mercado para transcrever dados de call center.

### <a name="sentiment"></a>Sentimento

Avaliar se o cliente teve uma boa experiência é uma das áreas mais importantes da análise da Fala quando aplicado ao espaço do call center. A nossa [API de Transcrição](batch-transcription.md) de Lote oferece análise de sentimento por expressão. Pode agregar o conjunto de valores obtidos como parte de uma transcrição de chamada para determinar o sentimento da chamada tanto para os seus agentes como para o cliente.

### <a name="silence-non-talk"></a>Silêncio (não-falar)

Não é incomum que 35% de um apelo de apoio seja o que chamamos de tempo de não-conversação. Alguns cenários para os quais não se fala são: agentes que procuram o histórico de casos anteriores com um cliente, agentes que usam ferramentas que lhes permitem aceder ao ambiente de trabalho do cliente e executar funções, clientes sentados à espera de uma transferência, e assim por diante. É extremamente importante avaliar quando o silêncio ocorre numa chamada, uma vez que há um número de sensibilidades importantes do cliente que ocorrem em torno deste tipo de cenários e onde ocorrem na chamada.

### <a name="translation"></a>Tradução

Algumas empresas estão a experimentar o fornecimento de transcrições traduzidas de chamadas de suporte de línguas estrangeiras para que os gestores de entregas possam compreender a experiência mundial dos seus clientes. As nossas capacidades de [tradução](./speech-translation.md) são insuperáveis. Podemos traduzir áudio-a-áudio ou áudio-a-texto para um grande número de locais.

### <a name="text-to-speech"></a>Conversão de Texto em Voz

[O texto-a-fala](text-to-speech.md) é outra área importante na implementação de bots que interagem com os clientes. O caminho típico é que o cliente fala, a sua voz é transcrita para texto, o texto é analisado para intenções, uma resposta é sintetizada com base na intenção reconhecida, e então um ativo é surgido para o cliente ou uma resposta de voz sintetizada é gerada. É claro que tudo isto tem de ocorrer rapidamente – portanto, a baixa latência é uma componente importante no sucesso destes sistemas.

A nossa latência de ponta a ponta é consideravelmente baixa para as várias tecnologias envolvidas, tais como [Discurso-a-Texto](speech-to-text.md), [LUIS,](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) [Quadro bot,](https://dev.botframework.com/) [texto-a-discurso.](text-to-speech.md)

As nossas novas vozes também são indistinguíveis das vozes humanas. Podes usar as nossas vozes para dar ao teu bot a sua personalidade única.

### <a name="search"></a>Pesquisar

Outro fundamento da análise é identificar interações onde ocorreu um evento ou experiência específico. Isto é normalmente feito com uma de duas abordagens; ou uma pesquisa ad hoc onde o utilizador simplesmente escreve uma frase e o sistema responde, ou uma consulta mais estruturada onde um analista pode criar um conjunto de declarações lógicas que identificam um cenário numa chamada, e então cada chamada pode ser indexada contra esse conjunto de consultas. Um bom exemplo de pesquisa é a declaração de conformidade ubíqua "esta chamada deve ser registada para fins de qualidade... ". Muitas empresas querem certificar-se de que os seus agentes estão a fornecer esta isenção de responsabilidade aos clientes antes de a chamada ser realmente gravada. A maioria dos sistemas de análise tem a capacidade de tendência para os comportamentos encontrados por algoritmos de consulta/pesquisa, e este relato de tendências é, em última análise, uma das funções mais importantes de um sistema de análise. Através do [diretório de serviços cognitivos,](https://azure.microsoft.com/services/cognitive-services/directory/search/) a sua solução final-final pode ser significativamente melhorada com capacidades de indexação e pesquisa.

### <a name="key-phrase-extraction"></a>Extração de Expressões-Chave

Esta área é uma das aplicações de análise mais desafiantes e que está a beneficiar da aplicação de IA e machine learning. O cenário principal neste caso é inferir a intenção do cliente. Porque é que o cliente está a ligar? Qual é o problema do cliente? Por que o cliente teve uma experiência negativa? O nosso serviço de análise de [texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/) fornece um conjunto de análises fora da caixa para atualizar rapidamente a sua solução de ponta a ponta para extrair essas palavras-chave ou frases importantes.

Vamos agora ver o processamento do lote e os oleodutos em tempo real para reconhecimento da fala com um pouco mais de detalhe.

## <a name="batch-transcription-of-call-center-data"></a>Transcrição de lote de dados do call center

Para transcrever áudio a granel desenvolvemos a [API de Transcrição de Lote.](batch-transcription.md) A API de Transcrição de Lote foi desenvolvida para transcrever grandes quantidades de dados áudio assíncronosamente. No que diz respeito à transcrição de dados de call center, a nossa solução baseia-se nestes pilares:

- **Precisão** - Com modelos unificados de quarta geração, oferecemos qualidade de transcrição inigualável.
- **Latência** - Entendemos que quando se fazem transcrições a granel, as transcrições são necessárias rapidamente. Os trabalhos de transcrição iniciados através da API de [Transcrição](batch-transcription.md) de Lote serão imediatamente feitos, e assim que o trabalho começar a funcionar, é realizado mais rápido que a transcrição em tempo real.
- **Segurança** - Entendemos que as chamadas podem conter dados sensíveis. Tenha certeza de que a segurança é uma das nossas maiores prioridades. O nosso serviço obteve certificações ISO, SOC, HIPAA, PCI.

Os call centers geram grandes volumes de dados áudio diariamente. Se o seu negócio armazenar dados de telefonia num local central, como o Azure Storage, pode utilizar a [API de Transcrição](batch-transcription.md) de Lote para solicitar e receber transcrições assíncronas.

Uma solução típica utiliza estes serviços:

- O serviço de discurso é utilizado para transcrever o discurso-a-texto. É necessária uma subscrição padrão (S0) para o serviço Speech para utilizar a API de Transcrição de Lote. As subscrições gratuitas (F0) não funcionarão.
- [O Azure Storage](https://azure.microsoft.com/services/storage/) é utilizado para armazenar dados de telefonia, e as transcrições devolvidas pela API de Transcrição de Lote. Esta conta de armazenamento deve utilizar notificações, especificamente para quando novos ficheiros são adicionados. Estas notificações são usadas para desencadear o processo de transcrição.
- [As Funções Azure](../../azure-functions/index.yml) são utilizadas para criar as assinaturas de acesso partilhado (SAS) URI para cada gravação e acionar o pedido HTTP POST para iniciar uma transcrição. Além disso, as Funções Azure são usadas para criar pedidos para recuperar e apagar transcrições usando a API de Transcrição de Lote.

Internamente, estamos a utilizar as tecnologias acima para suportar chamadas de clientes da Microsoft no modo Batch.
:::image type="content" source="media/scenarios/call-center-batch-pipeline.png" alt-text="Tecnologias utilizadas para suportar chamadas de clientes da Microsoft no modo Batch.":::

## <a name="real-time-transcription-for-call-center-data"></a>Transcrição em tempo real para dados de call center

Algumas empresas são obrigadas a transcrever conversas em tempo real. A transcrição em tempo real pode ser usada para identificar palavras-chave e desencadear pesquisas de conteúdos e recursos relevantes para a conversa, para monitorizar o sentimento, melhorar a acessibilidade, ou para fornecer traduções para clientes e agentes que não são falantes nativos.

Para cenários que exijam transcrição em tempo real, recomendamos a utilização do [SDK do Discurso](speech-sdk.md). Atualmente, o discurso-a-texto está disponível em [mais de 20 idiomas](language-support.md), e o SDK está disponível em C++, C#, Java, Python, Node.js, Objective-C e JavaScript. As amostras estão disponíveis em cada idioma no [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk) Para obter as últimas notícias e atualizações, consulte [as notas de lançamento](releasenotes.md).

Internamente, estamos a usar as tecnologias acima para analisar em tempo real chamadas de clientes da Microsoft à medida que acontecem, como ilustrado no diagrama seguinte.

![Arquitetura de Lote](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>Uma palavra sobre IVRs

O serviço Speech pode ser facilmente integrado em qualquer solução utilizando o [SDK](speech-sdk.md) de discurso ou a [API REST](./overview.md#reference-docs). No entanto, a transcrição do call center pode requerer tecnologias adicionais. Normalmente, é necessária uma ligação entre um sistema IVR e Azure. Embora não ofereçamos tais componentes, aqui está uma descrição do que uma ligação a um IVR implica.

Vários produtos de serviço IVR ou de telefonia (como genesys ou AudioCodes) oferecem capacidades de integração que podem ser alavancadas para permitir a entrada e saída de áudio para um serviço Azure. Basicamente, um serviço Azure personalizado pode fornecer uma interface específica para definir sessões de chamadas telefónicas (como Call Start ou Call End) e expor uma API webSocket para receber áudio de entrada que é usado com o serviço de Fala. As respostas de saída, como a transcrição de conversas ou ligações com o Quadro Bot, podem ser sintetizadas com o serviço de texto-a-voz da Microsoft e devolvidas ao IVR para reprodução.

Outro cenário é a integração direta com o Protocolo de Iniciação da Sessão (SIP). Um serviço Azure conecta-se a um SIP Server, obtendo assim um fluxo de entrada e um fluxo de saída, que é usado para as fases de fala-a-texto e texto-a-voz. Para se ligar a um SIP Server existem ofertas de software comercial, como o Ozeki SDK, ou [as Equipas que ligam e reuniões API](/graph/api/resources/communications-api-overview) (atualmente em versão beta), que são projetadas para suportar este tipo de cenário para chamadas áudio.

## <a name="customize-existing-experiences"></a>Personalizar experiências existentes

 O serviço Speech funciona bem com modelos embutidos. No entanto, é melhor personalizar e afinar ainda mais a experiência para o seu produto ou ambiente. As opções de personalização vão desde a sintonização de modelos acústicos a fontes de voz únicas para a sua marca. Depois de ter construído um modelo personalizado, pode usá-lo com qualquer uma das funcionalidades do serviço Speech em tempo real ou em modo de lote.

| Serviço de voz | Modelação | Description |
| -------------- | ----- | ----------- |
| Conversão de voz em texto | [Modelo acústico](./how-to-custom-speech-train-model.md) | Crie um modelo acústico personalizado para aplicações, ferramentas ou dispositivos que sejam utilizados em ambientes particulares, como num carro ou num piso de fábrica, cada um com condições de gravação específicas. Exemplos incluem discurso acentuado, ruídos de fundo específicos, ou usar um microfone específico para a gravação. |
|                | [Modelo de linguagem](./how-to-custom-speech-train-model.md) | Crie um modelo de linguagem personalizado para melhorar a transcrição do vocabulário e gramática específicos da indústria, como terminologia médica, ou jargão de TI. |
|                | [Modelo de pronúncia](./how-to-custom-speech-train-model.md) | Com um modelo de pronúncia personalizado, pode definir a forma fonética e exibir por uma palavra ou termo. É útil para lidar com termos personalizados, como nomes de produtos ou siglas. Tudo o que precisa para começar é um ficheiro de pronúncia, que é um `.txt` ficheiro simples. |
| Conversão de texto em voz | [Tipo de voz](./how-to-custom-voice-create-voice.md) | As fontes de voz personalizadas permitem-lhe criar uma voz única e reconhecível para a sua marca. Só é preciso uma pequena quantidade de dados para começar. Quanto mais dados fornecer, mais natural e humano soará a sua fonte de voz. |

## <a name="sample-code"></a>Código de exemplo

O código de amostra está disponível no GitHub para cada uma das funcionalidades do serviço Speech. Estas amostras cobrem cenários comuns como ler áudio a partir de um ficheiro ou stream, reconhecimento contínuo e de tiro único, e trabalhar com modelos personalizados. Utilize estes links para visualizar amostras de SDK e REST:

- [Amostras de tradução de discursos e de fala (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Amostras de transcrição de lote (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Amostras de texto para falar (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Documentos de referência

- [API de Voz](./speech-sdk.md)
- [SDK de Dispositivos de Voz](speech-devices-sdk.md)
- [REST API: Discurso-a-texto](rest-speech-to-text.md)
- [REST API: Texto-a-discurso](rest-text-to-speech.md)
- [REST API: Transcrição e personalização do lote](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Obtenha gratuitamente uma chave de subscrição de serviço de discurso](overview.md#try-the-speech-service-for-free)