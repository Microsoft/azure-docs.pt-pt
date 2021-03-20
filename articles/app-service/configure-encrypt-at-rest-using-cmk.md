---
title: Criptografe a sua fonte de aplicação em repouso
description: Saiba como encriptar os dados da sua aplicação no Azure Storage e implemente-os como um ficheiro de pacote.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 5524b749b1e15342dd0133920d7190e33ced18ad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92146038"
---
# <a name="encryption-at-rest-using-customer-managed-keys"></a>Encriptação em repouso usando chaves geridas pelo cliente

Encriptar os dados de aplicações da sua aplicação web em repouso requer uma conta de armazenamento Azure e um Cofre de Chaves Azure. Estes serviços são utilizados quando executam a sua aplicação a partir de um pacote de implementação.

  - [O Azure Storage fornece encriptação em repouso](../storage/common/storage-service-encryption.md). Pode utilizar as chaves fornecidas pelo sistema ou as suas próprias chaves geridas pelo cliente. É aqui que os dados da sua aplicação são armazenados quando não estão a ser distribuídos numa aplicação web em Azure.
  - [Executar a partir de um pacote de implementação](deploy-run-package.md) é uma funcionalidade de implementação do Serviço de Aplicações. Permite-lhe implementar o conteúdo do seu site a partir de uma Conta de Armazenamento Azure utilizando um URL de Assinatura de Acesso Partilhado (SAS).
  - [As referências ao Cofre](app-service-key-vault-references.md) chave são uma funcionalidade de segurança do Serviço de Aplicações. Permite-lhe importar segredos no tempo de execução como configurações de aplicação. Utilize isto para encriptar o URL SAS da sua Conta de Armazenamento Azure.

## <a name="set-up-encryption-at-rest"></a>Configurar encriptação em repouso

### <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure

Em primeiro lugar, [crie uma conta de Armazenamento Azure](../storage/common/storage-account-create.md) e [criptografe-a com as teclas geridas pelo cliente](../storage/common/customer-managed-keys-overview.md). Assim que a conta de armazenamento for criada, utilize o [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) para carregar ficheiros de pacotes.

Em seguida, utilize o Explorador de Armazenamento para [gerar um SAS](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer). 

> [!NOTE]
> Guarde este URL SAS, este é usado mais tarde para permitir o acesso seguro do pacote de implementação em tempo de execução.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>Configurar a partir de um pacote da sua conta de armazenamento
  
Assim que fizer o upload do seu ficheiro para o armazenamento blob e tiver um URL SAS para o ficheiro, defina a definição de `WEBSITE_RUN_FROM_PACKAGE` aplicação para o URL SAS. O exemplo que se segue fá-lo utilizando o Azure CLI:

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

A adição desta definição de aplicação faz com que a sua aplicação web reinicie. Depois de a aplicação ter sido reiniciada, navegue na sua e certifique-se de que a aplicação começou a utilizar corretamente o pacote de implementação. Se a aplicação não tiver começado corretamente, consulte o [Guia de Resolução de Problemas da embalagem](deploy-run-package.md#troubleshooting).

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>Criptografe a definição da aplicação utilizando referências do Key Vault

Agora pode substituir o valor da definição de `WEBSITE_RUN_FROM_PACKAGE` aplicação por uma referência do Cofre de Chaves ao URL codificado pelo SAS. Isto mantém o URL SAS encriptado no Key Vault, que fornece uma camada extra de segurança.

1. Utilize o seguinte [`az keyvault create`](/cli/azure/keyvault#az-keyvault-create) comando para criar uma instância key vault.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. Siga [estas instruções para conceder acesso](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) à sua aplicação ao cofre da chave:

1. Utilize o seguinte [`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set) comando para adicionar o seu URL externo como segredo no seu cofre chave:   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  Utilize o seguinte [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) comando para criar a definição de `WEBSITE_RUN_FROM_PACKAGE` aplicação com o valor como referência do Cofre de Chaves ao URL externo:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    O `<secret-version>` estará na saída do comando `az keyvault secret set` anterior.

A atualização desta definição de aplicação faz com que a sua aplicação web reinicie. Depois de a aplicação ter sido reiniciada, navegue para ela certifique-se de que começou corretamente a utilizar a referência Key Vault.

## <a name="how-to-rotate-the-access-token"></a>Como rodar o token de acesso

É prática ser prática rodar periodicamente a chave SAS da sua conta de armazenamento. Para garantir que a aplicação web não tem acesso inadvertidamente livre, também deve atualizar o URL SAS em Key Vault.

1. Rode a tecla SAS navegando para a sua conta de armazenamento no portal Azure. Em **Definições**  >  **As teclas de acesso,** clique no ícone para rodar a tecla SAS.

1. Copie o novo URL SAS e use o seguinte comando para definir o URL SAS atualizado no seu cofre de chaves:

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. Atualize a referência do cofre chave na definição da sua aplicação para a nova versão secreta:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    O `<secret-version>` estará na saída do comando `az keyvault secret set` anterior.

## <a name="how-to-revoke-the-web-apps-data-access"></a>Como revogar o acesso de dados da aplicação web

Existem dois métodos para revogar o acesso da aplicação web à conta de armazenamento. 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>Rode a chave SAS para a conta de Armazenamento Azure

Se a chave SAS para a conta de armazenamento for rotativa, a aplicação web deixará de ter acesso à conta de armazenamento, mas continuará a funcionar com a última versão descarregada do ficheiro do pacote. Reinicie a aplicação web para limpar a última versão descarregada.

### <a name="remove-the-web-apps-access-to-key-vault"></a>Remova o acesso da aplicação web ao Key Vault

Pode revogar o acesso da aplicação web aos dados do site, desativando o acesso da aplicação web ao Key Vault. Para isso, remova a política de acesso para a identidade da aplicação web. Esta é a mesma identidade que criou anteriormente enquanto configurava referências de cofre chave.

## <a name="summary"></a>Resumo

Os ficheiros da sua aplicação estão agora encriptados em repouso na sua conta de armazenamento. Quando a sua aplicação web começa, recupera o URL SAS do seu cofre de chaves. Finalmente, a aplicação web carrega os ficheiros de aplicações a partir da conta de armazenamento. 

Se precisar de revogar o acesso da web à sua conta de armazenamento, pode revogar o acesso ao cofre de chaves ou rodar as chaves da conta de armazenamento, o que invalida o URL SAS.

## <a name="frequently-asked-questions"></a>Perguntas Mais Frequentes

### <a name="is-there-any-additional-charge-for-running-my-web-app-from-the-deployment-package"></a>Existe algum custo adicional para executar a minha aplicação web a partir do pacote de implementação?

Apenas o custo associado à Conta de Armazenamento Azure e quaisquer encargos de saída aplicáveis.

### <a name="how-does-running-from-the-deployment-package-affect-my-web-app"></a>Como é que correr a partir do pacote de implementação afeta a minha aplicação web?

- Executar a sua aplicação a partir do pacote de implementação faz `wwwroot/` apenas leitura. A sua aplicação recebe um erro quando tenta escrever para este diretório.
- Os formatos TAR e GZIP não são suportados.
- Esta função não é compatível com cache local.

## <a name="next-steps"></a>Passos seguintes

- [Referências do Cofre chave para o Serviço de Aplicações](app-service-key-vault-references.md)
- [Azure Storage encryption for data at rest](../storage/common/storage-service-encryption.md) (Encriptação do Armazenamento do Azure para dados inativos)