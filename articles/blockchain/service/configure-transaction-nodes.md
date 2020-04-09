---
title: Configure Azure Blockchain Service nódosos de transação
description: Como configurar os nódosos de transação do Serviço Blockchain Azure
ms.date: 11/20/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: 8fa18496d0c1aa59beb55569e731967d5ebea427
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876934"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Configure Azure Blockchain Service nódosos de transação

Os nódosos de transação são usados para enviar transações blockchain para o Serviço Blockchain Azure através de um ponto final público. O nó de transação predefinido contém a chave privada da conta Ethereum registada na blockchain, e como tal não pode ser eliminada.

Para ver os detalhes do nó de transação por defeito:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue para o seu membro do Serviço Azure Blockchain. Selecione **os nódosos de transação**.

    ![Selecione o nó de transações por defeito](./media/configure-transaction-nodes/nodes.png)

    Os detalhes da visão geral incluem endereços finais públicos e chave pública.

## <a name="create-transaction-node"></a>Criar nó de transação

Pode adicionar até nove nós de transação adicionais ao seu membro blockchain, num total de 10 nós de transação. Adicionando nós de transação, pode aumentar a escalabilidade ou distribuir carga. Por exemplo, você poderia ter um ponto final de nó de transação para diferentes aplicações de clientes.

Para adicionar um nó de transação:

1. No portal Azure, navegue para o seu membro do Serviço Azure Blockchain e selecione os nós de **transação > Add**.
1. Preencha as definições para o novo nó de transações.

    ![Adicionar nó de transação](./media/configure-transaction-nodes/add-node.png)

    | Definição | Descrição |
    |---------|-------------|
    | Nome | Nome do nó de transações. O nome é usado para criar o endereço DNS para o ponto final do nó de transação. Por exemplo, `newnode-myblockchainmember.blockchain.azure.com`. O nome do nó não pode ser alterado uma vez que é criado. |
    | Palavra-passe | Detete uma senha forte. Utilize a palavra-passe para aceder ao ponto final do nó de transação com autenticação básica.

