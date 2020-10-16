---
title: Streaming ao vivo usando a Azure Media Services para criar streams multi-bitrates Microsoft Docs
description: Este tópico descreve como configurar um Canal que recebe um único stream bitrate ao vivo a partir de um codificador no local e, em seguida, executa codificação ao vivo para fluxo bitrate adaptativo com Serviços de Media.
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
ms.openlocfilehash: 09d0e53840c2bf7a0d67c7c7fb0b224f9f77c587
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89268310"
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>Transmissão em fluxo em direto utilizando os Serviços de Multimédia do Azure para criar transmissões com velocidade de transmissão múltipla

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> A partir de 12 de maio de 2018, os canais ao vivo deixarão de suportar o protocolo de transmissão rtp/MPEG-2. Por favor, migrar de PROTOCOLOs de ingestão RTP/MPEG-2 para RTMP ou MP4 (Smooth Streaming).

## <a name="overview"></a>Descrição geral
Na Azure Media Services (AMS), um **Canal** representa um oleoduto para o processamento de conteúdos de streaming ao vivo. Um **Canal** recebe transmissões de entrada ao vivo de uma de duas maneiras:

* Um codificador ao vivo no local envia um fluxo de bitrate único para o Canal que está habilitado a realizar codificação ao vivo com os Media Services num dos seguintes formatos: RTMP ou Smooth Streaming (MP4 fragmentado). O Canal, em seguida, realiza live encoding da transmissão em fluxo de velocidade de transmissão única de entrada para uma transmissão em fluxo de vídeo com várias velocidades (adaptável). Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.
* Um codificador ao vivo no local envia um **RTMP** ou **Streaming Liso** (MP4 fragmentado) para o Canal que não está habilitado a realizar codificação ao vivo com AMS. Os riachos ingeridos passam pelo **Canal**s sem qualquer processamento adicional. Este método **chama-se passagem.** Pode utilizar os seguintes codificadores ao vivo que produzem streaming suave multi-bitrate: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco e Elemental. Os seguintes codificadores ao vivo produção RTMP: [Telestream Wirecast,](media-services-configure-wirecast-live-encoder.md)Haivision, Teradek codificadores.  Um codificador em direto pode também enviar uma transmissão em fluxo de velocidade de transmissão única para um canal, que não está ativado para live encoding, mas tal não é recomendado. Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.

  > [!NOTE]
  > A utilização de um método de passagem é a forma mais económica de fazer streaming ao vivo.
  > 
  > 

Começando com a versão Media Services 2.10, quando cria um Canal, pode especificar de que forma pretende que o seu canal receba o fluxo de entrada e se deseja ou não que o canal realize a codificação ao vivo do seu fluxo. Tem duas opções:

* **Nenhum** – Especifique este valor, se pretender utilizar um codificadora vivo no local que produza fluxo multi-bitrate (um fluxo de passagem). Neste caso, o fluxo de entrada passou para a saída sem qualquer codificação. Este é o comportamento de um Canal antes da libertação das 2.10.  Para obter informações mais detalhadas sobre o trabalho com canais deste tipo, consulte [o streaming ao vivo com codificadores no local que criam fluxos multi-bitrates](media-services-live-streaming-with-onprem-encoders.md).
* **Standard** – Escolha este valor, se planeia utilizar os Media Services para codificar o seu fluxo de live stream bitrate único para fluxo multi-bitrate. Esteja ciente de que há um impacto de faturação para codificação ao vivo e deve lembrar-se que deixar um canal de codificação ao vivo no estado "Running" irá incorrer em taxas de faturação.  Recomenda-se que pare imediatamente os seus canais de funcionamento após o evento de streaming ao vivo estar completo para evitar custos extra por hora.

