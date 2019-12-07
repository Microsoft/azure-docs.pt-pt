---
title: Transmita ao vivo com codificadores locais que criam fluxos de múltiplas taxas de bits – Azure | Microsoft Docs
description: Este tópico descreve como configurar um canal que recebe uma transmissão ao vivo com múltiplas taxas de bits de um codificador local.
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
ms.openlocfilehash: f6366f162cb09898b694b14440718401c57c0adf
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74887106"
---
# <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders"></a>Trabalhando com canais que recebem transmissão ao vivo de múltiplas taxas de bits de codificadores locais

> [!NOTE]
> A partir de 12 de maio de 2018, os canais ao vivo não serão mais compatíveis com o protocolo de ingestão de fluxo de transporte RTP/MPEG-2. Migre do RTP/MPEG-2 para protocolos de ingestão RTMP ou MP4 fragmentado (Smooth Streaming).

## <a name="overview"></a>Visão geral
Nos serviços de mídia do Azure, um *canal* representa um pipeline para o processamento de conteúdo de streaming ao vivo. Um canal recebe fluxos de entrada ao vivo de uma das duas maneiras:

* Um codificador ao vivo local envia um fluxo RTMP de múltiplas taxas de bits ou Smooth Streaming (MP4 fragmentado) para o canal que não está habilitado para executar a codificação ativa com os serviços de mídia. Os fluxos ingeridos passam por canais sem nenhum processamento adicional. Esse método é chamado *de passagem*. Um codificador ao vivo também pode enviar um fluxo de taxa de bits única para um canal que não está habilitado para codificação ativa, mas não recomendamos isso. Os serviços de mídia fornecem o fluxo para clientes que o solicitam.

  > [!NOTE]
  > Usar um método de passagem é a maneira mais econômica de realizar a transmissão ao vivo.


* Um codificador ao vivo local envia um fluxo de taxa de bits única para o canal que está habilitado para executar a codificação ativa com os serviços de mídia em um dos seguintes formatos: RTMP ou Smooth Streaming (MP4 fragmentado). Em seguida, o canal executa a codificação ativa da transmissão de taxa de bits única de entrada para um fluxo de vídeo com múltiplas taxas de bits (adaptável). Os serviços de mídia fornecem o fluxo para clientes que o solicitam.

A partir da versão 2,10 dos serviços de mídia, ao criar um canal, você pode especificar como deseja que seu canal receba o fluxo de entrada. Você também pode especificar se deseja que o canal execute a codificação ativa de seu fluxo. Tem duas opções:

* **Passagem**: Especifique esse valor se você planeja usar um codificador ao vivo local que tenha um fluxo de múltiplas taxas de bits (um fluxo de passagem) como saída. Nesse caso, o fluxo de entrada passa para a saída sem qualquer codificação. Esse é o comportamento de um canal antes da versão 2,10. Este artigo fornece detalhes sobre como trabalhar com canais deste tipo.
* **Codificação ativa**: escolha esse valor se você planeja usar os serviços de mídia para codificar sua transmissão ao vivo de taxa de bits única para um fluxo de múltiplas taxas de bits. Deixar um canal de codificação ativo em um estado de **execução** incorre em encargos de cobrança. Recomendamos que você pare imediatamente seus canais em execução depois que o evento de transmissão ao vivo for concluído para evitar cobranças por hora extra. Os serviços de mídia fornecem o fluxo para clientes que o solicitam.

> [!NOTE]
> Este artigo discute os atributos de canais que não estão habilitados para executar a codificação ativa. Para obter informações sobre como trabalhar com canais habilitados para executar a codificação ativa, consulte [transmissão ao vivo usando os serviços de mídia do Azure para criar fluxos de múltiplas taxas](media-services-manage-live-encoder-enabled-channels.md)de bits.
>
>Para obter informações sobre codificadores locais recomendados, consulte [codificadores locais recomendados](media-services-recommended-encoders.md).

