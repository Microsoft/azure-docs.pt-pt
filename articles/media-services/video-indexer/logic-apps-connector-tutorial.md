---
title: Os conectores do Indexer de Vídeo com A Aplicação Lógica e tutorial power automate.
description: Este tutorial mostra como desbloquear novas experiências e oportunidades de rentabilização Conectores de Indexer de vídeo com Logic App e Power Automate.
author: anikaz
manager: johndeu
ms.author: anzaman
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 05/01/2020
ms.openlocfilehash: 932f52aa694c13fd3696d82872135304a4e41bdc
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801132"
---
# <a name="tutorial-use-video-indexer-with-logic-app-and-power-automate"></a>Tutorial: use Indexer de vídeo com App Lógica e Power Automate

O Azure Media Services [Video Indexer v2 REST API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Delete-Video?) suporta a comunicação servidor-a-servidor e cliente-a-servidor e permite que os utilizadores do Video Indexer integrem facilmente as informações de vídeo e áudio na sua lógica de aplicação, desbloqueando novas experiências e oportunidades de rentabilização.

Para tornar a integração ainda mais fácil, apoiamos [aplicações](https://azure.microsoft.com/services/logic-apps/) lógicas e conectores [Power Automate](https://preview.flow.microsoft.com/connectors/shared_videoindexer-v2/video-indexer-v2/) que são compatíveis com a nossa API. Pode utilizar os conectores para configurar fluxos de trabalho personalizados para indexar e extrair informações de uma grande quantidade de ficheiros de vídeo e áudio, sem escrever uma única linha de código. Além disso, usar os conectores para a sua integração dá-lhe uma melhor visibilidade sobre a saúde do seu fluxo de trabalho e uma maneira fácil de desincomodá-lo.  

Para ajudá-lo a começar rapidamente com os conectores do Indexer de Vídeo, faremos uma passagem por um exemplo de App Lógica e solução Power Automate que você pode configurar. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Faça upload e indexe o seu vídeo automaticamente
> * Configurar o fluxo de upload de ficheiros
> * Configurar o fluxo de extração JSON

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para começar, também necessitará de uma conta de Indexer de Vídeo, juntamente com o acesso às APIs através da chave API. 

Também vai precisar de uma conta de Armazenamento Azure. Mantenha nota da chave de acesso da sua conta de Armazenamento. Crie dois recipientes – um para armazenar vídeos e outro para armazenar insights gerados pelo Video Indexer.  

Em seguida, terá de abrir dois fluxos separados em aplicações lógicas ou power automate (dependendo dos quais está a usar).  

## <a name="upload-and-index-your-video-automatically"></a>Faça upload e indexe o seu vídeo automaticamente 

Este cenário é composto por dois fluxos diferentes que funcionam em conjunto. O primeiro fluxo é acionado quando uma bolha é adicionada ou modificada numa conta de Armazenamento Azure. Envia o novo ficheiro para o Video Indexer com um URL de callback para enviar uma notificação assim que a operação de indexação estiver concluída. O segundo fluxo é acionado com base no URL de chamada e guarda as informações extraídas de volta para um ficheiro JSON no Armazenamento Azure. Esta abordagem de dois fluxos é usada para suportar o upload e indexação de ficheiros de maiores dimensões de forma eficaz. 

### <a name="set-up-the-file-upload-flow"></a>Configurar o fluxo de upload de ficheiros 

O primeiro fluxo é acionado sempre que uma bolha é adicionada no seu recipiente de armazenamento Azure. Uma vez acionado, criará um SAS URI que pode utilizar para carregar e indexar o vídeo em Video Indexer. Comece por criar o seguinte fluxo. 

![Fluxo de upload de ficheiros](./media/logic-apps-connector-tutorial/file-upload-flow.png)

Para configurar o primeiro fluxo, terá de fornecer as suas credenciais de Chave API do Indexante de Vídeo e de Armazenamento Azure. 

![Armazenamento de blobs do Azure](./media/logic-apps-connector-tutorial/azure-blob-storage.png)

![Nome da ligação e a chave de API](./media/logic-apps-connector-tutorial/connection-name-api-key.png)

Assim que conseguir ligar-se às suas contas Azure Storage e Video Indexer, vá ao gatilho "Quando uma bolha for adicionada ou modificada) e selecione o recipiente onde colocará os seus ficheiros de vídeo. 

![Contentor](./media/logic-apps-connector-tutorial/container.png)

Em seguida, vá à ação "Create SAS URI por caminho" e selecione Lista de Ficheiros Caminho das opções de conteúdo Dinâmico.  

![SAS uri por caminho](./media/logic-apps-connector-tutorial/sas-uri-by-path.jpg)

Preencha [a sua conta Localização e ID](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-use-apis#location) para obter o token da conta Do Indexer de Vídeo.

![Obtenha ficha de acesso à conta](./media/logic-apps-connector-tutorial/account-access-token.png)

Para "Carregar vídeo e índice", preencha os parâmetros necessários e url de vídeo. Selecione "Adicionar novo parâmetro" e selecione URL de callback. 

![Upload e índice](./media/logic-apps-connector-tutorial/upload-and-index.png)

Por enquanto, deixará a URL de chamada vazia. Só o adicionará depois de terminar o segundo fluxo onde o URL de callback é criado. 

Pode utilizar o valor predefinido para os outros parâmetros ou defini-los de acordo com as suas necessidades. 

Clique em "Guardar", e vamos avançar para configurar o segundo fluxo, para extrair os insights assim que o upload e indexação estiverem concluídos. 

## <a name="set-up-the-json-extraction-flow"></a>Configurar o fluxo de extração JSON 

A conclusão do upload e indexação a partir do primeiro fluxo enviará um pedido HTTP com o URL de chamada correto para desencadear o segundo fluxo. Em seguida, irá recuperar os insights gerados pelo Indexer de Vídeo. Neste exemplo, irá armazenar a saída do seu trabalho de indexação no seu Armazenamento Azure.  No entanto, cabe-lhe a si o que pode fazer com a saída.  

Crie o segundo fluxo separado do primeiro. 

![Fluxo de extração JSON](./media/logic-apps-connector-tutorial/json-extraction-flow.png)

Para configurar este fluxo, terá de fornecer novamente as suas credenciais de Chave API do Indexer de Vídeo e do Armazenamento Azure. Terá de atualizar os mesmos parâmetros que fez para o primeiro fluxo. 

Para o seu gatilho, verá um campo DE URL HTTP POST. O URL só será gerado depois de poupar o seu fluxo; no entanto, eventualmente vai precisar do URL. Voltaremos a isto. 

Preencha [a sua conta Localização e ID](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-use-apis#location) para obter o token da conta Do Indexer de Vídeo.  

Vá à ação "Get Video Index" e preencha os parâmetros necessários. Para id vídeo, coloque na seguinte expressão: triggerOutputs[)['consultas'['id'] 

![informação de ação do indexante de vídeo](./media/logic-apps-connector-tutorial/video-indexer-action-info.jpg)

Esta expressão diz ao conector para obter o ID de vídeo a partir da saída do seu gatilho. Neste caso, a saída do gatilho será a saída de "Upload vídeo e índice" no seu primeiro gatilho. 

Vá à ação "Criar blob" e selecione o caminho para a pasta em que irá guardar as informações. Desloque o nome da bolha que está a criar. Para o conteúdo blob, coloque na seguinte expressão: corpo ('Get_Video_Index') 

![Criar ação blob](./media/logic-apps-connector-tutorial/create-blob-action.jpg)

Esta expressão retira a saída da ação "Get Video Index" deste fluxo. 

Clique em "Salvar o fluxo". 

Uma vez que o fluxo é guardado, um URL POST HTTP é criado no gatilho. Copie o URL do gatilho. 

![Salvar o gatilho url](./media/logic-apps-connector-tutorial/save-url-trigger.png)

Agora, volte ao primeiro fluxo e colhe o URL na ação "Upload vídeo e index" para o parâmetro URL callback. 

Certifique-se de que ambos os fluxos estão salvos, e está pronto para ir! 

Experimente a sua recém-criada App Lógica ou solução Power Automate adicionando um vídeo ao seu recipiente de blobs Azure, e volte alguns minutos depois para ver se as informações aparecem na pasta de destino. 

## <a name="clean-up-resources"></a>Limpar recursos

Depois de terminar com este tutorial, sinta-se à vontade para manter esta App Lógica ou a solução Power Automate em funcionamento se precisar. No entanto, se não quiser manter isto em funcionamento e não quiser ser cobrado, desligue os dois fluxos se estiver a utilizar power automate. Desative ambos os fluxos se estiver a usar aplicações lógicas. 

## <a name="next-steps"></a>Passos seguintes

Este tutorial mostrou apenas um exemplo de conectores de Indexer de Vídeo. Pode utilizar os conectores do Indexer de Vídeo para qualquer chamada API fornecida pelo Indexer de Vídeo. Por exemplo: fazer upload e recuperar insights, traduzir os resultados, obter widgets incorporados e até personalizar os seus modelos. Além disso, pode optar por desencadear essas ações com base em diferentes fontes, como atualizações para ficheiros de repositórios ou e-mails enviados. Pode então optar por ter a atualização de resultados da nossa infraestrutura ou aplicação relevantes ou gerar qualquer número de itens de ação.  

> [!div class="nextstepaction"]
> [Utilizar a API do Video Indexer](video-indexer-use-apis.md)
