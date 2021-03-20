---
title: Versão da aplicação Blockchain - Azure Blockchain Workbench
description: Como utilizar versões de aplicação no Azure Blockchain Workbench Preview.
ms.date: 11/20/2019
ms.topic: how-to
ms.reviewer: brendal
ms.openlocfilehash: 2a70112fd0ab6e2f664ca48265c121936b01e58b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85209883"
---
# <a name="azure-blockchain-workbench-preview-application-versioning"></a>Versão da aplicação de pré-visualização da azure Blockchain Workbench

Pode criar e utilizar várias versões de uma aplicação de pré-visualização da bancada de trabalho Azure Blockchain. Se várias versões da mesma aplicação forem carregadas, um histórico de versão está disponível e os utilizadores podem escolher qual a versão que pretendem utilizar.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma implantação da bancada blockchain Workbench. Para obter mais informações, consulte [a implementação da bancada de workbench Azure Blockchain](deploy.md) para obter detalhes sobre a implementação
* Uma aplicação blockchain implantada na Blockchain Workbench. Ver [Criar uma aplicação blockchain na bancada Azure Blockchain Workbench](create-app.md)

## <a name="add-an-app-version"></a>Adicione uma versão de aplicativo

Para adicionar uma nova versão, faça o upload da nova configuração e dos ficheiros de contrato inteligente para blockchain Workbench.

1. Num navegador web, navegue para o endereço web blockchain Workbench. Por exemplo, `https://{workbench URL}.azurewebsites.net/` Para obter informações sobre como encontrar o seu endereço web Blockchain Workbench, consulte [o URL web da Blockchain Workbench Web](deploy.md#blockchain-workbench-web-url)
2. Inscreva-se como [administrador da Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).
3. Selecione a aplicação blockchain que pretende atualizar com outra versão.
4. Selecione **a versão Adicionar**. É apresentado o painel **de versões Add.**
5. Escolha a nova configuração do contrato de versão e os ficheiros de código contratual para carregar. O ficheiro de configuração é automaticamente validado. Corrija quaisquer erros de validação antes de implementar a aplicação.
6. **Selecione a versão Adicionar** para adicionar a nova versão da aplicação blockchain.

    ![Adicionar uma nova versão](media/version-app/add-version.png)

A implementação da aplicação blockchain pode demorar alguns minutos. Quando a implementação estiver concluída, refresque a página de aplicação. Escolhendo a aplicação e selecionando o botão **de histórico da versão,** exibe o histórico da versão da aplicação.

> [!IMPORTANT]
> As versões anteriores da aplicação estão desativadas. Pode ree capacitar individualmente versões passadas.
>
> Poderá ser necessário voltar a adicionar membros às funções de candidatura se forem feitas alterações às funções de candidatura na nova versão.

## <a name="using-app-versions"></a>Usando versões de aplicativos

Por predefinição, a versão mais recente da aplicação ativada é utilizada na Bancada Blockchain. Se pretender utilizar uma versão anterior de uma aplicação, tem de escolher a versão da página de aplicação primeiro.

1. Na secção de aplicação blockchain Workbench, selecione a caixa de verificação de aplicações que contém o contrato que pretende utilizar. Se as versões anteriores estiverem ativadas, o botão de histórico da versão está disponível.
2. Selecione o botão **de histórico versão.**
3. No painel de história da versão, escolha a versão da aplicação selecionando o link na coluna *modificada Date.*

    ![Escolha uma versão anterior](media/version-app/use-version.png)

    Pode criar novos contratos ou tomar medidas em contratos de versão anteriores. A versão da aplicação é apresentada seguindo o nome da aplicação e é apresentado um aviso sobre a versão mais antiga.

## <a name="next-steps"></a>Passos seguintes

* [Azure Blockchain Workbench resolução de problemas](troubleshooting.md)
