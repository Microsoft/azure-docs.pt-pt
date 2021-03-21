---
title: Configurar os nosdes de transação do Serviço Blockchain Azure
description: Como configurar os nóns de transação do Serviço Azure Blockchain
ms.date: 11/20/2019
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: 0b659fa9f15cd41e9e55de526747d470046cafa4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101091268"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Configurar os nosdes de transação do Serviço Blockchain Azure

Os nós de transações são usados para enviar transações blockchain para o Azure Blockchain Service através de um ponto final público. O nó de transação padrão contém a chave privada da conta Ethereum registada na blockchain e, como tal, não pode ser eliminada.

Para ver os detalhes do nó de transação predefinido:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue para o seu membro do Serviço Azure Blockchain. Selecione **nós de transação**.

    ![Selecione o nó de transação padrão](./media/configure-transaction-nodes/nodes.png)

    Os detalhes da visão geral incluem endereços de ponto final público e chave pública.

## <a name="create-transaction-node"></a>Criar nó de transação

Pode adicionar até nove nós de transação adicionais ao seu membro blockchain, num total de 10 nós de transação. Ao adicionar nós de transação, pode aumentar a escalabilidade ou distribuir carga. Por exemplo, pode ter um ponto final de nó de transação para diferentes aplicações do cliente.

Para adicionar um nó de transação:

1. No portal Azure, navegue para o seu membro do Serviço Azure Blockchain e selecione **nós de transação > Add**.
1. Complete as definições do novo nó de transação.

    ![Adicionar nó de transação](./media/configure-transaction-nodes/add-node.png)

    | Definições | Descrição |
    |---------|-------------|
    | Nome | Nome do nó de transação. O nome é usado para criar o endereço DNS para o ponto final do nó de transação. Por exemplo, `newnode-myblockchainmember.blockchain.azure.com`. O nome do nó não pode ser alterado uma vez que é criado. |
    | Palavra-passe | Estabeleça uma senha forte. Utilize a palavra-passe para aceder ao ponto final do nó de transação com autenticação básica.

