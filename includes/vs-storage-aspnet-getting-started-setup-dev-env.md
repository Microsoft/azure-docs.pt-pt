---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a7c696870e22e1692ca5ed778e47f8e4cc00615a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67184160"
---
## <a name="set-up-the-development-environment"></a>Configurar o ambiente de desenvolvimento

Esta secção passa pela criação do ambiente de desenvolvimento. Isto inclui a criação de uma aplicação ASP.NET MVC, a adição de uma ligação de serviços conectados, a adição de um controlador e a especificação das diretivas de espaço de nome necessários.

### <a name="create-an-aspnet-mvc-app-project"></a>Criar um ASP.NET projeto de aplicação MVC

1. Abra o Visual Studio.

1. A partir do menu principal, selecione **File** > **New** > **Project**.

1. Na caixa de diálogo **New Project,** selecione **Web** > **ASP.NET Web Application (.NET Framework)**. No campo **Nome,** especifique **StorageAspNet**. Selecione **OK**.

    ![Captura de ecrã da caixa de diálogo Novo Projeto](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. Na caixa de diálogo **New ASP.NET Web Application,** selecione **MVC**, e, em seguida, selecione **OK**.

    ![Screenshot da caixa de diálogo de aplicação web de nova ASP.NET](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Utilize serviços conectados para se conectar a uma conta de armazenamento Azure

1. No **Solution Explorer,** clique no projeto.

1. A partir do menu de contexto, selecione **Adicionar** > **Serviço Conectado**.

1. Na caixa de diálogo **Serviços Conectados,** selecione **Cloud Storage com Armazenamento Azure**.

    ![Screenshot da caixa de diálogo de serviços conectados](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. Na caixa de diálogo **Azure Storage,** selecione a conta de armazenamento Azure a utilizar para este tutorial. Para criar uma nova conta de armazenamento Azure, selecione **Criar uma Nova Conta**de Armazenamento e preencher o formulário. Depois de selecionar uma conta de armazenamento existente ou criar uma nova, **selecione Adicionar**. O Visual Studio instala o pacote NuGet para armazenamento azure e uma cadeia de ligação de armazenamento à **Web.config**.

1. No **Solution Explorer**, clique à direita em **Dependências,** escolha Gerir **pacotes NuGet**e adicione uma referência de pacote NuGet à versão mais recente do Microsoft.Azure.ConfigurationManager.

> [!TIP]
> Para aprender a criar uma conta de armazenamento com o [portal Azure,](https://portal.azure.com)consulte [Criar uma conta](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)de armazenamento .
>
> Também pode criar uma conta de armazenamento utilizando [o Azure PowerShell,](../articles/storage/common/storage-powershell-guide-full.md) [o Azure CLI](../articles/storage/common/storage-azure-cli.md)ou [o Azure Cloud Shell.](../articles/cloud-shell/overview.md)
