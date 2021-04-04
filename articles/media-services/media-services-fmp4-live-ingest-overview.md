---
title: Azure Media Services fragmentou a especificação de ingestão ao vivo mp4 | Microsoft Docs
description: Esta especificação descreve o protocolo e o formato para a ingestão de streaming ao vivo fragmentada baseada em MP4 para a Azure Media Services. Este documento também discute as melhores práticas para a construção de mecanismos de ingestão viva altamente redundantes e robustos.
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
ms.openlocfilehash: 7323ae611431e1d91fd1a8471914be388fcc4712
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92019516"
---
# <a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Azure Media Services fragmentou a especificação de ingestão ao vivo mp4 

Esta especificação descreve o protocolo e o formato para a ingestão de streaming ao vivo fragmentada baseada em MP4 para a Azure Media Services. Os Media Services fornecem um serviço de streaming ao vivo que os clientes podem usar para transmitir eventos ao vivo e transmitir conteúdos em tempo real, utilizando o Azure como plataforma de nuvem. Este documento também discute as melhores práticas para a construção de mecanismos de ingestão viva altamente redundantes e robustos.

## <a name="1-conformance-notation"></a>1. Notação de conformidade
As palavras-chave "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHOULD", "SHOULD", "SHOULD", "RECOMMENDED", "MAY" e "OPTIONAL" neste documento devem ser interpretadas como estão descritas no RFC 2119.

## <a name="2-service-diagram"></a>2. Diagrama de serviço
O seguinte diagrama mostra a arquitetura de alto nível do serviço de streaming ao vivo nos Serviços de Media:

1. Um codificadores ao vivo empurra feeds ao vivo para canais que são criados e a provisionados através do Azure Media Services SDK.
1. Canais, programas e pontos finais de streaming nos Media Services lidam com todas as funcionalidades de streaming ao vivo, incluindo ingestão, formatação, DVR em nuvem, segurança, escalabilidade e redundância.
1. Opcionalmente, os clientes podem optar por implementar uma camada da Rede de Entrega de Conteúdos Azure entre o ponto final de streaming e os pontos finais do cliente.
1. Os pontos finais do cliente transmitem a partir do ponto final de streaming utilizando protocolos http adaptive streaming. Exemplos incluem o Microsoft Smooth Streaming, o Streaming Adaptativo Dinâmico sobre HTTP (DASH, ou MPEG-DASH) e o Apple HTTP Live Streaming (HLS).

![ingerir fluxo][image1]

## <a name="3-bitstream-format--iso-14496-12-fragmented-mp4"></a>3. Formato Bitstream – ISO 14496-12 MP4 fragmentado
O formato de fio para streaming vivo discutido neste documento baseia-se em [ISO-14496-12]. Para obter uma explicação detalhada do formato e extensões fragmentadas de MP4, tanto para ficheiros vídeo-on-demand como para ingestão de streaming ao vivo, consulte [[MS-SSTR]](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251).

### <a name="live-ingest-format-definitions"></a>Definições de formato de ingestão ao vivo
A lista que se segue descreve definições de formato especial que se aplicam à ingestão ao vivo nos Serviços de Media Azure:

