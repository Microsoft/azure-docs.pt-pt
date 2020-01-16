---
title: Conectar-se ao servidor FTP
description: Automatizar tarefas e fluxos de trabalho que criam, monitoram e gerenciam arquivos em um servidor FTP usando aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 12/15/2019
tags: connectors
ms.openlocfilehash: 0314dceb23f02b723854dfc406e9440bbc14ccf6
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044297"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Criar, monitorar e gerenciar arquivos FTP usando aplicativos lógicos do Azure

Com os aplicativos lógicos do Azure e o conector de FTP, você pode criar tarefas e fluxos de trabalho automatizados que criam, monitoram, enviam e recebem arquivos por meio de sua conta em um servidor FTP, juntamente com outras ações, por exemplo:

* Monitorar quando os arquivos são adicionados ou alterados.
* Obter, criar, copiar, atualizar, listar e excluir arquivos.
* Obter conteúdo do arquivo e metadados.
* Extraia arquivos mortos em pastas.

Você pode usar gatilhos que obtêm respostas do servidor FTP e disponibilizam a saída para outras ações. Você pode usar ações de execução em seus aplicativos lógicos para gerenciar arquivos no servidor FTP. Você também pode fazer com que outras ações usem a saída de ações de FTP. Por exemplo, se você obtiver regularmente arquivos do seu servidor FTP, poderá enviar um email sobre esses arquivos e seu conteúdo usando o conector do Outlook do Office 365 ou o conector do Outlook.com. Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md).

## <a name="limitations"></a>Limitações

* O conector de FTP dá suporte apenas a FTPS (FTP explícito sobre SSL) e não é compatível com FTPS implícita.

* Por padrão, as ações de FTP podem ler ou gravar arquivos que são *50 MB ou menores*. Para lidar com arquivos com mais de 50 MB, as ações de FTP dão suporte a [agrupamento de mensagens](../logic-apps/logic-apps-handle-large-messages.md). A ação **obter conteúdo do arquivo** usa implicitamente o agrupamento.

* Os gatilhos de FTP não dão suporte a agrupamentos. Ao solicitar o conteúdo do arquivo, os gatilhos selecionam apenas os arquivos que são 50 MB ou menores. Para obter arquivos com mais de 50 MB, siga este padrão:

  * Use um gatilho de FTP que retorne Propriedades de arquivo, como **quando um arquivo é adicionado ou modificado (somente Propriedades)** .

  * Siga o gatilho com a ação FTP **Get file Content** , que lê o arquivo completo e usa o agrupamento implicitamente.

* Se você tiver um servidor FTP local, considere criar um [ISE (ambiente do serviço de integração)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) ou usar [Azure app conexões híbridas do serviço](../app-service/app-service-hybrid-connections.md), que permitem que você acesse fontes de dados locais sem usar um gateway de dados local.

## <a name="how-ftp-triggers-work"></a>Como funcionam os gatilhos de FTP

Os gatilhos de FTP funcionam sondando o sistema de arquivos FTP e procurando qualquer arquivo que tenha sido alterado desde a última sondagem. Algumas ferramentas permitem preservar o carimbo de data/hora quando os arquivos são alterados. Nesses casos, você precisa desabilitar esse recurso para que o gatilho possa funcionar. Aqui estão algumas configurações comuns:

| Cliente SFTP | Ação |
|-------------|--------|
| Winscp | Acesse **opções** > **preferências** > **transferir** > **Editar** > **preservar carimbo de data/hora** > **desabilitar** |
| FileZilla | Vá para **transferir** > **preservar carimbos de data/hora dos arquivos transferidos** > **desabilitar** |
|||

Quando um gatilho encontra um novo arquivo, o gatilho verifica se o novo arquivo está concluído e não parcialmente gravado. Por exemplo, um arquivo pode ter alterações em andamento quando o gatilho verifica o servidor de arquivos. Para evitar o retorno de um arquivo parcialmente gravado, o gatilho nota o carimbo de data/hora do arquivo que tem alterações recentes, mas não retorna imediatamente esse arquivo. O gatilho retorna o arquivo somente ao sondar o servidor novamente. Às vezes, esse comportamento pode causar um atraso que é até duas vezes o intervalo de sondagem do gatilho.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* O endereço do servidor host FTP e as credenciais da conta

  O conector de FTP requer que o servidor FTP esteja acessível pela Internet e configurado para operar no modo *passivo* . Suas credenciais permitem que seu aplicativo lógico crie uma conexão e acesse sua conta FTP.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar sua conta FTP. Para começar com um gatilho de FTP, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação de FTP, inicie seu aplicativo lógico com outro gatilho, por exemplo, o gatilho de **recorrência** .

