---
title: Como configurar o acesso ao Azure Active Directory
description: Como configurar o serviço do Azure Blockchain com acesso ao Active Directory do Azure
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: seal
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: 616e342f1d52179c40c225c5dafc9de13ce85e06
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028219"
---
# <a name="how-to-configure-azure-active-directory-access"></a>Como configurar o acesso ao Azure Active Directory

Neste artigo, saiba como conceder acesso e ligar a nós de serviço do Azure Blockchain com o utilizador, grupo ou IDs de aplicação do Azure Active Directory (Azure AD).

Azure AD fornece gestão de identidade com base na cloud e permite-lhe utilizar uma única identidade entre um aplicativos inteiros de enterprise e acesso no Azure. O serviço do Azure Blockchain é integrado com o Azure AD e oferece benefícios, como o Federação de ID, é necessário um início de sessão e multi-factor authentication de único.

## <a name="prerequisites"></a>Pré-requisitos

* [Criar um membro de blockchain com o portal do Azure](create-member.md)

## <a name="grant-access"></a>Conceder acesso

Pode conceder acesso em nível de membro e o nível de nó. Conceder direitos de acesso no nível de membro será, por sua vez, conceder acesso a todos os nós sob o membro.

### <a name="grant-member-level-access"></a>Conceder acesso de nível de membro

Para conceder permissão de acesso no nível de membro.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue para **controlo de acesso (IAM) > Adicionar > Adicionar atribuição de função**.
1. Selecione o **Blockchain membro nó acesso (pré-visualização)** função e adicionar o objeto de ID do Azure AD que pretende conceder acesso a. Pode ser objeto de ID do AD do Azure:

    | Objetos do Azure AD | Exemplo |
    |-----------------|---------|
    | Utilizador do Azure AD   | `frank@contoso.onmicrosoft.com` |
    | Grupo do Azure AD  | `sales@contoso.onmicrosoft.com` |
    | ID da aplicação  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Adicionar atribuição de função](./media/configure-aad/add-role-assignment.png)

1. Selecione **Guardar**.

### <a name="grant-node-level-access"></a>Conceder acesso de nível de nó

1. Pode conceder acesso ao nível do nó ao navegar para a segurança do nó e clique no nome do nó que deseja conceder acesso.
1. Selecione a função de acesso de nó de membro de Blockchain (pré-visualização) e adicione o objeto de ID do Azure AD que pretende conceder acesso a. 

## <a name="connect-using-azure-blockchain-connector"></a>Ligar através do conector do Azure Blockchain

Transfira ou clone a [conector do Azure Blockchain do GitHub](https://github.com/Microsoft/azure-blockchain-connector/).

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

Siga o guia de introdução secção a **Leiame** para criar o conector do código-fonte.

### <a name="connect-using-an-azure-ad-user-account"></a>Ligar através de uma conta de utilizador do Azure AD

1. Execute o seguinte comando para autenticar com uma conta de utilizador do Azure AD. Substitua \<myAADDirectory\> com um domínio do Azure AD. Por exemplo, `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Pede as credenciais do Azure AD.
1. Inicie sessão com o nome de utilizador e palavra-passe.
1. Após a autenticação com êxito, o proxy local liga-se ao seu nó de blockchain. Agora pode anexar o seu cliente Geth com o ponto final local.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Ligar com um ID de aplicação

Muitos aplicativos autenticar com o Azure AD com um ID de aplicação em vez de uma conta de utilizador do Azure AD.

Para ligar ao seu nó com um ID de aplicação, substitua **aadauthcode** com **aadclient**.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Parâmetro | Descrição |
|-----------|-------------|
| id do inquilino | Domínio do Azure AD, por exemplo, `yourdomain.onmicrosoft.com`
| id de cliente | ID de cliente da aplicação registada no Azure AD
| segredo do cliente | Segredo do cliente da aplicação registada no Azure AD

Para obter mais informações sobre como registar uma aplicação no Azure AD, consulte [como: Utilizar o portal para criar uma aplicação do Azure AD e principal de serviço que pode aceder aos recursos](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>Ligue-se de um navegador de texto ou de dispositivo móvel

Para um dispositivo móvel ou browser baseado em texto, em que a exibição de janelas pop-up de autenticação do Azure AD não é possível, o Azure AD gera um código de acesso único. Pode copiar o código de acesso e prosseguir com a autenticação do Azure AD no outro ambiente.

Para gerar o código de acesso, substitua **aadauthcode** com **aaddevice**. Substitua \<myAADDirectory\> com um domínio do Azure AD. Por exemplo, `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a segurança de dados no serviço do Azure Blockchain, consulte:

> [!div class="nextstepaction"]
> [Segurança do Azure do serviço de Blockchain](data-security.md)