> [!NOTE]
> Este tópico discute atributos de canais que estão habilitados a realizar codificação ao vivo ( Tipo de codificação**standard).** Para obter informações sobre o trabalho com canais que não estejam habilitados a realizar codificação ao vivo, consulte [o streaming ao vivo com codificadores no local que criam fluxos multi-bitrates](media-services-live-streaming-with-onprem-encoders.md).
> 
> Certifique-se de rever a secção [de Considerações.](media-services-manage-live-encoder-enabled-channels.md#Considerations)
> 
> 

## <a name="billing-implications"></a>Implicações de faturação
Um canal de codificação ao vivo começa a faturar assim que é transição do estado para "Running" através da API.   Também pode ver o estado no portal Azure, ou na ferramenta Azure Media Services Explorer https://aka.ms/amse) (

A tabela que se segue mostra como o Canal dos Estados mapeia para estados de faturação no portal API e Azure. Os estados são ligeiramente diferentes entre a API e o Portal UX. Assim que um canal estiver no estado "Running" através da API, ou no estado "Ready" ou "Streaming" no portal Azure, a faturação estará ativa.
Para impedir que o Canal o faturar ainda mais, tem de parar o Canal através da API ou do portal Azure.
É responsável por parar os seus canais quando terminar com o canal de codificação ao vivo.  A não paragem de um canal de codificação resultará na continuação da faturação.

### <a name="channel-states-and-how-they-map-to-the-billing-mode"></a><a id="states"></a>Os estados do canal e como mapeiam para o modo de faturação
O estado atual de um Canal. Valores possíveis incluem:

* **Parado.** Este é o estado inicial do Canal após a sua criação (a menos que o autostart tenha sido selecionado no portal.) Não há faturação neste estado. Neste estado, as propriedades do Canal podem ser atualizadas, mas o streaming não é permitido.
* **A partir de**. O Canal está a começar. Não há faturação neste estado. Não são permitidas atualizações ou streamings durante este estado. Se ocorrer um erro, o Canal regressa ao estado Stop.
* **A correr.** O Canal é capaz de processar transmissões ao vivo. Está agora a cobrar o uso. Tens de parar o canal para evitar mais faturações. 
* **Parar.** O Canal está a ser detido. Nenhuma faturação ocorre neste estado transitório. Não são permitidas atualizações ou streamings durante este estado.
* **Apagar.** O Canal está a ser apagado. Nenhuma faturação ocorre neste estado transitório. Não são permitidas atualizações ou streamings durante este estado.

A tabela que se segue mostra como o Channel afirma o mapa para o modo de faturação. 

| Estado do canal | Indicadores de UI do Portal | É Billing? |
| --- | --- | --- |
| A iniciar |A iniciar |Não (estado transitório) |
| Em Execução |Pronto (sem programas de execução)<br/>ou<br/>Streaming (pelo menos um programa em execução) |SIM |
| A parar |A parar |Não (estado transitório) |
| Parada |Parada |Não |

### <a name="automatic-shut-off-for-unused-channels"></a>Desligação automática para canais não reutilizados
A partir de 25 de janeiro de 2016, os Media Services lançaram uma atualização que para automaticamente um Canal (com codificação ao vivo ativada) depois de ter estado a funcionar num estado não reutilizado durante um longo período. Isto aplica-se a Canais que não têm Programas ativos, e que não receberam um feed de contribuição por um longo período de tempo.

O limiar para um período não reutilizado é nominalmente de 12 horas, mas está sujeito a alterações.

## <a name="live-encoding-workflow"></a>Fluxo de trabalho de codificação ao vivo
O diagrama a seguir representa um fluxo de trabalho de streaming ao vivo onde um canal recebe um único fluxo de bitrate num dos seguintes protocolos: RTMP ou Smooth Streaming; codifica então o fluxo para um fluxo multi-bitrate. 

![Fluxo de trabalho em direto][live-overview]

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Cenário comum de streaming ao vivo
Os seguintes são passos gerais referentes à criação de aplicações comuns de transmissão em fluxo em direto.

> [!NOTE]
> Atualmente, a duração máxima recomendada de um evento em direto é de 8 horas.
>
> Há um impacto de faturação para codificação ao vivo e deve lembrar-se que deixar um canal de codificação ao vivo no estado "Running" incorrerá em taxas de faturação de hora a hora. Recomenda-se que pare imediatamente os seus canais de funcionamento após o evento de streaming ao vivo estar completo para evitar custos extra por hora. 

1. Ligue uma câmara de vídeo a um computador. Lançar e configurar um codificadora ao vivo no local que pode lançar um **único** fluxo de bitrate num dos seguintes protocolos: RTMP ou Smooth Streaming. 

    Este passo também pode ser realizado depois de criar o Canal.
2. Crie e inicie um Canal. 
3. Obtenha o URL de inserção do Canal. 

    O URL de inserção é utilizado pelo codificador em direto para enviar a transmissão para o Canal.
4. Obtenha o URL de pré-visualização do Canal. 

    Utilize este URL para verificar se o canal está a receber corretamente a transmissão em fluxo em direto.
5. Criar um programa. 

    Ao utilizar o portal Azure, criar um programa também cria um ativo. 

    Ao utilizar .NET SDK ou REST, é necessário criar um ativo e especificar para utilizar este ativo ao criar um Programa. 
6. Publique o ativo associado ao programa.   

    >[!NOTE]
    >Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. O ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 

7. Inicie o programa quando estiver pronto para começar a transmissão em fluxo e o arquivamento.
8. Opcionalmente, o codificador em direto pode ser indicado para iniciar um anúncio. O anúncio é inserido na transmissão de saída.
9. Pare o programa sempre que pretender interromper a transmissão e arquivar o evento.
10. Elimine o Programa (e, opcionalmente, elimine o elemento).   

> [!NOTE]
> É muito importante não esquecer de parar um Canal de Codificação ao Vivo. Esteja ciente de que há um impacto de faturação de hora a hora para codificação ao vivo e deve lembrar-se que deixar um canal de codificação ao vivo no estado "Running" irá incorrer em taxas de faturação.  Recomenda-se que pare imediatamente os seus canais de funcionamento após o evento de streaming ao vivo estar completo para evitar custos extra por hora. 
> 
> 

## <a name="channels-input-ingest-configurations"></a><a id="channel"></a>Configurações de entrada (ingestão) do Canal
### <a name="ingest-streaming-protocol"></a><a id="Ingest_Protocols"></a>Ingeri protocolo de streaming
Se o **Tipo codificado for** definido como **Standard,** as opções válidas são:

* **RTMP** bitrate único
* **MP4 fragmentado único (Streaming** suave)

#### <a name="single-bitrate-rtmp"></a><a id="single_bitrate_RTMP"></a>RTMP de velocidade transmissão única
Considerações:

* O fluxo de entrada não pode conter vídeo multi-bitrate
* O fluxo de vídeo deve ter uma bitrate média abaixo de 15 Mbps
* O fluxo de áudio deve ter uma bitrate média abaixo de 1 Mbps
* Seguem-se os codecs suportados:
* VÍDEO MPEG-4 AVC / H.264
* Linha de Base, Principal, Alto Perfil (8-bit 4:2:0)
* Perfil alto de 10 (10-bit 4:2:0)
* Perfil alto 422 (10-bit 4:2:2)
* Áudio MPEG-2 AAC-LC
* Mono, Estéreo, Surround (5.1, 7.1)
* Taxa de amostragem de 44,1 kHz
* Embalagem ADTS estilo MPEG-2
* Os codificadores recomendados incluem:
* [Telestream Wirecast](media-services-configure-wirecast-live-encoder.md)
* Codificação Flash Media Live

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>MP4 fragmentado de velocidade de transmissão única (Transmissão em Fluxo Uniforme)
Caso de uso típico:

Utilize codificadores ao vivo no local de fornecedores como Elemental Technologies, Ericsson, Ateme, Envivio para enviar o fluxo de entrada através da internet aberta para um centro de dados Azure nas proximidades.

Considerações:

O mesmo que para [o rtmp bitrate único](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

#### <a name="other-considerations"></a>Outras considerações
* Não é possível alterar o protocolo de entrada enquanto o Canal ou os seus programas associados estiverem em execução. Se necessitar de protocolos diferentes, deve criar canais separados para cada protocolo de entrada.
* A resolução máxima para o fluxo de vídeo de entrada é de 1920x1080, e no máximo 60 campos/segundo se entrelaçados, ou 30 fotogramas/segundo se for progressivo.

### <a name="ingest-urls-endpoints"></a>Ingerir URLs (pontos finais)
Um Canal fornece um ponto final de entrada (ingest URL) que especifica no codificante ao vivo, para que o codificante possa empurrar os streams para os seus Canais.

Pode obter os URLs ingeridos assim que criar um Canal. Para obter estes URLs, o Canal não tem que estar no estado **de Corrida.** Quando estiver pronto para começar a empurrar dados para o Canal, deve estar no estado **de Corrida.** Assim que o Canal começar a ingerir dados, pode visualizar o seu fluxo através do URL de pré-visualização.

Tem a opção de ingerir o stream ao vivo de MP4 fragmentado (Smooth Streaming) sobre uma ligação TLS. Para ingerir sobre TLS, certifique-se de atualizar o URL de ingestão para HTTPS. Atualmente, a AMS não suporta TLS com domínios personalizados.  

### <a name="allowed-ip-addresses"></a>Endereços IP permitidos
Pode definir os endereços IP que podem publicar vídeo neste canal. Os endereços IP autorizados podem ser especificados como um único endereço IP (por exemplo, '10.0.0.1'), uma gama IP utilizando um endereço IP e uma máscara de sub-rede CIDR (por exemplo, «10.0.0.1/22»), ou uma gama DEP utilizando um endereço IP e uma máscara de sub-rede decimal pontilhada (por exemplo, '10.0.0.1(255.255.252.0)»).

Se não for especificado qualquer endereço IP e não existir nenhuma definição de regra, então, não será permitido qualquer endereço IP. Para permitir um endereço IP, crie uma regra e defina 0.0.0.0/0.

## <a name="channel-preview"></a>Pré-visualização do canal
### <a name="preview-urls"></a>URLs de pré-visualização
Os canais fornecem um ponto final de pré-visualização (URL de pré-visualização) que utiliza para visualizar e validar o seu fluxo antes de ser processado e entregue.

Pode obter o URL de pré-visualização quando criar o canal. Para obter o URL, o canal não tem que estar no estado **de Corrida.**

Assim que o Canal começar a ingerir dados, pode pré-visualizar o seu fluxo.

> [!NOTE]
> Atualmente, o fluxo de pré-visualização só pode ser entregue no formato MP4 fragmentado (Smooth Streaming), independentemente do tipo de entrada especificado.  Pode utilizar um jogador alojado no portal Azure para visualizar o seu fluxo.
> 
> 

### <a name="allowed-ip-addresses"></a>Endereços IP permitidos
Pode definir os endereços IP que podem ligar-se ao ponto final de pré-visualização. Se nenhum endereço IP for especificado, qualquer endereço IP será permitido. Os endereços IP autorizados podem ser especificados como um único endereço IP (por exemplo, '10.0.0.1'), uma gama IP utilizando um endereço IP e uma máscara de sub-rede CIDR (por exemplo, «10.0.0.1/22»), ou uma gama DEP utilizando um endereço IP e uma máscara de sub-rede decimal pontilhada (por exemplo, '10.0.0.1(255.255.252.0)»).

## <a name="live-encoding-settings"></a>Definições de codificação ao vivo
Esta secção descreve como as definições para o codificadora ao vivo dentro do Canal podem ser ajustadas, quando o Tipo de **Codificação** de um Canal está definido para **Standard**.

> [!NOTE]
> O feed de contribuição só pode conter uma única faixa áudio – a ingestão de várias faixas de áudio não é suportada atualmente. Ao fazer codificação ao vivo com [códigos de encodificar ao vivo no local,](media-services-live-streaming-with-onprem-encoders.md)pode enviar um feed de contribuição no protocolo Smooth Streaming contendo várias faixas de áudio.
> 
> 

### <a name="ad-marker-source"></a>Fonte de marcador de anúncios
Pode especificar a fonte para sinais de marcadores de anúncios. O valor predefinido é **a Api,** o que indica que o codificader vivo dentro do Canal deve ouvir um API assíncrono **do Marcador de Ad**.

### <a name="cea-708-closed-captions"></a>CEA 708 Legendas Fechadas
Uma bandeira opcional que diz ao codificadora ao vivo para ignorar qualquer dado de legendas CEA 708 incorporados no vídeo de entrada. Quando a bandeira estiver definida como falsa (predefinição), o codificadora detetará e reinserirá os dados DO CEA 708 nos fluxos de vídeo de saída.

#### <a name="index"></a>Índice
Recomenda-se o envio de um único fluxo de transporte de programa (SPTS). Se o fluxo de entrada contiver vários programas, o codificadora ao vivo dentro do Canal analisa a Tabela de Mapas do Programa (PMT) na entrada, identifica as entradas que têm um nome tipo de fluxo de MPEG-2 AAC ADTS ou AC-3 System-A ou AC-3 System-B ou MPEG-2 Private PES ou MPEG-1 Audio ou MPEG-2 Audio, e organiza-as na ordem especificada no PMT. O índice baseado em zero é então usado para captar a entrada n-th nesse arranjo.

#### <a name="language"></a>Idioma
O identificador de linguagem do fluxo de áudio, em conformidade com a ISO 639-2, tal como o ENG. Se não estiver presente, o padrão é UND (indefinido).

### <a name="system-preset"></a><a id="preset"></a>Predefinição do sistema
Especifica a predefinição a ser utilizada pelo codificar vivo dentro deste Canal. Atualmente, o único valor permitido é **Default720p** (predefinição).

**O padrão 720p** codificará o vídeo nas seguintes 6 camadas.

#### <a name="output-video-stream"></a>Fluxo de vídeo de saída

| BitRate | Width | Height | MaxFPS | Perfil | Nome do fluxo de saída |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Alto |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Alto |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Alto |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Alto |Video_512x288_850kbps |
| 550 |384 |216 |30 |Alto |Video_384x216_550kbps |
| 200 |340 |192 |30 |Alto |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>Fluxo de áudio de saída

O áudio é codificado para estéreo AAC-LC a 128 kbps, taxa de amostragem de 48 kHz.

## <a name="signaling-advertisements"></a>Sinalização de Anúncios
Quando o seu Canal tem a Live Encoding ativada, tem um componente no seu pipeline que está a processar vídeo e pode manipulá-lo. Pode fazer sinal para o Canal inserir ardósias e/ou anúncios no fluxo de bitrate adaptativo de saída. As ardósias ainda são imagens que pode usar para encobrir o feed ao vivo de entrada em certos casos (por exemplo, durante uma pausa comercial). Os sinais publicitários são sinais sincronizados no tempo que incorpora no stream de saída para dizer ao leitor de vídeo para tomar medidas especiais – como mudar para um anúncio no momento apropriado. Consulte este [blog](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) para obter uma visão geral do mecanismo de sinalização SCTE-35 utilizado para este fim. Abaixo está um cenário típico que você poderia implementar no seu evento ao vivo.

1. Peça aos seus espectadores que obtenham uma imagem PRÉ-EVENT antes do início do evento.
2. Faça com que os seus espectadores obtenham uma imagem PÓS-EVENTO após o final do evento.
3. Faça com que os seus espectadores obtenham uma imagem ERROR-EVENT se houver algum problema durante o evento (por exemplo, falha de energia no estádio).
4. Envie uma imagem AD-BREAK para esconder o feed do evento ao vivo durante uma pausa comercial.

Seguem-se as propriedades que pode definir ao sinalizar anúncios. 

### <a name="duration"></a>Duração
A duração, em segundos, da pausa comercial. Isto tem de ser um valor positivo não nulo para iniciar a rutura comercial. Quando uma pausa comercial está em andamento, e a duração é definida para zero com o CueId correspondente à pausa comercial em curso, então essa rutura é cancelada.

### <a name="cueid"></a>CueId
Um ID único para a pausa comercial, a ser utilizado por aplicação a jusante para tomar as medidas adequadas. Tem de ser um inteiro positivo. Pode definir este valor para qualquer número inteiro positivo aleatório ou utilizar um sistema a montante para rastrear os Ids Cue. Certifique-se de normalizar quaisquer IDs para inteiros positivos antes de submeter através da API.

### <a name="show-slate"></a>Mostrar ardósia
Opcional. Sinaliza o codificador ao vivo para mudar para a imagem [de ardósia predefinido](media-services-manage-live-encoder-enabled-channels.md#default_slate) durante uma pausa comercial e esconder o feed de vídeo que está a chegar. O áudio também é silenciado durante a ardósia. O padrão é **falso.** 

A imagem utilizada será a especificada através da propriedade de ID de ardósia padrão no momento da criação do canal. A ardósia será esticada para se adaptar ao tamanho da imagem do visor. 

## <a name="insert-slate--images"></a>Inserir imagens de ardósia
O codificador ao vivo dentro do Canal pode ser sinalizado para mudar para uma imagem de ardósia. Também pode ser sinalizado para acabar com uma ardósia em curso. 

O codificador ao vivo pode ser configurado para mudar para uma imagem de ardósia e esconder o sinal de vídeo que chega em determinadas situações – por exemplo, durante uma pausa de anúncios. Se tal ardósia não estiver configurada, o vídeo de entrada não é mascarado durante a rutura do anúncio.

### <a name="duration"></a>Duração
A duração da ardósia em segundos. Isto tem de ser um valor positivo não zero para começar a ardósia. Se houver uma ardósia em curso, e uma duração de zero for especificada, então essa ardósia em curso será encerrada.

### <a name="insert-slate-on-ad-marker"></a>Insira ardósia no marcador de anúncios
Quando definido como verdadeiro, esta definição configura o codificader ao vivo para inserir uma imagem de ardósia durante uma rutura de anúncio. O valor predefinido é true. 

### <a name="default-slate-asset-id"></a><a id="default_slate"></a>Id de ardósia padrão

Opcional. Especifica o ID do Ativo do Ativo dos Serviços de Mídia que contém a imagem de ardósia. O incumprimento é nulo. 


> [!NOTE] 
> Antes de criar o Canal, a imagem de ardósia com os seguintes constrangimentos deve ser carregada como um ativo dedicado (nenhum outro ficheiro deve estar neste ativo). Esta imagem é utilizada apenas quando o codificadora ao vivo estiver a inserir uma ardósia devido a uma rutura de anúncio, ou foi explicitamente sinalizada para inserir uma ardósia. Atualmente, não existe opção de usar uma imagem personalizada quando o codificader vivo entra num estado de "sinal de entrada perdido". Pode votar nesta funcionalidade [aqui.](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel)

* No máximo 1920x1080 em resolução.
* No máximo 3 Mbytes de tamanho.
* O nome do ficheiro deve ter uma extensão *.jpg.
* A imagem deve ser carregada num Ativo como o único ActivoFile nesse Ativo e este AssetFile deve ser marcado como o ficheiro principal. O Ativo não pode ser encriptado.

Se a **ardósia padrão O Id** do ativo não for especificado e **inserir ardósia no marcador de anúncios** é definido como **verdadeiro**, uma imagem padrão do Azure Media Services será usada para ocultar o fluxo de vídeo de entrada. O áudio também é silenciado durante a ardósia. 

## <a name="channels-programs"></a>Programas do Canal
Um canal está associado a programas que permitem controlar a publicação e armazenamento de segmentos numa transmissão em fluxo em direto. Canais gerem Programas. A relação channel and Program é muito semelhante aos meios tradicionais onde um Canal tem um fluxo constante de conteúdo e um programa é telescópio para algum evento cronometrado nesse Canal.

Pode especificar o número de horas que pretenda manter o conteúdo gravado para o programa através da configuração da duração da **Janela de Arquivo**. Este valor pode ser definido a partir de um mínimo de 5 minutos até um máximo de 25 horas. O comprimento da janela de arquivo também dita o número máximo de tempo que os clientes podem procurar no tempo a partir da posição ao vivo atual. Os programas podem ser executados durante o período de tempo especificado, mas o conteúdo que se situe atrás da duração da janela é continuamente descartado. O valor desta propriedade também determina durante quanto tempo os manifestos dos clientes podem aumentar.

Cada programa está associado a um Ativo que armazena o conteúdo transmitido. Um ativo é mapeado para um recipiente de blocos na conta de Armazenamento Azure e os ficheiros do ativo são armazenados como bolhas nesse recipiente. Para publicar o programa para que os seus clientes possam ver o fluxo, tem de criar um localizador OnDemand para o ativo associado. Ter este localizador irá permitir compilar um URL de transmissão em fluxo que pode fornecer aos seus clientes.

Um Canal suporta até três programas em execução simultânea para que possa criar múltiplos arquivos do mesmo fluxo de entrada. Isto permite publicar e arquivar diferentes partes de um evento, conforme necessário. Por exemplo, os seus requisitos de negócios devem arquivar 6 horas de um programa, mas difundir apenas os últimos 10 minutos. Para tal, tem de criar dois programas em execução em simultâneo. Um programa está definido para arquivar 6 horas do evento, mas o programa não está publicado. O outro programa está definido para arquivar durante 10 minutos e este está publicado.

Não deve reutilizar programas existentes para novos eventos. Em vez disso, crie e inicie um novo programa para cada evento, conforme descrito na secção aplicações de streaming ao vivo de programação.

Inicie o programa quando estiver pronto para começar a transmissão em fluxo e o arquivamento. Pare o programa sempre que pretender interromper a transmissão e arquivar o evento. 

Para eliminar conteúdo arquivado, pare e elimine o programa e, em seguida, elimine o elemento associado. Não é possível eliminar um elemento se este é utilizado por um programa; o programa deve ser eliminado primeiro. 

Mesmo depois de parar e eliminar o programa, os utilizadores conseguirão transmitir o seu conteúdo arquivado como um vídeo a pedido até que elimine o elemento.

Se pretende manter o conteúdo arquivado, mas não o quer manter disponível para transmissão em fluxo, elimine o localizador de transmissão em fluxo.

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>Obtenção de uma pré-visualização de miniatura de um feed ao vivo
Quando o Live Encoding estiver ativado, pode agora obter uma pré-visualização do feed ao vivo à medida que chega ao Canal. Esta pode ser uma ferramenta valiosa para verificar se o seu feed ao vivo está realmente a chegar ao Canal. 

## <a name="channel-states-and-how-states-map-to-the-billing-mode"></a><a id="states"></a>Os estados do canal e como os estados mapeiam para o modo de faturação
O estado atual de um Canal. Valores possíveis incluem:

* **Parado.** Este é o estado inicial do Canal após a sua criação. Neste estado, as propriedades do Canal podem ser atualizadas, mas o streaming não é permitido.
* **A partir de**. O Canal está a começar. Não são permitidas atualizações ou streamings durante este estado. Se ocorrer um erro, o Canal regressa ao estado Stop.
* **A correr.** O Canal é capaz de processar transmissões ao vivo.
* **Parar.** O Canal está a ser detido. Não são permitidas atualizações ou streamings durante este estado.
* **Apagar.** O Canal está a ser apagado. Não são permitidas atualizações ou streamings durante este estado.

A tabela que se segue mostra como o Channel afirma o mapa para o modo de faturação. 

| Estado do canal | Indicadores de UI do Portal | Cobrado? |
| --- | --- | --- |
| A iniciar |A iniciar |Não (estado transitório) |
| Em Execução |Pronto (sem programas de execução)<br/>ou<br/>Streaming (pelo menos um programa em execução) |Sim |
| A parar |A parar |Não (estado transitório) |
| Parada |Parada |Não |

> [!NOTE]
> Atualmente, a média de início do Canal é de cerca de 2 minutos, mas às vezes pode demorar até 20 minutos. Os resets do canal podem demorar até 5 minutos.
> 
> 

## <a name="considerations"></a><a id="Considerations"></a>Considerações
* Quando um tipo de codificação do Canal da **Norma** experimenta uma perda de fonte de entrada/feed de contribuição, compensa-o substituindo o vídeo/áudio de origem por uma ardósia e silêncio de erro. O Canal continuará a emitir uma ardósia até que o feed de entrada/contribuição retome. Recomendamos que um canal ao vivo não seja deixado em tal estado por mais de 2 horas. Além desse ponto, o comportamento do Canal na reconexão de entradas não é garantido, nem o seu comportamento em resposta a um comando Reset. Terá de parar o Canal, eliminá-lo e criar um novo.
* Não é possível alterar o protocolo de entrada enquanto o Canal ou os seus programas associados estiverem em execução. Se necessitar de protocolos diferentes, deve criar canais separados para cada protocolo de entrada.
* Sempre que reconfigurar o codificar ao vivo, ligue para o método **Reset** no canal. Antes de reiniciar o canal, tem de parar o programa. Depois de reiniciar o canal, reinicie o programa.
* Um canal só pode ser parado quando está no estado de Execução, e todos os programas no canal foram parados.
* Por predefinição, só pode adicionar 5 canais à sua conta de Media Services. Esta é uma quota suave em todas as novas contas. Para mais informações, consulte [Quotas e Limitações.](media-services-quotas-and-limitations.md)
* Não é possível alterar o protocolo de entrada enquanto o Canal ou os seus programas associados estiverem em execução. Se necessitar de protocolos diferentes, deve criar canais separados para cada protocolo de entrada.
* Só é cobrado quando o seu Canal está no estado **de Corrida.** Para mais informações, consulte [esta](media-services-manage-live-encoder-enabled-channels.md#states) secção.
* Atualmente, a duração máxima recomendada de um evento em direto é de 8 horas. 
* Certifique-se de que tem o ponto final de streaming a partir do qual pretende transmitir conteúdos no estado **de Funcionamento.**
* A predefinição codificante utiliza a noção de "taxa máxima de fotogramas" de 30 fps. Assim, se a entrada for de 60fps/59.94i, os quadros de entrada são largados/desligados para 30/29,97 fps. Se a entrada for de 50fps/50i, os quadros de entrada são largados/desligados a 25 fps. Se a entrada for de 25 fps, a saída permanece a 25 fps.
* Não se esqueça de parar os seus canais quando terminar. Se não o fizeres, a faturação vai continuar.

## <a name="known-issues"></a>Problemas Conhecidos
* O tempo de arranque do canal foi melhorado para uma média de 2 minutos, mas em momentos de maior procura ainda pode demorar até 20 minutos.
* As imagens de ardósia devem estar em conformidade com as restrições descritas [aqui.](media-services-manage-live-encoder-enabled-channels.md#default_slate) Se tentar criar um Canal com uma ardósia padrão superior a 1920x1080, o pedido acabará por falhar.
* Mais uma vez.... não se esqueça de parar os seus canais quando terminar o streaming. Se não o fizeres, a faturação vai continuar.

## <a name="need-help"></a>Precisa de ajuda?

Você pode abrir um bilhete de apoio navegando para [novo pedido de apoio](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-step"></a>Passo seguinte

Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tópicos relacionados
[Distribuir Eventos em Direto com Media Services do Azure](media-services-overview.md)

[Criar canais que realizem codificação ao vivo de um bitrate de singe para fluxo bitrate adaptativo com portal](media-services-portal-creating-live-encoder-enabled-channel.md)

[Criar canais que realizem codificação ao vivo a partir de um bitrate de singe para fluxo bitrate adaptativo com .NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[Gerir canais com REST API](/rest/api/media/operations/channel)

[Conceitos de Serviços de Mídia](media-services-concepts.md)

[Azure Media Services Fragmentado MP4 Live Ingest Specification](../media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png
