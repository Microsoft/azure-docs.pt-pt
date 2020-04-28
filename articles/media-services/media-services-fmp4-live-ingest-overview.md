---
title: Azure Media Services fragmentado MP4 especificação de ingestão viva / Microsoft Docs
description: Esta especificação descreve o protocolo e o formato para a ingestão fragmentada de streaming baseado em MP4 para a Azure Media Services. Este documento também discute as melhores práticas para a construção de mecanismos de ingestão viva altamente redundantes e robustos.
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
ms.openlocfilehash: 507afad294e8233ea4de4130795f29925870fcdf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74888058"
---
# <a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Azure Media Services fragmentado MP4 especificação de ingestão viva 

Esta especificação descreve o protocolo e o formato para a ingestão fragmentada de streaming baseado em MP4 para a Azure Media Services. A Media Services disponibiliza um serviço de streaming ao vivo que os clientes podem usar para transmitir eventos ao vivo e transmitir conteúdos em tempo real, utilizando o Azure como plataforma cloud. Este documento também discute as melhores práticas para a construção de mecanismos de ingestão viva altamente redundantes e robustos.

## <a name="1-conformance-notation"></a>1. Notação de conformidade
As palavras-chave "MUST", "MUST NOT", "REQUIRED", "DEVE", "NÃO DEVE", "DEVE", "NÃO DEVE", "RECOMENDADO", "MAIO" e "OPCIONAL" neste documento devem ser interpretadas como são descritas no RFC 2119.

## <a name="2-service-diagram"></a>2. Diagrama de serviço
O diagrama seguinte mostra a arquitetura de alto nível do serviço de streaming ao vivo nos Serviços de Media:

1. Um codificador ao vivo empurra feeds ao vivo para canais que são criados e aprovisionados através do Azure Media Services SDK.
1. Canais, programas e pontos finais de streaming em Media Services lidam com todas as funcionalidades de streaming ao vivo, incluindo ingerir, formatação, DVR em nuvem, segurança, escalabilidade e redundância.
1. Opcionalmente, os clientes podem optar por implementar uma camada de Rede de Entrega de Conteúdos Azure entre o ponto final de streaming e os pontos finais do cliente.
1. Os pontos finais do cliente transmitem a partir do ponto final de streaming utilizando protocolos de streaming adaptivos HTTP. Exemplos incluem Microsoft Smooth Streaming, Dynamic Adaptive Streaming over HTTP (DASH, ou MPEG-DASH) e Apple HTTP Live Streaming (HLS).

![fluxo de ingest][image1]

## <a name="3-bitstream-format--iso-14496-12-fragmented-mp4"></a>3. Formato Bitstream – ISO 14496-12 MP4 fragmentado
O formato de fio para o streaming ao vivo discutido neste documento baseia-se em [ISO-14496-12]. Para uma explicação detalhada do formato MP4 fragmentado e extensões tanto para ficheiros de vídeo a pedido como para a ingestão de streaming ao vivo, consulte [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).

### <a name="live-ingest-format-definitions"></a>Definições de formato de ingestão ao vivo
A seguinte lista descreve definições de formato especial que se aplicam à ingestão ao vivo nos Serviços De Mídia Azure:

