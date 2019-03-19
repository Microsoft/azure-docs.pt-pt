---
title: Modelo de solução de blockchain Ethereum de pilha do Azure
description: Tutorial sobre como utilizar um modelo de solução personalizada para implementar e configurar uma rede de blockchain consortium Ethereum no Azure Stack
services: azure-stack
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 02/04/2019
ms.topic: tutorial
ms.service: azure-stack
ms.reviewer: seyadava
ms.custom: mvc
manager: femila
ms.lastreviewed: 02/04/2019
ms.openlocfilehash: a1902ae2bf663c432876a0f73e2bb17616023b8a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58006926"
---
# <a name="deploy-an-ethereum-blockchain-network-on-azure-stack"></a>Implementar uma rede de blockchain Ethereum no Azure Stack

O modelo de solução Ethereum foi concebido para que seja mais fácil e rápido implementar e configurar uma rede do consórcio com vários membros Ethereum blockchain com o mínimo de conhecimento do Azure e Ethereum.

Com um punhado de entradas do utilizador e uma implementação de clique único através do portal de inquilino do Azure Stack, cada membro pode aprovisionar a seus requisitos de espaço da rede. Requisitos de espaço de rede de cada membro é composta por um conjunto de nós de transação com balanceamento de carga com que uma aplicação ou o utilizador pode interagir para submeter transações, um conjunto de nós de extração para registrar transações e uma aplicação Virtual de rede (NVA). Um passo de ligação subsequentes liga-se as NVAs para criar uma rede de totalmente configurado com vários membros blockchain.

Para configurar:

- Escolha uma arquitetura de implantação
- Implementar um autónomo, o líder de consórcio ou a rede de membro de consórcio

## <a name="prerequisites"></a>Pré-requisitos

Transferir os itens mais recente [do Marketplace](../azure-stack-download-azure-marketplace-item.md):

- Ubuntu Server 16.04 LTS
- Windows Server 2016
- Script personalizado para Linux 2.0
- Extensão de Script personalizado para Windows

Para obter mais informações sobre os cenários de blockchain, consulte [modelo de solução de prova da autoridade consortium Ethereum](../../blockchain/templates/ethereum-poa-deployment.md).

## <a name="deployment-architecture"></a>Arquitetura de implantação

Este modelo de solução pode implementar única ou de múltiplas rede de consórcio Ethereum membro. A rede virtual está ligada numa topologia de cadeia com os recursos de aplicação Virtual de rede e a ligação. 

## <a name="deployment-use-cases"></a>Casos de utilização de implementação

O modelo pode implementar Ethereum consortium para líder e a associação de membro de diversas formas, aqui são aqueles que testamos:

- Numa pilha do Azure com vários nós, com o Azure AD ou AD FS, implementar líder e membro com a mesma subscrição ou com diferentes subscrições.
- Num nó único do Azure Stack (com o Azure AD), implemente líder e membro com a mesma subscrição.

### <a name="standalone-and-consortium-leader-deployment"></a>Implementação de líder autónomo e consortium

O modelo de líder consortium configura os requisitos de espaço do primeiro membro na rede. 

1. Transferir o [modelo líder do GitHub](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/ConsortiumLeader/mainTemplate.json)
2. No portal de inquilino do Azure Stack, selecione **+ criar um recurso > implementação do modelo** para implementar a partir de um modelo personalizado.
3. Selecione **Editar modelo** para editar o novo modelo personalizado.
4. No painel de edição no lado direito, copie e cole o modelo de líder JSON que transferiu anteriormente.
    
    ![Editar modelo líder](./media/azure-stack-ethereum/edit-leader-template.png)

