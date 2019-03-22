---
title: Ligar a sistemas de ficheiros no local - Azure Logic Apps | Documentos da Microsoft
description: Automatizar tarefas e fluxos de trabalho que se ligam a sistemas de ficheiros no local com o conector do sistema de ficheiros através do gateway de dados no local no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 01/13/2019
ms.openlocfilehash: a933824e9553ee7f638da495b46ebed19e04169d
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316971"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Ligar a sistemas de ficheiros no local com o Azure Logic Apps

Com o conector de sistema de ficheiros e o Azure Logic Apps, pode criar tarefas automatizadas e partilharem fluxos de trabalho que criar e gerenciar arquivos num arquivo no local, por exemplo:  

- Criar, obter, acrescentar, atualizar e eliminar ficheiros.
- Listar ficheiros em pastas ou pastas raiz.
- Obter conteúdo do ficheiro e metadados.

Este artigo mostra como pode ligar a um sistema de ficheiros no local, conforme descrito neste cenário de exemplo: copiar um ficheiro que é carregado para a Dropbox para uma partilha de ficheiros e, em seguida, enviar um e-mail. Para ligar e aceder a sistemas no local, utilização de aplicações lógicas com segurança os [gateway de dados no local](../logic-apps/logic-apps-gateway-connection.md). Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

Para seguir o exemplo, precisa destes itens:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>. 

* Antes de poder ligar aplicações lógicas para sistemas no local, como o seu servidor de sistema de arquivos, precisa [instalar e configurar um gateway de dados no local](../logic-apps/logic-apps-gateway-install.md). Dessa forma, pode especificar a utilizar a instalação do gateway, quando criar a ligação do sistema de ficheiros da sua aplicação lógica.

* R [conta do Dropbox](https://www.dropbox.com/), que pode inscrever-se gratuitamente. As credenciais da conta são necessárias para criar uma ligação entre a sua aplicação lógica e a sua conta do Dropbox. 

* Acesso ao computador que tenha o sistema de ficheiros que pretende utilizar. Por exemplo, se instalar o gateway de dados no mesmo computador que o seu sistema de ficheiros, terá das credenciais da conta para esse computador. 

* Uma conta de e-mail de um fornecedor suportado pelo Logic Apps, como o Outlook do Office 365, Outlook.com ou Gmail. Para outros fornecedores, [consulte a lista de conectores aqui](https://docs.microsoft.com/connectors/). Esta aplicação lógica utiliza uma conta do Outlook do Office 365. Se utilizar outra conta de e-mail, os passos gerais são os mesmos, mas a IU poderá ser ligeiramente diferente. 

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md). Neste exemplo, precisa de uma aplicação lógica em branco.

## <a name="add-trigger"></a>Adicionar acionador

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e abra a aplicação lógica no Estruturador da aplicação lógica, se não estiver já abrir.

1. Na caixa de pesquisa, introduza "dropbox" como o filtro. Na lista de disparadores, selecione este acionador: **Quando é criado um ficheiro**

   ![Selecione o acionador do Dropbox](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. Inicie sessão com as credenciais da conta Dropbox e autorizar o acesso aos seus dados do Dropbox para o Azure Logic Apps. 

1. Indique as informações necessárias para o acionador.

   ![Acionador do Dropbox](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>Adicionar ações

1. No acionador, escolha **passo seguinte**. Na caixa de pesquisa, introduza "sistema de ficheiros" como o filtro. Na lista de ações, selecione a ação: **Criar ficheiro - sistema de ficheiros**

   ![Localize o conector do sistema de ficheiros](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Se ainda não tiver uma ligação ao sistema de arquivos, lhe for pedido para criar uma ligação.

   ![Criar ligação](media/logic-apps-using-file-connector/file-system-connection.png)

   | Propriedade | Necessário | Value | Descrição | 
   | -------- | -------- | ----- | ----------- | 
   | **Nome da Ligação** | Sim | <*connection-name*> | O nome que pretende para a sua ligação | 
   | **Pasta raiz** | Sim | <*root-folder-name*> | A pasta de raiz para o seu sistema de ficheiros, por exemplo, se tiver instalado o gateway de dados no local, como uma pasta local no computador onde está instalado o gateway de dados no local, ou a pasta para uma partilha de rede que o computador pode aceder. <p>Por exemplo: `\\PublicShare\\DropboxFiles` <p>A pasta de raiz é a pasta de principal principal, que é utilizada para caminhos relativos para todas as ações relacionadas com o ficheiro. | 
   | **Tipo de autenticação** | Não | <*auth-type*> | O tipo de autenticação que utiliza o seu sistema de ficheiros, por exemplo, **Windows** | 
   | **Nome de Utilizador** | Sim | <*domain*>\\<*username*> | O nome de utilizador para o computador em que tem o sistema de ficheiros | 
   | **Palavra-passe** | Sim | <*a palavra-passe*> | A palavra-passe para o computador em que tem o sistema de ficheiros | 
   | **gateway** | Sim | <*installed-gateway-name*> | O nome para o seu gateway instalado anteriormente | 
   ||| 

1. Quando tiver terminado, escolha **Create** (Criar).

   O Logic Apps configura e testa a ligação, certificar-se de que a ligação funciona corretamente. 
   Se a ligação está configurada corretamente, são apresentadas as opções para a ação que selecionou anteriormente. 

1. Na **criar ficheiro** ação, forneça os detalhes para copiar os ficheiros do Dropbox para a pasta de raiz na partilha de ficheiros no local. Para adicionar saídas dos passos anteriores, clique no interior das caixas e selecione campos disponíveis quando for apresentada a lista de conteúdo dinâmico.

   ![Criar uma ação de ficheiro](media/logic-apps-using-file-connector/create-file-filled.png)

1. Agora, adicione uma ação do Outlook que envia um e-mail para que os utilizadores adequados sabem sobre o novo ficheiro. Introduza os destinatários, o título e o corpo do e-mail. Para fins de teste, pode utilizar o seu endereço de e-mail.

   ![Enviar a ação de e-mail](media/logic-apps-using-file-connector/send-email.png)

1. Guarde a aplicação lógica. Teste seu aplicativo ao carregar um ficheiro ao Dropbox. 

   A aplicação lógica deve copiar o ficheiro para a partilha de ficheiros no local e enviar um e-mail de destinatários sobre o ficheiro copiado.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre os limites, ações e acionadores, que é descrito através OpenAPI do conector (anteriormente Swagger) descrição, reveja o conector [página de referência](/connectors/fileconnector/).

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Para ajudar a melhorar o Azure Logic Apps e conectores, vote ou submeta ideias no [site do Azure Logic Apps User Voice](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [ligar aos dados no local](../logic-apps/logic-apps-gateway-connection.md) 
* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)
