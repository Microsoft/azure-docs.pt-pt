---
title: Stream live with in-premirs that create multibitrate streams - Azure [ Microsoft Docs
description: Este tópico descreve como configurar um canal que recebe um live stream multibitrate a partir de um codificador no local.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: d9f0912d-39ec-4c9c-817b-e5d9fcf1f7ea
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: c0d19d68d016a47762fb5d2646ea6ccf74d3ef75
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476549"
---
# <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders"></a>Trabalhar com canais que recebem fluxo ao vivo multibitado a partir de codificadores no local

> [!NOTE]
> A partir de 12 de maio de 2018, os canais ao vivo deixarão de apoiar o protocolo de transporte sintetizador da RTP/MPEG-2. Por favor, emigrar de PROTOCOLOS de ingerir RTP/MPEG-2 para RTMP ou MP4 fragmentados (Smooth Streaming).

## <a name="overview"></a>Descrição geral
Na Azure Media Services, um *canal* representa um oleoduto para o processamento de conteúdos em streaming. Um canal recebe streams de entrada ao vivo de uma de duas maneiras:

* Um codificador ao vivo no local envia um fluxo de RTMP ou Smooth Streaming (MP4 fragmentado) para o canal que não está habilitado a realizar codificação ao vivo com os Media Services. Os fluxos ingeridos passam por canais sem qualquer processamento adicional. Este método *chama-se pass-through*. Um codificador ao vivo também pode enviar um fluxo de bitrate único para um canal que não está habilitado para codificação ao vivo, mas não recomendamos isso. A Media Services entrega o stream aos clientes que o solicitam.

  > [!NOTE]
  > Usar um método de passagem é a forma mais económica de fazer streaming ao vivo.


* Um codificador ao vivo no local envia um fluxo de bitrate único para o canal que está habilitado a realizar codificação ao vivo com os Media Services num dos seguintes formatos: RTMP ou Smooth Streaming (MP4 fragmentado). Em seguida, o canal executa a codificação ao vivo do fluxo de bitrate único que chega a um fluxo de vídeo multibitável (adaptável). A Media Services entrega o stream aos clientes que o solicitam.

A partir do lançamento do Media Services 2.10, quando criar um canal, pode especificar como pretende que o seu canal receba o fluxo de entrada. Também pode especificar se pretende que o canal realize a codificação ao vivo do seu fluxo. Tem duas opções:

* **Passe através**: Especifique este valor se planeia utilizar um codificador ao vivo no local que tenha um fluxo multibitulado (um fluxo de passagem) como saída. Neste caso, o fluxo de entrada passa para a saída sem qualquer codificação. Este é o comportamento de um canal antes do lançamento das 2:10. Este artigo dá detalhes sobre trabalhar com canais deste tipo.
* **Live Encoding**: Escolha este valor se planeia utilizar os Serviços de Media para codificar o seu fluxo ao vivo de bitrate único para um fluxo multibitante. Deixar um canal de codificação ao vivo num estado **de execução** incorre em acusações de faturação. Recomendamos que pare imediatamente os seus canais de funcionamento após o seu evento de streaming estar completo para evitar encargos extra por hora. A Media Services entrega o stream aos clientes que o solicitam.

> [!NOTE]
> Este artigo discute atributos de canais que não estão habilitados a realizar codificação ao vivo. Para obter informações sobre trabalhar com canais que estejam habilitados a realizar codificação ao vivo, consulte o streaming ao vivo utilizando o [Azure Media Services para criar streams multibitáveis.](media-services-manage-live-encoder-enabled-channels.md)
>
>Para obter informações sobre os codificadores recomendados no local, consulte [recomendado no local codificadores](media-services-recommended-encoders.md).

O diagrama seguinte representa um fluxo de trabalho em transmissão ao vivo que utiliza um codificador vivo no local para ter fluxos de RTMP multibitantes ou MP4 fragmentados (Smooth Streaming) como saída.

![Fluxo de trabalho em direto][live-overview]

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Cenário comum de streaming em direto
Os seguintes passos descrevem tarefas envolvidas na criação de aplicações comuns de streaming.

1. Ligue uma câmara de vídeo a um computador. Inicie e configure um codificador ao vivo no local que tenha um fluxo de RTMP multibitável ou MP4 fragmentado (Smooth Streaming) como saída. Para obter mais informações, consulte [Suporte RTMP dos Media Services do Azure e Codificadores em Direto](https://go.microsoft.com/fwlink/?LinkId=532824).

    Também pode executar este passo depois de criar o seu canal.
2. Crie e inicie um canal.

3. Recupere o URL de ingerir o canal.

    O codificador ao vivo utiliza o URL ingerir para enviar o fluxo para o canal.
4. Recupere o URL de pré-visualização do canal.

    Utilize este URL para verificar se o canal está a receber corretamente a transmissão em fluxo em direto.
5. Criar um programa.

    Quando se utiliza o portal Azure, criar um programa também cria um ativo.

    Quando utilizar o .NET SDK ou REST, precisa de criar um ativo e especificar para utilizar este ativo ao criar um programa.
6. Publique o ativo que está associado ao programa.   

    >[!NOTE]
    >Quando a sua conta Azure Media Services é criada, um ponto final de streaming **padrão** é adicionado à sua conta no estado **Deter.** O ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**.

7. Inicie o programa quando estiver pronto para começar a transmitir e arquivar.

8. Opcionalmente, o codificador em direto pode ser indicado para iniciar um anúncio. O anúncio é inserido na transmissão de saída.

9. Pare o programa sempre que pretender interromper a transmissão e arquivar o evento.

10. Eliminar o programa (e excluir opcionalmente o ativo).     

## <a name="description-of-a-channel-and-its-related-components"></a><a id="channel"></a>Descrição de um canal e dos seus componentes conexos
### <a name="channel-input-ingest-configurations"></a><a id="channel_input"></a>Configurações de entrada de canal (ingestão)
#### <a name="ingest-streaming-protocol"></a><a id="ingest_protocols"></a>Protocolo de streaming ingest
A Media Services apoia a ingestão de feeds ao vivo utilizando mp4 fragmentados multibitados e RTMP multibitados como protocolos de streaming. Quando o protocolo de streaming de intenção RTMP é selecionado, são criados dois pontos finais de ingerir (entrada) para o canal:

* **URL primário**: Especifica o URL totalmente qualificado do ponto final primário de ingestão RTMP do canal.
* **URL secundário** (opcional): Especifica o URL totalmente qualificado do ponto final de ingestão rtmp secundário do canal.

Utilize o URL secundário se pretender melhorar a durabilidade e a tolerância à falha do seu fluxo de ingestão (bem como a falha do codificador e a tolerância à falha), especialmente para os seguintes cenários:

- Um único codificador duplo empurrando para URLs primários e secundários:

    O principal objetivo deste cenário é fornecer mais resiliência às flutuações da rede e nervosismo. Alguns codificadores DE RTMP não lidam bem com as desconexões da rede. Quando uma desconexão de rede acontece, um codificador pode parar de codificar e, em seguida, não enviar os dados tampão quando uma religação acontece. Isto causa descontinuidades e perda de dados. As desconexões de rede podem ocorrer devido a uma má rede ou manutenção no lado Azure. Os URLs primários/secundários reduzem os problemas de rede e fornecem um processo de atualização controlado. De cada vez que uma rede regular desliga, a Media Services gere as desconexões primárias e secundárias e proporciona uma desconexão retardada entre os dois. Os codificadores têm então tempo para continuar a enviar dados e voltar a ligar-se. A ordem das desconexões pode ser aleatória, mas haverá sempre um atraso entre URLs primários/secundários ou secundários/primários. Neste cenário, o codificador continua a ser o único ponto de falha.

- Vários codificadores, com cada codificador a empurrar para um ponto dedicado:

    Este cenário proporciona tanto o cemcódigo como a redundância. Neste cenário, o coder1 empurra para o URL primário, e codificar2 empurra para o URL secundário. Quando um codificador falha, o outro codificador pode continuar a enviar dados. A redundância de dados pode ser mantida porque os Serviços de Media não desligam urls primários e secundários ao mesmo tempo. Este cenário pressupõe que os codificadores são sincronizados no tempo e fornecem exatamente os mesmos dados.  

- Vários codificadores que empurram duas vezes para URLs primários e secundários:

    Neste cenário, ambos os codificadores empurram dados para URLs primários e secundários. Isto proporciona a melhor fiabilidade e tolerância a falhas, bem como a redundância de dados. Este cenário pode tolerar falhas e desconexões codificadoras, mesmo que um codificador deixe de funcionar. Assume que os codificadores são sincronizados no tempo e fornecem exatamente os mesmos dados.  

Para obter informações sobre codificadores ao vivo da RTMP, consulte [o Suporte RTMP dos Serviços de Mídia Azure e codificadores ao vivo](https://go.microsoft.com/fwlink/?LinkId=532824).

#### <a name="ingest-urls-endpoints"></a>URLs ingerir (pontos finais)
Um canal fornece um ponto final de entrada (URL ingerir) que especifica no codificador ao vivo, para que o codificador possa empurrar os streams para os seus canais.   

Pode obter os URLs ingerir quando criar o canal. Para obter estes URLs, o canal não tem que estar no estado **de Execução.** Quando estiver pronto para começar a empurrar dados para o canal, o canal deve estar no estado **de Execução.** Depois de o canal começar a ingerir dados, pode pré-visualizar o seu fluxo através do URL de pré-visualização.

Tem a opção de ingerir um fluxo ao vivo de MP4 fragmentado (Smooth Streaming) sobre uma ligação TLS. Para ingerir sobre tLS, certifique-se de atualizar o URL ingerir para HTTPS. Atualmente, não pode ingerir RTMP sobre TLS.

#### <a name="keyframe-interval"></a><a id="keyframe_interval"></a>Intervalo do quadro de chaves
Quando se está a utilizar um codificador ao vivo no local para gerar fluxo multibitado, o intervalo do quadro-chave especifica a duração do grupo de imagens (GOP) como utilizado por esse codificador externo. Depois de o canal receber este fluxo de entrada, pode entregar o seu live stream às aplicações de reprodução do cliente em qualquer um dos seguintes formatos: Smooth Streaming, Dynamic Adaptive Streaming over HTTP (DASH) e HTTP Live Streaming (HLS). Quando se está a fazer streaming ao vivo, o HLS é sempre embalado dinamicamente. Por padrão, os Serviços de Media calculam automaticamente a relação de embalagem do segmento HLS (fragmentos por segmento) com base no intervalo do quadro-chave que é recebido do codificador ao vivo.

O quadro seguinte mostra como a duração do segmento é calculada:

| Intervalo do quadro de chaves | Rácio de embalagem de segmento HLS (FragmentsPerSegment) | Exemplo |
| --- | --- | --- |
| Menos ou igual a 3 segundos |3:1 |Se o Intervalo do KeyFrame (ou GOP) for de 2 segundos, a relação padrão de embalagem do segmento HLS é de 3 a 1. Isto cria um segmento HLS de 6 segundos. |
| 3 a 5 segundos |2:1 |Se o Intervalo do KeyFrame (ou GOP) for de 4 segundos, a relação padrão de embalagem do segmento HLS é de 2 a 1. Isto cria um segmento HLS de 8 segundos. |
| Mais de 5 segundos |1:1 |Se o Intervalo do KeyFrame (ou GOP) for de 6 segundos, a relação padrão de embalagem do segmento HLS é de 1 a 1. Isto cria um segmento HLS de 6 segundos. |

Pode alterar a relação fragmentos por segmento configurando a saída do canal e definindo fragmentsPerSegment em ChannelOutputHls.

Também pode alterar o valor do intervalo do quadro-chave, definindo a propriedade KeyFrameInterval no ChannelInput. Se definir explicitamente o KeyFrameInterval, a relação de embalagem do segmento HLS FragmentsPerSegment é calculada através das regras descritas anteriormente.  

Se definir explicitamente o KeyFrameInterval e o FragmentsPerSegment, o Media Services utiliza os valores que definiu.

#### <a name="allowed-ip-addresses"></a>Endereços IP permitidos
Pode definir os endereços IP que podem publicar vídeo para este canal. Um endereço IP permitido pode ser especificado como um dos seguintes:

* Um único endereço IP (por exemplo, 10.0.0.1)
* Uma gama IP que utiliza um endereço IP e uma máscara de sub-rede CIDR (por exemplo, 10.0.0.1/22)
* Uma gama IP que utilize um endereço IP e uma máscara de sub-rede decimal pontilhada (por exemplo, 10.0.0.1 (255.255.252.0))

Se não forem especificados endereços IP e não houver definição de regra, então não é permitido nenhum endereço IP. Para permitir um endereço IP, crie uma regra e defina 0.0.0.0/0.

### <a name="channel-preview"></a>Pré-visualização do canal
#### <a name="preview-urls"></a>URLs de pré-visualização
Os canais fornecem um ponto final de pré-visualização (URL de pré-visualização) que utiliza para pré-visualizar e validar o seu fluxo antes de continuar a processar e a entregar.

Pode obter o URL de pré-visualização quando criar o canal. Para obter o URL, o canal não tem que estar no estado **de Execução.** Depois de o canal começar a ingerir dados, pode pré-visualizar o seu fluxo.

Atualmente, o fluxo de pré-visualização só pode ser entregue em formato MP4 fragmentado (Smooth Streaming), independentemente do tipo de entrada especificado. Pode utilizar o leitor [de Monitorização](https://playready.directtaps.net/smoothstreaming/) de Saúde de Streaming Suave para testar o fluxo suave. Também pode utilizar um jogador hospedado no portal Azure para ver o seu fluxo.

#### <a name="allowed-ip-addresses"></a>Endereços IP permitidos
Pode definir os endereços IP que podem ligar-se ao ponto final de pré-visualização. Se não forem especificados endereços IP, é permitido qualquer endereço IP. Um endereço IP permitido pode ser especificado como um dos seguintes:

* Um único endereço IP (por exemplo, 10.0.0.1)
* Uma gama IP que utiliza um endereço IP e uma máscara de sub-rede CIDR (por exemplo, 10.0.0.1/22)
* Uma gama IP que utilize um endereço IP e uma máscara de sub-rede decimal pontilhada (por exemplo, 10.0.0.1 (255.255.252.0))

### <a name="channel-output"></a>Saída do canal
Para obter informações sobre a saída do canal, consulte a secção de [intervalo sinuosa.](#keyframe_interval)

### <a name="channel-managed-programs"></a>Programas geridos por canais
Um canal está associado a programas que pode usar para controlar a publicação e armazenamento de segmentos em live stream. Os canais gerem programas. A relação canal e programa é semelhante aos meios tradicionais, onde um canal tem um fluxo constante de conteúdos e um programa é traçado para algum evento cronometrado nesse canal.

Pode especificar o número de horas que pretenda manter o conteúdo gravado para o programa através da configuração da duração da **Janela de Arquivo**. Este valor pode ser definido a partir de um mínimo de 5 minutos até um máximo de 25 horas. O comprimento da janela de arquivo também dita o número máximo de tempo que os clientes podem procurar no tempo a partir da posição atual ao vivo. Os programas podem ser executados durante o período de tempo especificado, mas o conteúdo que se situe atrás da duração da janela é continuamente descartado. O valor desta propriedade também determina durante quanto tempo os manifestos dos clientes podem aumentar.

Cada programa está associado a um ativo que armazena o conteúdo transmitido. Um ativo é mapeado para um contentor de blocos na conta de armazenamento Azure, e os ficheiros do ativo são armazenados como bolhas nesse contentor. Para publicar o programa para que os seus clientes possam ver o stream, você deve criar um localizador OnDemand para o ativo associado. Pode utilizar este localizador para construir um URL de streaming que pode fornecer aos seus clientes.

Um canal suporta até três programas em execução simultaneamente, para que possa criar vários arquivos do mesmo fluxo de entrada. Pode publicar e arquivar diferentes partes de um evento, se necessário. Por exemplo, imagine que o seu requisito de negócio é arquivar 6 horas de um programa, mas transmitir apenas os últimos 10 minutos. Para tal, tem de criar dois programas em execução em simultâneo. Um programa está programado para arquivar seis horas do evento, mas o programa não é publicado. O outro programa está definido para ser arquivado por 10 minutos, e este programa é publicado.

Não deve reutilizar programas existentes para novos eventos. Em vez disso, crie um novo programa para cada evento. Inicie o programa quando estiver pronto para começar a transmitir e arquivar. Pare o programa sempre que pretender interromper a transmissão e arquivar o evento.

Para eliminar o conteúdo arquivado, pare e elimine o programa e, em seguida, elimine o ativo associado. Um ativo não pode ser eliminado se um programa o utilizar. O programa deve ser eliminado primeiro.

Mesmo depois de parar e apagar o programa, os utilizadores podem transmitir o seu conteúdo arquivado como um vídeo a pedido, até eliminar o ativo. Se pretender reter o conteúdo arquivado, mas não o tiver disponível para streaming, elimine o localizador de streaming.

## <a name="channel-states-and-billing"></a><a id="states"></a>Estados do canal e faturação
Os valores possíveis para o estado atual de um canal incluem:

* **Stop**: Este é o estado inicial do canal após a sua criação. Neste estado, as propriedades do canal podem ser atualizadas, mas o streaming não é permitido.
* **Início**: O canal está a ser iniciado. Não são permitidas atualizações ou streaming durante este estado. Se ocorrer um erro, o canal regressa ao estado **de parada.**
* **Funcionamento**: O canal pode processar transmissões ao vivo.
* **Paragem**: O canal está a ser parado. Não são permitidas atualizações ou streaming durante este estado.
* **Eliminação**: O canal está a ser apagado. Não são permitidas atualizações ou streaming durante este estado.

A tabela que se segue mostra como os estados do canal mapeiam para o modo de faturação.

| Estado do canal | Indicadores do Portal UI | Faturado? |
| --- | --- | --- |
| **Início** |**Início** |Não (estado transitório) |
| **A executar** |**Pronto** (sem programas de execução)<p><p>ou<p>**Streaming** (pelo menos um programa de execução) |Sim |
| **A parar** |**A parar** |Não (estado transitório) |
| **Parada** |**Parada** |Não |

## <a name="closed-captioning-and-ad-insertion"></a><a id="cc_and_ads"></a>Legendagem fechada e inserção de anúncios
A tabela seguinte demonstra normas suportadas para legendagem fechada e inserção de anúncios.

| Standard | Notas |
| --- | --- |
| CEA-708 e EIA-608 (708/608) |CeA-708 e EIA-608 são normas de legendagem fechada para os Estados Unidos e Canadá.<p><p>Atualmente, a legendação só é suportada se transportada no fluxo de entrada codificado. Você precisa usar um codificador de mídia ao vivo que pode inserir 608 ou 708 legendas no fluxo codificado que é enviado para os Media Services. A Media Services entrega o conteúdo com legendas inseridas aos seus telespectadores. |
| TTML dentro de .ismt (faixas de texto de streaming suave) |A embalagem dinâmica dos Media Services permite aos seus clientes transmitir conteúdo em qualquer um dos seguintes formatos: DASH, HLS ou Smooth Streaming. No entanto, se ingerir MP4 fragmentado (Smooth Streaming) com legendas dentro de .ismt (faixas de texto de streaming suave), pode entregar o stream apenas a clientes De Streaming Suave. |
| SCTE-35 |O SCTE-35 é um sistema de sinalização digital que é usado para evitar a inserção publicitária. Os recetores a jusante utilizam o sinal para encaixar a publicidade no fluxo durante o tempo atribuído. O SCTE-35 deve ser enviado como uma faixa escassa no fluxo de entrada.<p><p>Atualmente, o único formato de fluxo de entrada suportado que transporta sinais de anúncio é mp4 fragmentado (Smooth Streaming). O único formato de saída suportado é também o Smooth Streaming. |

## <a name="considerations"></a><a id="considerations"></a>Considerações
Quando se está a usar um codificador ao vivo no local para enviar um fluxo multibitado para um canal, aplicam-se os seguintes constrangimentos:

* Certifique-se de que tem conectividade gratuita com a Internet para enviar dados para os pontos ingerir.
* A utilização de um URL de ingerir secundário requer largura de banda adicional.
* O fluxo multibitulado pode ter um máximo de 10 níveis de qualidade de vídeo (camadas) e um máximo de 5 faixas de áudio.
* A bitrate média mais alta para qualquer um dos níveis de qualidade de vídeo deve ser inferior a 10 Mbps.
* O agregado das bitrates médias para todos os streams de vídeo e áudio deve ser inferior a 25 Mbps.
* Não é possível alterar o protocolo de entrada enquanto o canal ou os seus programas associados estiverem em execução. Se necessitar de protocolos diferentes, deve criar canais separados para cada protocolo de entrada.
* Pode ingerir um único bitrate no seu canal. Mas como o canal não processa o fluxo, as aplicações do cliente também receberão um único fluxo bitrate. (Não recomendamos esta opção.)

Eis outras considerações relacionadas com o trabalho com canais e componentes conexos:

* Sempre que reconfigurar o codificador ao vivo, ligue para o método **Reset** no canal. Antes de redefinir o canal, tem de parar o programa. Depois de reiniciar o canal, reinicie o programa.

  > [!NOTE]
  > Quando reiniciar o programa, precisa associá-lo a um novo ativo e criar um novo localizador. 
  
* Um canal só pode ser parado quando estiver no estado **de Execução** e todos os programas do canal tiverem sido parados.
* Por predefinição, pode adicionar apenas cinco canais à sua conta de Serviços de Media. Para mais informações, consulte [Quotas e limitações.](media-services-quotas-and-limitations.md)
* Só é cobrado quando o seu canal estiver no estado **de Execução.** Para mais informações, consulte os estados do Canal e a secção [de faturação.](media-services-live-streaming-with-onprem-encoders.md#states)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="feedback"></a>Comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tópicos relacionados
[Recomendado no local codificadores](media-services-recommended-encoders.md)

[Azure Media Services fragmentado MP4 vive especificação ingerir](../media-services-fmp4-live-ingest-overview.md)

[Visão geral dos Serviços de Mídia Azure e cenários comuns](media-services-overview.md)

[Conceitos de Serviços de Media](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
