---
title: Configure Acesso ao Diretório Ativo Azure - Serviço Azure Blockchain
description: Como configurar o Serviço Azure Blockchain com acesso ao Azure Ative Directory
ms.date: 11/22/2019
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: 337d01abc51d310d06aeea3427b770132be4824c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85208778"
---
# <a name="how-to-configure-azure-active-directory-access-for-azure-blockchain-service"></a>Como configurar o acesso ao Azure Ative Directory para o Serviço Azure Blockchain

Neste artigo, aprende a conceder acesso e a ligar-se aos nós do Serviço Azure Blockchain utilizando o utilizador, grupo ou IDs de aplicação Azure Ative.

O Azure AD fornece uma gestão de identidade baseada na nuvem e permite-lhe usar uma única identidade em toda uma empresa e aceder a aplicações em Azure. O Azure Blockchain Service está integrado com a Azure AD e oferece benefícios como a federação de ID, a autenticação de entrada única e multi-fator.

## <a name="prerequisites"></a>Pré-requisitos

* [Criar um membro blockchain usando o portal Azure](create-member.md)

## <a name="grant-access"></a>Conceder acesso

Pode conceder acesso tanto ao nível dos membros como ao nível do nó. A concessão de direitos de acesso ao nível dos membros concederá, por sua vez, acesso a todos os nós sob o membro.

### <a name="grant-member-level-access"></a>Acesso ao nível do membro subvenção

Para conceder permissão de acesso ao nível dos membros.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegar para **o controlo de acesso (IAM) > Adicionar > Adicionar função**.
1. Selecione a função **de Acesso ao Nó do Membro blockchain (Pré-visualização)** e adicione o objeto Azure AD ID a que deseja conceder acesso. O objeto de ID AD Azure pode ser:

    | Objeto AD de Azure | Exemplo |
    |-----------------|---------|
    | Utilizador do Azure Active Directory   | `kim@contoso.onmicrosoft.com` |
    | Grupo AD AZure  | `sales@contoso.onmicrosoft.com` |
    | ID da Aplicação  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Adicionar atribuição de função](./media/configure-aad/add-role-assignment.png)

1. Selecione **Guardar**.

### <a name="grant-node-level-access"></a>Acesso ao nível do nó de subvenção

Você pode conceder acesso ao nível do nó navegando para a segurança do nó e clicar no nome do nó que deseja conceder acesso.

Selecione a função de Acesso ao Nó do Membro blockchain (Pré-visualização) e adicione o objeto Azure AD ID a que deseja conceder acesso.

Para obter mais informações, consulte [os nós de transação do Serviço Configure Azure Blockchain](configure-transaction-nodes.md#azure-active-directory-access-control).

## <a name="connect-using-azure-blockchain-connector"></a>Conecte-se usando o conector Azure Blockchain

Faça o download ou clone do [Conector Azure Blockchain do GitHub](https://github.com/Microsoft/azure-blockchain-connector/).

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

Siga a secção de arranque rápido na **leitura** para construir o conector a partir do código fonte.

### <a name="connect-using-an-azure-ad-user-account"></a>Conecte-se usando uma conta de utilizador AZure AD

1. Executar o seguinte comando para autenticar utilizando uma conta de utilizador Azure AD. Substitua \<myAADDirectory\> por um domínio AD Azure. Por exemplo, `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Azure AD pede credenciais.
1. Inscreva-se com o seu nome de utilizador e senha.
1. Após a autenticação bem sucedida, o seu representante local conecta-se ao seu nó blockchain. Pode agora anexar o seu cliente Geth com o ponto final local.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Conecte-se usando um ID de aplicação

Muitas aplicações autenticam-se com Azure AD usando um ID de aplicação em vez de uma conta de utilizador AZure AD.

Para ligar ao seu nó utilizando um ID de aplicação, **substitua o código aadauthcode** por **aadclient**.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Parâmetro | Descrição |
|-----------|-------------|
| inquilino-id | Domínio AD azul, por exemplo, `yourdomain.onmicrosoft.com`
| id cliente | ID do cliente do pedido registado em Azure AD
| cliente-segredo | Segredo de cliente da aplicação registada no Azure AD

Para obter mais informações sobre como registar uma aplicação em Azure AD, consulte [Como: Usar o portal para criar uma aplicação AD AD Azure e um responsável de serviço que possa aceder a recursos](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>Ligue um dispositivo móvel ou navegador de texto

Para um dispositivo móvel ou navegador baseado em texto onde o ecrã pop-up de autenticação AD AZure não é possível, o Azure AD gera uma senha única. Pode copiar a senha e proceder à autenticação Azure AD noutro ambiente.

Para gerar a senha, **substitua o código aadauth por** **aaddevice**. Substitua \<myAADDirectory\> por um domínio AD Azure. Por exemplo, `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a segurança de dados no Serviço Azure Blockchain, consulte [a segurança do Serviço Azure Blockchain](data-security.md).