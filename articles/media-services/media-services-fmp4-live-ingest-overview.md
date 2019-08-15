---
title: Especificação de ingestão dinâmica de MP4 fragmentado dos serviços de mídia do Azure | Microsoft Docs
description: Esta especificação descreve o protocolo e o formato para ingestão de transmissão ao vivo com base em MP4 fragmentado para os serviços de mídia do Azure. Você pode usar os serviços de mídia do Azure para transmitir eventos ao vivo e transmitir conteúdo em tempo real usando o Azure como a plataforma de nuvem. Este documento também aborda as práticas recomendadas para a criação de mecanismos de ingestão dinâmica altamente redundantes e robustos.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: 43fac263-a5ea-44af-8dd5-cc88e423b4de
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 4e1d41216f99a86a1b04ada882dcae0ff34b823b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "69014825"
---
# <a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Especificação de ingestão dinâmica de MP4 fragmentado dos serviços de mídia do Azure 

Esta especificação descreve o protocolo e o formato para ingestão de transmissão ao vivo com base em MP4 fragmentado para os serviços de mídia do Azure. Os serviços de mídia fornecem um serviço de transmissão ao vivo que os clientes podem usar para transmitir eventos ao vivo e transmitir conteúdo em tempo real usando o Azure como a plataforma de nuvem. Este documento também aborda as práticas recomendadas para a criação de mecanismos de ingestão dinâmica altamente redundantes e robustos.

## <a name="1-conformance-notation"></a>1. Notação de conformidade
As palavras-chave "deve", "" não deve "," obrigatório "," "é necessário", "" pode, "e" opcional "neste documento devem ser interpretadas como são descritas na RFC 2119 (" recomendado ").

## <a name="2-service-diagram"></a>2. Diagrama de serviço
O diagrama a seguir mostra a arquitetura de alto nível do serviço de transmissão ao vivo nos serviços de mídia:

1. Um codificador ao vivo envia feeds ao vivo para canais que são criados e provisionados por meio do SDK dos serviços de mídia do Azure.
1. Os pontos de extremidade de canais, programas e streaming nos serviços de mídia lidam com todas as funcionalidades de transmissão ao vivo, incluindo ingestão, formatação, DVR de nuvem, segurança, escalabilidade e redundância.
1. Opcionalmente, os clientes podem optar por implantar uma camada de rede de distribuição de conteúdo do Azure entre os pontos de extremidade de streaming e de cliente.
1. Os pontos de extremidade do cliente são transmitidos do ponto de extremidades de streaming usando protocolos de streaming adaptável HTTP. Os exemplos incluem Microsoft Smooth Streaming, streaming adaptável dinâmico sobre HTTP (DASH ou MPEG-DASH) e Apple HTTP Live Streaming (HLS).

![fluxo de ingestão][image1]

## <a name="3-bitstream-format--iso-14496-12-fragmented-mp4"></a>3. Formato fragmentado – MP4 fragmentado ISO 14496-12
O formato de conexão para a ingestão de transmissão ao vivo abordado neste documento é baseado em [ISO-14496-12]. Para obter uma explicação detalhada do formato MP4 fragmentado e das extensões para arquivos de vídeo sob demanda e ingestão de transmissão ao vivo, consulte [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).

### <a name="live-ingest-format-definitions"></a>Definições de formato de ingestão dinâmica
A lista a seguir descreve as definições de formato especial que se aplicam à ingestão dinâmica nos serviços de mídia do Azure:

