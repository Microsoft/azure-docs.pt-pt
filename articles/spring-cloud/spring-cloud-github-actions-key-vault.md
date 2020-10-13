---
title: Autenticar nuvem de primavera azure com cofre chave em ações do GitHub
description: Como usar o cofre-chave com fluxo de trabalho CI/CD para Azure Spring Cloud com ações do GitHub
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java
ms.openlocfilehash: 995d10b3c7064e462500e0bec4d5d8aa010afe64
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90888782"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>Autenticar nuvem de primavera azure com cofre chave em ações do GitHub

**Este artigo aplica-se a:** ✔️ Java ✔️ C #

O cofre é um lugar seguro para guardar as chaves. Os utilizadores empresariais precisam de armazenar credenciais para ambientes CI/CD no âmbito que controlam. A chave para obter credenciais no cofre-chave deve limitar-se ao âmbito de recursos.  Tem acesso apenas ao alcance do cofre, não a toda a mira do Azure. É como uma chave que só pode abrir uma caixa forte e não uma chave mestra que pode abrir todas as portas de um edifício. É uma forma de obter uma chave com outra chave, que é útil num fluxo de trabalho CICD. 

## <a name="generate-credential"></a>Gerar Credencial
Para gerar uma chave para aceder ao cofre de chaves, execute o comando abaixo na sua máquina local:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
O âmbito especificado pelo `--scopes` parâmetro limita o acesso da chave ao recurso.  Só pode aceder à caixa forte.

Com resultados:
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Em seguida, guarde os resultados para **os segredos** do GitHub, conforme descrito no [set up your GitHub repository and authenticate with Azure](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate).

## <a name="add-access-policies-for-the-credential"></a>Adicionar Políticas de Acesso para a Credencial
A credencial que criou acima só pode obter informações gerais sobre o Cofre de Chaves, e não sobre o conteúdo que armazena.  Para guardar segredos no Cofre de Chaves, precisa de definir políticas de acesso para a credencial.

Vá ao painel **key Vault** no portal Azure, clique no menu de **controlo access** e, em seguida, abra o **separador atribuições de Função.** Selecione **Apps** for **Type** e `This resource` para o **âmbito**.  Deve ver a credencial que criou no passo anterior:

 ![Definir política de acesso](./media/github-actions/key-vault1.png)

Copiar o nome credencial, por exemplo, `azure-cli-2020-01-19-04-39-02` . Abra o menu **de políticas de acesso,** clique em +Adicionar link **Política de Acesso.**  Selecione `Secret Management` para **Modelo**e, em seguida, selecione **Principal**. Cole o nome credencial na caixa de entrada **Principal** / **Select:**

 ![Selecione](./media/github-actions/key-vault2.png)

 Clique no botão **Adicionar** o diálogo da política de **acesso ao adicionar** e, em seguida, clique em **Guardar**.

## <a name="generate-full-scope-azure-credential"></a>Gerar credencial Azure de âmbito completo
Esta é a chave principal para abrir todas as portas do edifício. O procedimento é semelhante ao passo anterior, mas aqui mudamos o âmbito para gerar a chave principal:

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

Mais uma vez, resultados:
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Copie toda a cadeia JSON.  Bo de volta ao painel **key vault.** Abra o menu **Segredos** e, em seguida, clique no botão **Gerar/Importar.** Insira o nome secreto, tal como `AZURE-CREDENTIALS-FOR-SPRING` . Cole a cadeia credencial JSON na caixa de entrada **Value.** Pode notar que a caixa de entrada de valor é um campo de texto de uma linha, em vez de uma área de texto de várias linhas.  Pode colar a corda JSON completa.

 ![Credencial de âmbito total](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>Combine credenciais em ações do GitHub
Definir as credenciais utilizadas quando o gasoduto CICD executa:

```
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}           # Strong box key you generated in the first step
    - uses: Azure/get-keyvault-secrets@v1.0
      with:
        keyvault: "<Your Key Vault Name>"
        secrets: "AZURE-CREDENTIALS-FOR-SPRING"           # Master key to open all doors in the building
      id: keyvaultaction
    - uses: azure/login@v1
      with:
        creds: ${{ steps.keyvaultaction.outputs.AZURE-CREDENTIALS-FOR-SPRING }}
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud             # Spring CLI commands from here
          az spring-cloud list

```

## <a name="next-steps"></a>Passos seguintes
* [Ações do GitHub da Nuvem de primavera](./spring-cloud-howto-github-actions.md)
