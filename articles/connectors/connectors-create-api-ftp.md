---
title: Ligar ao servidor FTP - Azure Logic Apps
description: Criar, monitorizar e gerir os ficheiros num servidor FTP no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 10/15/2018
tags: connectors
ms.openlocfilehash: e5aeaa707c7a839483484c524e982204d6fe055c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60408601"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Criar, monitorizar e gerir ficheiros de FTP ao utilizar o Azure Logic Apps

Com o Azure Logic Apps e o conector FTP, pode criar tarefas automatizadas e fluxos de trabalho que criarem, monitorizarem, enviarem e recebem ficheiros através da sua conta num servidor FTP, juntamente com outras ações, por exemplo:

* Monitor de quando os ficheiros são adicionados ou alterados.
* Obter, criar, copiar, atualizar, lista e eliminar ficheiros.
* Obter conteúdo do ficheiro e metadados.
* Extraia os arquivos para pastas.

Pode usar acionadores que obtém respostas a partir do seu servidor FTP e disponibilizar a saída para outras ações. Pode utilizar ações de execução nas suas aplicações lógicas para gerir ficheiros no seu servidor FTP. Pode também ter outras ações utilizar a saída de ações de FTP. Por exemplo, se regularmente obter ficheiros do seu servidor FTP, pode enviar por e-mail sobre esses arquivos e seu conteúdo com o conector do Outlook do Office 365 ou o conector do Outlook.com. Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Limits

* Ações de FTP suportam apenas os ficheiros que estão *50 MB ou mais pequeno* a menos que utilize [mensagem segmentação](../logic-apps/logic-apps-handle-large-messages.md), que permitem-lhe exceder este limite. Atualmente, os acionadores FTP não suportam a segmentação.

* O conector FTP suporta apenas explícita FTP sobre SSL (FTPS) e não é compatível com FTPS implícita.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>. 

