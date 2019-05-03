---
title: Configurar os nós de transação de serviço do Azure Blockchain
description: Como configurar nós de transação de serviço do Azure Blockchain
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: dffeb81ae1eb244c38639a1241c0581e6fcdf94a
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027964"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Configurar os nós de transação de serviço do Azure Blockchain

Para interagir com o serviço do Azure Blockchain, fazer isso por meio de ligar a um ou mais nós de transação no seu membro de blockchain.  Para interagir com os nós de transação, terá de configurar os nós para o acesso.

## <a name="prerequisites"></a>Pré-requisitos

* [Criar um membro do Azure Blockchain](create-member.md)

## <a name="transaction-node-overview"></a>Descrição geral do nó de transação

Nós de transação são utilizados para enviar transações de blockchain no serviço do Azure Blockchain por meio de um ponto final público. O nó de transação padrão contém a chave privada da conta Ethereum registrada a desenvolver o blockchain e como tal, não pode ser eliminado.

Para ver os detalhes de nó de transação de predefinição:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue para o membro de serviço do Azure Blockchain. Selecione **nós de transação**.

    ![Selecione o nó de transação de predefinição](./media/configure-transaction-nodes/nodes.png)

    Detalhes da descrição geral incluem endereços de ponto final público e a chave pública.

## <a name="create-transaction-node"></a>Criar o nó de transação

Pode adicionar até nove nós de transações adicionais para o membro de blockchain, para um total de dez nós de transação. Ao adicionar nós de transação, pode aumentar a escalabilidade ou distribuir a carga. Por exemplo, poderia ter um ponto de extremidade de nó de transação para as aplicações cliente diferentes.

Para adicionar um nó de transação:

1. No portal do Azure, navegue para o membro de serviço do Azure Blockchain e selecione **nós de transação > Adicionar**.
1. Conclua as definições para o novo nó de transação.

    ![Adicionar nó de transação](./media/configure-transaction-nodes/add-node.png)

    | Definição | Descrição |
    |---------|-------------|
    | Name | Nome de nó da transação. O nome é utilizado para criar o endereço DNS para o ponto de final do nó de transação. Por exemplo, `newnode-myblockchainmember.blockchain.azure.com`. O nome do nó não pode ser alterado depois de criado. |
    | Palavra-passe | Defina uma palavra-passe segura. Utilize a palavra-passe para aceder ao ponto de final de nó de transação com a autenticação básica.

