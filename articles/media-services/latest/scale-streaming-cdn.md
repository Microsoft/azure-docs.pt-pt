---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>Campos obrigatórios. Veja mais na aka.ms/skyeye/meta.
título: Stream content with CDN integration : Azure Media Services description: Learn about streaming content with CDN integration, bem como prefetching and Origin-Assist CDN-Prefetch.
serviços: media-services documentationcenter: 'author: IngridAtMicrosoft manager: femila editor: '' ms.service: media-services ms.workload: ms.topic: conceptual ms.date: 08/31/2020 ms.author: inhenkel
---

# <a name="stream-content-with-cdn-integration"></a>Stream de conteúdo com integração de CDN

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Rede de Entrega de Conteúdos (CDN) oferece aos programadores uma solução global para o fornecimento rápido de conteúdo de largura de banda alta aos utilizadores, ao colocar em cache o respetivo conteúdo em nós físicos estrategicamente colocados em todo o mundo.  

O conteúdo de caches CDN transmitido a partir de um Ponto Final de Streaming de Serviços de Mídia [(origem)](streaming-endpoint-concept.md) por código, por protocolo de streaming, por bitrate, por formato de recipiente, e por encriptação/DRM. Para cada combinação de código-streaming protocolo-container-bitrate-encriptação, haverá uma cache CDN separada.

O conteúdo popular será servido diretamente a partir da cache CDN, desde que o fragmento de vídeo esteja em cache. É provável que os conteúdos ao vivo sejam em cache porque normalmente temos muitas pessoas a ver exatamente a mesma coisa. O conteúdo a pedido pode ser um pouco mais complicado porque pode ter algum conteúdo que seja popular e outros que não sejam. Se tens milhões de ativos de vídeo onde nenhum deles é popular (apenas um ou dois espectadores por semana) mas tens milhares de pessoas a ver todos os vídeos diferentes, o CDN torna-se muito menos eficaz.

Também é preciso considerar como o streaming adaptativo funciona. Cada fragmento de vídeo individual é cached como sua própria entidade. Por exemplo, imagine a primeira vez que um determinado vídeo é visto. Se o espectador saltar por aí a ver apenas alguns segundos aqui e ali, apenas os fragmentos de vídeo associados ao que a pessoa viu ficam em cache na CDN. Com o streaming adaptativo, normalmente tem 5 a 7 bitrates diferentes de vídeo. Se uma pessoa está a ver um bitrate e outra está a ver um bitrate diferente, então estão cada um em cache separadamente no CDN. Mesmo que duas pessoas estejam a ver a mesma bitrate, podem estar a transmitir diferentes protocolos. Cada protocolo (HLS, MPEG-DASH, Smooth Streaming) é em cache separadamente. Assim, cada bitrate e protocolo são em cache separadamente e apenas os fragmentos de vídeo que foram solicitados estão em cache.

Com exceção do ambiente de teste, recomendamos que a CDN seja ativada tanto para os pontos finais de streaming Standard como para Premium. Cada tipo de ponto final de streaming tem um limite de produção suportado diferente.
É difícil fazer um cálculo preciso para o número máximo de fluxos simultâneos suportados por um ponto final de streaming, uma vez que existem vários fatores a ter em conta. Incluem-se:

- Bitrates máximos utilizados para o streaming
- Pré-tampão do jogador e comportamento de comutação. Os jogadores tentam rebentar segmentos a partir de uma origem e usam a velocidade de carga para calcular a comutação de bitrate adaptativo. Se um ponto final de streaming se aproximar da saturação, os tempos de resposta podem variar e os jogadores começam a mudar para uma qualidade mais baixa. Como isto está a reduzir a carga nos jogadores do Streaming Endpoint, reduza para uma qualidade mais alta criando gatilhos de comutação indesejados.
No geral, é seguro estimar os fluxos máximos simultâneos, tomando a produção final máxima de streaming e dividindo-a pela bitrate máxima (assumindo que todos os jogadores usam a bitrate mais alta.) Por exemplo, pode ter um ponto final de streaming Standard que está limitado a 600 Mbps e o bitrate mais alto de 3Mbp. Neste caso, aproximadamente 200 fluxos simultâneos são suportados na bitrate superior. Lembre-se de ter em conta os requisitos de largura de banda áudio também. Embora um fluxo de áudio possa ser transmitido apenas a 128 km/h, o streaming total aumenta rapidamente quando o multiplica pelo número de fluxos simultâneos.

