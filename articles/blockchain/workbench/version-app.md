---
title: Controle de versão do aplicativo Blockchain – Azure Blockchain Workbench
description: Como usar versões de aplicativo na visualização do Azure Blockchain Workbench.
ms.date: 11/20/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 45219790cf0cd064e0fcd456e262b2f93aa03ac9
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74323918"
---
# <a name="azure-blockchain-workbench-preview-application-versioning"></a>Versão prévia do aplicativo do Azure Blockchain Workbench

Você pode criar e usar várias versões de um aplicativo de visualização do Azure Blockchain Workbench. Se várias versões do mesmo aplicativo forem carregadas, um histórico de versão estará disponível e os usuários poderão escolher qual versão deseja usar.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma implantação do Blockchain Workbench. Para obter mais informações, consulte [implantação do Azure Blockchain Workbench](deploy.md) para obter detalhes sobre a implantação
* Um aplicativo blockchain implantado no Blockchain Workbench. Consulte [criar um aplicativo blockchain no Azure Blockchain Workbench](create-app.md)

## <a name="add-an-app-version"></a>Adicionar uma versão do aplicativo

Para adicionar uma nova versão, carregue os novos arquivos de configuração e de contrato inteligente no Blockchain Workbench.

1. Em um navegador da Web, navegue até o endereço Web do Blockchain Workbench. Por exemplo, `https://{workbench URL}.azurewebsites.net/` para obter informações sobre como encontrar seu endereço Web do Blockchain Workbench, consulte [URL da Web do Blockchain Workbench](deploy.md#blockchain-workbench-web-url)
2. Entre como administrador do [Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).
3. Selecione o aplicativo blockchain que você deseja atualizar com outra versão.
4. Selecione **Adicionar versão**. O painel **Adicionar versão** é exibido.
5. Escolha a nova configuração de contrato de versão e os arquivos de código de contrato para carregar. O arquivo de configuração é validado automaticamente. Corrija os erros de validação antes de implantar o aplicativo.
6. Selecione **Adicionar versão** para adicionar a nova versão do aplicativo blockchain.

    ![Adicionar uma nova versão](media/version-app/add-version.png)

A implantação do aplicativo blockchain pode levar alguns minutos. Quando a implantação for concluída, atualize a página do aplicativo. Escolher o aplicativo e selecionar o botão **histórico de versão** exibe o histórico de versão do aplicativo.

> [!IMPORTANT]
> As versões anteriores do aplicativo estão desabilitadas. Você pode reabilitar individualmente versões anteriores.
>
> Talvez seja necessário adicionar novamente os membros às funções do aplicativo se forem feitas alterações nas funções do aplicativo na nova versão.

## <a name="using-app-versions"></a>Usando versões de aplicativo

Por padrão, a versão mais recente habilitada do aplicativo é usada no Blockchain Workbench. Se você quiser usar uma versão anterior de um aplicativo, primeiro será necessário escolher a versão na página do aplicativo.

1. Na seção aplicativo do Blockchain Workbench, marque a caixa de seleção aplicativo que contém o contrato que você deseja usar. Se as versões anteriores estiverem habilitadas, o botão histórico de versão estará disponível.
2. Selecione o botão **histórico de versão** .
3. No painel Histórico de versão, escolha a versão do aplicativo selecionando o link na coluna *data de modificação* .

    ![Escolher uma versão anterior](media/version-app/use-version.png)

    Você pode criar novos contratos ou executar ações em contratos de versão anteriores. A versão do aplicativo é exibida seguindo o nome do aplicativo e um aviso é exibido sobre a versão mais antiga.

## <a name="next-steps"></a>Passos seguintes

* [Solução de problemas do Azure Blockchain Workbench](troubleshooting.md)
