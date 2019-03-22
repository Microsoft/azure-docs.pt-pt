---
title: Especificação de ingestão de MP4 fragmentado em direto dos serviços de multimédia do Azure | Documentos da Microsoft
description: Essa especificação descreve o protocolo e o formato fragmentado com base em MP4 em direto transmissão em fluxo para ingestão de para serviços de multimédia do Azure. Pode utilizar os serviços de multimédia do Azure para transmitir em fluxo eventos em direto e transmissão de conteúdo em tempo real ao utilizar o Azure como a plataforma na cloud. Este documento também discute as melhores práticas para a criação altamente redundantes e robustos em direto de mecanismos de ingestão.
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
ms.author: cenkd;juliako
ms.openlocfilehash: b3357436d068396c5c3c4fae10ed6857759c5aed
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189347"
---
# <a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Especificação de ingestão de MP4 fragmentado em direto dos serviços de multimédia do Azure 

Essa especificação descreve o protocolo e o formato fragmentado com base em MP4 em direto transmissão em fluxo para ingestão de para serviços de multimédia do Azure. Os Media Services fornecem um serviço de transmissão em fluxo em direto que os clientes podem utilizar para transmitir em fluxo eventos em direto e transmissão de conteúdo em tempo real ao utilizar o Azure como a plataforma na cloud. Este documento também discute as melhores práticas para a criação altamente redundantes e robustos em direto de mecanismos de ingestão.

## <a name="1-conformance-notation"></a>1. Notação de conformidade
A chave ",""Não deve," "REQUIRED", "SHALL", "deve não deve," "SHOULD", "Deve NOT," palavras "Recomendados", "Maio," e "Opcional" neste documento devem ser interpretados como estas estão descritas no RFC 2119.

## <a name="2-service-diagram"></a>2. Diagrama de serviço
O diagrama seguinte mostra a arquitetura de alto nível do serviço de transmissão em fluxo em direto nos serviços de multimédia:

1. Um codificador em direto pushes feeds em direto para canais que são criados e aprovisionados através do SDK de serviços de suporte de dados do Azure.
1. Canais, programas e pontos finais de transmissão em fluxo nos serviços de multimédia lidar com todas as transmissão em fluxo funcionalidades direto, incluindo a ingestão, formatação, DVR, segurança, escalabilidade e redundância da cloud.
1. Opcionalmente, os clientes podem optar por implementar uma camada de rede de entrega de conteúdos do Azure entre o ponto final de transmissão em fluxo e os pontos de extremidade do cliente.
1. Fluxo de pontos finais de cliente do ponto de final de transmissão em fluxo através de protocolos HTTP transmissão em fluxo adaptável. Os exemplos incluem o Microsoft Smooth Streaming, Dynamic Adaptive Streaming através de HTTP (DASH ou MPEG-DASH) e Apple HTTP Live Streaming (HLS).

![ingestão de fluxo][image1]

## <a name="3-bitstream-format--iso-14496-12-fragmented-mp4"></a>3. Formato de Bitstream – ISO 14496-12 real de MP4 fragmentado
O formato de transmissão em direto de ingestão discutidos neste documento se baseia em [ISO-14496-12]. Para obter uma explicação detalhada de fragmentados formato MP4 e extensões tanto para ficheiros de vídeo a pedido e ingestão de transmissão em fluxo em direto, consulte [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).

### <a name="live-ingest-format-definitions"></a>Definições do formato de ingestão em direto
A lista seguinte descreve as definições que se aplicam ao live ingerir para os serviços de multimédia do Azure de formato especial:

