---
title: Visão geral da recuperação do Azure Key Vault Microsoft Docs
description: As funcionalidades de recuperação do cofre de chaves foram concebidas para evitar a eliminação acidental ou maliciosa do cofre e segredos, chaves e certificados armazenados dentro do cofre de chaves.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: ShaneBala-keyvault
ms.author: sudbalas
manager: ravijan
ms.date: 12/15/2020
ms.openlocfilehash: 485da2230de80150c9a5d13b262d1857c8c172fc
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587116"
---
# <a name="how-to-enable-soft-delete-and-purge-protection"></a>Como permitir a eliminação suave e a proteção de purga

Este artigo abrange duas funcionalidades de recuperação do Cofre da Chave Azure, proteção de eliminação suave e purga. Este documento fornece uma visão geral destas funcionalidades, e mostra-lhe como geri-las através do portal Azure CLI e Azure PowerShell.

## <a name="what-are-soft-delete-and-purge-protection"></a>O que são a proteção de eliminação e purga suave

A proteção de eliminação e purga suave são duas características diferentes de recuperação do cofre.
> [!IMPORTANT]
> Ligar a eliminação suave é fundamental para garantir que os seus cofres e credenciais estão protegidos contra a eliminação acidental. No entanto, ligar o soft delete é considerado uma mudança de rutura porque pode exigir que altere a sua lógica de aplicação ou forneça permissões adicionais aos seus principais serviços. Antes de ligar suavemente, elimine usando as instruções abaixo, certifique-se de que a sua aplicação é compatível com a alteração utilizando este documento [ **aqui**.](soft-delete-change.md)

**O soft delete** foi concebido para evitar a eliminação acidental do cofre e chaves, segredos e certificados armazenados dentro do cofre da chave. Pense em apagar suavemente como um caixote de reciclagem. Quando eliminar um cofre de teclas ou um objeto de cofre chave, permanecerá recuperável durante um período de retenção configurável do utilizador ou por um padrão de 90 dias. As abóbadas-chave no estado suave apagado também podem ser **purgadas,** o que significa que são permanentemente eliminadas. Isto permite-lhe recriar cofres-chave e objetos-chave com o mesmo nome. Tanto a recuperação como a eliminação de cofres e objetos requerem permissões elevadas de política de acesso. **Uma vez ativada a eliminação suave, não pode ser desativada.**

É importante notar que **os nomes do cofre são globalmente únicos,** por isso não será capaz de criar um cofre com o mesmo nome que um cofre chave no estado suave apagado. Da mesma forma, os nomes das chaves, segredos e certificados são únicos dentro de um cofre chave. Não será capaz de criar um segredo, chave ou certificado com o mesmo nome que outro no estado suave apagado.

**A proteção de purga** foi concebida para evitar a eliminação do cofre, chaves, segredos e certificados por um infiltrado malicioso. Pense nisto como um caixote de reciclagem com uma fechadura baseada no tempo. Pode recuperar itens em qualquer ponto durante o período de retenção configurável. **Não poderá apagar ou limpar permanentemente um cofre de teclas até que o período de retenção dedespere.** Uma vez decorrido o período de retenção, o cofre da chave ou o objeto do cofre da chave serão purgados automaticamente.

> [!NOTE]
> A Proteção de Purga foi concebida de modo a que nenhuma função ou permissão do administrador possa sobrepor-se, desativar ou contornar a proteção da purga. **Uma vez ativada a proteção contra a purga, não pode ser desativada ou ultrapassada por ninguém, incluindo a Microsoft.** Isto significa que deve recuperar um cofre de teclas apagado ou esperar que o período de retenção dedante antes de reutilizar o nome do cofre da chave.

# <a name="azure-portal"></a>[Portal do Azure](#tab/azure-portal)

## <a name="verify-if-soft-delete-is-enabled-on-a-key-vault-and-enable-soft-delete"></a>Verifique se a eliminação suave está ativada num cofre de teclas e ative a eliminação suave

1. Inicie sessão no portal do Azure.
1. Selecione o cofre da chave.
1. Clique na lâmina "Propriedades".
1. Verifique se o botão de rádio ao lado do soft-delete está definido para "Ativar a recuperação".
1. Se a eliminação suave não estiver ativada no cofre da chave, clique no botão de rádio para ativar a eliminação suave e clique em "Guardar".

:::image type="content" source="../media/key-vault-recovery-1.png" alt-text="Em Propriedades, a eliminação de soft-delete é realçada, assim como o valor para o ativar.":::

