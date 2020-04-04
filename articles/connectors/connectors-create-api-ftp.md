---
title: Ligar ao servidor FTP
description: Automatizar tarefas e fluxos de trabalho que criam, monitorizam e gerem ficheiros num servidor FTP utilizando aplicações lógicas do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 12/15/2019
tags: connectors
ms.openlocfilehash: 5b61b51e79c71736e18aaa63ab032c05c512c8d7
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656339"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Criar, monitorizar e gerir ficheiros FTP utilizando aplicações lógicas do Azure

Com as Aplicações Lógicas Azure e o conector FTP, pode criar tarefas automatizadas e fluxos de trabalho que criam, monitorizam, enviam e recebem ficheiros através da sua conta num servidor FTP, juntamente com outras ações, por exemplo:

* Monitorize quando os ficheiros forem adicionados ou alterados.
* Obtenha, crie, copie, atualize, e apague ficheiros.
* Obtenha conteúdo de ficheiros e metadados.
* Extrair arquivos para pastas.

Pode utilizar gatilhos que obtêm respostas do seu servidor FTP e disponibilizar a saída a outras ações. Pode utilizar ações de execução nas suas aplicações lógicas para gerir ficheiros no seu servidor FTP. Também pode ter outras ações a utilizar a saída a partir de ações ftp. Por exemplo, se obtém regularmente ficheiros do seu servidor FTP, pode enviar e-mail sobre esses ficheiros e o seu conteúdo utilizando o conector do Office 365 Outlook ou Outlook.com conector. Se você é novo em aplicativos lógicos, reveja [o que é Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="limitations"></a>Limitações

* O conector FTP suporta apenas FTP explícito sobre TLS/SSL (FTPS) e não é compatível com FTPS implícito.

* Por predefinição, as ações ftp podem ler ou escrever ficheiros com *50 MB ou menores*. Para lidar com ficheiros superiores a 50 MB, as ações FTP suportam [a manipulação da mensagem](../logic-apps/logic-apps-handle-large-messages.md). A ação de conteúdo do **ficheiro Get** utiliza implicitamente a chunking.

* Os gatilhos ftp não suportam o chunking. Ao solicitar o conteúdo do ficheiro, os gatilhos selecionam apenas ficheiros com 50 MB ou menores. Para obter ficheiros superiores a 50 MB, siga este padrão:

  * Utilize um gatilho FTP que dedreta propriedades de ficheiros, como **quando um ficheiro é adicionado ou modificado (apenas propriedades)**.

  * Siga o gatilho com a ação de conteúdo de ficheiro FTP **Get,** que lê o ficheiro completo e utiliza implicitamente a chunking.

* Se tiver um servidor FTP no local, considere criar um ambiente de serviço de [integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) ou utilizar [ligações Azure App Service Hybrid,](../app-service/app-service-hybrid-connections.md)que tanto lhe permitem aceder a fontes de dados no local sem utilizar um portal de dados no local.

## <a name="how-ftp-triggers-work"></a>Como o FTP dispara

O FTP desencadeia o trabalho através da sondagem do sistema de ficheiros FTP e à procura de qualquer ficheiro que tenha sido alterado desde a última sondagem. Algumas ferramentas permitem-lhe preservar a marca de tempo quando os ficheiros mudam. Nestes casos, tem de desativar esta funcionalidade para que o gatilho possa funcionar. Aqui estão algumas configurações comuns:

| Cliente SFTP | Ação |
|-------------|--------|
| Estação Winscp | Ir para **opções** > **Preferências** > **Transferência** > Editar**Editar** > Carimbo de**Tempo** > **Desativar** |
| FileZilla | Vá para **transferir** > **preservar selos de tempo dos ficheiros transferidos** > **Desativar** |
|||

Quando um gatilho encontra um novo ficheiro, o gatilho verifica se o novo ficheiro está completo e não parcialmente escrito. Por exemplo, um ficheiro pode ter alterações em andamento quando o gatilho verificar o servidor de ficheiros. Para evitar a devolução de um ficheiro parcialmente escrito, o gatilho assinala a marca ção do ficheiro que tem alterações recentes, mas não devolve imediatamente esse ficheiro. O gatilho devolve o ficheiro apenas quando voltar a votar no servidor. Às vezes, este comportamento pode causar um atraso que é até o dobro do intervalo de votação do gatilho.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* O seu endereço de servidor de anfitriões FTP e credenciais de conta

  O conector FTP requer que o seu servidor FTP esteja acessível a partir da internet e configurado para funcionar em modo *passivo.* As suas credenciais permitem que a sua aplicação lógica crie uma ligação e aceda à sua conta FTP.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder à sua conta FTP. Para começar com um gatilho FTP, [crie uma aplicação lógica em branco.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Para utilizar uma ação FTP, inicie a sua aplicação lógica com outro gatilho, por exemplo, o gatilho **recorrência.**

## <a name="connect-to-ftp"></a>Ligar ao FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer.

1. Para aplicações lógicas em branco, na caixa de pesquisa, introduza `ftp` como filtro. A partir da lista **de Gatilhos,** selecione o gatilho que deseja.

   -ou-

   Para aplicações lógicas existentes, sob o último passo onde pretende adicionar uma ação, selecione **Novo passo,** e depois selecione **Adicionar uma ação**. Na caixa de `ftp` pesquisa, introduza como filtro. Na lista **de Ações,** selecione a ação que deseja.

   Para adicionar uma ação entre passos, mova o ponteiro sobre a seta entre os degraus. Selecione o**+** sinal de mais ( ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Forneça as suas informações de ligação e selecione **Criar**.

1. Forneça as informações para o seu gatilho ou ação selecionados e continue a construir o fluxo de trabalho da sua aplicação lógica.

## <a name="examples"></a>Exemplos

<a name="file-added-modified"></a>

### <a name="add-ftp-trigger"></a>Adicionar gatilho FTP

O gatilho **Quando um ficheiro é adicionado ou modificado (apenas propriedades)** inicia um fluxo de trabalho de aplicação lógica quando o gatilho deteta que um ficheiro é adicionado ou alterado num servidor FTP. Por exemplo, pode adicionar uma condição que verifica o conteúdo do ficheiro e decide se obtém esse conteúdo, com base no facto de esse conteúdo cumprir uma condição especificada. Finalmente, pode adicionar uma ação que obtenha o conteúdo do ficheiro e colocar esse conteúdo numa pasta diferente no servidor SFTP.

Por exemplo, pode utilizar este gatilho para monitorizar uma pasta FTP para novos ficheiros que descrevem as encomendas dos clientes. Em seguida, pode utilizar uma ação FTP, como **obter metadados** de ficheiros para obter as propriedades para esse novo ficheiro, e depois utilizar o conteúdo do **ficheiro Get** para obter o conteúdo desse ficheiro para posterior processamento e armazenar essa encomenda numa base de dados de encomendas.

Aqui está um exemplo que mostra como usar o gatilho **Quando um ficheiro é adicionado ou modificado (apenas propriedades).**

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se ainda não estiver aberta.

1. Para aplicações lógicas em branco, na caixa de pesquisa, introduza `ftp` como filtro. Na lista de gatilhos, selecione este gatilho: **Quando um arquivado é adicionado ou modificado (apenas propriedades)**

   ![Encontre e selecione o gatilho FTP](./media/connectors-create-api-ftp/select-ftp-trigger-logic-app.png)

1. Forneça os detalhes necessários para a sua ligação e, em seguida, selecione **Criar**.

   Por predefinição, este conector transfere ficheiros em formato de texto. Para transferir ficheiros em formato binário, por exemplo, onde e quando for utilizada codificação, **selecione Binary Transport**.

   ![Criar ligação ao servidor FTP](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)

1. Na caixa **pasta,** selecione o ícone da pasta para que apareça uma lista. Para encontrar a pasta que pretende monitorizar para ficheiros novos**>** ou editados, selecione a seta angular direita (), navegue para essa pasta e, em seguida, selecione a pasta.

   ![Localizar e selecionar pasta para monitorizar](./media/connectors-create-api-ftp/select-folder-ftp-trigger.png)

   A pasta selecionada aparece na caixa de **pastas.**

   ![Pasta selecionada aparece na propriedade "Pasta"](./media/connectors-create-api-ftp/selected-folder-ftp-trigger.png)

1. Guarde a aplicação lógica. Na barra de ferramentas de design, selecione **Guardar**.

Agora que a sua aplicação lógica tem um gatilho, adicione as ações que pretende executar quando a sua aplicação lógica encontrar um ficheiro novo ou editado. Para este exemplo, pode adicionar uma ação FTP que obtém o conteúdo novo ou atualizado.

<a name="get-content"></a>

### <a name="add-ftp-action"></a>Adicionar ação FTP

A ação de **metadados** do ficheiro Get obtém as propriedades de um ficheiro que está no seu servidor FTP e a ação de conteúdo do **ficheiro Get** obtém o conteúdo do ficheiro com base nas informações sobre esse ficheiro no seu servidor FTP. Por exemplo, pode adicionar o gatilho do exemplo anterior e estas ações para obter o conteúdo do ficheiro após a adição ou edição desse ficheiro.

1. Sob o gatilho ou quaisquer outras ações, selecione **Novo passo**.

1. Na caixa de `ftp` pesquisa, introduza como filtro. Na lista de ações, selecione esta ação: **Obtenha metadados de ficheiros**

   ![Selecione a ação "Obter metadados de ficheiros"](./media/connectors-create-api-ftp/select-get-file-metadata-ftp-action.png)

1. Se já tem uma ligação ao seu servidor ftp e conta, vá para o próximo passo. Caso contrário, forneça os detalhes necessários para essa ligação e, em seguida, selecione **Criar**.

   ![Criar ligação ao servidor FTP](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Depois de aparecer a ação dos metadados do **ficheiro Get,** clique no interior da caixa **de ficheiros** para que a lista de conteúdos dinâmicos apareça. Agora pode selecionar propriedades para as saídas a partir de passos anteriores. Na lista de conteúdos dinâmicos, em **obter metadados**de ficheiros, selecione a **propriedade Lista de Ficheiros Id,** que refere a recolha onde o ficheiro foi adicionado ou atualizado.

   ![Localizar e selecionar propriedade "List of Files Id"](./media/connectors-create-api-ftp/select-list-of-files-id-output.png)

   A **propriedade Id da Lista de Ficheiros** aparece agora na caixa de **ficheiros.**

   ![Propriedade "Lista de Ficheiros Id"](./media/connectors-create-api-ftp/selected-list-file-ids-ftp-action.png)

1. Agora adicione esta ação FTP: **Obtenha conteúdo de ficheiro**

   ![Localizar e selecionar a ação "Obter conteúdo de ficheiro"](./media/connectors-create-api-ftp/select-get-file-content-ftp-action.png)

1. Depois de aparecer a ação de conteúdo do **ficheiro Get,** clique no interior da caixa **de ficheiros** para que a lista de conteúdos dinâmicos apareça. Agora pode selecionar propriedades para as saídas a partir de passos anteriores. Na lista de conteúdos dinâmicos, em obter metadados de **ficheiros**, selecione a propriedade **Id,** que refere o ficheiro que foi adicionado ou atualizado.

   ![Localizar e selecionar propriedade "Id"](./media/connectors-create-api-ftp/get-file-content-id-output.png)

   A propriedade **id** agora aparece na caixa de **arquivos.**

   ![Propriedade "Id" selecionada](./media/connectors-create-api-ftp/selected-get-file-content-id-ftp-action.png)

1. Guarde a aplicação lógica.

## <a name="test-your-logic-app"></a>Teste a sua aplicação lógica

Para verificar se o seu fluxo de trabalho devolve o conteúdo que espera, adicione outra ação que lhe envie o conteúdo do ficheiro carregado ou atualizado.

1. No âmbito da ação de conteúdo do **ficheiro Get,** adicione uma ação que pode enviar-lhe o conteúdo do ficheiro. Este exemplo adiciona o Envio de uma ação **de e-mail** para o Office 365 Outlook.

   ![Adicione uma ação para enviar e-mail](./media/connectors-create-api-ftp/select-send-email-action.png)

1. Depois da ação aparecer, forneça as informações e inclua as propriedades que pretende testar. Por exemplo, inclua a propriedade de conteúdo de **Ficheiro,** que aparece na lista de conteúdos dinâmicos depois de selecionar **Ver mais** na secção de conteúdo de **ficheiros Get.**

   ![Fornecer informações sobre ação de e-mail](./media/connectors-create-api-ftp/selected-send-email-action.png)

1. Guarde a aplicação lógica. Para executar e acionar a aplicação lógica, na barra de ferramentas, selecione **Executar**, e depois adicione um ficheiro à pasta FTP que a sua aplicação lógica agora monitoriza.

## <a name="connector-reference"></a>Referência do conector

Para obter mais detalhes técnicos sobre este conector, tais como gatilhos, ações e limites descritos pelo ficheiro Swagger do conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/ftpconnector/).

> [!NOTE]
> Para aplicações lógicas num ambiente de serviço de [integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão do conector com o rótulo ISE utiliza os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)
