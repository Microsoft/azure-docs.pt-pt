---
title: Transmissão ao vivo usando os serviços de mídia do Azure para criar fluxos de múltiplas taxas de bits | Microsoft Docs
description: Este tópico descreve como configurar um canal que recebe uma transmissão ao vivo de taxa de bits única de um codificador local e, em seguida, executa a codificação ativa para o fluxo de taxa de bits adaptável com os serviços de mídia.
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
ms.openlocfilehash: 32a4fde12287e06c12fac9ed13ad7a8889b49fc1
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895920"
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>Transmissão em fluxo em direto utilizando os Serviços de Multimédia do Azure para criar transmissões com velocidade de transmissão múltipla

> [!NOTE]
> A partir de 12 de maio de 2018, os canais ao vivo não serão mais compatíveis com o protocolo de ingestão de fluxo de transporte RTP/MPEG-2. Migre do RTP/MPEG-2 para protocolos de ingestão RTMP ou MP4 fragmentado (Smooth Streaming).

## <a name="overview"></a>Visão geral
No AMS (serviços de mídia do Azure), um **canal** representa um pipeline para o processamento de conteúdo de transmissão ao vivo. Um **canal** recebe fluxos de entrada ao vivo de uma das duas maneiras:

* Um codificador ao vivo local envia um fluxo de taxa de bits única para o canal que está habilitado para executar a codificação ativa com os serviços de mídia em um dos seguintes formatos: RTMP ou Smooth Streaming (MP4 fragmentado). O Canal, em seguida, realiza live encoding da transmissão em fluxo de velocidade de transmissão única de entrada para uma transmissão em fluxo de vídeo com várias velocidades (adaptável). Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.
* Um codificador ao vivo local envia um **RTMP** de múltiplas taxas de bits ou **Smooth streaming** (MP4 fragmentado) para o canal que não está habilitado para executar a codificação ativa com o AMS. Os fluxos ingeridos passam pelo **canal**s sem nenhum processamento adicional. Esse método é chamado **de passagem**. Você pode usar os codificadores ao vivo a seguir que geram várias taxas de bits Smooth Streaming: MediaExcel, Ateme, imagine Communications, envivio, Cisco e Elemental. Os seguintes codificadores dinâmicos produzem RTMP: os codificadores do Adobe Flash Media Live encoder (FMLE), Telestream Wirecast, HaiVision, Teradek e TriCaster.  Um codificador em direto pode também enviar uma transmissão em fluxo de velocidade de transmissão única para um canal, que não está ativado para live encoding, mas tal não é recomendado. Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.

  > [!NOTE]
  > Usar um método de passagem é a maneira mais econômica de realizar a transmissão ao vivo.
  > 
  > 

A partir da versão 2,10 dos serviços de mídia, ao criar um canal, você pode especificar de que maneira deseja que o canal receba o fluxo de entrada e se deseja ou não que o canal execute a codificação ativa de seu fluxo. Tem duas opções:

* **Nenhum** – Especifique esse valor se você planeja usar um codificador ao vivo local que produzirá fluxo de múltiplas taxas de bits (um fluxo de passagem). Nesse caso, o fluxo de entrada passou para a saída sem nenhuma codificação. Esse é o comportamento de um canal antes da versão 2,10.  Para obter informações mais detalhadas sobre como trabalhar com canais desse tipo, consulte [transmissão ao vivo com codificadores locais que criam fluxos de múltiplas taxas de bits](media-services-live-streaming-with-onprem-encoders.md).
* **Padrão** – escolha esse valor se você planeja usar os serviços de mídia para codificar sua transmissão ao vivo de taxa de bits única para fluxo de múltiplas taxas de bits. Lembre-se de que há um impacto de cobrança para a codificação ativa e você deve se lembrar de que deixar um canal de codificação ativa no estado "em execução" incorrerá em encargos de cobrança.  É recomendável parar imediatamente os canais em execução após a conclusão do evento de transmissão ao vivo para evitar cobranças por hora extra.