5. Selecione **Guardar**.
6. Selecione **Editar parâmetros** e concluir os parâmetros de modelo para a sua implementação.
    
    ![Editar parâmetros de modelo de líder](./media/azure-stack-ethereum/edit-leader-parameters.png)

    Nome do Parâmetro | Descrição | Valores Permitidos | Valor da amostra
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | A cadeia de caracteres usada como base para atribuir nomes os recursos implementados. | Carateres de alfanuméricos com comprimento de 1 a 6 | Eth
    AUTHTYPE | O método para autenticar para a máquina virtual. | Chave pública de palavra-passe ou SSH | Palavra-passe
    ADMINUSERNAME | Nome de utilizador de administrador de cada VM implementada | 1 - 64 carateres | gethadmin
    ADMINPASSWORD (tipo de autenticação = a palavra-passe)| A palavra-passe da conta de administrador para cada uma das máquinas virtuais implementadas. A palavra-passe tem de conter 3 dos seguintes requisitos: 1 caráter em maiúsculas, 1 carater em minúsculas, 1 número e 1 caráter especial. <br />Embora todas as VMs tenham inicialmente a mesma palavra-passe, pode alterar a palavra-passe após o aprovisionamento.|12 - 72 carateres|
    ADMINSSHKEY (tipo de autenticação = sshPublicKey) | A chave secure shell utilizada para início de sessão remoto. | |
    GENESISBLOCK | Cadeia de caracteres JSON que representa o bloco de genesis personalizado.  Especificar um valor para este parâmetro é opcional. | |
    ETHEREUMACCOUNTPSSWD | A palavra-passe de administrador utilizada para proteger a conta de Ethereum. | |
    ETHEREUMACCOUNTPASSPHRASE | A frase de acesso utilizada para gerar a chave privada associada à conta Ethereum. | |
    ETHEREUMNETWORKID | O ID de rede do consortium. | Utilizar qualquer valor entre 5 e 999,999,999 | 72
    CONSORTIUMMEMBERID | O ID associado a cada membro da rede de consórcio.   | Este ID deve ser exclusivo na rede. | 0
    NUMMININGNODES | Número de nós de extração. | Entre 2 e 15. | 2
    MNNODEVMSIZE | Tamanho da VM de nós de extração. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Desempenho de armazenamento de nós de extração. | | Standard_LRS
    NUMTXNODES | Número de nós de transação. | Entre 1 e 5. | 1
    TXNODEVMSIZE | Tamanho da VM de nós de transação. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | Desempenho de armazenamento de nós de transação. | | Standard_LRS
    BASEURL | URL de base para obter os modelos dependendo do. | Utilize o valor predefinido, a menos que queira personalizar os modelos de implementação. | 

7. Selecione **OK**.
8. Na **implementação personalizada**, especifique **subscrição**, **grupo de recursos**, e **localização do grupo de recursos**.
    
    ![Parâmetros de implementação do líder](./media/azure-stack-ethereum/leader-deployment-parameters.png)

    Nome do Parâmetro | Descrição | Valores Permitidos | Valor da amostra
    ---------------|-------------|----------------|-------------
    Subscrição | A subscrição ao qual pretende implementar a rede de consórcio | | Subscrição de consumo
    Grupo de Recursos | O grupo de recursos ao qual pretende implementar a rede de consórcio. | | EthereumResources
    Localização | A região do Azure para o grupo de recursos. | | local

8. Selecione **Criar**.

Implementação pode demorar de 20 minutos ou mais tempo a concluir.

Depois de concluída a implementação, pode rever a implementação de resumo para **Microsoft. Modelo** na secção de implementação do grupo de recursos. O resumo contém valores de saída que podem ser utilizadas para associar a membros de consortium.

Para verificar a implementação de líder, procure o site de administração do líder. Pode encontrar o endereço do site de administração na secção de saída **Template** implementação.  

![Resumo da implementação líder](./media/azure-stack-ethereum/ethereum-node-status.png)

### <a name="joining-consortium-member-deployment"></a>Implementação de membro de consórcio junção

