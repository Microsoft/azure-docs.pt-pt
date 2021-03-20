---
title: Tutorial `:` Utilize uma identidade gerida para aceder ao Cofre da Chave Azure - Linux - Azure AD
description: Um tutorial que o orienta pelo processo de utilização de uma identidade gerida atribuída pelo sistema de uma VM do Linux para aceder ao Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb31d6e25ce1c1ff5c3e4dbabb4fa53da0bd2ef3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101093937"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Tutorial: utilizar uma identidade gerida atribuída pelo sistema de VM do Linux para aceder ao Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra-lhe como uma máquina virtual Linux (VM) pode usar uma identidade gerida pelo sistema para aceder ao [Cofre da Chave Azure](../../key-vault/general/overview.md). Servindo como uma bota, o Key Vault permite que a aplicação do seu cliente utilize um segredo para aceder a recursos não garantidos pela Azure Ative Directory (AD). As Identidades de Serviço Gerido são geridas automaticamente pela Azure e permitem-lhe autenticar serviços que suportem a autenticação AZure AD, sem incluir informações de autenticação no seu código.

Saiba como:

> [!div class="checklist"]
> * Conceder o acesso da VM a um segredo armazenado num Key Vault 
> * Obter um token de acesso com a identidade da VM e utilizá-lo para obter o segredo a partir do Key Vault 
 
## <a name="prerequisites"></a>Pré-requisitos