## <a name="grant-access-to-a-service-principal-to-purge-and-recover-deleted-secrets"></a>Conceder acesso a um diretor de serviço para purgar e recuperar segredos apagados

1. Inicie sessão no portal do Azure.
1. Selecione o cofre da chave.
1. Clique na lâmina "Política de Acesso".
1. Na tabela, encontre a linha do diretor de segurança a que deseja conceder acesso (ou adicionar um novo diretor de segurança).
1. Clique na entrega para obter chaves, certificados e segredos.
1. Percorra a parte inferior da queda e clique em "Recuperar" e "Purgar"
1. Os diretores de segurança também precisarão de obter e listar a funcionalidade para executar a maioria das operações.

:::image type="content" source="../media/key-vault-recovery-2.png" alt-text="No painel de navegação à esquerda, destacam-se as políticas de acesso. Nas políticas de Acesso, é apresentada a lista de abandono de Posições Secretas e são selecionados quatro itens: Obter, Lista, Recuperar e Purgar.":::

## <a name="list-recover-or-purge-a-soft-deleted-key-vault"></a>Liste, recupere ou purgue um cofre de chaves apagado

1. Inicie sessão no portal do Azure.
1. Clique na barra de pesquisa no topo da página.
1. Em "Serviços Recentes" clique em "Key Vault". Não clique num cofre individual.
1. No topo do ecrã clique na opção de "Gerir cofres apagados"
1. Um painel de contexto abrir-se-á no lado direito do ecrã.
1. Selecione a sua subscrição.
1. Se o cofre da chave tiver sido apagado suavemente, aparecerá no painel de contexto à direita.
1. Se houver demasiados cofres, pode clicar em "Load More" na parte inferior do painel de contexto ou utilizar CLI ou PowerShell para obter os resultados.
1. Assim que encontrar o cofre que deseja recuperar ou purgar, selecione a caixa de verificação ao lado.
1. Selecione a opção de recuperação na parte inferior do painel de contexto se quiser recuperar o cofre da chave.
1. Selecione a opção de purga se quiser eliminar permanentemente o cofre da chave.

:::image type="content" source="../media/key-vault-recovery-3.png" alt-text="Nos cofres key, a opção de cofres eliminados do Manage é realçada.":::

:::image type="content" source="../media/key-vault-recovery-4.png" alt-text="No Manage eliminados cofres-chave, o único cofre de chaves listado é realçado e selecionado, e o botão Recuperar é realçado.":::

## <a name="list-recover-or-purge-soft-deleted-secrets-keys-and-certificates"></a>Lista, recuperar ou purgar segredos, chaves e certificados suaves apagados

1. Inicie sessão no portal do Azure.
1. Selecione o cofre da chave.
1. Selecione a lâmina correspondente ao tipo secreto que pretende gerir (chaves, segredos ou certificados).
1. Na parte superior do ecrã, clique em "Gerir eliminado (chaves, segredos ou certificados)
1. Aparecerá um painel de contexto no lado direito do ecrã.
1. Se o seu segredo, chave ou certificado não aparecer na lista, não está no estado de apagação suave.
1. Selecione o segredo, chave ou certificado que gostaria de gerir.
1. Selecione a opção de recuperar ou purgar na parte inferior do painel de contexto.

:::image type="content" source="../media/key-vault-recovery-5.png" alt-text="Em Teclas, destaca-se a opção &quot;Gerir&quot; as teclas eliminadas.":::

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

## <a name="key-vault-cli"></a>Cofre-chave (CLI)

* Verifique se um cofre-chave tem a eliminação suave ativada

    ```azurecli
    az keyvault show --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Ativar a eliminação suave no cofre de chaves

    Todas as novas abóbadas-chave têm exclusão suave ativada por padrão. Se atualmente tiver um cofre de teclas que não tenha uma eliminação suave ativada, utilize o seguinte comando para ativar a eliminação suave.

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-soft-delete true
    ```

* Eliminar cofre de teclas (recuperável se a eliminação suave estiver ativada)

    ```azurecli
    az keyvault delete --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Listar todos os cofres-chave apagados suaves

    ```azurecli
    az keyvault list-deleted --subscription {SUBSCRIPTION ID} --resource-type vault
    ```

* Recuperar o cofre de chaves comvidade apagada

    ```azurecli
    az keyvault recover --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* Purgue o cofre da chave com eliminação suave **(AVISO! ESTA OPERAÇÃO IRÁ ELIMINAR PERMANENTEMENTE O SEU COFRE DE CHAVES)**

    ```azurecli
    az keyvault purge --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* Ativar a proteção da purga no cofre de chaves

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-purge-protection true
    ```

