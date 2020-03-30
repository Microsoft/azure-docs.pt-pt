---
title: Versão de aplicativoblockchain - Bancada de trabalho Azure Blockchain
description: Como utilizar versões de aplicação na Pré-visualização da bancada de trabalho do Azure Blockchain.
ms.date: 11/20/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 45219790cf0cd064e0fcd456e262b2f93aa03ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74323918"
---
# <a name="azure-blockchain-workbench-preview-application-versioning"></a>Versão da aplicação de pré-visualização da bancada de trabalho Azure Blockchain

Pode criar e utilizar várias versões de uma aplicação de pré-visualização da bancada de trabalho Do Blockchain. Se várias versões da mesma aplicação forem carregadas, um histórico de versão está disponível e os utilizadores podem escolher qual a versão que pretendem utilizar.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma implantação da bancada blockchain. Para mais informações, consulte a implementação da [bancada de trabalho Azure Blockchain](deploy.md) para obter detalhes sobre a implementação
* Uma aplicação blockchain implantada na Bancada de Trabalho blockchain. Ver [Criar uma aplicação blockchain na bancada azure blockchain](create-app.md)

## <a name="add-an-app-version"></a>Adicione uma versão de aplicativo

Para adicionar uma nova versão, carregue a nova configuração e os ficheiros de contrato inteligentes para blockchain Workbench.

1. Num navegador web, navegue para o endereço web blockchain Workbench. Por exemplo, `https://{workbench URL}.azurewebsites.net/` Para obter informações sobre como encontrar o seu endereço web blockchain Workbench, consulte [Blockchain Workbench Web URL](deploy.md#blockchain-workbench-web-url)
2. Inscreva-se como administrador da [bancada blockchain.](manage-users.md#manage-blockchain-workbench-administrators)
3. Selecione a aplicação blockchain que pretende atualizar com outra versão.
4. Selecione **versão Adicionar**. O painel da **versão Add** é apresentado.
5. Escolha a configuração do contrato da nova versão e os ficheiros de código de contrato para carregar. O ficheiro de configuração é validado automaticamente. Corrija quaisquer erros de validação antes de implementar a aplicação.
6. Selecione **adicionar versão** para adicionar a nova versão da aplicação blockchain.

    ![Adicionar uma nova versão](media/version-app/add-version.png)

A implementação da aplicação blockchain pode demorar alguns minutos. Quando a implementação estiver concluída, refresque a página de aplicação. Escolhendo a aplicação e selecionando o botão de histórico da **versão,** apresenta o histórico da versão da aplicação.

> [!IMPORTANT]
> As versões anteriores da aplicação são desativadas. Pode reativar individualmente versões passadas.
>
> Poderá ter de readicionar os membros às funções de candidatura se forem introduzidas alterações nas funções de aplicação na nova versão.

## <a name="using-app-versions"></a>Usando versões de aplicativos

Por padrão, a versão mais recente ativada da aplicação é utilizada na Bancada de Trabalho blockchain. Se pretender utilizar uma versão anterior de uma aplicação, tem de escolher a versão a partir da página de aplicação primeiro.

1. Na secção de aplicação blockchain Workbench, selecione a caixa de verificação de aplicações que contém o contrato que pretende utilizar. Se as versões anteriores estiverem ativadas, o botão de histórico da versão está disponível.
2. Selecione o botão histórico da **versão.**
3. No painel histórico da versão, escolha a versão da aplicação selecionando o link na coluna *modificada Data.*

    ![Escolha uma versão anterior](media/version-app/use-version.png)

    Pode criar novos contratos ou tomar medidas em contratos de versão anterior. A versão da aplicação é apresentada seguindo o nome da aplicação e é apresentado um aviso sobre a versão mais antiga.

## <a name="next-steps"></a>Passos seguintes

* [Tiroteio na bancada azure Blockchain](troubleshooting.md)