O diagrama a seguir representa um fluxo de trabalho de transmissão ao vivo que usa um codificador ao vivo local para ter fluxos de múltiplas taxas de bits RTMP ou MP4 fragmentados (Smooth Streaming) como saída.

![Fluxo de trabalho em direto][live-overview]

## <a id="scenario"></a>Cenário comum de streaming ao vivo
As etapas a seguir descrevem as tarefas envolvidas na criação de aplicativos comuns de streaming ao vivo.

1. Ligue uma câmara de vídeo a um computador. Inicie e configure um codificador ao vivo local que tenha um fluxo RTMP de múltiplas taxas de bits ou MP4 fragmentado (Smooth Streaming) como saída. Para obter mais informações, consulte [Suporte RTMP dos Media Services do Azure e Codificadores em Direto](https://go.microsoft.com/fwlink/?LinkId=532824).

    Você também pode executar essa etapa depois de criar seu canal.
2. Crie e inicie um canal.

3. Recupere a URL de ingestão de canal.

    O codificador ao vivo usa a URL de ingestão para enviar o fluxo para o canal.
4. Recupere a URL de visualização do canal.

    Utilize este URL para verificar se o canal está a receber corretamente a transmissão em fluxo em direto.
5. Crie um programa.

    Quando você usa o portal do Azure, a criação de um programa também cria um ativo.

    Ao usar o SDK do .NET ou o REST, você precisa criar um ativo e especificar para usar esse ativo ao criar um programa.
6. Publique o ativo associado ao programa.   

    >[!NOTE]
    >Quando sua conta dos serviços de mídia do Azure é criada, um ponto de extremidade de streaming **padrão** é adicionado à sua conta no estado **parado** . O ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**.

7. Inicie o programa quando estiver pronto para iniciar o streaming e o arquivamento.

8. Opcionalmente, o codificador em direto pode ser indicado para iniciar um anúncio. O anúncio é inserido na transmissão de saída.

9. Pare o programa sempre que pretender interromper a transmissão e arquivar o evento.

10. Exclua o programa (e, opcionalmente, exclua o ativo).     

## <a id="channel"></a>Descrição de um canal e seus componentes relacionados
### <a id="channel_input"></a>Configurações de entrada de canal (ingestão)
#### <a id="ingest_protocols"></a>Protocolo de streaming de ingestão
Os serviços de mídia dão suporte à ingestão de feeds ao vivo usando MP4 fragmentado de múltiplas taxas de bits e RTMP com várias taxas de bits como protocolos de streaming. Quando o protocolo de streaming de ingestão RTMP é selecionado, dois pontos de extremidade de ingestão (entrada) são criados para o canal:

* **URL primária**: especifica a URL totalmente qualificada do ponto de extremidade de ingestão RTMP primário do canal.
* **URL secundária** (opcional): especifica a URL totalmente qualificada do ponto de extremidade de ingestão RTMP secundário do canal.

Use a URL secundária se desejar melhorar a durabilidade e a tolerância a falhas do seu fluxo de ingestão (bem como failover do codificador e tolerância a falhas), especialmente para os seguintes cenários:

- Único codificador duplo enviando duas URLs primárias e secundárias:

    A principal finalidade desse cenário é fornecer mais resiliência às oscilações de rede e tremulações. Alguns codificadores RTMP não tratam bem de desconexões de rede. Quando ocorre uma desconexão de rede, um codificador pode parar de codificar e, em seguida, não enviar os dados armazenados em buffer quando ocorrer uma reconexão. Isso causa descontinuidades e perda de dados. As desconexões de rede podem ocorrer devido a uma rede ou manutenção inadequada no lado do Azure. As URLs primária/secundária reduzem os problemas de rede e fornecem um processo de atualização controlado. Cada vez que ocorre uma desconexão de rede agendada, os serviços de mídia gerenciam as desconexões primárias e secundárias e fornecem uma desconexão atrasada entre as duas. Os codificadores têm tempo para continuar a enviar dados e reconectar-se novamente. A ordem das desconexões pode ser aleatória, mas sempre haverá um atraso entre as URLs primária/secundária ou secundária/primária. Nesse cenário, o codificador ainda é o único ponto de falha.

- Vários codificadores, cada um deles é enviado por push para um ponto dedicado:

    Esse cenário fornece o codificador e a redundância de ingestão. Nesse cenário, o condificador1 envia por push para a URL primária e o codificador2 envia para a URL secundária. Quando um codificador falha, o outro codificador pode continuar enviando dados. A redundância de dados pode ser mantida porque os serviços de mídia não desconectam URLs primárias e secundárias ao mesmo tempo. Esse cenário pressupõe que os codificadores são sincronizados e fornecem exatamente os mesmos dados.  

- Vários codificadores enviando duas vezes para URLs primárias e secundárias:

    Nesse cenário, os codificadores enviam dados por push para as URLs primária e secundária. Isso fornece a melhor confiabilidade e tolerância a falhas, bem como a redundância de dados. Esse cenário pode tolerar falhas de codificador e desconexões, mesmo se um codificador parar de funcionar. Ele pressupõe que os codificadores são sincronizados e fornecem exatamente os mesmos dados.  

Para obter informações sobre codificadores ao vivo RTMP, consulte [suporte a RTMP dos serviços de mídia do Azure e codificadores ao vivo](https://go.microsoft.com/fwlink/?LinkId=532824).

#### <a name="ingest-urls-endpoints"></a>URLs de ingestão (pontos de extremidade)
Um canal fornece um ponto de extremidade de entrada (URL de ingestão) que você especifica no codificador ao vivo, para que o codificador possa enviar fluxos para seus canais.   

Você pode obter as URLs de ingestão ao criar o canal. Para que você obtenha essas URLs, o canal não precisa estar no estado **executando** . Quando você estiver pronto para começar a enviar dados por push para o canal, o canal deverá estar no estado **executando** . Depois que o canal inicia a ingestão de dados, você pode visualizar seu fluxo por meio da URL de visualização.

Você tem a opção de ingerir uma transmissão ao vivo com MP4 fragmentado (Smooth Streaming) em uma conexão SSL. Para ingerir sobre SSL, certifique-se de atualizar a URL de ingestão para HTTPS. No momento, não é possível ingerir RTMP sobre SSL.

#### <a id="keyframe_interval"></a>Intervalo de quadro-chave
Quando você estiver usando um codificador ao vivo local para gerar fluxo de múltiplas taxas de bits, o intervalo de quadro-chave especifica a duração do grupo de imagens (GOP), conforme usado por esse codificador externo. Depois que o canal recebe esse fluxo de entrada, você pode entregar sua transmissão ao vivo para aplicativos de reprodução de cliente em qualquer um dos seguintes formatos: Smooth Streaming, streaming adaptável dinâmico sobre HTTP (DASH) e HTTP Live Streaming (HLS). Quando você estiver fazendo uma transmissão ao vivo, o HLS sempre será empacotado dinamicamente. Por padrão, os serviços de mídia calculam automaticamente a taxa de empacotamento de segmento HLS (fragmentos por segmento) com base no intervalo de quadro-chave que é recebido do codificador ao vivo.

A tabela a seguir mostra como a duração do segmento é calculada:

| Intervalo de quadro-chave | Taxa de empacotamento de segmento HLS (FragmentsPerSegment) | Exemplo |
| --- | --- | --- |
| Menor ou igual a 3 segundos |3:1 |Se KeyFrameInterval (ou GOP) for de 2 segundos, a taxa de empacotamento de segmento HLS padrão será de 3 para 1. Isso cria um segmento HLS de 6 segundos. |
| 3 a 5 segundos |2:1 |Se KeyFrameInterval (ou GOP) for de 4 segundos, a taxa de empacotamento de segmento HLS padrão será de 2 para 1. Isso cria um segmento HLS de 8 segundos. |
| Mais de 5 segundos |1:1 |Se KeyFrameInterval (ou GOP) for de 6 segundos, a taxa de empacotamento de segmento HLS padrão será de 1 a 1. Isso cria um segmento HLS de 6 segundos. |

Você pode alterar a taxa de fragmentos por segmento Configurando a saída do canal e configurando FragmentsPerSegment em ChannelOutputHls.

Você também pode alterar o valor do intervalo de quadro-chave definindo a propriedade KeyFrameInterval em ChannelInput. Se você definir explicitamente KeyFrameInterval, a proporção de empacotamento de segmento HLS FragmentsPerSegment será calculada por meio das regras descritas anteriormente.  

Se você definir explicitamente KeyFrameInterval e FragmentsPerSegment, os serviços de mídia usarão os valores que você definiu.

#### <a name="allowed-ip-addresses"></a>Endereços IP permitidos
Você pode definir os endereços IP que têm permissão para publicar o vídeo nesse canal. Um endereço IP permitido pode ser especificado como um dos seguintes:

* Um único endereço IP (por exemplo, 10.0.0.1)
* Um intervalo IP que usa um endereço IP e uma máscara de sub-rede CIDR (por exemplo, 10.0.0.1/22)
* Um intervalo IP que usa um endereço IP e uma máscara de sub-rede decimal com pontos (por exemplo, 10.0.0.1 (255.255.252.0))

Se nenhum endereço IP for especificado e não houver nenhuma definição de regra, não haverá permissão para nenhum endereço IP. Para permitir um endereço IP, crie uma regra e defina 0.0.0.0/0.

### <a name="channel-preview"></a>Visualização do canal
#### <a name="preview-urls"></a>URLs de visualização
Os canais fornecem um ponto de extremidade de visualização (URL de visualização) que você usa para visualizar e validar seu fluxo antes do processamento e da entrega.

Você pode obter a URL de visualização ao criar o canal. Para que você obtenha a URL, o canal não precisa estar no estado **executando** . Depois que o canal inicia a ingestão de dados, você pode visualizar seu fluxo.

Atualmente, o fluxo de visualização só pode ser entregue em formato MP4 fragmentado (Smooth Streaming), independentemente do tipo de entrada especificado. Você pode usar o player do [Monitor de integridade Smooth streaming](https://playready.directtaps.net/smoothstreaming/) para testar o fluxo suave. Você também pode usar um player hospedado no portal do Azure para exibir seu fluxo.

#### <a name="allowed-ip-addresses"></a>Endereços IP permitidos
Você pode definir os endereços IP que têm permissão para se conectar ao ponto de extremidade de visualização. Se nenhum endereço IP for especificado, todos os endereços IP serão permitidos. Um endereço IP permitido pode ser especificado como um dos seguintes:

* Um único endereço IP (por exemplo, 10.0.0.1)
* Um intervalo IP que usa um endereço IP e uma máscara de sub-rede CIDR (por exemplo, 10.0.0.1/22)
* Um intervalo IP que usa um endereço IP e uma máscara de sub-rede decimal com pontos (por exemplo, 10.0.0.1 (255.255.252.0))

### <a name="channel-output"></a>Saída do canal
Para obter informações sobre a saída do canal, consulte a seção [intervalo de quadro-chave](#keyframe_interval) .

### <a name="channel-managed-programs"></a>Programas gerenciados por canal
Um canal é associado a programas que você pode usar para controlar a publicação e o armazenamento de segmentos em uma transmissão ao vivo. Os canais gerenciam programas. A relação entre canal e programa é semelhante à mídia tradicional, em que um canal tem um fluxo constante de conteúdo e um programa tem como escopo um evento cronometrado nesse canal.

Pode especificar o número de horas que pretenda manter o conteúdo gravado para o programa através da configuração da duração da **Janela de Arquivo**. Este valor pode ser definido a partir de um mínimo de 5 minutos até um máximo de 25 horas. O comprimento da janela de arquivo também determina o número máximo de tempo que os clientes podem buscar de volta no tempo a partir da posição dinâmica atual. Os programas podem ser executados durante o período de tempo especificado, mas o conteúdo que se situe atrás da duração da janela é continuamente descartado. O valor desta propriedade também determina durante quanto tempo os manifestos dos clientes podem aumentar.

Cada programa é associado a um ativo que armazena o conteúdo transmitido. Um ativo é mapeado para um contêiner de blobs de blocos na conta de armazenamento do Azure e os arquivos no ativo são armazenados como BLOBs nesse contêiner. Para publicar o programa para que os clientes possam exibir o fluxo, você deve criar um localizador OnDemand para o ativo associado. Você pode usar esse localizador para criar uma URL de streaming que você pode fornecer aos clientes.

Um canal dá suporte a até três programas em execução simultânea, para que você possa criar vários arquivos do mesmo fluxo de entrada. Você pode publicar e arquivar partes diferentes de um evento, conforme necessário. Por exemplo, imagine que seu requisito de negócios seja arquivar 6 horas de um programa, mas para difundir apenas os últimos 10 minutos. Para tal, tem de criar dois programas em execução em simultâneo. Um programa é definido para arquivar seis horas do evento, mas o programa não é publicado. O outro programa é definido como arquivamento por 10 minutos e este programa é publicado.

Não deve reutilizar programas existentes para novos eventos. Em vez disso, crie um novo programa para cada evento. Inicie o programa quando estiver pronto para iniciar o streaming e o arquivamento. Pare o programa sempre que pretender interromper a transmissão e arquivar o evento.

Para excluir o conteúdo arquivado, pare e exclua o programa e, em seguida, exclua o ativo associado. Um ativo não poderá ser excluído se um programa o usar. O programa deve ser excluído primeiro.

Mesmo depois de parar e excluir o programa, os usuários podem transmitir seu conteúdo arquivado como um vídeo sob demanda, até que você exclua o ativo. Se você quiser manter o conteúdo arquivado, mas não o tiver disponível para streaming, exclua o localizador de streaming.

## <a id="states"></a>Estados e cobrança do canal
Os valores possíveis para o estado atual de um canal incluem:

* **Stopped**: esse é o estado inicial do canal após sua criação. Neste estado, as propriedades do canal podem ser atualizadas, mas a transmissão em fluxo não é permitida.
* **Iniciando**: o canal está sendo iniciado. Não são permitidas transmissões em fluxo nem atualizações durante este estado. Se ocorrer um erro, o canal retornará ao estado **parado** .
* **Em execução**: o canal pode processar fluxos ao vivo.
* **Parando**: o canal está sendo interrompido. Não são permitidas transmissões em fluxo nem atualizações durante este estado.
* **Excluindo**: o canal está sendo excluído. Não são permitidas transmissões em fluxo nem atualizações durante este estado.

A tabela seguinte mostra como os estados de um canal mapeiam para o modo de faturação.

| Estado do canal | Indicadores IU do portal | Foi cobrado? |
| --- | --- | --- |
| **Comece** |**Comece** |Não (estado transitório) |
| **Executado** |**Pronto** (sem programas em execução)<p><p>ou<p>**Streaming** (pelo menos um programa em execução) |Sim |
| **Impedir** |**Impedir** |Não (estado transitório) |
| **Interrompido** |**Interrompido** |Não |

## <a id="cc_and_ads"></a>Legendagem oculta e inserção de anúncio
A tabela a seguir demonstra os padrões suportados para Legendagem oculta e inserção de anúncio.

| Padrão | Notas |
| --- | --- |
| CEA-708 e EIA-608 (708/608) |O CEA-708 e o EIA-608 são padrões de legenda codificada para o Estados Unidos e o Canadá.<p><p>Atualmente, há suporte para legendas somente se ela for transportada no fluxo de entrada codificado. Você precisa usar um codificador de mídia ao vivo que pode inserir legendas 608 ou 708 no fluxo codificado que é enviado aos serviços de mídia. Os serviços de mídia fornecem o conteúdo com legendas inseridas para seus visualizadores. |
| TTML Inside. ISMT (Smooth Streaming faixas de texto) |O empacotamento dinâmico dos serviços de mídia permite que seus clientes transmitam conteúdo em qualquer um dos seguintes formatos: DASH, HLS ou Smooth Streaming. No entanto, se você ingerir MP4 fragmentado (Smooth Streaming) com legendas dentro de. ISMT (Smooth Streaming faixas de texto), você pode entregar o fluxo somente para clientes Smooth Streaming. |
| SCTE-35 |SCTE-35 é um sistema de sinalização digital que é usado para marcar a inserção de anúncios. Os receptores downstream usam o sinal para unir anúncios no fluxo pelo tempo alocado. SCTE-35 deve ser enviado como uma faixa esparsa no fluxo de entrada.<p><p>Atualmente, o único formato de fluxo de entrada com suporte que transporta sinais do AD é MP4 fragmentado (Smooth Streaming). O único formato de saída com suporte também é Smooth Streaming. |

## <a id="considerations"></a>Considere
Quando você está usando um codificador ao vivo local para enviar um fluxo de múltiplas taxas de bits para um canal, as seguintes restrições se aplicam:

* Verifique se você tem conectividade de Internet livre suficiente para enviar dados aos pontos de ingestão.
* O uso de uma URL de ingestão secundária requer largura de banda adicional.
* O fluxo de várias taxas de bits de entrada pode ter um máximo de 10 níveis de qualidade de vídeo (camadas) e um máximo de 5 faixas de áudio.
* A taxa de bits média mais alta para qualquer um dos níveis de qualidade de vídeo deve estar abaixo de 10 Mbps.
* A agregação das taxas de bits médias para todos os fluxos de áudio e vídeo deve estar abaixo de 25 Mbps.
* Você não pode alterar o protocolo de entrada enquanto o canal ou seus programas associados estão em execução. Se necessitar de protocolos diferentes, deve criar canais separados para cada protocolo de entrada.
* Você pode ingerir uma taxa de bits única em seu canal. Mas como o canal não processa o fluxo, os aplicativos cliente também receberão um fluxo de taxa de bits única. (Não recomendamos essa opção.)

Aqui estão outras considerações relacionadas ao trabalho com canais e componentes relacionados:

* Toda vez que você reconfigurar o codificador ao vivo, chame o método **Reset** no canal. Antes de redefinir o canal, você precisa parar o programa. Depois de redefinir o canal, reinicie o programa.

  > [!NOTE]
  > Ao reiniciar o programa, você precisa associá-lo a um novo ativo e criar um novo localizador. 
  
* Um canal pode ser interrompido somente quando está no estado de **execução** e todos os programas no canal foram interrompidos.
* Por padrão, você pode adicionar apenas cinco canais à sua conta de serviços de mídia. Para obter mais informações, consulte [cotas e limitações](media-services-quotas-and-limitations.md).
* Você será cobrado somente quando o canal estiver no estado **executando** . Para obter mais informações, consulte a seção [Estados e cobrança do canal](media-services-live-streaming-with-onprem-encoders.md#states) .

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="feedback"></a>Comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tópicos relacionados
[Codificadores locais recomendados](media-services-recommended-encoders.md)

[Especificação de ingestão de vidas de MP4 fragmentados dos serviços de mídia do Azure](../media-services-fmp4-live-ingest-overview.md)

[Visão geral dos serviços de mídia do Azure e cenários comuns](media-services-overview.md)

[Conceitos dos serviços de mídia](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
