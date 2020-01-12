---
title: Criptografar dados de implantação
description: Saiba mais sobre a criptografia de dados persistidos para seus recursos de instância de contêiner e como criptografar os dados com uma chave gerenciada pelo cliente
ms.topic: article
ms.date: 01/10/2020
ms.author: danlep
ms.openlocfilehash: 146effd7f1a7ad1ddd94886d1a79e2914bd1c94b
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75904214"
---
# <a name="encrypt-deployment-data"></a>Criptografar dados de implantação

Ao executar recursos de ACI (instâncias de contêiner do Azure) na nuvem, o serviço ACI coleta e mantém os dados relacionados aos seus contêineres. O ACI criptografa esses dados automaticamente quando eles são persistidos na nuvem. Essa criptografia protege seus dados para ajudar a atender aos compromissos de segurança e conformidade de sua organização. O ACI também oferece a opção de criptografar esses dados com sua própria chave, proporcionando a você maior controle sobre os dados relacionados às implantações do ACI.

## <a name="about-aci-data-encryption"></a>Sobre a criptografia de dados do ACI 

Os dados no ACI são criptografados e descriptografados usando a criptografia AES de 256 bits. Ele está habilitado para todas as implantações de ACI e você não precisa modificar sua implantação ou contêineres para aproveitar essa criptografia. Isso inclui metadados sobre a implantação, as variáveis de ambiente, as chaves que estão sendo passadas para seus contêineres e os logs persistiram depois que os contêineres são interrompidos para que você possa vê-los. A criptografia não afeta o desempenho do grupo de contêineres e não há nenhum custo adicional para a criptografia.

## <a name="encryption-key-management"></a>Gerenciamento de chaves de criptografia

Você pode contar com chaves gerenciadas pela Microsoft para a criptografia de seus dados de contêiner ou pode gerenciar a criptografia com suas próprias chaves. A tabela a seguir compara estas opções: 

|    |    Chaves gerenciadas pela Microsoft     |     Chaves gerenciadas pelo cliente     |
|----|----|----|
|    Operações de criptografia/descriptografia    |    Azure    |    Azure    |
|    Armazenamento de chaves    |    Repositório de chaves da Microsoft    |    Azure Key Vault    |
|    Responsabilidade de rotação de chave    |    Microsoft    |    Cliente    |
|    Acesso à chave    |    Somente Microsoft    |    Microsoft, cliente    |

O restante do documento aborda as etapas necessárias para criptografar os dados de implantação do ACI com sua chave (chave gerenciada pelo cliente). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>Criptografar dados com uma chave gerenciada pelo cliente

### <a name="create-service-principal-for-aci"></a>Criar entidade de serviço para ACI

A primeira etapa é garantir que seu [locatário do Azure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) tenha uma entidade de serviço atribuída para conceder permissões ao serviço de instâncias de contêiner do Azure. 

O seguinte comando da CLI irá configurar o SP ACI em seu ambiente do Azure:

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

A saída da execução deste comando deve mostrar uma entidade de serviço que foi configurada com "displayName": "serviço de instância de contêiner do Azure".

### <a name="create-a-key-vault-resource"></a>Criar um recurso de Key Vault

Crie um Azure Key Vault usando [portal do Azure](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault), [CLI](https://docs.microsoft.com/azure/key-vault/quick-create-cli)ou [PowerShell](https://docs.microsoft.com/azure/key-vault/quick-create-powershell). 

Para as propriedades do cofre de chaves, use as seguintes diretrizes: 
* Nome: é necessário um nome exclusivo. 
* Assinatura: escolha uma assinatura.
* Em grupo de recursos, escolha um grupo de recursos existente ou crie um novo e insira um nome de grupo de recursos.
* No menu suspenso local, escolha um local.
* Você pode deixar as outras opções para seus padrões ou escolher com base em requisitos adicionais.

> [!IMPORTANT]
> Ao usar chaves gerenciadas pelo cliente para criptografar um modelo de implantação ACI, é recomendável que as duas propriedades a seguir sejam definidas no cofre de chaves, exclusão reversível e não sejam limpas. Essas propriedades não são habilitadas por padrão, mas podem ser habilitadas usando o PowerShell ou CLI do Azure em um cofre de chaves novo ou existente.

### <a name="generate-a-new-key"></a>Gerar uma nova chave 

Depois que o cofre de chaves for criado, navegue até o recurso em portal do Azure. No menu de navegação à esquerda da folha de recursos, em configurações, clique em **chaves**. Na exibição de "chaves", clique em "gerar/importar" para gerar uma nova chave. Use qualquer nome exclusivo para essa chave e outras preferências com base em seus requisitos. 

![Gerar uma nova chave](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>Definir política de acesso

Crie uma nova política de acesso para permitir que o serviço ACI acesse sua chave.

* Depois que a chave tiver sido gerada, volte na folha de recursos do cofre de chaves, em configurações, clique em **políticas de acesso**.
* Na página "políticas de acesso" do cofre de chaves, clique em **Adicionar política de acesso**.
* Defina as *permissões de chave* para incluir a chave **Get** e a **desencapsulamento** ![definir permissões de chave](./media/container-instances-encrypt-data/set-key-permissions.png)
* Para *selecionar entidade de segurança*, selecione **serviço de instância de contêiner do Azure**
* Clique em **Adicionar** na parte inferior 

A política de acesso agora deve aparecer nas políticas de acesso do cofre de chaves.

![Nova política de acesso](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>Modificar seu modelo de implantação JSON

> [!IMPORTANT]
> A criptografia de dados de implantação com uma chave gerenciada pelo cliente está disponível na versão mais recente da API (2019-12-01) que está sendo distribuída no momento. Especifique essa versão de API em seu modelo de implantação. Se você tiver problemas com isso, entre em contato com o suporte do Azure.

Depois que a chave do Key Vault e a política de acesso forem configuradas, adicione a propriedade a seguir ao seu modelo de implantação do ACI. Você pode aprender mais sobre a implantação de recursos do ACI com um modelo no [tutorial: implantar um grupo de vários contêineres usando um modelo do Resource Manager](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 

Especificamente, na seção Propriedades do grupo de contêineres do modelo de implantação, adicione um "EncryptionProperties", que contém os seguintes valores:
* vaultBaseUrl: o nome DNS do cofre de chaves, pode ser encontrado na folha visão geral do recurso do cofre de chaves no portal
* keyName: o nome da chave gerada anteriormente
* keyversion: a versão atual da chave. Isso pode ser encontrado ao clicar na chave em si (em "chaves" na seção de configurações do recurso do cofre de chaves)


```json
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "encryptionProperties": {
                "vaultBaseUrl": "https://example.vault.azure.net",
                "keyName": "acikey",
                "keyVersion": "xxxxxxxxxxxxxxxx"
            },
            "containers": {
                [...]
            }
        }
    }
]
```

### <a name="deploy-your-resources"></a>Implantar seus recursos

Se você criou e editou o arquivo de modelo em sua área de trabalho, você pode carregá-lo em seu diretório Cloud Shell arrastando o arquivo para ele. 

Crie um grupo de recursos com o comando [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implante o modelo com o comando [AZ Group Deployment Create][az-group-deployment-create] .

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

Dentro de alguns segundos, deverá receber uma resposta inicial do Azure. Depois que a implantação for concluída, todos os dados relacionados a ela persistidos pelo serviço ACI serão criptografados com a chave que você forneceu.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create