> [!NOTE]
> Este tópico discute os atributos de canais que estão habilitados para executar a codificação ativa (tipo de codificação**padrão** ). Para obter informações sobre como trabalhar com canais que não estão habilitados para executar a codificação ativa, consulte [transmissão ao vivo com codificadores locais que criam fluxos de múltiplas taxas](media-services-live-streaming-with-onprem-encoders.md)de bits.
> 
> Certifique-se de examinar a seção [considerações](media-services-manage-live-encoder-enabled-channels.md#Considerations) .
> 
> 

## <a name="billing-implications"></a>Implicações de cobrança
Um canal de codificação ativa começa a cobrança assim que é transição de estado para "em execução" por meio da API.   Você também pode exibir o estado na portal do Azure ou na ferramenta do Gerenciador de serviços de mídia do Azure (https://aka.ms/amse).

A tabela a seguir mostra como os Estados de canal são mapeados para os Estados de cobrança na API e portal do Azure. Os Estados são ligeiramente diferentes entre a API e o UX do Portal. Assim que um canal estiver no estado "em execução" por meio da API, ou no estado "pronto" ou "streaming" na portal do Azure, a cobrança estará ativa.
Para impedir que o canal faça uma cobrança adicional, você precisa interromper o canal por meio da API ou na portal do Azure.
Você é responsável por parar seus canais quando terminar com o canal de codificação ativa.  A falha na interrupção de um canal de codificação resultará em uma cobrança contínua.

### <a id="states"></a>Estados de canal e como eles são mapeados para o modo de cobrança
O estado atual de um Canal. Os valores possíveis incluem:

* **Parado**. Esse é o estado inicial do canal após sua criação (a menos que AutoStart tenha sido selecionado no Portal). Nenhuma cobrança ocorre nesse estado. Neste estado, as propriedades do Canal podem ser atualizadas, mas a transmissão em fluxo não é permitida.
* **Iniciando**. O Canal está a ser iniciado. Nenhuma cobrança ocorre nesse estado. Não são permitidas transmissões em fluxo nem atualizações durante este estado. Caso ocorra um erro, o Canal volta para o estado Parado.
* **Em execução**. O Canal é capaz de processar transmissões em fluxo. Agora é o uso de cobrança. Você deve parar o canal para evitar cobrança adicional. 
* **Parando**. O Canal está a ser parado. Nenhuma cobrança ocorre nesse estado transitório. Não são permitidas transmissões em fluxo nem atualizações durante este estado.
* **Excluindo**. O Canal está a ser apagado. Nenhuma cobrança ocorre nesse estado transitório. Não são permitidas transmissões em fluxo nem atualizações durante este estado.

A tabela seguinte mostra como os estados de um Canal mapeiam para o modo de faturação. 

| Estado do canal | Indicadores IU do portal | É cobrança? |
| --- | --- | --- |
| A Iniciar |A Iniciar |Não (estado transitório) |
| A executar |Pronto (nenhum programa em execução)<br/>ou<br/>A Transmitir em fluxo (pelo menos um programa em execução) |SIM |
| A Parar |A Parar |Não (estado transitório) |
| Parada |Parada |Não |

### <a name="automatic-shut-off-for-unused-channels"></a>Desligamento automático para canais não utilizados
A partir de 25 de janeiro de 2016, os serviços de mídia distribuíram uma atualização que interrompe automaticamente um canal (com codificação ativa habilitada) depois de ser executado em um estado não utilizado por um longo período. Isso se aplica a canais que não têm programas ativos e que não receberam um feed de contribuição de entrada por um longo período de tempo.

O limite de um período não usado é nominalmente 12 horas, mas está sujeito a alterações.

## <a name="live-encoding-workflow"></a>Fluxo de trabalho de codificação ativa
O diagrama a seguir representa um fluxo de trabalho de transmissão ao vivo em que um canal recebe um fluxo de taxa de bits única em um dos seguintes protocolos: RTMP ou Smooth Streaming; em seguida, ele codifica o fluxo para um fluxo de múltiplas taxas de bits. 

![Fluxo de trabalho em direto][live-overview]

## <a id="scenario"></a>Cenário comum de transmissão ao vivo
Os seguintes são passos gerais referentes à criação de aplicações comuns de transmissão em fluxo em direto.

> [!NOTE]
> Atualmente, a duração máxima recomendada de um evento em direto é de 8 horas.
>
> Há um impacto de cobrança para a codificação ativa e você deve se lembrar de que deixar um canal de codificação ativa no estado "em execução" incorrerá em encargos de cobrança por hora. É recomendável parar imediatamente os canais em execução após a conclusão do evento de transmissão ao vivo para evitar cobranças por hora extra. 

1. Ligue uma câmara de vídeo a um computador. Inicie e configure um codificador ao vivo local que pode gerar um fluxo de taxa de bits **única** em um dos seguintes protocolos: RTMP ou Smooth streaming. 

    Este passo também pode ser realizado depois de criar o Canal.
2. Crie e inicie um Canal. 
3. Obtenha o URL de inserção do Canal. 

    O URL de inserção é utilizado pelo codificador em direto para enviar a transmissão para o Canal.
4. Obtenha o URL de pré-visualização do Canal. 

    Utilize este URL para verificar se o canal está a receber corretamente a transmissão em fluxo em direto.
5. Crie um programa. 

    Ao usar o portal do Azure, a criação de um programa também cria um ativo. 

    Ao usar o SDK do .NET ou o REST, você precisa criar um ativo e especificar para usar esse ativo ao criar um programa. 
6. Publicar o ativo associado ao programa.   

    >[!NOTE]
    >Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. O ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 

7. Inicie o programa quando estiver pronto para começar a transmissão em fluxo e o arquivamento.
8. Opcionalmente, o codificador em direto pode ser indicado para iniciar um anúncio. O anúncio é inserido na transmissão de saída.
9. Pare o programa sempre que pretender interromper a transmissão e arquivar o evento.
10. Elimine o Programa (e, opcionalmente, elimine o elemento).   

> [!NOTE]
> É muito importante não esquecer de parar um canal de codificação ativa. Lembre-se de que há um impacto de cobrança por hora para a codificação ativa e você deve se lembrar de que deixar um canal de codificação ativa no estado "em execução" incorrerá em encargos de cobrança.  É recomendável parar imediatamente os canais em execução após a conclusão do evento de transmissão ao vivo para evitar cobranças por hora extra. 
> 
> 

## <a id="channel"></a>Configurações de entrada (ingestão) do canal
### <a id="Ingest_Protocols"></a>Protocolo de streaming de ingestão
Se o **tipo de codificador** for definido como **padrão**, as opções válidas serão:

* **RTMP** de taxa de bits única
* **MP4 fragmentado** de taxa de bits única (Smooth streaming)

#### <a id="single_bitrate_RTMP"></a>RTMP de taxa de bits única
Considerações:

* O fluxo de entrada não pode conter vídeo de múltiplas taxas de bits
* O fluxo de vídeo deve ter uma taxa de bits média abaixo de 15 Mbps
* O fluxo de áudio deve ter uma taxa de bits média abaixo de 1 Mbps
* A seguir estão os codecs com suporte:
* Vídeo MPEG-4 AVC/H. 264
* Linha de base, principal, perfil alto (8 bits 4:2:0)
* Alto perfil 10 (10 bits 4:2:0)
* Alto perfil 422 (10 bits 4:2:2)
* Áudio MPEG-2 AAC-LC
* Mono, estéreo, surround (5,1, 7,1)
* taxa de amostragem de 44,1 kHz
* Empacotamento de ADTS de estilo MPEG-2
* Os codificadores recomendados incluem:
* Wirecast de Telestream
* Codificador do Flash Media Live

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>MP4 fragmentado de velocidade de transmissão única (Transmissão em Fluxo Uniforme)
Caso de uso típico:

Use codificadores ativos locais de fornecedores como tecnologias de elemento, Ericsson, Ateme, envivio para enviar o fluxo de entrada pela Internet aberta para uma data center próxima do Azure.

Considerações:

O mesmo que para [RTMP de taxa de bits única](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

#### <a name="other-considerations"></a>Outras considerações
* Não é possível alterar o protocolo de entrada enquanto o Canal ou os seus programas associados estiverem em execução. Se necessitar de protocolos diferentes, deve criar canais separados para cada protocolo de entrada.
* A resolução máxima para o fluxo de vídeo de entrada é 1920 x 1080 e, no máximo, 60 campos/segundo se entrelaçados ou 30 quadros/segundo se progressivo.

### <a name="ingest-urls-endpoints"></a>URLs de ingestão (pontos de extremidade)
Um canal fornece um ponto de extremidade de entrada (URL de ingestão) que você especifica no codificador ao vivo, para que o codificador possa enviar fluxos para seus canais.

Você pode obter as URLs de ingestão depois de criar um canal. Para obter essas URLs, o canal não precisa estar no estado **executando** . Quando você estiver pronto para começar a enviar dados por push para o canal, ele deverá estar no estado **executando** . Depois que o canal inicia a ingestão de dados, você pode visualizar seu fluxo por meio da URL de visualização.

Você tem a opção de ingerir a transmissão ao vivo com MP4 fragmentado (Smooth Streaming) em uma conexão SSL. Para ingerir sobre SSL, certifique-se de atualizar a URL de ingestão para HTTPS. Atualmente, o AMS não dá suporte a SSL com domínios personalizados.  

### <a name="allowed-ip-addresses"></a>Endereços IP permitidos
Você pode definir os endereços IP que têm permissão para publicar o vídeo nesse canal. Os endereços IP permitidos podem ser especificados como um único endereço IP (por exemplo, ' 10.0.0.1 '), um intervalo de IP usando um endereço IP e uma máscara de sub-rede CIDR (por exemplo, ' 10.0.0.1/22 ') ou um intervalo de IP usando um endereço IP e uma máscara de sub-rede decimal com pontos (por exemplo, ' 10.0.0.1 (255.255.252.0) ').

Se não for especificado qualquer endereço IP e não existir nenhuma definição de regra, então, não será permitido qualquer endereço IP. Para permitir um endereço IP, crie uma regra e defina 0.0.0.0/0.

## <a name="channel-preview"></a>Visualização do canal
### <a name="preview-urls"></a>URLs de visualização
Os canais fornecem um ponto de extremidade de visualização (URL de visualização) que você usa para visualizar e validar seu fluxo antes do processamento e da entrega.

Você pode obter a URL de visualização ao criar o canal. Para obter a URL, o canal não precisa estar no estado **executando** .

Depois que o canal inicia a ingestão de dados, você pode visualizar seu fluxo.

> [!NOTE]
> Atualmente, o fluxo de visualização só pode ser entregue em formato MP4 fragmentado (Smooth Streaming), independentemente do tipo de entrada especificado.  Você pode usar um player hospedado no portal do Azure para exibir seu fluxo.
> 
> 

### <a name="allowed-ip-addresses"></a>Endereços IP permitidos
Você pode definir os endereços IP que têm permissão para se conectar ao ponto de extremidade de visualização. Se não forem especificados endereços IP, qualquer endereço IP será permitido. Os endereços IP permitidos podem ser especificados como um único endereço IP (por exemplo, ' 10.0.0.1 '), um intervalo de IP usando um endereço IP e uma máscara de sub-rede CIDR (por exemplo, ' 10.0.0.1/22 ') ou um intervalo de IP usando um endereço IP e uma máscara de sub-rede decimal com pontos (por exemplo, ' 10.0.0.1 (255.255.252.0) ').

## <a name="live-encoding-settings"></a>Configurações de codificação ao vivo
Esta seção descreve como as configurações para o codificador ao vivo dentro do canal podem ser ajustadas quando o **tipo de codificação** de um canal é definido como **Standard**.

> [!NOTE]
> Seu feed de contribuição pode conter apenas uma única faixa de áudio – não há suporte para a ingestão de várias faixas de áudio no momento. Ao fazer a codificação ativa com os [codificadores ativos locais](media-services-live-streaming-with-onprem-encoders.md), você pode enviar um feed de contribuição no protocolo Smooth streaming que contém várias faixas de áudio.
> 
> 

### <a name="ad-marker-source"></a>Fonte do marcador do AD
Você pode especificar a fonte para sinais de marcadores do AD. O valor padrão é **API**, que indica que o codificador ao vivo dentro do canal deve escutar uma API assíncrona do **marcador do AD**.

### <a name="cea-708-closed-captions"></a>Legendas codificadas CEA 708
Um sinalizador opcional que informa o codificador ao vivo para ignorar os dados de legendas CEA 708 inseridos no vídeo de entrada. Quando o sinalizador é definido como false (padrão), o codificador detectará e reinserirá dados CEA 708 nos fluxos de vídeo de saída.

#### <a name="index"></a>Índice
É recomendável enviar em um fluxo de transporte de programa único (SPTS). Se o fluxo de entrada contiver vários programas, o codificador ao vivo dentro do canal analisa a tabela de mapa do programa (PGTO) na entrada, identifica as entradas que têm um nome de tipo de fluxo de MPEG-2 AAC ADTS ou AC-3 System-A ou AC-3 sistema-B ou MPEG-2 Private PES ou MPEG-1 Áudio ou MPEG-2 Audio e os organiza na ordem especificada no pgto. Em seguida, o índice de base zero é usado para pegar a entrada n-ésimo nesse arranjo.

#### <a name="language"></a>Linguagem
O identificador de idioma do fluxo de áudio, em conformidade com o ISO 639-2, como ENG. Se não estiver presente, o padrão será UND (indefinido).

### <a id="preset"></a>Predefinição do sistema
Especifica a predefinição a ser usada pelo codificador ao vivo dentro deste canal. Atualmente, o único valor permitido é **default720p** (padrão).

**Default720p** codificará o vídeo nas 6 camadas a seguir.

#### <a name="output-video-stream"></a>Fluxo de vídeo de saída

| BitRate | Largura | Altura | MaxFPS | Perfil | Nome do fluxo de saída |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Elevado |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Elevado |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Elevado |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Elevado |Video_512x288_850kbps |
| 550 |384 |216 |30 |Elevado |Video_384x216_550kbps |
| 200 |340 |192 |30 |Elevado |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>Fluxo de áudio de saída

O áudio é codificado para estéreo AAC-LC a 128 kbps, taxa de amostragem de 48 kHz.

## <a name="signaling-advertisements"></a>Sinalizando anúncios
Quando o canal tiver a codificação ativa habilitada, você terá um componente em seu pipeline que está processando o vídeo e poderá manipulá-lo. Você pode sinalizar para que o canal insira slates e/ou anúncios no fluxo de taxa de bits adaptável de saída. Os slates ainda são imagens que você pode usar para cobrir o feed ao vivo de entrada em determinados casos (por exemplo, durante um intervalo comercial). Os sinais de publicidade, são sinais sincronizados de tempo que você insere no fluxo de saída para instruir o player de vídeo a executar uma ação especial – por exemplo, para alternar para um anúncio no momento apropriado. Consulte este [blog](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) para obter uma visão geral do mecanismo de sinalização SCTE-35 usado para essa finalidade. Veja abaixo um cenário típico que você poderia implementar em seu evento ao vivo.

1. Os visualizadores recebem uma imagem anterior ao evento antes do início do evento.
2. Faça com que os visualizadores obtenham uma imagem de POST-evento após o término do evento.
3. Os visualizadores recebem uma imagem de evento de erro se houver um problema durante o evento (por exemplo, falha de energia no estádio).
4. Envie uma imagem de quebra de anúncio para ocultar o feed de eventos ao vivo durante um intervalo comercial.

A seguir estão as propriedades que você pode definir ao sinalizar anúncios. 

### <a name="duration"></a>Duração
A duração, em segundos, do intervalo comercial. Isso deve ser um valor positivo diferente de zero para iniciar o intervalo comercial. Quando um intervalo comercial está em andamento, e a duração é definida como zero com o CueId correspondente ao intervalo comercial contínuo, essa interrupção é cancelada.

### <a name="cueid"></a>CueId
Uma ID exclusiva para o intervalo comercial, a ser usada pelo aplicativo downstream para executar as ações apropriadas. Precisa ser um inteiro positivo. Você pode definir esse valor como qualquer inteiro positivo aleatório ou usar um sistema upstream para acompanhar as IDs de indicação. Certifique-se de normalizar quaisquer IDs para inteiros positivos antes de enviar por meio da API.

### <a name="show-slate"></a>Mostrar Slate
Opcional. Sinaliza o codificador ao vivo para alternar para a imagem [Slate padrão](media-services-manage-live-encoder-enabled-channels.md#default_slate) durante um intervalo comercial e ocultar o feed de vídeo de entrada. O áudio também é mudo durante o Slate. O padrão é **false**. 

A imagem usada será aquela especificada por meio da propriedade ID do ativo de Slate padrão no momento da criação do canal. O Tablet será alongado para se ajustar ao tamanho da imagem de exibição. 

## <a name="insert-slate--images"></a>Inserir imagens Slate
O codificador ao vivo dentro do canal pode ser sinalizado para alternar para uma imagem de Slate. Ele também pode ser sinalizado para encerrar um Slate contínuo. 

O codificador ao vivo pode ser configurado para alternar para uma imagem Slate e ocultar o sinal de vídeo de entrada em determinadas situações – por exemplo, durante um intervalo de anúncio. Se esse Slate não estiver configurado, o vídeo de entrada não será mascarado durante esse intervalo de anúncio.

### <a name="duration"></a>Duração
A duração do Slate em segundos. Isso deve ser um valor positivo diferente de zero para iniciar o Slate. Se houver um Slate contínuo e uma duração igual a zero for especificada, esse Tablet contínuo será encerrado.

### <a name="insert-slate-on-ad-marker"></a>Inserir Slate no marcador do anúncio
Quando definido como true, essa configuração configura o codificador ao vivo para inserir uma imagem de Slate durante um intervalo de anúncio. O valor predefinido é verdadeiro. 

### <a id="default_slate"></a>ID do ativo de Slate padrão

Opcional. Especifica a ID de ativo do ativo dos serviços de mídia que contém a imagem de Slate. O padrão é NULL. 


> [!NOTE] 
> Antes de criar o canal, a imagem Slate com as seguintes restrições deve ser carregada como um ativo dedicado (nenhum outro arquivo deve estar nesse ativo). Essa imagem é usada somente quando o codificador ao vivo está inserindo um Slate devido a um intervalo de anúncio ou foi sinalizado explicitamente para inserir um Slate. Atualmente, não há nenhuma opção para usar uma imagem personalizada quando o codificador ao vivo entra no estado ' sinal de entrada perdido '. Você pode votar neste recurso [aqui](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel).

* No máximo 1920 x 1080 na resolução.
* No máximo 3 Mbytes de tamanho.
* O nome do arquivo deve ter uma extensão *. jpg.
* A imagem deve ser carregada em um ativo como o único Assetfile nesse ativo e esse Assetfile deve ser marcado como o arquivo primário. O ativo não pode ser criptografado como armazenamento.

Se a **ID do ativo de Slate padrão** não for especificada e a **inserção de Slate no marcador do AD** estiver definida como **true**, uma imagem padrão dos serviços de mídia do Azure será usada para ocultar o fluxo de vídeo de entrada. O áudio também é mudo durante o Slate. 

## <a name="channels-programs"></a>Programas do canal
Um canal está associado a programas que permitem controlar a publicação e armazenamento de segmentos numa transmissão em fluxo em direto. Canais gerem Programas. A relação entre canal e programa é muito semelhante à mídia tradicional, em que um canal tem um fluxo constante de conteúdo e um programa tem como escopo algum evento cronometrado nesse canal.

Pode especificar o número de horas que pretenda manter o conteúdo gravado para o programa através da configuração da duração da **Janela de Arquivo**. Este valor pode ser definido a partir de um mínimo de 5 minutos até um máximo de 25 horas. O comprimento da janela de arquivo também determina o número máximo de tempo que os clientes podem buscar de volta no tempo a partir da posição dinâmica atual. Os programas podem ser executados durante o período de tempo especificado, mas o conteúdo que se situe atrás da duração da janela é continuamente descartado. O valor desta propriedade também determina durante quanto tempo os manifestos dos clientes podem aumentar.

Cada programa é associado a um ativo que armazena o conteúdo transmitido. Um ativo é mapeado para um contêiner de blobs de blocos na conta de armazenamento do Azure e os arquivos no ativo são armazenados como BLOBs nesse contêiner. Para publicar o programa para que os clientes possam exibir o fluxo, você deve criar um localizador OnDemand para o ativo associado. Ter este localizador irá permitir compilar um URL de transmissão em fluxo que pode fornecer aos seus clientes.

Um canal dá suporte a até três programas em execução simultânea, para que você possa criar vários arquivos do mesmo fluxo de entrada. Isto permite publicar e arquivar diferentes partes de um evento, conforme necessário. Por exemplo, os seus requisitos de negócios devem arquivar 6 horas de um programa, mas difundir apenas os últimos 10 minutos. Para tal, tem de criar dois programas em execução em simultâneo. Um programa está definido para arquivar 6 horas do evento, mas o programa não está publicado. O outro programa está definido para arquivar durante 10 minutos e este está publicado.

Não deve reutilizar programas existentes para novos eventos. Em vez disso, crie e inicie um novo programa para cada evento, conforme descrito na seção Programming Live streaming Applications.

Inicie o programa quando estiver pronto para começar a transmissão em fluxo e o arquivamento. Pare o programa sempre que pretender interromper a transmissão e arquivar o evento. 

Para eliminar conteúdo arquivado, pare e elimine o programa e, em seguida, elimine o elemento associado. Não é possível eliminar um elemento se este é utilizado por um programa; o programa deve ser eliminado primeiro. 

Mesmo depois de parar e eliminar o programa, os utilizadores conseguirão transmitir o seu conteúdo arquivado como um vídeo a pedido até que elimine o elemento.

Se pretende manter o conteúdo arquivado, mas não o quer manter disponível para transmissão em fluxo, elimine o localizador de transmissão em fluxo.

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>Obtendo uma visualização em miniatura de um feed ao vivo
Quando a codificação ativa está habilitada, agora você pode obter uma visualização do feed ao vivo enquanto ele atinge o canal. Essa pode ser uma ferramenta valiosa para verificar se o feed ao vivo está realmente atingindo o canal. 

## <a id="states"></a>Estados de canal e como os Estados são mapeados para o modo de cobrança
O estado atual de um Canal. Os valores possíveis incluem:

* **Parado**. Este é o estado inicial de um Canal após a sua criação. Neste estado, as propriedades do Canal podem ser atualizadas, mas a transmissão em fluxo não é permitida.
* **Iniciando**. O Canal está a ser iniciado. Não são permitidas transmissões em fluxo nem atualizações durante este estado. Caso ocorra um erro, o Canal volta para o estado Parado.
* **Em execução**. O Canal é capaz de processar transmissões em fluxo.
* **Parando**. O Canal está a ser parado. Não são permitidas transmissões em fluxo nem atualizações durante este estado.
* **Excluindo**. O Canal está a ser apagado. Não são permitidas transmissões em fluxo nem atualizações durante este estado.

A tabela seguinte mostra como os estados de um Canal mapeiam para o modo de faturação. 

| Estado do canal | Indicadores IU do portal | Foi cobrado? |
| --- | --- | --- |
| A Iniciar |A Iniciar |Não (estado transitório) |
| A executar |Pronto (nenhum programa em execução)<br/>ou<br/>A Transmitir em fluxo (pelo menos um programa em execução) |Sim |
| A Parar |A Parar |Não (estado transitório) |
| Parada |Parada |Não |

> [!NOTE]
> Atualmente, a média de início do canal é de cerca de 2 minutos, mas às vezes pode levar até 20 minutos. As redefinições de canal podem levar até 5 minutos.
> 
> 

## <a id="Considerations"></a>Considere
* Quando um canal de tipo de codificação **padrão** passa por uma perda de fonte de entrada/feed de contribuição, ele compensa isso substituindo o vídeo/áudio de origem por um erro Slate e silêncio. O canal continuará emitindo um Slate até que o feed de entrada/contribuição seja retomado. É recomendável que um canal ativo não seja deixado nesse estado por mais de 2 horas. Além desse ponto, o comportamento do canal na reconexão de entrada não é garantido, nenhum deles é seu comportamento em resposta a um comando de redefinição. Você precisará parar o canal, excluí-lo e criar um novo.
* Não é possível alterar o protocolo de entrada enquanto o Canal ou os seus programas associados estiverem em execução. Se necessitar de protocolos diferentes, deve criar canais separados para cada protocolo de entrada.
* Toda vez que você reconfigurar o codificador ao vivo, chame o método **Reset** no canal. Antes de redefinir o canal, você precisa parar o programa. Depois de redefinir o canal, reinicie o programa.
* Um canal pode ser interrompido somente quando está no estado de execução e todos os programas no canal foram interrompidos.
* Por padrão, você só pode adicionar 5 canais à sua conta de serviços de mídia. Esta é uma cota flexível em todas as novas contas. Para obter mais informações, consulte [cotas e limitações](media-services-quotas-and-limitations.md).
* Não é possível alterar o protocolo de entrada enquanto o Canal ou os seus programas associados estiverem em execução. Se necessitar de protocolos diferentes, deve criar canais separados para cada protocolo de entrada.
* Você só será cobrado quando o canal estiver no estado **executando** . Para obter mais informações, consulte [esta](media-services-manage-live-encoder-enabled-channels.md#states) seção.
* Atualmente, a duração máxima recomendada de um evento em direto é de 8 horas. 
* Certifique-se de ter o ponto de extremidade de streaming do qual você deseja transmitir conteúdo no estado de **execução** .
* A predefinição de codificação usa a noção de "taxa de quadros máxima" de 30 fps. Portanto, se a entrada for 60fps/59,94 i, os quadros de entrada serão descartados/desentrelaçados a 30/29,97 fps. Se a entrada for 50fps/50i, os quadros de entrada serão descartados/desentrelaçados para 25 fps. Se a entrada for 25 fps, a saída permanecerá em 25 fps.
* Não se esqueça de parar seus canais quando terminar. Caso contrário, a cobrança continuará.

## <a name="known-issues"></a>Problemas Conhecidos
* O tempo de inicialização do canal foi melhorado para uma média de 2 minutos, mas às vezes o aumento de demanda ainda pode levar até 20 minutos.
* As imagens de Slate devem estar em conformidade com as restrições descritas [aqui](media-services-manage-live-encoder-enabled-channels.md#default_slate). Se você tentar criar um canal com um Slate padrão maior que 1920 x 1080, a solicitação eventualmente falhará.
* Mais uma vez.... Não se esqueça de parar seus canais quando terminar de transmitir. Caso contrário, a cobrança continuará.

## <a name="need-help"></a>Precisa de ajuda?

Você pode abrir um tíquete de suporte navegando até [nova solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-step"></a>Passo seguinte

Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tópicos relacionados
[Entregando eventos de transmissão ao vivo com os serviços de mídia do Azure](media-services-overview.md)

[Criar canais que executam codificação ativa de uma taxa de bits única para o fluxo de taxa de bits adaptável com o portal](media-services-portal-creating-live-encoder-enabled-channel.md)

[Criar canais que executam codificação ativa de uma taxa de bits única para o fluxo de taxa de bits adaptável com o SDK do .NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[Gerenciar canais com a API REST](https://docs.microsoft.com/rest/api/media/operations/channel)

[Conceitos dos serviços de mídia](media-services-concepts.md)

[Especificação de ingestão dinâmica de MP4 fragmentado dos serviços de mídia do Azure](../media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png