1. Selecione **Criar**.

    Providenciar um novo nó de transações leva cerca de 10 minutos. Os nódos de transação adicionais incorrem em custos. Para obter mais informações sobre os custos, consulte [os preços do Azure.](https://aka.ms/ABSPricing)

## <a name="endpoints"></a>Pontos Finais

Os nódosos de transações têm um nome dNS único e pontos finais públicos.

Para ver os detalhes finais de um nó de transações:

1. No portal Azure, navegue para um dos seus nós de transação do Serviço Azure Blockchain e selecione **Overview**.

    ![Pontos Finais](./media/configure-transaction-nodes/endpoints.png)

Os pontos finais do nó de transação são seguros e requerem autenticação. Pode ligar-se a um ponto final de transação utilizando a autenticação Azure AD, autenticação básica HTTPS e utilizando uma chave de acesso através de HTTPS ou Websocket através de TLS.

### <a name="azure-active-directory-access-control"></a>Controlo de acesso ao Diretório Ativo Azure

O nó de transações do Azure Blockchain Service suporta a autenticação do Azure Ative Directory (Azure AD). Pode conceder ao utilizador, grupo e ao serviço do Azure AD o acesso principal ao seu ponto final.

Para conceder o controlo de acesso da AD Azure ao seu ponto final:

1. No portal Azure, navegue para o seu membro do Serviço Blockchain Azure e selecione os nós de transação > controlo de **acesso (IAM) > Adicionar > Adicionar a atribuição de funções**.
1. Criar uma nova atribuição de funções para um utilizador, grupo ou diretor de serviço (funções de aplicação).

    ![Adicionar papel IAM](./media/configure-transaction-nodes/add-role.png)

    | Definição | Ação |
    |---------|-------------|
    | Função | Selecione **Proprietário,** **Colaborador**ou **Leitor**.
    | Atribuir acesso a | Selecione utilizador, grupo ou diretor de **serviço Azure AD**.
    | Selecione | Procure o utilizador, grupo ou diretor de serviço que pretende adicionar.

1. Selecione **Guardar** para adicionar a atribuição de funções.

Para obter mais informações sobre o controlo de acesso da AD Azure, consulte [Gerir o acesso aos recursos do Azure utilizando o RBAC e o portal Azure](../../role-based-access-control/role-assignments-portal.md)

Para mais informações sobre como se conectar utilizando a autenticação Azure AD, consulte [a ligação ao seu nó utilizando a autenticação AAD](configure-aad.md).

### <a name="basic-authentication"></a>Autenticação básica

Para autenticação básica HTTPS, o nome do utilizador e as credenciais de palavra-passe são passados no cabeçalho HTTPS do pedido para o ponto final.

Pode ver os detalhes básicos de autenticação de um nó de audação no portal Azure. Navegue para um dos seus nódosos de transação do Serviço Blockchain Azure e selecione **Autenticação Básica** em definições.

![Autenticação básica](./media/configure-transaction-nodes/basic.png)

O nome do utilizador é o nome do seu nó e não pode ser alterado.

Para utilizar o \<URL, substitua a palavra-passe\> pelo conjunto de palavra-passe quando o nó foi provisionado. Pode atualizar a palavra-passe selecionando a **palavra-passe Reset**.

### <a name="access-keys"></a>Chaves de acesso

Para autenticação da chave de acesso, a chave de acesso está incluída no URL final. Quando o nó de transações é provisionado, são geradas duas chaves de acesso. Qualquer uma das teclas de acesso pode ser utilizada para autenticação. Duas teclas permitem-lhe alterar e rodar as teclas.

Pode visualizar os principais detalhes da chave de acesso de um nó de transação e copiar endereços finais que incluem as chaves de acesso. Navegue para um dos seus nódosos de transação do Serviço Blockchain Azure e selecione **Access Keys** em definições.

### <a name="firewall-rules"></a>Regras da firewall

As regras de firewall permitem-lhe limitar os endereços IP que podem tentar autenticar o seu nó de transações.  Se não estiverem configuradas regras de firewall para o seu nó de transações, não pode ser acedida por nenhuma parte.  

Para ver as regras de firewall de um nó de transações, navegue para um dos seus nós de transação do Membro do Serviço Blockchain Azure e selecione regras de **Firewall** em definições.

Pode adicionar regras de firewall inserindo um nome de regra, iniciando o endereço IP e um endereço IP final na grelha de regras da **Firewall.**

![Regras da firewall](./media/configure-transaction-nodes/firewall-rules.png)

Para permitir:

* **Endereço IP único:** Configure o mesmo endereço IP para os endereços IP intitular e finais.
* **Intervalo de endereços IP:** Configure a gama de endereços IP inicial e final. Por exemplo, uma gama a partir de 10.221.34.0 e terminando em 10.221.34.255 permitiria a totalidade da 10.221.34.xxx sub-rede.
* **Permitir todos os endereços IP:** Configure o endereço IP inicial para 0.0.0.0 e o endereço IP final para 255.255.255.255.

## <a name="connection-strings"></a>Cadeias de ligação

A sintaxe de cadeia de ligação para o nó de transações está prevista para autenticação básica ou utilização de chaves de acesso. São fornecidas cordas de ligação, incluindo chaves de acesso sobre HTTPS e WebSockets.

Pode ver as cordas de ligação de um nó de transações e copiar endereços finais. Navegue para um dos seus nódosos de transação do membro do Serviço Blockchain Azure e selecione **as cordas de Ligação** nas definições.

![Cadeias de ligação](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Código de exemplo

O código da amostra é fornecido para permitir a ligação ao seu nó de transação via Web3, Nethereum, Web3js e Truffle.

Pode ver o código de ligação de um nó de transação e copiá-lo para usar com ferramentas de desenvolvimento populares. Vá a um dos seus nódosos de transação do Serviço Blockchain Azure e selecione **Código de Amostra** em definições.

Escolha o separador Web3, Nethereum, Truffle ou Web3j para ver a amostra de código que pretende utilizar.

![Código de exemplo](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configure os nódosdeos de transação usando o Azure CLI](manage-cli.md)