1. Selecione **Criar**.

    Um novo nó de transação o aprovisionamento demora cerca de 10 minutos. Nós de transações adicionais incorrer em custos. Para obter mais informações sobre os custos, veja [os preços do Azure](https://aka.ms/ABSPricing).

## <a name="endpoints"></a>Pontos Finais

Nós de transação de ter um nome DNS exclusivo e pontos finais públicos.

Para ver detalhes do ponto final de um nó de transação:

1. No portal do Azure, navegue até um de nós de transação do serviço do Azure Blockchain membro e selecione **descrição geral**.

    ![Pontos Finais](./media/configure-transaction-nodes/endpoints.png)

Pontos finais de nó de transação são seguros e exigem a autenticação. Pode ligar a um ponto de extremidade de transação utilizar autenticação do Azure AD, autenticação básica de HTTPS e utilizar uma chave de acesso através de HTTPS ou Websocket através de SSL.

### <a name="azure-active-directory-access-control"></a>Controlo de acesso do Azure Active Directory

Serviço de Blockchain transação nó pontos finais do Azure suportam a autenticação do Azure Active Directory (Azure AD). Pode conceder o utilizador, grupo e acesso ao principal de serviço do Azure AD para o ponto final.

Para conceder o Azure AD do controlo de acesso para o ponto final:

1. No portal do Azure, navegue para o membro de serviço do Azure Blockchain e selecione **nós de transação > controlo de acesso (IAM) > Adicionar > Adicionar atribuição de função**.
1. Crie uma nova atribuição de função para um utilizador, grupo ou principal de serviço (funções da aplicação).

    ![Adicionar função IAM](./media/configure-transaction-nodes/add-role.png)

    | Definição | Ação |
    |---------|-------------|
    | Função | Selecione **proprietário**, **contribuinte**, ou **leitor**.
    | Atribuir acesso a | Selecione **utilizador, grupo ou principal de serviço do Azure AD**.
    | Selecione | Procure o utilizador, grupo ou principal de serviço que pretende adicionar.

1. Selecione **guardar** para adicionar a atribuição de função.

Para obter mais informações sobre o controlo de acesso do Azure AD, consulte [gerir o acesso aos recursos do Azure através do RBAC e o portal do Azure](../../role-based-access-control/role-assignments-portal.md)

Para obter detalhes sobre como ligar através de autenticação do Azure AD, consulte [ligue-se ao seu nó usando a autenticação do AAD](configure-aad.md).

### <a name="basic-authentication"></a>Autenticação básica

Para a autenticação básica de HTTPS, as credenciais de nome e palavra-passe do utilizador são passadas no cabeçalho do pedido HTTPS para o ponto final.

Pode ver detalhes do ponto final de autenticação básica de um nó de transação no portal do Azure. Navegue até um de nós de transação do serviço do Azure Blockchain membro e selecione **autenticação básica** nas definições.

![Autenticação básica](./media/configure-transaction-nodes/basic.png)

O nome de utilizador é o nome do seu nó e não pode ser alterado.

Para utilizar o URL, substitua \<palavra-passe\> com a palavra-passe definida quando o nó foi aprovisionado. Pode atualizar a palavra-passe, selecionando **Repor palavra-passe**.

### <a name="access-keys"></a>Chaves de acesso

Para autenticação de chave de acesso, a chave de acesso está incluída no URL do ponto final. Quando o nó de transação é aprovisionado, são geradas duas chaves de acesso. Nenhuma das chaves de acesso pode ser utilizado para autenticação. Ativação de duas chaves alterar e Rodar chaves.

Pode ver os detalhes de chave de acesso de um nó de transação e copiar os endereços de ponto final que incluem as chaves de acesso. Navegue até um de nós de transação do serviço do Azure Blockchain membro e selecione **chaves de acesso** nas definições.

### <a name="firewall-rules"></a>Regras da firewall

Regras de firewall permitem-lhe limitar os endereços IP que podem tentar autenticar para o nó de transação.  Se não existem regras de firewall estão configuradas para o seu nó de transação, não pode ser acedido por nenhuma parte.  

Para ver as regras de firewall de um nó de transação, navegue até um de nós de transação do serviço do Azure Blockchain membro e selecione **regras de Firewall** nas definições.

Pode adicionar regras de firewall ao introduzir um nome de regra, a partir de IP endereço e um final de endereços IP na **regras de Firewall** grid.

![Regras da firewall](./media/configure-transaction-nodes/firewall-rules.png)

Para ativar:

* **Endereço IP único:** Configure o mesmo endereço IP para endereços IP de início e fim.
* **Intervalo de endereços IP:** Configure o intervalo de endereços IP de início e fim. Por exemplo, um intervalo começando 10.221.34.0 e terminando no 10.221.34.255 permitiria a sub-rede 10.221.34.xxx inteiro.
* **Permita todos os endereços IP:** Configure o endereço IP inicial como 0.0.0.0 e o endereço IP final para 255.255.255.255.

## <a name="connection-strings"></a>Cadeias de ligação

A sintaxe da cadeia de ligação para o seu nó de transação é fornecido para o básico de autenticação ou com chaves de acesso. Cadeias de ligação, incluindo as chaves de acesso através de HTTPS e WebSockets são fornecidas.

Pode ver cadeias de ligação de um nó de transação e copiar os endereços de ponto final. Navegue até um de nós de transação do serviço do Azure Blockchain membro e selecione **cadeias de ligação** nas definições.

![Cadeias de ligação](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Código de exemplo

Código de exemplo é fornecido para ativar rapidamente a ligar ao seu nó de transação por meio de Web3, Nethereum, Web3js e Truffle.

Pode ver o código de ligação de exemplo de um nó de transação e copie-o para utilizar com ferramentas populares para programadores. Navegue até um de nós de transação do serviço do Azure Blockchain membro e selecione **código de exemplo** nas definições.

Escolha o separador de Web3 ou Nethereum para ver o código de exemplo que pretende utilizar.

![Código de exemplo](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Configurar nós de transação com a CLI do Azure](manage-cli.md)