## <a name="connect-to-ftp"></a>Conectar-se ao FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com)e abra seu aplicativo lógico no designer de aplicativo lógico.

1. Para aplicativos lógicos em branco, na caixa de pesquisa, insira `ftp` como filtro. Na lista de **gatilhos** , selecione o gatilho desejado.

   -ou-

   Para os aplicativos lógicos existentes, na última etapa em que você deseja adicionar uma ação, selecione **nova etapa**e, em seguida, selecione **Adicionar uma ação**. Na caixa de pesquisa, insira `ftp` como seu filtro. Na lista **ações** , selecione a ação desejada.

   Para adicionar uma ação entre etapas, mova o ponteiro sobre a seta entre as etapas. Selecione o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Forneça suas informações de conexão e selecione **criar**.

1. Forneça as informações para o gatilho ou ação selecionada e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="examples"></a>Exemplos

<a name="file-added-modified"></a>

### <a name="add-ftp-trigger"></a>Adicionar gatilho de FTP

O gatilho **quando um arquivo é adicionado ou modificado (somente Propriedades)** inicia um fluxo de trabalho do aplicativo lógico quando o gatilho detecta que um arquivo é adicionado ou alterado em um servidor FTP. Por exemplo, você pode adicionar uma condição que verifica o conteúdo do arquivo e decide se deseja obter esse conteúdo, com base em se esse conteúdo atende a uma condição especificada. Por fim, você pode adicionar uma ação que obtém o conteúdo do arquivo e colocar esse conteúdo em uma pasta diferente no servidor SFTP.

Por exemplo, você pode usar esse gatilho para monitorar uma pasta de FTP em busca de novos arquivos que descrevem pedidos de clientes. Você pode usar uma ação de FTP como **obter metadados de arquivo** para obter as propriedades desse novo arquivo e, em seguida, usar **obter conteúdo do arquivo** para obter o conteúdo desse arquivo para processamento adicional e armazenar esse pedido em um banco de dados de pedidos.

Aqui está um exemplo que mostra como usar o gatilho **quando um arquivo é adicionado ou modificado (somente Propriedades)** .