1. O **ftyp**, a **caixa de manifesto do Live Server**e as caixas de **Moov** devem ser enviadas com cada solicitação (http post). Essas caixas devem ser enviadas no início do fluxo e sempre que o codificador precisar se reconectar para retomar a ingestão de fluxo. Para obter mais informações, consulte a seção 6 em [1].
1. A seção 3.3.2 em [1] define uma caixa opcional chamada **StreamManifestBox** para ingestão dinâmica. Devido à lógica de roteamento do balanceador de carga do Azure, usar essa caixa é preterido. A caixa não deve estar presente ao ingerir os serviços de mídia. Se essa caixa estiver presente, os serviços de mídia o ignorarão silenciosamente.
1. A caixa **TrackFragmentExtendedHeaderBox** definida em 3.2.3.2 em [1] deve estar presente para cada fragmento.
1. A versão 2 da caixa **TRACKFRAGMENTEXTENDEDHEADERBOX** deve ser usada para gerar segmentos de mídia que têm URLs idênticas em vários datacenters. O campo de índice de fragmento é necessário para failover entre datacenters de formatos de streaming baseados em índice, como o Apple HLS e o MPEG-DASH baseado em índice. Para habilitar o failover entre datacenters, o índice de fragmento deve ser sincronizado em vários codificadores e aumentado em 1 para cada fragmento de mídia sucessiva, mesmo entre reinicializações do codificador ou falhas.
1. A seção 3.3.6 em [1] define uma caixa chamada **MovieFragmentRandomAccessBox** (**MFRA**) que pode ser enviada no final da ingestão dinâmica para indicar o fim do fluxo (fim de transmissão) para o canal. Devido à lógica de ingestão dos serviços de mídia, o uso de EOS é preterido e a caixa **mfra** para ingestão dinâmica não deve ser enviada. Se enviado, os serviços de mídia o ignoram silenciosamente. Para redefinir o estado do ponto de ingestão, recomendamos que você use a redefinição de [canal](https://docs.microsoft.com/rest/api/media/operations/channel#reset_channels). Também recomendamos que você use a [parada do programa](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) para encerrar uma apresentação e um fluxo.
1. A duração do fragmento MP4 deve ser constante para reduzir o tamanho dos manifestos do cliente. Uma duração de fragmento MP4 constante também melhora a heurística de download do cliente por meio do uso de marcas de repetição. A duração pode flutuar para compensar as taxas de quadros não inteiros.
1. A duração do fragmento MP4 deve estar entre aproximadamente 2 e 6 segundos.
1. Os carimbos de data/hora do fragmento MP4 `fragment_index`(**TrackFragmentExtendedHeaderBox** `fragment_ absolute_ time` e) devem chegar em ordem crescente. Embora os serviços de mídia sejam resilientes a fragmentos duplicados, ele tem capacidade limitada de reordenar fragmentos de acordo com a linha do tempo de mídia.

## <a name="4-protocol-format--http"></a>4. Formato de protocolo – HTTP
A ingestão dinâmica com base em MP4 fragmentado ISO para os serviços de mídia usa uma solicitação HTTP POST padrão de execução longa para transmitir dados de mídia codificados que são empacotados em formato MP4 fragmentado para o serviço. Cada HTTP POST envia um fragmentado MP4 fragmentado completo ("fluxo"), começando do início com as caixas de cabeçalho (caixas**ftyp**, **Live Server manifest**e **Moov** ) e continuando com uma sequência de fragmentos (**Moof** e  **caixas mdat** ). Para obter a sintaxe de URL para a solicitação HTTP POST, consulte a seção 9,2 em [1]. Um exemplo da URL de POSTAgem é: 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

### <a name="requirements"></a>Requisitos
Estes são os requisitos detalhados:

1. O codificador deve iniciar a difusão enviando uma solicitação HTTP POST com um "corpo" vazio (comprimento de conteúdo zero) usando a mesma URL de ingestão. Isso pode ajudar o codificador a detectar rapidamente se o ponto de extremidade de ingestão dinâmica é válido e se há alguma autenticação ou outras condições necessárias. Por protocolo HTTP, o servidor não pode enviar de volta uma resposta HTTP até que toda a solicitação, incluindo o corpo da POSTAgem, seja recebida. Devido à natureza de longa execução de um evento ao vivo, sem essa etapa, o codificador pode não ser capaz de detectar qualquer erro até que ele termine de enviar todos os dados.
1. O codificador deve lidar com erros ou desafios de autenticação por causa de (1). Se (1) tiver sucesso com uma resposta de 200, continue.
1. O codificador deve iniciar uma nova solicitação HTTP POST com o fluxo MP4 fragmentado. A carga deve começar com as caixas de cabeçalho, seguidas por fragmentos. Observe que as caixas **ftyp**, **manifesto do Live Server**e **Moov** (nesta ordem) devem ser enviadas com cada solicitação, mesmo que o codificador deva se reconectar porque a solicitação anterior foi encerrada antes do fim do fluxo. 
1. O codificador deve usar a codificação de transferência em partes para carregar, porque é impossível prever todo o tamanho do conteúdo do evento ao vivo.
1. Quando o evento terminar, depois de enviar o último fragmento, o codificador deverá finalizar normalmente a sequência de mensagens de codificação de transferência em partes (a maioria das pilhas de cliente HTTP lidam automaticamente). O codificador deve aguardar que o serviço retorne o código de resposta final e, em seguida, encerrar a conexão. 
1. O codificador não deve usar `Events()` o substantivo conforme descrito em 9,2 em [1] para ingestão dinâmica nos serviços de mídia.
1. Se a solicitação HTTP POST for encerrada ou expirar com um erro TCP antes do fim do fluxo, o codificador deverá emitir uma nova solicitação POST usando uma nova conexão e seguir os requisitos anteriores. Além disso, o codificador deve reenviar os dois fragmentos MP4 anteriores para cada faixa no fluxo e retomar sem introduzir uma descontinuidade na linha do tempo de mídia. Reenviar os dois últimos fragmentos MP4 para cada faixa garante que não haja perda de dados. Em outras palavras, se um fluxo contiver uma faixa de áudio e de vídeo e a solicitação POST atual falhar, o codificador deverá reconectar e reenviar os dois últimos fragmentos para a faixa de áudio, que foram enviados com êxito e os últimos dois fragmentos para o vídeo Acompanhe, que foram enviadas com êxito, para garantir que não haja perda de dados. O codificador deve manter um buffer de "encaminhamento" de fragmentos de mídia, que ele reenvia quando se reconecta.

## <a name="5-timescale"></a>5. Timescale
[[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx) descreve o uso da escala de bits para **SmoothStreamingMedia** (seção 2.2.2.1), **streamelement** (seção 2.2.2.3), **StreamFragmentElement** (seção 2.2.2.6) e **LiveSMIL** (seção 2.2.7.3.1). Se o valor da escala de valores não estiver presente, o valor padrão usado será 10 milhões (10 MHz). Embora a especificação de formato de Smooth Streaming não bloqueie o uso de outros valores de escala de momento, a maioria das implementações de codificador usa esse valor padrão (10 MHz) para gerar Smooth Streaming dados de ingestão. Devido ao recurso de [empacotamento dinâmico de mídia do Azure](media-services-dynamic-packaging-overview.md) , recomendamos que você use uma escala de time90-kHz para fluxos de vídeo e 44,1 khz ou 48,1 kHz para fluxos de áudio. Se diferentes valores de escala de bits forem usados para fluxos diferentes, a escala de timestream de nível de fluxo deverá ser enviada. Para obter mais informações, consulte [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).     

## <a name="6-definition-of-stream"></a>6. Definição de "fluxo"
Stream é a unidade básica de operação na ingestão dinâmica para composição de apresentações ao vivo, tratamento de failover de streaming e cenários de redundância. O fluxo é definido como um fragmentado único e MP4 fragmentado que pode conter uma única faixa ou várias faixas. Uma apresentação dinâmica completa pode conter um ou mais fluxos, dependendo da configuração dos codificadores ao vivo. Os exemplos a seguir ilustram várias opções de uso de fluxos para compor uma apresentação ao vivo completa.

**Example:** 

Um cliente deseja criar uma apresentação de transmissão ao vivo que inclui as seguintes taxas de bits de áudio/vídeo:

Vídeo – 3000 Kbps, 1500 Kbps, 750 kbps

Áudio – 128 kbps

### <a name="option-1-all-tracks-in-one-stream"></a>Opção 1: Todas as faixas em um fluxo
Nessa opção, um único codificador gera todas as faixas de áudio/vídeo e os agrupa em um fragmentado MP4 fragmentado. O fragmentado MP4 fragmentado é enviado por meio de uma única conexão HTTP POST. Neste exemplo, há apenas um fluxo para esta apresentação ao vivo.

![Fluxos-um acompanhamento][image2]

### <a name="option-2-each-track-in-a-separate-stream"></a>Opção 2: Cada faixa em um fluxo separado
Nessa opção, o codificador coloca uma faixa em cada fragmentado MP4 de fragmento e, em seguida, posta todos os fluxos em conexões HTTP separadas. Isso pode ser feito com um codificador ou com vários codificadores. A ingestão dinâmica vê essa apresentação ao vivo como composta de quatro fluxos.

![Fluxos – faixas separadas][image3]

### <a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Opção 3: Agrupar faixa de áudio com a faixa de vídeo de taxa de bits mais baixa em um fluxo
Nessa opção, o cliente escolhe agrupar a faixa de áudio com a faixa de vídeo de taxa de bits mais baixa em um fragmentado de MP4 de fragmento e deixar as outras duas faixas de vídeo como fluxos separados. 

![Fluxos-faixas de áudio e vídeo][image4]

### <a name="summary"></a>Resumo
Esta não é uma lista completa de todas as opções de ingestão possíveis para este exemplo. Na verdade, qualquer agrupamento de faixas em fluxos é suportado pela ingestão dinâmica. Os clientes e os fornecedores de codificadores podem escolher suas próprias implementações com base na complexidade da engenharia, capacidade do codificador e considerações sobre redundância e failover. No entanto, na maioria dos casos, há apenas uma faixa de áudio para toda a apresentação ao vivo. Portanto, é importante garantir a integridade do fluxo de ingestão que contém a faixa de áudio. Essa consideração geralmente resulta na colocação da faixa de áudio em seu próprio fluxo (como na opção 2) ou no agrupamento com a faixa de vídeo de taxa de bits mais baixa (como na opção 3). Além disso, para melhor redundância e tolerância a falhas, o envio da mesma faixa de áudio em dois fluxos diferentes (opção 2 com faixas de áudio redundantes) ou o agrupamento da faixa de áudio com pelo menos duas das faixas de vídeo de taxa de bits mais baixa (opção 3 com áudio empacotado em pelo menos dois fluxos de vídeo) é altamente recomendável para ingestão dinâmica nos serviços de mídia.

## <a name="7-service-failover"></a>7. Failover de serviço
Devido à natureza da transmissão ao vivo, o bom suporte a failover é essencial para garantir a disponibilidade do serviço. Os serviços de mídia foram projetados para lidar com vários tipos de falhas, incluindo erros de rede, erros de servidor e problemas de armazenamento. Quando usado em conjunto com a lógica de failover adequada do lado do codificador ao vivo, os clientes podem obter um serviço de transmissão ao vivo altamente confiável da nuvem.

Nesta seção, discutiremos cenários de failover de serviço. Nesse caso, a falha ocorre em algum lugar dentro do serviço e se manifesta como um erro de rede. Aqui estão algumas recomendações para a implementação do codificador para tratamento de failover de serviço:

1. Use um tempo limite de 10 segundos para estabelecer a conexão TCP. Se uma tentativa de estabelecer a conexão demorar mais de 10 segundos, anule a operação e tente novamente. 
1. Use um tempo limite curto para enviar as partes da mensagem de solicitação HTTP. Se a duração do fragmento MP4 de destino for de N segundos, use um tempo limite de envio entre N e 2 N segundos; por exemplo, se a duração do fragmento MP4 for de 6 segundos, use um tempo limite de 6 a 12 segundos. Se ocorrer um tempo limite, redefina a conexão, abra uma nova conexão e retome a ingestão de fluxo na nova conexão. 
1. Mantenha um buffer sem interrupção que tenha os dois últimos fragmentos para cada faixa que foram enviados com êxito e completamente para o serviço.  Se a solicitação HTTP POST de um fluxo for encerrada ou expirar antes do fim do fluxo, abra uma nova conexão e inicie outra solicitação HTTP POST, reenvie os cabeçalhos de fluxo, reenvie os dois últimos fragmentos para cada faixa e retome o fluxo sem introduzir um d iscontinuity na linha do tempo de mídia. Isso reduz a chance de perda de dados.
1. Recomendamos que o codificador não limite o número de tentativas para estabelecer uma conexão ou retomar o streaming depois que um erro TCP ocorrer.
1. Após um erro de TCP:
  
    a. A conexão atual deve ser fechada e uma nova conexão deve ser criada para uma nova solicitação HTTP POST.

    b. A nova URL HTTP POST deve ser a mesma que a URL de POSTAgem inicial.
  
    c. O novo HTTP POST deve incluir cabeçalhos de fluxo (**ftyp**, **caixa de manifesto do Live Server**e caixas de **Moov** ) que são idênticos aos cabeçalhos de fluxo na postagem inicial.
  
    d. Os dois últimos fragmentos enviados para cada faixa devem ser reenviados e o streaming deve retomar sem introduzir uma descontinuidade na linha do tempo de mídia. Os carimbos de data/hora do fragmento MP4 devem aumentar continuamente, mesmo entre solicitações HTTP POST.
1. O codificador deve terminar a solicitação HTTP POST se os dados não estiverem sendo enviados a uma taxa proporcional à duração do fragmento MP4.  Uma solicitação HTTP POST que não envia dados pode impedir que os serviços de mídia se desconectem rapidamente do codificador no caso de uma atualização de serviço. Por esse motivo, o HTTP POST para faixas esparsas (sinal de anúncio) deve ser de curta duração, terminando assim que o fragmento esparso é enviado.

## <a name="8-encoder-failover"></a>8. Failover do codificador
O failover do codificador é o segundo tipo de cenário de failover que precisa ser resolvido para entrega de streaming ao vivo de ponta a ponta. Nesse cenário, a condição de erro ocorre no lado do codificador. 

![failover do codificador][image5]

As expectativas a seguir se aplicam do ponto de extremidade de ingestão dinâmica quando ocorre o failover do codificador:

1. Uma nova instância do codificador deve ser criada para continuar o streaming, conforme ilustrado no diagrama (fluxo para vídeo 3.000 k, com linha tracejada).
1. O novo codificador deve usar a mesma URL para solicitações HTTP POST como a instância com falha.
1. A solicitação POST do novo codificador deve incluir as mesmas caixas de cabeçalho MP4 fragmentadas que a instância com falha.
1. O novo codificador deve ser sincronizado corretamente com todos os outros codificadores em execução para a mesma apresentação ao vivo para gerar amostras de áudio/vídeo sincronizadas com limites de fragmento alinhados.
1. O novo fluxo deve ser semanticamente equivalente ao fluxo anterior e intercambiável nos níveis de cabeçalho e de fragmento.
1. O novo codificador deve tentar minimizar a perda de dados. O `fragment_absolute_time` e`fragment_index` os fragmentos de mídia devem aumentar desde o ponto em que o codificador foi interrompido pela última vez. O `fragment_absolute_time` e`fragment_index` deve aumentar de maneira contínua, mas é permitido introduzir uma descontinuidade, se necessário. Os serviços de mídia ignoram os fragmentos que já foram recebidos e processados, portanto, é melhor errar no lado de reenviar fragmentos do que introduzir descontinuidades na linha do tempo de mídia. 

## <a name="9-encoder-redundancy"></a>9. Redundância de codificador
Para determinados eventos ao vivo críticos que demandam disponibilidade e qualidade de experiência ainda maiores, recomendamos que você use codificadores redundantes ativos-ativos para obter um failover contínuo sem perda de dados.

![redundância de codificador][image6]

Conforme ilustrado neste diagrama, dois grupos de codificadores enviam duas cópias de cada fluxo simultaneamente para o serviço ao vivo. Essa configuração tem suporte porque os serviços de mídia podem filtrar fragmentos duplicados com base na ID do fluxo e no carimbo de data/hora do fragmento. O fluxo ao vivo resultante e o arquivo morto são uma única cópia de todos os fluxos que são a melhor agregação possível das duas fontes. Por exemplo, em um caso extremo hipotético, desde que haja um codificador (ele não precisa ser o mesmo) em execução em um determinado momento para cada fluxo, o fluxo ao vivo resultante do serviço é contínuo sem perda de dados. 

Os requisitos para esse cenário são quase os mesmos que os requisitos no caso de "failover do codificador", com a exceção de que o segundo conjunto de codificadores está em execução ao mesmo tempo que os codificadores primários.

## <a name="10-service-redundancy"></a>10. Redundância de serviço
Para distribuição global altamente redundante, às vezes você deve ter um backup entre regiões para lidar com desastres regionais. Expandindo a topologia de "redundância do codificador", os clientes podem optar por ter uma implantação de serviço redundante em uma região diferente conectada ao segundo conjunto de codificadores. Os clientes também podem trabalhar com um provedor de rede de distribuição de conteúdo para implantar um Gerenciador de tráfego global na frente das duas implantações de serviço para rotear diretamente o tráfego do cliente. Os requisitos para os codificadores são os mesmos do caso de "redundância de codificador". A única exceção é que o segundo conjunto de codificadores precisa ser apontado para um ponto de extremidade de ingestão dinâmica diferente. O diagrama a seguir mostra essa configuração:

![redundância de serviço][image7]

## <a name="11-special-types-of-ingestion-formats"></a>11. Tipos especiais de formatos de ingestão
Esta seção aborda tipos especiais de formatos de ingestão dinâmica projetados para lidar com cenários específicos.

### <a name="sparse-track"></a>Faixa esparsa
Ao fornecer uma apresentação de transmissão ao vivo com uma experiência de cliente rica, geralmente é necessário transmitir eventos sincronizados com o tempo ou sinais em banda com os dados de mídia principais. Um exemplo disso é a inserção dinâmica do AD do Live. Esse tipo de sinalização de evento é diferente do streaming de áudio/vídeo normal devido à sua natureza esparsa. Em outras palavras, os dados de sinalização geralmente não ocorrem continuamente e o intervalo pode ser difícil de prever. O conceito de faixa esparsa foi projetado para ingerir e difundir dados de sinalização em banda.

As etapas a seguir são uma implementação recomendada para ingestão de faixa esparsa:

1. Crie um fragmentado MP4 fragmentado separado que contenha apenas faixas esparsas, sem faixas de áudio/vídeo.
1. Na **caixa manifesto do Live Server** , conforme definido na seção 6 em [1], use o parâmetro *parentTrackName* para especificar o nome da faixa pai. Para obter mais informações, consulte a seção 4.2.1.2.1.2 em [1].
1. Na **caixa manifesto do Live Server**, **manifestOutput** deve ser definido como **true**.
1. Devido à natureza esparsa do evento de sinalização, recomendamos o seguinte:
   
    a. No início do evento ao vivo, o codificador envia as caixas de cabeçalho inicial para o serviço, o que permite que o serviço Registre a faixa esparsa no manifesto do cliente.
   
    b. O codificador deve encerrar a solicitação HTTP POST quando os dados não estão sendo enviados. Um POST HTTP de execução longa que não envia dados pode impedir que os serviços de mídia se desconectem rapidamente do codificador no caso de uma atualização de serviço ou de uma reinicialização do servidor. Nesses casos, o servidor de mídia é temporariamente bloqueado em uma operação de recebimento no soquete.
   
    c. Durante o tempo em que os dados de sinalização não estão disponíveis, o codificador deve fechar a solicitação HTTP POST. Enquanto a solicitação POST está ativa, o codificador deve enviar dados.

    d. Ao enviar fragmentos esparsos, o codificador pode definir um cabeçalho de comprimento de conteúdo explícito, se estiver disponível.

    e. Ao enviar fragmentos esparsos com uma nova conexão, o codificador deve começar a enviar as caixas de cabeçalho, seguidos pelos novos fragmentos. Isso ocorre para casos em que o failover ocorre no-between, e a nova conexão esparsa está sendo estabelecida com um novo servidor que não tenha visto a faixa esparsa antes.

    f. O fragmento de faixa esparsa fica disponível para o cliente quando o fragmento de faixa pai correspondente que tem um valor de carimbo de data/hora igual ou maior é disponibilizado para o cliente. Por exemplo, se o fragmento esparso tiver um carimbo de data/hora de t = 1000, espera-se que depois que o cliente Veja "vídeo" (supondo que o nome da faixa pai seja "vídeo") o carimbo de data/hora do fragmento 1000 ou posterior, ele pode baixar o fragmento esparso t = 1000. Observe que o sinal real pode ser usado para uma posição diferente na linha do tempo da apresentação para sua finalidade designada. Neste exemplo, é possível que o fragmento esparso de t = 1000 tenha uma carga XML, que é para inserir um anúncio em uma posição que seja de alguns segundos depois.

    g. A carga de fragmentos de faixas esparsas pode estar em formatos diferentes (como XML, texto ou binário), dependendo do cenário.

### <a name="redundant-audio-track"></a>Faixa de áudio redundante
Em um cenário típico de streaming adaptável HTTP (por exemplo, Smooth Streaming ou DASH), muitas vezes, há apenas uma faixa de áudio em toda a apresentação. Ao contrário das faixas de vídeo, que têm vários níveis de qualidade para o cliente escolher em condições de erro, a faixa de áudio poderá ser um ponto único de falha se a ingestão do fluxo que contém a faixa de áudio for quebrada. 

Para resolver esse problema, os serviços de mídia dão suporte à ingestão dinâmica de faixas de áudio redundantes. A ideia é que a mesma faixa de áudio pode ser enviada várias vezes em fluxos diferentes. Embora o serviço Registre apenas a faixa de áudio uma vez no manifesto do cliente, ele pode usar faixas de áudio redundantes como backups para recuperar fragmentos de áudio se a faixa de áudio principal tiver problemas. Para ingerir faixas de áudio redundantes, o codificador precisa:

1. Crie a mesma faixa de áudio em vários fragmentado MP4 de fragmento. As faixas de áudio redundantes devem ser semanticamente equivalentes, com os mesmos carimbos de data/hora do fragmento e ser intercambiáveis nos níveis de cabeçalho e de fragmento.
1. Verifique se a entrada "áudio" no manifesto do Live Server (seção 6 em [1]) é a mesma para todas as faixas de áudio redundantes.

A implementação a seguir é recomendada para faixas de áudio redundantes:

1. Envie cada faixa de áudio exclusiva em um fluxo por si só. Além disso, envie um fluxo redundante para cada um desses fluxos de faixas de áudio, em que o segundo fluxo difere do primeiro somente pelo identificador na URL HTTP POST: {Protocol}:}://{address}/{caminho ponto Path}/fluxos ({identificador}).
1. Use fluxos separados para enviar as duas taxas de bits de vídeo mais baixas. Cada um desses fluxos também deve conter uma cópia de cada faixa de áudio exclusiva. Por exemplo, quando há suporte para vários idiomas, esses fluxos devem conter faixas de áudio para cada idioma.
1. Use instâncias de servidor (codificador) separadas para codificar e enviar os fluxos redundantes mencionados em (1) e (2). 

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