1. A **ftyp**, **Live Server Manifest Box,** e as caixas **moov** DEVEM ser enviadas a cada pedido (HTTP POST). Estas caixas devem ser enviadas no início do fluxo e sempre que o codificadores deve voltar a ligar-se para retomar a ingestão de correntes. Para mais informações, consulte a Secção 6 em [1].
1. A secção 3.3.2 em [1] define uma caixa opcional chamada **StreamManifestBox** para ingerir ao vivo. Devido à lógica de encaminhamento do equilibrador de carga Azure, a utilização desta caixa é depreciada. A caixa NÃO deve estar presente ao ingerir nos Serviços de Comunicação Social. Se esta caixa estiver presente, os Serviços de Comunicação social ignoram-na silenciosamente.
1. A caixa **TrackFragmentExtendedHeaderBox** definida em 3.2.3.2 em [1] MUST está presente para cada fragmento.
1. A versão 2 da caixa **TrackFragmentExtendedHeaderBox** deve ser utilizada para gerar segmentos de mídia que tenham URLs idênticos em vários centros de dados. O campo de índice de fragmento é necessário para o failover de formatos de streaming baseados em índices como o Apple HLS e o MPEG-DASH baseado em índices. Para permitir a falha do centro de dados cruzados, o índice de fragmentoS DEVE ser sincronizado em vários codificadores e ser aumentado em 1 para cada fragmento de mídia sucessiva, mesmo através de reinícios ou falhas do codificador.
1. A secção 3.3.6 em [1] define uma caixa chamada **MovieFragmentRandomAccessBox** **(mfra)** que pode ser enviada no final da ingestão ao vivo para indicar o fim do fluxo (EOS) para o canal. Devido à lógica de ingestão de Serviços de Mídia, a utilização do EOS é depreciada, e a caixa **de mfra** para ingestão ao vivo NÃO deve ser enviada. Se for enviado, os Serviços de Comunicação ignoram-no silenciosamente. Para repor o estado do ponto de ingestão, recomendamos que utilize [o Channel Reset](/rest/api/media/operations/channel#reset_channels). Recomendamos também que utilize [o Program Stop](/rest/api/media/operations/program#stop_programs) para terminar uma apresentação e transmitir.
1. A duração do fragmento MP4 deve ser constante, para reduzir o tamanho dos manifestos do cliente. Uma duração constante do fragmento mp4 também melhora a heurística do download do cliente através da utilização de tags repetidas. A duração DE MAIO flutua para compensar as taxas de fotogramas não-inteiros.
1. A duração do fragmento MP4 deve estar entre aproximadamente 2 e 6 segundos.
1. Os índices e os índices de tempo de fragmentos MP4 **(TrackFragmentExtendedHeaderBox** `fragment_ absolute_ time` e ) devem chegar em ordem `fragment_index` crescente. Embora os Serviços de Comunicação Social sejam resistentes a duplicar fragmentos, tem capacidade limitada de reordenar fragmentos de acordo com a linha temporal dos meios de comunicação.

## <a name="4-protocol-format--http"></a>4. Formato protocolar – HTTP
A ingeria ao vivo fragmentada da MP4 para serviços de mídia utiliza um pedido padrão de longa duração HTTP POST para transmitir dados codificados de mídia que é embalado em formato MP4 fragmentado para o serviço. Cada HTTP POST envia um bitstream mp4 fragmentado completo ("stream"), começando desde o início com caixas de cabeçalho **(ftyp**, **Live Server Manifest Box**, e caixas **moov),** e continuando com uma sequência de fragmentos (caixas **de moof** e **mdat).** Para a sintaxe URL para o pedido HTTP POST, consulte a secção 9.2 em [1]. Um exemplo do URL post é: 

`http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)`

### <a name="requirements"></a>Requisitos
Aqui estão os requisitos detalhados:

1. O codificante DEVE iniciar a transmissão enviando um pedido HTTP POST com um "corpo" vazio (comprimento de conteúdo zero) utilizando o mesmo URL de ingestão. Isto pode ajudar o codificador a detetar rapidamente se o ponto final de ingestão ao vivo é válido, e se existem alguma autenticação ou outras condições necessárias. Por protocolo HTTP, o servidor não pode enviar uma resposta HTTP até que todo o pedido, incluindo o corpo POST, seja recebido. Dada a natureza de longo prazo de um evento ao vivo, sem este passo, o codificadora pode não ser capaz de detetar qualquer erro até que termine de enviar todos os dados.
1. O codificader MUST lida com quaisquer erros ou desafios de autenticação por causa de (1). Se (1) tiver sucesso com uma resposta de 200, continue.
1. O codificante DEVE iniciar um novo pedido HTTP POST com o fluxo MP4 fragmentado. A carga útil DEVE começar com as caixas de cabeçalho, seguidas de fragmentos. Note que o **ftyp**, **Live Server Manifest Box**, e as caixas **moov** (por esta ordem) DEVEM ser enviados a cada pedido, mesmo que o codificante tenha de voltar a ligar-se porque o pedido anterior foi encerrado antes do fim do fluxo. 
1. O codificante MUST usa codificação de transferências em pedaços para o upload, porque é impossível prever toda a duração do conteúdo do evento ao vivo.
1. Quando o evento terminar, depois de enviar o último fragmento, o codificador DEVE terminar graciosamente a sequência de mensagem de codificação de transferência em pedaços (a maioria das pilhas de clientes HTTP tratam-no automaticamente). O codificador DEVE aguardar que o serviço devolva o código de resposta final e, em seguida, termine a ligação. 
1. O codificader NÃO DEVE utilizar o `Events()` substantivo descrito em 9.2 em [1] para ingestão ao vivo nos Serviços de Comunicação Social.
1. Se o pedido HTTP POST terminar ou sair com um erro TCP antes do fim do fluxo, o codificante MUST emitiu um novo pedido DEM utilizando uma nova ligação e segue os requisitos anteriores. Adicionalmente, o codificante MUST reencamina os dois fragmentos de MP4 anteriores para cada faixa no fluxo, e retoma sem introduzir uma descontinuidade na linha temporal dos meios de comunicação. Reencaendendo os dois últimos fragmentos de MP4 para cada faixa garante que não há perda de dados. Por outras palavras, se um fluxo contiver tanto um áudio como uma faixa de vídeo, e o atual pedido de POST falhar, o codificante deve voltar a ligar e reenexá-los os dois últimos fragmentos para a faixa de áudio, que foram previamente enviados com sucesso, e os dois últimos fragmentos para a pista de vídeo, que foram previamente enviados com sucesso, para garantir que não há perda de dados. O codificader MUST mantém um tampão "para a frente" de fragmentos de mídia, que reencaminha quando reconecta.

## <a name="5-timescale"></a>5. Calendário
[[MS-SSTR]](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) descreve a utilização da escala de tempo para **SmoothStreamingMedia** (Secção 2.2.2.1), **StreamElement** (Secção 2.2.2.3), **StreamFragmentElement** (Secção 2.2.2.6) e **LiveSMIL** (Secção 2.2.7.3.1). Se o valor do calendário não estiver presente, o valor predefinido utilizado é de 10.000.000 (10 MHz). Embora a especificação do formato De Streaming Suave não bloqueie o uso de outros valores de escala de tempo, a maioria das implementações codificadores usam este valor padrão (10 MHz) para gerar dados de ingestão de streaming suave. Devido à funcionalidade [Azure Media Dynamic Packaging,](./previous/media-services-dynamic-packaging-overview.md) recomendamos que utilize um calendário de 90 KHz para streams de vídeo e 44.1 KHz ou 48.1 KHz para streams de áudio. Se forem utilizados valores de calendário diferentes para diferentes fluxos, o calendário de nível de fluxo DEVE ser enviado. Para mais informações, consulte [[MS-SSTR]](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251).     

## <a name="6-definition-of-stream"></a>6. Definição de "fluxo"
Stream é a unidade básica de operação em ingestão ao vivo para compor apresentações ao vivo, lidar com o streaming failover, e cenários de redundância. O fluxo é definido como um bitstream mp4 único e fragmentado que pode conter uma única faixa ou várias faixas. Uma apresentação ao vivo completa pode conter um ou mais streams, dependendo da configuração dos codificadores ao vivo. Os exemplos a seguir ilustram várias opções de utilização de streams para compor uma apresentação ao vivo completa.

**Exemplo:** 

Um cliente quer criar uma apresentação em streaming ao vivo que inclua os seguintes bitrates de áudio/vídeo:

Vídeo – 3000 kbps, 1500 kbps, 750 kbps

Áudio – 128 kbps

### <a name="option-1-all-tracks-in-one-stream"></a>Opção 1: Todas as faixas num só fluxo
Nesta opção, um único codificadores gera todas as faixas de áudio/vídeo e, em seguida, embala-as num bitstream mp4 fragmentado. O bitstream MP4 fragmentado é então enviado através de uma única ligação HTTP POST. Neste exemplo, há apenas um fluxo para esta apresentação ao vivo.

![Pista streams-one][image2]

### <a name="option-2-each-track-in-a-separate-stream"></a>Opção 2: Cada faixa num fluxo separado
Nesta opção, o codificader coloca uma faixa em cada bitstream mp4 fragmento e, em seguida, coloca todos os fluxos sobre ligações HTTP separadas. Isto pode ser feito com um codificadores ou com vários codificadores. A ingestão ao vivo vê esta apresentação ao vivo como composta por quatro riachos.

![Faixas separadas de fluxos][image3]

### <a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Opção 3: Agregar a faixa de áudio com a faixa de vídeo bitrate mais baixa em um fluxo
Nesta opção, o cliente opta por agregar a faixa de áudio com a faixa de vídeo bitrate mais baixa num bitstream de fragmentação MP4, e deixar as outras duas faixas de vídeo como streams separados. 

![Streams-áudio e faixas de vídeo][image4]

### <a name="summary"></a>Resumo
Esta não é uma lista exaustiva de todas as opções possíveis de ingestão para este exemplo. Na verdade, qualquer agrupamento de faixas em riachos é suportado pela ingestão ao vivo. Os clientes e os fornecedores de codificadores podem escolher as suas próprias implementações com base na complexidade da engenharia, capacidade de codificação e redundância e considerações de failover. No entanto, na maioria dos casos, há apenas uma faixa áudio para toda a apresentação ao vivo. Por isso, é importante garantir a saúde do fluxo de ingestão que contém a faixa de áudio. Esta consideração resulta frequentemente em colocar a faixa de áudio no seu próprio fluxo (como na Opção 2) ou em agrupar com a faixa de vídeo bitrate mais baixa (como na Opção 3). Além disso, para uma melhor redundância e tolerância a falhas, o envio da mesma faixa de áudio em dois streams diferentes (Opção 2 com faixas de áudio redundantes) ou a agregação da faixa de áudio com pelo menos duas das faixas de vídeo mais baixas (Opção 3 com áudio agregado em pelo menos dois streams de vídeo) é altamente recomendado para ingerir ao vivo nos Media Services.

## <a name="7-service-failover"></a>7. Falha de serviço
Dada a natureza do streaming ao vivo, o bom suporte de failover é fundamental para garantir a disponibilidade do serviço. Os Serviços de Comunicação Social são projetados para lidar com vários tipos de falhas, incluindo erros de rede, erros de servidor e problemas de armazenamento. Quando usados em conjunto com a lógica de failover adequada do lado do codificader ao vivo, os clientes podem alcançar um serviço de streaming ao vivo altamente confiável a partir da nuvem.

Nesta secção, discutimos cenários de falha de serviço. Neste caso, a falha ocorre algures dentro do serviço, e manifesta-se como um erro de rede. Aqui ficam algumas recomendações para a implementação do codificadores para o tratamento do serviço de failover:

1. Utilize um intervalo de 10 segundos para estabelecer a ligação TCP. Se uma tentativa de estabelecer a ligação demorar mais de 10 segundos, aborte a operação e tente novamente. 
1. Utilize um curto período de tempo para enviar os pedaços de mensagem de pedido HTTP. Se a duração do fragmento mp4 do alvo for de N segundos, utilize um intervalo de envio entre N e 2 N segundos; por exemplo, se a duração do fragmento MP4 for de 6 segundos, utilize um intervalo de 6 a 12 segundos. Se ocorrer um intervalo, reinicie a ligação, abra uma nova ligação e retome a ingestão de fluxos na nova ligação. 
1. Mantenha um tampão rolante que tenha os dois últimos fragmentos para cada faixa que foram enviadas com sucesso e completamente para o serviço.  Se o pedido HTTP POST para um fluxo for encerrado ou os horários fora antes do fim do fluxo, abra uma nova ligação e inicie outro pedido HTTP POST, reencaminhe os cabeçalhos de fluxo, reencaminhe os dois últimos fragmentos para cada faixa e retome o fluxo sem introduzir uma descontinuidade na linha de tempo dos meios de comunicação. Isto reduz a possibilidade de perda de dados.
1. Recomendamos que o codificadores NÃO limite o número de retrações para estabelecer uma ligação ou retomar o streaming após ocorrer um erro de TCP.
1. Após um erro de TCP:
  
    a. A ligação atual DEVE ser encerrada, e uma nova ligação DEVE ser criada para um novo pedido HTTP POST.

    b. O novo URL HTTP POST DEVE ser o mesmo que o URL post inicial.
  
    c. O novo HTTP POST DEVE incluir cabeçalhos de fluxo **(ftyp**, **Live Server Manifest Box**, e caixas **moov)** que são idênticos aos cabeçalhos de fluxo no POST inicial.
  
    d. Os dois últimos fragmentos enviados para cada via devem ser ressentidos, e o streaming deve ser retomado sem introduzir uma descontinuidade na linha temporal dos meios de comunicação. Os selos temporais de fragmentos MP4 devem aumentar continuamente, mesmo através dos pedidos HTTP POST.
1. O codificante deve encerrar o pedido HTTP POST se os dados não forem enviados a uma taxa proporcional à duração do fragmento MP4.  Um pedido HTTP POST que não envie dados pode impedir que os Serviços de Comunicação Social se desconectem rapidamente do codificadora em caso de atualização de serviço. Por esta razão, o HTTP POST para faixas escassas (sinal de anúncio) deve ser de curta duração, terminando assim que o fragmento escasso é enviado.

## <a name="8-encoder-failover"></a>8. Falha do codificação da Encoder
O failover da Codificação é o segundo tipo de cenário de failover que precisa de ser abordado para a entrega de streaming ao vivo de ponta a ponta. Neste cenário, a condição de erro ocorre no lado do codificador. 

![falha de codificação][image5]

As seguintes expectativas aplicam-se a partir do ponto final de ingestão ao vivo quando o insígnio do codificadores acontece:

1. Deve ser criado um novo caso de codificação para continuar o streaming, como ilustrado no diagrama (Stream for 3000k video, com linha tracejada).
1. O novo codificante DEVE utilizar o mesmo URL para pedidos HTTP POST como a instância falhada.
1. O novo pedido de CORREIO do codificante DEVE incluir as mesmas caixas de cabeçalho MP4 fragmentadas que a instância falhada.
1. O novo codificante DEVE ser devidamente sincronizado com todos os outros codificadores em execução para a mesma apresentação ao vivo para gerar amostras de áudio/vídeo sincronizadas com limites de fragmentos alinhados.
1. O novo fluxo DEVE ser semânticamente equivalente ao fluxo anterior, e permutável nos níveis do cabeçalho e fragmento.
1. O novo codificader DEVE tentar minimizar a perda de dados. Os `fragment_absolute_time` `fragment_index` fragmentos e dos meios de comunicação devem aumentar a partir do ponto onde o codificar parou pela última vez. O `fragment_absolute_time` e DEVE aumentar de forma `fragment_index` contínua, mas é permitido introduzir uma descontinuidade, se necessário. Os Serviços de Comunicação Ignoram fragmentos que já recebeu e processou, por isso é melhor errar do lado de fragmentos reensitados do que introduzir descontinuidades na linha temporal dos meios de comunicação. 

## <a name="9-encoder-redundancy"></a>9. Redundância encoder
Para certos eventos críticos ao vivo que exigem uma maior disponibilidade e qualidade de experiência, recomendamos que utilize codificadores redundantes ativos para alcançar um failover sem perda de dados.

![redundância codificação][image6]

Como ilustrado neste diagrama, dois grupos de codificadores empurram duas cópias de cada fluxo simultaneamente para o serviço ao vivo. Esta configuração é suportada porque os Serviços de Mídia podem filtrar fragmentos duplicados com base no ID de fluxo e na hora do fragmento. O fluxo e arquivo ao vivo resultantes é uma única cópia de todos os fluxos que é a melhor agregação possível das duas fontes. Por exemplo, num caso hipotético extremo, desde que exista um codificar (não tem de ser o mesmo) a funcionar a qualquer momento para cada fluxo, o fluxo ao vivo resultante do serviço é contínuo sem perda de dados. 

Os requisitos para este cenário são quase os mesmos que os requisitos no caso "Encoder failover", com a exceção de que o segundo conjunto de codificadores está a funcionar ao mesmo tempo que os codificadores primários.

## <a name="10-service-redundancy"></a>10. Redundância de serviço
Para uma distribuição global altamente redundante, por vezes é preciso ter apoio inter-região para lidar com desastres regionais. Expandindo-se na topologia "Encoder redundância", os clientes podem optar por ter uma implantação de serviço redundante numa região diferente que está ligada ao segundo conjunto de codificadores. Os clientes também podem trabalhar com um fornecedor de Rede de Entrega de Conteúdos para implementar um Global Traffic Manager em frente às duas implementações de serviço para encaminhar o tráfego de clientes de forma perfeita. Os requisitos para os codificadores são os mesmos que o caso "Redundância Encoder". A única exceção é que o segundo conjunto de codificadores precisa de ser apontado para um ponto final diferente. O seguinte diagrama mostra esta configuração:

![redundância de serviço][image7]

## <a name="11-special-types-of-ingestion-formats"></a>11. Tipos especiais de formatos de ingestão
Esta secção discute tipos especiais de formatos de ingestão ao vivo que são projetados para lidar com cenários específicos.

### <a name="sparse-track"></a>Pista escassa
Ao entregar uma apresentação em streaming ao vivo com uma experiência de cliente rica, muitas vezes é necessário transmitir eventos sincronizados com o tempo ou sinais em banda com os principais dados dos meios de comunicação. Um exemplo disso é a inserção dinâmica de anúncios ao vivo. Este tipo de sinalização de evento é diferente do streaming regular de áudio/vídeo devido à sua natureza escassa. Por outras palavras, os dados de sinalização geralmente não acontecem continuamente, e o intervalo pode ser difícil de prever. O conceito de faixa escassa foi projetado para ingerir e transmitir dados de sinalização em banda.

Os seguintes passos são uma implementação recomendada para ingerir faixas escassas:

1. Crie um bitstream mp4 fragmentado separado que contenha apenas faixas escassas, sem faixas de áudio/vídeo.
1. Na Caixa manifesto do **servidor ao vivo,** tal como definida na secção 6 em [1], utilize o parâmetro *parentTrackName* para especificar o nome da faixa-mãe. Para obter mais informações, consulte a secção 4.2.1.2.1.2 em [1].
1. Na **Caixa Manifesto do Servidor Ao Vivo,** **manifestoOutput** MUST deve ser definido como **verdadeiro**.
1. Dada a natureza escassa do evento de sinalização, recomendamos o seguinte:
   
    a. No início do evento ao vivo, o codificader envia as caixas iniciais de cabeçalho para o serviço, o que permite ao serviço registar a faixa escassa no manifesto do cliente.
   
    b. O codificante DEVE encerrar o pedido HTTP POST quando os dados não forem enviados. Um HTTP POST de longa duração que não envie dados pode impedir que os Serviços de Comunicação Social se desconectem rapidamente do codificadora em caso de uma atualização de serviço ou reinicialização do servidor. Nestes casos, o servidor de mídia encontra-se temporariamente bloqueado numa operação de receção na tomada.
   
    c. Durante o momento em que os dados de sinalização não estão disponíveis, o codificante DEVE encerrar o pedido HTTP POST. Enquanto o pedido do POST estiver ativo, o codificante DEVE enviar dados.

    d. Ao enviar fragmentos escassos, o codificadora pode definir um cabeçalho explícito de comprimento de conteúdo, se estiver disponível.

    e. Ao enviar fragmentos escassos com uma nova ligação, o codificar DEVE começar a enviar a partir das caixas de cabeçalho, seguido dos novos fragmentos. Isto acontece em casos em que o failover acontece entre si, e a nova ligação escassa está a ser estabelecida para um novo servidor que nunca tinha visto a faixa de sparse antes.

    f. O fragmento de faixa escassa fica disponível para o cliente quando o fragmento de pista parental correspondente que tem um valor de tempotamp igual ou maior é disponibilizado ao cliente. Por exemplo, se o fragmento escasso tiver um tempotamp de t=1000, espera-se que depois de o cliente ver "vídeo" (assumindo que o nome da faixa dos pais é "vídeo") fragmento de marcação de tempo 1000 ou mais, pode descarregar o fragmento escasso t=1000. Note que o sinal real poderia ser usado para uma posição diferente na linha de tempo de apresentação para o fim designado. Neste exemplo, é possível que o fragmento escasso de t=1000 tenha uma carga útil XML, que é para inserir um anúncio numa posição que é alguns segundos depois.

    exemplo, A carga útil de fragmentos de faixas escassas pode ser em diferentes formatos (como XML, texto ou binário), dependendo do cenário.

### <a name="redundant-audio-track"></a>Faixa áudio redundante
Num cenário típico de streaming adaptativo HTTP (por exemplo, Smooth Streaming ou DASH), muitas vezes, há apenas uma faixa áudio em toda a apresentação. Ao contrário das faixas de vídeo, que têm vários níveis de qualidade para o cliente escolher em condições de erro, a faixa de áudio pode ser um único ponto de falha se a ingestão do fluxo que contém a faixa de áudio estiver quebrada. 

Para resolver este problema, os Media Services apoiam a ingestão ao vivo de faixas de áudio redundantes. A ideia é que a mesma faixa de áudio possa ser enviada várias vezes em diferentes streams. Embora o serviço apenas registe a faixa de áudio uma vez no manifesto do cliente, pode usar faixas de áudio redundantes como cópias de segurança para recuperar fragmentos de áudio se a faixa de áudio primária tiver problemas. Para ingerir faixas áudio redundantes, o codificar precisa:

1. Crie a mesma faixa de áudio em vários bitstreams de fragmentos MP4. As faixas de áudio redundantes DEVEM ser semânticamente equivalentes, com os mesmos sinais temporais de fragmento, e ser permutáveis nos níveis do cabeçalho e fragmentos.
1. Certifique-se de que a entrada "áudio" no Manifesto do Servidor Ao Vivo (Secção 6 em [1]) é a mesma para todas as faixas de áudio redundantes.

Recomenda-se a seguinte implementação para faixas de áudio redundantes:

1. Envie cada faixa áudio única num fluxo por si só. Além disso, envie um fluxo redundante para cada uma destas transmissões de faixas de áudio, onde o segundo fluxo difere do primeiro apenas pelo identificador no URL HTTP POST: {protocol}://{server address}/{publishing point path}/Streams ({identifier}).
1. Utilize fluxos separados para enviar os dois bitrates de vídeo mais baixos. Cada um destes fluxos deve conter também uma cópia de cada faixa áudio única. Por exemplo, quando várias línguas são suportadas, estes streams DEVEM conter faixas áudio para cada idioma.
1. Utilize instâncias separadas do servidor (codificadores) para codificar e enviar os fluxos redundantes mencionados nos (1) e (2). 

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