1. Selecione **Criar**.

    O fornecimento de um novo nó de transação demora cerca de 10 minutos. Os nós de transação adicionais incorrem no custo. Para obter mais informações sobre os custos, consulte [os preços do Azure.](https://aka.ms/ABSPricing)

## <a name="endpoints"></a>Pontos Finais

Os nós de transação têm um nome DNS único e pontos finais públicos.

Para ver os detalhes do ponto final de um nó de transação:

1. No portal Azure, navegue para um dos seus nós de transação do Azure Blockchain Service e selecione **a Visão Geral**.

    ![A captura de ecrã mostra a visão geral dos nós de transação para um membro blockchain.](./media/configure-transaction-nodes/endpoints.png)

Os pontos finais do nó de transação são seguros e requerem autenticação. Pode ligar-se a um ponto final de transação utilizando a autenticação Azure AD, a autenticação básica HTTPS e a utilização de uma chave de acesso sobre HTTPS ou Websocket sobre TLS.

### <a name="azure-active-directory-access-control"></a>Controlo de acesso ao Diretório Ativo Azure

Os pontos finais de transação do Serviço Azure Blockchain suportam a autenticação do Azure Ative Directory (Azure AD). Pode conceder ao utilizador, grupo e serviço de acesso principal da Azure AD ao seu ponto final.

Para conceder ao Azure AD o controlo de acesso ao seu ponto final:

1. No portal Azure, navegue para o seu membro do Serviço Azure Blockchain e selecione **os nós de Transação > Controlo de Acesso (IAM) > Adicionar > atribuição de função**.
1. Criar uma nova atribuição de funções para um utilizador, grupo ou principal de serviço (funções de aplicação).

    ![Adicionar papel IAM](./media/configure-transaction-nodes/add-role.png)

    | Definição | Ação |
    |---------|-------------|
    | Função | Selecione **Proprietário,** **Contribuinte** ou **Leitor**.
    | Atribuir acesso a | Selecione **O utilizador, grupo ou principal de serviço da Azure.**
    | Selecione | Procure o utilizador, grupo ou o principal de serviço que pretende adicionar.

1. **Selecione Guardar** para adicionar a atribuição de funções.

Para obter mais informações sobre o controlo de acesso a Azure, consulte [as funções de Atribuição Azure utilizando o portal Azure](../../role-based-access-control/role-assignments-portal.md)

Para obter mais informações sobre como ligar usando a autenticação AZure AD, consulte [a ligação ao seu nó utilizando a autenticação AAD](configure-aad.md).

### <a name="basic-authentication"></a>Autenticação básica

Para a autenticação básica HTTPS, o nome de utilizador e as credenciais de palavra-passe são passados no cabeçalho HTTPS do pedido para o ponto final.

Pode ver os detalhes básicos do ponto final de autenticação de um nó de transação no portal Azure. Navegue para um dos nós de transação do seu membro do Serviço Azure Blockchain e selecione **a Autenticação Básica** em definições.

![Autenticação básica](./media/configure-transaction-nodes/basic.png)

O nome de utilizador é o nome do seu nó e não pode ser alterado.

Para utilizar o URL, \<password\> substitua-o pelo conjunto de palavra-passe quando o nó foi provisionado. Pode atualizar a palavra-passe selecionando a **palavra-passe Reset**.

### <a name="access-keys"></a>Chaves de acesso

Para a autenticação da chave de acesso, a chave de acesso está incluída no URL do ponto final. Quando o nó de transação é provisionado, duas teclas de acesso são geradas. Qualquer uma das teclas de acesso pode ser utilizada para a autenticação. Duas teclas permitem-lhe alterar e rodar as teclas.

Pode ver os principais detalhes de acesso de um nó de transação e copiar endereços finais que incluem as teclas de acesso. Navegue para um dos nós de transação do seu membro do Azure Blockchain Service e selecione **As Chaves de Acesso** em definições.

### <a name="firewall-rules"></a>Regras da firewall

As regras de firewall permitem-lhe limitar os endereços IP que podem tentar autenticar o seu nó de transação.  Se não houver regras de firewall configuradas para o seu nó de transação, não pode ser acedido por nenhuma parte.  

Para ver as regras de firewall de um nó de transação, navegue para um dos nós de transação do seu membro do Azure Blockchain Service e selecione **regras de Firewall** em definições.

Pode adicionar regras de firewall introduzindo um nome de regra, o endereço IP inicial e um endereço IP final na grelha de regras de **Firewall.**

![Regras da firewall](./media/configure-transaction-nodes/firewall-rules.png)

Para ativar:

* **Endereço IP único:** Configure o mesmo endereço IP para os endereços IP inicio e final.
* **Intervalo de endereços IP:** Configure o intervalo de endereço IP inicial e final. Por exemplo, um intervalo a partir de 10.221.34.0 e terminando em 10.221.34.255 permitiria toda a sub-rede 10.221.34.xxx.
* **Permitir todos os endereços IP:** Configure o endereço IP inicial para 0.0.0.0 e o endereço IP final para 255.255.255.255.255.

## <a name="connection-strings"></a>Cadeias de ligação

A sintaxe de fio de ligação para o nó de transação é fornecida para autenticação básica ou usando teclas de acesso. São fornecidas cadeias de ligação, incluindo teclas de acesso sobre HTTPS e WebSockets.

Pode ver as cordas de ligação de um nó de transação e copiar endereços de ponto final. Navegue para um dos nós de transação do seu membro do Azure Blockchain Service e selecione **cordas de ligação** em definições.

![Cadeias de ligação](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Código de exemplo

O código de amostra é fornecido para permitir a ligação rapidamente ao seu nó de transação via Web3, Nethereum, Web3js e Truffle.

Pode ver o código de ligação de amostra de um nó de transação e copiá-lo para ser usado com ferramentas de desenvolvimento populares. Aceda a um dos nós de transação do seu membro do Serviço Azure Blockchain e selecione **o Código de Amostra** nas definições.

Escolha o separador Web3, Nethereum, Truffle ou Web3j para ver a amostra de código que pretende utilizar.

![Código de exemplo](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configurar os nosdes de transação utilizando o Azure CLI](manage-cli.md)
