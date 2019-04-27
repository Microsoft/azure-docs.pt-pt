---
title: Implementação contínua para funções do Azure | Documentos da Microsoft
description: Utilize os recursos de implementação contínua do serviço de aplicações do Azure para publicar as suas funções do Azure.
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
ms.openlocfilehash: fd8fa690c508b8bf748490668c1e9aaa811ac247
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731298"
---
# <a name="continuous-deployment-for-azure-functions"></a>Implementação contínua para Funções do Azure
As funções do Azure torna mais fácil de implementar a sua aplicação de função a utilizar a integração contínua do serviço de aplicações. Funções integra-se com o BitBucket, Dropbox, GitHub e do Azure DevOps. Isto permite que um fluxo de trabalho que onde o código de função atualizações feito através de um desses implantação de Acionador de serviços integrados no Azure. Se estiver familiarizado com as funções do Azure, comece com [descrição geral das funções do Azure](functions-overview.md).

A implementação contínua é uma excelente opção para projetos em que várias e frequentes contribuições estão a ser integradas. Ele também permite manter o controlo de origem no seu código de funções. Atualmente são suportadas as seguintes origens de implementação:

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://www.dropbox.com/)
* Repositório externo (Git ou Mercurial)
* [Repositório de Git local](../app-service/deploy-local-git.md)
* [GitHub](https://github.com)
* [OneDrive](https://onedrive.live.com/)
* [DevOps do Azure](https://azure.microsoft.com/services/devops/)

As implementações são configuradas numa base por função de aplicação. Depois da implementação contínua é ativada, o acesso ao código de função no portal do estiver definido como *só de leitura*.

## <a name="continuous-deployment-requirements"></a>Requisitos de implementação contínua

Tem de ter sua origem de implementação configurado e o código de funções na origem de implementação antes de configurar a implementação contínua. Numa implementação de aplicação de função especificada, cada função reside num subdiretório nomeado, onde o nome do diretório é o nome da função.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Para poder implementar a partir do Azure DevOps, deve primeiro ligar a sua organização de DevOps do Azure com a sua subscrição do Azure. Para obter mais informações, consulte [configure a cobrança para sua organização de Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/billing/set-up-billing-for-your-organization-vs?view=vsts#set-up-billing-via-the-azure-portal).

## <a name="set-up-continuous-deployment"></a>Definir implementação contínua
Utilize este procedimento para configurar a implementação contínua para uma aplicação de função existente. Estes passos demonstram a integração com um repositório do GitHub, mas os passos semelhantes são aplicáveis para DevOps do Azure ou outros serviços de implementação.

1. Na sua aplicação de função no [portal do Azure](https://portal.azure.com), clique em **recursos da plataforma** e **opções de implementação**. 
   
    ![Configurar a implementação contínua](./media/functions-continuous-deployment/setup-deployment.png)
 
2. Em seguida, no **implementações** painel, clique em **configuração**.
 
    ![Configurar a implementação contínua](./media/functions-continuous-deployment/setup-deployment-1.png)
   
3. Na **origem de implementação** painel, clique em **Escolher origem**, em seguida, preencha as informações para a sua origem de implementação escolhido e clique em **OK**.
   
    ![Escolher origem de implementação](./media/functions-continuous-deployment/choose-deployment-source.png)

Após a configuração da implementação contínua, todas as alterações de ficheiros na sua origem de implementação são copiadas para a aplicação de função e uma implementação completa do site é acionada. O site é reimplementado quando os ficheiros na origem são atualizados.

## <a name="deployment-options"></a>Opções de implementação

Seguem-se alguns cenários típicos de implantação:

- [Criar uma implementação de teste](#staging)
- [Mover as funções existentes para a implementação contínua](#existing)

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>Criar uma implementação de teste

Aplicações de função ainda não suportam ranhuras de implementação. No entanto, ainda pode gerir implementações de teste e produção separadas ao utilizar a integração contínua.

O processo para configurar e trabalhar com uma implementação de teste geralmente fica assim:

1. Crie duas aplicações de funções na sua subscrição, um para o código de produção e outro para teste. 

2. Crie uma origem de implementação, se ainda não tiver uma. Este exemplo utiliza [GitHub].

3. Para a sua aplicação de função de produção, complete os passos anteriores **configurar a implementação contínua** e defina o ramo de implementação para o ramo principal do repositório do GitHub.
   
    ![Escolher ramo de implementação](./media/functions-continuous-deployment/choose-deployment-branch.png)

4. Repita este passo para a aplicação de funções de teste, mas selecione o ramo de teste em vez disso, no seu repositório do GitHub. Se a sua origem de implementação não suporta a ramificação, utilize uma pasta diferente.
    
5. Fazer atualizações ao seu código no ramo ou pasta de transição, em seguida, certifique-se de que essas alterações são refletidas na implementação de teste.

6. Após os testes, merge alterações do ramo a transição no ramo principal. Esta intercalação aciona a implementação para a aplicação de função de produção. Se a sua origem de implementação não suporta ramificações, substitua os ficheiros na pasta de produção com os ficheiros da pasta de transição.

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Mover as funções existentes para a implementação contínua
Quando tem funções existentes que foi criado e mantido no portal, tem de transferir os ficheiros de código de função existente com o FTP ou pode configurar o repositório de Git local antes da implementação contínua, conforme descrito acima. Pode fazê-lo nas definições do serviço de aplicações, para a sua aplicação de função. Depois de transferir os ficheiros, pode carregá-los à sua origem de implementação contínua escolhido.

> [!NOTE]
> Depois de configurar a integração contínua, já não será capaz de editar os arquivos de origem no portal de funções.

- [How to: Configurar as credenciais de implementação](#credentials)
- [How to: Transferir os ficheiros com FTP](#downftp)
- [How to: Baixar os arquivos usando o repositório de Git local](#downgit)

<a name="credentials"></a>
#### <a name="how-to-configure-deployment-credentials"></a>Como: Configurar as credenciais de implementação
Pode transferir ficheiros da sua aplicação de função com o FTP ou o repositório de Git local, tem de configurar as suas credenciais para aceder ao site. As credenciais são definidas ao nível da aplicação de função. Utilize os seguintes passos para definir as credenciais de implementação no portal do Azure:

1. Na sua aplicação de função no [portal do Azure](https://portal.azure.com), clique em **recursos da plataforma** e **credenciais de implementação**.
   
    ![Definir credenciais de implementação de local](./media/functions-continuous-deployment/setup-deployment-credentials.png)

2. Escreva um nome de utilizador e palavra-passe, em seguida, clique em **guardar**. Agora, pode utilizar estas credenciais para aceder à sua aplicação de função de FTP ou o repositório de Git incorporado.

<a name="downftp"></a>
#### <a name="how-to-download-files-using-ftp"></a>Como: Transferir os ficheiros com FTP

1. Na sua aplicação de função no [portal do Azure](https://portal.azure.com), clique em **recursos da plataforma** e **propriedades**, em seguida, copie os valores de **utilizador de FTP/implementação**, **Nome de anfitrião FTP**, e **nome de anfitrião FTPS**.  

    **Utilizador de FTP/implementação** devem ser inseridas, tal como apresentado no portal, incluindo o nome da aplicação, para fornecer contexto adequado para o servidor FTP.
   
    ![Obter as informações de implementação](./media/functions-continuous-deployment/get-deployment-credentials.png)

2. A partir do seu cliente FTP, utilize as informações de ligação que recolheu para ligar à sua aplicação e transfira os ficheiros de origem para as suas funções.

<a name="downgit"></a>
#### <a name="how-to-download-files-using-a-local-git-repository"></a>Como: Transferir ficheiros através de um repositório de Git local

1. Na sua aplicação de função no [portal do Azure](https://portal.azure.com), clique em **recursos da plataforma** e **opções de implementação**. 
   
    ![Configurar a implementação contínua](./media/functions-continuous-deployment/setup-deployment.png)
 
2. Em seguida, no **implementações** painel, clique em **configuração**.
 
    ![Configurar a implementação contínua](./media/functions-continuous-deployment/setup-deployment-1.png)
   
2. Na **origem de implementação** painel, clique em **repositório de Local Git** e, em seguida, clique em **OK**.

3. Na **funcionalidades de plataforma**, clique em **propriedades** e anote o valor de URL do Git. 
   
    ![Configurar a implementação contínua](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

4. Clone o repositório no seu computador local utilizando uma linha de comandos com suporte para o Git ou sua ferramenta favorita do Git. O comando de clonagem do Git tem esta aparência:
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

5. Obter ficheiros da sua aplicação de função para o clone no seu computador local, como no exemplo seguinte:
   
        git pull origin master
   
    Se solicitado, forneça seus [configuradas credenciais de implementação](#credentials).  

[GitHub]: https://github.com/

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