1. O **ftyp**, **Live Server Manifest Box,** e caixas **moov** DEVEM ser enviados com cada pedido (HTTP POST). Estas caixas DEVEM ser enviadas no início do fluxo e sempre que o codificador deve voltar a ligar-se para retomar a ingestão de correntes. Para mais informações, consulte a Secção 6 em [1].
1. A secção 3.3.2 em [1] define uma caixa opcional chamada **StreamManifestBox** para ingerir ao vivo. Devido à lógica de encaminhamento do equilibrador de carga Azure, a utilização desta caixa é depreciada. A caixa não deve estar presente quando ingerir nos Serviços de Comunicação Social. Se esta caixa estiver presente, os Serviços de Media ignoram-na silenciosamente.
1. A caixa **TrackFragmentExtendedHeaderBox** definida em 3.2.3.2 em [1] MUST estar presente para cada fragmento.
1. A versão 2 da caixa **TrackFragmentExtendedHeaderBox** deve ser utilizada para gerar segmentos de mídia que tenham URLs idênticos em vários datacenters. O campo de índice de fragmentos é exigido para falhas de sistemas de streaming baseados em índices, tais como o Apple HLS e o MPEG-DASH baseado em índices. Para permitir a falha do centro de dados transversais, o índice de fragmentodeve ser sincronizado através de vários codificadores e ser aumentado em 1 para cada fragmento de meio de comunicação sucessivo, mesmo através de reinícios ou falhas codificadoras.
1. A secção 3.3.6 em [1] define uma caixa chamada **MovieFragmentRandomAccessBox** **(mfra)** que pode ser enviada no final da ingestão ao vivo para indicar o fim do fluxo (EOS) para o canal. Devido à lógica ingénica dos Serviços de Media, a utilização do EOS é depreciada, e a caixa **de mfra** para ingestão ao vivo NÃO deve ser enviada. Se for enviado, os Serviços de Media ignoram-no silenciosamente. Para repor o estado do ponto de ingerir, recomendamos que utilize o [Reset do Canal](https://docs.microsoft.com/rest/api/media/operations/channel#reset_channels). Recomendamos também que utilize o [Programa Stop](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) para terminar uma apresentação e um stream.
1. A duração do fragmento MP4 deve ser constante, para reduzir o tamanho dos manifestos do cliente. Uma duração constante do fragmento de MP4 também melhora a heurística de descarregamento do cliente através da utilização de tags repetidas. A duração de MAIO oscila para compensar as taxas de fotogramas não-inteiros.
1. A duração do fragmento MP4 deve ser entre aproximadamente 2 e 6 segundos.
1. Os selos e índices de fragmento MP4 `fragment_index`**(TrackFragmentExtendedHeaderBox** `fragment_ absolute_ time` e ) devem chegar em ordem crescente. Embora os Serviços de Media seja resistente a duplicar fragmentos, tem capacidade limitada para reordenar fragmentos de acordo com a linha temporal dos meios de comunicação.

## <a name="4-protocol-format--http"></a>4. Formato protocole - HTTP
A ingestão ao vivo fragmentada de MP4 baseada em MP4 para os Serviços de Media utiliza um pedido de LONGA duração http post padrão para transmitir dados codificados de meios de comunicação que são embalados em formato MP4 fragmentado para o serviço. Cada HTTP POST envia um bitstream MP4 fragmentado completo ("stream"), começando desde o início com caixas de cabeçalho **(ftyp,** **Live Server Manifest Box,** e caixas **de moov),** e continuando com uma sequência de fragmentos (caixas de**mdat** e **mdat).** Para sintaxe de URL para o pedido HTTP POST, consulte a secção 9.2 em [1]. Um exemplo do URL POST é: 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

### <a name="requirements"></a>Requisitos
Aqui estão os requisitos detalhados:

1. O codificador deve iniciar a transmissão enviando um pedido HTTP POST com um "corpo" vazio (comprimento de conteúdo zero) utilizando o mesmo URL de ingestão. Isto pode ajudar o codificador a detetar rapidamente se o ponto final de ingestão viva é válido, e se existem alguma autenticação ou outras condições necessárias. Por protocolo HTTP, o servidor não pode enviar de volta uma resposta HTTP até que todo o pedido, incluindo o corpo POST, seja recebido. Dada a natureza de longa duração de um evento ao vivo, sem este passo, o codificador pode não ser capaz de detetar qualquer erro até que termine o envio de todos os dados.
1. O codificador DEVE lidar com quaisquer erros ou desafios de autenticação por causa de (1). Se (1) conseguir com uma resposta de 200, continue.
1. O codificador DEVE iniciar um novo pedido HTTP POST com o fluxo MP4 fragmentado. A carga deve começar com as caixas de cabeçalho, seguidas de fragmentos. Note que as caixas **de correio**pesado , Live Server **Manifest Box**e **moov** (por esta ordem) DEVEM ser enviadas com cada pedido, mesmo que o codificador tenha de voltar a ligar-se porque o pedido anterior foi encerrado antes do fim do fluxo. 
1. O codificador DEVE utilizar codificação de transferência saqueada para upload, porque é impossível prever todo o comprimento do conteúdo do evento ao vivo.
1. Quando o evento terminar, depois de enviar o último fragmento, o codificador MUST termina graciosamente a sequência de mensagens de codificação de transferências grudadas (a maioria das pilhas de clientes HTTP manuseiam-no automaticamente). O codificador DEVE esperar que o serviço devolva o código de resposta final e, em seguida, terminar a ligação. 
1. O codificador NÃO DEVE `Events()` utilizar o substantivo conforme descrito em 9.2 em [1] para ingestão ao vivo nos Serviços de Comunicação Social.
1. Se o pedido HTTP POST terminar ou sair com um erro de TCP antes do fim do fluxo, o codificador DEVE emitir um novo pedido de POST utilizando uma nova ligação e seguir os requisitos anteriores. Além disso, o codificador DEVE reenviar os dois fragmentos MP4 anteriores para cada faixa no fluxo, e retomar sem introduzir uma descontinuidade na linha temporal dos meios de comunicação. A reenvio dos dois últimos fragmentos de MP4 para cada faixa garante que não há perda de dados. Por outras palavras, se um fluxo contiver uma faixa de áudio e vídeo, e o pedido de POST atual falhar, o codificador deve reconectar e reenviar os dois últimos fragmentos para a faixa de áudio, que foram previamente enviadas com sucesso, e os dois últimos fragmentos para a faixa de vídeo, que foram previamente enviadas com sucesso, para garantir que não há perda de dados. O codificador DEVE manter um tampão "para a frente" dos fragmentos dos meios de comunicação, que reenvia quando se reconecta.

## <a name="5-timescale"></a>5. Escala de tempo
[[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx) descreve o uso do prazo para **SmoothStreamingMedia** (Secção 2.2.2.1), **StreamElement** (Secção 2.2.2.3), **StreamFragmentElement** (Secção 2.2.2.6) e **LiveSMIL** (Secção 2.2.7.3.1). Se o valor do calendário não estiver presente, o valor predefinido utilizado é de 10.000.000 (10 MHz). Embora a especificação do formato Smooth Streaming não bloqueie o uso de outros valores de escala de tempo, a maioria das implementações codificadoras usam este valor padrão (10 MHz) para gerar dados de ingestão de streaming liso. Devido à funcionalidade [Azure Media Dynamic Packaging,](media-services-dynamic-packaging-overview.md) recomendamos que utilize um calendário de 90 KHz para streams de vídeo e 44,1 KHz ou 48,1 KHz para streams de áudio. Se forem utilizados diferentes valores de escala de tempo para diferentes fluxos, o calendário de nível de fluxo MUST será enviado. Para mais informações, consulte [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).     

## <a name="6-definition-of-stream"></a>6. Definição de "fluxo"
Stream é a unidade básica de operação na ingestão ao vivo para compor apresentações ao vivo, lidar com o streaming de falhas e cenários de despedimento. O fluxo é definido como um bitstream MP4 único e fragmentado que pode conter uma única faixa ou várias faixas. Uma apresentação completa ao vivo pode conter um ou mais streams, dependendo da configuração dos codificadores ao vivo. Os exemplos seguintes ilustram várias opções de utilização de streams para compor uma apresentação ao vivo completa.

**Exemplo:** 

Um cliente quer criar uma apresentação em streaming ao vivo que inclua os seguintes bitrates de áudio/vídeo:

Vídeo – 3000 kbps, 1500 kbps, 750 kbps

Áudio – 128 kbps

### <a name="option-1-all-tracks-in-one-stream"></a>Opção 1: Todas as faixas num só fluxo
Nesta opção, um único codificador gera todas as faixas de áudio/vídeo e, em seguida, embala-as em um bitstream MP4 fragmentado. O bitstream MP4 fragmentado é então enviado através de uma única ligação HTTP POST. Neste exemplo, há apenas um fluxo para esta apresentação ao vivo.

![Pista streams-one][image2]

### <a name="option-2-each-track-in-a-separate-stream"></a>Opção 2: Cada faixa num fluxo separado
Nesta opção, o codificador coloca uma faixa em cada bitstream MP4 fragmentado e, em seguida, coloca todos os fluxos sobre ligações HTTP separadas. Isto pode ser feito com um codificador ou com vários codificadores. A ingestão ao vivo vê esta apresentação ao vivo como composta por quatro riachos.

![Faixas separadas de fluxos][image3]

### <a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Opção 3: Agregar a faixa de áudio com a faixa de vídeo mais baixa de bitrate em um fluxo
Nesta opção, o cliente opta por agregar a faixa de áudio com a faixa de vídeo mais baixa num bitstream MP4 fragmentado, e deixar as outras duas faixas de vídeo como streams separados. 

![Streams-audio e faixas de vídeo][image4]

### <a name="summary"></a>Resumo
Esta não é uma lista exaustiva de todas as opções possíveis de ingestão para este exemplo. Na verdade, qualquer agrupamento de faixas em ribeiros é apoiado por ingestão ao vivo. Os clientes e os fornecedores de codificadores podem escolher as suas próprias implementações com base na complexidade da engenharia, capacidade codificadora e redundância e falhas. No entanto, na maioria dos casos, há apenas uma faixa de áudio para toda a apresentação ao vivo. Então, é importante garantir a saúde do fluxo ingerir que contém a faixa de áudio. Esta consideração resulta frequentemente em colocar a faixa de áudio no seu próprio fluxo (como na Opção 2) ou agregar com a faixa de vídeo mais baixa (como na Opção 3). Além disso, para uma melhor redundância e tolerância a falhas, enviar a mesma faixa de áudio em dois streams diferentes (Opção 2 com faixas de áudio redundantes) ou agregar a faixa de áudio com pelo menos duas das faixas de vídeo mais baixas (Opção 3 com áudio agregado em pelo menos dois streams de vídeo) é altamente recomendado para ingerir ao vivo nos Media Services.

## <a name="7-service-failover"></a>7. Falha no serviço
Dada a natureza do streaming ao vivo, um bom suporte falhado é fundamental para garantir a disponibilidade do serviço. O Media Services foi concebido para lidar com vários tipos de falhas, incluindo erros de rede, erros de servidor e problemas de armazenamento. Quando usados em conjunto com a lógica de failover adequada do lado codificador ao vivo, os clientes podem obter um serviço de streaming ao vivo altamente confiável a partir da nuvem.

Nesta secção, discutimos cenários de falhas de serviço. Neste caso, a falha acontece algures dentro do serviço, e manifesta-se como um erro de rede. Aqui ficam algumas recomendações para a implementação do codificador para o tratamento do serviço failover:

1. Utilize um intervalo de 10 segundos para estabelecer a ligação TCP. Se uma tentativa de estabelecer a ligação demorar mais de 10 segundos, aborte a operação e tente novamente. 
1. Utilize um prazo curto para enviar os pedaços de mensagem de pedido HTTP. Se a duração do fragmento de MP4 alvo for n segundos, utilize um tempo de envio entre n e 2 N segundos; por exemplo, se a duração do fragmento MP4 for de 6 segundos, utilize um tempo útil de 6 a 12 segundos. Se ocorrer um tempo limite, redefinir a ligação, abrir uma nova ligação e retomar a ingerir a corrente na nova ligação. 
1. Mantenha um tampão rolante que tenha os dois últimos fragmentos para cada pista que foram enviados com sucesso e completamente para o serviço.  Se o pedido http post para um fluxo for encerrado ou horas fora antes do fim do fluxo, abra uma nova ligação e inicie outro pedido HTTP POST, reenvie os cabeçalhos do fluxo, reenvie os dois últimos fragmentos para cada faixa e retome o fluxo sem introduzir uma descontinuidade na linha temporal dos meios de comunicação. Isto reduz a probabilidade de perda de dados.
1. Recomendamos que o codificador NÃO limite o número de tentativas para estabelecer uma ligação ou retomar o streaming após o erro do TCP.
1. Após um erro de TCP:
  
    a. A ligação atual DEVE ser fechada e uma nova ligação DEVE ser criada para um novo pedido HTTP POST.

    b. O novo URL HTTP POST DEVE ser o mesmo que o URL POST inicial.
  
    c. O novo HTTP POST MUST inclui cabeçalhos de fluxo **(ftyp,** **Live Server Manifest Box**, e caixas **moov)** que são idênticos aos cabeçalhos de fluxo no POST inicial.
  
    d. Os dois últimos fragmentos enviados para cada faixa devem ser ressentidos, e o streaming deve ser retomado sem introduzir uma descontinuidade na linha temporal dos meios de comunicação. Os selos de tempo de fragmento MP4 devem aumentar continuamente, mesmo em todos os pedidos DO HTTP POST.
1. O codificador deve encerrar o pedido HTTP POST se os dados não forem enviados a uma taxa proporcional à duração do fragmento MP4.  Um pedido HTTP POST que não envia dados pode impedir que os Serviços de Comunicação Social se desliguem rapidamente do codificador em caso de atualização de serviço. Por esta razão, o HTTP POST para faixas escassas (sinal de anúncio) deve ser de curta duração, terminando assim que o fragmento escasso é enviado.

## <a name="8-encoder-failover"></a>8. Falha do codificador
O failover do codificador é o segundo tipo de cenário de failover que precisa de ser abordado para a entrega de streaming ao vivo de ponta a ponta. Neste cenário, a condição de erro ocorre no lado do codificador. 

![codificar failover][image5]

As seguintes expectativas aplicam-se a partir do ponto final de ingestão ao vivo quando o descodificador falha:

1. Deve ser criada uma nova instância codificadora para continuar a transmitir, como ilustrado no diagrama (Stream for 3000k video, com linha tracejada).
1. O novo codificador DEVE utilizar o mesmo URL para pedidos HTTP POST como a instância falhada.
1. O pedido de CORREIO do novo codificador DEVE incluir as mesmas caixas de cabeçalho MP4 fragmentadas que a instância falhada.
1. O novo codificador DEVE ser devidamente sincronizado com todos os outros codificadores em execução para a mesma apresentação ao vivo para gerar amostras de áudio/vídeo sincronizadas com limites de fragmentoalinhados.
1. O novo fluxo DEVE ser semanticamente equivalente com o fluxo anterior, e permutável nos níveis de cabeçalho e fragmento.
1. O novo codificador deve tentar minimizar a perda de dados. Os `fragment_absolute_time` `fragment_index` fragmentos dos meios de comunicação devem aumentar a partir do ponto em que o codificador parou pela última vez. O `fragment_absolute_time` `fragment_index` e deve aumentar de forma contínua, mas é permitido introduzir uma descontinuidade, se necessário. A Media Services ignora fragmentos que já recebeu e processou, por isso é melhor errar do lado da reenvio de fragmentos do que introduzir descontinuidades na linha temporal dos media. 

## <a name="9-encoder-redundancy"></a>9. Redundância codificadora
Para certos eventos críticos ao vivo que exigem ainda maior disponibilidade e qualidade de experiência, recomendamos que utilize codificadores redundantes ativos ativos para obter falhas perfeitas sem perda de dados.

![redundância codificadora][image6]

Como ilustrado neste diagrama, dois grupos de codificadores empurram duas cópias de cada fluxo simultaneamente para o serviço ao vivo. Esta configuração é suportada porque os Serviços de Media podem filtrar fragmentos duplicados com base no ID de fluxo e na marca de tempo de fragmento. O fluxo e arquivo ao vivo resultantes é uma única cópia de todos os streams que é a melhor agregação possível das duas fontes. Por exemplo, num caso hipotético extremo, desde que exista um codificador (não tem de ser o mesmo) a funcionar em qualquer momento para cada fluxo, o fluxo ao vivo resultante do serviço é contínuo sem perda de dados. 

Os requisitos para este cenário são quase os mesmos que os requisitos no caso "Falha de codificador", com a exceção de que o segundo conjunto de codificadores está a funcionar ao mesmo tempo que os codificadores primários.

## <a name="10-service-redundancy"></a>10. Redundância de serviço
Para uma distribuição global altamente redundante, por vezes é preciso ter apoio transversal para lidar com catástrofes regionais. Expandindo-se na topologia de "Redundância Codificadora", os clientes podem optar por ter uma implantação de serviço redundante numa região diferente que esteja ligada ao segundo conjunto de codificadores. Os clientes também podem trabalhar com um fornecedor da Rede de Entrega de Conteúdos para implementar um Global Traffic Manager em frente às duas implementações de serviços para encaminhar sem problemas o tráfego do cliente. Os requisitos para os codificadores são os mesmos que o caso "Redundância Codificadora". A única exceção é que o segundo conjunto de codificadores precisa de ser apontado para um ponto final diferente. O diagrama seguinte mostra esta configuração:

![redundância de serviço][image7]

## <a name="11-special-types-of-ingestion-formats"></a>11. Tipos especiais de formatos de ingestão
Esta secção discute tipos especiais de formatos de ingestão ao vivo que são projetados para lidar com cenários específicos.

### <a name="sparse-track"></a>Pista escassa
Ao apresentar uma apresentação em streaming ao vivo com uma experiência de cliente rica, muitas vezes é necessário transmitir eventos sincronizados no tempo ou sinais em banda com os principais dados dos meios de comunicação. Um exemplo disso é a inserção dinâmica de anúncios ao vivo. Este tipo de sinalização de evento é diferente do streaming regular de áudio/vídeo devido à sua natureza escassa. Por outras palavras, os dados de sinalização geralmente não acontecem continuamente, e o intervalo pode ser difícil de prever. O conceito de faixa escassa foi projetado para ingerir e transmitir dados de sinalização em banda.

Os seguintes passos são uma implementação recomendada para ingerir faixa escassa:

1. Crie um bitstream MP4 fragmentado separado que contenha apenas faixas escassas, sem faixas de áudio/vídeo.
1. Na **Caixa de Manifesto** do Servidor Vivo, tal como definida na Secção 6 em [1], utilize o parâmetro *parentTrackName* para especificar o nome da faixa-mãe. Para mais informações, consulte a secção 4.2.1.2.1.2 em [1].
1. Na Caixa de Manifesto do **Servidor Vivo,** **manifestaçãoA saída** deve ser definida como **verdadeira**.
1. Dada a escassa natureza do evento de sinalização, recomendamos o seguinte:
   
    a. No início do evento ao vivo, o codificador envia as caixas iniciais para o serviço, o que permite ao serviço registar a faixa escassa no manifesto do cliente.
   
    b. O codificador deve encerrar o pedido HTTP POST quando os dados não forem enviados. Um POST http de longa duração que não envia dados pode impedir que os Serviços de Media se desliguem rapidamente do codificador em caso de atualização de serviço ou de reinicialização do servidor. Nestes casos, o servidor de mídia está temporariamente bloqueado numa operação de receção na tomada.
   
    c. Durante o período em que os dados de sinalização não estão disponíveis, o codificador deve encerrar o pedido HTTP POST. Enquanto o pedido do POST estiver ativo, o codificador deve enviar dados.

    d. Ao enviar fragmentos escassos, o codificador pode definir um cabeçalho explícito de comprimento de conteúdo, se estiver disponível.

    e. Ao enviar fragmentos escassos com uma nova ligação, o codificador deve começar a enviar das caixas de cabeçalho, seguido dos novos fragmentos. Isto é para casos em que a falha acontece no meio, e a nova ligação escassa está sendo estabelecida para um novo servidor que nunca viu a faixa escassa antes.

    f. O fragmento escasso da faixa fica disponível para o cliente quando o fragmento de faixa-mãe correspondente que tem um valor de carimbo de tempo igual ou maior é disponibilizado ao cliente. Por exemplo, se o fragmento escasso tiver um carimbo temporal de t=1000, espera-se que depois de o cliente ver "vídeo" (assumindo que o nome da faixa-mãe é "vídeo") fragmentar o carimbo de tempo 1000 ou mais, pode descarregar o fragmento escasso t=1000. Note que o sinal real poderia ser utilizado para uma posição diferente na linha temporal de apresentação para o seu fim designado. Neste exemplo, é possível que o fragmento escasso de t=1000 tenha uma carga útil XML, que é para inserir um anúncio numa posição que é alguns segundos depois.

    g. A carga útil de fragmentos de faixa escassa pode estar em diferentes formatos (como XML, texto ou binário), dependendo do cenário.

### <a name="redundant-audio-track"></a>Pista de áudio redundante
Num cenário de streaming adaptativo típico do HTTP (por exemplo, Smooth Streaming ou DASH), muitas vezes, há apenas uma faixa de áudio em toda a apresentação. Ao contrário das faixas de vídeo, que têm múltiplos níveis de qualidade para o cliente escolher em condições de erro, a faixa de áudio pode ser um único ponto de falha se a ingestão do fluxo que contém a faixa de áudio estiver quebrada. 

Para resolver este problema, a Media Services suporta a ingestão ao vivo de faixas áudio redundantes. A ideia é que a mesma faixa de áudio possa ser enviada várias vezes em diferentes streams. Embora o serviço apenas registe a faixa de áudio uma vez no manifesto do cliente, pode usar faixas de áudio redundantes como cópias de segurança para recuperar fragmentos de áudio se a faixa de áudio primária tiver problemas. Para ingerir faixas de áudio redundantes, o codificador precisa de:

1. Crie a mesma faixa de áudio em bitstreams MP4 de fragmentos múltiplos. As faixas de áudio redundantes DEVEM ser semanticamente equivalentes, com os mesmos selos de tempo fragmentos, e ser permutáveis nos níveis de cabeçalho e fragmento.
1. Certifique-se de que a entrada "áudio" no Manifesto do Servidor Vivo (Secção 6 em [1]) é a mesma para todas as faixas de áudio redundantes.

Recomenda-se a seguinte implementação para faixas áudio redundantes:

1. Envie cada faixa de áudio única num riacho por si só. Além disso, envie um fluxo redundante para cada um destes fluxos de faixas áudio, onde o segundo fluxo difere do primeiro apenas pelo identificador no URL HTTP POST: {protocol}://{server address}/{publishing point path}/Streams ({identifier}).
1. Utilize streams separados para enviar os dois bitrates de vídeo mais baixos. Cada um destes fluxos deve também conter uma cópia de cada faixa áudio única. Por exemplo, quando várias línguas são suportadas, estes fluxos devem conter faixas áudio para cada idioma.
1. Utilize instâncias separadas do servidor (codificador) para codificar e enviar os fluxos redundantes mencionados nos (1) e (2). 

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
