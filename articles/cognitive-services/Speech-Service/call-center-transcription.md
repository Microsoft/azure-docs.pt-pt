---
title: Chamar transcrição Center - serviços de voz
titleSuffix: Azure Cognitive Services
description: Um cenário comum para conversão de voz em texto é Fotografar grandes volumes de dados de telefonia que podem ter vários sistemas, como a resposta de voz interativa (IVR). O áudio pode ser estéreo ou mono, e não processados, com pouca ou nenhuma pós processamento efetuada no sinal. Utilizar serviços de voz e o modelo de voz unificado, uma empresa pode obter transcrições de alta qualidade, com muitos sistemas de captura de áudio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 7d844f4d2ad77f5b7cc53275a24167e5f2e71b78
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027034"
---
# <a name="speech-services-for-telephony-data"></a>Serviços de voz para dados de telefonia

Dados de telefonia que são gerados por meio de landlines, celulares e rádios são, normalmente, de baixa qualidade e banda estreita no intervalo de 8 KHz, que cria desafios ao converter voz em texto. Do excel em Fotografar estes dados de telefonia, mesmo em casos em que os dados são difícil para um humano compreender os modelos de reconhecimento de fala mais recentes dos serviços de voz do Azure. Esses modelos são treinados com grandes volumes de dados de telefonia e tem melhor na precisão de reconhecimento de mercado, mesmo em ambientes ruidosos.

Um cenário comum para conversão de voz em texto é Fotografar grandes volumes de dados de telefonia que podem ter vários sistemas, como a resposta de voz interativa (IVR). O áudio que fornecem estes sistemas pode ser estéreo ou mono e não processados com o processamento de mensagem de pouca ou nenhuma feito no sinal. Utilizar serviços de voz e o modelo de voz unificado, uma empresa pode obter alta qualidade transcrições, seja qual for os sistemas usados para capturar áudio.

Dados de telefonia podem ser utilizados para melhor compreender as necessidades dos seus clientes, identificar novas oportunidades de marketing ou avaliar o desempenho de agentes do Centro de chamada. Depois dos dados é transcrito, uma empresa pode utilizar a saída para a telemetria melhorada, identificação de expressões-chave ou análise de sentimento do cliente.

As tecnologias descritas nesta página são pela Microsoft internamente para vários serviços de processamento de chamada, ambos em tempo real de suporte e o modo de lote.

Vamos rever alguns dos recursos relacionados oferta de serviços de voz do Azure e tecnologia.

> [!IMPORTANT]
> Modelo de unificação de serviços de voz é preparado com dados diversificado e oferece uma solução de modelo único para um número de cenário de ditado para análise de telefonia.

## <a name="azure-technology-for-call-centers"></a>Tecnologia Azure centrais de chamadas

Posteriores, o aspecto funcional dos serviços de fala o seu principal objetivo - quando aplicado a central de atendimento - é melhorar a experiência do cliente. Três domínios claros existem nesse aspecto 

* Análise de pós-chamada de ou seja, processamento de chamada gravações em lotes 
* Processamento de análises em tempo real do sinal de áudio para extrair várias informações como a chamada está sendo executada (com o sentimento que está a ser um caso de utilização proeminente) e
* Assistentes virtual (Bots), a orientar a caixa de diálogo entre o cliente e o bot numa tentativa de resolver o problema do cliente com a participação de nenhum agente ou que está a ser a aplicação de IA protocolos para auxiliar o agente.

Um diagrama de arquitetura típica da implementação de um cenário de batch está descrito na figura abaixo ![arquitetura de transcrição do Centro de chamada](media/scenarios/call-center-transcription-architecture.png)

## <a name="speech-analytics-technology-components"></a>Componentes de tecnologia de análise de voz

Se o domínio é pós- chamada de ou em tempo real, o Azure oferece um conjunto de maduro e emergente do conjunto de tecnologias para melhorar a experiência do cliente. 

### <a name="speech-to-text-stt"></a>Conversão de voz em texto (STT) 

