---
title: Stream ao vivo com codificadores no local que criam riachos multi-bitrate - Azure | Microsoft Docs
description: Este tópico descreve como configurar um canal que recebe um stream multi-bitrate ao vivo a partir de um codificador no local.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: d9f0912d-39ec-4c9c-817b-e5d9fcf1f7ea
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 316372f091833519f0479d07355d2845c82743b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014847"
---
# <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders"></a>Trabalhar com canais que recebem transmissão ao vivo multi-bitrate a partir de codificadores no local

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> A partir de 12 de maio de 2018, os canais ao vivo deixarão de suportar o protocolo de transmissão rtp/MPEG-2. Por favor, migrar de PROTOCOLOs de ingestão RTP/MPEG-2 para RTMP ou MP4 (Smooth Streaming).

## <a name="overview"></a>Descrição geral
Na Azure Media Services, um *canal* representa um oleoduto para o processamento de conteúdos de streaming em direto. Um canal recebe transmissões de entrada ao vivo de uma de duas maneiras:

* Um codificador ao vivo no local envia um stream rtmp multi-bitrate ou smooth streaming (MP4 fragmentado) para o canal que não está habilitado a realizar codificação ao vivo com serviços de media. Os fluxos ingeridos passam por canais sem qualquer processamento adicional. Este método *chama-se passagem.* Um codificador ao vivo também pode enviar um fluxo de bitrate único para um canal que não está habilitado para codificação ao vivo, mas não recomendamos isso. A Media Services entrega o fluxo aos clientes que o solicitam.

  > [!NOTE]
  > A utilização de um método de passagem é a forma mais económica de fazer streaming ao vivo.


* Um codificador ao vivo no local envia um fluxo de bitrate único para o canal que está habilitado a realizar codificação ao vivo com os Media Services num dos seguintes formatos: RTMP ou Smooth Streaming (MP4 fragmentado). Em seguida, o canal executa a codificação ao vivo do fluxo de bitrate único para um fluxo de vídeo multi-bitrate (adaptável). A Media Services entrega o fluxo aos clientes que o solicitam.

Começando com a versão 2.10 dos Media Services, quando criar um canal, pode especificar como pretende que o seu canal receba o fluxo de entrada. Também pode especificar se deseja que o canal realize a codificação ao vivo do seu fluxo. Tem duas opções:

* **Passe:** Especifique este valor se pretender utilizar um codificadora vivo no local que tenha um fluxo multi-bitrate (um fluxo de passagem) como saída. Neste caso, o fluxo de entrada passa para a saída sem qualquer codificação. Este é o comportamento de um canal antes da libertação das 2.10. Este artigo dá detalhes sobre o trabalho com canais deste tipo.
* **Codificação ao vivo**: Escolha este valor se planeia utilizar os Media Services para codificar o seu fluxo ao vivo de bitrate único para um fluxo multi-bitrate. Deixar um canal de codificação ao vivo num estado **em execução** incorre em taxas de faturação. Recomendamos que pare imediatamente os seus canais de funcionamento após o evento de streaming estar completo para evitar custos extra por hora. A Media Services entrega o fluxo aos clientes que o solicitam.

> [!NOTE]
> Este artigo discute atributos de canais que não estão habilitados a realizar codificação ao vivo. Para obter informações sobre o trabalho com canais que estejam habilitados a realizar codificação ao vivo, consulte [o streaming ao vivo utilizando o Azure Media Services para criar streams multi-bitrates](media-services-manage-live-encoder-enabled-channels.md).
>
>Para obter informações sobre os codificadores recomendados nas instalações, consulte [Recomendado nos codificadores de instalações.](media-services-recommended-encoders.md)

O diagrama seguinte representa um fluxo de trabalho em live-streaming que utiliza um codificadora vivo no local para ter streams RTMP multi-bitrate ou MP4 fragmentado (Smooth Streaming) como saída.

![Fluxo de trabalho em direto][live-overview]

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Cenário comum de streaming ao vivo
Os seguintes passos descrevem as tarefas envolvidas na criação de aplicações comuns de live-streaming.

