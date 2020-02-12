---
title: Streaming ao vivo utilizando o Azure Media Services para criar streams multibitantes  Microsoft Docs
description: Este tópico descreve como criar um Canal que recebe um único stream bitrate ao vivo a partir de um codificador no local e, em seguida, executa codificação ao vivo para fluxo de bitrate adaptativo com media services.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.assetid: 30ce6556-b0ff-46d8-a15d-5f10e4c360e2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: ec34ed723e9b0743a9a5fbbe6413659dd63b0e8a
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134915"
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>Transmissão em fluxo em direto utilizando os Serviços de Multimédia do Azure para criar transmissões com velocidade de transmissão múltipla

> [!NOTE]
> A partir de 12 de maio de 2018, os canais ao vivo não serão mais compatíveis com o protocolo de ingestão de fluxo de transporte RTP/MPEG-2. Migre do RTP/MPEG-2 para protocolos de ingestão RTMP ou MP4 fragmentado (Smooth Streaming).

## <a name="overview"></a>Descrição geral
Na Azure Media Services (AMS), um **Canal** representa um oleoduto para o processamento de conteúdos de streaming ao vivo. Um **Canal** recebe streams de entrada ao vivo de uma de duas maneiras:

* Um codificador ao vivo no local envia um fluxo de bitrate único para o Canal que está habilitado a realizar codificação ao vivo com os Media Services num dos seguintes formatos: RTMP ou Smooth Streaming (MP4 fragmentado). O Canal, em seguida, realiza live encoding da transmissão em fluxo de velocidade de transmissão única de entrada para uma transmissão em fluxo de vídeo com várias velocidades (adaptável). Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.
* Um codificador ao vivo no local envia um **RTMP** multibitado ou **Smooth Streaming** (MP4 fragmentado) para o Canal que não está habilitado a realizar codificação ao vivo com ams. Os fluxos ingeridos passam pelo **Canal**sem qualquer processamento adicional. Este método **chama-se pass-through**. Pode utilizar os seguintes codificadores ao vivo que produzem streaming liso multibitado: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco e Elemental. Os seguintes codificadores ao vivo de saída RTMP: Telestream Wirecast, Haivision, Teradek e Tricaster codificadores.  Um codificador em direto pode também enviar uma transmissão em fluxo de velocidade de transmissão única para um canal, que não está ativado para live encoding, mas tal não é recomendado. Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.

  > [!NOTE]
  > Usar um método de passagem é a forma mais económica de fazer streaming ao vivo.
  > 
  > 

A partir do lançamento do Media Services 2.10, quando criar um Canal, pode especificar de que forma pretende que o seu canal receba o fluxo de entrada e se pretende ou não que o canal realize codificação ao vivo do seu stream. Tem duas opções:

* **Nenhum** – Especifique este valor, se planeia utilizar um codificador ao vivo no local que produz fluxo multibitado (um fluxo de passagem). Neste caso, o fluxo de entrada passou para a saída sem qualquer codificação. Este é o comportamento de um Canal antes do lançamento das 2:10.  Para obter informações mais detalhadas sobre o trabalho com canais deste tipo, consulte [o streaming ao vivo com codificadores no local que criam fluxos multibitantes.](media-services-live-streaming-with-onprem-encoders.md)
* **Standard** – Escolha este valor, se planeia utilizar os Serviços de Media para codificar o seu único fluxo bitrate ao fluxo multibitado. Esteja ciente de que há um impacto de faturação para codificação ao vivo e deve lembrar-se que deixar um canal de codificação ao vivo no estado de "Running" incorrerá em taxas de faturação.  Recomenda-se que pare imediatamente os seus canais de funcionamento após o seu evento de streaming ao vivo estar completo para evitar encargos extra por hora.

