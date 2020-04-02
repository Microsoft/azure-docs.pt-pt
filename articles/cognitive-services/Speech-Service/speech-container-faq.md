---
title: Contentores de serviço de fala frequentemente fazem perguntas (FAQ)
titleSuffix: Azure Cognitive Services
description: Instale e corra recipientes de fala. o discurso-a-texto transcreve os fluxos de áudio para texto em tempo real que as suas aplicações, ferramentas ou dispositivos podem consumir ou exibir. Texto-a-fala converte texto de entrada em discurso sintetizado como o humano.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: 2afb7c84c4d737703917d12a43aa7c3bb799836b
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80537023"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>Contentores de serviço de fala frequentemente fazem perguntas (FAQ)

Ao utilizar o serviço de Fala com contentores, confie nesta coleção de perguntas frequentes antes de se intensificar para apoiar. Este artigo captura questões de grau variado, de geral a técnico. Para expandir uma resposta, clique na pergunta.

## <a name="general-questions"></a>Perguntas gerais

<details>
<summary>
<b>Como funcionam os contentores da Fala e como os configurarei?</b>
</summary>

**Resposta:** Ao criar o cluster de produção, há várias coisas a considerar. Em primeiro lugar, a criação de uma única língua, vários recipientes, na mesma máquina, não devem ser um problema importante. Se estiver com problemas, pode ser um problema relacionado com hardware - por isso, primeiro, olharíamos para o recurso, isto é; CPU e especificações de memória.

Considere por um `ja-JP` momento, o recipiente e o último modelo. O modelo acústico é a peça cpu mais exigente em termos de CPU, enquanto o modelo linguístico exige mais memória. Quando comparamos a utilização, são necessários cerca de 0,6 núcleos cpu para processar um único pedido de fala a texto quando o áudio flui em tempo real (como do microfone). Se estiver a alimentar áudio mais rápido do que em tempo real (como a partir de um ficheiro), essa utilização pode duplicar (1,2x núcleos). Entretanto, a memória listada abaixo é a memória operacional para descodificar o discurso. Não *tem* em conta a dimensão real do modelo linguístico, que reside em cache de ficheiros. Pois `ja-JP` são mais 2 GB; para, `en-US`pode ser mais (6-7 GB).

Se tiver uma máquina onde a memória é escassa e estiver a tentar implementar várias línguas, é possível que a cache de ficheiros esteja cheia, e o SISTEMA seja forçado a páginar modelos dentro e fora. Para uma transcrição em execução, isso pode ser desastroso, e pode levar a abrandamentos e outras implicações de desempenho.

