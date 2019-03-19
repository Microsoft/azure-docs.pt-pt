---
title: Stream em direto com codificadores no local que criam transmissões em fluxo - Azure | Documentos da Microsoft
description: 'Este tópico descreve como configurar um canal que recebe um velocidade de transmissão em fluxo em direto de um codificador no local. O fluxo, em seguida, pode ser entregues aos aplicativos de reprodução de cliente por meio de um ou mais pontos finais de transmissão, através de um dos seguintes protocolos de transmissão em fluxo adaptáveis: HLS, Smooth Streaming, DASH.'
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
ms.author: cenkd;juliako
ms.openlocfilehash: da20e4601b75bcb22546d21f6ad218ac9ba2728b
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58188361"
---
# <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders"></a>Trabalhar com canais que recebem transmissões com velocidade de transmissão em fluxo em direto a partir de codificadores no local

> [!NOTE]
> A partir de 12 de Maio de 2018, os canais em direto será já não suporte o fluxo de transporte RTP/MPEG-2 protocolo de ingestão. Migre de RTP/MPEG-2 para RTMP ou MP4 fragmentado (Smooth Streaming) protocolos de ingestão.

## <a name="overview"></a>Descrição geral
Nos serviços de multimédia do Azure, um *canal* representa um pipeline de processamento de conteúdo de transmissão em direto. Um canal recebe transmissões em direto de entrada em uma das seguintes formas:

* Um codificador em direto de no local envia uma velocidade de transmissão RTMP ou transmissão em fluxo uniforme (MP4 fragmentado) transmita em fluxo para o canal que não está ativado para realizar live encoding com Media Services. Passam as transmissões em fluxo através de canais, sem qualquer processamento adicional. Esse método é chamado *pass-through*. Um codificador em direto também pode enviar um fluxo de velocidade de transmissão única para um canal que não está ativado para live encoding, mas não recomendamos que. Media Services disponibilizam a transmissão aos clientes que solicitação-la.

  > [!NOTE]
  > Usando um método pass-through é a forma mais económica para transmissão em direto.


* Um codificador em direto no local envia um fluxo de velocidade de transmissão única para o canal ativado para realizar live encoding com Media Services dos seguintes formatos: RTMP ou transmissão em fluxo uniforme (MP4 fragmentado). O canal, em seguida, executa a codificação em tempo real do fluxo de velocidade de transmissão única de entrada para um fluxo de vídeo com várias (adaptável). Media Services disponibilizam a transmissão aos clientes que solicitação-la.

Começando com o lançamento de 2.10 de serviços de multimédia, quando cria um canal, pode especificar como pretende que o canal para receber o fluxo de entrada. Também pode especificar se pretende que o canal para realizar live encoding da sua transmissão em fluxo. Tem duas opções:

* **Pass-Through**: Especifica este valor se planeja usar um codificador em direto no local que tenha um fluxo de velocidade de transmissão (um fluxo de pass-through) como saída. Neste caso, o fluxo de entrada passa à saída sem qualquer tipo de codificação. Este é o comportamento de um canal antes do lançamento 2.10. Este artigo fornece detalhes sobre como trabalhar com canais deste tipo.
* **Codificação em direto**: Escolha esse valor se planeja usar serviços de multimédia para codificar a transmissão em direto de velocidade de transmissão única para um fluxo de velocidade de transmissão. A sair de um canal de codificação em direto numa **em execução** Estado incorre em encargos de faturação. Recomendamos que pare imediatamente os seus canais em execução depois do evento de transmissão em fluxo foi concluído para evitar custos adicionais por hora. Media Services disponibilizam a transmissão aos clientes que solicitação-la.

> [!NOTE]
> Este artigo aborda os atributos de canais que não estão ativados para realizar live encoding. Para obter informações sobre como trabalhar com canais ativados para realizar live encoding, consulte [transmissão em direto através dos serviços de multimédia do Azure para criar transmissões em fluxo](media-services-manage-live-encoder-enabled-channels.md).
>
>Para obter informações sobre recomendado no codificadores locais, consulte [recomendado no codificadores locais](media-services-recommended-encoders.md).

