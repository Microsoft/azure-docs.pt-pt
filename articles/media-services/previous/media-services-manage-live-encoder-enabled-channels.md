---
title: Live a transmissão em fluxo através dos serviços de multimédia do Azure para criar transmissões em fluxo | Documentos da Microsoft
description: 'Este tópico descreve como configurar um canal que recebe um fluxo em direto com velocidade de transmissão única de um codificador no local e, em seguida, realiza a codificação em tempo real para o fluxo de velocidade de transmissão adaptável com os Media Services. O fluxo, em seguida, pode ser fornecido para aplicações de reprodução de cliente através de um ou mais de transmissão em fluxo pontos finais, através de um dos seguintes protocolos de transmissão em fluxo adaptáveis: HLS, Smooth Stream, MPEG DASH.'
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
ms.author: juliako;anilmur
ms.openlocfilehash: c168182f0b34329ed3e72e90ce86456dfbe210ca
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189857"
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>Transmissão em fluxo em direto utilizando os Serviços de Multimédia do Azure para criar transmissões com velocidade de transmissão múltipla

> [!NOTE]
> A partir de 12 de Maio de 2018, os canais em direto será já não suporte o fluxo de transporte RTP/MPEG-2 protocolo de ingestão. Migre de RTP/MPEG-2 para RTMP ou MP4 fragmentado (Smooth Streaming) protocolos de ingestão.

## <a name="overview"></a>Descrição geral
No Azure Media Services (AMS), um **canal** representa um pipeline de processamento de conteúdo de transmissão em fluxo em direto. R **canal** recebe transmissões em direto de entrada em uma das seguintes formas:

* Um codificador em direto no local envia um fluxo de velocidade de transmissão única para o canal ativado para realizar live encoding com Media Services dos seguintes formatos: RTMP ou Smooth Streaming (MP4 fragmentado). O Canal, em seguida, realiza live encoding da transmissão em fluxo de velocidade de transmissão única de entrada para uma transmissão em fluxo de vídeo com várias velocidades (adaptável). Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.
* Um codificador em direto no local envia uma velocidade de transmissão **RTMP** ou **Smooth Streaming** (MP4 fragmentado) para o canal que não está ativado para realizar live encoding com o AMS. As transmissões em fluxo passam-through **canal**s sem qualquer processamento adicional. Esse método é chamado **pass-through**. Pode utilizar os seguintes codificadores em direto que múltipla transmissão em fluxo uniforme de saída: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco e Elemental. Os seguintes codificadores em direto transmitem RTMP: Codificadores Adobe Flash Media Live Encoder (FMLE), Telestream Wirecast, Haivision, Teradek e Tricaster.  Um codificador em direto pode também enviar uma transmissão em fluxo de velocidade de transmissão única para um canal, que não está ativado para live encoding, mas tal não é recomendado. Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.

  > [!NOTE]
  > Usando um método pass-through é a forma mais económica para transmissão em direto.
  > 
  > 

Começando com o lançamento de 2.10 de serviços de multimédia, quando cria um canal, pode especificar de que forma pretende para o canal receber o fluxo de entrada e se é ou não desejar para o canal realizar live encoding da sua transmissão em fluxo. Tem duas opções:

* **Nenhum** – especificar este valor, se planeja usar um codificador em direto no local, que serão de saída do fluxo de velocidade de transmissão (um fluxo de pass-through). Neste caso, o fluxo de entrada transmitida para a saída sem qualquer tipo de codificação. Este é o comportamento de um canal antes do lançamento 2.10.  Para obter mais informações sobre como trabalhar com canais deste tipo, consulte [transmissão em fluxo em direto com codificadores no local que criam transmissões em fluxo](media-services-live-streaming-with-onprem-encoders.md).
* **Padrão** – escolha esse valor, se planeja usar serviços de multimédia para codificar a transmissão em direto de velocidade de transmissão única para o fluxo de velocidade de transmissão. Lembre-se de que há um impacto de faturação para live encoding e deve se lembrar de que a sair de um canal de codificação em direto no estado "Em execução" irá incorrer em custos de faturas.  Recomenda-se que parar imediatamente os seus canais em execução depois do evento de transmissão em fluxo em direto foi concluído para evitar custos adicionais por hora.

