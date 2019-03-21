---
title: Como realizar uma transmissão em fluxo em direto utilizando os Serviços de Multimédia do Azure para criar transmissões em fluxo com velocidade de transmissão múltipla com o Portal do Azure | Microsoft Docs
description: Este tutorial explica-lhe os passos da criação de um canal que recebe uma transmissão em fluxo em direto com uma velocidade de transmissão única e a codifica para uma transmissão em fluxo com velocidade de transmissão múltipla utilizando o Portal do Azure.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.assetid: 504f74c2-3103-42a0-897b-9ff52f279e23
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 1482569e415971fba98de8a586cc2868cc574198
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258093"
---
# <a name="how-to-perform-live-streaming-using-media-services-to-create-multi-bitrate-streams-with-the-azure-portal"></a>Como realizar a transmissão em direto através dos serviços de multimédia para criar transmissões em fluxo com o portal do Azure  
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
> * [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
> * [API REST](https://docs.microsoft.com/rest/api/media/operations/channel)
> 
> 

Este tutorial explica-lhe os passos da criação de um **Canal** que recebe uma transmissão em fluxo em direto com uma velocidade de transmissão única e a codifica para uma transmissão com velocidade de transmissão múltipla.

> [!NOTE]
> Para obter mais informações conceptuais relacionadas com Canais ativados para Live Encoding, consulte [Transmissão em fluxo em direto através dos Media Services do Azure para criar transmissões com velocidade de transmissão múltipla](media-services-manage-live-encoder-enabled-channels.md).
> 
> 

## <a name="common-live-streaming-scenario"></a>Cenário Comum de Transmissão em Fluxo em Direto
Os seguintes são passos gerais referentes à criação de aplicações comuns de transmissão em fluxo em direto.

> [!NOTE]
> Atualmente, a duração máxima recomendada de um evento em direto é de 8 horas. Contacte a amslived@microsoft.com se tiver de executar um Canal durante períodos de tempo mais longos.

1. Ligue uma câmara de vídeo a um computador. Iniciar e configurar um codificador em direto no local que possa enviar um fluxo de velocidade de transmissão única dos seguintes protocolos: RTMP ou Smooth Streaming. Para obter mais informações, consulte [Suporte RTMP dos Media Services do Azure e Codificadores em Direto](https://go.microsoft.com/fwlink/?LinkId=532824).

    Este passo também pode ser realizado depois de criar o Canal.
2. Crie e inicie um Canal. 
3. Obtenha o URL de inserção do Canal. 

    O URL de inserção é utilizado pelo codificador em direto para enviar a transmissão para o Canal.
4. Obtenha o URL de pré-visualização do Canal. 

    Utilize este URL para verificar se o canal está a receber corretamente a transmissão em fluxo em direto.
5. Crie um evento/programa (que também irá criar um elemento). 
6. Publique o evento (que criará um localizador OnDemand para o elemento associado).    
7. Inicie o evento quando estiver pronto para começar a transmissão em fluxo e o arquivamento.
8. Opcionalmente, o codificador em direto pode ser indicado para iniciar um anúncio. O anúncio é inserido na transmissão de saída.
9. Pare o evento sempre que pretender interromper a transmissão em fluxo e arquivar o evento.
10. Elimine o evento (e, opcionalmente, elimine o elemento).   

## <a name="in-this-tutorial"></a>Neste tutorial
Neste tutorial, o Portal do Azure é utilizado para realizar as seguintes tarefas: 

1. Crie um canal ativado para realizar live encoding.
2. Obtenha o URL de Inserção para o fornecer ao codificador em direto. O codificador em direto irá utilizar este URL para inserir a transmissão em fluxo no Canal.
3. Crie um evento/programa (e um elemento).
4. Publique o elemento e obtenha os URLs de transmissão em fluxo.  
5. Reproduzir os conteúdos.
6. Faça a limpeza.

## <a name="prerequisites"></a>Pré-requisitos
O seguinte é necessário para concluir o tutorial.

* Para concluir este tutorial, precisa de uma conta do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. 
  Para obter mais detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Uma conta dos Media Services. Para criar uma conta dos Media Services, consulte [Criar Conta](media-services-portal-create-account.md).
* Uma câmara Web e um codificador que possa enviar uma transmissão de velocidade de transmissão única.

## <a name="create-a-channel"></a>Criar um canal
1. No [portal do Azure](https://portal.azure.com/), selecione os Serviços de Multimédia e, em seguida, clique no nome da sua conta dos Serviços de Multimédia.
2. Selecione **Transmissão em Direto**.
3. Selecione **Criação personalizada**. Esta opção permitir-lhe-á criar um canal que está ativado para codificação em direto.

    ![Criar um canal](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel.png)
4. Clique em **Definições**.

   1. Escolha o tipo de canal do **Live Encoding**. Este tipo especifica que pretende criar um Canal ativado para live encoding. Isso significa que a transmissão em fluxo de velocidade de transmissão de entrada é enviada para o Canal e codificada como uma transmissão em fluxo com velocidade de transmissão múltipla através de configurações específicas de codificador em direto. Para obter mais informações, consulte [Transmissão em fluxo em direto utilizando os Media Services do Azure para criar transmissões em fluxo com velocidade de transmissão múltipla](media-services-manage-live-encoder-enabled-channels.md). Clique em OK.
   2. Especifique o nome de um canal.
   3. Clique em OK na parte inferior do ecrã.
5. Selecione o separador **Ingerir**.

   1. Nesta página, pode selecionar um protocolo de transmissão em fluxo. Para o tipo de canal do **Live Encoding**, as opções de protocolo válidas são:

      * MP4 fragmentado de velocidade de transmissão única (Transmissão em Fluxo Uniforme)
      * RTMP de velocidade transmissão única

        Para uma explicação mais detalhada sobre cada protocolo, consulte [Transmissão em fluxo em direto utilizando os Media Services do Azure para criar transmissões em fluxo com velocidade de transmissão múltipla](media-services-manage-live-encoder-enabled-channels.md).

        Não é possível alterar a opção de protocolo enquanto o Canal ou os seus evento/programas associados estiverem em execução. Se necessitar de protocolos diferentes, deve criar canais separados para cada protocolo de transmissão em fluxo.  
   2. Pode aplicar restrição de IP na ingestão. 

       Pode definir os endereços IP que estão autorizados a ingerir um vídeo neste canal. Os endereços IP permitidos podem ser especificados como um endereço IP único (por exemplo,  "10.0.0.1"), um intervalo IP com um endereço IP e uma máscara sub-rede CIDR (por exemplo "10.0.0.1/22") ou um intervalo IP com um endereço IP e uma máscara sub-rede de ponto decimal (por exemplo, '10.0.0.1(255.255.252.0)').

       Se não for especificado qualquer endereço IP e não existir nenhuma definição de regra, então, não será permitido qualquer endereço IP. Para permitir um endereço IP, crie uma regra e defina 0.0.0.0/0.
6. No separador **ré-visualização**, aplique de restrição de IP na pré-visualização.
7. No separador **Encoding**, especifique a configuração predefinida de codificação. 

    Atualmente, a única predefinição do sistema que pode selecionar é **720p Padrão**. Para especificar uma configuração predefinida, abra um pedido de suporte da Microsoft. Em seguida, introduza o nome da configuração predefinida criada para si. 

> [!NOTE]
> Atualmente, o arranque do Canal pode demorar até 30 minutos. A reposição do canal pode demorar até 5 minutos.
> 
> 

Uma vez criado o Canal, pode clicar no canal e selecionar **Definições** onde pode ver as configurações dos canais. 

Para obter mais informações, consulte [Transmissão em fluxo em direto utilizando os Media Services do Azure para criar transmissões em fluxo com velocidade de transmissão múltipla](media-services-manage-live-encoder-enabled-channels.md).

## <a name="get-ingest-urls"></a>Obter URLs de inserção
Assim que o canal seja criado, pode obter os URLs de inserção que fornecerá ao codificador em direto. O codificador utiliza estes URLs para exibir uma transmissão um fluxo direto.

![ingesturls](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ingest-urls.png)

## <a name="create-and-manage-events"></a>Criar e gerir eventos
### <a name="overview"></a>Descrição geral
Um canal está associado a eventos/programas que permitem controlar a publicação e armazenamento de segmentos numa transmissão em fluxo em direto. Os canais gerem eventos/programas. A relação entre o Canal e o Programa é muito semelhante à multimédia tradicional onde um canal tem uma transmissão em fluxo constante de conteúdo e um programa está confinado a alguns eventos temporizados nesse canal.

Pode especificar o número de horas que pretenda manter o conteúdo gravado para o evento através da configuração da duração da **Janela de Arquivo**. Este valor pode ser definido a partir de um mínimo de 5 minutos até um máximo de 25 horas. A duração da janela de arquivo dita também o tempo máximo que os clientes podem recuar a partir da posição atual em direto. Os eventos podem ser executados durante o período de tempo especificado, contudo, o conteúdo que não respeitar essa duração da janela é continuamente descartado. O valor desta propriedade também determina durante quanto tempo os manifestos dos clientes podem aumentar.

Cada evento está associado a um Elemento. Para publicar o evento, deve criar um localizador OnDemand para o elemento associado. Ter este localizador irá permitir compilar um URL de transmissão em fluxo que pode fornecer aos seus clientes.

Um canal suporta até três eventos em execução em simultâneo para que possa criar vários arquivos da mesma transmissão em fluxo recebida. Isto permite publicar e arquivar diferentes partes de um evento, conforme necessário. Por exemplo, os seus requisitos de negócios devem arquivar 6 horas de um evento, mas difundir apenas os últimos 10 minutos. Para tal, tem de criar dois eventos em execução em simultâneo. Um evento está definido para arquivar 6 horas do evento, mas o programa não está publicado. O outro evento está definido para arquivar durante 10 minutos e este está publicado.

Não deve reutilizar programas existentes para novos eventos. Em vez disso, crie e inicie um novo programa para cada evento.

Inicie um evento/programa quando estiver pronto para começar a transmissão em fluxo e o arquivamento. Pare o evento sempre que pretender interromper a transmissão em fluxo e arquivar o evento. 

Para eliminar conteúdo arquivado, pare e elimine o evento e, em seguida, elimine o elemento associado. Não é possível eliminar um elemento se este é utilizado pelo evento; o evento deve ser eliminado primeiro. 

Mesmo depois de parar e eliminar o evento, os utilizadores conseguirão transmitir o seu conteúdo arquivado como um vídeo a pedido, desde que não elimine o elemento.

Se pretende manter o conteúdo arquivado, mas não o quer manter disponível para transmissão em fluxo, elimine o localizador de transmissão em fluxo.

### <a name="createstartstop-events"></a>Criar/iniciar/parar eventos
Assim que a transmissão em fluxo esteja a ser enviada para o Canal, pode começar o evento de transmissão em fluxo através da criação de um Elemento, Programa e Localizador de Transmissão em Fluxo. Isto irá arquivar a transmissão em fluxo e torná-la disponível para os espetadores através do Ponto Final de Transmissão em Fluxo. 

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 

Existem duas formas de iniciar o evento: 

1. Na página **Canal**, prima **Evento em Direto** para adicionar um novo evento.

    Especifique: nome do evento, nome do elemento, janela de arquivo e opção de encriptação.

    ![createprogram](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-program.png)

    Se tiver marcado **Publicar este evento em direto agora**, serão criados os URLs de PUBLICAÇÃO.

    Pode premir **Iniciar**, sempre que estiver pronto para transmitir o evento.

    Assim que iniciar o evento, pode premir **Ver** para começar a reproduzir o conteúdo.
2. Em alternativa, pode utilizar um atalho e premir o botão **Go Live** na página **Canal**. Isto irá criar um Elemento de predefinição, Programa e Localizador de Transmissão em Fluxo.

    O programa é denominado **predefinido** e a janela de arquivo está definida para 8 horas.

Pode ver o evento publicado a partir da página **Evento em direto**. 

Se clicar em **Off Air**, parará todos os eventos em direto. 

## <a name="watch-the-event"></a>Ver o evento
Para ver o evento, clique em **Ver** no Portal do Azure ou copie o URL de transmissão em fluxo e utilize um leitor da sua preferência. 

![Criado](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-play-event.png)

O evento em direto converte automaticamente os eventos para conteúdo a pedido quando parado.

## <a name="clean-up"></a>Limpeza
Se terminar a transmissão em fluxo de eventos e pretende limpar os recursos aprovisionados anteriormente, siga o procedimento seguinte.

* Termine o envio da transmissão em fluxo do codificador.
* Pare o canal. Assim que o Canal esteja parado, não será cobrado qualquer custo. Quando quiser reiniciar a transmissão, esta terá o mesmo URL de inserção, desta forma, não terá de reconfigurar o codificador.
* Pode parar o seu Ponto Final de Transmissão em Fluxo, a menos que pretenda continuar a fornecer o arquivo do seu evento em direto como uma transmissão em fluxo a pedido. Se o canal estiver num estado parado, não será cobrado qualquer custo.

## <a name="view-archived-content"></a>Ver conteúdo arquivado
Mesmo depois de parar e eliminar o evento, os utilizadores conseguirão transmitir o seu conteúdo arquivado como um vídeo a pedido, desde que não elimine o elemento. Não é possível eliminar um elemento se este é utilizado por um evento; o evento deve ser eliminado primeiro. 

Para gerir os seus elementos, selecione **Definição** e clique em **Elementos**.

![Elementos](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-assets.png)

## <a name="considerations"></a>Considerações
* Atualmente, a duração máxima recomendada de um evento em direto é de 8 horas. Contacte a amslived@microsoft.com se tiver de executar um Canal durante períodos de tempo mais longos.
* Certifique-se de que o ponto final de transmissão em fluxo a partir do qual quer transmitir o seu conteúdo está no estado **Em execução**.

## <a name="next-step"></a>Passo seguinte
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