Além disso, pré-embalamos executáveis para máquinas com o conjunto de instruções [de extensão vetorial avançada (AVX2).](speech-container-howto.md#advanced-vector-extension-support) Uma máquina com o conjunto de instruções AVX512 exigirá a geração de códigos para esse objetivo, e o arranque de 10 recipientes para 10 idiomas pode esgotar temporariamente o CPU. Uma mensagem como esta aparecerá nos registos dos estivadores:

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

Finalmente, pode definir o número de descodificadores que `DECODER MAX_COUNT` deseja dentro de um *único* recipiente usando variável. Então, basicamente, devemos começar com o seu SKU (CPU/memória), e podemos sugerir como tirar o melhor partido dele. Um ótimo ponto de partida é referir-se às especificações recomendadas de recursos da máquina hospedeira.

<br>
</details>

<details>
<summary>
<b>Poderia ajudar no planeamento da capacidade e na estimativa de custos dos contentores da fala on-prem?</b>
</summary>

**Resposta:** Para a capacidade do contentor no modo de processamento do lote, cada descodificador poderia manusear 2-3x em tempo real, com dois núcleos de CPU, para um único reconhecimento. Não recomendamos manter mais de dois reconhecimentos simultâneos por instância de contentores, mas recomendamos a execução de mais instâncias de contentores por razões de fiabilidade/disponibilidade, por trás de um equilibrador de carga.

Embora pudéssemos ter cada instância de contentores a funcionar com mais descodificadores. Por exemplo, podemos ser capazes de configurar 7 descodificadores por instância de contentor numa máquina de oito núcleos (a mais de 2x cada), produzindo uma entrada de 15x. Há um param `DECODER_MAX_COUNT` para estar atento. Para o caso extremo, surgem questões de fiabilidade e latência, com a entrada a aumentar significativamente. Para um microfone, será a 1x em tempo real. A utilização global deve ser de cerca de um núcleo para um único reconhecimento.

Para o cenário de processamento de 1 K horas/dia em modo de processamento de lote, em caso extremo, 3 VMs poderiam manuseá-lo em 24 horas, mas não garantidos. Para lidar com dias de pico, failover, atualização e para fornecer o mínimo de backup/BCP, recomendamos 4-5 máquinas em vez de 3 por cluster, e com 2+ clusters.

Para hardware, utilizamos o `DS13_v2` Azure VM padrão como referência (cada núcleo deve ter 2,6 GHz ou melhor, com conjunto de instruções AVX2 ativado).

| Instância  | vCPU(s) | RAM    | Armazenamento temporário | Pay-as-you-go com AHB | Reserva de 1 ano com AHB (% Poupança) | 3 anos reservado com AHB (% Poupança) |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 GiB | 112 GiB      | $0.598/hora            | $0.3528/hora (~41%)                 | $0.2333/hora (~61%)                  |

Com base na referência de design (dois clusters de 5 VMs para lidar com o processamento de lote de áudio de 1 K horas/dia), o custo de hardware de 1 ano será:

> 2 (clusters) * 5 (VMs por cluster) * $0,3528/hora * 365 (dias) * 24 (horas) = $31K/ano

Ao mapear para a máquina física, uma estimativa geral é 1 vCPU = 1 Núcleo de CPU Físico. Na realidade, 1vCPU é mais poderoso que um único núcleo.

Para o on-prem, todos estes fatores adicionais entram em jogo:

- Em que tipo é o CPU físico e quantos núcleos nele
- Quantos CPUs correndo juntos na mesma caixa/máquina
- Como os VMs são configurados
- Como é utilizado hiper-roscado / multi-threading
- Como a memória é partilhada
- O SO, etc.

Normalmente não está tão bem afinado como o Azure o ambiente. Considerando outras despesas gerais, diria que uma estimativa segura é de 10 núcleos de CPU físico = 8 Azure vCPU. Embora os cpus populares só tenham oito núcleos. Com a implantação on-prem, o custo será mais elevado do que a utilização de VMs Azure. Além disso, considere a taxa de depreciação.

O custo do serviço é o mesmo que o serviço online: $1/hora para o discurso-a-texto. O custo do serviço da Fala é:

> $1 * 1000 * 365 = $365K

O custo de manutenção pago à Microsoft depende do nível de serviço e do conteúdo do serviço. É vários desde $29.99/mês para o nível básico para centenas de milhares se o serviço no local estiver envolvido. Um número áspero é de $300/hora para o serviço/manutenção. O custo das pessoas não está incluído. Outros custos de infraestrutura (como armazenamento, redes e equilibradores de carga) não estão incluídos.

<br>
</details>

<details>
<summary>
<b>Porque é que falta pontuação da transcrição?</b>
</summary>

**Resposta:** O `speech_recognition_language=<YOUR_LANGUAGE>` pedido deve ser explicitamente configurado no pedido se estiverem a utilizar o cliente Carbon.

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
<b>Posso usar um modelo acústico personalizado e modelo de linguagem com recipiente de Fala?</b>
</summary>

Atualmente só podemos passar um modelo de ID, quer modelo de idioma personalizado quer modelo acústico personalizado.

**Resposta:** A decisão de *não* apoiar simultaneamente modelos acústicos e linguísticos foi tomada. Isto permanecerá em vigor, até que seja criado um identificador unificado para reduzir as quebras de API. Portanto, infelizmente, isto não é apoiado neste momento.

<br>
</details>

<details>
<summary>
<b>Pode explicar estes erros do recipiente de discurso a texto personalizado?</b>
</summary>

**Erro 1:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**Resposta 1:** Se estás a treinar com o modelo personalizado mais recente, não apoiamos isso. Se treinar com uma versão mais antiga, deve ser possível usá-la. Ainda estamos a trabalhar no apoio às versões mais recentes.

Essencialmente, os recipientes personalizados não suportam modelos acústicos baseados em Halide ou ONNX (que é o padrão no portal de treino personalizado). Isto deve-se ao não encriptado de modelos personalizados e não queremos expor os modelos ONNX, no entanto; modelos linguísticos são bons. O cliente terá de selecionar explicitamente um modelo não ONNX mais antigo para formação personalizada. A precisão não será afetada. O tamanho do modelo pode ser maior (em 100 MB).

> Modelo de suporte > 20190220 (v4.5 Unificado)

**Erro 2:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**Resposta 2:** Tem de fornecer o nome de voz correto no pedido, que é sensível ao caso. Consulte o mapeamento completo do nome do serviço. Tem de `en-US-JessaRUS`utilizar `en-US-JessaNeural` , como não está disponível neste momento na versão contentor do texto-a-fala.

**Erro 3:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**Resposta 3:** Tu gostas de criar um recurso da Fala, não um recurso dos Serviços Cognitivos.


<br>
</details>

<details>
<summary>
<b>Que protocolos API são suportados, REST ou WS?</b>
</summary>

**Resposta:** Para recipientes de discurso a texto e de discurso personalizado, atualmente apenas apoiamos o protocolo baseado em websocket. O SDK só suporta chamar wS, mas não REST. Há um plano para adicionar apoio rest, mas não ETA por enquanto. Consulte sempre a documentação oficial, consulte [os pontos finais](speech-container-howto.md#query-the-containers-prediction-endpoint)da previsão de consulta .

<br>
</details>

<details>
<summary>
<b>O CentOS é apoiado para contentores da Fala?</b>
</summary>

**Resposta:** O CentOS 7 ainda não é apoiado pela Python SDK, também o Ubuntu 19.04 não é suportado.

O pacote Python Speech SDK está disponível para estes sistemas operativos:
- **Janelas** - x64 e x86
- **Mac** - macOS X versão 10.12 ou posterior
- **Linux** - Ubuntu 16.04, Ubuntu 18.04, Debian 9 em x64

Para obter mais informações sobre a configuração do ambiente, consulte a [configuração da plataforma Python.](quickstarts/setup-platform.md?pivots=programming-language-python) Por enquanto, Ubuntu 18.04 é a versão recomendada.

<br>
</details>

<details>
<summary>
<b>Porque é que estou a ter erros ao tentar chamar pontos finais de previsão do LUIS?</b>
</summary>

Estou a usar o contentor LUIS numa implantação ioT Edge e estou a tentar chamar o ponto final da previsão LUIS de outro contentor. O contentor LUIS está a ouvir na porta 5001, e o URL que estou a usar é o seguinte:

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

O que é que isto quer dizer? O que me está a escapar? Estava a seguir o exemplo para o SDK do discurso, [daqui.](https://github.com/Azure-Samples/cognitive-services-speech-sdk) O cenário é que estamos a detetar o áudio diretamente a partir do microfone do PC e a tentar determinar a intenção, com base na app LUIS que treinámos. O exemplo a que me relacionei faz exatamente isso. E funciona bem com o serviço baseado em nuvem LUIS. Usar o SDK do discurso parecia salvar-nos de ter de fazer uma chamada explícita separada para a API do discurso ao texto e, em seguida, uma segunda chamada para luis.

Portanto, tudo o que estou a tentar fazer é mudar do cenário de usar o LUIS na nuvem para usar o contentor LUIS. Não consigo imaginar que se o SDK do Discurso funcionar para um, não funcionará para o outro.

**Resposta:** O SDK de Fala não deve ser utilizado contra um recipiente LUIS. Para a utilização do recipiente LUIS, deve ser utilizado o LUIS SDK ou a API LUIS REST. O SDK da fala deve ser usado contra um recipiente de fala.

Uma nuvem é diferente de um recipiente. Uma nuvem pode ser composta por múltiplos recipientes agregados (às vezes chamados micro serviços). Portanto, há um recipiente LUIS e depois há um contentor da Fala - Dois recipientes separados. O contentor da fala só fala. O recipiente LUIS só faz LUIS. Na nuvem, porque ambos os contentores são conhecidos por serem implantados, e é um mau desempenho para um cliente remoto ir à nuvem, fazer discurso, voltar, depois voltar para a nuvem novamente e fazer LUIS, fornecemos uma funcionalidade que permite ao cliente ir ao Discurso, ficar na nuvem, ir para o LUIS e depois voltar para o cliente. Assim, mesmo neste cenário, o SDK do Discurso vai para o contentor da nuvem de Discurso com áudio, e depois o contentor da nuvem de discurso fala com o contentor da nuvem LUIS com texto. O recipiente LUIS não tem noção de aceitar áudio (não faria sentido que o recipiente LUIS aceitasse o streaming de áudio - o LUIS é um serviço baseado em texto). Com o on-prem, não temos a certeza de que o nosso cliente tenha implantado ambos os contentores, não presumimos orquestrar entre contentores nas instalações dos nossos clientes, e se ambos os contentores forem implantados on-prem, dado que são mais locais para o cliente, não é um fardo ir primeiro ao SR, voltar ao cliente, e ter o cliente então pegar nesse texto e ir para a LUIS.

<br>
</details>

<details>
<summary>
<b>Porque é que estamos a ter erros com o macOS, o contentor da fala e o SDK python?</b>
</summary>

Quando enviamos um ficheiro *.wav* para ser transcrito, o resultado volta com:

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

Sabemos que a tomada está corretamente configurada.

**Resposta:** Se for esse o caso, veja [esta questão do GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310) Temos uma volta por aí, [proposta aqui.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722)

O carbono fixou isto na versão 1.8.


<br>
</details>

<details>
<summary>
<b>Quais são as diferenças nos pontos finais do contentor da Fala?</b>
</summary>

Poderia ajudar a preencher as seguintes métricas de teste, incluindo quais as funções a testar, e como testar as APIs SDK e REST? Especialmente, diferenças na "interativa" e na "conversa", que não vi do doc/amostra existente.

| Ponto Final                                                | Teste funcional                                                   | SDK | API REST |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | Texto sintetizador (texto-a-fala)                                  |     | Sim      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | Serviços Cognitivos em prem ditado v1 ponto final websocket        | Sim | Não       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | O ponto final de websocket interativo v1 do Serviço Cognitivo on-prem  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | Os serviços cognitivos em prem conversa v1 websocket endpoint |     |          |

**Resposta:** Esta é uma fusão de:
- Pessoas tentando o ponto final de ditado para recipientes, (não sei como conseguiram aquele URL)
- O ponto final da<sup>1ª</sup> festa é o de um contentor.
- O discurso final do<sup>1º</sup> partido retorna.fragmenta mensagens em vez das `speech.hypothesis` mensagens que os pontos finais da<sup>3ª</sup> parte regressam para o ponto final do ditado.
- O Carbon acelera `RecognizeOnce` todas as utilizações (modo interativo)
- O carbono tem `speech.fragment` uma afirmação de que para mensagens que exigem que não sejam devolvidas em modo interativo.
- O carbono ter o fogo afirma em construções de libertação (matando o processo).

A suticultura é mudar para utilizar o reconhecimento contínuo no seu código, ou (mais rapidamente) ligar-se aos pontos finais interativos ou contínuos do recipiente.
Para o seu código, detete o ponto final para <anfitrião:porta>/fala/reconhecimento/serviços interativos/cognitivos/v1

Para os vários modos, consulte os modos De Fala - veja abaixo:

[!INCLUDE [speech-modes](includes/speech-modes.md)]

A correção adequada vem com o SDK 1.8, que tem suporte on-prem (escolherá o ponto final certo, por isso não seremos piores que o serviço online). Entretanto, há uma amostra para reconhecimento contínuo, por que não apontamos para ela?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>Que modo devo usar para vários ficheiros áudio?</b>
</summary>

**Resposta:** Aqui está um [começo rápido usando Python.](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-python) Pode encontrar outras línguas ligadas no site do c.s.

Apenas para esclarecer para o interativo, conversa e ditado; esta é uma forma avançada de especificar a forma particular como o nosso serviço irá lidar com o pedido de fala. Infelizmente, para os recipientes on-prem temos que especificar o URI completo (uma vez que inclui a máquina local), por isso esta informação vazou da abstração. Estamos a trabalhar com a equipa sdk para tornar isto mais utilizável no futuro.

<br>
</details>

<details>
<summary>
<b>Como podemos comparar uma medida áspera de transações/segundo/núcleo?</b>
</summary>

**Resposta:** Eis alguns dos números brutos a esperar do modelo existente (mudará para melhor no que enviaremos na GA):

- Para ficheiros, a aceleração estará no discurso SDK, a 2x. Os primeiros cinco segundos de áudio não são estrangulados. Descodificador é capaz de fazer cerca de 3x em tempo real. Para isso, a utilização global do CPU será de perto de 2 núcleos para um único reconhecimento.
- Para o microfone, será a 1x em tempo real. A utilização global deve ser de cerca de 1 núcleo para um único reconhecimento.

Tudo isto pode ser verificado a partir dos troncos de estivador. Na verdade, despejamos a linha com estatísticas de sessão e frase/expressão, e isso inclui os números de RTF.


<br>
</details>

<details>
<summary>
<b>É comum dividir ficheiros áudio em chucks para uso de contentores da Fala?</b>
</summary>

O meu plano atual é pegar num ficheiro áudio existente e dividi-lo em pedaços de 10 segundos e enviá-los através do contentor. É um cenário aceitável?  Existe uma maneira melhor de processar ficheiros áudio maiores com o recipiente?

**Resposta:** Usa o discurso SDK e dá-lhe o ficheiro, vai fazer a coisa certa. Por que precisa de arquivar?


<br>
</details>

<details>
<summary>
<b>Como faço vários contentores funcionarem no mesmo hospedeiro?</b>
</summary>

O médico diz para expor um porto diferente, o que eu faço, mas o contentor do LUIS ainda está a ouvir no porto 5000?

**Resposta:** Tente. `-p <outside_unique_port>:5000` Por exemplo, `-p 5001:5000`.


<br>
</details>

## <a name="technical-questions"></a>Questões técnicas

<details>
<summary>
<b>Como posso conseguir que as APIs &lt;não-lote sais para lidar com áudio 15 segundos de duração?</b>
</summary>

**Resposta:** Isto está em modo interativo. Se usar ditado ou conversação, isso não é um problema.


<br>
</details>

<details>
<summary>
<b>Quais são os recursos recomendados, CPU e RAM; para 50 pedidos simultâneos?</b>
</summary>

Quantos pedidos simultâneos um cabo de 4 núcleos, 4 GB de RAM? Se tivermos de servir, por exemplo, 50 pedidos simultâneos, quantos Core e RAM são recomendados?

**Resposta:** Em tempo real, 8 `en-US`com o nosso mais recente , por isso recomendamos a utilização de mais contentores para além de 6 pedidos simultâneos. Fica mais louco além de 16 núcleos, e torna-se um acesso não uniforme de memória (NA) sensível ao nó. O quadro seguinte descreve a alocação mínima e recomendada de recursos para cada recipiente da Fala.

# <a name="speech-to-text"></a>[Conversão de voz em texto](#tab/stt)

| Contentor      | Mínimo             | Recomendado         |
|----------------|---------------------|---------------------|
| Conversão de voz em texto | 2 núcleo, 2-GB memória | 4 núcleos, 4-GB de memória |

# <a name="custom-speech-to-text"></a>[Discurso personalizado a texto](#tab/cstt)

| Contentor             | Mínimo             | Recomendado         |
|-----------------------|---------------------|---------------------|
| Discurso personalizado a texto | 2 núcleo, 2-GB memória | 4 núcleos, 4-GB de memória |

# <a name="text-to-speech"></a>[Conversão de texto em voz](#tab/tts)

| Contentor      | Mínimo             | Recomendado         |
|----------------|---------------------|---------------------|
| Conversão de texto em voz | 1 núcleo, 2-GB de memória | 2 núcleo, 3-GB de memória |

# <a name="custom-text-to-speech"></a>[Texto-a-fala personalizado](#tab/ctts)

| Contentor             | Mínimo             | Recomendado         |
|-----------------------|---------------------|---------------------|
| Texto-a-fala personalizado | 1 núcleo, 2-GB de memória | 2 núcleo, 3-GB de memória |

***

- Cada núcleo deve ter pelo menos 2,6 GHz ou mais rápido.
- Para os ficheiros, o estrangulamento estará no SDK do Discurso, a 2x (os primeiros 5 segundos de áudio não são estrangulados).
- O descodificador é capaz de fazer cerca de 2-3x em tempo real. Para isso, a utilização global do CPU será próxima de dois núcleos para um único reconhecimento. É por isso que não recomendamos manter mais de duas ligações ativas, por instância de contentores. O lado extremo seria colocar cerca de 10 descodificadores a 2x em tempo real numa máquina de oito núcleos como `DS13_V2`. Para a versão 1.3 e mais tarde, há um `DECODER_MAX_COUNT=20`param que pode tentar definir .
- Para o microfone, será a 1x em tempo real. A utilização global deve ser de cerca de um núcleo para um único reconhecimento.

Considere o número total de horas de áudio que tem. Se o número for grande, para melhorar a fiabilidade/disponibilidade, sugerimos executar mais instâncias de contentores, seja numa única caixa ou em várias caixas, atrás de um equilibrista de carga. A orquestração poderia ser feita usando Kubernetes (K8S) e Helm, ou com Docker compôs.

Como exemplo, para lidar com 1000 horas/24 horas, tentámos criar 3-4 VMs, com 10 instâncias/descodificadores por VM.

<br>
</details>

<details>
<summary>
<b>O contentor da Fala apoia a pontuação?</b>
</summary>

**Resposta:** Temos capitalização (ITN) disponível no recipiente on-prem. A pontuação é dependente da linguagem, e não é suportada para algumas línguas, incluindo o chinês e o japonês.

Temos *do* suporte implícito e básico para a pontuação dos contentores `off` existentes, mas é por defeito. O que isso significa é `.` que podes ter `。` o personagem no teu exemplo, mas não no personagem. Para permitir esta lógica implícita, aqui está um exemplo de como fazê-lo em Python usando o nosso SDK de Fala (seria semelhante em outras línguas):

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
<b>Porque é que estou a ter 404 erros ao tentar publicar dados para o contentor da fala-a-texto?</b>
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

**Resposta:** Não apoiamos a API REST em nenhum dos recipientes de discurso a texto, apenas apoiamos WebSockets através do Speech SDK. Consulte sempre a documentação oficial, consulte [os pontos finais](speech-container-howto.md#query-the-containers-prediction-endpoint)da previsão de consulta .

<br>
</details>

<details>
<summary>
<b>Ao usar o serviço de fala-a-texto, porque é que estou a ter este erro?</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**Resposta:** Isto normalmente acontece quando se alimenta o áudio mais rapidamente do que o recipiente de reconhecimento da Fala pode tostá-lo. Os amortecedores do cliente enchem-se e o cancelamento é desencadeado. Tens de controlar a moeda e a RTF a que envias o áudio.

<br>
</details>

<details>
<summary>
<b>Pode explicar estes erros de contentor de texto para fala a partir dos exemplos C++?</b>
</summary>

**Resposta:** Se a versão do recipiente for superior a 1.3, então este código deve ser utilizado:

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Os recipientes mais antigos não têm o ponto `FromHost` final necessário para que o Carbono trabalhe com a API. Se os recipientes utilizados para a versão 1.3, então este código deve ser utilizado:

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Abaixo está um exemplo `FromEndpoint` de utilização da API:

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 A `SetSpeechSynthesisVoiceName` função é chamada porque os recipientes com um motor de texto-a-fala atualizado requerem o nome de voz.

<br>
</details>

<details>
<summary>
<b>Como posso usar v1.7 do SDK da fala com um recipiente de discurso?</b>
</summary>

**Resposta:** Existem três pontos finais no recipiente da Fala para diferentes utilizações, são definidos como modos de Fala - veja abaixo:

[!INCLUDE [speech-modes](includes/speech-modes.md)]

São para diferentes fins e são usados de forma diferente.

[Amostras de](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)python:
- Para um único reconhecimento (modo interativo) com um ponto final personalizado (isto é; `SpeechConfig` com um parâmetro de `speech_recognize_once_from_file_with_custom_endpoint_parameters()`ponto final, ver .
- Para um reconhecimento contínuo (modo de conversação), e apenas modifique para utilizar um ponto final personalizado como acima, ver `speech_recognize_continuous_from_file()`.
- Para ativar o ditado em amostras como acima (apenas `speech_config`se `speech_config.enable_dictation()`realmente precisar), logo após a criação, adicione código .

Em C# para permitir o `SpeechConfig.EnableDictation()` ditado, invoque a função.

### <a name="fromendpoint-apis"></a>`FromEndpoint`APIs
| Idioma | Detalhes da API |
|----------|:------------|
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromendpoint" target="_blank">`SpeechConfig::FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet" target="_blank">`SpeechConfig.FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-java-stable" target="_blank">`SpeechConfig.fromendpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initWithEndpoint" target="_blank">`SPXSpeechConfiguration:initWithEndpoint;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Não é atualmente apoiado, nem está previsto. |

<br>
</details>

<details>
<summary>
<b>Como posso usar v1.8 do SDK da fala com um recipiente de discurso?</b>
</summary>

**Resposta:** Há uma nova `FromHost` API. Isto não substitui nem modifica as APIs existentes. Apenas adiciona uma forma alternativa de criar um config de fala usando um hospedeiro personalizado.

### <a name="fromhost-apis"></a>`FromHost`APIs

| Idioma | Detalhes da API |
|--|:-|
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet" target="_blank">`SpeechConfig.FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromhost" target="_blank">`SpeechConfig::FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-java-stable" target="_blank">`SpeechConfig.fromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithhost" target="_blank">`SPXSpeechConfiguration:initWithHost;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Atualmente, não é suportado |

> Parâmetros: anfitrião (obrigatório), chave de subscrição (opcional, se puder utilizar o serviço sem ele).

Formato para `protocol://hostname:port` `:port` hospedeiro é onde é opcional (ver abaixo):
- Se o contentor estiver a funcionar `localhost`localmente, o nome de anfitrião é .
- Se o recipiente estiver a funcionar num servidor remoto, utilize o nome de anfitrião ou o endereço IPv4 desse servidor.

Exemplos de parâmetros de acolhimento para o discurso-a-texto:
- `ws://localhost:5000`- ligação não segura a um contentor local que utilize o porto 5000
- `ws://some.host.com:5000`- ligação não segura a um contentor em funcionamento num servidor remoto

Amostras de python `host` de cima, `endpoint`mas utilizem parâmetros em vez de:

```python
speech_config = speechsdk.SpeechConfig(host="ws://localhost:5000")
```

<br>
</details>

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Recipientes de Serviços Cognitivos](speech-container-howto.md)