1. Entre no [portal do Azure](https://portal.azure.com)e abra seu aplicativo lógico no designer de aplicativo lógico, se ainda não estiver aberto.

1. Para aplicativos lógicos em branco, na caixa de pesquisa, insira `ftp` como filtro. Na lista de gatilhos, selecione este gatilho: **quando um arquivado é adicionado ou modificado (somente Propriedades)**

   ![Localizar e selecionar o gatilho de FTP](./media/connectors-create-api-ftp/select-ftp-trigger-logic-app.png)

1. Forneça os detalhes necessários para sua conexão e, em seguida, selecione **criar**.

   Por padrão, esse conector transfere arquivos no formato de texto. Para transferir arquivos em formato binário, por exemplo, onde e quando a codificação é usada, selecione **transporte binário**.

   ![Criar conexão com o servidor FTP](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)

1. Na caixa **pasta** , selecione o ícone de pasta para que uma lista seja exibida. Para localizar a pasta que você deseja monitorar para arquivos novos ou editados, selecione a seta de ângulo à direita ( **>** ), navegue até essa pasta e, em seguida, selecione a pasta.

   ![Localizar e Selecionar pasta a ser monitorada](./media/connectors-create-api-ftp/select-folder-ftp-trigger.png)

   A pasta selecionada aparece na caixa **pasta** .

   ![A pasta selecionada aparece na propriedade "pasta"](./media/connectors-create-api-ftp/selected-folder-ftp-trigger.png)

1. Guarde a aplicação lógica. Na barra de ferramentas do designer, selecione **salvar**.

Agora que seu aplicativo lógico tem um gatilho, adicione as ações que você deseja executar quando seu aplicativo lógico encontrar um arquivo novo ou editado. Para este exemplo, você pode adicionar uma ação de FTP que obtém o conteúdo novo ou atualizado.

<a name="get-content"></a>

### <a name="add-ftp-action"></a>Adicionar ação de FTP

A ação **obter metadados do arquivo** Obtém as propriedades de um arquivo que está no servidor FTP e a ação **obter conteúdo do arquivo** Obtém o conteúdo do arquivo com base nas informações sobre esse arquivo no servidor FTP. Por exemplo, você pode adicionar o gatilho do exemplo anterior e essas ações para obter o conteúdo do arquivo depois que esse arquivo é adicionado ou editado.

1. No gatilho ou em qualquer outra ação, selecione **nova etapa**.

1. Na caixa de pesquisa, insira `ftp` como seu filtro. Na lista ações, selecione esta ação: **obter metadados do arquivo**

   ![Selecione a ação "obter metadados de arquivo"](./media/connectors-create-api-ftp/select-get-file-metadata-ftp-action.png)

1. Se você já tiver uma conexão com o servidor FTP e a conta, vá para a próxima etapa. Caso contrário, forneça os detalhes necessários para essa conexão e, em seguida, selecione **criar**.

   ![Criar conexão do servidor FTP](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Após a ação **obter metadados do arquivo** aparecer, clique dentro da caixa **arquivo** para que a lista de conteúdo dinâmico seja exibida. Agora você pode selecionar propriedades para as saídas de etapas anteriores. Na lista conteúdo dinâmico, em **obter metadados de arquivo**, selecione a propriedade **ID de arquivos** , que faz referência à coleção em que o arquivo foi adicionado ou atualizado.

   ![Localize e selecione a propriedade "lista de arquivos ID"](./media/connectors-create-api-ftp/select-list-of-files-id-output.png)

   A **lista de propriedades de ID de arquivos** agora aparece na caixa **arquivo** .

   ![Propriedade "lista de arquivos de ID" selecionada](./media/connectors-create-api-ftp/selected-list-file-ids-ftp-action.png)

1. Agora, adicione esta ação de FTP: **obter conteúdo do arquivo**

   ![Localize e selecione a ação "obter conteúdo do arquivo"](./media/connectors-create-api-ftp/select-get-file-content-ftp-action.png)

1. Após a ação **obter conteúdo do arquivo** aparecer, clique dentro da caixa **arquivo** para que a lista de conteúdo dinâmico seja exibida. Agora você pode selecionar propriedades para as saídas de etapas anteriores. Na lista de conteúdo dinâmico, em **obter metadados de arquivo**, selecione a propriedade **ID** , que faz referência ao arquivo que foi adicionado ou atualizado.

   ![Localizar e selecionar a propriedade "ID"](./media/connectors-create-api-ftp/get-file-content-id-output.png)

   A propriedade **ID** agora aparece na caixa **arquivo** .

   ![Propriedade "ID" selecionada](./media/connectors-create-api-ftp/selected-get-file-content-id-ftp-action.png)

1. Guarde a aplicação lógica.

## <a name="test-your-logic-app"></a>Testar seu aplicativo lógico

Para verificar se o fluxo de trabalho retorna o conteúdo esperado, adicione outra ação que lhe envie o conteúdo do arquivo carregado ou atualizado.

1. Na ação **obter conteúdo do arquivo** , adicione uma ação que possa enviar o conteúdo do arquivo. Este exemplo adiciona a ação **enviar um email** para o Outlook do Office 365.

   ![Adicionar uma ação para enviar email](./media/connectors-create-api-ftp/select-send-email-action.png)

1. Depois que a ação for exibida, forneça as informações e inclua as propriedades que você deseja testar. Por exemplo, inclua a propriedade **Content File** , que aparece na lista de conteúdo dinâmico depois de selecionar **Ver mais** na seção **obter conteúdo do arquivo** .

   ![Fornecer informações sobre a ação de email](./media/connectors-create-api-ftp/selected-send-email-action.png)

1. Guarde a aplicação lógica. Para executar e disparar o aplicativo lógico, na barra de ferramentas, selecione **executar**e, em seguida, adicione um arquivo à pasta de FTP que seu aplicativo lógico agora monitora.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição de OpenAPI (anteriormente, Swagger) do conector, examine a [página de referência do conector](/connectors/ftpconnector/).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)
