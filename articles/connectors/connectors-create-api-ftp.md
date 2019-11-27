---
title: Conectar-se ao servidor FTP-aplicativo lógico do Azure
description: Criar, monitorar e gerenciar arquivos em um servidor FTP com aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: divswa, klam, LADocs
ms.topic: conceptual
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: ac6ae1a3b00a4e7568bd7967105f202fbf2e4f9b
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547496"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Criar, monitorar e gerenciar arquivos FTP usando aplicativos lógicos do Azure

Com os aplicativos lógicos do Azure e o conector de FTP, você pode criar tarefas e fluxos de trabalho automatizados que criam, monitoram, enviam e recebem arquivos por meio de sua conta em um servidor FTP, juntamente com outras ações, por exemplo:

* Monitorar quando os arquivos são adicionados ou alterados.
* Obter, criar, copiar, atualizar, listar e excluir arquivos.
* Obter conteúdo do arquivo e metadados.
* Extraia arquivos mortos em pastas.

Você pode usar gatilhos que obtêm respostas do servidor FTP e disponibilizam a saída para outras ações. Você pode usar ações de execução em seus aplicativos lógicos para gerenciar arquivos no servidor FTP. Você também pode fazer com que outras ações usem a saída de ações de FTP. Por exemplo, se você obtiver regularmente arquivos do seu servidor FTP, poderá enviar um email sobre esses arquivos e seu conteúdo usando o conector do Outlook do Office 365 ou o conector do Outlook.com. Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Limites

* O conector de FTP dá suporte apenas a FTPS (FTP explícito sobre SSL) e não é compatível com FTPS implícita.

* Por padrão, as ações de FTP podem ler ou gravar arquivos que são *50 MB ou menores*. Para lidar com arquivos com mais de 50 MB, as ações de FTP dão suporte a [agrupamento de mensagens](../logic-apps/logic-apps-handle-large-messages.md). A ação **obter conteúdo do arquivo** usa implicitamente o agrupamento.

* Os gatilhos de FTP não dão suporte a agrupamentos. Ao solicitar o conteúdo do arquivo, os gatilhos selecionam apenas os arquivos que são 50 MB ou menores. Para obter arquivos com mais de 50 MB, siga este padrão:

  * Use um gatilho de FTP que retorne Propriedades de arquivo, como **quando um arquivo é adicionado ou modificado (somente Propriedades)** .

  * Siga o gatilho com a ação FTP **Get file Content** , que lê o arquivo completo e usa o agrupamento implicitamente.

## <a name="how-ftp-triggers-work"></a>Como funcionam os gatilhos de FTP

Os gatilhos de FTP funcionam sondando o sistema de arquivos FTP e procurando qualquer arquivo que tenha sido alterado desde a última sondagem. Algumas ferramentas permitem preservar o carimbo de data/hora quando os arquivos são alterados. Nesses casos, você precisa desabilitar esse recurso para que o gatilho possa funcionar. Aqui estão algumas configurações comuns:

| Cliente SFTP | Ação |
|-------------|--------|
| WinSCP | Acesse **opções** > **preferências** > **transferir** > **Editar** > **preservar carimbo de data/hora** > **desabilitar** |
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