1. Ligue uma câmara de vídeo a um computador. Iniciar e configurar um codificadora ao vivo no local que tenha um fluxo DE MPMP multi-bitrate ou fragmentado MP4 (Smooth Streaming) como saída. Para obter mais informações, consulte [Suporte RTMP dos Media Services do Azure e Codificadores em Direto](https://go.microsoft.com/fwlink/?LinkId=532824).

    Também pode executar este passo depois de criar o seu canal.
2. Crie e inicie um canal.

3. Recupere a URL de ingestão de canais.

    O codificader ao vivo usa o URL inger para enviar o fluxo para o canal.
4. Recupere o URL de pré-visualização do canal.

    Utilize este URL para verificar se o canal está a receber corretamente a transmissão em fluxo em direto.
5. Criar um programa.

    Quando utiliza o portal Azure, criar um programa também cria um ativo.

    Quando utilizar o .NET SDK ou REST, tem de criar um ativo e especificar para utilizar este ativo ao criar um programa.
6. Publique o ativo associado ao programa.   

    >[!NOTE]
    >Quando a sua conta Azure Media Services é criada, um ponto final de streaming **padrão** é adicionado à sua conta no estado **Stop.** O ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**.

7. Inicie o programa quando estiver pronto para começar a transmitir e arquivar.

8. Opcionalmente, o codificador em direto pode ser indicado para iniciar um anúncio. O anúncio é inserido na transmissão de saída.

9. Pare o programa sempre que pretender interromper a transmissão e arquivar o evento.

10. Elimine o programa (e elimine opcionalmente o ativo).     

## <a name="description-of-a-channel-and-its-related-components"></a><a id="channel"></a>Descrição de um canal e dos seus componentes relacionados
### <a name="channel-input-ingest-configurations"></a><a id="channel_input"></a>Configurações de entrada de canal (ingest)
#### <a name="ingest-streaming-protocol"></a><a id="ingest_protocols"></a>Ingeri protocolo de streaming
Os Media Services suportam a ingestão de feeds ao vivo utilizando MP4 fragmentado multi-bitrate e RTMP multi-bitrate como protocolos de streaming. Quando o protocolo de streaming de ingestão RTMP é selecionado, são criados dois pontos finais de ingestão (entrada) para o canal:

* **URL primário**: Especifica o URL totalmente qualificado do ponto final primário de ingestão de RTMP do canal.
* **URL secundário** (opcional): Especifica o URL totalmente qualificado do ponto final secundário de INGEST RTMP do canal.

Utilize o URL secundário se quiser melhorar a durabilidade e a tolerância à falha do seu fluxo de ingestão (bem como a insípição do codificadores e tolerância à falha), especialmente para os seguintes cenários:

- Codificação única empurrando duplo para URLs primários e secundários:

    O principal objetivo deste cenário é proporcionar mais resiliência às flutuações da rede e nervosismo. Alguns codificadores RTMP não lidam bem com as desconexões de rede. Quando uma desconexão de rede acontece, um codificadora pode parar de codificar e, em seguida, não enviar os dados tampão quando uma reconexão acontece. Isto causa descontinuidades e perda de dados. As desconexões de rede podem ocorrer devido a uma má rede ou manutenção do lado Azure. Os URLs primários/secundários reduzem os problemas de rede e fornecem um processo de atualização controlado. Cada vez que acontece uma desconexão de rede programada, os Serviços de Comunicação gerem as desconexões primárias e secundárias e proporcionam uma desconexão retardada entre os dois. Os codificadores têm então tempo para continuar a enviar dados e reconectar-se novamente. A ordem das desconexões pode ser aleatória, mas haverá sempre um atraso entre URLs primários/secundários ou secundários/primários. Neste cenário, o codificar continua a ser o único ponto de falha.

- Múltiplos codificadores, com cada codificadora empurrando para um ponto dedicado:

    Este cenário proporciona tanto codificação como ingerido redundância. Neste cenário, o encoder1 empurra para o URL primário, e o encoder2 empurra para o URL secundário. Quando um codificadores falha, o outro codificar pode continuar a enviar dados. A redundância de dados pode ser mantida porque os Serviços de Mídia não desligam os URLs primários e secundários ao mesmo tempo. Este cenário pressupõe que os codificadores estão sincronizados com o tempo e fornecem exatamente os mesmos dados.  

- Múltiplos codificadores empurrando duas vezes para URLs primários e secundários:

    Neste cenário, ambos os codificadores empurram os dados para URLs primários e secundários. Isto proporciona a melhor fiabilidade e tolerância a falhas, bem como redundância de dados. Este cenário pode tolerar falhas e desconexões de codificadores, mesmo que um codificadores deixe de funcionar. Assume que os codificadores estão sincronizados com o tempo e fornecem exatamente os mesmos dados.  

Para obter informações sobre codificadores ao vivo da RTMP, consulte [a Azure Media Services RTMP Support e Live Encoders](https://go.microsoft.com/fwlink/?LinkId=532824).

#### <a name="ingest-urls-endpoints"></a>Ingerir URLs (pontos finais)
Um canal fornece um ponto final de entrada (ingest URL) que especifica no codificante ao vivo, para que o codificante possa empurrar os fluxos para os seus canais.   

Pode obter os URLs ingeridos quando criar o canal. Para obter estes URLs, o canal não tem que estar no estado **de Corrida.** Quando estiver pronto para começar a empurrar dados para o canal, o canal deve estar no estado **de Funcionamento.** Depois de o canal começar a ingerir dados, pode visualizar o seu fluxo através do URL de pré-visualização.

Tem a opção de ingerir um stream fragmentado mp4 (Smooth Streaming) sobre uma ligação TLS. Para ingerir sobre TLS, certifique-se de atualizar o URL de ingestão para HTTPS. Atualmente, não é possível ingerir RTMP sobre TLS.

#### <a name="keyframe-interval"></a><a id="keyframe_interval"></a>Intervalo do quadro-chave
Quando se utiliza um codificadora ao vivo no local para gerar fluxo multi-bitrate, o intervalo do quadro-chave especifica a duração do grupo de imagens (GOP) como usado por esse codificadora externo. Depois de o canal receber este fluxo de entrada, pode entregar o seu fluxo ao vivo para aplicações de reprodução do cliente em qualquer um dos seguintes formatos: Smooth Streaming, Dynamic Adaptive Streaming over HTTP (DASH) e HTTP Live Streaming (HLS). Quando se faz streaming ao vivo, o HLS é sempre embalado dinamicamente. Por padrão, os Serviços de Media calculam automaticamente a relação de embalagem do segmento HLS (fragmentos por segmento) com base no intervalo de quadro-chave recebido do codificador ao vivo.

O quadro a seguir mostra como a duração do segmento é calculada:

| Intervalo do quadro-chave | Relação de embalagem do segmento HLS (FragmentsPerSegment) | Exemplo |
| --- | --- | --- |
| Menos ou igual a 3 segundos |3:1 |Se o KeyFrameInterval (ou GOP) for de 2 segundos, a relação de embalagem do segmento HLS padrão é de 3 para 1. Isto cria um segmento HLS de 6 segundos. |
| 3 a 5 segundos |2:1 |Se o KeyFrameInterval (ou GOP) for de 4 segundos, a relação de embalagem do segmento HLS padrão é de 2 para 1. Isto cria um segmento HLS de 8 segundos. |
| Maior que 5 segundos |1:1 |Se o KeyFrameInterval (ou GOP) for de 6 segundos, a relação de embalagem do segmento HLS padrão é de 1 a 1. Isto cria um segmento HLS de 6 segundos. |

Pode alterar a relação fragmentos por segmento configurando a saída do canal e definindo fragmentsPerSegment em ChannelOutputHls.

Também pode alterar o valor do intervalo do quadro de chaves definindo a propriedade KeyFrameInterval no ChannelInput. Se definir explicitamente o KeyFrameInterval, a relação de embalagem do segmento HLS FragmentsPerSegment é calculada através das regras descritas anteriormente.  

Se definir explicitamente tanto o KeyFrameInterval como o FragmentsPerSegment, os Serviços de Mídia utilizam os valores que definiu.

#### <a name="allowed-ip-addresses"></a>Endereços IP permitidos
Pode definir os endereços IP que podem publicar vídeo neste canal. Um endereço IP autorizado pode ser especificado como um dos seguintes:

* Um único endereço IP (por exemplo, 10.0.0.1)
* Uma gama IP que utiliza um endereço IP e uma máscara de sub-rede CIDR (por exemplo, 10.0.0.1/22)
* Uma gama DEP que utiliza um endereço IP e uma máscara de sub-rede decimal pontilhada (por exemplo, 10.0.0.1(255.255.252.0))

Se nenhum endereço IP for especificado e não houver definição de regra, então nenhum endereço IP é permitido. Para permitir um endereço IP, crie uma regra e defina 0.0.0.0/0.

### <a name="channel-preview"></a>Pré-visualização do canal
#### <a name="preview-urls"></a>URLs de pré-visualização
Os canais fornecem um ponto final de pré-visualização (URL de pré-visualização) que utiliza para visualizar e validar o seu fluxo antes de ser processado e entregue.

Pode obter o URL de pré-visualização quando criar o canal. Para obter o URL, o canal não tem que estar no estado **de Corrida.** Depois de o canal começar a ingerir dados, pode pré-visualizar o seu fluxo.

Atualmente, o fluxo de pré-visualização só pode ser entregue no formato MP4 fragmentado (Smooth Streaming), independentemente do tipo de entrada especificado. Pode utilizar o leitor [smooth streaming Health Monitor](https://playready.directtaps.net/smoothstreaming/) para testar o fluxo suave. Também pode utilizar um jogador que esteja hospedado no portal Azure para ver o seu stream.

#### <a name="allowed-ip-addresses"></a>Endereços IP permitidos
Pode definir os endereços IP que podem ligar-se ao ponto final de pré-visualização. Se não forem especificados endereços IP, qualquer endereço IP é permitido. Um endereço IP autorizado pode ser especificado como um dos seguintes:

* Um único endereço IP (por exemplo, 10.0.0.1)
* Uma gama IP que utiliza um endereço IP e uma máscara de sub-rede CIDR (por exemplo, 10.0.0.1/22)
* Uma gama DEP que utiliza um endereço IP e uma máscara de sub-rede decimal pontilhada (por exemplo, 10.0.0.1(255.255.252.0))

### <a name="channel-output"></a>Saída do canal
Para obter informações sobre a saída do canal, consulte a secção [de intervalo do Keyframe.](#keyframe_interval)

### <a name="channel-managed-programs"></a>Programas geridos por canais
Um canal está associado a programas que pode usar para controlar a publicação e armazenamento de segmentos em um live stream. Os canais gerem programas. A relação de canal e programa é semelhante aos meios tradicionais, onde um canal tem um fluxo constante de conteúdos e um programa é telescópio para algum evento cronometrado nesse canal.

Pode especificar o número de horas que pretenda manter o conteúdo gravado para o programa através da configuração da duração da **Janela de Arquivo**. Este valor pode ser definido a partir de um mínimo de 5 minutos até um máximo de 25 horas. O comprimento da janela de arquivo também dita o número máximo de tempo que os clientes podem procurar no tempo a partir da posição ao vivo atual. Os programas podem ser executados durante o período de tempo especificado, mas o conteúdo que se situe atrás da duração da janela é continuamente descartado. O valor desta propriedade também determina durante quanto tempo os manifestos dos clientes podem aumentar.

Cada programa está associado a um ativo que armazena o conteúdo transmitido. Um ativo é mapeado para um recipiente de blocos na conta de armazenamento Azure, e os ficheiros no ativo são armazenados como bolhas nesse recipiente. Para publicar o programa para que os seus clientes possam ver o stream, tem de criar um localizador OnDemand para o ativo associado. Pode utilizar este localizador para construir um URL de streaming que pode fornecer aos seus clientes.

Um canal suporta até três programas em execução simultânea, para que possa criar múltiplos arquivos do mesmo fluxo de entrada. Pode publicar e arquivar diferentes partes de um evento conforme necessário. Por exemplo, imagine que o seu requisito de negócio é arquivar 6 horas de um programa, mas transmitir apenas os últimos 10 minutos. Para tal, tem de criar dois programas em execução em simultâneo. Um programa está programado para arquivar seis horas do evento, mas o programa não é publicado. O outro programa está programado para arquivar por 10 minutos, e este programa é publicado.

Não deve reutilizar programas existentes para novos eventos. Em vez disso, crie um novo programa para cada evento. Inicie o programa quando estiver pronto para começar a transmitir e arquivar. Pare o programa sempre que pretender interromper a transmissão e arquivar o evento.

Para eliminar o conteúdo arquivado, pare e elimine o programa e, em seguida, elimine o ativo associado. Um ativo não pode ser eliminado se um programa o utilizar. O programa deve ser apagado primeiro.

Mesmo depois de parar e apagar o programa, os utilizadores podem transmitir o conteúdo arquivado como um vídeo a pedido, até que apague o ativo. Se pretender reter o conteúdo arquivado mas não o tiver disponível para streaming, elimine o localizador de streaming.

## <a name="channel-states-and-billing"></a><a id="states"></a>Estados do canal e faturação
Os valores possíveis para o estado atual de um canal incluem:

* **Parado**: Este é o estado inicial do canal após a sua criação. Neste estado, as propriedades do canal podem ser atualizadas, mas o streaming não é permitido.
* **A partir de** início: O canal está a ser iniciado. Não são permitidas atualizações ou streamings durante este estado. Se ocorrer um erro, o canal volta ao estado **Stop.**
* **Funcionando**: O canal pode processar streams ao vivo.
* **Paragem:** O canal está a ser parado. Não são permitidas atualizações ou streamings durante este estado.
* **Eliminação:** O canal está a ser apagado. Não são permitidas atualizações ou streamings durante este estado.

A tabela seguinte mostra como os estados do canal mapeiam para o modo de faturação.

| Estado do canal | Indicadores de UI do Portal | Cobrado? |
| --- | --- | --- |
| **A iniciar** |**A iniciar** |Não (estado transitório) |
| **Em Execução** |**Pronto** (sem programas de execução)<p><p>ou<p>**Streaming** (pelo menos um programa em execução) |Yes |
| **A parar** |**A parar** |Não (estado transitório) |
| **Parada** |**Parada** |No |

## <a name="closed-captioning-and-ad-insertion"></a><a id="cc_and_ads"></a>Legenda fechada e inserção de anúncios
A tabela a seguir demonstra normas suportadas para legendas fechadas e inserção de anúncios.

| Standard | Notas |
| --- | --- |
| CEA-708 e EIA-608 (708/608) |O CEA-708 e o EIA-608 são normas de legendagem fechada para os Estados Unidos e Canadá.<p><p>Atualmente, a legendagem só é suportada se for transportada no fluxo de entrada codificado. Você precisa usar um codificadores de mídia ao vivo que pode inserir 608 ou 708 legendas no fluxo codificado que é enviado para os Media Services. Os Serviços de Comunicação Social entregam o conteúdo com legendas inseridas aos seus espectadores. |
| TTML dentro .ismt (faixas de texto de streaming suave) |A embalagem dinâmica dos Media Services permite aos seus clientes transmitir conteúdo em qualquer um dos seguintes formatos: DASH, HLS ou Smooth Streaming. No entanto, se ingerir MP4 fragmentado (Smooth Streaming) com legendas dentro de .ismt (faixas de texto de streaming suave), pode entregar o stream apenas a clientes Smooth Streaming. |
| SCTE-35 |O SCTE-35 é um sistema de sinalização digital que é usado para introduzir publicidade. Os recetores a jusante utilizam o sinal para encaixar a publicidade no fluxo durante o tempo atribuído. O SCTE-35 deve ser enviado como uma faixa escassa no fluxo de entrada.<p><p>Atualmente, o único formato de fluxo de entrada suportado que transporta sinais de anúncio é MP4 fragmentado (Smooth Streaming). O único formato de saída suportado é também o Smooth Streaming. |

## <a name="considerations"></a><a id="considerations"></a>Considerações
Quando se usa um codificadora ao vivo no local para enviar um fluxo multi-bitrate para um canal, aplicam-se os seguintes constrangimentos:

* Certifique-se de que tem conectividade de Internet gratuita suficiente para enviar dados para os pontos de ingestão.
* A utilização de um URL de ingestão secundária requer largura de banda adicional.
* O fluxo multi-bitrate de entrada pode ter um máximo de 10 níveis de qualidade de vídeo (camadas) e um máximo de 5 faixas de áudio.
* A média mais alta para qualquer um dos níveis de qualidade de vídeo deve ser inferior a 10 Mbps.
* O agregado dos bitrates médios para todos os streams de vídeo e áudio deve ser inferior a 25 Mbps.
* Não é possível alterar o protocolo de entrada enquanto o canal ou os programas associados estiverem em execução. Se necessitar de protocolos diferentes, deve criar canais separados para cada protocolo de entrada.
* Podes ingerir uma única bitrate no teu canal. Mas como o canal não processa o stream, as aplicações do cliente também receberão um único fluxo bitrate. (Não recomendamos esta opção.)

Aqui estão outras considerações relacionadas com o trabalho com canais e componentes relacionados:

* Sempre que reconfigurar o codificar ao vivo, ligue para o método **Reset** no canal. Antes de reiniciar o canal, tem de parar o programa. Depois de reiniciar o canal, reinicie o programa.

  > [!NOTE]
  > Quando reinicia o programa, tem de o associar a um novo ativo e criar um novo localizador. 
  
* Um canal só pode ser parado quando está no estado **de Execução** e todos os programas no canal foram parados.
* Por predefinição, pode adicionar apenas cinco canais à sua conta de Media Services. Para mais informações, consulte [Quotas e limitações.](media-services-quotas-and-limitations.md)
* Só é cobrado quando o seu canal está no estado **de Running.** Para mais informações, consulte os estados do Canal da Mancha e a secção [de faturação.](media-services-live-streaming-with-onprem-encoders.md#states)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="suggestions-and-feedback"></a>Sugestões e Feedback

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tópicos relacionados
[Recomendado em instalações codificadores](media-services-recommended-encoders.md)

[Azure Media Services fragmentado MP4 vive especificação](../media-services-fmp4-live-ingest-overview.md)

[Visão geral dos Serviços de Media Azure e cenários comuns](media-services-overview.md)

[Conceitos de Serviços de Mídia](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
