---
title: O que é o serviço de Voz?
titleSuffix: Azure Cognitive Services
description: O serviço de discurso é a unificação da tradução de discurso para texto, texto-a-fala e tradução da fala numa única subscrição do Azure. Adicione a fala às suas aplicações, ferramentas e dispositivos com o SDK de fala, dispositivos de fala SDK ou APIs REST.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 11/23/2020
ms.author: trbye
ms.openlocfilehash: 3b20773de62f036b507f1612f2b38d613d54ba2f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96485200"
---
# <a name="what-is-the-speech-service"></a>O que é o serviço de Voz?

O serviço de discurso é a unificação da palavra-a-texto, texto-a-discurso e tradução da fala numa única subscrição do Azure. É fácil de falar para ativar as suas aplicações, ferramentas e dispositivos com o [Speech CLI](spx-overview.md), [Speech SDK,](./speech-sdk.md) [Speech Devices SDK,](./speech-devices-sdk-quickstart.md?pivots=platform-android) [Speech Studio](https://speech.microsoft.com/)ou [REST APIs](#reference-docs).

> [!IMPORTANT]
> O serviço de discurso substituiu a API de Discurso de Bing e o Discurso do Tradutor. Consulte a secção _de migração_ para obter instruções de migração.

As seguintes funcionalidades fazem parte do serviço Speech. Utilize os links desta tabela para saber mais sobre casos de uso comuns para cada recurso, ou navegue na referência API.

| Serviço | Funcionalidade | Descrição | SDK | REST |
|---------|---------|-------------|-----|------|
| [Discurso-a-Texto](speech-to-text.md) | Discurso-a-texto em tempo real | A palavra-para-texto transcreve ou traduz streams de áudio ou ficheiros locais para texto em tempo real que as suas aplicações, ferramentas ou dispositivos podem consumir ou exibir. Use o discurso-a-texto com [a Compreensão da Linguagem (LUIS)](../luis/index.yml) para obter as intenções dos utilizadores da fala transcrita e agir em comandos de voz. | [Sim](./speech-sdk.md) | [Sim](#reference-docs) |
| | [Lote Discurso-a-Texto](batch-transcription.md) | Lote O discurso-a-texto permite a transcrição assíncrona de discurso a texto de grandes volumes de dados áudio da fala armazenados no Azure Blob Storage. Além de converter áudio de fala em texto, o Batch Speech-to-text também permite a diarização e a análise de sentimentos. | Não | [Sim](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) |
| | [Conversa multi-dispositivo](multi-device-conversation.md) | Conecte vários dispositivos ou clientes numa conversação para enviar mensagens baseadas em discursos ou textos, com suporte fácil para transcrição e tradução| Sim | Não |
| | [Transcrição de conversa](./conversation-transcription.md) | Permite o reconhecimento da fala em tempo real, a identificação e a diarização dos altifalantes. É perfeito para transcrever reuniões presenciais com a capacidade de distinguir os falantes. | Sim | Não |
| | [Criar modelos de discurso personalizados](#customize-your-speech-experience) | Se estiver a usar texto-a-texto para reconhecimento e transcrição num ambiente único, pode criar e formar modelos acústicos, linguísticos e de pronúncia personalizados para abordar o ruído ambiente ou o vocabulário específico da indústria. | Não | [Sim](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) |
| [Texto-a-Discurso](text-to-speech.md) | Conversão de texto em voz | O texto-a-discurso converte o texto de entrada em linguagem humana sintetizada utilizando [a linguagem de marcação da síntese de fala (SSML)](speech-synthesis-markup.md). Escolha entre vozes padrão e vozes neurais (ver [suporte linguístico).](language-support.md) | [Sim](./speech-sdk.md) | [Sim](#reference-docs) |
| | [Criar vozes personalizadas](#customize-your-speech-experience) | Crie fontes de voz personalizadas únicas à sua marca ou produto. | Não | [Sim](#reference-docs) |
| [Tradução de Voz](speech-translation.md) | Tradução de voz | A tradução da fala permite a tradução em tempo real, multi-linguístico da fala para as suas aplicações, ferramentas e dispositivos. Utilize este serviço para tradução fala-a-fala e discurso-a-texto. | [Sim](./speech-sdk.md) | Não |
| [Assistentes de voz](voice-assistants.md) | Assistentes de voz | Os assistentes de voz que usam o serviço Speech capacitam os desenvolvedores para criar interfaces de conversação naturais e humanas para as suas aplicações e experiências. O serviço de assistente de voz proporciona uma interação rápida e fiável entre um dispositivo e uma implementação de assistente que utiliza o canal de Fala de Linha Direta do Quadro Bot ou o serviço de Comandos Personalizados integrados (Pré-visualização) para a conclusão da tarefa. | [Sim](voice-assistants.md) | Não |
| [Reconhecimento de Orador](speaker-recognition-overview.md) | Verificação de & de verificação de altifalantes | O serviço de Reconhecimento de Altifalantes fornece algoritmos que verificam e identificam os altifalantes pelas suas características de voz únicas. O Reconhecimento dos Oradores é usado para responder à pergunta "quem está a falar?". | Sim | [Sim](/rest/api/speakerrecognition/) |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service-for-free"></a>Experimente o serviço de Voz gratuitamente

Para os seguintes passos, precisa tanto de uma conta Microsoft como de uma conta Azure. Se não tiver uma conta Microsoft, pode inscrever-se gratuitamente no portal da conta da [Microsoft.](https://account.microsoft.com/account) Selecione **Iniciar sessão com** a Microsoft e, quando lhe pedirem para iniciar sessão, selecione **Criar uma conta Microsoft**. Siga os passos para criar e verificar a sua nova conta Microsoft.

Assim que tiver uma conta Microsoft, vá à página de inscrição do [Azure](https://azure.microsoft.com/free/ai/), selecione **Iniciar gratuitamente** e crie uma nova conta Azure utilizando uma conta Microsoft. Aqui está um vídeo de [como se inscrever para a conta gratuita do Azure.](https://www.youtube.com/watch?v=GWT2R1C_uUU)

> [!NOTE]
> Quando se inscreve numa conta Azure gratuita, ele vem com $200 em crédito de serviço que você pode aplicar para uma subscrição de serviço de discurso pago, válido por até 30 dias. Os seus serviços Azure são desativados quando o seu crédito se esgota ou expira no final dos 30 dias. Para continuar a utilizar os serviços do Azure, deverá atualizar a conta. Para mais informações, consulte [Como atualizar a sua conta gratuita da Azure.](../../cost-management-billing/manage/upgrade-azure-subscription.md) 
>
> O serviço Discurso tem dois níveis de serviço: gratuito(f0) e subscrição(s0), que têm diferentes limitações e benefícios. Se utilizar o nível de serviço de fala gratuito e de baixo volume, pode manter esta subscrição gratuita mesmo depois de expirar o seu crédito gratuito de teste ou serviço. Para mais informações, consulte [os preços dos Serviços Cognitivos - Serviço de Fala](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="create-the-azure-resource"></a>Criar o recurso Azure

Para adicionar um recurso de serviço de discurso (nível gratuito ou pago) à sua conta Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com a sua conta Microsoft.

1. **Selecione Criar um recurso** na parte superior esquerda do portal. Se não vir **Criar um recurso,** pode sempre encontrá-lo selecionando o menu colapsado no canto superior esquerdo do ecrã.

1. Na **nova** janela, escreva "discurso" na caixa de pesquisa e prima ENTER.

1. Nos resultados da pesquisa, selecione **Speech**.

   ![resultados da pesquisa de discursos](media/index/speech-search.png)

1. Selecione **Criar,** em seguida:

   - Dê um nome único para o seu novo recurso. O nome ajuda-o a distinguir entre várias subscrições ligadas ao mesmo serviço.
   - Escolha a subscrição Azure a que o novo recurso está associado para determinar como as taxas são cobradas. Aqui está a introdução [de como criar uma subscrição do Azure](../../cost-management-billing/manage/create-subscription.md#create-a-subscription-in-the-azure-portal) no portal Azure.
   - Escolha a [região](regions.md) onde o recurso será utilizado. O Azure é uma plataforma global de nuvem que está geralmente disponível em muitas regiões do mundo. Para obter o melhor desempenho, selecione uma região mais próxima de si ou onde a sua aplicação é executado. As disponibilidades do serviço Speech variam de diferentes regiões. Certifique-se de que cria o seu recurso numa região apoiada. Consulte [o apoio da região aos serviços de fala.](./regions.md#speech-to-text-text-to-speech-and-translation)
   - Escolha um preço gratuito (F0) ou pago (S0). Para obter informações completas sobre preços e quotas de utilização para cada nível, **selecione Ver todos os detalhes dos preços** ou consulte os preços dos [serviços de fala](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/). Para limites de recursos, consulte [os Limites dos Serviços Cognitivos Azure.](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cognitive-services-limits)
   - Crie um novo grupo de recursos para esta subscrição do Discurso ou atribua a subscrição a um grupo de recursos existente. Os grupos de recursos ajudam-no a manter as suas várias subscrições Azure organizadas.
   - Selecione **Criar**. Isto irá levá-lo à visão geral da implementação e exibir mensagens de progresso de implantação.  
<!--
> [!NOTE]
> You can create an unlimited number of standard-tier subscriptions in one or multiple regions. However, you can create only one free-tier subscription. Model deployments on the free tier that remain unused for 7 days will be decommissioned automatically.
-->
Leva alguns momentos para implementar o seu novo recurso speech. 

### <a name="find-keys-and-region"></a>Encontre chaves e região

Para encontrar as chaves e a região de uma implantação concluída, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com a sua conta Microsoft.

2. Selecione **Todos os recursos** e selecione o nome do seu recurso Serviços Cognitivos.

3. No painel esquerdo, em **GESTÃO DE RECURSOS,** selecione **Keys e Endpoint**.

Cada subscrição tem duas chaves; pode utilizar qualquer uma das chaves na sua aplicação. Para copiar/colar uma chave para o seu editor de código ou outro local, selecione o botão de cópia ao lado de cada tecla, altere as janelas para colar o conteúdo da área de transferência para o local pretendido.

Além disso, copie o `LOCATION` valor, que é o seu ID da região (ex. `westus`, `westeurope` ) para chamadas SDK.

> [!IMPORTANT]
> Estas chaves de subscrição são usadas para aceder à sua API de Serviço Cognitivo. Não partilhem as chaves. Guarde-os de forma segura, por exemplo, utilizando o Cofre da Chave Azure. Recomendamos também a regeneração regular destas teclas. Apenas uma chave é necessária para fazer uma chamada da API. Ao regenerar a primeira chave, pode utilizar a segunda chave para o acesso continuado ao serviço.

## <a name="complete-a-quickstart"></a>Complete um arranque rápido

Oferecemos quickstarts nas linguagens de programação mais populares, cada uma projetada para lhe ensinar padrões básicos de design, e tê-lo a executar código em menos de 10 minutos. Consulte a seguinte lista para o arranque rápido de cada recurso.

* [Início rápido da conversão de voz em texto](get-started-speech-to-text.md)
* [Início rápido da conversão de texto em voz](get-started-text-to-speech.md)
* [Início rápido da tradução de voz](./get-started-speech-translation.md)
* [Início rápido do reconhecimento de intenção](quickstarts/intent-recognition.md)
* [Início rápido de reconhecimento de altifalantes](./get-started-speaker-recognition.md)

Depois de ter tido a oportunidade de começar com o serviço de Discurso, experimente os nossos tutoriais que lhe mostram como resolver vários cenários.

- [Tutorial: Reconhecer intenções do discurso com o Discurso SDK e LUIS, C #](how-to-recognize-intents-from-speech-csharp.md)
- [Tutorial: A voz permite o seu bot com o Speech SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Tutorial: Construir uma app Flask para traduzir texto, analisar sentimentos e sintetizar texto traduzido para a fala, REST](../translator/tutorial-build-flask-app-translation-synthesis.md?bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json%252c%2fen-us%2fazure%2fbread%2ftoc.json&toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json%252c%2fen-us%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json)

## <a name="get-sample-code"></a>Obter o código de exemplo

O código de amostra está disponível no GitHub para o serviço Speech. Estas amostras cobrem cenários comuns como ler áudio a partir de um ficheiro ou stream, reconhecimento contínuo e de tiro único, e trabalhar com modelos personalizados. Utilize estes links para visualizar amostras de SDK e REST:

- [Amostras de tradução de texto e fala (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Amostras de transcrição de lote (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Amostras de texto para falar (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Amostras de assistente de voz (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Personalize a sua experiência de fala

O serviço Speech funciona bem com modelos incorporados, no entanto, é melhor personalizar e afinar a experiência para o seu produto ou ambiente. As opções de personalização vão desde a sintonização de modelos acústicos a fontes de voz únicas para a sua marca.

Outros produtos oferecem modelos de fala sintonizados para fins específicos, como cuidados de saúde ou seguros, mas estão disponíveis para todos igualmente. A personalização no Azure Speech torna-se parte da sua vantagem competitiva *única* que não está disponível para qualquer outro utilizador ou cliente. Por outras palavras, os seus modelos são privados e personalizados apenas para o seu caso de uso.

| Serviço de Voz | Plataforma | Descrição |
| -------------- | -------- | ----------- |
| Conversão de Voz em Texto | [Discurso personalizado](https://aka.ms/customspeech) | Personalize os modelos de reconhecimento de voz às suas necessidades e dados disponíveis. Supere barreiras de reconhecimento de voz, tais como estilo de fala, vocabulário e ruído de fundo. |
| Conversão de Texto em Voz | [Voz Personalizada](https://aka.ms/customvoice) | Crie uma voz única reconhecível para as suas aplicações de Conversão de Texto em Voz com os dados de voz disponíveis. Pode afinar ainda mais as saídas de voz ajustando um conjunto de parâmetros de voz. |

## <a name="deploy-on-premises-using-docker-containers"></a>Implantar nas instalações utilizando contentores Docker

[Utilize recipientes de serviço de](speech-container-howto.md) voz para implantar funcionalidades de API no local. Estes recipientes Docker permitem-lhe aproximar o serviço dos seus dados por razões de conformidade, segurança ou outras razões operacionais. O serviço Discurso oferece os seguintes recipientes:

* Padrão Discurso-a-Texto
* Discurso-a-texto personalizado
* Texto-a-discurso padrão
* Texto-a-discurso neural
* Texto-a-fala personalizado (pré-visualização)
* Deteção de linguagem da fala (pré-visualização)

## <a name="reference-docs"></a>Documentos de referência

- [API de Voz](./speech-sdk.md)
- [SDK de Dispositivos de Voz](speech-devices-sdk.md)
- [REST API: Discurso-a-texto](rest-speech-to-text.md)
- [REST API: Texto-a-discurso](rest-text-to-speech.md)
- [REST API: Transcrição e personalização do lote](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Começar com discurso-a-texto](./get-started-speech-to-text.md) 
>  [Começar com texto-a-discurso](get-started-text-to-speech.md)