1. Transferir o [modelo de membro consortium partir do GitHub](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/JoiningMember/mainTemplate.json)
2. No portal de inquilino do Azure Stack, selecione **+ criar um recurso > implementação do modelo** para implementar a partir de um modelo personalizado.
3. Selecione **Editar modelo** para editar o novo modelo personalizado.
4. No painel de edição no lado direito, copie e cole o modelo de líder JSON que transferiu anteriormente.
5. Selecione **Guardar**.
6. Selecione **Editar parâmetros** e concluir os parâmetros de modelo para a sua implementação.

    Nome do Parâmetro | Descrição | Valores Permitidos | Valor da amostra
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | A cadeia de caracteres usada como base para atribuir nomes os recursos implementados. | Carateres de alfanuméricos com comprimento de 1 a 6 | Eth
    AUTHTYPE | O método para autenticar para a máquina virtual. | Chave pública de palavra-passe ou SSH | Palavra-passe
    ADMINUSERNAME | Nome de utilizador de administrador de cada VM implementada | 1 - 64 carateres | gethadmin
    ADMINPASSWORD (tipo de autenticação = a palavra-passe)| A palavra-passe da conta de administrador para cada uma das máquinas virtuais implementadas. A palavra-passe tem de conter 3 dos seguintes requisitos: 1 caráter em maiúsculas, 1 carater em minúsculas, 1 número e 1 caráter especial. <br />Embora todas as VMs tenham inicialmente a mesma palavra-passe, pode alterar a palavra-passe após o aprovisionamento.|12 - 72 carateres|
    ADMINSSHKEY (tipo de autenticação = sshPublicKey) | A chave secure shell utilizada para início de sessão remoto. | |
    CONSORTIUMMEMBERID | O ID associado a cada membro da rede de consórcio.   | Este ID deve ser exclusivo na rede. | 0
    NUMMININGNODES | Número de nós de extração. | Entre 2 e 15. | 2
    MNNODEVMSIZE | Tamanho da VM de nós de extração. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Desempenho de armazenamento de nós de extração. | | Standard_LRS
    NUMTXNODES | Número de nós de transação. | Entre 1 e 5. | 1
    TXNODEVMSIZE | Tamanho da VM de nós de transação. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | Desempenho de armazenamento de nós de transação. | | Standard_LRS
    CONSORTIUMDATA | O URL que aponta para os dados de configuração de consórcio relevantes fornecidos pela implementação de outro membro. Este valor pode ser encontrado na saída de implementação do líder. | |
    REMOTEMEMBERVNETADDRESSSPACE | O endereço IP da NVA do coordenador. Este valor pode ser encontrado na saída de implementação do líder. | | 
    REMOTEMEMBERNVAPUBLICIP | O endereço IP da NVA do coordenador. Este valor pode ser encontrado na saída de implementação do líder. | | 
    CONNECTIONSHAREDKEY | Um segredo previamente estabelecido entre os membros da rede de consórcio que estão a estabelecer uma ligação. | |
    BASEURL | URL de base para o modelo. | Utilize o valor predefinido, a menos que queira personalizar os modelos de implementação. | 

7. Selecione **OK**.
8. Na **implementação personalizada**, especifique **subscrição**, **grupo de recursos**, e **localização do grupo de recursos**.

    Nome do Parâmetro | Descrição | Valores Permitidos | Valor da amostra
    ---------------|-------------|----------------|-------------
    Subscrição | A subscrição ao qual pretende implementar a rede de consórcio | | Subscrição de consumo
    Grupo de Recursos | O grupo de recursos ao qual pretende implementar a rede de consórcio. | | MemberResources
    Localização | A região do Azure para o grupo de recursos. | | local

8. Selecione **Criar**.

Implementação pode demorar de 20 minutos ou mais tempo a concluir.

Depois de concluída a implementação, pode rever o resumo da implementação **Template** na secção de implementação do grupo de recursos. O resumo contém valores de saída que podem ser utilizados para ligar os membros de consortium.

