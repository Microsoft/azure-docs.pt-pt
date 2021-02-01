---
title: Recipientes de serviço de fala frequentemente perguntas (FAQ)
titleSuffix: Azure Cognitive Services
description: Instale e execute recipientes de fala. o discurso-a-texto transcreve os fluxos de áudio para texto em tempo real que as suas aplicações, ferramentas ou dispositivos podem consumir ou exibir. O texto-a-discurso converte o texto de entrada em discurso sintetizado humano.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/12/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 59429358469e4c236755f37b9c992439472d9aa7
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226326"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>Recipientes de serviço de fala frequentemente perguntas (FAQ)

Ao utilizar o serviço Speech com recipientes, confie nesta coleção de perguntas frequentes antes de se intensificar para apoiar. Este artigo capta questões de grau variável, de geral a técnico. Para expandir uma resposta, clique na pergunta.

## <a name="general-questions"></a>Perguntas gerais

<details>
<summary>
<b>Como funcionam os contentores da fala e como os instalo?</b>
</summary>

**Resposta:** Ao criar o cluster de produção, há várias coisas a considerar. Em primeiro lugar, a criação de uma única língua, vários recipientes, na mesma máquina, não deve ser um problema importante. Se estiver a ter problemas, pode ser uma questão relacionada com hardware - por isso, primeiro, olharíamos para os recursos, ou seja; CPU e especificações de memória.

Considere por um momento, o `ja-JP` recipiente e o modelo mais recente. O modelo acústico é a peça mais exigente em termos de CPU, enquanto o modelo linguístico exige mais memória. Quando comparamos a utilização, são necessários cerca de 0,6 núcleos de CPU para processar um único pedido de voz a texto quando o áudio está a fluir em tempo real (como no microfone). Se estiver a alimentar áudio mais rapidamente do que em tempo real (como num ficheiro), essa utilização pode duplicar (núcleos de 1,2x). Entretanto, a memória listada abaixo é a memória operacional para descodição do discurso. *Não* tem em conta o tamanho real do modelo linguístico, que reside em cache de ficheiros. Por `ja-JP` isso são mais 2 GB; `en-US` para, pode ser mais (6-7 GB).

Se tiver uma máquina onde a memória é escassa e estiver a tentar implantar várias idiomas nela, é possível que a cache de ficheiro esteja cheia e o SISTEMA seja forçado a enviar modelos de página dentro e fora. Para uma transcrição em execução, isso pode ser desastroso, e pode levar a abrandamentos e outras implicações de desempenho.