[Conversão de voz em texto](speech-to-text.md) é mais buscado após a funcionalidade em qualquer solução de call center. Uma vez que muitos dos processos de análise de downstream dependem de texto transcrito, a taxa de erros do word (WER) é de extrema importância. Um dos principais desafios no transcrição do Centro de chamada é o ruído que é predominante na central de atendimento (por exemplo – outros agentes de fala em segundo plano), a várias localidades de idioma e dialetos, bem como a baixa qualidade do sinal de telefone real. WER é altamente correlacionada com o quão bem os modelos de acústica e idioma estão treinados para uma determinada localidade, assim, a capacidade para personalizar o modelo para sua localidade é importante. Nossos modelos de 4.x de versão mais recente do Unified são a solução para precisão de transcrição e latência. Treinados com dezenas de milhares de horas de dados acústicos e de milhares de milhões de informações léxicos unificado modelos são os modelos mais precisos no mercado de dados do Centro de chamada de transcrever.

### <a name="sentiment"></a>Sentimento
Medição do se o cliente tinha uma boa experiência é uma das áreas mais importantes de análise de voz quando aplicado ao espaço do Centro de chamada. Nosso [API do Batch transcrição](batch-transcription.md) oferece análise de sentimentos por expressão. Pode agregar o conjunto de valores de obteve como parte de uma transcrição de chamada para determinar o sentimento da chamada para os agentes e o cliente.

### <a name="silence-non-talk"></a>Silence (não-talk)
Não é incomum para 35 por cento de uma chamada de suporte para ser o que chamamos de tempo não conversação. Alguns cenários que não-talk ocorre são: Procurar anterior de caso de histórico, com um cliente de agentes, agentes usando ferramentas que permitem-lhes aceder à área de trabalho do cliente e realizar funções, os clientes em manter aguardar que uma transferência e assim por diante. É extremamente importante podem medir quando silêncio está a ocorrer numa chamada quantos forem os números de sensitivities de clientes importantes que ocorrem em torno desses tipos de cenários e o local em que ocorrem na chamada.

### <a name="translation"></a>Tradução
Algumas empresas experimentando fornecendo traduzidas transcrições de chamadas de suporte de idiomas estrangeiros para que os gerentes de entrega podem compreender a experiência de todo o mundo dos seus clientes. Nosso [tradução](translation.md) os recursos são unsurpassed. Podemos pode traduzir áudio para áudio ou áudio em texto de um grande número de localidades.

### <a name="text-to-speech"></a>Conversão de Texto em Voz
[Voz](text-to-speech.md) é outra área importante na implementação de bots que interagem com os clientes. O caminho típico é que o cliente participa como palestrante, pela voz é transcrito para texto, o texto é analisado relativamente a objetivos, uma resposta é sintetizada com base na intenção reconhecida e, em seguida, um recurso ou aparece para o cliente ou uma resposta de voz sintetizada ao falar é gerado. Claro que tudo isso deve ocorrer rapidamente – assim a latência é um componente importante no êxito desses sistemas. 

