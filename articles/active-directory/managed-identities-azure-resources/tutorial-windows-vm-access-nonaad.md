---
title: Tutorial `:` Utilize uma identidade gerida para aceder ao Cofre da Chave Azure - Windows - Azure AD
description: Um tutorial que explica o processo de utilização de uma identidade gerida atribuída pelo sistema numa VM do Windows, para aceder ao Azure Key Vault.
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
ms.openlocfilehash: 668d3cb044512220ff7afbc165c77da704a9a5d7
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97107520"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Tutorial: Utilizar uma identidade gerida atribuída pelo sistema de VM do Windows para aceder ao Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra-lhe como uma máquina virtual do Windows (VM) pode usar uma identidade gerida pelo sistema para aceder ao [Cofre da Chave Azure](../../key-vault/general/overview.md). Servindo como uma bota, o Key Vault permite que a aplicação do seu cliente utilize um segredo para aceder a recursos não garantidos pela Azure Ative Directory (AD). As Identidades de Serviço Gerido são geridas automaticamente pela Azure e permitem-lhe autenticar serviços que suportem a autenticação AZure AD, sem incluir informações de autenticação no seu código.

Saiba como:

> [!div class="checklist"]
> * Conceder o acesso da VM a um segredo armazenado num Key Vault
> * Obter um token de acesso com a identidade da VM e utilizá-lo para obter o segredo a partir do Key Vault 

## <a name="prerequisites"></a>Pré-requisitos

- Uma compreensão das identidades geridas. Se não estiver familiarizado com a funcionalidade das identidades geridas para os recursos do Azure, veja esta [descrição geral](overview.md). 
- Uma conta Azure, [inscreva-se para uma conta gratuita.](https://azure.microsoft.com/free/)
- Permissões "Proprietário" no âmbito apropriado (a sua subscrição ou grupo de recursos) para executar as etapas necessárias de criação de recursos e gestão de funções. Se precisar de assistência com a atribuição de função, veja [Utilizar Controlo de Acesso Baseado em Funções para gerir o acesso aos recursos da sua subscrição do Azure](../../role-based-access-control/role-assignments-portal.md).
- Também precisa de uma máquina Virtual do Windows que tenha o sistema atribuído a identidades geridas ativadas.
  - Se precisar de criar uma máquina virtual para este tutorial, pode seguir o artigo intitulado [Criar uma máquina virtual com identidade atribuída ao sistema ativada](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)

## <a name="create-a-key-vault"></a>Criar um Key Vault  

Esta secção mostra como conceder o acesso ao seu VM a um segredo armazenado num Cofre de Chaves. Com as identidades geridas para recursos do Azure, o seu código pode obter tokens de acesso para autenticação perante recursos que suportem a Autenticação do Azure AD.No entanto, nem todos os serviços do Azure suportam a autenticação do Azure AD. Para utilizar identidades geridas para recursos do Azure com esses serviços, armazene as credenciais do serviço no Azure Key Vault e utilize a identidade gerida da VM para aceder ao Key Vault para obter as credenciais.

Primeiro, é preciso criar um Key Vault e conceder o acesso de identidade gerida atribuída pelo sistema da nossa VM ao Key Vault.

1. Abra o [portal](https://portal.azure.com/) Azure
1. No topo da barra de navegação esquerda, **selecione Criar um recurso**  
1. Na **pesquisa do** tipo de caixa Marketplace no **Cofre de Chaves** e **embram Enter**.  
1. Selecione **Key Vault** a partir dos resultados.
1. Selecione **Criar**
1. Indique um **Nome** para o novo Key Vault.

    ![Criar um ecrã de cofre chave](./media/msi-tutorial-windows-vm-access-nonaad/create-key-vault.png)

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

   ![Criar um segredo](./media/msi-tutorial-windows-vm-access-nonaad/create-secret.png)

## <a name="grant-access"></a>Conceder acesso

A identidade gerida usada pela máquina virtual precisa de ter acesso para ler o segredo que armazenaremos no Cofre de Chaves.

1. Navegue até ao seu recém-criado Cofre-Chave
1. Selecione **a Política** de Acesso do menu do lado esquerdo.
1. Selecione **Adicionar Política de Acesso**

   ![cofre chave criar tela de política de acesso](./media/msi-tutorial-windows-vm-access-nonaad/key-vault-access-policy.png)

1. Na secção **de política de acesso adicionar** ao abrigo do **Configure do modelo (opcional)** escolha **Gestão Secreta** no menu pull-down.
1. Selecione **Selecionar Principal** e, no campo de pesquisa, introduza o nome da VM que criou anteriormente.  Selecione o VM na lista de resultados e escolha **Selecione.**
1. Selecione **Adicionar**
1. Selecione **Guardar**.


## <a name="access-data"></a>Aceder a dados  

Esta secção mostra como obter um token de acesso usando a identidade VM e usá-lo para recuperar o segredo do Key Vault. Se não tiver o PowerShell 4.3.1 ou posterior instalado, terá de [transferir e instalar a versão mais recente](/powershell/azure/).

Primeiro, utilizamos a identidade gerida atribuída pelo sistema da VM para obter um token de acesso para autenticação no Key Vault:
 
1. No portal, navegue para **Máquinas Virtuais**, aceda à sua máquina virtual do Windows e, em **Descrição Geral**, clique em **Ligar**.
2. Introduza o seu **nome de utilizador** e **palavra-passe** para o qual adicionou quando criou o **Windows VM**.  
3. Agora que criou uma **Ligação de Ambiente de Trabalho Remoto** com a máquina virtual, abra o PowerShell na sessão remota.  
4. No PowerShell, invoca o pedido Web no inquilino para obter o token para o anfitrião local na porta específica para a VM.  

O pedido do PowerShell:

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
```

Pode ver como é a resposta abaixo:

![pedido com resposta simbólica](./media/msi-tutorial-windows-vm-access-nonaad/token.png)

Em seguida, extraia o token de acesso da resposta.  

```powershell
   $KeyVaultToken = $Response.access_token
```

Por fim, utilize o comando Invoke-WebRequest do PowerShell para obter o segredo que criou anteriormente no Key Vault ao transmitir o token de acesso no cabeçalho de Autorização.  Precisará do URL do Key Vault, que está na secção **Informações Básicas** da página **Descrição Geral** do Key Vault.  

```powershell
Invoke-RestMethod -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}
```

A resposta terá o seguinte aspeto: 

```powershell
  value       id                                                                                    attributes
  -----       --                                                                                    ----------
  'My Secret' https://mi-lab-vault.vault.azure.net/secrets/mi-test/50644e90b13249b584c44b9f712f2e51 @{enabled=True; created=16…
```

Depois de recuperar o segredo do Key Vault, pode utilizá-lo para autenticação num serviço que requeira um nome e uma palavra-passe.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando pretender limpar os recursos, visite o [portal Azure](https://portal.azure.com), selecione **Grupos de Recursos,** localize e selecione o grupo de recursos que foi criado no processo deste tutorial (como) e, em `mi-test` seguida, utilize o comando do **grupo de recursos Delete.**

Em alternativa, também pode fazê-lo através [do PowerShell ou do CLI](../../azure-resource-manager/management/delete-resource-group.md)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar uma identidade gerida pelo sistema Windows VM para aceder ao Cofre da Chave Azure.  Para saber mais sobre o Azure Key Vault, veja:

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)