- Uma compreensão básica das identidades geridas. Se não estiver familiarizado com a funcionalidade das identidades geridas para os recursos do Azure, veja esta [descrição geral](overview.md). 
- Uma conta Azure, [inscreva-se para uma conta gratuita.](https://azure.microsoft.com/free/)
- Permissões "Proprietário" no âmbito apropriado (a sua subscrição ou grupo de recursos) para executar as etapas necessárias de criação de recursos e gestão de funções. Se precisar de assistência com a atribuição de funções, consulte [as funções de Assign Azure para gerir o acesso aos seus recursos de subscrição Azure](../../role-based-access-control/role-assignments-portal.md).
- Também precisa de uma máquina Virtual Linux que tenha o sistema atribuído a identidades geridas ativadas.
  - Se precisar de criar uma máquina virtual para este tutorial, pode seguir o artigo intitulado [Criar uma máquina virtual Linux com o portal Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


## <a name="create-a-key-vault"></a>Criar um Key Vault  

Esta secção mostra como conceder o acesso ao seu VM a um segredo armazenado num Cofre de Chaves. Com as identidades geridas para recursos do Azure, o seu código pode obter tokens de acesso para autenticação perante recursos que suportem a Autenticação do Azure AD.No entanto, nem todos os serviços do Azure suportam a autenticação do Azure AD. Para utilizar identidades geridas para recursos do Azure com esses serviços, armazene as credenciais do serviço no Azure Key Vault e utilize a identidade gerida da VM para aceder ao Key Vault para obter as credenciais.

Primeiro, é preciso criar um Key Vault e conceder o acesso de identidade gerida atribuída pelo sistema da nossa VM ao Key Vault.

1. Abra o [portal](https://portal.azure.com/) Azure
1. No topo da barra de navegação esquerda, **selecione Criar um recurso**  
1. Na **pesquisa do** tipo de caixa Marketplace no **Cofre de Chaves** e **embram Enter**.  
1. Selecione **Key Vault** a partir dos resultados.
1. Selecione **Criar**
1. Indique um **Nome** para o novo Key Vault.

    ![Criar um ecrã de cofre chave](./media/tutorial-linux-vm-access-nonaad/create-key-vault.png)

1. Preencha todas as informações necessárias certificando-se de que escolhe o grupo de subscrição e recursos onde criou a máquina virtual que está a usar para este tutorial.
1. **Selecione Review+ criar**
1. Selecione **Criar**

### <a name="create-a-secret"></a>Criar um segredo

Em seguida, adicione um segredo ao Cofre de Chaves, para que possa recuperá-lo mais tarde usando o código em execução no seu VM. Para efeitos deste tutorial, estamos a usar o PowerShell mas os mesmos conceitos aplicam-se a qualquer código de execução nesta máquina virtual.

1. Navegue para o seu recém-criado Cofre-Chave.
1. Selecione **Segredos** e clique em **Adicionar**.
1. **Selecione Gerar/Importar**
1. No **Criar um** ecrã secreto a partir das **opções de Upload** deixar o **Manual** selecionado.
1. Introduza o nome e o valor do segredo.  O valor pode ser o que quiser. 
1. Deixe as datas de ativação e expiração claras e mantenha **Ativado** como **Sim**. 
1. Clique em **Criar** para criar o segredo.

   ![Criar um segredo](./media/tutorial-linux-vm-access-nonaad/create-secret.png)

## <a name="grant-access"></a>Conceder acesso

A identidade gerida usada pela máquina virtual precisa de ter acesso para ler o segredo que armazenaremos no Cofre de Chaves.

1. Navegue até ao seu recém-criado Cofre-Chave
1. Selecione **a Política** de Acesso do menu do lado esquerdo.
1. Selecione **Adicionar Política de Acesso**

   ![cofre chave criar tela de política de acesso](./media/tutorial-linux-vm-access-nonaad/key-vault-access-policy.png)

1. Na secção **de política de acesso adicionar** ao abrigo do **Configure do modelo (opcional)** escolha **Gestão Secreta** no menu pull-down.
1. Selecione **Selecionar Principal** e, no campo de pesquisa, introduza o nome da VM que criou anteriormente.  Selecione o VM na lista de resultados e escolha **Selecione.**
1. Selecione **Adicionar**
1. Selecione **Guardar**.

## <a name="access-data"></a>Aceder a dados

Para concluir estes passos, precisa de um cliente SSH.  Se estiver a utilizar o Windows, pode utilizar o cliente SSH no [Subsistema Windows para Linux](/windows/wsl/about). Se precisar de ajuda para configurar as chaves do seu cliente SSH, veja [Como utilizar chaves SSH com o Windows no Azure](../../virtual-machines/linux/ssh-from-windows.md) ou [Como criar e utilizar um par de chaves SSH públicas e privadas para VMs do Linux no Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).
 
1. No portal, navegue para a VM do Linux e, em **Descrição Geral**, clique em **Ligar**. 
2. Clique em **Ligar** para ligar à VM com o cliente SSH que escolheu. 
3. Na janela de terminal, através do CURL, envie um pedido às identidades geridas no local para que o ponto final dos recursos do Azure obtenha um token de acesso ao Azure Key Vault.  
 
    O pedido CURL para o token de acesso encontra-se abaixo.  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true  
    ```
    A resposta inclui o token de acesso necessário para aceder ao Resource Manager. 
    
    Resposta:  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    Pode utilizar este token de acesso para autenticação no Azure Key Vault.  O pedido CURL seguinte mostra como ler um segredo do Key Vault com o CURL e a API REST do Key Vault.  Precisará do URL do Key Vault, que está na secção **Informações Básicas** da página **Descrição Geral** do Key Vault.  Também precisará do token de acesso que obteve na chamada anterior. 
        
    ```bash
    curl 'https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    A resposta terá o seguinte aspeto: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Depois de recuperar o segredo do Key Vault, pode utilizá-lo para autenticação num serviço que requeira um nome e uma palavra-passe.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando pretender limpar os recursos, visite o [portal Azure](https://portal.azure.com), selecione **Grupos de Recursos,** localize e selecione o grupo de recursos que foi criado no processo deste tutorial (como) e, em `mi-test` seguida, utilize o comando do **grupo de recursos Delete.**

Em alternativa, também pode fazê-lo através [do PowerShell ou do CLI](../../azure-resource-manager/management/delete-resource-group.md)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a utilizar uma identidade gerida atribuída pelo sistema de VM do Linux para aceder ao Azure Key Vault.  Para saber mais sobre o Azure Key Vault, veja:

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)