## <a name="certificates-cli"></a>Certificados (CLI)

* Conceder acesso à purga e recuperar certificados

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --certificate-permissions recover purge
    ```

* Apagar certificado

    ```azurecli
    az keyvault certificate delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Lista de certificados eliminados

    ```azurecli
    az keyvault certificate list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Recuperar certificado eliminado

    ```azurecli
    az keyvault certificate recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Purgue o certificado de eliminação suave **(AVISO! ESTA OPERAÇÃO IRÁ ELIMINAR PERMANENTEMENTE O SEU CERTIFICADO)**

    ```azurecli
    az keyvault certificate purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

## <a name="keys-cli"></a>Chaves (CLI)

* Conceder acesso à purga e recuperar chaves

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --key-permissions recover purge
    ```

* Tecla Delete

    ```azurecli
    az keyvault key delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Lista de chaves eliminadas

    ```azurecli
    az keyvault key list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Recuperar chave eliminada

    ```azurecli
    az keyvault key recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Purgue a tecla de eliminação suave **(AVISO! ESTA OPERAÇÃO IRÁ ELIMINAR PERMANENTEMENTE A SUA CHAVE)**

    ```azurecli
    az keyvault key purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

## <a name="secrets-cli"></a>Segredos (CLI)

* Conceder acesso à purga e recuperar segredos

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --secret-permissions recover purge
    ```

* Apagar segredo

    ```azurecli
    az keyvault secret delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Lista de segredos eliminados

    ```azurecli
    az keyvault secret list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Recuperar segredo apagado

    ```azurecli
    az keyvault secret recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Purgue o segredo apagado **(AVISO! ESTA OPERAÇÃO IRÁ APAGAR PERMANENTEMENTE O SEU SEGREDO)**

    ```azurecli
    az keyvault secret purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

## <a name="key-vault-powershell"></a>Cofre-chave (PowerShell)

* Verifique se um cofre-chave tem a eliminação suave ativada

    ```powershell
    Get-AzKeyVault -VaultName "ContosoVault"
    ```

* Apagar cofre de chaves

    ```powershell
    Remove-AzKeyVault -VaultName 'ContosoVault'
    ```

* Listar todos os cofres-chave apagados suaves

    ```powershell
    Get-AzKeyVault -InRemovedState
    ```

* Recuperar o cofre de chaves comvidade apagada

    ```powershell
    Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
    ```

* Purgue o cofre de chaves apagado **suavemente (AVISO! ESTA OPERAÇÃO IRÁ ELIMINAR PERMANENTEMENTE O SEU COFRE DE CHAVES)**

    ```powershell
    Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
    ```

* Ativar a proteção da purga no cofre de chaves

    ```powershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="certificates-powershell"></a>Certificados (PowerShell)

* Conceder permissões para recuperar e purgar certificados

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToCertificates recover,purge
    ```

* Excluir um Certificado

  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

* Listar todos os certificados eliminados num cofre chave

  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

* Recuperar um certificado no estado apagado

  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

* Purgue um certificado de eliminação suave **(AVISO! ESTA OPERAÇÃO IRÁ ELIMINAR PERMANENTEMENTE O SEU CERTIFICADO)**

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState
  ```

## <a name="keys-powershell"></a>Teclas (PowerShell)

* Conceder permissões para recuperar e purgar chaves

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys recover,purge
    ```

* Apagar uma chave

  ```powershell
  Remove-AzKeyVaultKey -VaultName ContosoVault -Name 'MyKey'
  ```

* Listar todos os certificados eliminados num cofre chave

  ```powershell
  Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
  ```

* Para recuperar uma chave de apagação suave

    ```powershell
    Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
    ```

* Purgue uma chave de eliminação suave **(AVISO! ESTA OPERAÇÃO IRÁ ELIMINAR PERMANENTEMENTE A SUA CHAVE)**

    ```powershell
    Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
    ```

## <a name="secrets-powershell"></a>Segredos (PowerShell)

* Conceder permissões para recuperar e purgar segredos

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToSecrets recover,purge
    ```

* Excluir um segredo chamado SQLPassword

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

* Lista todos os segredos apagados em um cofre chave

  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

* Recuperar um segredo no estado apagado

  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

* Purgue um segredo em estado apagado **(AVISO! ESTA OPERAÇÃO IRÁ ELIMINAR PERMANENTEMENTE A SUA CHAVE)**

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```