* Os anfitrião servidor endereço e a conta as credenciais de FTP

  O conector FTP requer que o servidor de FTP está acessível a partir da internet e de instalação operar no *passivo* modo. As suas credenciais permitem que a sua aplicação lógica, crie uma ligação e aceder à sua conta FTP.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação de lógica onde pretende aceder à sua conta FTP. Para começar com um acionador FTP [criar uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar uma ação de FTP, inicie a aplicação lógica com outro acionador, por exemplo, o **periodicidade** acionador.

## <a name="connect-to-ftp"></a>Ligar ao FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e abra a aplicação lógica no Estruturador da aplicação lógica, se não estiver já abrir.

1. Para aplicações de lógica em branco, na caixa de pesquisa, introduza "ftp" como o filtro. Abaixo da lista de disparadores, selecione o acionador que pretende.

   -ou-

   Para logic apps existentes, sob a última etapa em que pretende adicionar uma ação, escolha **novo passo**e, em seguida, selecione **adicionar uma ação**. 
   Na caixa de pesquisa, introduza "ftp" como o filtro. 
   Abaixo da lista de ações, selecione a ação que pretende.

   Para adicionar uma ação entre passos, mova o ponteiro do mouse sobre a seta entre passos. 
   Selecione o sinal de adição ( **+** ) que é apresentada e, em seguida, selecione **adicionar uma ação**.

1. Forneça os detalhes necessários para a sua ligação e, em seguida, escolha **criar**.

1. Forneça os detalhes necessários para o seu acionador selecionado ou a ação e continuar a criar o fluxo de trabalho da sua aplicação lógica.

Quando solicitar o conteúdo do ficheiro, o acionador não obter ficheiros superior a 50 MB. Para obter os ficheiros mais de 50 MB, siga este padrão:

* Utilizar um acionador que retorna as propriedades do ficheiro, tal como **quando um ficheiro é adicionado ou modificado (propriedades apenas)** .

* Siga o acionador com uma ação que lê o arquivo completo, como **obter conteúdo do ficheiro através do caminho**, e ter a ação utilizar [mensagem segmentação](../logic-apps/logic-apps-handle-large-messages.md).

## <a name="examples"></a>Exemplos

<a name="file-added-modified"></a>

### <a name="ftp-trigger-when-a-file-is-added-or-modified"></a>Acionador FTP: Quando um ficheiro é adicionado ou modificado

Este acionador é iniciado um fluxo de trabalho de aplicação lógica quando o acionador detetar quando um ficheiro é adicionado ou alterado num servidor FTP. Por exemplo, que pode adicionar uma condição que verifica o conteúdo do arquivo e decide se obter esse conteúdo, com base em se esse conteúdo cumpre uma condição especificada. Por fim, pode adicionar uma ação que obtém o conteúdo do arquivo e colocar esse conteúdo numa pasta no servidor SFTP. 

**Exemplo de Enterprise**: Pode utilizar este acionador para monitorizar uma pasta FTP para novos ficheiros que descrevem as encomendas de cliente. Em seguida, pode utilizar como uma ação de FTP **obter conteúdo do ficheiro**, para que possa obter conteúdo do pedido para processamento adicional e armazenar essa ordem numa base de dados de encomendas.

Quando solicitar o conteúdo do ficheiro, os acionadores não é possível obter ficheiros superior a 50 MB. Para obter os ficheiros mais de 50 MB, siga este padrão: 

* Utilizar um acionador que retorna as propriedades do ficheiro, tal como **quando um ficheiro é adicionado ou modificado (propriedades apenas)** .

* Siga o acionador com uma ação que lê o arquivo completo, como **obter conteúdo do ficheiro através do caminho**, e ter a ação utilizar [mensagem segmentação](../logic-apps/logic-apps-handle-large-messages.md).

Uma aplicação lógica válida e funcional exige um acionador e pelo menos uma ação. Por isso, certifique-se de que adicionar uma ação depois de adicionar um acionador.

Eis um exemplo que mostra este acionador: **Quando um ficheiro é adicionado ou modificado**

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e abra a aplicação lógica no Estruturador da aplicação lógica, se não estiver já abrir.

1. Para aplicações de lógica em branco, na caixa de pesquisa, introduza "ftp" como o filtro. Abaixo da lista de disparadores, selecione este acionador: **Quando um arquivado é adicionado ou modificado - FTP**

   ![Localize e selecione o acionador FTP](./media/connectors-create-api-ftp/select-ftp-trigger.png)  

1. Forneça os detalhes necessários para a sua ligação e, em seguida, escolha **criar**.

   Por predefinição, este conector transfere ficheiros num formato de texto. 
   Transferência de ficheiros em binário formatar, por exemplo, onde e quando o tipo de codificação, selecione **transporte binário**.

   ![Criar a ligação ao servidor FTP](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)  

1. Junto a **pasta** caixa, escolha o ícone de pasta, portanto, é apresentada uma lista. Para localizar a pasta que pretende monitorizar ficheiros novos ou editados, selecione a seta de ângulo direito ( **>** ), navegue para essa pasta e, em seguida, selecione a pasta.

   ![Localize e selecione a pasta a monitorizar](./media/connectors-create-api-ftp/select-folder.png)  

   Sua pasta selecionada é apresentada no **pasta** caixa.

   ![Pasta selecionada](./media/connectors-create-api-ftp/selected-folder.png)  

Agora que a aplicação lógica tem um acionador, adicione as ações que pretende executar quando a aplicação lógica encontrar um ficheiro novo ou editado. Neste exemplo, pode adicionar uma ação de FTP que obtém o conteúdo de novo ou atualizado.

<a name="get-content"></a>

### <a name="ftp-action-get-content"></a>Ação de FTP: Obter o conteúdo

Esta ação obtém o conteúdo de um arquivo num servidor FTP quando esse ficheiro é adicionado ou atualizado. Por exemplo, pode adicionar o acionador do exemplo anterior e uma ação que obtém o conteúdo do ficheiro após esse ficheiro é adicionado ou editado. 

Quando solicitar o conteúdo do ficheiro, os acionadores não é possível obter ficheiros superior a 50 MB. Para obter os ficheiros mais de 50 MB, siga este padrão: 

* Utilizar um acionador que retorna as propriedades do ficheiro, tal como **quando um ficheiro é adicionado ou modificado (propriedades apenas)** .

* Siga o acionador com uma ação que lê o arquivo completo, como **obter conteúdo do ficheiro através do caminho**, e ter a ação utilizar [mensagem segmentação](../logic-apps/logic-apps-handle-large-messages.md).

Eis um exemplo que mostra esta ação: **Obter o conteúdo**

1. Sob o acionador ou outras ações, escolha **novo passo**. 

1. Na caixa de pesquisa, introduza "ftp" como o filtro. Abaixo da lista de ações, selecione a ação: **Obter conteúdo do ficheiro - FTP**

   ![Selecione a ação de FTP](./media/connectors-create-api-ftp/select-ftp-action.png)  

1. Se já tiver uma ligação para o servidor FTP e a conta, vá para o passo seguinte. Caso contrário, forneça os detalhes necessários para essa ligação e, em seguida, escolha **criar**. 

   ![Criar a ligação ao servidor FTP](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Depois do **obter conteúdo do ficheiro** abre-se de ação, clique no interior do **ficheiro** caixa para que a lista de conteúdo dinâmico apareça. Agora, pode selecionar propriedades para as saídas dos passos anteriores. Na lista de conteúdo dinâmico, selecione o **conteúdo do ficheiro** propriedade, que tem o conteúdo para o ficheiro foi adicionado ou atualizado.  

   ![Localize e selecione o ficheiro](./media/connectors-create-api-ftp/ftp-action-get-file-content.png)

   O **conteúdo do ficheiro** propriedade aparece a **ficheiro** caixa.

   ![Propriedade de "Conteúdo do ficheiro" selecionada](./media/connectors-create-api-ftp/ftp-action-selected-file-content-property.png)

1. Guarde a aplicação lógica. Para testar o fluxo de trabalho, adicione um ficheiro para a pasta FTP que monitoriza a sua aplicação lógica agora.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre os limites, ações e acionadores, que é descrito através OpenAPI do conector (anteriormente Swagger) descrição, reveja os [página de referência do conector](/connectors/ftpconnector/).

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)
