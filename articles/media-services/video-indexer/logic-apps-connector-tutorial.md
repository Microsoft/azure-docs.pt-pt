---
title: Os conectores do Indexer de Vídeo com App Lógica e Tutorial de Automatização de Energia.
description: Este tutorial mostra como desbloquear novas experiências e oportunidades de rentabilização de conectores de indexante de vídeo com a Logic App e Power Automamate.
author: anzaman
manager: johndeu
ms.author: alzam
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: f3504ca4a706e92081209f4eaaa86af9f71c52b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98880915"
---
# <a name="tutorial-use-video-indexer-with-logic-app-and-power-automate"></a>Tutorial: use Video Indexer com App lógica e automatização de energia

O Azure Media Services [Video Indexer v2 REST API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Delete-Video?) suporta a comunicação servidor-servidor e cliente-servidor e permite aos utilizadores do Video Indexer integrar facilmente insights de vídeo e áudio na sua lógica de aplicação, desbloqueando novas experiências e oportunidades de rentabilização.

Para facilitar ainda mais a integração, apoiamos [apps lógicas](https://azure.microsoft.com/services/logic-apps/)   e conectores [Power Automamate](https://preview.flow.microsoft.com/connectors/shared_videoindexer-v2/video-indexer-v2/)   compatíveis com a nossa API. Pode utilizar os conectores para configurar fluxos de trabalho personalizados para indexar e extrair informações de uma grande quantidade de ficheiros de vídeo e áudio, sem escrever uma única linha de código. Além disso, a utilização dos conectores para a sua integração dá-lhe uma melhor visibilidade sobre a saúde do seu fluxo de trabalho e uma forma fácil de depurá-lo.  

Para ajudá-lo a começar rapidamente com os conectores do Índice de Vídeo, faremos uma passagem por um exemplo de Aplicação lógica e solução Power Automate que pode configurar. Este tutorial mostra como configurar fluxos usando Aplicações Lógicas. No entanto, os editores e as capacidades são quase idênticos em ambas as soluções, pelo que os diagramas e explicações são aplicáveis tanto às Apps Lógicas como ao Power Automamate.

O cenário de "carregar e indexar automaticamente o seu vídeo" coberto neste tutorial é composto por dois fluxos diferentes que funcionam em conjunto. 
* O primeiro fluxo é acionado quando uma bolha é adicionada ou modificada numa conta de Armazenamento Azure. Ele envia o novo ficheiro para o Video Indexer com um URL de retorno para enviar uma notificação uma vez que a operação de indexação esteja concluída. 
* O segundo fluxo é acionado com base no URL de retorno e guarda as informações extraídas de volta para um ficheiro JSON no Azure Storage. Esta abordagem de fluxo de dois é usada para suportar o upload e indexação de ficheiros maiores de forma eficaz. 

Este tutorial está a usar a App Lógica para mostrar como:

> [!div class="checklist"]
> * Configurar o fluxo de upload de ficheiros
> * Configurar o fluxo de extração JSON

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Para começar, necessitará de uma conta de Indexer de Vídeo, juntamente com [o acesso às APIs através da chave API](video-indexer-use-apis.md). 
* Também vai precisar de uma conta de Armazenamento Azure. Tenha em atenção a chave de acesso para a sua conta de Armazenamento. Crie dois recipientes – um para armazenar vídeos e outro para armazenar insights gerados pelo Video Indexer.  
* Em seguida, terá de abrir dois fluxos separados em Apps Lógicas ou Power Automamate (dependendo do que estiver a utilizar). 

## <a name="set-up-the-first-flow---file-upload"></a>Configurar o primeiro fluxo - upload de ficheiros   

O primeiro fluxo é acionado sempre que uma bolha é adicionada no seu recipiente de armazenamento Azure. Uma vez acionado, criará um SAS URI que pode utilizar para carregar e indexar o vídeo no Índice de Vídeo. Nesta secção irá criar o seguinte fluxo. 

![Fluxo de upload de ficheiros](./media/logic-apps-connector-tutorial/file-upload-flow.png)

Para configurar o primeiro fluxo, terá de fornecer as suas credenciais de API indexante de vídeo e credenciais de armazenamento Azure. 

![Armazenamento de blobs do Azure](./media/logic-apps-connector-tutorial/azure-blob-storage.png)

![Nome da ligação e a chave de API](./media/logic-apps-connector-tutorial/connection-name-api-key.png)

> [!TIP]
> Se anteriormente ligou uma conta Azure Storage ou Video Indexer a uma App Lógica, os seus dados de ligação são armazenados e será ligado automaticamente. <br/>Pode editar a ligação clicando na **ligação Change** na parte inferior de uma ação de Armazenamento Azure (a janela de armazenamento) ou indexante de vídeo (a janela do leitor).

Assim que conseguir ligar-se às suas contas Azure Storage e Video Indexer, encontre e escolha o gatilho "Quando uma bolha é adicionada ou modificada" no **Logic Apps Designer**.

Selecione o recipiente onde irá colocar os seus ficheiros de vídeo. 

![A screenshot mostra o Quando uma bolha é adicionada ou uma caixa de diálogo modificada onde pode selecionar um recipiente.](./media/logic-apps-connector-tutorial/container.png)

Em seguida, encontre e selecione a ação "Create SAS URI by path". No diálogo para a ação, selecione List of Files Path a partir das opções de conteúdo Dinâmico.  

Além disso, adicione um novo parâmetro "Protocolo de Acesso Partilhado". Escolha HttpsOnly pelo valor do parâmetro.

![SAS uri por caminho](./media/logic-apps-connector-tutorial/sas-uri-by-path.jpg)

Preencha [a localização da sua conta](regions.md) e o [ID da conta](./video-indexer-use-apis.md#account-id)para obter o   token da conta do Indexer de Vídeo.

![Obter ficha de acesso à conta](./media/logic-apps-connector-tutorial/account-access-token.png)

Para "Carregar vídeo e índice", preencha os parâmetros necessários e URL de vídeo. Selecione "Adicionar novo parâmetro" e selecione URL de retorno. 

![Upload e índice](./media/logic-apps-connector-tutorial/upload-and-index.png)

Por enquanto, deixará a URL de retorno. Só o adicionará depois de terminar o segundo fluxo onde o URL de retorno é criado. 

Pode utilizar o valor predefinido para os outros parâmetros ou defini-los de acordo com as suas necessidades. 

Clique em **Guardar**, e vamos passar a configurar o segundo fluxo, para extrair os insights uma vez que o upload e a indexação esteja concluído. 

## <a name="set-up-the-second-flow---json-extraction"></a>Configurar o segundo fluxo - extração JSON  

A conclusão do upload e indexação a partir do primeiro fluxo enviará um pedido HTTP com o URL de retorno correto para desencadear o segundo fluxo. Em seguida, recuperará as informações geradas pelo Video Indexer. Neste exemplo, irá armazenar a produção do seu trabalho de indexação no seu Azure Storage.  No entanto, cabe-lhe a si o que pode fazer com a saída.  

Crie o segundo fluxo separado do primeiro. 

![Fluxo de extração JSON](./media/logic-apps-connector-tutorial/json-extraction-flow.png)

Para configurar este fluxo, terá de fornecer novamente as suas credenciais de API indexante de vídeo e armazenamento Azure. Terá de atualizar os mesmos parâmetros que fez para o primeiro fluxo. 

Para o seu gatilho, verá um campo URL HTTP POST. O URL só será gerado depois de poupar o seu fluxo; no entanto, eventualmente vai precisar do URL. Voltaremos a isto. 

Preencha [a localização da sua conta](regions.md) e o [ID da conta](./video-indexer-use-apis.md#account-id)para obter o   token da conta do Indexer de Vídeo.  

Vá à ação "Obter Índice de Vídeo" e preencha os parâmetros necessários. Para o ID de vídeo, coloque a seguinte expressão: triggerOutputs()['consultas']['id'] 

![informação de ação do indexante de vídeo](./media/logic-apps-connector-tutorial/video-indexer-action-info.jpg)

Esta expressão diz ao conector para obter o ID de vídeo a partir da saída do seu gatilho. Neste caso, a saída do seu gatilho será a saída de "Upload video and index" no seu primeiro gatilho. 

Vá à ação "Criar bolha" e selecione o caminho para a pasta na qual irá guardar as introspeções. Descreva o nome da bolha que está a criar. Para o conteúdo blob, coloque na seguinte expressão: corpo ('Get_Video_Index') 

![Criar ação blob](./media/logic-apps-connector-tutorial/create-blob-action.jpg)

Esta expressão retira a saída da ação "Obter Índice de Vídeo" deste fluxo. 

Clique **em Guardar o fluxo**. 

Uma vez que o fluxo é guardado, um URL HTTP POST é criado no gatilho. Copie o URL do gatilho. 

![Salvar o gatilho URL](./media/logic-apps-connector-tutorial/save-url-trigger.png)

Agora, volte ao primeiro fluxo e cole o URL na ação "Upload video and index" para o parâmetro URL callback. 

Certifique-se de que ambos os fluxos estão guardados, e está pronto para ir! 

Experimente a sua recém-criada solução Logic App ou Power Automamate adicionando um vídeo ao seu recipiente de blobs Azure e volte alguns minutos depois para ver se as informações aparecem na pasta de destino. 

## <a name="generate-captions"></a>Gerar legendas

Consulte o blog seguinte para os passos que mostram [como gerar legendas com Video Indexer e Logic Apps](https://techcommunity.microsoft.com/t5/azure-media-services/generating-captions-with-video-indexer-and-logic-apps/ba-p/1672198). 

O artigo também mostra como indexar um vídeo automaticamente copiando-o para o OneDrive e como armazenar as legendas geradas pelo Video Indexer no OneDrive.
 
## <a name="clean-up-resources"></a>Limpar os recursos

Depois de terminar este tutorial, sinta-se à vontade para manter esta solução Logic App ou Power Automamate em funcionamento se precisar. No entanto, se não quiser manter isto em funcionamento e não quiser ser cobrado, desligue ambos os fluxos se estiver a utilizar o Power Automamate. Desative ambos os fluxos se estiver a utilizar as Aplicações Lógicas. 

## <a name="next-steps"></a>Passos seguintes

Este tutorial mostrou apenas um exemplo de conectores de indexante de vídeo. Pode utilizar os conectores do Indexador de Vídeo para qualquer chamada de API fornecida pelo Video Indexer. Por exemplo: carregar e recuperar insights, traduzir os resultados, obter widgets incorporados e até personalizar os seus modelos. Além disso, pode optar por desencadear essas ações com base em diferentes fontes, como atualizações para repositórios de ficheiros ou e-mails enviados. Em seguida, pode optar por ter a atualização de resultados para a nossa infraestrutura ou aplicação relevante ou gerar qualquer número de itens de ação.  

> [!div class="nextstepaction"]
> [Utilizar a API do Video Indexer](video-indexer-use-apis.md)

Para obter recursos adicionais, consulte este documento no [indexador de vídeo.](/connectors/videoindexer-v2/)