> [!NOTE]
> Este tópico descreve os atributos de canais que estão ativados para realizar live encoding (**padrão** tipo de codificação). Para obter informações sobre como trabalhar com canais que não estão ativados para realizar live encoding, consulte [transmissão em fluxo em direto com codificadores no local que criam transmissões em fluxo](media-services-live-streaming-with-onprem-encoders.md).
> 
> Lembre-se de que reveja os [considerações](media-services-manage-live-encoder-enabled-channels.md#Considerations) secção.
> 
> 

## <a name="billing-implications"></a>Implicações de faturação
Um canal de codificação em direto começa assim que é transições de estado para "Em execução" através da API de faturação.   Também pode ver o estado no portal do Azure ou na ferramenta do Explorador de serviços de multimédia do Azure (https://aka.ms/amse).

A tabela seguinte mostra como os Estados de um canal mapeiam para Estados de faturas no portal do Azure e de API. Os Estados são ligeiramente diferentes entre a API e o Portal de experiência do usuário. Assim que um canal está no estado "Em execução" através da API ou, no estado "Pronto" ou "Transmissão em fluxo" no portal do Azure, a faturação será Active Directory.
Para parar o canal de faturação ainda mais, terá de parar o canal através da API ou no portal do Azure.
É responsável por seus canais a parar quando tiver terminado com o canal de codificação em direto.  Falha ao parar um canal de codificação irá resultar numa faturação contínua.

### <a id="states"></a>Estados de um canal e como devem ser mapeadas para o modo de faturação
O estado atual de um Canal. Os valores possíveis incluem:

* **Parado**. Este é o estado inicial do canal após a sua criação (a menos que início automático foi selecionado no portal.) Ocorre uma faturação sem neste estado. Neste estado, as propriedades do Canal podem ser atualizadas, mas a transmissão em fluxo não é permitida.
* **A iniciar**. O Canal está a ser iniciado. Ocorre uma faturação sem neste estado. Não são permitidas transmissões em fluxo nem atualizações durante este estado. Caso ocorra um erro, o Canal volta para o estado Parado.
* **Executar**. O Canal é capaz de processar transmissões em fluxo. Ele é agora utilização de faturação. Tem de parar o canal para impedir que mais de faturação. 
* **A parar**. O Canal está a ser parado. Ocorre uma faturação sem neste estado transitório. Não são permitidas transmissões em fluxo nem atualizações durante este estado.
* **A eliminar**. O Canal está a ser apagado. Ocorre uma faturação sem neste estado transitório. Não são permitidas transmissões em fluxo nem atualizações durante este estado.

A tabela seguinte mostra como os estados de um Canal mapeiam para o modo de faturação. 

| Estado do canal | Indicadores IU do portal | É a faturação? |
| --- | --- | --- |
| A iniciar |A iniciar |Não (estado transitório) |
| A executar |Pronto (nenhum programa em execução)<br/>ou<br/>A Transmitir em fluxo (pelo menos um programa em execução) |SIM |
| A parar |A parar |Não (estado transitório) |
| Parada |Parada |Não |

### <a name="automatic-shut-off-for-unused-channels"></a>Desligar-off automático para canais não utilizados
A partir de 25 de Janeiro de 2016, os serviços de multimédia distribuiu uma atualização que para automaticamente um canal (com codificação em tempo real ativada) após está funcionando num Estado de funcionamento não utilizado por um longo período. Isto aplica-se aos canais que não têm a nenhum programa de Active Directory e que não tenham recebido uma contribuição de entrada por um longo período de tempo do feed.

O limiar durante um período não utilizado é nominally 12 horas, mas está sujeitas a alterações.

## <a name="live-encoding-workflow"></a>Fluxo de trabalho de codificação em direto
O diagrama abaixo representa um fluxo de trabalho de transmissão em fluxo em direto onde um canal recebe um fluxo de velocidade de transmissão única dos seguintes protocolos: RTMP ou Smooth Streaming; ele codifica, em seguida, o fluxo para um fluxo de velocidade de transmissão. 

![Fluxo de trabalho em direto][live-overview]

## <a id="scenario"></a>Cenário comum de transmissão em fluxo em direto
Os seguintes são passos gerais referentes à criação de aplicações comuns de transmissão em fluxo em direto.

> [!NOTE]
> Atualmente, a duração máxima recomendada de um evento em direto é de 8 horas. Contacte a amslived@microsoft.com se tiver de executar um Canal durante períodos de tempo mais longos. Há um impacto de faturação para live encoding e deve se lembrar de que a sair de um canal de codificação em direto no estado "Em execução" irá incorrer em custos de faturas por hora.  Recomenda-se que parar imediatamente os seus canais em execução depois do evento de transmissão em fluxo em direto foi concluído para evitar custos adicionais por hora. 

1. Ligue uma câmara de vídeo a um computador. Iniciar e configurar um codificador em direto no local, cuja saída pode ser um **único** transmissão em fluxo em um dos seguintes protocolos: RTMP ou Smooth Streaming. 

    Este passo também pode ser realizado depois de criar o Canal.
2. Crie e inicie um Canal. 
3. Obtenha o URL de inserção do Canal. 

    O URL de inserção é utilizado pelo codificador em direto para enviar a transmissão para o Canal.
4. Obtenha o URL de pré-visualização do Canal. 

    Utilize este URL para verificar se o canal está a receber corretamente a transmissão em fluxo em direto.
5. Crie um programa. 

    Ao utilizar o portal do Azure, a criação de um programa também cria um recurso. 

    Ao utilizar o SDK do .NET ou REST terá de criar um elemento e especifique a utilização deste recurso ao criar um programa. 
6. Publique o elemento associado ao programa.   

    >[!NOTE]
    >Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. O ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 

7. Inicie o programa quando estiver pronto para começar a transmissão em fluxo e o arquivamento.
8. Opcionalmente, o codificador em direto pode ser indicado para iniciar um anúncio. O anúncio é inserido na transmissão de saída.
9. Pare o programa sempre que pretender interromper a transmissão e arquivar o evento.
10. Elimine o Programa (e, opcionalmente, elimine o elemento).   

> [!NOTE]
> É muito importante não se esqueça de parar um canal de codificação em direto. Lembre-se de que há uma hora a hora de faturação impacto para a codificação em direto e deve se lembrar de que a sair de um canal de codificação em direto no estado "Em execução" irá incorrer em custos de faturas.  Recomenda-se que parar imediatamente os seus canais em execução depois do evento de transmissão em fluxo em direto foi concluído para evitar custos adicionais por hora. 
> 
> 

## <a id="channel"></a>Entrada do canal (ingerir) configurações
### <a id="Ingest_Protocols"></a>Transmissão em fluxo de protocolo de ingestão
Se o **tipo de codificador** está definida como **padrão**, as opções válidas são:

* Velocidade de transmissão única **RTMP**
* Velocidade de transmissão única **MP4 fragmentado** (transmissão em fluxo uniforme)

#### <a id="single_bitrate_RTMP"></a>RTMP de velocidade de transmissão única
Considerações:

* Fluxo de entrada não pode conter a velocidade de transmissão vídeo
* O fluxo de vídeo deve ter uma média de velocidade de transmissão abaixo de 15 Mbps
* O fluxo de áudio deve ter uma média de velocidade de transmissão abaixo 1 Mbps
* Seguem-se os codecs suportados:
* MPEG-4 AVC / H.264 vídeo
* Linha de base, o principal, o perfil alto (4 de 8 bits: 2:0)
* Perfil de 10 elevada (4 de 10 bits: 2:0)
* Perfil 422 elevada (4 de 10 bits: 2:2)
* Áudio AAC-LC MPEG-2
* Mono, Estéreo, Surround (5.1, 7.1)
* frequência de amostragem 44.1 kHz
* Empacotamento de ADTS de estilo de MPEG-2
* Codificadores recomendados incluem:
* Telestream Wirecast
* Codificador em direto de mídia Flash

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>MP4 fragmentado de velocidade de transmissão única (Transmissão em Fluxo Uniforme)
Caso de uso típico:

Utilizar codificadores no local em direto de fornecedores como tecnologias elementar, Ericsson, Ateme, Envivio para enviar o fluxo de entrada na Internet aberta para um Azure próximos Centro de dados.

Considerações:

Mesmo que para [único RTMP de velocidade de transmissão](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

#### <a name="other-considerations"></a>Outras considerações
* Não é possível alterar o protocolo de entrada enquanto o Canal ou os seus programas associados estiverem em execução. Se necessitar de protocolos diferentes, deve criar canais separados para cada protocolo de entrada.
* Resolução máxima de fluxo de vídeo de entrada é de 1920 x 1080 e até 60 campos por segundo se entrelaçadas ou 30 quadros por segundo se progressiva.

### <a name="ingest-urls-endpoints"></a>URLs (pontos de extremidade) de inserção
Um canal fornece um ponto de final de entrada (URL de ingestão) que especifica em que o codificador em direto, para que o codificador pode enviar por push fluxos aos seus canais.

Depois de criar um canal, pode obter os URLs de inserção. Para obter estes URLs, o canal não tem de estar no **em execução** estado. Quando estiver pronto para começar a enviar por push dados para o canal, tem de ter o **em execução** estado. Assim que o canal de inicia a ingestão de dados, pode visualizar a sua transmissão em fluxo por meio da URL de pré-visualização.

Tem uma opção de ingestão de MP4 fragmentado (Smooth Streaming) em fluxo em direto através de uma ligação SSL. Ingerir através de SSL, certifique-se atualizar o URL de inserção para HTTPS. Atualmente, o AMS não suporta SSL com domínios personalizados.  

### <a name="allowed-ip-addresses"></a>Endereços IP permitidos
Pode definir os endereços IP que estão autorizados a publicar vídeo neste canal. Permitido IP endereços podem ser especificados como um único IP de endereços (por exemplo, "10.0.0.1"), um intervalo IP com um endereço IP e uma máscara de sub-rede CIDR (por exemplo, "10.0.0.1/22") ou um IP no intervalo com um endereço IP e uma máscara de sub-rede de ponto decimal (por exemplo , ' 10.0.0.1(255.255.252.0)').

Se não for especificado qualquer endereço IP e não existir nenhuma definição de regra, então, não será permitido qualquer endereço IP. Para permitir um endereço IP, crie uma regra e defina 0.0.0.0/0.

## <a name="channel-preview"></a>Pré-visualização do canal
### <a name="preview-urls"></a>URLs de pré-visualização
Canais de fornecem um ponto de extremidade pré-visualização (URL de pré-visualização) que utilizar para pré-visualizar e validar a sua transmissão antes de mais processamentos e entregas.

Pode obter o URL de pré-visualização ao criar o canal. Para obter o URL, o canal não tem de estar no **em execução** estado.

Assim que o canal de inicia a ingestão de dados, pode visualizar a sua transmissão em fluxo.

> [!NOTE]
> Atualmente o fluxo de pré-visualização só pode ser fornecido num MP4 fragmentado (Smooth Streaming) formato, independentemente do tipo de entrada especificado.  Pode usar um leitor alojado no portal do Azure para ver a sua transmissão em fluxo.
> 
> 

### <a name="allowed-ip-addresses"></a>Endereços IP permitidos
Pode definir os endereços IP que estão autorizados a ligar para o ponto final de pré-visualização. Se não houver endereços IP são especificados qualquer endereço IP será permitido. Permitido IP endereços podem ser especificados como um único IP de endereços (por exemplo, "10.0.0.1"), um intervalo IP com um endereço IP e uma máscara de sub-rede CIDR (por exemplo, "10.0.0.1/22") ou um IP no intervalo com um endereço IP e uma máscara de sub-rede de ponto decimal (por exemplo , ' 10.0.0.1(255.255.252.0)').

## <a name="live-encoding-settings"></a>Definições de codificação direta
Esta secção descreve como as definições para o codificador em direto dentro do canal podem ser ajustadas, quando o **tipo de codificação** de um canal está definido como **padrão**.

> [!NOTE]
> O feed de contribuição só pode conter uma faixa de áudio única – ingestão de múltiplas faixas de áudio não é atualmente suportada. Quando realizar live encoding com os [locais live codifica](media-services-live-streaming-with-onprem-encoders.md), pode enviar uma contribuição feed no protocolo de transmissão em fluxo uniforme de mensagens em fila que contém várias faixas de áudio.
> 
> 

### <a name="ad-marker-source"></a>Origem de marcador do AD
Pode especificar a origem de sinais de marcadores do ad. Valor predefinido é **Api**, que indica que o codificador em direto dentro do canal deve ouvir assíncrona **API de marcador do Ad**.

### <a name="cea-708-closed-captions"></a>Legenda oculta CEA 708 legendas
Um sinalizador opcional que informa ao codificador em direto para ignorar quaisquer dados de legendas de legenda oculta CEA 708 incorporado no vídeo de entrada. Quando o sinalizador é definido como false (predefinição), o codificador detectará e volte a inserir dados de legenda oculta CEA 708 para os fluxos de vídeo de saída.

#### <a name="index"></a>Índice
Recomenda-se para enviar num fluxo de transporte de único programa (SPTS). Se o fluxo de entrada contém vários programas, o codificador em direto dentro do canal analisa a tabela de mapa do programa (PMT) na entrada, que identifica as entradas que tem um nome de tipo de fluxo de MPEG-2 AAC ADTS ou AC-3 System-A ou AC-3. o sistema-B ou MPEG-2 de PES privada ou MPEG-1 Áudio ou áudio de MPEG-2 e as organiza em ordem especificada no PMT. O índice baseado em zero, em seguida, é utilizado para recolher a entrada de n-th essa disposição.

#### <a name="language"></a>Idioma
O identificador de idioma do fluxo de áudio, estando em conformidade com ISO 639-2, como ENG. Se não estiver presente, a predefinição é UND (indefinido).

### <a id="preset"></a>Configuração predefinida do sistema
Especifica a configuração predefinida a ser utilizado pelo codificador em direto dentro deste canal. Atualmente, o único valor permitido é **Default720p** (predefinição).

Tenha em atenção que se precisar de configurações predefinidas personalizadas, deve contactar amslived@microsoft.com.

**Default720p** irá codificar o vídeo para as camadas de 6 seguintes.

#### <a name="output-video-stream"></a>Stream de vídeo de saída

| BitRate | Largura | Altura | MaxFPS | Perfil | Nome do Stream de saída |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Elevado |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Elevado |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Elevado |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Elevado |Video_512x288_850kbps |
| 550 |384 |216 |30 |Elevado |Video_384x216_550kbps |
| 200 |340 |192 |30 |Elevado |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>Stream de áudio de saída

Áudio está codificado para estéreo AAC-LC em 128 kbps, taxa de amostragem de 48 kHz.

## <a name="signaling-advertisements"></a>Sinalização de anúncios
Quando seu canal tem Live Encoding ativado, que tem um componente no seu pipeline, que é o processamento de vídeo e pode manipulá-lo. Pode sinalizar ao canal a inserção de slates e/ou anúncios no fluxo de velocidade de transmissão adaptável de saída. Imagens fixas são imagens que podem ser utilizadas para cobrir o feed entrado em direto em certos casos (por exemplo, durante). Sinais de publicidade, são a hora sincronizada sinais que incorporar no fluxo de saída para informar o player de vídeo para adotar nenhuma medida especial – por exemplo, para mudar para um anúncio no momento adequado. Ver isso [blogue](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) para uma descrição geral do mecanismo de sinalização SCTE 35 utilizado para esta finalidade. Segue-se um cenário típico, que poderia implementar no seu evento em direto.

1. Ter os visualizadores de obter uma imagem de pré-evento de antes do evento é iniciado.
2. Ter os visualizadores de obter uma imagem de pós-evento após o evento terminar.
3. Ter os visualizadores de obter uma imagem de evento de erro, se houver um problema durante o evento (por exemplo, falha de energia no estádio).
4. Envie uma imagem de quebra de AD para ocultar o feed, durante de eventos em direto.

Seguem-se as propriedades que pode definir se os anúncios de sinalização. 

### <a name="duration"></a>Duração
A duração, em segundos, da quebra comercial. Isso deve ser um valor positivo diferente de zero para iniciar a garantia de reparação comercial. Quando uma quebra de comercial está em curso e a duração é definida como zero com o CueId correspondentes a quebra de comercial em curso, em seguida, essa garantia de reparação foi cancelada.

### <a name="cueid"></a>CueId
Um ID exclusivo para a quebra comercial, para ser utilizadas pela aplicação a jusante para efetuar ações adequadas. Tem de ser um número inteiro positivo. Pode definir este valor para qualquer número inteiro aleatório ou usar um sistema a montante para controlar os Ids de indicação. Certifique-se normalizar qualquer IDs para números inteiros positivos antes de submeter por meio da API.

### <a name="show-slate"></a>Show slate
Opcional. Sinaliza o codificador em direto para mudar para o [predefinido slate](media-services-manage-live-encoder-enabled-channels.md#default_slate) de imagem, durante e ocultar a transmissão de vídeo de entrada. Áudio também ficará sem áudio durante a imagem fixa. A predefinição é **false**. 

A imagem utilizada que vai ser especificado por meio da propriedade de Id de recurso de fixa predefinida no momento da criação de canal. A imagem fixa será esticada para se ajustar ao tamanho da imagem de apresentação. 

## <a name="insert-slate--images"></a>Inserir as imagens de imagem fixa
O codificador em direto dentro do canal pode ser indicado para mudar para uma imagem fixa. Também pode ser indicado para terminar uma ficha em curso. 

O codificador em direto pode ser configurado para mudar para uma imagem fixa e ocultar o sinal de vídeo de entrada em determinadas situações – por exemplo, durante uma garantia de reparação do ad. Se tais uma ficha não estiver configurada, o vídeo de entrada não está oculto durante essa garantia de reparação do ad.

### <a name="duration"></a>Duração
A duração da ficha em segundos. Isso deve ser um valor positivo diferente de zero para iniciar a imagem fixa. Se existir um slate contínuo, e uma duração de zero for especificada, em seguida, esse slate em curso será terminado.

### <a name="insert-slate-on-ad-marker"></a>Inserir imagem fixa num marcador de publicidade
Quando definido como true, esta definição configura o codificador em direto para inserir uma imagem fixa durante uma garantia de reparação do ad. O valor predefinido é verdadeiro. 

### <a id="default_slate"></a>Id do elemento fixa predefinida

Opcional. Especifica o Id de recurso do recurso de serviços de suporte de dados que contém a imagem fixa. A predefinição é nulo. 


> [!NOTE] 
> Antes de criar o canal, a imagem fixa com as seguintes restrições deve ser enviada como um recurso dedicado (não existem outros ficheiros devem ser deste recurso). Esta imagem é utilizada apenas quando o codificador em direto é inserir um slate devido a uma quebra de ad ou tiver sido assinalado explicitamente para inserir uma imagem fixa. Atualmente, não existe nenhuma opção para utilizar uma imagem personalizada quando o codificador em direto entra tais num estado "sinal de entrada perdido". Pode votar para esta funcionalidade [aqui](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel).


* No máximo 1920 x 1080 na resolução.
* No máximo 3 Mbytes de tamanho.
* O nome do ficheiro tem de ter uma extensão *. jpg.
* A imagem deve ser carregada para um elemento como o AssetFile apenas em que o recurso e este AssetFile deverá ser marcado como ficheiro principal. O elemento não pode ser armazenamento encriptado.

Se o **predefinição azulado Id do elemento** não for especificado, e **Inserir imagem fixa num marcador de publicidade** está definida como **verdadeiro**, uma imagem de serviços de multimédia do Azure de predefinição será utilizada para ocultar o vídeo de entrada Stream. Áudio também ficará sem áudio durante a imagem fixa. 

## <a name="channels-programs"></a>Programas do canal
Um canal está associado a programas que permitem controlar a publicação e armazenamento de segmentos numa transmissão em fluxo em direto. Canais gerem Programas. A relação de canal e o programa é muito semelhante à multimédia tradicional onde um canal tem um fluxo constante de conteúdo e um programa está confinado a alguns eventos temporizados nesse canal.

Pode especificar o número de horas que pretenda manter o conteúdo gravado para o programa através da configuração da duração da **Janela de Arquivo**. Este valor pode ser definido a partir de um mínimo de 5 minutos até um máximo de 25 horas. Duração da janela de arquivo dita também que o número máximo de clientes de tempo pode recuar a partir da posição atual em direto. Os programas podem ser executados durante o período de tempo especificado, mas o conteúdo que se situe atrás da duração da janela é continuamente descartado. O valor desta propriedade também determina durante quanto tempo os manifestos dos clientes podem aumentar.

Cada programa está associado um recurso que armazena o conteúdo de transmissão em fluxo contínuo. Um elemento é mapeado para um contentor de BLOBs de blocos na conta de armazenamento do Azure e ficheiros no elemento são armazenados como blobs existentes nesse contentor. Para publicar o programa, para que seus clientes podem ver o fluxo tem de criar um localizador OnDemand para o elemento associado. Ter este localizador irá permitir compilar um URL de transmissão em fluxo que pode fornecer aos seus clientes.

Um canal suporta até três programas em execução em simultâneo para que possa criar vários arquivos da mesma transmissão em fluxo recebido. Isto permite publicar e arquivar diferentes partes de um evento, conforme necessário. Por exemplo, os seus requisitos de negócios devem arquivar 6 horas de um programa, mas difundir apenas os últimos 10 minutos. Para tal, tem de criar dois programas em execução em simultâneo. Um programa está definido para arquivar 6 horas do evento, mas o programa não está publicado. O outro programa está definido para arquivar durante 10 minutos e este está publicado.

Não deve reutilizar programas existentes para novos eventos. Em vez disso, crie e inicie um novo programa para cada evento, conforme descrito na secção de programação em direto de transmissão em fluxo aplicativos.

Inicie o programa quando estiver pronto para começar a transmissão em fluxo e o arquivamento. Pare o programa sempre que pretender interromper a transmissão e arquivar o evento. 

Para eliminar conteúdo arquivado, pare e elimine o programa e, em seguida, elimine o elemento associado. Não é possível eliminar um elemento se este é utilizado por um programa; o programa deve ser eliminado primeiro. 

Mesmo depois de parar e eliminar o programa, os utilizadores conseguirão transmitir o seu conteúdo arquivado como um vídeo a pedido até que elimine o elemento.

Se pretende manter o conteúdo arquivado, mas não o quer manter disponível para transmissão em fluxo, elimine o localizador de transmissão em fluxo.

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>Obter uma pré-visualização em miniatura de uma transmissão em direto
Quando o Live Encoding está ativado, agora pode obter uma pré-visualização do feed em direto, quando ela atinge o canal. Isso pode ser uma ferramenta valiosa para verificar se o feed ao vivo, na verdade, está a atingir o canal. 

## <a id="states"></a>Estados de um canal e como os Estados de mapeiam para o modo de faturação
O estado atual de um Canal. Os valores possíveis incluem:

* **Parado**. Este é o estado inicial de um Canal após a sua criação. Neste estado, as propriedades do Canal podem ser atualizadas, mas a transmissão em fluxo não é permitida.
* **A iniciar**. O Canal está a ser iniciado. Não são permitidas transmissões em fluxo nem atualizações durante este estado. Caso ocorra um erro, o Canal volta para o estado Parado.
* **Executar**. O Canal é capaz de processar transmissões em fluxo.
* **A parar**. O Canal está a ser parado. Não são permitidas transmissões em fluxo nem atualizações durante este estado.
* **A eliminar**. O Canal está a ser apagado. Não são permitidas transmissões em fluxo nem atualizações durante este estado.

A tabela seguinte mostra como os estados de um Canal mapeiam para o modo de faturação. 

| Estado do canal | Indicadores IU do portal | Foi cobrado? |
| --- | --- | --- |
| A iniciar |A iniciar |Não (estado transitório) |
| A executar |Pronto (nenhum programa em execução)<br/>ou<br/>A Transmitir em fluxo (pelo menos um programa em execução) |Sim |
| A parar |A parar |Não (estado transitório) |
| Parada |Parada |Não |

> [!NOTE]
> Atualmente, a média de início do canal é cerca de dois minutos, mas às vezes, pode demorar até mais de 20 minutos. Reposições do canal podem demorar até 5 minutos.
> 
> 

## <a id="Considerations"></a>Considerações
* Quando um canal de **padrão** ocorre com o tipo de codificação com uma perda de feed de origem de entrada/contribuição, contrabalança-lo ao substituir o áudio/vídeo de origem uma imagem fixa de erro e silêncio. O canal irá continuar a emitir uma ficha até que a entrada/contribuição feed retoma. Recomendamos que um canal em direto não ser deixada no estado durante mais de 2 horas. Esse ponto, o comportamento de um canal no restabelecimento de ligação de entrada não é garantido, nenhuma delas é seu comportamento em resposta a um comando de reposição. Terá de parar o canal, eliminá-la e criar um novo.
* Não é possível alterar o protocolo de entrada enquanto o Canal ou os seus programas associados estiverem em execução. Se necessitar de protocolos diferentes, deve criar canais separados para cada protocolo de entrada.
* Sempre que reconfigurar o codificador em direto, chamar o **repor** método no canal. Antes de repor o canal, terá de parar o programa. Depois de repor o canal, reinicie o programa.
* Um canal pode ser parado apenas quando ele está no Estado em execução, e todos os programas no canal foram parados.
* Por predefinição só pode adicionar 5 canais à sua conta de Media Services. Trata-se de uma quota não restritiva em todas as contas de novo. Para obter mais informações, consulte [Quotas e limitações](media-services-quotas-and-limitations.md).
* Não é possível alterar o protocolo de entrada enquanto o Canal ou os seus programas associados estiverem em execução. Se necessitar de protocolos diferentes, deve criar canais separados para cada protocolo de entrada.
* É faturado apenas quando o canal permanece no **em execução** estado. Para obter mais informações, consulte [isso](media-services-manage-live-encoder-enabled-channels.md#states) secção.
* Atualmente, a duração máxima recomendada de um evento em direto é de 8 horas. Contacte a amslived@microsoft.com se tiver de executar um Canal durante períodos de tempo mais longos.
* Certifique-se de ter o partir do qual pretende transmitir conteúdo no ponto final de transmissão a **em execução** estado.
* A predefinição de codificação usa a noção de "taxa de quadros máximo" de 30 fps. Então, se a entrada é 60fps / 59.94i, os quadros de entrada são ignorados/desaprovisionamento-interlaced para 30/29.97 fps. Se a entrada for 50fps/50i, os quadros de entrada são ignorados/desaprovisionamento-interlaced para 25 fps. Se a entrada for 25 fps, a saída permanece em 25 fps.
* Não se esqueça de parar de canais do seu quando tiver terminado. Se não o fizer, irá continuar a faturação.

## <a name="known-issues"></a>Problemas Conhecidos
* Tempo de inicialização canal foi aperfeiçoado para uma média de 2 minutos, mas às vezes à demanda aumentada pode ainda demorar até mais de 20 minutos.
* Imagens de imagem fixa devem estar em conformidade com restrições descritas [aqui](media-services-manage-live-encoder-enabled-channels.md#default_slate). Se está tentando criar um canal com uma ficha de predefinição que é maior do que 1920 x 1080, o pedido, por fim, irá apresentar um erro.
* Mais uma vez... não se esqueça de canais de seu parar quando tiver terminado de transmissão em fluxo. Se não o fizer, irá continuar a faturação.

## <a name="next-step"></a>Passo seguinte
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tópicos relacionados
[Entrega de eventos de transmissão em fluxo em direto com os serviços de multimédia do Azure](media-services-overview.md)

[Criar canais que realizam live encoding a partir de uma única de velocidade de transmissão em fluxo de velocidade de transmissão adaptável com o Portal](media-services-portal-creating-live-encoder-enabled-channel.md)

[Criar canais que realizam live encoding a partir de uma única de velocidade de transmissão em fluxo de velocidade de transmissão adaptável com o SDK do .NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[Gerir canais com a REST API](https://docs.microsoft.com/rest/api/media/operations/channel)

[Conceitos dos serviços de multimédia](media-services-concepts.md)

[Especificação de ingestão de MP4 fragmentado em direto de serviços de multimédia do Azure](media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png

