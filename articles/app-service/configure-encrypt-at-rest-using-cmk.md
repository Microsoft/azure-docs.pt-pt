---
title: Criptografe a sua fonte de aplicação em repouso
description: Criptografe os dados da sua aplicação no Armazenamento Azure e implemente-os como um ficheiro de pacote.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: b1cfd99e2cc7c87675c72f679139a4299486cca6
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79374904"
---
# <a name="encryption-at-rest-using-customer-managed-keys"></a>Encriptação em repouso usando chaves geridas pelo cliente

Encriptar os dados da aplicação da sua aplicação web em repouso requer uma Conta de Armazenamento Azure e um Cofre de Chave Azure. Estes serviços são utilizados quando executa a sua aplicação a partir de um pacote de implementação.

  - [O Armazenamento Azure fornece encriptação em repouso.](../storage/common/storage-service-encryption.md) Pode utilizar as chaves fornecidas pelo sistema ou as suas próprias chaves geridas pelo cliente. É aqui que os dados da sua aplicação são armazenados quando não está a ser gerido numa aplicação web em Azure.
  - [A partir de um pacote de implementação](deploy-run-package.md) é uma funcionalidade de implementação do Serviço de Aplicações. Permite-lhe implementar o conteúdo do seu site a partir de uma conta de armazenamento Azure utilizando um URL de Assinatura de Acesso Partilhado (SAS).
  - [As referências chave vault](app-service-key-vault-references.md) são uma característica de segurança do Serviço de Aplicações. Permite-lhe importar segredos no prazo de execução como definições de aplicação. Utilize isto para encriptar o URL SAS da sua Conta de Armazenamento Azure.

## <a name="set-up-encryption-at-rest"></a>Configurar encriptação em repouso

### <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure

Em primeiro lugar, [crie uma conta de Armazenamento Azure](../storage/common/storage-account-create.md) e [criptografe-a com chaves geridas pelo cliente](../storage/common/storage-service-encryption.md#customer-managed-keys-with-azure-key-vault). Assim que a conta de armazenamento for criada, utilize o [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) para carregar ficheiros de pacotes.

Em seguida, utilize o Explorador de Armazenamento para [gerar um SAS](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer). 

> [!NOTE]
> Guarde este URL SAS, este é utilizado mais tarde para permitir o acesso seguro do pacote de implementação em tempo de execução.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>Configure a partir de um pacote da sua conta de armazenamento
  
Assim que enviar o seu ficheiro para o armazenamento blob e tiver um URL SAS para o ficheiro, defina a definição de aplicação `WEBSITE_RUN_FROM_PACKAGE` para o URL SAS. O exemplo seguinte fá-lo utilizando o Azure CLI:

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

A adição desta definição de aplicação faz com que a sua aplicação web reinicie. Depois de a aplicação ter reiniciado, navegue para a aplicação e certifique-se de que a aplicação começou a utilizar corretamente o pacote de implementação. Se a aplicação não tiver começado corretamente, consulte a [Corrida do guia de resolução de problemas do pacote](deploy-run-package.md#troubleshooting).

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>Criptografe a definição de aplicação utilizando referências chave vault

Agora pode substituir o valor da definição de aplicação `WEBSITE_RUN_FROM_PACKAGE` por uma referência chave vault ao URL codificado pela SAS. Isto mantém o URL SAS encriptado no Cofre chave, que fornece uma camada extra de segurança.

1. Utilize o comando [`az keyvault create`](/cli/azure/keyvault#az-keyvault-create) seguinte para criar uma instância chave vault.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. Siga [estas instruções para dar acesso](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) à sua aplicação ao seu cofre chave:

1. Utilize o seguinte comando [`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set) para adicionar o seu URL externo como um segredo no seu cofre chave:   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  Utilize o seguinte comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) para criar a definição de aplicação `WEBSITE_RUN_FROM_PACKAGE` com o valor como referência do Cofre chave ao URL externo:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    O `<secret-version>` estará na saída do comando `az keyvault secret set` anterior.

Atualizar esta definição de aplicação faz com que a sua aplicação web reinicie. Depois de a aplicação ter reiniciado, procure-a para se certificar de que começou corretamente a utilizar a referência do Cofre chave.

## <a name="how-to-rotate-the-access-token"></a>Como rodar o símbolo de acesso

É uma boa prática rodar periodicamente a chave SAS da sua conta de armazenamento. Para garantir que a aplicação web não perde inadvertidamente o acesso, também deve atualizar o URL SAS no Cofre de Chaves.

1. Rode a tecla SAS navegando para a sua conta de armazenamento no portal Azure. Em **Definições** > **Teclas de acesso,** clique no ícone para rodar a tecla SAS.

1. Copie o novo URL SAS e utilize o seguinte comando para definir o URL SAS atualizado no seu cofre chave:

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

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>Rode a tecla SAS para a conta de armazenamento azure

Se a chave SAS para a conta de armazenamento for rotativa, a aplicação web deixará de ter acesso à conta de armazenamento, mas continuará a funcionar com a última versão descarregada do ficheiro do pacote. Reinicie a aplicação web para limpar a última versão descarregada.

### <a name="remove-the-web-apps-access-to-key-vault"></a>Remova o acesso da aplicação web ao Key Vault

Pode revogar o acesso da aplicação web aos dados do site, desativando o acesso da aplicação web ao Key Vault. Para tal, remova a política de acesso à identidade da aplicação web. Esta é a mesma identidade que criou anteriormente enquanto configura referências chave do cofre.

## <a name="summary"></a>Resumo

Os ficheiros de aplicação estão agora encriptados em repouso na sua conta de armazenamento. Quando a sua aplicação web começa, ele recupera o URL SAS do seu cofre chave. Finalmente, a aplicação web carrega os ficheiros da aplicação a partir da conta de armazenamento. 

Se precisar de revogar o acesso da aplicação web à sua conta de armazenamento, pode revogar o acesso ao cofre da chave ou rodar as chaves da conta de armazenamento, o que invalida o URL SAS.

## <a name="frequently-asked-questions"></a>Perguntas Mais Frequentes

### <a name="is-there-any-additional-charge-for-running-my-web-app-from-the-deployment-package"></a>Há algum custo adicional para executar a minha aplicação web a partir do pacote de implementação?

Apenas o custo associado à Conta de Armazenamento Azure e quaisquer encargos de saída aplicáveis.

### <a name="how-does-running-from-the-deployment-package-affect-my-web-app"></a>Como é que o funcionamento do pacote de implementação afeta a minha aplicação web?

- Executar a sua aplicação a partir do pacote de implementação torna `wwwroot/` apenas leitura. A sua aplicação recebe um erro quando tenta escrever para este diretório.
- Os formatos TAR e GZIP não são suportados.
- Esta funcionalidade não é compatível com a cache local.

## <a name="next-steps"></a>Passos seguintes

- [Referências chave vault para serviço de aplicações](app-service-key-vault-references.md)
- [Encriptação azure storage para dados em repouso](../storage/common/storage-service-encryption.md)
