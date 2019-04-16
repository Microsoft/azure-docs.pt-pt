---
title: Controlo de versões de aplicação de Blockchain no Azure Blockchain Workbench
description: Como utilizar as versões da aplicação no Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 04/15/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 63f18e3ee316b9791bb62bfcd20c07a30cbebb5e
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2019
ms.locfileid: "59579221"
---
# <a name="azure-blockchain-workbench-application-versioning"></a>Controlo de versões de aplicações do Azure Blockchain Workbench

Pode criar e utilizar várias versões de uma aplicação do Azure Blockchain Workbench. Se forem carregadas várias versões do mesmo aplicativo, um histórico de versão está disponível e os utilizadores possam escolher qual é a versão que deseja usar.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma implementação de Blockchain Workbench. Para obter mais informações, consulte [implementação do Azure Blockchain Workbench](deploy.md) para obter detalhes sobre a implementação
* Uma aplicação de blockchain implementado na bancada de trabalho de Blockchain. Consulte [criar uma aplicação de blockchain no Azure Blockchain Workbench](create-app.md)

## <a name="add-an-app-version"></a>Adicionar uma versão de aplicação

Para adicionar uma nova versão, carregar os ficheiros de contrato inteligente e a nova configuração para o Blockchain Workbench.

1. Num browser, navegue para o endereço de web de Blockchain Workbench. Por exemplo, `https://{workbench URL}.azurewebsites.net/` para obter informações sobre como encontrar seu Blockchain Workbench endereço da web, consulte [URL de Web do Blockchain Workbench](deploy.md#blockchain-workbench-web-url)
2. Inicie sessão como um [administrador Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).
3. Selecione a aplicação de blockchain que pretende atualizar com outra versão.
4. Selecione **adicionar versão**. O **adicionar versão** é apresentado o painel.
5. Escolha a nova configuração de contrato de versão e de contrato de arquivos de código para carregar. O ficheiro de configuração é validado automaticamente. Corrija os erros de validação antes de implementar a aplicação.
6. Selecione **adicionar versão** para adicionar a nova versão de aplicação de blockchain.

    ![Adicionar uma nova versão](media/version-app/add-version.png)

Implementação da aplicação de blockchain pode demorar alguns minutos. Quando a implementação estiver concluída, atualize a página de aplicativo. Escolher o aplicativo e selecionando o **histórico de versões** botão, que mostra o histórico de versão do aplicativo.

> [!IMPORTANT]
> Versões anteriores da aplicação estão desativadas. Pode individualmente reativar as versões anteriores.
>
> Terá de voltar a adicionar membros a funções de aplicação se foram feitas alterações para as funções de aplicação na nova versão.

## <a name="using-app-versions"></a>Usar as versões da aplicação

Por predefinição, a versão mais recente ativada da aplicação é utilizada na bancada de trabalho de Blockchain. Se pretender utilizar uma versão anterior de uma aplicação, tem de escolher a versão da página de aplicativo em primeiro lugar.

1. Na secção de aplicações de Blockchain Workbench, selecione a caixa de verificação de aplicação que contém o contrato que pretende utilizar. Se as versões anteriores estiverem ativadas, o botão de histórico de versão está disponível.
2. Selecione o **histórico de versões** botão.
3. No painel do histórico de versão, escolha a versão da aplicação ao selecionar a ligação no *data de modificação* coluna.

    ![Escolha uma versão anterior](media/version-app/use-version.png)

    Pode criar novos contratos ou agir sobre contratos de versão anterior. A versão da aplicação é apresentada a seguir o nome da aplicação e é apresentado um aviso sobre a versão mais antiga.

## <a name="next-steps"></a>Passos Seguintes

* [O Azure Blockchain Workbench, resolução de problemas](troubleshooting.md)
