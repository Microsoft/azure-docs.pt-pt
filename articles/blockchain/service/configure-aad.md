---
title: Configure Azure Ative Diretório access - Azure Blockchain Service
description: Como configurar o Serviço Azure Blockchain com acesso ao Diretório Ativo Azure
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: 682ab282036fcd592e66942d08a84cdce46d8915
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74455863"
---
# <a name="how-to-configure-azure-active-directory-access-for-azure-blockchain-service"></a>Como configurar o acesso ao Diretório Ativo Azure para o Serviço Azure Blockchain

Neste artigo, aprende-se a conceder acesso e a ligar-se aos nós do Azure Blockchain Service utilizando utilizadores, grupos ou iDs de aplicação do Azure Ative Directory (Azure AD).

A Azure AD fornece gestão de identidade baseada na nuvem e permite-lhe usar uma única identidade em toda uma empresa e aplicações de acesso em Azure. O Azure Blockchain Service está integrado com a Azure AD e oferece benefícios como a federação de ID, a autenticação de entrada única e multi-factor.

## <a name="prerequisites"></a>Pré-requisitos

* [Crie um membro blockchain usando o portal Azure](create-member.md)

## <a name="grant-access"></a>Conceder acesso

Pode conceder acesso tanto ao nível dos membros como ao nó. A concessão de direitos de acesso ao nível dos membros, por sua vez, concederá acesso a todos os nós sob o membro.

### <a name="grant-member-level-access"></a>Acesso ao nível dos membros

Para conceder permissão de acesso ao nível dos membros.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegar para controlo de **acesso (IAM) > Adicionar > Adicionar atribuição de funções**.
1. Selecione a função **blockchain Member Node Access (Preview)** e adicione o objeto de ID Azure AD a que deseja conceder acesso. O objeto de ID Azure AD pode ser:

    | Objeto da AD Azure | Exemplo |
    |-----------------|---------|
    | Utilizador da AD Azure   | `kim@contoso.onmicrosoft.com` |
    | Grupo Azure AD  | `sales@contoso.onmicrosoft.com` |
    | ID da aplicação  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Adicionar atribuição de função](./media/configure-aad/add-role-assignment.png)

1. Selecione **Guardar**.

### <a name="grant-node-level-access"></a>Conceder acesso ao nível do nó

Você pode conceder acesso ao nível do nó navegando para a segurança do nó e clique no nome do nó que você deseja conceder acesso.

Selecione a função blockchain Member Node Access (Preview) e adicione o objeto de ID Azure AD a que deseja conceder acesso.

Para mais informações, consulte os nós de transação do [Serviço Configure Azure Blockchain](configure-transaction-nodes.md#azure-active-directory-access-control).

## <a name="connect-using-azure-blockchain-connector"></a>Conecte-se utilizando o Conector Azure Blockchain

Descarregue ou clone o [Conector Azure Blockchain do GitHub](https://github.com/Microsoft/azure-blockchain-connector/).

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

Siga a secção de arranque rápido no **readme** para construir o conector a partir do código fonte.

### <a name="connect-using-an-azure-ad-user-account"></a>Conecte-se usando uma conta de utilizador da AD Azure

1. Executar o seguinte comando para autenticar utilizando uma conta de utilizador Azure AD. Substitua \<o myAADDirectory\> por um domínio Azure AD. Por exemplo, `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. A D.A. Azure pede credenciais.
1. Inscreva-se com o seu nome de utilizador e senha.
1. Após a autenticação bem sucedida, o seu representante local liga-se ao seu nó blockchain. Agora pode anexar o seu cliente Geth ao ponto final local.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Conecte-se usando um ID de aplicação

Muitas aplicações autenticam com a AD Azure utilizando um ID de aplicação em vez de uma conta de utilizador Da D. Azure.

Para ligar ao seu nó utilizando um ID de aplicação, substitua o **código aadauthcode** por **aadclient**.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Parâmetro | Descrição |
|-----------|-------------|
| inquilino-id | Domínio Azure AD, por exemplo,`yourdomain.onmicrosoft.com`
| cliente-id | Identificação do cliente da aplicação registada na Azure AD
| segredo de cliente | Segredo do cliente da aplicação registada na Azure AD

Para obter mais informações sobre como registar uma aplicação em Azure AD, consulte [Como: Utilizar o portal para criar uma aplicação e diretor de serviço seletiva seletiva que possa aceder a recursos](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>Conecte um dispositivo móvel ou navegador de texto

Para um dispositivo móvel ou navegador baseado em texto onde o ecrã pop-up de autenticação AD Azure não é possível, o Azure AD gera uma senha única. Pode copiar a senha e proceder à autenticação da AD Azure noutro ambiente.

Para gerar a senha, substitua o **aadauthcode** por **um dispositivo addevice**. Substitua \<o myAADDirectory\> por um domínio Azure AD. Por exemplo, `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre segurança de dados no Serviço Azure Blockchain, consulte a [segurança do Serviço Azure Blockchain](data-security.md).