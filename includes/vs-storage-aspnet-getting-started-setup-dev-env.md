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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184160"
---
## <a name="set-up-the-development-environment"></a>Configurar o ambiente de desenvolvimento

Esta secção descreve como configurar o ambiente de desenvolvimento. Isto inclui a criação de uma aplicação ASP.NET MVC, adicionar uma ligação de serviços ligados, adicionar um controlador e especificando as diretivas de espaço de nomes necessários.

### <a name="create-an-aspnet-mvc-app-project"></a>Criar um projeto de aplicação ASP.NET MVC

1. Abra o Visual Studio.

1. A partir do menu principal, selecione **arquivo** > **New** > **projeto**.

1. Na **novo projeto** caixa de diálogo, selecione **Web** > **aplicação de Web do ASP.NET (.NET Framework)** . Na **Name** campo, especifique **StorageAspNet**. Selecione **OK**.

    ![Captura de ecrã da caixa de diálogo Novo Projeto](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. Na **nova aplicação Web ASP.NET** caixa de diálogo, selecione **MVC**e, em seguida, selecione **OK**.

    ![Caixa de diálogo de captura de ecrã da nova aplicação Web ASP.NET](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Utilizar serviços ligados para ligar a uma conta de armazenamento do Azure

1. Na **Explorador de soluções**, com o botão direito no projeto.

1. No menu de contexto, selecione **Add** > **serviço ligado**.

1. Na **serviços ligados** caixa de diálogo, selecione **armazenamento na Cloud com o armazenamento do Azure**.

    ![Caixa de diálogo de captura de ecrã de serviços ligados](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. Na **armazenamento do Azure** caixa de diálogo, selecione o armazenamento do Azure da conta a ser utilizado para este tutorial. Para criar uma nova conta de armazenamento do Azure, selecione **criar uma nova conta de armazenamento**e preencha o formulário. Depois de selecionar a conta de armazenamento existente ou criar uma nova, selecione **adicionar**. Visual Studio instala o pacote NuGet para o armazenamento do Azure e uma cadeia de ligação de armazenamento para **Web. config**.

1. Na **Explorador de soluções**, clique com o botão direito no **dependências**, escolha **gerir pacotes NuGet**e adicione uma referência de pacote NuGet para a versão mais recente do Microsoft.Azure.ConfigurationManager.

> [!TIP]
> Para saber como criar uma conta de armazenamento com o [portal do Azure](https://portal.azure.com), consulte [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
>
> Também pode criar uma conta de armazenamento, utilizando [do Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [CLI do Azure](../articles/storage/common/storage-azure-cli.md), ou [Azure Cloud Shell](../articles/cloud-shell/overview.md).