1. O **ftyp**, **Live manifesto caixa do servidor de**, e **moov** caixas têm de ser enviadas com cada solicitação (HTTP POST). Essas caixas têm de ser enviadas no início da transmissão em fluxo e sempre que o codificador deve voltar a ligar ao retomar o fluxo de ingestão. Para obter mais informações, consulte a secção 6 [1].
1. Secção 3.3.2 [1] Define uma caixa opcional chamada **StreamManifestBox** para ingestão em direto. Devido à lógica de encaminhamento do Balanceador de carga do Azure, o meio dessa caixa foi preterido. A caixa de não deve estar presente quando ingestão em serviços de multimédia. Se esta caixa está presente, os serviços de multimédia silenciosamente ignora-lo.
1. O **TrackFragmentExtendedHeaderBox** caixa definida no 3.2.3.2 em [1] tem de estar presente para cada fragmento.
1. Versão 2 dos **TrackFragmentExtendedHeaderBox** caixa deve ser utilizada para gerar os segmentos de suporte de dados que têm URLs idênticas em vários datacenters. O campo de índice de fragmento é obrigatório para a ativação pós-falha do Centro de dados em vários dos formatos de transmissão em fluxo com base no índice, tais como a Apple HLS e MPEG-DASH com base no índice. Para ativar a ativação pós-falha do Centro de dados entre, o índice de fragmento têm de ser sincronizado entre vários codificadores e ser aumentado em 1 para cada fragmento de suporte de dados sucessivas, mesmo entre reinícios de codificador ou falhas.
1. Secção 3.3.6 [1] Define uma caixa chamada **MovieFragmentRandomAccessBox** (**mfra**) que podem ser enviados no final de ingestão em direto para indicar o fim do fluxo (término do suporte ao) para o canal. Devido à lógica dos serviços de multimédia ingestão, usar o término do suporte ao foi preterido e o **mfra** caixa para ingestão em direto não deve ser enviada. Se enviado, serviços de multimédia silenciosamente ignora-lo. Para repor o estado do ponto de ingestão, recomendamos que utilize [canal repor](https://docs.microsoft.com/rest/api/media/operations/channel#reset_channels). Também recomendamos que utilize [programa parar](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) para terminar uma apresentação e o stream.
1. A duração de fragmento MP4 deve ser constante, para reduzir o tamanho dos manifestos dos clientes. Repita os uma constante MP4 duração de fragmento também melhora a heurística de transferência de cliente através da utilização de etiquetas. A duração pode variar para compensar taxas de quadros não inteiros.
1. A duração de fragmento MP4 deve ser entre aproximadamente 2 a 6 segundos.
1. Fragmentados de MP4 carimbos e índices (**TrackFragmentExtendedHeaderBox** `fragment_ absolute_ time` e `fragment_index`) deverá ser entregue em ordem crescente. Embora os serviços de multimédia é resiliente a fragmentos duplicados, limitou a capacidade de reordenar os fragmentos de acordo com a linha cronológica de suporte de dados.

## <a name="4-protocol-format--http"></a>4. Formato de protocolo – HTTP
ISO fragmentados com base em MP4 em direto do serviços de multimédia utiliza uma solicitação de HTTP POST de longa execução padrão para transmitir dados de suporte de dados com codificação que são empacotados em formatos fragmentados de MP4 para o serviço de ingestão. Cada HTTP POST envia uma completa fragmentados MP4 bitstream ("fluxo"), começando do início com caixas de cabeçalho (**ftyp**, **Live manifesto caixa do servidor de**, e **moov** caixas ) e continuando com uma seqüência de fragmentos (**moof** e **mdat** caixas). Sintaxe de URL para o pedido de HTTP POST, consulte a secção in 9.2 [1]. Um exemplo do URL de publicação é: 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

### <a name="requirements"></a>Requisitos
Eis os requisitos detalhados:

1. O codificador deve começar a transmissão ao enviar um pedido de HTTP POST com um "corpo vazio" (comprimento zero conteúdo) ao utilizar o mesmo URL de ingestão. Isso pode ajudar o codificador detetar rapidamente se o ponto final de ingestão em direto é válido e, se houver qualquer autenticação ou outras condições necessárias. Por protocolo HTTP, o servidor não é possível enviar de volta uma resposta HTTP até que o pedido de todo, incluindo o corpo da mensagem, é recebido. Dada a natureza de longa execução de um evento em direto, sem essa etapa, o codificador pode não ser capaz de detetar qualquer erro até que ele termine o envio de todos os dados.
1. O codificador deve processar quaisquer erros ou desafios de autenticação devido a (1). Se (1) for concluída com êxito com uma resposta 200, continuar.
1. O codificador deve iniciar um novo pedido de HTTP POST com o fluxo MP4 fragmentado. O payload tem de começar com as caixas de cabeçalho, seguidas de fragmentos. Tenha em atenção que o **ftyp**, **Live manifesto caixa do servidor de**, e **moov** caixas (por esta ordem) têm de ser enviadas com cada solicitação, mesmo que o codificador deve voltar a ligar porque o anterior pedido foi terminado antes do final da transmissão em fluxo. 
1. O codificador deve usar a codificação para carregar, porque é impossível prever o comprimento do conteúdo inteiro do evento em direto da transferência.
1. Quando o evento é, depois de enviar o último fragmento, o codificador normalmente tem de terminar a sequência de mensagem (a maioria das pilhas de cliente HTTP manipulá-lo automaticamente) de codificação de transferência em partes. O codificador tem de aguardar que o serviço devolver o código de resposta final e, em seguida, termine a ligação. 
1. O codificador não deve utilizar o `Events()` substantivo, conforme descrito em 9.2 in [1] para a ingestão em direto para os serviços multimédia.
1. Se o pedido de HTTP POST termina ou exceder o tempo limite com um erro TCP antes do final da transmissão em fluxo, o codificador deve emitir um novo pedido POST utilizando uma nova ligação e siga os requisitos anteriores. Além disso, o codificador deve reenviar os anteriores dois fragmentos de MP4 para cada faixa no fluxo e retomar sem introduzir uma descontinuidade na linha da tempo de suporte de dados. Reenviar os últimos dois fragmentos de MP4 para cada faixa garante que não existe nenhuma perda de dados. Em outras palavras, se um fluxo contém um áudio e uma faixa de vídeo e o atual pedido POST falha, o codificador deve voltar a ligar e reenviar os dois últimos fragmentos para a faixa de áudio, que anteriormente foram enviados com sucesso, e os dois últimos fragmentos do vídeo controle, que foram anteriormente enviado com sucesso, para garantir que não existe nenhuma perda de dados. O codificador deve manter uma memória intermédia "reencaminhar" dos fragmentos de suporte de dados, que reenviar quando voltar a ser ligado.

## <a name="5-timescale"></a>5. Escala temporal
[[MS-SSTR] ](https://msdn.microsoft.com/library/ff469518.aspx) descreve a utilização de escala temporal para **SmoothStreamingMedia** (secção 2.2.2.1), **StreamElement** (secção 2.2.2.3), **StreamFragmentElement** ( Secção 2.2.2.6), e **LiveSMIL** (secção 2.2.7.3.1). Se o valor de escala temporal não estiver presente, o valor predefinido utilizado é 10,000,000 (10 MHz). Embora a especificação de formato de transmissão em fluxo uniforme não bloqueia a utilização de outros valores de escala temporal, a maioria das implementações do codificador utilizar esta predefinição valor (10 MHz) para gerar a transmissão em fluxo uniforme ingestão de dados. Devido ao [empacotamento dinâmico do suporte de dados do Azure](media-services-dynamic-packaging-overview.md) funcionalidade, recomendamos que utilize uma escala temporal de 90 KHz para fluxos de vídeo e 44.1 KHz ou 48.1 KHz para fluxos de áudio. Se os valores de escala temporal diferentes são utilizados para várias transmissões em fluxo, a escala ao nível do fluxo temporal têm de ser enviada. Para obter mais informações, consulte [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).     

## <a name="6-definition-of-stream"></a>6. Definição de "stream"
Stream é a unidade básica de operação na ingestão em direto para compor a apresentações ao vivo, processamento de transmissão em fluxo de ativação pós-falha e cenários de redundância. Stream é definido como um bitstream MP4 de fragmentados, exclusivo que pode conter um Roteiro único ou vários roteiros. Uma apresentação online completo pode conter um ou mais fluxos, dependendo da configuração dos codificadores em direto. Os exemplos seguintes mostram as várias opções de utilização de fluxos para compor uma apresentação completa em direto.

**Exemplo:** 

Um cliente quiser criar uma apresentação de transmissão em fluxo em direto que inclui as seguintes velocidades de transmissão de áudio/vídeo:

Vídeo – 3000 kbps, 1500 kbps, 750 kbps

Áudio – 128 kbps

### <a name="option-1-all-tracks-in-one-stream"></a>Opção 1: Todas as faixas num fluxo
Esta opção, um codificador único gera todas as faixas de áudio/vídeo e, em seguida, agrupa-los num bitstream de MP4 fragmentado. Bitstream de MP4 fragmentado, em seguida, é enviado através de uma única ligação de HTTP POST. Neste exemplo, há apenas um fluxo para esta apresentação em direto.

![Controlar fluxos-um][image2]

### <a name="option-2-each-track-in-a-separate-stream"></a>Opção 2: Cada controle num fluxo separado
Esta opção, o codificador coloca uma faixa bitstream cada fragmento MP4 e, em seguida, publica todos os fluxos através de ligações de HTTP separadas. Isso pode ser feito com um codificador ou com vários codificadores. A ingestão em direto vê esta apresentação em direto como composta por quatro fluxos.

![Faixas de fluxos separados][image3]

### <a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Opção 3: Faixa de áudio do pacote com a faixa de vídeo de velocidade de transmissão mais baixa para um fluxo
Esta opção, o cliente escolhe para agrupar a faixa de áudio com a faixa de vídeo de velocidade de transmissão mais baixa no bitstream um fragmento MP4 e deixe os outros roteiros de vídeo dois fluxos separados como. 

![Faixas de fluxos de áudio e vídeos][image4]

### <a name="summary"></a>Resumo
Não se trata de uma lista completa de todas as opções de ingestão possíveis para este exemplo. Como na verdade, qualquer agrupamento de roteiros em fluxos é suportado pelo ingestão em direto. Os clientes e fornecedores de codificador, podem escolher suas próprias implementações baseadas em complexidade de engenharia, a capacidade de codificador e a redundância e considerações de ativação pós-falha. No entanto, na maioria dos casos, há apenas uma faixa de áudio para toda a apresentação de em direto. Assim, é importante certificar-se a healthiness do fluxo de ingestão que contém a faixa de áudio. Essa consideração, muitas vezes, resulta em colocando a faixa de áudio no seu próprio fluxo (como na opção 2) ou agrupamento-lo com a faixa de vídeo mais baixa velocidade de transmissão múltipla (como na opção 3). Além disso, para uma melhor redundância e tolerância a falhas, enviar a mesma faixa de áudio em dois fluxos diferentes (opção 2 com faixas de áudio redundantes) ou agrupamento a faixa de áudio com, pelo menos, dois dos roteiros de vídeo mais baixa velocidade de transmissão múltipla (3 de opção com áudio agrupado em, pelo menos, dois fluxos de vídeo) seja altamente recomendada para em direto ingerir nos serviços de multimédia.

## <a name="7-service-failover"></a>7. Ativação pós-falha do serviço
Dada a natureza da transmissão em direto, suporte de ativação pós-falha bom é essencial para garantir a disponibilidade do serviço. Serviços de suporte de dados foi concebido para lidar com vários tipos de falhas, incluindo erros de rede, erros de servidor e problemas de armazenamento. Quando utilizado em conjunto com a lógica apropriada de ativação pós-falha do lado do codificador em direto, os clientes podem obter um serviço altamente confiável de transmissão em fluxo em direto da cloud.

Nesta secção, vamos abordar cenários de ativação pós-falha do serviço. Neste caso, a falha ocorre em algum lugar no âmbito do serviço, e se manifesta como um erro de rede. Seguem-se algumas recomendações para a implementação de codificador para lidar com ativação pós-falha do serviço:

1. Utilize um tempo limite de 10 segundos para estabelecer a ligação de TCP. Se uma tentativa de estabelecer a ligação demorar mais de 10 segundos, a operação abort e tente novamente. 
1. Utilize o limite de tempo curto para enviar o pedido HTTP segmentos de mensagem. Se a duração de fragmento de MP4 de destino for N segundos, utilize um tempo limite de envio entre N e 2 N segundos; Por exemplo, se a duração de fragmento de MP4 é 6 segundos, utilize um tempo limite de 6 a 12 segundos. Se ocorrer um tempo limite, repor a ligação, abra uma nova ligação e retomar o fluxo de inserção na nova ligação. 
1. Manter uma memória intermédia sem interrupção que tenha os dois últimos fragmentos para cada faixa com êxito e completamente enviadas para o serviço.  Se o pedido de HTTP POST para um fluxo é encerrado ou exceder o tempo limite anteriores ao final da transmissão em fluxo, abrir uma nova ligação e começar outro pedido de HTTP POST, reenviar os cabeçalhos do fluxo, reenviar os dois últimos fragmentos para cada faixa e retomar o fluxo sem introduzir um d iscontinuity na linha da tempo de suporte de dados. Isso reduz a possibilidade de perda de dados.
1. Recomendamos que o codificador não limita o número de repetições para estabelecer uma ligação ou retomar a transmissão em fluxo depois de ocorrer um erro TCP.
1. Após um erro TCP:
  
    a. A ligação atual tem de ser fechada e tem de ser criada uma nova ligação para um novo pedido de HTTP POST.

    b. O novo HTTP POST URL tem de ser igual ao URL de publicação inicial.
  
    c. O novo HTTP POST deve incluir cabeçalhos de stream (**ftyp**, **Live manifesto caixa do servidor de**, e **moov** caixas) que são idênticos para os cabeçalhos do fluxo na mensagem inicial.
  
    d. Os dois últimos fragmentos enviados para cada faixa devem ser reenviados e transmissão em fluxo tem retomar sem introduzir uma descontinuidade na linha da tempo de suporte de dados. Os carimbos de fragmento MP4 devem aumentar continuamente, até mesmo em pedidos de HTTP POST.
1. O codificador deve terminar o pedido POST de HTTP, se não estão a ser enviados dados a uma taxa proporcional com a duração de fragmento de MP4.  Um pedido de HTTP POST não envia dados pode impedir que os serviços de multimédia rapidamente a desligar do codificador em caso de uma atualização de serviço. Por esse motivo, o HTTP POST para dispersas roteiros (sinal ad) devem ser com vida curta, assim que o fragmento disperso é enviado a terminar.

## <a name="8-encoder-failover"></a>8. Ativação pós-falha do codificador
Ativação pós-falha do codificador é o segundo tipo de cenário de ativação pós-falha que precisa ser resolvidos para entrega de transmissão em fluxo em direto de ponto-a-ponto. Neste cenário, a condição de erro ocorre no lado do codificador. 

![Ativação pós-falha do codificador][image5]

As expectativas seguintes aplicam-se do ponto de extremidade de ingestão em direto quando ocorre a ativação pós-falha de codificador:

1. Uma nova instância de codificador deverá ser criada para continuar a transmissão em fluxo, conforme ilustrado no diagrama (Stream para 3000k de vídeo, com linha tracejada).
1. O novo codificador de tem de utilizar o mesmo URL para pedidos de HTTP POST como a instância com falha.
1. Pedido POST o novo codificador tem de incluir as mesmo caixas de cabeçalho de MP4 fragmentadas conforme a instância com falha.
1. O novo codificador de têm de ser sincronizado corretamente com todos os outros codificadores em execução para a mesma apresentação em direto para gerar os exemplos de áudio/vídeo sincronizados com os limites de fragmento alinhados.
1. O novo fluxo tem de ser semanticamente equivalente com o fluxo anterior e intercambiáveis nos níveis de cabeçalho e fragmento.
1. O novo codificador deve tentar minimizar a perda de dados. O `fragment_absolute_time` e `fragment_index` de suportes de dados fragmentos devem aumentar a partir do ponto em que o codificador última paragem. O `fragment_absolute_time` e `fragment_index` deve aumentar de forma contínua, mas é permitido para introduzir uma descontinuidade, se necessário. Serviços de multimédia ignora fragmentos que já tenha recebido e processado, portanto, é melhor a errar por reenviar fragmentos do que para apresentam discontinuities na linha da tempo de suporte de dados. 

## <a name="9-encoder-redundancy"></a>9. Redundância do codificador
Para determinados eventos críticos em direto que a pedido ainda maior disponibilidade e qualidade da experiência, recomendamos que utilize codificadores ativo-ativo para obter uma ativação pós-falha totalmente integrada sem perda de dados.

![Redundância do codificador][image6]

Conforme ilustrado neste diagrama, dois grupos de codificadores enviar por push duas cópias de cada fluxo ao mesmo tempo para o serviço em direto. Esta configuração é suportada porque os serviços de multimédia pode filtrar os fragmentos duplicados com base em timestamp de ID e o fragmento de fluxo. O resultante em fluxo em direto e de arquivo é uma única cópia de todos os fluxos, que é a melhor possível agregação de duas origens. Por exemplo, num caso extremo hipotético, desde que haja um codificador (não tem de ser o mesmo) em execução em qualquer determinado ponto no tempo para cada fluxo, o fluxo resultante em direto do serviço é contínuo sem perda de dados. 

Os requisitos para este cenário são quase o mesmo que os requisitos no caso de "Codificador ativação pós-falha", com exceção de que o segundo conjunto de codificadores estão em execução ao mesmo tempo em que os codificadores primários.

## <a name="10-service-redundancy"></a>10. Redundância de serviços
Para a distribuição global altamente redundante, às vezes, tem de ter cópia de segurança em várias regiões para lidar com desastres regionais. Os clientes expandindo a topologia de "Redundância do codificador", podem optar por ter uma implementação de serviço redundante numa região diferente da que está ligado com o segundo conjunto de codificadores. Os clientes também podem trabalhar com um fornecedor de rede de entrega de conteúdos para implementar um Gestor de tráfego Global na frente as implementações de duas serviço perfeitamente encaminhar o tráfego de cliente. Os requisitos para os codificadores são os mesmos que o caso de "Redundância do codificador". A única exceção é que o segundo conjunto de codificadores tem de ser apontado em direto para outro ponto final de ingestão. O diagrama seguinte mostra esta configuração:

![Redundância de serviços][image7]

## <a name="11-special-types-of-ingestion-formats"></a>11. Tipos especiais de formatos de ingestão
Esta seção discute tipos especiais de formatos de ingestão em direto que foram criados para lidar com cenários específicos.

### <a name="sparse-track"></a>Track disperso
Ao fornecer uma apresentação de transmissão em fluxo em direto com uma experiência de cliente avançado, muitas vezes, é necessário transmitir eventos sincronizados com o tempo ou sinaliza em banda com os dados de suporte de dados principal. Um exemplo disso é a inserção de publicidade dinâmica de em direto. Este tipo de sinalização de eventos é diferente do normal de áudio/vídeo de transmissão em fluxo por causa de sua natureza dispersa. Em outras palavras, os dados de sinalização, normalmente, a não acontecem continuamente, e o intervalo pode ser difícil de prever. O conceito de controlar disperso foi projetado para ingerir e dados em banda de sinalização de difusão.

Os seguintes passos são uma implementação recomendada para a ingestão dispersa track:

1. Crie um bitstream de MP4 fragmentado separado que contenha apenas dispersas pistas sem faixas de áudio/vídeo.
1. Na **Live caixa do manifesto do servidor** conforme definido na secção 6 em [1], utilize o *parentTrackName* parâmetro para especificar o nome da faixa principal. Para obter mais informações, consulte a seção 4.2.1.2.1.2 [1].
1. Na **Live caixa do manifesto do servidor**, **manifestOutput** tem de ser definido como **verdadeiro**.
1. Devido à natureza dispersa do evento de sinalização, recomendamos o seguinte:
   
    a. No início do evento em direto, o codificador envia as caixas de cabeçalho inicial para o serviço, que permite que o serviço registar a faixa dispersa no manifesto do cliente.
   
    b. O codificador deve encerrar a solicitação de HTTP POST quando não estão a ser enviados dados. Um HTTP POST de longa execução que não envia dados pode impedir que os serviços de multimédia rapidamente a desligar do codificador em caso de um reinício de atualização ou servidor de serviço. Nestes casos, o servidor de suporte de dados está temporariamente bloqueado numa operação de recepção no socket.
   
    c. Durante a hora quando a sinalização de dados não estão disponíveis, feche o codificador SHOULD o HTTP POST do pedido. Enquanto o pedido POST estiver ativo, o codificador deve enviar dados.

    d. Ao enviar fragmentos dispersos, o codificador pode definir um cabeçalho content-length explícito, se estiver disponível.

    e. Ao enviar fragmentos dispersos com uma nova ligação, o codificador deve começar a enviar das caixas de cabeçalho, seguidas de fragmentos de novo. Isto é para casos em que ativação pós-falha ocorre entre e a nova ligação dispersa está sendo estabelecida para um novo servidor que não obteve a faixa dispersa antes.

    f. O fragmento de controlar dispersas torna-se disponíveis para o cliente quando o fragmento de controle principal correspondente que tem um valor de timestamp igual ou superior é disponibilizado para o cliente. Por exemplo, se o fragmento disperso tem um carimbo de t = 1000, espera-se que depois do cliente vê "vídeo" (supondo que o nome do controle pai é "vídeo") fragmentados timestamp 1000 ou posterior, pode transferir o fragmento dispersas t = 1000. Tenha em atenção que o sinal real pode ser utilizado para uma posição diferente na linha da tempo de apresentação para sua finalidade designada. Neste exemplo, é possível que o fragmento disperso de t = 1000 tem um payload XML, que é para a inserção de um anúncio numa posição que está a alguns segundos mais tarde.

    g. O payload dos fragmentos de controlar dispersa pode estar em diferentes formatos (como XML, texto ou binários), dependendo do cenário.

### <a name="redundant-audio-track"></a>Faixa de áudio redundante
Num cenário típico de HTTP adaptável transmissão em fluxo (por exemplo, transmissão em fluxo uniforme ou DASH), muitas vezes, existe apenas uma faixa de áudio em toda a apresentação. Ao contrário de faixas de vídeo, que tem vários níveis de qualidade para o cliente à sua escolha em condições de erro, a faixa de áudio pode ser um ponto único de falha se a ingestão da transmissão em fluxo que contém a faixa de áudio foi interrompida. 

Para resolver este problema, o Media Services suportam a ingestão em direto de faixas de áudio redundantes. A idéia é que a faixa de áudio mesmo pode ser enviada várias vezes em várias transmissões em fluxo. Embora o serviço só registra a faixa de áudio uma vez no manifesto do cliente, pode utilizar com redundância de faixas de áudio como cópias de segurança para recuperação das fragmentos de áudio se a faixa de áudio primária tem problemas. Para a ingestão de faixas de áudio redundantes, o codificador precisa:

1. Crie a faixa de áudio mesmo fragmento vários MP4 bitstreams. As faixas de áudio com redundância de tem de ser semanticamente equivalentes, com os mesmo carimbos de fragmento e serem intercambiáveis nos níveis de cabeçalho e fragmento.
1. Certifique-se de que a entrada "áudio" no Live servidor manifesto do (6 de secção [1]) é igual para todas as faixas de áudio redundantes.

Para as faixas de áudio redundantes, recomenda-se pela seguinte implementação:

1. Envie cada faixa de áudio exclusiva numa transmissão em fluxo por si só. Além disso, enviar um fluxo redundante para cada um destes fluxos de faixa de áudio, em que o segundo fluxo é diferente da primeira apenas pelo identificador no URL do HTTP POST: {protocolo} :// {endereço do servidor} / {path}/Streams({identifier}) de ponto de publicação.
1. Utilize fluxos separados para enviar as dois mais baixas vídeo velocidades de transmissão. Cada um destes fluxos também deve conter uma cópia de cada faixa de áudio exclusiva. Por exemplo, quando são suportados vários idiomas, estes fluxos devem conter faixas de áudio para cada idioma.
1. Utilize instâncias de servidor separado (codificador) para codificar e enviar os fluxos de redundantes mencionados em (1) e (2). 

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