O diagrama abaixo representa um fluxo de trabalho por transmissão em direto, que usa um codificador em direto no local para que a velocidade de transmissão RTMP ou MP4 fragmentado (Smooth Streaming) fluxos como saída.

![Fluxo de trabalho em direto][live-overview]

## <a id="scenario"></a>Cenário comum de transmissão em direto
Os passos seguintes descrevem as tarefas envolvidas na criação de aplicações de transmissão em fluxo comuns.

1. Ligue uma câmara de vídeo a um computador. Iniciar e configurar um codificador em direto no local com uma velocidade de transmissão RTMP ou MP4 fragmentado (Smooth Streaming) stream como saída. Para obter mais informações, consulte [Suporte RTMP dos Media Services do Azure e Codificadores em Direto](https://go.microsoft.com/fwlink/?LinkId=532824).

    Também pode executar este passo depois de criar o canal.
2. Crie e inicie um canal.

3. URL de ingestão de obter o canal.

    O codificador em direto utiliza o URL de inserção para enviar a transmissão para o canal.
4. Obter o URL de pré-visualização do canal.

    Utilize este URL para verificar se o canal está a receber corretamente a transmissão em fluxo em direto.
5. Crie um programa.

    Quando utiliza o portal do Azure, a criação de um programa também cria um recurso.

    Quando utiliza o SDK do .NET ou REST, terá de criar um elemento e especifique a utilização deste recurso ao criar um programa.
6. Publique o elemento que está associada com o programa.   

    >[!NOTE]
    >Quando é criada a sua conta de Media Services do Azure, um **predefinição** ponto final de transmissão em fluxo é adicionado à sua conta no **parado** estado. O ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**.

7. Inicie o programa quando estiver pronto para começar a transmissão em fluxo e o arquivamento.

8. Opcionalmente, o codificador em direto pode ser indicado para iniciar um anúncio. O anúncio é inserido na transmissão de saída.

9. Pare o programa sempre que pretender interromper a transmissão e arquivar o evento.

10. Eliminar o programa (e, opcionalmente, elimine o elemento).     

## <a id="channel"></a>Descrição de um canal e seus componentes relacionados
### <a id="channel_input"></a>Entrada de canal (ingerir) configurações
#### <a id="ingest_protocols"></a>Transmissão em fluxo de protocolo de ingestão
Os Media Services suportam a ingestão em direto feeds ao utilizar o MP4 de velocidade de transmissão fragmentada e RTMP de velocidade de transmissão como protocolos de transmissão em fluxo. Quando o RTMP ingerir protocolo de transmissão em fluxo é selecionado, dois pontos finais de (entrada) de ingestão são criadas para o canal:

* **URL principal**: Especifica o ponto final de ingestão do URL completamente qualificado do RTMP de principal do canal.
* **URL secundário** (opcional): Especifica o URL completamente qualificado do RTMP o canal de secundário ponto final de ingestão.

Utilize o URL secundário se pretender melhorar a durabilidade e tolerância a falhas de sua ingestão stream (bem como codificador ativação pós-falha e tolerância a falhas), especialmente para os seguintes cenários:

- Codificador única enviar double para URLs primários e secundários:

    A principal finalidade deste cenário é fornecer mais resiliência na flutuações de rede e jitters. Não processam alguns codificadores RTMP rede desliga bem. Quando ocorre uma desconexão de rede, um codificador pode parar a codificação e, em seguida, não enviar os dados em buffer quando acontece uma restabelecer a ligação. Isso faz com que discontinuities e perda de dados. Desliga da rede pode acontecer devido a uma rede incorreta ou a manutenção do lado do Azure. URLs de primária/secundária reduzem os problemas de rede e fornecem um processo de atualização controlado. Sempre que uma desconexão de rede agendada acontecer, serviços de multimédia gere primário e secundário se desliga e fornece um atrasada desligar entre os dois. Em seguida, os codificadores têm tempo para manter o envio de dados e voltar a ligar novamente. A ordem da desliga pode ser aleatório, mas sempre haverá um atraso entre a primária/secundária ou primária/secundária URLs. Neste cenário, o codificador ainda é o ponto único de falha.

- Vários codificadores, com cada codificador enviar para um ponto de dedicado:

    Este cenário fornece os dois codificador e ingere redundância. Neste cenário, encoder1 envia por push para o URL principal e encoder2 envia por push para o URL secundário. Quando um codificador falha, o codificador de outro pode manter a enviar dados. Redundância de dados pode ser mantida porque os serviços de multimédia não desliga URLs primários e secundários ao mesmo tempo. Este cenário pressupõe que os codificadores são sincronizado de tempo e fornecem exatamente os mesmos dados.  

- Vários codificadores double-enviar para URLs primários e secundários:

    Neste cenário, os dois codificadores enviar dados por push para os URLs primários e secundários. Isso fornece a melhor fiabilidade e a tolerância a falhas, bem como redundância de dados. Este cenário pode tolerar a ambas as falhas do codificador e desliga, mesmo que um codificador deixa de funcionar. Parte do princípio de que os codificadores são sincronizado de tempo e fornecem exatamente os mesmos dados.  

Para obter informações sobre codificadores em direto de RTMP, consulte [suporte RTMP dos serviços de multimédia do Azure e codificadores em direto](https://go.microsoft.com/fwlink/?LinkId=532824).

#### <a name="ingest-urls-endpoints"></a>URLs (pontos de extremidade) de inserção
Um canal fornece um ponto de final de entrada (URL de ingestão) que especifica em que o codificador em direto, para que o codificador pode enviar por push fluxos aos seus canais.   

Pode obter os URLs de ingestão ao criar o canal. Para poder obter estes URLs, o canal não tem de estar no **em execução** estado. Quando estiver pronto para começar a enviar dados para o canal, o canal tem de estar no **em execução** estado. Depois do canal de inicia a ingestão de dados, pode visualizar a sua transmissão em fluxo por meio da URL de pré-visualização.

Tem uma opção de ingestão de um MP4 fragmentado (Smooth Streaming) em fluxo em direto através de uma ligação SSL. Ingerir através de SSL, certifique-se atualizar o URL de inserção para HTTPS. Atualmente, não é possível de ingestão RTMP através de SSL.

#### <a id="keyframe_interval"></a>Intervalo de quadro-chave
Quando estiver usando um codificador em direto no local para gerar o fluxo de velocidade de transmissão, o intervalo de quadro-chave especifica a duração do grupo de imagens (GOP) como utilizado por esse codificador externo. Depois do canal recebe esse fluxo de entrada, pode fornecer a sua transmissão em fluxo em direto para aplicativos de reprodução de cliente em qualquer um dos seguintes formatos: Transmissão em fluxo uniforme, Dynamic Adaptive Streaming através de HTTP (DASH) e HTTP Live Streaming (HLS). Quando está fazendo a transmissão em direto, HLS é sempre empacotado dinamicamente. Por predefinição, os serviços de multimédia calcula automaticamente a HLS segmento empacotamento proporção (fragmentos por segmento) com base no intervalo de quadro-chave que é recebido do codificador em direto.

A tabela seguinte mostra como é calculada a duração de segmento:

| Intervalo de quadro-chave | Rácio de empacotamento de segmento HLS (FragmentsPerSegment) | Exemplo |
| --- | --- | --- |
| Menor ou igual a 3 segundos |3:1 |Se KeyFrameInterval (ou GOP) é de 2 segundos, a proporção de empacotamento de segmento HLS predefinido é 3 para 1. Esta ação cria um segmento HLS 6 segundos. |
| 3 a 5 segundos |2:1 |Se KeyFrameInterval (ou GOP) é 4 segundos, a proporção de empacotamento de segmento HLS de padrão é 2 para 1. Esta ação cria um segmento HLS 8 segundos. |
| Superior a 5 segundos |1:1 |Se KeyFrameInterval (ou GOP) é de 6 segundos, a proporção de empacotamento de segmento HLS predefinido é 1 para 1. Esta ação cria um segmento HLS 6 segundos. |

Pode alterar a proporção de fragmentos por segmento ao configurar a saída do canal e definir FragmentsPerSegment ChannelOutputHls.

Também pode alterar o valor de intervalo de quadro-chave, definindo a propriedade de KeyFrameInterval no ChannelInput. Se definir explicitamente KeyFrameInterval, os HLS segmentar a proporção de empacotamento que fragmentspersegment é calculada através de regras descritas anteriormente.  

Se definir explicitamente KeyFrameInterval e FragmentsPerSegment, serviços de multimédia utiliza os valores que definir.

#### <a name="allowed-ip-addresses"></a>Endereços IP permitidos
Pode definir os endereços IP que estão autorizados a publicar vídeo neste canal. Um endereço IP permitido pode ser especificado como um dos seguintes:

* Um endereço IP único (por exemplo, 10.0.0.1)
* Um intervalo IP que utiliza um endereço IP e uma máscara de sub-rede CIDR (por exemplo, 10.0.0.1/22)
* Um intervalo IP que utiliza um endereço IP e uma máscara de sub-rede de ponto decimal (por exemplo, 10.0.0.1(255.255.252.0))

Se não houver endereços IP são especificados e não existe nenhuma definição de regra, nenhum endereço IP é permitido. Para permitir um endereço IP, crie uma regra e defina 0.0.0.0/0.

### <a name="channel-preview"></a>Pré-visualização do canal
#### <a name="preview-urls"></a>URLs de pré-visualização
Canais de fornecem um ponto de extremidade pré-visualização (URL de pré-visualização) que utilizar para pré-visualizar e validar a sua transmissão antes de mais processamentos e entregas.

Pode obter o URL de pré-visualização ao criar o canal. Para poder obter o URL, o canal não tem de estar no **em execução** estado. Depois do canal de inicia a ingestão de dados, pode visualizar a sua transmissão em fluxo.

Atualmente, o fluxo de pré-visualização pode ser entregues apenas num MP4 fragmentado (Smooth Streaming) formato, independentemente do tipo de entrada especificado. Pode utilizar o [Monitor de estado de funcionamento de transmissão em fluxo uniforme](https://playready.directtaps.net/smoothstreaming/) player para testar a transmissão em fluxo uniforme. Também pode usar um leitor de que está alojado no portal do Azure para ver a sua transmissão em fluxo.

#### <a name="allowed-ip-addresses"></a>Endereços IP permitidos
Pode definir os endereços IP que estão autorizados a ligar para o ponto final de pré-visualização. Se forem especificados não existem endereços IP, qualquer endereço IP é permitido. Um endereço IP permitido pode ser especificado como um dos seguintes:

* Um endereço IP único (por exemplo, 10.0.0.1)
* Um intervalo IP que utiliza um endereço IP e uma máscara de sub-rede CIDR (por exemplo, 10.0.0.1/22)
* Um intervalo IP que utiliza um endereço IP e uma máscara de sub-rede de ponto decimal (por exemplo, 10.0.0.1(255.255.252.0))

### <a name="channel-output"></a>Saída de canal
Para obter informações sobre a saída de canal, consulte a [intervalo de quadro-chave](#keyframe_interval) secção.

### <a name="channel-managed-programs"></a>Gerido pelo canal de programas
Um canal está associado a programas que pode utilizar para controlar a publicação e armazenamento de segmentos numa transmissão em fluxo em direto. Os canais gerem programas. A relação de canal e o programa é semelhante à multimédia tradicional, onde um canal tem um fluxo constante de conteúdo e um programa está confinado a alguns eventos temporizados nesse canal.

Pode especificar o número de horas que pretenda manter o conteúdo gravado para o programa através da configuração da duração da **Janela de Arquivo**. Este valor pode ser definido a partir de um mínimo de 5 minutos até um máximo de 25 horas. Duração da janela de arquivo dita também que o número máximo de clientes de tempo pode recuar a partir da posição atual em direto. Os programas podem ser executados durante o período de tempo especificado, mas o conteúdo que se situe atrás da duração da janela é continuamente descartado. O valor desta propriedade também determina durante quanto tempo os manifestos dos clientes podem aumentar.

Cada programa está associado um elemento que armazena o conteúdo de transmissão em fluxo contínuo. Um elemento é mapeado para um contentor de BLOBs de blocos na conta de armazenamento do Azure e ficheiros no elemento são armazenados como blobs existentes nesse contentor. Para publicar o programa para que seus clientes podem ver o fluxo, tem de criar um localizador OnDemand para o elemento associado. Pode utilizar este localizador para compilar um URL de transmissão em fluxo que pode fornecer aos seus clientes.

Um canal suporta até três programas em execução em simultâneo, para que possa criar vários arquivos da mesma transmissão em fluxo recebido. Pode publicar e arquivar diferentes partes de um evento, conforme necessário. Por exemplo, imagine que é seus requisitos de negócios devem arquivar 6 horas de um programa, mas difundir apenas os últimos 10 minutos. Para tal, tem de criar dois programas em execução em simultâneo. Um programa está definido para arquivar a seis horas do evento, mas o programa não está publicado. O outro programa está definido para arquivar durante 10 minutos e este está publicado.

Não deve reutilizar programas existentes para novos eventos. Em vez disso, crie um novo programa para cada evento. Inicie o programa quando estiver pronto para começar a transmissão em fluxo e o arquivamento. Pare o programa sempre que pretender interromper a transmissão e arquivar o evento.

Para eliminar conteúdo arquivado, pare e elimine o programa e, em seguida, elimine o elemento associado. Não é possível eliminar um recurso, se um programa usa-lo. O programa deve ser eliminado primeiro.

Mesmo depois de parar e eliminar o programa, os utilizadores podem transmitir o seu conteúdo arquivado como um vídeo a pedido, até que elimine o elemento. Se quiser manter o conteúdo arquivado, mas não o tiver disponível para transmissão em fluxo, elimine o localizador de transmissão em fluxo.

## <a id="states"></a>Estados de um canal e faturação
Os valores possíveis para o estado atual de um canal incluem:

* **Parado**: Este é o estado inicial de um canal após a sua criação. Neste estado, as propriedades do canal podem ser atualizadas, mas a transmissão em fluxo não é permitida.
* **A iniciar**: O canal está a ser iniciado. Não são permitidas transmissões em fluxo nem atualizações durante este estado. Se ocorrer um erro, o canal volta para o **parado** estado.
* **Executar**: O canal pode processar transmissões em fluxo.
* **A parar**: O canal está a ser parado. Não são permitidas transmissões em fluxo nem atualizações durante este estado.
* **A eliminar**: O canal está a ser apagado. Não são permitidas transmissões em fluxo nem atualizações durante este estado.

A tabela seguinte mostra como os estados de um canal mapeiam para o modo de faturação.

| Estado do canal | Indicadores IU do portal | Foi cobrado? |
| --- | --- | --- |
| **A partir de** |**A partir de** |Não (estado transitório) |
| **Em execução** |**Pronto** (nenhum programa em execução)<p><p>ou<p>**Transmissão em fluxo** (pelo menos um programa em execução) |Sim |
| **A parar** |**A parar** |Não (estado transitório) |
| **Parado** |**Parado** |Não |

## <a id="cc_and_ads"></a>Inserção de legendas de áudio e o ad fechada
A tabela a seguir demonstra padrões suportados para inserção de legendas de áudio e o ad fechada.

| Standard | Notas |
| --- | --- |
| Legenda oculta CEA-708 e EIA 608 (708/608) |Legenda oculta CEA-708 e EIA 608 são legendagem padrões para os Estados Unidos e Canadá.<p><p>Atualmente, as legendas de áudio só é suportada se efetuado no fluxo de entrada codificado. Tem de utilizar um codificador de multimédia em direto que pode inserir 608 ou 708 legendas no fluxo codificado que é enviado para os serviços de multimédia. Serviços de multimédia fornece os conteúdos com inserido legendas para os seus utilizadores. |
| TTML dentro .ismt (faixas de texto de transmissão em fluxo uniforme) |Um empacotamento dinâmico dos serviços de multimédia permite que os seus clientes para transmitir conteúdo em qualquer um dos seguintes formatos: DASH, HLS ou transmissão em fluxo uniforme. No entanto, se ingerir real de MP4 fragmentado (Smooth Streaming) com as legendas dentro .ismt (transmissão em fluxo uniforme faixas de texto), que pode fornecer o stream apenas clientes de transmissão em fluxo uniforme. |
| SCTE-35 |SCTE 35 é um sistema de sinalização digital que serve para deixa a inserção de publicidade. Recetores Downstream use o sinal a fusão de anúncio no fluxo de tempo atribuído. SCTE 35 têm de ser enviados como um Roteiro disperso no fluxo de entrada.<p><p>Atualmente, o fluxo de entrada suportado apenas Formatar que executa diversas sinais de ad está fragmentado MP4 (transmissão em fluxo uniforme). O único suportado saída formato também é Smooth Streaming. |

## <a id="considerations"></a>Considerações
Quando estiver usando um codificador em direto no local para enviar um fluxo de velocidade de transmissão para um canal, aplicam-se as seguintes restrições:

* Certifique-se de que tem conectividade de Internet livre suficiente para enviar dados para os pontos de ingestão.
* URL de ingestão usando um secundário necessita de largura de banda adicional.
* Fluxo de velocidade de transmissão de entrada pode ter um máximo de 10 níveis de qualidade de vídeo (camadas) e um máximo de 5 faixas de áudio.
* A mais alta velocidade de transmissão de média para qualquer um dos níveis de qualidade do vídeo deve ter menos de 10 Mbps.
* O agregado das velocidades de transmissão de média para todos os fluxos de áudio e vídeos deve ter menos de 25 Mbps.
* Não é possível alterar o protocolo de entrada enquanto o canal ou seus programas associados estiverem em execução. Se necessitar de protocolos diferentes, deve criar canais separados para cada protocolo de entrada.
* Pode ingerir uma velocidade de transmissão única no seu canal. Mas uma vez que o canal não processa o fluxo, os aplicativos cliente também irão receber um fluxo de velocidade de transmissão única. (Não recomendamos esta opção.)

Seguem-se outras considerações relacionadas com a trabalhar com canais e componentes relacionados:

* Sempre que reconfigurar o codificador em direto, chamar o **repor** método no canal. Antes de repor o canal, terá de parar o programa. Depois de repor o canal, reinicie o programa.

  > [!NOTE]
  > Quando reiniciar o programa, precisa de associá-lo a um novo elemento e criar um novo localizador. 
  
* Um canal pode ser parado apenas quando está a ser o **em execução** estado e todos os programas no canal foram parados.
* Por predefinição, pode adicionar apenas cinco canais à sua conta de Media Services. Para obter mais informações, consulte [Quotas e limitações](media-services-quotas-and-limitations.md).
* É-lhe cobrada apenas quando o canal permanece no **em execução** estado. Para obter mais informações, consulte a [Estados e de faturação do Channel](media-services-live-streaming-with-onprem-encoders.md#states) secção.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="feedback"></a>Comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tópicos relacionados
[Recomendado no codificadores locais](media-services-recommended-encoders.md)

[Especificação de ingestão de Azure vidas MP4 fragmentadas dos serviços de multimédia](media-services-fmp4-live-ingest-overview.md)

[Descrição geral dos serviços de multimédia do Azure e cenários comuns](media-services-overview.md)

[Conceitos de serviços de multimédia](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