Para verificar a implementação do membro, procure o site de administração do membro. Pode encontrar o endereço do site de administração na secção de saída da implementação de Template.

![Resumo da implementação do membro](./media/azure-stack-ethereum/ethereum-node-status-2.png)

Como mostrado na imagem, o estado de nós do membro é **não está em execução**. Este estado é porque não for possível estabelecer a ligação entre o membro e o líder. A ligação entre o membro e líder é uma ligação bidirecional. Quando implementa o membro, o modelo cria automaticamente a ligação de membro para o líder. Para criar a ligação da líder para o membro, vá para o passo seguinte.

### <a name="connect-member-and-leader"></a>Ligue-se membro e líder

Este modelo cria uma ligação de coordenador para um membro remoto. 

1. Transferir o [ligar o modelo de membro e líder do GitHub](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/Connection/mainTemplate.json)
2. No portal de inquilino do Azure Stack, selecione **+ criar um recurso > implementação do modelo** para implementar a partir de um modelo personalizado.
3. Selecione **Editar modelo** para editar o novo modelo personalizado.
4. No painel de edição no lado direito, copie e cole o modelo de líder JSON que transferiu anteriormente.
    
    ![Editar modelo de ligar](./media/azure-stack-ethereum/edit-connect-template.png)

5. Selecione **Guardar**.
6. Selecione **Editar parâmetros** e concluir os parâmetros de modelo para a sua implementação.
    
    ![Editar ligar os parâmetros do modelo](./media/azure-stack-ethereum/edit-connect-parameters.png)

    Nome do Parâmetro | Descrição | Valores Permitidos | Valor da amostra
    ---------------|-------------|----------------|-------------
    MEMBERNAMEPREFIX | Prefixo do nome do líder. Este valor pode ser encontrado na saída de implementação do líder.  | Carateres de alfanuméricos com comprimento de 1 a 6 | |
    MEMBERROUTETABLENAME | Nome da tabela de rotas no coordenador. Este valor pode ser encontrado na saída de implementação do líder. |  | 
    REMOTEMEMBERVNETADDRESSSPACE | Espaço do membro de endereço. Este valor pode ser encontrado na saída de implementação do membro. | |
    CONNECTIONSHAREDKEY | Um segredo previamente estabelecido entre os membros da rede de consórcio que estão a estabelecer uma ligação.  | |
    REMOTEMEMBERNVAPUBLICIP | O endereço IP da NVA do membro. Este valor pode ser encontrado na saída de implementação do membro. | |
    MEMBERNVAPRIVATEIP | Endereço de IP da NVA privado líder. Este valor pode ser encontrado na saída de implementação do líder. | |
    LOCALIZAÇÃO | Localização do seu ambiente do Azure Stack. | | local
    BASEURL | URL de base para o modelo. | Utilize o valor predefinido, a menos que queira personalizar os modelos de implementação. | 

7. Selecione **OK**.
8. Na **implementação personalizada**, especifique **subscrição**, **grupo de recursos**, e **localização do grupo de recursos**.
    
    ![Ligue-se os parâmetros de implementação](./media/azure-stack-ethereum/connect-deployment-parameters.png)

    Nome do Parâmetro | Descrição | Valores Permitidos | Valor da amostra
    ---------------|-------------|----------------|-------------
    Subscrição | Subscrição a líder. | | Subscrição de consumo
    Grupo de Recursos | Grupo de recursos do coordenador. | | EthereumResources
    Localização | A região do Azure para o grupo de recursos. | | local

8. Selecione **Criar**.

Após a implementação estiver concluída, demora alguns minutos para líder e membro iniciar a comunicação. Para verificar a implementação, atualize o site de administração do membro. Estado de nós o membro deve estar em execução. 

![Verificar a implementação](./media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre Ethereum e o Azure, veja [tecnologia Blockchain e aplicativos](https://azure.microsoft.com/solutions/blockchain/).
