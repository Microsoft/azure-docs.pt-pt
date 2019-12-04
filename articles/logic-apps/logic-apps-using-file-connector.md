---
title: Conectar-se a sistemas de arquivos locais
description: Automatizar tarefas e fluxos de trabalho que se conectam a sistemas de arquivos locais com o conector do sistema de arquivos por meio do gateway de dados local em aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 01/13/2019
ms.openlocfilehash: bc2067dbde1e99619fb6e384be4e70f606c8518d
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792783"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Conectar-se a sistemas de arquivos locais com aplicativos lógicos do Azure

Com o conector do sistema de arquivos e os aplicativos lógicos do Azure, você pode criar tarefas e fluxos de trabalho automatizados que criam e gerenciam arquivos em um compartilhamento de arquivos local, por exemplo:  

- Criar, obter, acrescentar, atualizar e excluir arquivos.
- Listar arquivos em pastas ou pastas raiz.
- Obter conteúdo do arquivo e metadados.

Este artigo mostra como você pode se conectar a um sistema de arquivos local, conforme descrito neste cenário de exemplo: copiar um arquivo que é carregado no Dropbox para um compartilhamento de arquivos e, em seguida, enviar um email. Para conectar e acessar sistemas locais com segurança, os aplicativos lógicos usam o [Gateway de dados local](../logic-apps/logic-apps-gateway-connection.md). Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md). Para obter informações técnicas específicas do conector, consulte a [referência do conector do sistema de arquivos](/connectors/filesystem/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Antes de poder conectar aplicativos lógicos a sistemas locais, como o servidor do sistema de arquivos, você precisa [instalar e configurar um gateway de dados local](../logic-apps/logic-apps-gateway-install.md). Dessa forma, você pode especificar para usar a instalação do gateway ao criar a conexão do sistema de arquivos do seu aplicativo lógico.

* Uma [conta do Dropbox](https://www.dropbox.com/), que você pode assinar gratuitamente. Suas credenciais de conta são necessárias para criar uma conexão entre seu aplicativo lógico e sua conta do dropbox.

* Acesso ao computador que tem o sistema de arquivos que você deseja usar. Por exemplo, se você instalar o gateway de dados no mesmo computador que o sistema de arquivos, precisará das credenciais de conta para esse computador.

* Uma conta de email de um provedor com suporte dos aplicativos lógicos, como o Office 365 Outlook, Outlook.com ou gmail. Para outros fornecedores, [consulte a lista de conectores aqui](https://docs.microsoft.com/connectors/). Esta aplicação lógica utiliza uma conta do Outlook do Office 365. Se utilizar outra conta de e-mail, os passos gerais são os mesmos, mas a IU poderá ser ligeiramente diferente.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para este exemplo, você precisa de um aplicativo lógico em branco.

## <a name="add-trigger"></a>Adicionar gatilho

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com)e abra seu aplicativo lógico no designer de aplicativo lógico, se ainda não estiver aberto.

1. Na caixa de pesquisa, digite "Dropbox" como filtro. Na lista de gatilhos, selecione este gatilho: **quando um arquivo é criado**

   ![Selecionar gatilho do Dropbox](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. Entre com suas credenciais de conta do Dropbox e autorize o acesso aos dados do Dropbox para aplicativos lógicos do Azure.

1. Forneça as informações necessárias para seu gatilho.

   ![Gatilho do Dropbox](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>Adicionar ações

1. No gatilho, escolha a **próxima etapa**. Na caixa de pesquisa, digite "sistema de arquivos" como filtro. Na lista ações, selecione esta ação: **criar arquivo**

   ![Localizar conector do sistema de arquivos](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Se você ainda não tiver uma conexão com o sistema de arquivos, você será solicitado a criar uma conexão.

   ![Criar ligação](media/logic-apps-using-file-connector/file-system-connection.png)

   | Propriedade | Obrigatório | Valor | Descrição |
   | -------- | -------- | ----- | ----------- |
   | **Nome da Ligação** | Sim | <*nome da conexão*> | O nome que você deseja para sua conexão |
   | **Pasta raiz** | Sim | <*nome da pasta raiz*> | A pasta raiz do seu sistema de arquivos, por exemplo, se você instalou o gateway de dados local, como uma pasta de locais no computador em que o gateway de dados local está instalado ou a pasta de um compartilhamento de rede que o computador pode acessar. <p>Por exemplo: `\\PublicShare\\DropboxFiles` <p>A pasta raiz é a pasta pai principal, que é usada para caminhos relativos para todas as ações relacionadas a arquivos. |
   | **Tipo de autenticação** | Não | > *de <de tipo de autenticação* | O tipo de autenticação que o sistema de arquivos usa, por exemplo, **Windows** |
   | **Nome de Utilizador** | Sim | <>de *domínio* \\<*nome de usuário*> | O nome de usuário para o computador em que você tem o sistema de arquivos |
   | **Palavra-passe** | Sim | <*sua senha*> | A senha do computador em que você tem o sistema de arquivos |
   | **Gateway** | Sim | <*installed-gateway-name*> | O nome do gateway instalado anteriormente |
   |||||

1. Quando tiver terminado, escolha **Create** (Criar).

   Os aplicativos lógicos definem e testam sua conexão, certificando-se de que a conexão funcione corretamente. Se a conexão estiver configurada corretamente, as opções serão exibidas para a ação que você selecionou anteriormente.

1. Na ação **criar arquivo** , forneça os detalhes para copiar arquivos do Dropbox para a pasta raiz em seu compartilhamento de arquivos local. Para adicionar saídas de etapas anteriores, clique dentro das caixas e selecione os campos disponíveis quando a lista de conteúdo dinâmico for exibida.

   ![Criar ação de arquivo](media/logic-apps-using-file-connector/create-file-filled.png)

1. Agora, adicione uma ação do Outlook que envia um email para que os usuários apropriados saibam sobre o novo arquivo. Insira os destinatários, o título e o corpo do email. Para teste, você pode usar seu próprio endereço de email.

   ![Enviar ação de email](media/logic-apps-using-file-connector/send-email.png)

1. Guarde a aplicação lógica. Teste seu aplicativo carregando um arquivo no dropbox.

   Seu aplicativo lógico deve copiar o arquivo para o compartilhamento de arquivos local e enviar aos destinatários um email sobre o arquivo copiado.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição de OpenAPI (anteriormente, Swagger) do conector, examine a [página de referência](/connectors/fileconnector/)do conector.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [se conectar a dados locais](../logic-apps/logic-apps-gateway-connection.md) 
* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)
