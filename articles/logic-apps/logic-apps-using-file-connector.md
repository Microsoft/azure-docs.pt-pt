---
title: Ligar a sistemas de ficheiros nas instalações
description: Automatizar tarefas e fluxos de trabalho que se ligam aos sistemas de ficheiros no local com o conector do Sistema de Ficheiros através da gateway de dados no local em Aplicações lógicas azure
services: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 01/13/2019
ms.openlocfilehash: ab17137f162b893b54942d870b07a36f87d1b71d
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115077"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Ligar a sistemas de ficheiros no local com Azure Logic Apps

Com as Aplicações Lógicas Azure e o conector do Sistema de Ficheiros, pode criar tarefas automatizadas e fluxos de trabalho que criam e gerem ficheiros numa partilha de ficheiros no local, por exemplo:

- Criar, obter, anexar, atualizar e eliminar ficheiros.
- Enumerar ficheiros em pastas ou pastas-raiz.
- Obtenha conteúdo de ficheiros e metadados.

Este artigo mostra como pode ligar-se a um sistema de ficheiros no local, conforme descrito por este cenário de exemplo: copiar um ficheiro que é enviado para o Dropbox para uma partilha de ficheiros e, em seguida, enviar um e-mail. Para ligar e aceder de forma segura aos sistemas no local, as aplicações lógicas utilizam a [porta de dados no local.](../logic-apps/logic-apps-gateway-connection.md) Se é novo em aplicações lógicas, reveja [o que são as Aplicações Lógicas Azure?](../logic-apps/logic-apps-overview.md) Para obter informações técnicas específicas do conector, consulte a referência do [conector](/connectors/filesystem/)do Sistema de Ficheiros .

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Antes de poder ligar aplicações lógicas a sistemas no local, como o servidor do sistema de ficheiros, é necessário [instalar e configurar um portal de dados no local](../logic-apps/logic-apps-gateway-install.md). Desta forma, pode especificar utilizar a instalação do gateway quando criar a ligação do sistema de ficheiros a partir da sua aplicação lógica.

* Uma [conta Dropbox](https://www.dropbox.com/), que pode inscrever-se gratuitamente. As credenciais da sua conta são necessárias para criar uma ligação entre a sua aplicação lógica e a sua conta Dropbox.

* Acesso ao computador que tem o sistema de ficheiros que pretende utilizar. Por exemplo, se instalar a porta de dados no mesmo computador que o seu sistema de ficheiros, precisa das credenciais de conta para esse computador.

* Uma conta de e-mail de um fornecedor que é suportado por Aplicações Lógicas, como Office 365 Outlook, Outlook.com ou Gmail. Para outros fornecedores, [consulte a lista de conectores aqui](https://docs.microsoft.com/connectors/). Esta aplicação lógica utiliza uma conta do Outlook do Office 365. Se utilizar outra conta de e-mail, os passos gerais são os mesmos, mas a IU poderá ser ligeiramente diferente.

* Conhecimento básico sobre [como criar aplicações lógicas.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Para este exemplo, precisa de uma aplicação lógica em branco.

## <a name="add-trigger"></a>Adicionar acionador

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se ainda não estiver aberta.

1. Na caixa de pesquisa, introduza "dropbox" como filtro. A partir da lista de gatilhos, selecione este gatilho: **Quando um ficheiro é criado**

   ![Selecione gatilho Dropbox](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. Inscreva-se nas credenciais da sua conta Dropbox e autorize o acesso aos seus dados dropbox para Aplicações Lógicas Azure.

1. Forneça as informações necessárias para o seu gatilho.

   ![Gatilho de dropbox](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>Adicionar ações

1. Sob o gatilho, escolha **o próximo passo**. Na caixa de pesquisa, introduza o "sistema de ficheiros" como filtro. A partir da lista de ações, selecione esta ação: **Criar ficheiro**

   ![Localizar conector do sistema de ficheiros](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Se ainda não tiver uma ligação ao seu sistema de ficheiros, é-lhe pedido que crie uma ligação.

   ![Criar ligação](media/logic-apps-using-file-connector/file-system-connection.png)

   | Propriedade | Necessário | Valor | Descrição |
   | -------- | -------- | ----- | ----------- |
   | **Nome de ligação** | Sim | <*nome de ligação*> | O nome que quer para a sua ligação |
   | **Pasta raiz** | Sim | <*nome da pasta raiz*> | A pasta-raiz do seu sistema de ficheiros, por exemplo, se instalou o seu portal de dados no local, como uma pasta local no computador onde está instalada a porta de dados no local, ou a pasta para uma partilha de rede a que o computador pode aceder. <p>Por exemplo: `\\PublicShare\\DropboxFiles` <p>A pasta-raiz é a pasta principal dos pais, que é usada para caminhos relativos para todas as ações relacionadas com ficheiros. |
   | **Tipo de autenticação** | Não | <*auth-tipo*> | O tipo de autenticação que o seu sistema de ficheiros utiliza: **Windows** |
   | **Nome de utilizador** | Sim | <*domain*>\\<*nome de utilizador* de domínio> | O nome de utilizador do computador onde tem o seu sistema de ficheiros |
   | **Palavra-passe** | Sim | <*sua senha*> | A palavra-passe para o computador onde tem o seu sistema de ficheiros |
   | **porta de entrada** | Sim | <*instalado-gateway-nome*> | O nome para o seu gateway previamente instalado |
   |||||

1. Quando tiver terminado, escolha **Create** (Criar).

   As Aplicações Lógicas configuram e testam a sua ligação, certificando-se de que a ligação funciona corretamente. Se a ligação estiver corretamente configurada, aparecem opções para a ação que selecionou anteriormente.

1. Na ação **do ficheiro Create,** forneça os detalhes para copiar ficheiros do Dropbox para a pasta raiz na sua partilha de ficheiros no local. Para adicionar saídas de passos anteriores, clique no interior das caixas e selecione a partir de campos disponíveis quando a lista de conteúdos dinâmicos aparecer.

   ![Criar ação de arquivo](media/logic-apps-using-file-connector/create-file-filled.png)

1. Agora, adicione uma ação do Outlook que envia um e-mail para que os utilizadores apropriados saibam sobre o novo ficheiro. Insira os destinatários, o título e o corpo do e-mail. Para testes, pode utilizar o seu próprio endereço de e-mail.

   ![Enviar ação de e-mail](media/logic-apps-using-file-connector/send-email.png)

1. Guarde a aplicação lógica. Teste a sua aplicação fazendo o upload de um ficheiro para o Dropbox.

   A sua aplicação lógica deve copiar o ficheiro para a sua parte de ficheiro no local e enviar aos destinatários um e-mail sobre o ficheiro copiado.

## <a name="connector-reference"></a>Referência do conector

Para obter mais detalhes técnicos sobre este conector, tais como gatilhos, ações e limites descritos pelo ficheiro Swagger do conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/fileconnector/).

> [!NOTE]
> Para aplicações lógicas num ambiente de serviço de [integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão do conector com o rótulo ISE utiliza os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Passos seguintes

* Saiba como [se conectar aos dados no local](../logic-apps/logic-apps-gateway-connection.md) 
* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)