> [!NOTE]
> Este tópico discute atributos de canais que estão habilitados a realizar codificação ao vivo (tipo de codificação**padrão).** Para obter informações sobre trabalhar com canais que não estão habilitados a realizar codificação ao vivo, consulte [o streaming ao vivo com codificadores no local que criam fluxos multibitáveis.](media-services-live-streaming-with-onprem-encoders.md)
> 
> Certifique-se de rever a secção [Considerações.](media-services-manage-live-encoder-enabled-channels.md#Considerations)
> 
> 

## <a name="billing-implications"></a>Implicações de faturação
Um canal de codificação ao vivo começa a faturar assim que é a transição do estado para "Running" através da API.   Também pode ver o estado no portal Azure, ou na ferramenta Azure Media Services Explorer (https://aka.ms/amse).

A tabela que se segue mostra como os estados do Canal mapeiam os estados de faturação no portal API e Azure. Os estados são ligeiramente diferentes entre a API e o Portal UX. Assim que um canal estiver no estado "Running" através da API, ou no estado "Ready" ou "Streaming" no portal Azure, a faturação estará ativa.
Para impedir que o Canal o faça mais longe, tem de parar o Canal através da API ou no portal Azure.
É responsável por parar os seus canais quando terminar com o canal de codificação ao vivo.  A não paragem de um canal de codificação resultará na continuação da faturação.

### <a id="states"></a>Estados do canal e como mapeiam para o modo de faturação
O estado atual de um Canal. Os valores possíveis incluem:

* **Parou.** Este é o estado inicial do Canal após a sua criação (a menos que o autoarranque tenha sido selecionado no portal.) Não há faturação neste estado. Neste estado, as propriedades do Canal podem ser atualizadas, mas o streaming não é permitido.
* **A partir de**. O Canal está a ser iniciado. Nenhuma cobrança ocorre nesse estado. Não são permitidas atualizações ou streaming durante este estado. Se ocorrer um erro, o Canal regressa ao estado de parada.
* **A correr.** O Canal é capaz de processar transmissões ao vivo. Está agora a cobrar o uso. Tem de parar o canal para evitar mais faturações. 
* **Parando.** O Canal está a ser detido. Nenhuma cobrança ocorre nesse estado transitório. Não são permitidas atualizações ou streaming durante este estado.
* **Apagar.** O Canal está a ser apagado. Nenhuma cobrança ocorre nesse estado transitório. Não são permitidas atualizações ou streaming durante este estado.

A tabela que se segue mostra como os estados do Canal mapeiam para o modo de faturação. 

| Estado do canal | Indicadores do Portal UI | É billing? |
| --- | --- | --- |
| A iniciar |A iniciar |Não (estado transitório) |
| A executar |Pronto (sem programas de execução)<br/>ou<br/>Streaming (pelo menos um programa de execução) |SIM, SIM. |
| A parar |A parar |Não (estado transitório) |
| Parada |Parada |Não |

### <a name="automatic-shut-off-for-unused-channels"></a>Paragem automática para canais não utilizados
A partir de 25 de janeiro de 2016, a Media Services lançou uma atualização que para automaticamente um Canal (com codificação ao vivo ativado) depois de ter estado em funcionamento num estado não utilizado durante um longo período. Isto aplica-se a Canais que não têm Programas ativos, e que não receberam um feed de contribuição de entrada por um longo período de tempo.

O limiar para um período não utilizado é nominalmente de 12 horas, mas está sujeito a alterações.

## <a name="live-encoding-workflow"></a>Fluxo de trabalho de codificação ao vivo
O diagrama seguinte representa um fluxo de trabalho de streaming ao vivo onde um canal recebe um único fluxo bitrate num dos seguintes protocolos: RTMP ou Smooth Streaming; em seguida, codifica o fluxo para um fluxo multi-bitrate. 

![Fluxo de trabalho em direto][live-overview]

## <a id="scenario"></a>Cenário comum de streaming ao vivo
Os seguintes são passos gerais referentes à criação de aplicações comuns de transmissão em fluxo em direto.

> [!NOTE]
> Atualmente, a duração máxima recomendada de um evento em direto é de 8 horas.
>
> Há um impacto de faturação para codificação ao vivo e deve lembrar-se que deixar um canal de codificação ao vivo no estado de "Running" incorrerá em taxas de faturação horárias. Recomenda-se que pare imediatamente os seus canais de funcionamento após o seu evento de streaming ao vivo estar completo para evitar encargos extra por hora. 

1. Ligue uma câmara de vídeo a um computador. Lance e configure um codificador ao vivo no local que possa lançar um **único** fluxo bitrate num dos seguintes protocolos: RTMP ou Smooth Streaming. 

    Este passo também pode ser realizado depois de criar o Canal.
2. Crie e inicie um Canal. 
3. Obtenha o URL de inserção do Canal. 

    O URL de inserção é utilizado pelo codificador em direto para enviar a transmissão para o Canal.
4. Obtenha o URL de pré-visualização do Canal. 

    Utilize este URL para verificar se o canal está a receber corretamente a transmissão em fluxo em direto.
5. Criar um programa. 

    Ao utilizar o portal Azure, a criação de um programa também cria um ativo. 

    Ao utilizar .NET SDK ou REST, precisa de criar um ativo e especificar para utilizar este ativo ao criar um Programa. 
6. Publique o ativo associado ao programa.   

    >[!NOTE]
    >Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. O ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 

7. Inicie o programa quando estiver pronto para começar a transmissão em fluxo e o arquivamento.
8. Opcionalmente, o codificador em direto pode ser indicado para iniciar um anúncio. O anúncio é inserido na transmissão de saída.
9. Pare o programa sempre que pretender interromper a transmissão e arquivar o evento.
10. Elimine o Programa (e, opcionalmente, elimine o elemento).   

> [!NOTE]
> É muito importante não esquecer de parar um canal de codificação ao vivo. Esteja ciente de que há um impacto de faturação de hora em hora para codificação ao vivo e deve lembrar-se que deixar um canal de codificação ao vivo no estado de "Running" incorrerá em taxas de faturação.  Recomenda-se que pare imediatamente os seus canais de funcionamento após o seu evento de streaming ao vivo estar completo para evitar encargos extra por hora. 
> 
> 

## <a id="channel"></a>Configurações de entrada (ingestão) do canal
### <a id="Ingest_Protocols"></a>Protocolo de streaming ingest
Se o **Tipo Encoder** estiver definido para **Standard,** as opções válidas são:

* **RTMP** bitrate único
* **MP4 fragmentado de** bitrate simples (streaming suave)

#### <a id="single_bitrate_RTMP"></a>RTMP bitrate único
Considerações:

* O fluxo de entrada não pode conter vídeo multibitado
* O fluxo de vídeo deve ter uma bitrate média inferior a 15 Mbps
* O fluxo de áudio deve ter uma bitrate média abaixo de 1 Mbps
* Seguem-se os códigos suportados:
* MPEG-4 AVC / H.264 Vídeo
* Linha de Base, Main, Alto Perfil (8 bits 4:2:0)
* Perfil alto 10 (10-bit 4:2:0)
* Perfil alto 422 (10-bit 4:2:2)
* Áudio MPEG-2 AAC-LC
* Mono, Stereo, Surround (5.1, 7.1)
* Taxa de amostragem de 44,1 kHz
* Embalagem ADTS estilo MPEG-2
* Os codificadores recomendados incluem:
* Difusão telestream
* Flash Media Live Encoder

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>MP4 fragmentado de velocidade de transmissão única (Transmissão em Fluxo Uniforme)
Caso de utilização típica:

Use no local codificadores ao vivo de fornecedores como Elemental Technologies, Ericsson, Ateme, Envivio para enviar o fluxo de entrada através da internet aberta para um centro de dados Azure próximo.

Considerações:

O mesmo que para [o RTMP bitrate single.](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP)

#### <a name="other-considerations"></a>Outras considerações
* Não é possível alterar o protocolo de entrada enquanto o Canal ou os seus programas associados estiverem em execução. Se necessitar de protocolos diferentes, deve criar canais separados para cada protocolo de entrada.
* A resolução máxima para o fluxo de vídeo que chega é 1920x1080, e no máximo 60 campos/segundo se entrelaçados, ou 30 fotogramas/segundo se progressivos.

### <a name="ingest-urls-endpoints"></a>URLs ingerir (pontos finais)
Um Canal fornece um ponto final de entrada (URL ingerir) que especifica no codificador ao vivo, para que o codificador possa empurrar os streams para os seus Canais.

Pode obter os URLs ingerir assim que criar um Canal. Para obter estes URLs, o Canal não tem que estar no estado **de Execução.** Quando estiver pronto para começar a empurrar dados para o Canal, deve estar no estado **de Execução.** Assim que o Canal começar a ingerir dados, pode pré-visualizar o seu fluxo através do URL de pré-visualização.

Tem a opção de ingerir o fluxo ao vivo de MP4 fragmentado (Smooth Streaming) sobre uma ligação SSL. Para ingerir sobre SSL, certifique-se de atualizar a URL de ingestão para HTTPS. Atualmente, a AMS não suporta a SSL com domínios personalizados.  

### <a name="allowed-ip-addresses"></a>Endereços IP permitidos
Pode definir os endereços IP que podem publicar vídeo para este canal. Os endereços IP autorizados podem ser especificados como um único endereço IP (por exemplo, «10.0.0.1»), uma gama IP que utiliza um endereço IP e uma máscara de sub-rede CIDR (por exemplo, '10.0.0.1/22'), ou uma gama IP utilizando um endereço IP e uma máscara de sub-rede decimal pontilhada (por exemplo, '10.0.0.1 (255.255.252.0)»).

Se não for especificado qualquer endereço IP e não existir nenhuma definição de regra, então, não será permitido qualquer endereço IP. Para permitir um endereço IP, crie uma regra e defina 0.0.0.0/0.

## <a name="channel-preview"></a>Pré-visualização do canal
### <a name="preview-urls"></a>URLs de pré-visualização
Os canais fornecem um ponto final de pré-visualização (URL de pré-visualização) que utiliza para pré-visualizar e validar o seu fluxo antes de continuar a processar e a entregar.

Pode obter o URL de pré-visualização quando criar o canal. Para obter o URL, o canal não tem que estar no estado **de Execução.**

Assim que o Canal começar a ingerir dados, pode pré-visualizar o seu fluxo.

> [!NOTE]
> Atualmente, o fluxo de pré-visualização só pode ser entregue em formato MP4 fragmentado (Smooth Streaming), independentemente do tipo de entrada especificado.  Pode utilizar um jogador hospedado no portal Azure para visualizar o seu fluxo.
> 
> 

### <a name="allowed-ip-addresses"></a>Endereços IP permitidos
Pode definir os endereços IP que podem ligar-se ao ponto final de pré-visualização. Se não forem especificados endereços IP, será permitido qualquer endereço IP. Os endereços IP autorizados podem ser especificados como um único endereço IP (por exemplo, «10.0.0.1»), uma gama IP que utiliza um endereço IP e uma máscara de sub-rede CIDR (por exemplo, '10.0.0.1/22'), ou uma gama IP utilizando um endereço IP e uma máscara de sub-rede decimal pontilhada (por exemplo, '10.0.0.1 (255.255.252.0)»).

## <a name="live-encoding-settings"></a>Definições de codificação ao vivo
Esta secção descreve como as definições para o codificador ao vivo dentro do Canal podem ser ajustadas, quando o Tipo de **Codificação** de um Canal é definido para **Standard**.

> [!NOTE]
> O seu feed de contribuição só pode conter uma única faixa de áudio – a ingestão de várias faixas áudio não é suportada atualmente. Ao ecodificar ao vivo com [códigos ao vivo no local,](media-services-live-streaming-with-onprem-encoders.md)pode enviar um feed de contribuição no protocolo Smooth Streaming contendo múltiplas faixas de áudio.
> 
> 

### <a name="ad-marker-source"></a>Fonte de marcador de anúncios
Pode especificar a fonte dos sinais de marcadores de anúncios. O valor predefinido é **Api**, que indica que o codificador ao vivo dentro do Canal deve ouvir um **API ad marker asíncrono**.

### <a name="cea-708-closed-captions"></a>CEA 708 Legendas fechadas
Uma bandeira opcional que diz ao codificador ao vivo para ignorar quaisquer dados de legendas CEA 708 incorporados no vídeo que está a chegar. Quando a bandeira estiver definida como falsa (predefinido), o codificador detetará e reintroduzirá os dados CEA 708 nos streams de vídeo de saída.

#### <a name="index"></a>Índice
Recomenda-se o envio de um único fluxo de transporte de programas (SPTS). Se o fluxo de entrada contiver vários programas, o codificador ao vivo dentro do Canal analisa a tabela de mapas do programa (PMT) na entrada, identifica as inputs que têm um nome de tipo de fluxo de MPEG-2 AAC ADTS ou AC-3 System-A ou AC-3 System-B ou MPEG-2 Private PES ou MPEG-1 Áudio ou MPEG-2 Áudio, e organiza-os na ordem especificada no PMT. O índice de base zero é então usado para captar a entrada n-th nesse acordo.

#### <a name="language"></a>Idioma
O identificador linguístico do fluxo de áudio, em conformidade com a ISO 639-2, como o ENG. Se não estiver presente, o padrão é UND (indefinido).

### <a id="preset"></a>Predefinição do sistema
Especifica o predefinido a ser utilizado pelo codificador vivo dentro deste Canal. Atualmente, o único valor permitido é **o Default720p** (predefinição).

**O padrão720p** codificará o vídeo nas seguintes 6 camadas.

#### <a name="output-video-stream"></a>Fluxo de vídeo de saída

| BitRate | Largura | Altura | MaxFPS | Perfil | Nome do fluxo de saída |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |High |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |High |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |High |Video_704x396_1350kbps |
| 850 |512 |288 |30 |High |Video_512x288_850kbps |
| 550 |384 |216 |30 |High |Video_384x216_550kbps |
| 200 |340 |192 |30 |High |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>Fluxo de áudio de saída

O áudio é codificado para a aparelhagem AAC-LC a 128 kbps, taxa de amostragem de 48 kHz.

## <a name="signaling-advertisements"></a>Anúncios de sinalização
Quando o seu Canal tem a Live Encoding ativada, tem um componente no seu pipeline que está a processar vídeos e pode manipulá-lo. Pode fazer sinal para que o Canal insira ardósias e/ou anúncios no fluxo de bitrate adaptativo de saída. As ardósias ainda são imagens que pode usar para encobrir a entrada de alimentos ao vivo em certos casos (por exemplo, durante uma pausa comercial). Os sinais publicitários são sinais sincronizados no tempo que incorporano no fluxo de saída para dizer ao leitor de vídeo para tomar medidas especiais – como mudar para um anúncio no momento apropriado. Consulte este [blog](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) para obter uma visão geral do mecanismo de sinalização SCTE-35 utilizado para o efeito. Abaixo está um cenário típico que você poderia implementar no seu evento ao vivo.

1. Faça com que os seus espectadores tenham uma imagem pré-EVENTO antes do início do evento.
2. Faça com que os seus espectadores tenham uma imagem pós-EVENTO após o final do evento.
3. Os seus espectadores obtêm uma imagem ERROR-EVENT se houver algum problema durante o evento (por exemplo, falha de energia no estádio).
4. Envie uma imagem AD-BREAK para ocultar o feed do evento ao vivo durante uma pausa comercial.

Seguem-se as propriedades que pode definir ao sinalizar anúncios. 

### <a name="duration"></a>Duração
A duração, em segundos, da pausa comercial. Este tem de ser um valor positivo não nulo para iniciar a rutura comercial. Quando uma rutura comercial está em andamento, e a duração é definida para zero com o CueId que corresponde à pausa comercial em curso, então essa rutura é cancelada.

### <a name="cueid"></a>Cueid
Um ID único para a pausa comercial, a utilizar por aplicação a jusante para tomar as medidas adequadas. Tem de ser um inteiro positivo. Pode definir este valor para qualquer inteiro positivo aleatório ou utilizar um sistema a montante para rastrear os Cue Ids. Certifique-se de normalizar quaisquer IDs para inteiros positivos antes de submeter através da API.

### <a name="show-slate"></a>Mostrar ardósia
Opcional. Sinaliza o codificador ao vivo para mudar para a imagem [de ardósia padrão](media-services-manage-live-encoder-enabled-channels.md#default_slate) durante uma pausa comercial e ocultar o feed de vídeo que está a chegar. O áudio também é silenciado durante a ardósia. O predefinido é **falso.** 

A imagem utilizada será a especificada através da propriedade id de ardósia padrão no momento da criação do canal. A ardósia será esticada para se adaptar ao tamanho da imagem do visor. 

## <a name="insert-slate--images"></a>Insira imagens de ardósia
O codificador ao vivo dentro do Canal pode ser sinalizado para mudar para uma imagem de ardósia. Também pode ser sinalizado para acabar com uma ardósia em curso. 

O codificador ao vivo pode ser configurado para mudar para uma imagem de ardósia e esconder o sinal de vídeo que entra em determinadas situações – por exemplo, durante uma pausa de anúncios. Se tal ardósia não estiver configurada, o vídeo de entrada não é mascarado durante a rutura do anúncio.

### <a name="duration"></a>Duração
A duração da ardósia em segundos. Este tem de ser um valor positivo não nulo para iniciar a ardósia. Se houver uma ardósia em curso, e for especificada uma duração de zero, então essa ardósia em curso será terminada.

### <a name="insert-slate-on-ad-marker"></a>Insira ardósia no marcador de anúncios
Quando definido como verdadeiro, esta configuração configura o codificador vivo para inserir uma imagem de ardósia durante uma rutura de anúncio. O valor predefinido é true. 

### <a id="default_slate"></a>Id de ativo de ardósia padrão

Opcional. Especifica o Id de Ativo do Ativo de Serviços de Media que contém a imagem de ardósia. O padrão é NULL. 


> [!NOTE] 
> Antes de criar o Canal, a imagem de ardósia com os seguintes constrangimentos deve ser carregada como um ativo dedicado (nenhum outro ficheiro deve estar neste ativo). Esta imagem só é utilizada quando o codificador vivo está a inserir uma ardósia devido a uma rutura de anúncio, ou foi explicitamente sinalizada para inserir uma ardósia. Atualmente não existe qualquer opção de usar uma imagem personalizada quando o codificador ao vivo entra num estado de "sinal de entrada perdido". Pode votar nesta funcionalidade [aqui.](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel)

* No máximo, 1920x1080 em resolução.
* No máximo 3 Mbytes em tamanho.
* O nome do ficheiro deve ter uma extensão de *.jpg.
* A imagem deve ser enviada para um Ativo como o único AssetFile nesse Ativo e este AssetFile deve ser marcado como o ficheiro principal. O Ativo não pode ser encriptado.

Se a **ficha predefinida Asset Id** não for especificada e inserir ardósia no marcador de **anúncios** é definida como **verdadeira**, uma imagem padrão do Azure Media Services será usada para ocultar o fluxo de vídeo de entrada. O áudio também é silenciado durante a ardósia. 

## <a name="channels-programs"></a>Programas do Canal
Um canal está associado a programas que permitem controlar a publicação e armazenamento de segmentos numa transmissão em fluxo em direto. Canais gerem Programas. A relação Canal e Programa é muito semelhante aos meios tradicionais onde um Canal tem um fluxo constante de conteúdos e um programa é traçado para algum evento cronometrado nesse Canal.

Pode especificar o número de horas que pretenda manter o conteúdo gravado para o programa através da configuração da duração da **Janela de Arquivo**. Este valor pode ser definido a partir de um mínimo de 5 minutos até um máximo de 25 horas. O comprimento da janela de arquivo também dita o número máximo de tempo que os clientes podem procurar no tempo a partir da posição atual ao vivo. Os programas podem ser executados durante o período de tempo especificado, mas o conteúdo que se situe atrás da duração da janela é continuamente descartado. O valor desta propriedade também determina durante quanto tempo os manifestos dos clientes podem aumentar.

Cada programa está associado a um Ativo que armazena o conteúdo transmitido. Um ativo é mapeado para um contentor de blocos na conta de Armazenamento Azure e os ficheiros do ativo são armazenados como bolhas nesse contentor. Para publicar o programa para que os seus clientes possam ver o fluxo, tem de criar um localizador OnDemand para o ativo associado. Ter este localizador irá permitir compilar um URL de transmissão em fluxo que pode fornecer aos seus clientes.

Um Canal suporta até três programas em execução simultaneamente para que possa criar vários arquivos do mesmo fluxo de entrada. Isto permite publicar e arquivar diferentes partes de um evento, conforme necessário. Por exemplo, os seus requisitos de negócios devem arquivar 6 horas de um programa, mas difundir apenas os últimos 10 minutos. Para tal, tem de criar dois programas em execução em simultâneo. Um programa está definido para arquivar 6 horas do evento, mas o programa não está publicado. O outro programa está definido para arquivar durante 10 minutos e este está publicado.

Não deve reutilizar programas existentes para novos eventos. Em vez disso, crie e inicie um novo programa para cada evento, conforme descrito na secção de Aplicações de Streaming Ao Vivo de Programação.

Inicie o programa quando estiver pronto para começar a transmissão em fluxo e o arquivamento. Pare o programa sempre que pretender interromper a transmissão e arquivar o evento. 

Para eliminar conteúdo arquivado, pare e elimine o programa e, em seguida, elimine o elemento associado. Não é possível eliminar um elemento se este é utilizado por um programa; o programa deve ser eliminado primeiro. 

Mesmo depois de parar e eliminar o programa, os utilizadores conseguirão transmitir o seu conteúdo arquivado como um vídeo a pedido até que elimine o elemento.

Se pretende manter o conteúdo arquivado, mas não o quer manter disponível para transmissão em fluxo, elimine o localizador de transmissão em fluxo.

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>Obtendo uma pré-visualização de uma miniatura de um feed ao vivo
Quando a Live Encoding está ativada, pode agora obter uma pré-visualização do feed ao vivo à medida que chega ao Canal. Esta pode ser uma ferramenta valiosa para verificar se o seu feed ao vivo está realmente a chegar ao Canal. 

## <a id="states"></a>Estados do canal e como os Estados mapeiam para o modo de faturação
O estado atual de um Canal. Os valores possíveis incluem:

* **Parou.** Este é o estado inicial do Canal da Mancha após a sua criação. Neste estado, as propriedades do Canal podem ser atualizadas, mas o streaming não é permitido.
* **A partir de**. O Canal está a ser iniciado. Não são permitidas atualizações ou streaming durante este estado. Se ocorrer um erro, o Canal regressa ao estado de parada.
* **A correr.** O Canal é capaz de processar transmissões ao vivo.
* **Parando.** O Canal está a ser detido. Não são permitidas atualizações ou streaming durante este estado.
* **Apagar.** O Canal está a ser apagado. Não são permitidas atualizações ou streaming durante este estado.

A tabela que se segue mostra como os estados do Canal mapeiam para o modo de faturação. 

| Estado do canal | Indicadores do Portal UI | Faturado? |
| --- | --- | --- |
| A iniciar |A iniciar |Não (estado transitório) |
| A executar |Pronto (sem programas de execução)<br/>ou<br/>Streaming (pelo menos um programa de execução) |Sim |
| A parar |A parar |Não (estado transitório) |
| Parada |Parada |Não |

> [!NOTE]
> Atualmente, a média de início do Canal é de cerca de 2 minutos, mas às vezes pode demorar até 20 minutos. As resets do canal podem demorar até 5 minutos.
> 
> 

## <a id="Considerations"></a>Considerações
* Quando um tipo de codificação **Standard** experimenta uma perda de alimentação de origem/contribuição de entrada, compensa-o substituindo o vídeo/áudio de origem por uma ardósia de erro e silêncio. O Canal continuará a emitir uma ardósia até que o feed de entrada/contribuição retome. Recomendamos que um canal ao vivo não seja deixado em tal estado por mais de 2 horas. Além disso, o comportamento do Canal na reconexão de entrada não está garantido, nem o seu comportamento em resposta a um comando reset. Terá de parar o Canal, apagá-lo e criar um novo.
* Não é possível alterar o protocolo de entrada enquanto o Canal ou os seus programas associados estiverem em execução. Se necessitar de protocolos diferentes, deve criar canais separados para cada protocolo de entrada.
* Sempre que reconfigurar o codificador ao vivo, ligue para o método **Reset** no canal. Antes de redefinir o canal, tem de parar o programa. Depois de reiniciar o canal, reinicie o programa.
* Um canal só pode ser parado quando está no estado de Execução, e todos os programas no canal foram parados.
* Por predefinição, só pode adicionar 5 canais à sua conta de Media Services. Esta é uma quota suave em todas as novas contas. Para mais informações, consulte [Quotas e Limitações.](media-services-quotas-and-limitations.md)
* Não é possível alterar o protocolo de entrada enquanto o Canal ou os seus programas associados estiverem em execução. Se necessitar de protocolos diferentes, deve criar canais separados para cada protocolo de entrada.
* Só tens de ser cobrado quando o teu Canal estiver no estado **de Execução.** Para mais informações, consulte [esta](media-services-manage-live-encoder-enabled-channels.md#states) secção.
* Atualmente, a duração máxima recomendada de um evento em direto é de 8 horas. 
* Certifique-se de que tem o ponto final de streaming a partir do qual pretende transmitir conteúdo no estado **de Execução.**
* O preset de codificação utiliza a noção de "taxa de fotogramas máxima" de 30 fps. Assim, se a entrada for de 60fps/59.94i, os quadros de entrada são largados/desinterligados a 30/29,97 fps. Se a entrada for de 50fps/50i, os quadros de entrada são largados/desinterligados a 25 fps. Se a entrada for de 25 fps, a saída mantém-se em 25 fps.
* Não se esqueça de parar os canais quando terminar. Se não o fizeres, a faturação continuará.

## <a name="known-issues"></a>Problemas Conhecidos
* O tempo de arranque do canal foi melhorado para uma média de 2 minutos, mas em momentos de maior procura ainda pode demorar até 20 minutos.
* As imagens de ardósia devem estar em conformidade com as restrições [aqui](media-services-manage-live-encoder-enabled-channels.md#default_slate)descritas . Se tentar criar um Canal com uma ardósia padrão superior a 1920x1080, o pedido acabará por errar.
* Mais uma vez.... não se esqueça de parar os seus canais quando terminar o streaming. Se não o fizeres, a faturação continuará.

## <a name="need-help"></a>Precisa de ajuda?

Você pode abrir um bilhete de apoio navegando para [novo pedido](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) de apoio

## <a name="next-step"></a>Passo seguinte

Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tópicos relacionados
[Entrega de eventos de streaming ao vivo com serviços de mídia azure](media-services-overview.md)

[Crie canais que realizem codificação ao vivo de um bitrate singe para fluxo de bitrate adaptativo com portal](media-services-portal-creating-live-encoder-enabled-channel.md)

[Crie canais que realizem codificação ao vivo de um bitrate singe para fluxo de bitrate adaptativo com .NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[Gerir canais com REST API](https://docs.microsoft.com/rest/api/media/operations/channel)

[Conceitos de Serviços de Mídia](media-services-concepts.md)

[Especificação de ingest de mídia azure fragmentada MP4](../media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png

