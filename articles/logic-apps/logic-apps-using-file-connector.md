---
title: Ligar aos sistemas de ficheiros nas instalações
description: Automatizar tarefas e fluxos de trabalho que se ligam aos sistemas de ficheiros no local com o conector do Sistema de Ficheiros através do portal de dados no local em Azure Logic Apps
services: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/13/2019
ms.openlocfilehash: 65b6b1f783dbabc9ad2e1a4bf79008240d1b2726
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89659924"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Ligar a sistemas de ficheiros no local com o Azure Logic Apps

Com as Apps Lógicas Azure e o conector do Sistema de Ficheiros, pode criar tarefas e fluxos de trabalho automatizados que criam e gerem ficheiros numa partilha de ficheiros no local, por exemplo:

- Criar, obter, apender, atualizar e eliminar ficheiros.
- Listar ficheiros em pastas ou pastas de raiz.
- Obtenha conteúdo de ficheiro e metadados.

Este artigo mostra como pode ligar-se a um sistema de ficheiros no local, conforme descrito por este cenário de exemplo: copiar um ficheiro que é enviado para o Dropbox para uma partilha de ficheiros e, em seguida, enviar um e-mail. Para conectar e aceder de forma segura aos sistemas no local, as aplicações lógicas utilizam o [portal de dados no local.](../logic-apps/logic-apps-gateway-connection.md) Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps?](../logic-apps/logic-apps-overview.md) Para obter informações técnicas específicas do conector, consulte a referência do [conector do Sistema de Ficheiros](/connectors/filesystem/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Antes de poder ligar aplicações lógicas a sistemas no local, como o servidor do sistema de ficheiros, é necessário [instalar e configurar um gateway de dados no local.](../logic-apps/logic-apps-gateway-install.md) Desta forma, pode especificar para utilizar a instalação gateway quando criar a ligação do sistema de ficheiros a partir da sua aplicação lógica.

* Uma [conta Dropbox,](https://www.dropbox.com/)que pode inscrever-se gratuitamente. As credenciais da sua conta são necessárias para criar uma ligação entre a sua aplicação lógica e a sua conta Dropbox.

* Acesso ao computador que tem o sistema de ficheiros que pretende utilizar. Por exemplo, se instalar o portal de dados no mesmo computador que o seu sistema de ficheiros, precisa das credenciais de conta para esse computador.

* Uma conta de e-mail de um fornecedor que é suportado por Aplicações Lógicas, como o Office 365 Outlook, Outlook.com ou Gmail. Para outros fornecedores, [consulte a lista de conectores aqui](/connectors/). Esta aplicação lógica utiliza uma conta de trabalho ou escola. Se utilizar outra conta de e-mail, os passos gerais são os mesmos, mas a IU poderá ser ligeiramente diferente.

  > [!IMPORTANT]
  > Se quiser utilizar o conector do Gmail, apenas as contas de negócios da G-Suite podem utilizar este conector sem restrições em aplicações lógicas. Se tiver uma conta de consumo do Gmail, pode utilizar este conector apenas com serviços específicos aprovados pela Google, ou pode [criar uma aplicação para clientes da Google para utilizar para autenticação com o seu conector Gmail.](/connectors/gmail/#authentication-and-bring-your-own-application) Para obter mais informações, consulte [as políticas de segurança de dados e privacidade para conectores google em Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Conhecimento básico sobre [como criar aplicações lógicas.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Para este exemplo, precisa de uma aplicação lógica em branco.

## <a name="add-trigger"></a>Adicionar acionador

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se não abrir já.

1. Na caixa de pesquisa, introduza a "dropbox" como filtro. A partir da lista de gatilhos, selecione este gatilho: **Quando um ficheiro é criado**

   ![Selecione o gatilho Dropbox](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. Inscreva-se com as suas credenciais de conta Dropbox e autorize o acesso aos seus dados dropbox para Apps Azure Logic.

1. Forneça as informações necessárias para o seu gatilho.

   ![Gatilho dropbox](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>Adicionar ações

1. Sob o gatilho, escolha **o próximo passo**. Na caixa de pesquisa, introduza o "sistema de ficheiros" como filtro. A partir da lista de ações, selecione esta ação: **Criar ficheiro**

   ![Encontrar conector do sistema de ficheiros](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Se ainda não tiver uma ligação ao seu sistema de ficheiros, é solicitado que crie uma ligação.

   ![Criar ligação](media/logic-apps-using-file-connector/file-system-connection.png)

   | Propriedade | Necessário | Valor | Descrição |
   | -------- | -------- | ----- | ----------- |
   | **Nome de conexão** | Sim | <*nome de conexão*> | O nome que deseja para a sua ligação |
   | **Pasta raiz** | Sim | <*nome de pasta de raiz*> | A pasta raiz do seu sistema de ficheiros, por exemplo, se instalou o seu portal de dados no local, como uma pasta local no computador onde está instalado o gateway de dados no local ou a pasta para uma partilha de rede a que o computador pode aceder. <p>Por exemplo: `\\PublicShare\\DropboxFiles` <p>A pasta raiz é a pasta principal dos pais, que é usada para caminhos relativos para todas as ações relacionadas com o ficheiro. |
   | **Tipo de autenticação** | Não | <*auth-type*> | O tipo de autenticação que o seu sistema de ficheiros utiliza: **Windows** |
   | **Nome de Utilizador** | Sim | <*domain* > \\ domínio < *nome de utilizador*> <p>-ou- <p><*local-computador* > \\ < *nome de utilizador*> | O nome de utilizador do computador onde tem a pasta do sistema de ficheiros. <p>Se a pasta do sistema de ficheiros estiver no mesmo computador que o gateway de dados no local, pode utilizar <nome de utilizador *local do computador* > \\ < *username*>. |
   | **Palavra-passe** | Sim | <*sua senha*> | A palavra-passe para o computador onde tem o seu sistema de ficheiros |
   | **porta de entrada** | Sim | <*instalado-gateway-name*> | O nome do seu gateway previamente instalado |
   |||||

1. Quando tiver terminado, escolha **Create** (Criar).

   As Aplicações Lógicas configuram e testa a sua ligação, certificando-se de que a ligação funciona corretamente. Se a ligação estiver corretamente configurada, aparecem opções para a ação que selecionou anteriormente.

1. Na ação de **ficheiro Create,** forneça os detalhes para copiar ficheiros do Dropbox para a pasta raiz na partilha de ficheiros no local. Para adicionar saídas de passos anteriores, clique no interior das caixas e selecione dos campos disponíveis quando aparecer a lista de conteúdos dinâmicos.

   ![Criar ação de ficheiros](media/logic-apps-using-file-connector/create-file-filled.png)

1. Agora, adicione uma ação do Outlook que envia um e-mail para que os utilizadores apropriados saibam sobre o novo ficheiro. Insira os destinatários, título e corpo do e-mail. Para testes, pode utilizar o seu próprio endereço de e-mail.

   ![Enviar ação de e-mail](media/logic-apps-using-file-connector/send-email.png)

1. Guarde a sua aplicação lógica. Teste a sua aplicação enviando um ficheiro para o Dropbox.

   A sua aplicação lógica deve copiar o ficheiro para a sua partilha de ficheiros no local e enviar aos destinatários um e-mail sobre o ficheiro copiado.

## <a name="connector-reference"></a>Referência do conector

Para obter mais detalhes técnicos sobre este conector, tais como gatilhos, ações e limites descritos pelo ficheiro Swagger do conector, consulte a [página de referência do conector](/connectors/fileconnector/).

> [!NOTE]
> Para aplicações lógicas num ambiente de [serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão com rótulo ISE deste conector utiliza os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Passos seguintes

* Saiba como [ligar-se aos dados no local](../logic-apps/logic-apps-gateway-connection.md) 
* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)
