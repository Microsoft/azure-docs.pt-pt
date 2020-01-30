---
title: Authenticate Azure Spring Cloud com cofre chave em ações GitHub
description: Como usar o cofre chave com fluxo de trabalho CI/CD para Azure Spring Cloud com Ações GitHub
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/20/2019
ms.openlocfilehash: efe8c1a2726054c54934926f652e338797d4efa1
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776550"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>Authenticate Azure Spring Cloud com cofre chave em ações GitHub
O cofre da chave é um lugar seguro para guardar as chaves. Os utilizadores empresariais precisam de armazenar credenciais para ambientes CI/CD no âmbito que controlam. A chave para obter credenciais no cofre principal deve ser limitada ao âmbito de recursos.  Tem acesso apenas ao âmbito do cofre chave, não a toda a mira Azure. É como uma chave que só pode abrir uma caixa forte, não uma chave mestre que pode abrir todas as portas de um edifício. É uma forma de obter uma chave com outra chave, o que é útil num fluxo de trabalho cicd. 

## <a name="generate-credential"></a>Gerar Credencial
Para gerar uma chave para aceder ao cofre chave, execute o comando abaixo na sua máquina local:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
O âmbito especificado pelo parâmetro `--scopes` limita o acesso chave ao recurso.  Só pode aceder à caixa forte.

Com resultados:
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "activeDirectoryGraphResourceId": "https://graph.windows.net/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Em seguida, guarde os resultados para os **segredos** do GitHub, conforme descrito no [seu repositório GitHub e autenticar com Azure](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate).

## <a name="add-access-policies-for-the-credential"></a>Adicionar Políticas de Acesso para a Credencial
A credencial que criou acima só pode obter informações gerais sobre o Cofre chave, não sobre o conteúdo que armazena.  Para obter segredos guardados no Cofre chave, precisa de definir políticas de acesso para a credencial.

Vá ao painel de instrumentos **key vault** no portal Azure, clique no menu de controlo de **acesso** e abra o separador de **atribuições de funções.** Selecione **Apps** para **Tipo** e `This resource` para **âmbito**.  Deve ver a credencial que criou em passo anterior:

 ![Definir política de acesso](./media/github-actions/key-vault1.png)

Copiar o nome credencial, por exemplo, `azure-cli-2020-01-19-04-39-02`. Abra o menu de políticas de **acesso,** clique em **+Adicionar link política de acesso.**  Selecione `Secret Management` para **modelo,** em seguida, selecione **Principal**. Colar o nome da credencial no **/Principal** Selecione a caixa de **entrada:**

 ![Selecione](./media/github-actions/key-vault2.png)

 Clique no botão **Adicionar** no diálogo da **política de acesso adicionar** e, em seguida, clique em **Guardar**.

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
    "activeDirectoryGraphResourceId": "https://graph.windows.net/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Copie toda a corda JSON.  Bo de volta ao painel **key vault.** Abra o menu **Segredos** e, em seguida, clique no botão **Geração/Importação.** Insera o nome secreto, como `AZURE-CRENDENTIALS-FOR-SPRING`. Colar a corda credencial JSON à caixa de entrada **Value.** Pode notar que a caixa de entrada de valor é um campo de texto de uma linha, em vez de uma área de texto de várias linhas.  Pode colar a corda JSON completa.

 ![Credencial de âmbito completo](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>Combine credenciais em Ações GitHub
Delineie as credenciais utilizadas quando o gasoduto CICD executa:

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
        keyvault: "zlhe-test"
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
* [Ações gitHub da nuvem de primavera](./spring-cloud-howto-github-actions.md)