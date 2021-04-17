---
title: Coisas a ter em conta ao utilizar o Indexante de Vídeo em escala - Azure
titleSuffix: Azure Media Services
description: Este tópico explica o que as coisas devem considerar ao utilizar o Video Indexer em escala.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: how-to
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: f941d81df670f017d24a7c5011c55fcc4f082605
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531570"
---
# <a name="things-to-consider-when-using-video-indexer-at-scale"></a>Coisas a ter em conta ao utilizar o Indexante de Vídeo em escala

Ao utilizar o indexante Azure Media Services Video para indexar vídeos e o seu arquivo de vídeos está a crescer, considere o dimensionamento. 

Este artigo responde a perguntas como:

* Há algum constrangimento tecnológico que eu precise de ter em conta?
* Existe uma maneira inteligente e eficiente de fazê-lo?
* Posso evitar gastar dinheiro em excesso no processo?

O artigo fornece seis melhores práticas de como usar o Video Indexer em escala.

## <a name="when-uploading-videos-consider-using-a-url-over-byte-array"></a>Ao carregar vídeos considere usar um URL sobre byte array

O Video Indexer dá-lhe a opção de fazer o upload de vídeos a partir de URL ou diretamente enviando o ficheiro como um byte array, este último vem com algumas restrições. Para obter mais informações, consulte [considerações e limitações de upload)](upload-index-videos.md#uploading-considerations-and-limitations)

Primeiro, tem limitações de tamanho de ficheiro. O tamanho do ficheiro byte array é limitado a 2 GB em comparação com a limitação do tamanho do upload de 30 GB enquanto utiliza o URL.

Em segundo lugar, considere apenas alguns dos problemas que podem afetar o seu desempenho e, portanto, a sua capacidade de escalar:

* Enviar ficheiros usando várias partes significa alta dependência da sua rede, 
* fiabilidade do serviço, 
* Conectividade 
* velocidade de carregamento, 
* pacotes perdidos em algum lugar da rede mundial.

:::image type="content" source="./media/considerations-when-use-at-scale/first-consideration.png" alt-text="Primeira consideração para a utilização do Indexante de Vídeo à escala":::

Ao fazer o upload de vídeos usando URL, basta fornecer um caminho para a localização de um ficheiro de mídia e o Video Indexer cuida do resto (veja o `videoUrl` campo no upload de [vídeo](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) API).

> [!TIP]
> Utilize o `videoUrl` parâmetro opcional do upload de vídeo API.

Para ver um exemplo de como fazer o upload de vídeos usando URL, confira [este exemplo](upload-index-videos.md#code-sample). Ou, você pode usar [a AzCopy](../../storage/common/storage-use-azcopy-v10.md) para uma forma rápida e fiável de obter o seu conteúdo para uma conta de armazenamento a partir da qual você pode submetê-lo para Índice de Vídeo usando [URL SAS](../../storage/common/storage-sas-overview.md).

## <a name="increase-media-reserved-units-if-needed"></a>Aumentar as unidades reservadas aos meios de comunicação, se necessário

Normalmente na prova de fase de conceito quando se começa a usar o Video Indexer, não precisa de muito poder de computação. Quando começa a ter um arquivo maior de vídeos que precisa de indexar e quer que o processo esteja a um ritmo que se ajuste ao seu caso de utilização, precisa de aumentar o seu uso do Video Indexer. Portanto, deve pensar em aumentar o número de recursos de computação que utiliza se a quantidade atual de poder de computação não for suficiente.

Na Azure Media Services, quando pretende aumentar o poder de computação e a paralelização, é necessário prestar atenção às [unidades reservadas](../latest/concept-media-reserved-units.md)aos meios de comunicação social (RUs). As RUs são as unidades de cálculo que determinam os parâmetros para as suas tarefas de processamento de mídia. O número de RUs afeta o número de tarefas mediáticas que podem ser processadas simultaneamente em cada conta e o seu tipo determina a velocidade de processamento e um vídeo pode exigir mais do que um RU se a sua indexação for complexa. Quando as suas RUs estiverem ocupadas, novas tarefas serão realizadas em fila até que outro recurso esteja disponível.

Para operar de forma eficiente e para evitar ter recursos que permanecem inativos parte do tempo, o Video Indexer oferece um sistema de escala automática que gira RUs para baixo quando é necessário menos processamento e gira RUs quando estiver em suas horas de ponta (até usar totalmente todos os seus RUs). Pode ativar esta funcionalidade [ligando o autoescalo](manage-account-connected-to-azure.md#autoscale-reserved-units) nas definições da conta ou utilizando a API de [Atualização-Conta-Azure-Media-Services](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Paid-Account-Azure-Media-Services).

:::image type="content" source="./media/considerations-when-use-at-scale/second-consideration.jpg" alt-text="Segunda consideração para a utilização do Indexador de Vídeo à escala":::

:::image type="content" source="./media/considerations-when-use-at-scale/reserved-units.jpg" alt-text="Unidades reservadas da AMS":::

Para minimizar a duração da indexação e a baixa produção que recomendamos, comece com 10 RUs do tipo S3. Mais tarde, se se aumentar para suportar mais conteúdo ou maior concordância, e precisar de mais recursos para o fazer, pode [contactar-nos através do sistema de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) (apenas em contas pagas) para pedir mais alocação de RUs.

## <a name="respect-throttling"></a>Respeito estrangulamento

O Video Indexer é construído para lidar com a indexação em escala, e quando você quer tirar o máximo proveito dele você também deve estar ciente das capacidades do sistema e projetar a sua integração em conformidade. Não pretende enviar um pedido de upload de um lote de vídeos apenas para descobrir que alguns dos filmes não foram carregados e está a receber um código de resposta HTTP 429 (demasiados pedidos). Pode acontecer devido ao facto de ter enviado mais pedidos do que o [limite de filmes por minuto que apoiamos.](upload-index-videos.md#uploading-considerations-and-limitations) O Indexante de Vídeo adiciona um `retry-after` cabeçalho na resposta HTTP, o cabeçalho especifica quando deve tentar a sua próxima nova tentativa. Certifique-se de respeitá-lo antes de tentar o seu próximo pedido.

:::image type="content" source="./media/considerations-when-use-at-scale/respect-throttling.jpg" alt-text="Desenhe bem a sua integração, respeite o estrangulamento":::

## <a name="use-callback-url"></a>Use URL de retorno

Recomendamos que, em vez de sondar constantemente o estado do seu pedido a partir do momento em que enviou o pedido de upload, possa adicionar um [URL de retorno](upload-index-videos.md#callbackurl)de chamadas e esperar que o Video Indexer o atualize. Assim que houver qualquer alteração de estado no seu pedido de upload, recebe uma notificação POST para o URL especificado.

Pode adicionar um URL de retorno como um dos parâmetros da [API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)de vídeo de upload . Confira as amostras de código no [gitHub repo.](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/) 

Para o URL de retorno, também pode utilizar as Funções Azure, uma plataforma orientada para eventos sem servidor que pode ser ativada por HTTP e implementar um fluxo seguinte.

### <a name="callback-url-definition"></a>definição de URL callBack

[!INCLUDE [callback url](./includes/callback-url.md)]

## <a name="use-the-right-indexing-parameters-for-you"></a>Use os parâmetros de indexação certos para si

Ao tomar decisões relacionadas com a utilização do Video Indexer em escala, veja como tirar o máximo partido dele com os parâmetros certos para as suas necessidades. Pense no seu caso de uso, definindo diferentes parâmetros pode economizar dinheiro e tornar o processo de indexação para os seus vídeos mais rápido.

Antes de carregar e indexar o seu vídeo leia esta [documentação](upload-index-videos.md)curta , verifique o [IndexingPreset](upload-index-videos.md#indexingpreset) e [streamingPreset](upload-index-videos.md#streamingpreset) para ter uma melhor ideia das suas opções.

Por exemplo, não defina a predefinição para o streaming se não planeia ver o vídeo, não indexe as introspeções de vídeo se necessitar apenas de insights áudio.

## <a name="index-in-optimal-resolution-not-highest-resolution"></a>Índice em resolução ideal, não resolução mais alta

Podes estar a perguntar, que qualidade de vídeo precisas para indexar os teus vídeos? 

Em muitos casos, o desempenho da indexação não tem quase nenhuma diferença entre vídeos HD (720P) e vídeos 4K. Eventualmente, terá quase as mesmas ideias com a mesma confiança. Quanto maior for a qualidade do filme que carregas significa que maior o tamanho do ficheiro, isto leva a uma maior potência de computação e tempo necessário para carregar o vídeo.

Por exemplo, para a funcionalidade de deteção facial, uma resolução mais alta pode ajudar com o cenário onde existem muitas caras pequenas mas contexicamente importantes. No entanto, isto virá com um aumento quadrático do tempo de execução e um risco acrescido de falsos positivos.

Por isso, recomendamos que verifique se obtém os resultados certos para o seu caso de utilização e que o teste primeiro localmente. Faça o upload do mesmo vídeo em 720P e em 4K e compare as ideias que obtém.

## <a name="next-steps"></a>Passos seguintes

[Examinar a produção do Azure Video Indexer produzido pela API](video-indexer-output-json-v2.md)