Nosso latência ponto a ponto é muito baixa, considerar as várias tecnologias envolvidas, tal como [voz em texto](speech-to-text.md), [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), [Bot Framework](https://dev.botframework.com/), [ Voz](text-to-speech.md). 

Nosso novo vozes também são distinguir de voz humana. Pode usar vozes para dar o seu bot sua personalidade exclusiva.

### <a name="search"></a>Pesquisa
São objetos de outra de análise consiste em identificar as interações em que um evento específico ou experiência ocorreu. Isso normalmente é feito com uma das duas abordagens, de qualquer uma pesquisa ad hoc em que o utilizador simplesmente escreve uma frase e o sistema responde ou uma consulta mais estruturada, em que um analista pode criar um conjunto de declarações de lógicas que identificar um cenário numa chamada , e, em seguida, cada chamada pode ser indexada em relação a essas conjunto de consultas. Um exemplo de pesquisa boa é a declaração de conformidade onipresente "essa chamada deve ser registrada para fins de qualidade... "– como muitas empresas pretendem Certifique-se de que seus agentes estão a disponibilizar esta exclusão de responsabilidade aos clientes antes da chamada, na verdade, é registada. A maioria dos sistemas de análise têm a capacidade de tendência os comportamentos encontrados pelos algoritmos de /search consulta – como esses relatórios de tendências, por fim, é uma das funções mais importantes de um sistema de análise. Por meio [diretório dos serviços cognitivos](https://azure.microsoft.com/services/cognitive-services/directory/search/) sua solução ponto a ponto pode ser significativamente melhorada com capacidades de indexação e pesquisa.

### <a name="key-phrase-extraction"></a>Extração de Expressões-Chave
Esta área é uma das aplicações de análise de mais desafiadoras e outro que vem se beneficiando a aplicação de IA e ML. O cenário principal aqui é inferir o objetivo do cliente. Por que motivo está a chamar o cliente? O que é o problema do cliente? Por que o cliente tinha experiência negativa? Nosso [serviço de análise de texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/) fornece um conjunto de análise prontos para atualizar rapidamente a sua solução ponto a ponto para extrair essas palavras-chave importantes ou frases.

Vamos agora estão de cara do processamento em lotes e os pipelines em tempo real para o reconhecimento de fala um pouco mais detalhadamente.

## <a name="batch-transcription-of-call-center-data"></a>Transcrição de lote de dados do Centro de chamada

Para fotografar em massa de áudio desenvolvemos o [API do Batch transcrição](batch-transcription.md). A API de transcrição do Batch foi desenvolvida de transcrever grandes quantidades de dados de áudio de forma assíncrona. Com respeito Fotografar os dados do Centro de chamada, a nossa solução se baseia nestes pilares:

* **Precisão**: Com modelos de unificação de quarta geração, podemos oferecer qualidade de transcrição unsurpassed.
* **Latência**: Compreendemos que durante a realização de transcrições em massa, as transcrições são necessárias rapidamente. As tarefas de transcrição iniciadas no [API do Batch transcrição](batch-transcription.md) ficarão em fila imediatamente, e assim que a tarefa é executada é executado mais rapidamente do que a transcrição em tempo real.
* **Segurança**: Compreendemos que chamadas podem conter dados confidenciais. Tenha a certeza de que a segurança é um dos nossos prioridades. Nosso serviço tenha obtida ISO, SOC, HIPAA, certificações de PCI.

Centros de chamada de gerar grandes volumes de dados de áudio numa base diária. Se a sua empresa armazena dados de telefonia numa localização central, como o armazenamento do Azure, pode utilizar o [Batch transcrição API]((batch-transcription.md) para pedir e receber transcrições de forma assíncrona.

Uma solução típica utiliza estes serviços:

* Serviços de voz do Azure são utilizados para transcrição de voz em texto. Uma standard subscription (assim) para os serviços de voz é necessário para utilizar a API de transcrição do Batch. As subscrições gratuitas (F0) não funcionará.
* [O armazenamento do Azure](https://azure.microsoft.com/services/storage/) é utilizada para armazenar dados de telefonia e transcrições devolvidas pela API de transcrição do Batch. Esta conta de armazenamento deve utilizar notificações, especificamente para quando são adicionados novos ficheiros. Estas notificações são utilizadas para acionar o processo de transcrição.
* [As funções do Azure](https://docs.microsoft.com/azure/azure-functions/) é utilizado para criar as assinaturas de acesso partilhado (SAS) URI para cada gravação e acionar o pedido de HTTP POST para iniciar uma transcrição. Além disso, as funções do Azure é utilizada para criar pedidos para obter e eliminar transcrições com a API de transcrição do Batch.
* [WebHooks](webhooks.md) são utilizados para receber notificações quando transcrições são concluídas.

Internamente estamos a utilizar as tecnologias acima para oferecer suporte a Microsoft chama de cliente no modo de lote.
![Arquitetura de batch](media/scenarios/call-center-batch-pipeline.png)

## <a name="real-time-transcription-for-call-center-data"></a>Transcrição em tempo real para dados do Centro de chamada

Algumas empresas devem transcreva conversas em tempo real. Transcrição em tempo real pode ser utilizada para identificar palavras-chave e acionar a procura de conteúdo e recursos relevantes para a conversa, para monitorização sentimentos, para melhorar a acessibilidade, ou para fornecer as traduções para os clientes e os agentes que não são nativos oradores.

Para cenários que exigem a transcrição em tempo real, recomendamos que utilize o [SDK de voz](speech-sdk.md). Atualmente, a conversão de voz para texto está disponível no [mais de 20 linguagens](language-support.md), e o SDK está disponível no C++, C#, Java, Python, node. js e Javascript. Os exemplos estão disponíveis em cada linguagem no [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk). Para as mais recentes notícias e atualizações, consulte [notas de versão](releasenotes.md).

Internamente estamos a utilizar as tecnologias acima para analisar em chamadas de cliente do Microsoft em tempo real à medida que acontecem.

![Arquitetura de batch](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>Uma palavra em IVRs

Serviços de voz podem ser facilmente integrados em qualquer solução utilizando o [SDK de voz](speech-sdk.md) ou o [REST API](rest-apis.md). No entanto, a transcrição do Centro de chamada pode exigir tecnologias adicionais. Normalmente, uma ligação entre um sistema IVR e o Azure é necessária. Embora não oferecemos tais componentes, gostaríamos de descrever o que envolve uma ligação para uma IVR.

Vários produtos do serviço IVR ou telefonia (como Genesys ou AudioCodes) oferecem capacidades de integração que podem ser utilizadas para ativar a entrada e saída passthrough áudio para um serviço do Azure. Basicamente, um serviço do Azure personalizado pode fornecer uma interface específica de definir a sessões de chamada telefónica (por exemplo, chamar início ou fim de chamar) e expor uma API de WebSocket para receber o áudio de fluxo de entrada que é utilizado com os serviços de voz. As respostas de saída, como transcrição da conversação ou ligações com o Bot Framework, podem ser sintetizadas com o serviço de texto para voz da Microsoft e retornadas ao IVR para reprodução.

Outro cenário é a integração de SIP direto. Um serviço do Azure liga a um servidor SIP, introdução, portanto, um fluxo de entrada e um fluxo de saída, que é utilizado para as fases de voz em texto e texto para voz. Para ligar ao aí exista um servidor SIP são ofertas de software comercial, como o SDK de Ozieki, ou [chamar a Equipes e a API de reuniões](https://docs.microsoft.com/graph/api/resources/calls-api-overview?view=graph-rest-beta) (atualmente em beta), que foram concebidos para suportar este tipo de cenário para chamadas de áudio.

## <a name="customize-existing-experiences"></a>Personalizar experiências existentes

Serviços de voz do Azure funciona bem com modelos incorporados, no entanto, pode querer personalizar e otimizar a experiência para o seu produto ou o ambiente ainda mais. Intervalo de opções de personalização do modelo acústico ajuste para tipos de voz exclusivo para a sua marca. Depois de criar um modelo personalizado, pode usá-lo com qualquer um dos serviços de voz do Azure em tempo real ou no modo de lote.

| Serviço de voz | Modelo | Descrição |
|----------------|-------|-------------|
| Conversão de voz em texto | [Modelo acústico](how-to-customize-acoustic-models.md) | Criar um modelo acústico personalizado para aplicações, ferramentas, ou dispositivos que são utilizados em particular ambientes, como num carro ou num chão de fábrica, cada um com condições de gravação específico. Os exemplos incluem voz acentuado, ruídos de fundo específico ou usando um microfone específico para gravação. |
| | [Modelo de linguagem](how-to-customize-language-model.md) | Crie um modelo de idioma personalizado para melhorar a transcrição de vocabulário específicos da indústria e de gramática, como a terminologia médica ou gíria, IT. |
| | [Modelo de pronúncia](how-to-customize-pronunciation.md) | Com um modelo de pronúncia personalizado, pode definir o formulário fonético e a exibição de uma palavra ou o termo. É útil para lidar com os termos personalizados, tais como nomes de produto ou acrônimos. Tudo o que precisa para começar a utilizar é um ficheiro de pronúncia – um arquivo. txt simples. |
| Conversão de texto em voz | [Tipo de voz](how-to-customize-voice-font.md) | Tipos de voz personalizadas permitem-lhe criar uma voz reconhecível, um do-única para sua marca. Leva apenas uma pequena quantidade de dados para começar a utilizar. Quanto mais dados que fornecer, o mais natural e semelhante à humana soará seu tipo de voz. |

## <a name="sample-code"></a>Código de exemplo

Código de exemplo está disponível no GitHub para cada um dos serviços de voz do Azure. Esses exemplos abrangem os cenários comuns, como ler o áudio de um arquivo ou fluxo, reconhecimento contínuo e única e trabalhar com modelos personalizados. Utilize estas ligações para ver exemplos do SDK e REST:

* [Exemplos de tradução de voz em texto e voz (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Exemplos de transcrição do batch (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Exemplos de texto para voz (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Documentos de referência

* [SDK de Voz](speech-sdk-reference.md)
* [Dispositivos de voz SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Text-to-speech](rest-text-to-speech.md)
* [REST API: Transcrição de batch e personalização](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Obtenha gratuitamente uma chave de subscrição de serviços de voz](get-started.md)
