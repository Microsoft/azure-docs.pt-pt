---
title: Implementação contínua para funções do Azure | Documentos da Microsoft
description: Use os recursos de implementação contínua do serviço de aplicações do Azure para publicar as suas funções.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: cb3f3ad3bb7b42429654ea4bf9b49f7e230db1da
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943886"
---
# <a name="continuous-deployment-for-azure-functions"></a>Implementação contínua para Funções do Azure
As funções do Azure torna mais fácil implementar a aplicação de funções com a integração contínua. Funções integra-se com repositórios de código principal e origens de implementação. Esta integração permite que um fluxo de trabalho que onde o código de função atualizações feito por meio de uma implementação de Acionador estes serviços do Azure. Se estiver familiarizado com as funções do Azure, comece com o [descrição geral das funções do Azure](functions-overview.md).

Implementação contínua é uma excelente opção para projetos em que estiver a integrar várias e frequentes contribuições. Ele também permite manter o controlo de origem no seu código de função. As funções do Azure suporta as seguintes origens de implementação:

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://www.dropbox.com/)
* Repositório externo (Git ou Mercurial)
* [Repositório de Git local](../app-service/deploy-local-git.md)
* [GitHub](https://github.com)
* [OneDrive](https://onedrive.live.com/)
* [DevOps do Azure](https://azure.microsoft.com/services/devops/)

As implementações são configuradas numa base por função de aplicação. Depois da implementação contínua é ativada, o acesso ao código de função no portal do estiver definido como *só de leitura*.

## <a name="requirements-for-continuous-deployment"></a>Requisitos para a implementação contínua

Antes de configurar a implementação contínua, tem de ter sua origem de implementação configurado e o código de função na origem de implementação. Uma implementação de aplicação de função, cada função é num subdiretório nomeado, onde o nome do diretório é o nome da função.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Para poder implementar a partir do Azure DevOps, deve primeiro ligar a sua organização de DevOps do Azure com a sua subscrição do Azure. Para obter mais informações, consulte [configure a cobrança para sua organização de Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/billing/set-up-billing-for-your-organization-vs?view=vsts#set-up-billing-via-the-azure-portal).

## <a name="set-up-continuous-deployment"></a>Definir implementação contínua
Utilize este procedimento para configurar a implementação contínua para uma aplicação de função existente. Estes passos demonstram a integração com um repositório do GitHub, mas os passos semelhantes são aplicáveis para DevOps do Azure ou outros serviços de implementação.

1. Na sua aplicação de função no [portal do Azure](https://portal.azure.com), selecione **recursos da plataforma** > **opções de implementação**. 
   
    ![Seleções para abrir Opções de implementação](./media/functions-continuous-deployment/setup-deployment.png)
 
1. Sobre o **implementações** painel, selecione **configuração**.
 
    ![Painel de implementações](./media/functions-continuous-deployment/setup-deployment-1.png)
   
1. Sobre o **origem de implementação** painel, selecione **Escolher origem**. Preencha as informações para a sua origem de implementação escolhido e, em seguida, selecione **OK**.
   
    ![Escolher uma origem de implementação](./media/functions-continuous-deployment/choose-deployment-source.png)

Depois de configurar a implementação contínua, todas as alterações de ficheiros na sua origem de implementação são copiadas para a aplicação de função e uma implementação completa do site é acionada. O site é reimplementado quando os ficheiros na origem são atualizados.

## <a name="deployment-scenarios"></a>Cenários de implementação

Cenários de implementação típica incluem criar uma implementação de teste e mover as funções existentes para a implementação contínua.

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>Criar uma implementação de teste

Aplicações de função ainda não suportam ranhuras de implementação. Mas pode continuar a gerir implementações de teste e produção separadas usando a integração contínua.

O processo para configurar e trabalhar com uma implementação de teste geralmente fica assim:

1. Criar duas aplicações de funções na sua subscrição: um para o código de produção e outro para teste. 

1. Crie uma origem de implementação, se ainda não tiver uma. Este exemplo utiliza [GitHub].

1. Para a sua aplicação de função de produção, complete os passos anteriores [configurar a implementação contínua](#set-up-continuous-deployment) e defina o ramo de implementação para o ramo principal do repositório do GitHub.
   
    ![Seleções para escolher um ramo de implementação](./media/functions-continuous-deployment/choose-deployment-branch.png)

1. Repita o passo 3 para a aplicação de funções de teste, mas selecione o ramo de teste em vez disso, no seu repositório do GitHub. Se a sua origem de implementação não suporta a ramificação, utilize uma pasta diferente.
    
1. Fazer atualizações ao seu código no ramo ou pasta de teste e, em seguida, certifique-se de que a implementação de teste reflita essas alterações.

1. Após os testes, merge alterações do ramo a transição no ramo principal. Esta intercalação aciona a implementação para a aplicação de função de produção. Se a sua origem de implementação não suporta ramificações, substitua os ficheiros na pasta de produção com os ficheiros da pasta de transição.

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Mover as funções existentes para a implementação contínua
Quando tem funções existentes que tenha criado e mantido no portal, tem de transferir seus arquivos de código de função utilizando o FTP ou pode configurar o repositório de Git local antes da implementação contínua, conforme descrito anteriormente. Pode fazê-lo nas definições do serviço de aplicações do Azure, para a sua aplicação de função. Depois de transferir os ficheiros, pode carregá-los à sua origem de implementação contínua escolhido.

> [!NOTE]
> Depois de configurar a integração contínua, já não é possível editar os arquivos de origem no portal de funções.

<a name="credentials"></a>
#### <a name="configure-deployment-credentials"></a>Configurar as credenciais de implementação
Pode transferir os ficheiros da sua aplicação de função através de FTP ou um repositório de Git local, tem de configurar as suas credenciais para aceder ao site. As credenciais são definidas ao nível da aplicação de função. Utilize os seguintes passos para definir as credenciais de implementação no portal do Azure:

1. Na sua aplicação de função no [portal do Azure](https://portal.azure.com), selecione **recursos da plataforma** > **credenciais de implementação**.
   
1. Introduza um nome de utilizador e palavra-passe e, em seguida, selecione **guardar**. 

   ![Seleções para definir credenciais de implementação de local](./media/functions-continuous-deployment/setup-deployment-credentials.png)

Agora, pode utilizar estas credenciais para aceder à sua aplicação de função de FTP ou o repositório de Git incorporado.

<a name="downftp"></a>
#### <a name="download-files-by-using-ftp"></a>Transferir ficheiros utilizando o FTP

1. Na sua aplicação de função no [portal do Azure](https://portal.azure.com), selecione **recursos da plataforma** > **propriedades**. Em seguida, copie os valores para **utilizador de FTP/implementação**, **nome do anfitrião FTP**, e **nome de anfitrião FTPS**.  

   **Utilizador de FTP/implementação** devem ser inseridas, tal como apresentado no portal, incluindo o nome da aplicação, para fornecer contexto adequado para o servidor FTP.
   
   ![Seleções para obter as informações de implementação](./media/functions-continuous-deployment/get-deployment-credentials.png)

1. A partir do seu cliente FTP, utilize as informações de ligação que recolheu para ligar à sua aplicação e transfira os ficheiros de origem para as suas funções.

<a name="downgit"></a>
#### <a name="download-files-by-using-a-local-git-repository"></a>Transferir ficheiros através de um repositório de Git local

1. Na sua aplicação de função no [portal do Azure](https://portal.azure.com), selecione **recursos da plataforma** > **opções de implementação**. 
   
    ![Seleções para abrir Opções de implementação](./media/functions-continuous-deployment/setup-deployment.png)
 
1. Em seguida, na **implementações** painel, selecione **configuração**.
 
    ![Painel de implementações](./media/functions-continuous-deployment/setup-deployment-1.png)
   
1. Sobre o **origem de implementação** painel, selecione **repositório de Local Git** > **OK**.

1. Na **funcionalidades de plataforma**, selecione **propriedades** e anote o valor do URL do Git. 
   
    ![Seleções para obter o URL de Git](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

1. Clone o repositório no seu computador local utilizando uma linha de comandos com suporte para o Git ou sua ferramenta favorita do Git. O comando de clonagem do Git tem esta aparência:
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

1. Obter ficheiros da sua aplicação de função para o clone no seu computador local, como no exemplo seguinte:
   
        git pull origin master
   
    Se solicitado, forneça seus [configuradas credenciais de implementação](#credentials).  

[GitHub]: https://github.com/

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