1. Entre no [portal do Azure](https://portal.azure.com)e abra seu aplicativo lógico no designer de aplicativo lógico, se ainda não estiver aberto.

1. Para aplicativos lógicos em branco, na caixa de pesquisa, digite "FTP" como filtro. Na lista de gatilhos, selecione o gatilho desejado.

   -ou-

   Para os aplicativos lógicos existentes, na última etapa em que você deseja adicionar uma ação, escolha **nova etapa**e, em seguida, selecione **Adicionar uma ação**. Na caixa de pesquisa, digite "FTP" como filtro. Na lista ações, selecione a ação desejada.

   Para adicionar uma ação entre etapas, mova o ponteiro sobre a seta entre as etapas. Escolha o sinal de adição ( **+** ) que aparece e selecione **Adicionar uma ação**.

1. Forneça os detalhes necessários para sua conexão e, em seguida, escolha **criar**.

1. Forneça os detalhes necessários para o gatilho ou ação selecionado e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="examples"></a>Exemplos

<a name="file-added-modified"></a>

### <a name="ftp-trigger-when-a-file-is-added-or-modified"></a>Gatilho de FTP: quando um arquivo é adicionado ou modificado

Esse gatilho inicia um fluxo de trabalho do aplicativo lógico quando o gatilho detecta quando um arquivo é adicionado ou alterado em um servidor FTP. Por exemplo, você pode adicionar uma condição que verifica o conteúdo do arquivo e decide se deseja obter esse conteúdo, com base em se o conteúdo atende a uma condição especificada. Por fim, você pode adicionar uma ação que obtém o conteúdo do arquivo e colocar esse conteúdo em uma pasta no servidor SFTP.

**Exemplo empresarial**: você pode usar esse gatilho para monitorar uma pasta de FTP para novos arquivos que descrevem pedidos de clientes. Você pode usar uma ação de FTP, como **obter conteúdo do arquivo**, para que possa obter o conteúdo da ordem para processamento adicional e armazenar esse pedido em um banco de dados de pedidos.

Aqui está um exemplo que mostra esse gatilho: **quando um arquivo é adicionado ou modificado**

1. Entre no [portal do Azure](https://portal.azure.com)e abra seu aplicativo lógico no designer de aplicativo lógico, se ainda não estiver aberto.

1. Para aplicativos lógicos em branco, na caixa de pesquisa, digite "FTP" como filtro. Na lista de gatilhos, selecione este gatilho: **quando um arquivado é adicionado ou modificado-FTP**

   ![Localizar e selecionar gatilho de FTP](./media/connectors-create-api-ftp/select-ftp-trigger.png)  

1. Forneça os detalhes necessários para sua conexão e, em seguida, escolha **criar**.

   Por padrão, esse conector transfere arquivos no formato de texto. Para transferir arquivos em formato binário, por exemplo, onde e quando a codificação é usada, selecione **transporte binário**.

   ![Criar conexão do servidor FTP](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)  

1. Ao lado da caixa **pasta** , escolha o ícone de pasta para que uma lista seja exibida. Para localizar a pasta que você deseja monitorar para arquivos novos ou editados, selecione a seta de ângulo à direita ( **>** ), navegue até essa pasta e, em seguida, selecione a pasta.

   ![Localizar e Selecionar pasta a ser monitorada](./media/connectors-create-api-ftp/select-folder.png)  

   A pasta selecionada aparece na caixa **pasta** .

   ![Pasta selecionada](./media/connectors-create-api-ftp/selected-folder.png)  

Agora que seu aplicativo lógico tem um gatilho, adicione as ações que você deseja executar quando seu aplicativo lógico encontrar um arquivo novo ou editado. Para este exemplo, você pode adicionar uma ação de FTP que obtém o conteúdo novo ou atualizado.

<a name="get-content"></a>

### <a name="ftp-action-get-content"></a>Ação de FTP: obter conteúdo

Essa ação Obtém o conteúdo de um arquivo em um servidor FTP quando esse arquivo é adicionado ou atualizado. Por exemplo, você pode adicionar o gatilho do exemplo anterior e uma ação que obtém o conteúdo do arquivo depois que esse arquivo é adicionado ou editado.

Aqui está um exemplo que mostra esta ação: **obter conteúdo**

1. No gatilho ou em qualquer outra ação, escolha **nova etapa**.

1. Na caixa de pesquisa, digite "FTP" como filtro. Na lista ações, selecione esta ação: **obter conteúdo do arquivo-FTP**

   ![Selecionar ação de FTP](./media/connectors-create-api-ftp/select-ftp-action.png)  

1. Se você já tiver uma conexão com o servidor FTP e a conta, vá para a próxima etapa. Caso contrário, forneça os detalhes necessários para essa conexão e, em seguida, escolha **criar**.

   ![Criar conexão do servidor FTP](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Depois que a ação **obter conteúdo do arquivo** for aberta, clique dentro da caixa **arquivo** para que a lista de conteúdo dinâmico seja exibida. Agora você pode selecionar propriedades para as saídas de etapas anteriores. Na lista conteúdo dinâmico, selecione a propriedade **conteúdo do arquivo** , que tem o conteúdo para o arquivo adicionado ou atualizado.  

   ![Localizar e Selecionar arquivo](./media/connectors-create-api-ftp/ftp-action-get-file-content.png)

   A propriedade **conteúdo do arquivo** agora aparece na caixa **arquivo** .

   ![Propriedade "conteúdo do arquivo" selecionada](./media/connectors-create-api-ftp/ftp-action-selected-file-content-property.png)

1. Guarde a aplicação lógica. Para testar seu fluxo de trabalho, adicione um arquivo à pasta de FTP que seu aplicativo lógico monitora agora.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição de OpenAPI (anteriormente, Swagger) do conector, examine a [página de referência do conector](/connectors/ftpconnector/).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)