Além disso, pré-embalamos executáveis para máquinas com o conjunto de instruções [de extensão de vetor avançado (AVX2).](speech-container-howto.md#advanced-vector-extension-support) Uma máquina com o conjunto de instruções AVX512 exigirá a geração de códigos para esse objetivo, e o início de 10 recipientes para 10 idiomas pode esgotar temporariamente o CPU. Uma mensagem como esta aparecerá nos registos do estivador:

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

Pode definir o número de descodificações que pretende dentro de um *único* recipiente utilizando `DECODER MAX_COUNT` variável. Então, basicamente, devemos começar com o seu SKU (CPU/memória), e podemos sugerir como tirar o melhor dele. Um ótimo ponto de partida refere-se às especificações recomendadas de recursos da máquina hospedeira.

<br>
</details>

<details>
<summary>
<b>Poderia ajudar no planeamento de capacidades e na estimativa de custos dos recipientes on-prem Speech-to-text?</b>
</summary>

**Resposta:** Para a capacidade do contentor no modo de processamento de lote, cada descodificador poderia manusear 2-3x em tempo real, com dois núcleos de CPU, para um único reconhecimento. Não recomendamos a manutenção de mais de dois reconhecimentos simultâneos por instância de contentores, mas recomendamos a execução de mais casos de contentores por razões de fiabilidade/disponibilidade, atrás de um equilibrador de carga.

Embora pudéssemos ter cada caso de contentor a funcionar com mais descodificações. Por exemplo, podemos ser capazes de configurar 7 descodificantes por instância de contentor numa máquina de oito núcleos (a mais de 2x cada), produzindo 15x de produção. Há um param `DECODER_MAX_COUNT` para se ter em conta. Para o caso extremo, surgem problemas de fiabilidade e latência, com a produção a aumentar significativamente. Para um microfone, será a 1x em tempo real. O uso geral deve ser de cerca de um núcleo para um único reconhecimento.

Para o cenário de processamento de 1 Hora/dia no modo de processamento do lote, num caso extremo, 3 VMs poderia manuseá-lo dentro de 24 horas, mas não garantido. Para lidar com os dias de pico, failover, atualização e fornecer o mínimo de backup/BCP, recomendamos 4-5 máquinas em vez de 3 por cluster, e com mais de 2 agrupamentos.

Para hardware, utilizamos o Azure VM padrão `DS13_v2` como referência (cada núcleo deve ser de 2,6 GHz ou melhor, com o conjunto de instruções AVX2 ativado).

| Instância  | vCPU(s) | RAM    | Armazenamento temporário | Pay-as-you-go com AHB | Reserva de 1 ano com AHB (% Poupança) | 3 anos reservado com AHB (% Poupança) |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 GiB | 112 GiB      | $0.598/hora            | $0,3528/hora (~41%)                 | $0,2333/hora (~61%)                  |

Com base na referência de design (dois clusters de 5 VMs para lidar com o processamento de lotes de áudio de 1 K/dia), o custo de hardware de 1 ano será:

> 2 (agrupamentos) * 5 (VMs por cluster) * $0.3528/hora * 365 (dias) * 24 (horas) = $31K/ano

Ao mapear para a máquina física, uma estimativa geral é de 1 vCPU = 1 Núcleo físico do CPU. Na realidade, 1vCPU é mais poderoso que um único núcleo.

Para o on-prem, todos estes fatores adicionais entram em jogo:

- Sobre que tipo de CPU físico é e quantos núcleos nele
- Quantos CPUs a funcionar juntos na mesma caixa/máquina
- Como os VMs são configurado
- Como é utilizado o hiper-rosca / multi-rosca
- Como a memória é partilhada
- O SO, etc.

Normalmente não é tão bem sintonizado como Azure o ambiente. Considerando outras despesas gerais, diria que uma estimativa segura é de 10 núcleos de CPU físicos = 8 Azure vCPU. Embora os CPUs populares tenham apenas oito núcleos. Com a colocação no pré-pré, o custo será maior do que o uso de VMs Azure. Além disso, considere a taxa de depreciação.

O custo do serviço é o mesmo que o serviço online: $1/hora para falar-a-texto. O custo do serviço de fala é:

> $1 * 1000 * 365 = $365K

O custo de manutenção pago à Microsoft depende do nível de serviço e do conteúdo do serviço. Várias, desde $29,99/mês para nível básico até centenas de milhares se o serviço no local estiver envolvido. Um número bruto é $300/hora para serviço/manutenção. O custo das pessoas não está incluído. Outros custos de infraestrutura (como armazenamento, redes e equilibradores de carga) não estão incluídos.

<br>
</details>

<details>
<summary>
<b>Por que falta pontuação da transcrição?</b>
</summary>

**Resposta:** O `speech_recognition_language=<YOUR_LANGUAGE>` deve ser explicitamente configurado no pedido se estiverem a usar o cliente Carbon.

Por exemplo:

```python
if not recognize_once(
    speechsdk.SpeechRecognizer(
        speech_config=speechsdk.SpeechConfig(
            endpoint=template.format("interactive"),
            speech_recognition_language="ja-JP"),
            audio_config=audio_config)):

    print("Failed interactive endpoint")
    exit(1)
```
A saída é:

```cmd
RECOGNIZED: SpeechRecognitionResult(
    result_id=2111117c8700404a84f521b7b805c4e7, 
    text="まだ早いまだ早いは猫である名前はまだないどこで生まれたかとんと見当を検討をなつかぬ。
    何でも薄暗いじめじめした所でながら泣いていた事だけは記憶している。
    まだは今ここで初めて人間と言うものを見た。
    しかも後で聞くと、それは書生という人間中で一番同額同額。",
    reason=ResultReason.RecognizedSpeech)
```

<br>
</details>

<details>
<summary>
<b>Posso usar um modelo acústico personalizado e um modelo de linguagem com recipiente de fala?</b>
</summary>

Atualmente só somos capazes de passar um ID de modelo, seja um modelo de linguagem personalizada ou um modelo acústico personalizado.

**Resposta:** A decisão de *não* apoiar simultaneamente os modelos acústicos e linguísticos foi tomada. Isto permanecerá em vigor, até que um identificador unificado seja criado para reduzir as quebras de API. Portanto, infelizmente, isto não é apoiado neste momento.

<br>
</details>

<details>
<summary>
<b>Pode explicar estes erros do recipiente personalizado de fala para texto?</b>
</summary>

**Erro 1:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**Resposta 1:** Se está a treinar com o último modelo personalizado, atualmente não apoiamos isso. Se treinar com uma versão mais antiga, deve ser possível usá-lo. Ainda estamos a trabalhar no apoio às versões mais recentes.

Essencialmente, os recipientes personalizados não suportam modelos acústicos baseados em Halide ou ONNX (que é o padrão no portal de treino personalizado). Isto deve-se a modelos personalizados que não estão a ser encriptados e não queremos expor os modelos ONNX, no entanto; os modelos linguísticos são bons. O cliente terá de selecionar explicitamente um modelo não ONNX mais antigo para treino personalizado. A precisão não será afetada. O tamanho do modelo pode ser maior (em 100 MB).

> Modelo de suporte > 20190220 (v4.5 Unificado)

**Erro 2:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**Resposta 2:** Tem de fornecer o nome de voz correto no pedido, que é sensível a casos. Consulte o mapeamento de nome de serviço completo.

**Erro 3:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**Resposta 3:** Você quer criar um recurso de fala, não um recurso de Serviços Cognitivos.


<br>
</details>

<details>
<summary>
<b>Quais os protocolos da API suportados, REST ou WS?</b>
</summary>

**Resposta:** Para recipientes de voz para texto e de voz para texto personalizados, atualmente apenas apoiamos o protocolo baseado em websocket. O SDK só suporta chamar em WS, mas não REST. Há um plano para adicionar suporte REST, mas não ETA por enquanto. Consulte sempre a documentação oficial, consulte [os pontos finais de previsão de consulta](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>

<details>
<summary>
<b>O CentOS é apoiado para recipientes de fala?</b>
</summary>

**Resposta:** O CentOS 7 ainda não é suportado pela Python SDK, também o Ubuntu 19.04 não é suportado.

O pacote Python Speech SDK está disponível para estes sistemas operativos:
- **Janelas** - x64 e x86
- **Mac** - macOS X versão 10.12 ou mais tarde
- **Linux** - Ubuntu 16.04, Ubuntu 18.04, Debian 9 em x64

Para obter mais informações sobre a configuração do ambiente, consulte [a configuração da plataforma Python](quickstarts/setup-platform.md?pivots=programming-language-python). Por enquanto, Ubuntu 18.04 é a versão recomendada.

<br>
</details>

<details>
<summary>
<b>Porque é que estou a ter erros ao tentar ligar para os pontos finais da previsão do LUIS?</b>
</summary>

Estou a usar o contentor LUIS numa implantação IoT Edge e estou a tentar chamar o ponto final de previsão do LUIS de outro contentor. O contentor LUIS está a ouvir na porta 5001, e o URL que estou a usar é o seguinte:

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

O erro que estou a ter é:

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

Vejo o pedido nos registos do contentor LUIS e a mensagem diz:

```cmd
The request path /luis//predict" does not match a supported file type.
```

O que é que isto quer dizer? O que me está a escapar? Estava a seguir o exemplo para o Discurso SDK, [daqui.](https://github.com/Azure-Samples/cognitive-services-speech-sdk) O cenário é que estamos a detetar o áudio diretamente do microfone do PC e a tentar determinar a intenção, com base na app LUIS que treinamos. O exemplo a que me relacionei faz exatamente isso. E funciona bem com o serviço baseado na nuvem LUIS. A utilização do Discurso SDK parecia evitar-nos de ter de fazer uma chamada explícita separada para a API de discurso para texto e, em seguida, uma segunda chamada para LUIS.

Então, tudo o que estou a tentar fazer é mudar do cenário de usar o LUIS na nuvem para usar o recipiente LUIS. Não consigo imaginar se o Discurso SDK funcionar para um, não funcionará para o outro.

**Resposta:** O SDK de discurso não deve ser utilizado contra um recipiente LUIS. Para a utilização do recipiente LUIS, deve ser utilizado o API LUIS SDK ou LUIS REST. O SDK de fala deve ser usado contra um recipiente de fala.

Uma nuvem é diferente de um recipiente. Uma nuvem pode ser composta por vários recipientes agregados (às vezes chamados micro serviços). Portanto, há um recipiente LUIS e depois há um recipiente de fala - dois recipientes separados. O recipiente do Discurso só fala. O recipiente LUIS só faz LUIS. Na nuvem, porque ambos os contentores são conhecidos por serem implantados, e é um mau desempenho para um cliente remoto ir para a nuvem, fazer discurso, voltar, depois ir para a nuvem novamente e fazer LUIS, nós fornecemos uma funcionalidade que permite ao cliente ir ao Speech, ficar na nuvem, ir para o LUIS e depois voltar para o cliente. Assim, mesmo neste cenário, o Speech SDK vai para o recipiente de nuvem de discurso com áudio, e depois o contentor de nuvem de fala fala para o recipiente de nuvem LUIS com texto. O recipiente LUIS não tem noção de aceitar áudio (não faria sentido que o recipiente LUIS aceitasse o streaming de áudio - o LUIS é um serviço baseado em texto). Com o on-prem, não temos a certeza de que o nosso cliente tenha implantado ambos os contentores, não presumimos orquestrar entre contentores nas instalações dos nossos clientes, e se ambos os contentores são implantados no pré-pré, dado que são mais locais para o cliente, não é um fardo ir primeiro ao SR, de volta ao cliente, e ter o cliente em seguida, pegar esse texto e ir para LUIS.

<br>
</details>

<details>
<summary>
<b>Porque estamos a ter erros com o macOS, o contentor da fala e o Python SDK?</b>
</summary>

Quando enviamos um ficheiro *.wav* a ser transcrito, o resultado volta com:

```cmd
recognition is running....
Speech Recognition canceled: CancellationReason.Error
Error details: Timeout: no recognition result received.
When creating a websocket connection from the browser a test, we get:
wb = new WebSocket("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
WebSocket
{
    url: "ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1",
    readyState: 0,
    bufferedAmount: 0,
    onopen: null,
    onerror: null,
    ...
}
```

Sabemos que o websocket está bem montado.

**Resposta:** Se for esse o caso, então veja [esta questão do GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310) Temos uma sedução, [proposta aqui.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722)

O carbono fixou isto na versão 1.8.


<br>
</details>

<details>
<summary>
<b>Quais são as diferenças nos pontos finais do recipiente da fala?</b>
</summary>

Pode ajudar a preencher as seguintes métricas de teste, incluindo que funções testar, e como testar as APIs SDK e REST? Especialmente, diferenças em "interativo" e "conversa", que não vi do doc/amostra existente.

| Ponto final                                                | Teste funcional                                                   | SDK | API REST |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | Sintetizar texto (texto-a-discurso)                                  |     | Sim      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | Serviços cognitivos no ponto final do ditado v1 websocket        | Sim | Não       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | O ponto final do serviço cognitivo on-prem interactive v1 websocket  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | Os serviços cognitivos on-prem conversa v1 websocket endpoint |     |          |

**Resposta:** Esta é uma fusão de:
- Pessoas que tentam o ponto final do ditado para contentores, (não sei como conseguiram a URL)
- O ponto final<sup>da 1ª</sup> festa é o de um contentor.
- O ponto final<sup>do 1º</sup> ponto final do partido retornando mensagens.fragmentos em vez das `speech.hypothesis` mensagens que os pontos finais da<sup>3ª</sup> parte regressam para o ponto final do ditado.
- O carbon quickstarts todos os usos `RecognizeOnce` (modo interativo)
- O carbono afirma que para as mensagens que `speech.fragment` exigem não são devolvidas em modo interativo.
- O carbono que tem o fogo afirma em libertação constrói (matando o processo).

A solução alternativa é ou mudar para usar reconhecimento contínuo no seu código, ou (mais rápido) ligar-se aos pontos finais interativos ou contínuos no recipiente.
Para o seu código, desaponte o ponto final para `host:port` /speech/recognition/interactive/cognitiveservices/v1

Para os vários modos, consulte os modos de voz - veja abaixo:

## <a name="speech-modes---interactive-conversation-dictation"></a>Modos de fala - Interativo, conversa, ditado

[!INCLUDE [speech-modes](includes/speech-modes.md)]

A correção adequada está a vir com o SDK 1.8, que tem suporte on-prem (escolherá o ponto final certo, por isso não seremos piores do que o serviço online). Entretanto, há uma amostra para reconhecimento contínuo, por que não a apontamos?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>Que modo devo usar para vários ficheiros áudio?</b>
</summary>

**Resposta:** Aqui está um [quickstart usando Python.](./get-started-speech-to-text.md?pivots=programming-language-python) Pode encontrar as outras línguas ligadas no site do docs.

Apenas para esclarecer para o interativo, conversa e ditado; esta é uma forma avançada de especificar a forma particular como o nosso serviço irá lidar com o pedido de fala. Infelizmente, para os recipientes on-prem temos que especificar o URI completo (uma vez que inclui a máquina local), por isso esta informação vazou da abstração. Estamos a trabalhar com a equipa SDK para tornar isto mais utilizável no futuro.

<br>
</details>

<details>
<summary>
<b>Como podemos comparar uma medida áspera de transações/segundo/núcleo?</b>
</summary>

**Resposta:** Eis alguns dos números brutos que se esperam do modelo existente (mudará para melhor no que enviaremos em GA):

- Para os ficheiros, o estrangulamento estará no discurso SDK, a 2x. Os primeiros cinco segundos de áudio não são acelerados. Descodificador é capaz de fazer cerca de 3x em tempo real. Para isso, o uso global do CPU será de cerca de 2 núcleos para um único reconhecimento.
- Para o microfone, será a 1x em tempo real. O uso geral deve ser de cerca de 1 núcleo para um único reconhecimento.

Tudo isto pode ser verificado a partir dos registos do estivador. Na verdade, despejamos a linha com estatísticas de sessão e frase/expressão, e isso inclui os números de RTF.


<br>
</details>

<details>
<summary>
<b>É comum dividir ficheiros de áudio em mandrils para uso de recipientes de fala?</b>
</summary>

O meu plano atual é pegar num ficheiro áudio existente e dividi-lo em pedaços de 10 segundos e enviá-los através do contentor. É um cenário aceitável?  Existe uma maneira melhor de processar ficheiros áudio maiores com o recipiente?

**Resposta:** Usa o discurso SDK e dá-lhe o ficheiro, vai fazer a coisa certa. Por que precisa de acabar com o ficheiro?


<br>
</details>

<details>
<summary>
<b>Como faço vários contentores funcionarem no mesmo hospedeiro?</b>
</summary>

O médico diz para expor um porto diferente, o que eu faço, mas o contentor LUIS ainda está a ouvir no porto 5000?

**Resposta:** `-p <outside_unique_port>:5000`Tente. Por exemplo, `-p 5001:5000`.


<br>
</details>

## <a name="technical-questions"></a>Questões técnicas

<details>
<summary>
<b>Como posso conseguir que as APIs não-lotes manuseiem o áudio &lt; com 15 segundos de duração?</b>
</summary>

**Resposta:** `RecognizeOnce()` em modo interativo apenas processa até 15 segundos de áudio, uma vez que o modo se destina a Comandar a Fala onde se espera que as expressões sejam curtas. Se usar `StartContinuousRecognition()` para ditado ou conversa, não há limite de 15 segundos.


<br>
</details>

<details>
<summary>
<b>Quais são os recursos recomendados, CPU e RAM; para 50 pedidos simultâneos?</b>
</summary>

Quantos pedidos simultâneos terá um cabo RAM de 4 núcleos e 4 GB? Se tivermos de servir, por exemplo, 50 pedidos simultâneos, quantos Core e RAM são recomendados?

**Resposta:** Em tempo real, 8 com o nosso mais `en-US` recente, por isso recomendamos a utilização de mais contentores estivadores para além de 6 pedidos simultâneos. Fica mais louco para além de 16 núcleos, e torna-se sensível ao acesso à memória não uniforme (NUMA). O quadro seguinte descreve a alocação mínima e recomendada de recursos para cada recipiente de discurso.

# <a name="speech-to-text"></a>[Conversão de voz em texto](#tab/stt)

| Contentor      | Mínimo             | Recomendado         |
|----------------|---------------------|---------------------|
| Conversão de voz em texto | 2 núcleo, memória de 2 GB | 4 núcleo, memória de 4-GB |

# <a name="custom-speech-to-text"></a>[Discurso-a-texto personalizado](#tab/cstt)

| Contentor             | Mínimo             | Recomendado         |
|-----------------------|---------------------|---------------------|
| Discurso-a-texto personalizado | 2 núcleo, memória de 2 GB | 4 núcleo, memória de 4-GB |

# <a name="text-to-speech"></a>[Conversão de texto em voz](#tab/tts)

| Contentor      | Mínimo             | Recomendado         |
|----------------|---------------------|---------------------|
| Conversão de texto em voz | 1 núcleo, memória de 2 GB | 2 núcleo, memória de 3-GB |

# <a name="custom-text-to-speech"></a>[Texto-a-discurso personalizado](#tab/ctts)

| Contentor             | Mínimo             | Recomendado         |
|-----------------------|---------------------|---------------------|
| Texto-a-discurso personalizado | 1 núcleo, memória de 2 GB | 2 núcleo, memória de 3-GB |

**_

- Cada núcleo deve ser pelo menos 2,6 GHz ou mais rápido.
- Para os ficheiros, o estrangulamento estará no SDK do Discurso, a 2x (os primeiros 5 segundos de áudio não são acelerados).
- O descodificador é capaz de fazer cerca de 2-3x em tempo real. Para isso, o uso global do CPU será próximo de dois núcleos para um único reconhecimento. É por isso que não recomendamos manter mais de duas ligações ativas, por instância de contentor. O lado extremo seria colocar cerca de 10 descodificações a 2x em tempo real numa máquina de oito núcleos como `DS13_V2` . Para a versão 1.3 do recipiente e depois, há um param que pode tentar definir `DECODER_MAX_COUNT=20` .
- Para o microfone, será a 1x em tempo real. O uso geral deve ser de cerca de um núcleo para um único reconhecimento.

Considere o número total de horas de áudio que tem. Se o número for grande, para melhorar a fiabilidade/disponibilidade, sugerimos que se verifiquem mais casos de contentores, seja numa única caixa ou em várias caixas, atrás de um equilibrador de carga. A orquestração poderia ser feita usando Kubernetes (K8S) e Helm, ou com Docker compor.

Como exemplo, para lidar 1000 horas/24 horas, tentámos configurar 3-4 VMs, com 10 instâncias/descodificadores por VM.

<br>
</details>

<details>
<summary>
<b>O recipiente de discurso suporta a pontuação?</b>
</summary>

_ *Resposta:** Temos capitalização (ITN) disponível no recipiente on-prem. A pontuação é dependente da linguagem, e não é apoiada para algumas línguas, incluindo chinês e japonês.

Temos, *de facto,* um suporte de pontuação implícito e básico para os contentores existentes, mas é `off` por defeito. O que isso significa é que podes ter o `.` personagem no teu exemplo, mas não no `。` personagem. Para permitir esta lógica implícita, aqui está um exemplo de como fazê-lo em Python usando o nosso Speech SDK (seria semelhante em outras línguas):

```python
speech_config.set_service_property(
    name='punctuation',
    value='implicit',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

<br>
</details>

<details>
<summary>
<b>Porque é que estou a ter 404 erros ao tentar enviar dados post para o contentor de fala para texto?</b>
</summary>

Aqui está um exemplo HTTP POST:

```http
POST /speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Transfer-Encoding: chunked
User-Agent: PostmanRuntime/7.18.0
Cache-Control: no-cache
Postman-Token: xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Host: 10.0.75.2:5000
Accept-Encoding: gzip, deflate
Content-Length: 360044
Connection: keep-alive
HTTP/1.1 404 Not Found
Date: Tue, 22 Oct 2019 15:42:56 GMT
Server: Kestrel
Content-Length: 0
```

**Resposta:** Não apoiamos a API REST em nenhum dos recipientes de discurso para texto, apenas apoiamos WebSockets através do SDK de discurso. Consulte sempre a documentação oficial, consulte [os pontos finais de previsão de consulta](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>


<details>
<summary>
<b> Porque é que o contentor funciona como utilizador não-raiz? Que problemas podem ocorrer por causa disto?</b>
</summary>

**Resposta:** Note que o utilizador predefinido dentro do recipiente é um utilizador não-raiz. Isto fornece proteção contra os processos que escapam ao contentor e obtém permissões agravadas no nó de hospedeiro. Por padrão, algumas plataformas como a Plataforma de Contentores OpenShift já o fazem executando contentores utilizando um ID de utilizador arbitrariamente atribuído. Para estas plataformas, o utilizador sem raízes terá de ter permissões para escrever a qualquer volume mapeado externamente que necessite de escrita. Por exemplo, uma pasta de registo ou uma pasta de descarregamento de modelos personalizados.
<br>
</details>

<details>
<summary>
<b>Ao usar o serviço de fala-a-texto, por que estou a cometer este erro?</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**Resposta:** Isto acontece normalmente quando se alimenta o áudio mais rapidamente do que o recipiente de reconhecimento de voz pode levá-lo. Os amortecedores de clientes enchem-se e o cancelamento é desencadeado. Tem de controlar a concordância e o RTF para onde envia o áudio.

<br>
</details>

<details>
<summary>
<b>Poderia explicar estes erros do contentor text-to-speech dos exemplos C+++ ?</b>
</summary>

**Resposta:** Se a versão do recipiente tiver mais de 1.3, este código deve ser utilizado:

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Os recipientes mais antigos não têm o ponto final necessário para o Carbono trabalhar com a `FromHost` API. Se os recipientes utilizados para a versão 1.3, então este código deve ser utilizado:

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Abaixo está um exemplo de utilização da `FromEndpoint` API:

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 A `SetSpeechSynthesisVoiceName` função é chamada porque os recipientes com um motor text-to-speech atualizado requerem o nome de voz.

<br>
</details>

<details>
<summary>
<b>Como posso usar v1.7 do Speech SDK com um recipiente de fala?</b>
</summary>

**Resposta:** Existem três pontos finais no recipiente de discurso para diferentes utilizações, são definidos como modos de fala - veja abaixo:

## <a name="speech-modes"></a>Modos de fala

[!INCLUDE [speech-modes](includes/speech-modes.md)]

São para propósitos diferentes e são usados de forma diferente.

Amostras [de python:](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)
- Para um único reconhecimento (modo interativo) com um ponto final personalizado (isto é, `SpeechConfig` com um parâmetro de ponto final), ver `speech_recognize_once_from_file_with_custom_endpoint_parameters()` .
- Para reconhecimento contínuo (modo de conversação), e basta modificar para utilizar um ponto final personalizado como acima, ver `speech_recognize_continuous_from_file()` .
- Para ativar o ditado em amostras como acima (apenas se precisar), logo após a `speech_config` criação, adicione código `speech_config.enable_dictation()` .

Em C# para permitir o ditado, invoque a `SpeechConfig.EnableDictation()` função.

### <a name="fromendpoint-apis"></a>`FromEndpoint` APIs
| Linguagem | Detalhes da API |
|----------|:------------|
| C++ | <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#fromendpoint" target="_blank">`SpeechConfig::FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet" target="_blank">`SpeechConfig.FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromendpoint" target="_blank">`SpeechConfig.fromendpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithendpoint" target="_blank">`SPXSpeechConfiguration:initWithEndpoint;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Não está atualmente apoiado, nem está previsto. |

<br>
</details>

<details>
<summary>
<b>Como posso usar v1.8 do Speech SDK com um recipiente de discurso?</b>
</summary>

**Resposta:** Há uma nova `FromHost` API. Isto não substitui nem modifica quaisquer APIs existentes. Apenas adiciona uma forma alternativa de criar um config de fala usando um hospedeiro personalizado.

### <a name="fromhost-apis"></a>`FromHost` APIs

| Linguagem | Detalhes da API |
|--|:-|
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet" target="_blank">`SpeechConfig.FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C++ | <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#fromhost" target="_blank">`SpeechConfig::FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromhost" target="_blank">`SpeechConfig.fromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithhost" target="_blank">`SPXSpeechConfiguration:initWithHost;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Atualmente, não é suportado |

> Parâmetros: anfitrião (obrigatório), chave de subscrição (opcional, se puder utilizar o serviço sem ele).

O formato para anfitrião `protocol://hostname:port` é onde é opcional `:port` (ver abaixo):
- Se o recipiente estiver a funcionar localmente, o nome de hospedeiro é `localhost` .
- Se o contentor estiver a funcionar num servidor remoto, utilize o nome de anfitrião ou o endereço IPv4 desse servidor.

Exemplos de parâmetros de anfitrião para discurso-a-texto:
- `ws://localhost:5000` - ligação não segura a um contentor local que utilize a porta 5000
- `ws://some.host.com:5000` - ligação não segura a um contentor em execução num servidor remoto

Amostras de python de cima, mas use `host` parâmetro em vez `endpoint` de:

```python
speech_config = speechsdk.SpeechConfig(host="ws://localhost:5000")
```

<br>
</details>

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Contentores de Serviços Cognitivos](speech-container-howto.md)