Este tópico discute a integração do [CDN.](#enable-azure-cdn-integration) Também explica a prefetching (caching ativo) e o conceito [de CDN-Prefetch de Assistência de Origem.](#origin-assist-cdn-prefetch)

## <a name="considerations"></a>Considerações

- O [ponto final de streaming](streaming-endpoint-concept.md) e o URL de streaming permanecem os `hostname` mesmos quer ativem ou não a CDN.
- Se precisar da capacidade de testar o seu conteúdo com ou sem CDN, crie outro ponto final de streaming que não esteja ativado pela CDN.

## <a name="enable-azure-cdn-integration"></a>Permitir a integração do Azure CDN

> [!IMPORTANT]
> Não é possível permitir a CDN para o julgamento ou para as contas do Azure.
>
> A integração da CDN está ativada em todos os centros de dados do Azure, exceto nas regiões do Governo Federal e da China.

Depois de um ponto final de streaming ser a provisionado com CDN ativado, há um tempo de espera definido nos Serviços de Mídia antes de a atualização do DNS ser feita para mapear o ponto final de streaming para o ponto final da CDN.

Se mais tarde pretender desativar/ativar o CDN, o seu ponto final de streaming deve estar no estado **de paragem.** Uma vez iniciado o ponto final de streaming, pode levar até quatro horas para que a integração do CDN do Azure seja ativada e para que as alterações estejam ativas em todos os POPs do CDN. No entanto, pode iniciar o seu ponto final de streaming e transmitir sem interrupções a partir do ponto final de streaming. Uma vez concluída a integração, o fluxo é entregue a partir do CDN. Durante o período de provisionamento, o seu ponto final de streaming estará no estado **inicial** e poderá observar um desempenho degradado.

Quando o ponto final de streaming Standard é criado, é configurado por padrão com Standard Verizon. Pode configurar fornecedores Premium Verizon ou Standard Akamai utilizando APIs REST.

A integração do Azure Media Services com a Azure CDN é implementada na **Azure CDN da Verizon** para os pontos finais de streaming padrão. Os pontos finais de streaming premium podem ser configurados utilizando todos os **níveis e fornecedores de preços da Azure CDN**.

> [!NOTE]
> Para mais detalhes sobre a Azure CDN, consulte a [visão geral](../../cdn/cdn-overview.md)do CDN .

## <a name="determine-if-a-dns-change-was-made"></a>Determinar se foi feita uma alteração ao DNS

Pode determinar se a alteração do DNS foi efetuada num ponto final de streaming (o tráfego está a ser direcionado para o Azure CDN) utilizando <https://www.digwebinterface.com> . Se vir azureedge.net nomes de domínio nos resultados, o tráfego está agora a ser apontado para o CDN.

## <a name="origin-assist-cdn-prefetch"></a>Origin-Assist CDN-Prefetch

O caching CDN é um processo reativo. Se a CDN conseguir prever qual será o próximo objeto, a CDN pode solicitar e cache proactivamente o próximo objeto. Com este processo, pode obter um cache-hit para todos (ou a maioria) dos objetos, o que melhora o desempenho.

O conceito de prefetching esforça-se por posicionar objetos na "borda da internet" antecipando que estes serão solicitados iminentemente pelo jogador, reduzindo assim o tempo para entregar esse objeto ao jogador.

Para atingir este objetivo, um ponto final de streaming (origem) e CDN precisam trabalhar de mãos dadas de algumas formas:

- A origem dos Serviços de Comunicação social precisa de ter a "inteligência" (Origin-Assist) para informar a CDN sobre o próximo objeto a pré-venda.
- O CDN faz a pré-gravura e o caching (parte CDN-prefetch). A CDN também precisa de ter a "inteligência" para informar a origem se é uma prefetch ou uma busca regular, lidar com as 404 respostas, e uma maneira de evitar um ciclo de pré-ferimentos interminável.

### <a name="benefits"></a>Benefícios

Os benefícios da funcionalidade *Origin-Assist CDN-Prefetch* incluem:

- O Prefetch melhora a qualidade da reprodução de vídeo, pré-posicionando os segmentos de vídeo antecipados na borda durante a reprodução, reduzindo a latência para o espectador e melhorando os tempos de descarregamento do segmento de vídeo. Isto resulta em tempo de arranque de vídeo mais rápido e menos ocorrências de rejeição.
- Este conceito é aplicável ao cenário geral de origem cdn e não se limita aos meios de comunicação.
- A Akamai adicionou esta funcionalidade ao [Akamai Cloud Embed (ACE)](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html).

> [!NOTE]
> Esta funcionalidade ainda não é aplicável ao CDN da Akamai integrado com o ponto final de streaming dos Media Services. No entanto, está disponível para clientes dos Media Services que tenham um contrato de Akamai pré-existente e exijam integração personalizada entre a Akamai CDN e a origem dos Media Services.

### <a name="how-it-works"></a>Como funciona

O suporte da CDN para os `Origin-Assist CDN-Prefetch` cabeçalhos (tanto para streaming ao vivo como para vídeo a pedido) está disponível para clientes que tenham contrato direto com a Akamai CDN. A funcionalidade envolve as seguintes trocas de cabeçalho HTTP entre a Akamai CDN e a origem dos Serviços de Comunicação Social:

|Cabeçalho HTTP|Valores|Remetente|Recetor|Objetivo|
| ---- | ---- | ---- | ---- | ----- |
|`CDN-Origin-Assist-Prefetch-Enabled` | 1 (padrão) ou 0 |CDN|Origem|Para indicar que o CDN está ativado prefetch.|
|`CDN-Origin-Assist-Prefetch-Path`| Exemplo: <br/>Fragmentos (vídeo=14000000000000000000000000000000000000000000000000000000000000000000000000000|Origem|CDN|Para fornecer um caminho prefetch para a CDN.|
|`CDN-Origin-Assist-Prefetch-Request`|1 (pedido de precade) ou 0 (pedido regular)|CDN|Origem|Para indicar o pedido da CDN é uma precatão.|

Para ver parte da troca de cabeçalho em ação, pode experimentar os seguintes passos:

1. Utilize o Carteiro ou o CURL para emitir um pedido à origem dos Serviços de Comunicação social para um segmento de áudio ou vídeo ou fragmento. Certifique-se de adicionar o cabeçalho `CDN-Origin-Assist-Prefetch-Enabled: 1` no pedido.
2. Na resposta, deve ver o cabeçalho `CDN-Origin-Assist-Prefetch-Path` com um percurso relativo como o seu valor.

### <a name="supported-streaming-protocols"></a>Protocolos de streaming suportados

A `Origin-Assist CDN-Prefetch` funcionalidade suporta os seguintes protocolos de streaming para streaming ao vivo e a pedido:

* HLS v3
* HLS v4
* HLS CMAF
* DASH (CSF)
* DASH (CMAF)
* Streaming suave

### <a name="faqs"></a>FAQs

* E se um URL de caminho de pré-recorrida for inválido para que a prefâmia cdn obtenha um 404?

    O CDN só cacheá uma resposta de 404 durante 10 segundos (ou outro valor configurado).

* Suponha que tenha um vídeo a pedido. Se o CDN-prefetch estiver ativado, esta funcionalidade implica que uma vez que um cliente solicite o primeiro segmento de vídeo, a prefetch iniciará um loop para pré-prender todos os segmentos de vídeo subsequentes na mesma bitrate?

    Não, a pré-prisão de CDN só é feita após um pedido/resposta iniciado pelo cliente. A pré-prisão de CDN nunca é acionada por uma prefâmia, para evitar um laço de prefetch.

* A Origin-Assist CDN-Prefetch característica está sempre ligado? Como pode ser ligado/desligado?

    Esta funcionalidade está desligada por defeito. Os clientes precisam ligá-lo através da Akamai API.

* Para o streaming ao vivo, o que aconteceria a Origin-Assist se o próximo segmento ou fragmento ainda não estiver disponível?

    Neste caso, a origem dos Serviços de Comunicação Social não fornecerá `CDN-Origin-Assist-Prefetch-Path` cabeçalho e a CDN-prefetch não ocorrerá.

* Como funciona `Origin-Assist CDN-Prefetch` com filtros manifestos dinâmicos?

    Esta característica funciona independentemente do filtro manifesto. Quando o próximo fragmento estiver fora de uma janela de filtro, o seu URL continuará a ser localizado olhando para o manifesto do cliente bruto e depois devolvido como cabeçalho de resposta pré-redação CDN. Assim, o CDN obterá o URL de um fragmento que é filtrado do manifesto DASH/HLS/Smooth. No entanto, o leitor nunca fará um pedido GET à CDN para obter esse fragmento, porque esse fragmento não está incluído no manifesto DASH/HLS/Smooth detido pelo leitor (o jogador não sabe a existência desse fragmento).

* A lista de reprodução/manifesto suave do DASH MPD/HLS pode ser pré-gravada?

    Não, DASH MPD, lista de reprodução master HLS, lista de reprodução de variante HLS ou URL manifesto suave não são adicionados ao cabeçalho pré-gravado.

* Os URLs pré-fetch são relativos ou absolutos?

    Enquanto a Akamai CDN permite ambos, a origem dos Media Services apenas fornece URLs relativos para o caminho da precato, porque não há nenhum benefício aparente na utilização de URLs absolutos.

* Esta funcionalidade funciona com conteúdos protegidos por DRM?

    Sim, uma vez que esta funcionalidade funciona ao nível HTTP, não descodifica nem analisa qualquer segmento/fragmento. Não importa se o conteúdo está encriptado ou não.

* Esta funcionalidade funciona com a Inserção de Anúncios Laterais do Servidor (SSAI)?
    
    Faz para o conteúdo original/principal (o conteúdo original de vídeo antes da inserção de anúncios) funciona, uma vez que a SSAI não altera o tempotabo do conteúdo de origem da origem dos Media Services. Se esta funcionalidade funciona com conteúdo de anúncios depende se a origem do anúncio suporta o Origin-Assist. Por exemplo, se os conteúdos de anúncios também estiverem hospedados no Azure Media Services (origem igual ou separada), os conteúdos de anúncios também serão pré-gravados.

* Esta funcionalidade funciona com conteúdo UHD/HEVC?

    Yes.

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Consulte o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Media Services.

## <a name="next-steps"></a>Próximos passos

* Certifique-se de rever o documento [streaming Endpoint (origem).](streaming-endpoint-concept.md)
* A amostra [deste repositório](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) mostra como iniciar o ponto final de streaming padrão com .NET.
