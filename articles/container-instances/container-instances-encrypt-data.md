---
title: Encriptar dados de implementação
description: Saiba mais sobre encriptação de dados persistidos para os recursos da instância do seu recipiente e como encriptar os dados com uma chave gerida pelo cliente
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: ad232c5d9df9f6bfae3a79dbd72e2c68143be949
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79080365"
---
# <a name="encrypt-deployment-data"></a>Encriptar dados de implementação

Ao executar os recursos da Azure Container Instances (ACI) na nuvem, o serviço ACI recolhe e persiste dados relacionados com os seus contentores. O ACI encripta automaticamente estes dados quando é persistido na nuvem. Esta encriptação protege os seus dados para ajudar a cumprir os compromissos de segurança e conformidade da sua organização. O ACI também lhe dá a opção de encriptar estes dados com a sua própria chave, dando-lhe um maior controlo sobre os dados relacionados com as suas implementações de ACI.

## <a name="about-aci-data-encryption"></a>Sobre encriptação de dados DaCI 

Os dados em ACI são encriptados e desencriptados utilizando encriptação AES de 256 bits. Está ativado para todas as implementações de ACI, e não precisa de modificar a sua implementação ou contentores para tirar partido desta encriptação. Isto inclui metadados sobre a implementação, variáveis ambientais, chaves que estão a ser passadas para os seus contentores, e os registos persistiram após a paragem dos seus contentores para que ainda possa vê-los. A encriptação não afeta o desempenho do grupo de contentores e não existe um custo adicional para a encriptação.

## <a name="encryption-key-management"></a>Gestão de chaves de encriptação

Pode contar com chaves geridas pela Microsoft para a encriptação dos dados do seu contentor, ou pode gerir a encriptação com as suas próprias chaves. A tabela seguinte compara estas opções: 

|    |    Chaves geridas pela Microsoft     |     Chaves geridas pelo cliente     |
|----|----|----|
|    Operações de encriptação/desencriptação    |    Azure    |    Azure    |
|    Armazenamento de chaves    |    Loja chave da Microsoft    |    Azure Key Vault    |
|    Responsabilidade de rotação chave    |    Microsoft    |    Cliente    |
|    Acesso chave    |    Apenas microsoft    |    Microsoft, Cliente    |

O resto do documento cobre os passos necessários para encriptar os seus dados de implementação de ACI com a sua chave (chave gerida pelo cliente). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>Criptografe dados com uma chave gerida pelo cliente

### <a name="create-service-principal-for-aci"></a>Criar Diretor de Serviço para ACI

O primeiro passo é garantir que o seu [inquilino Azure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) tenha um diretor de serviço atribuído para a concessão de permissões ao serviço Deocorrências de Contentores Azure. 

> [!IMPORTANT]
> Para executar o seguinte comando e criar um diretor de serviço com sucesso, confirme que tem permissões para criar diretores de serviço no seu inquilino.
>

O seguinte comando CLI configurará o ACI SP no seu ambiente Azure:

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

A saída de executar este comando deve mostrar-lhe um diretor de serviço que tenha sido configurado com "displayName": "Serviço de Instância de Contentores Azure".

Caso não consiga criar com sucesso o diretor de serviço:
* confirmar que você tem permissões para fazê-lo em seu inquilino
* verifique se já existe um diretor de serviço no seu inquilino para ser destacado para o ACI. Pode fazê-lo executando `az ad sp show --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9` e usar esse diretor de serviço

### <a name="create-a-key-vault-resource"></a>Criar um recurso key vault

Crie um Cofre de Chave Azure utilizando [o portal Azure,](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) [CLI](https://docs.microsoft.com/azure/key-vault/quick-create-cli)ou [PowerShell](https://docs.microsoft.com/azure/key-vault/quick-create-powershell). 

Para as propriedades do seu cofre chave, use as seguintes diretrizes: 
* Nome: É necessário um nome único. 
* Subscrição: Escolha uma subscrição.
* No Grupo de Recursos, escolha um grupo de recursos existente, ou crie novos e introduza um nome de grupo de recursos.
* No menu de retirada de localização, escolha um local.
* Pode deixar as outras opções para os seus incumprimentos ou escolher com base em requisitos adicionais.

> [!IMPORTANT]
> Ao utilizar chaves geridas pelo cliente para encriptar um modelo de implementação ACI, recomenda-se que as duas propriedades seguintes sejam definidas no cofre da chave, Soft Delete e Não Expurgar. Estas propriedades não são ativadas por padrão, mas podem ser ativadas usando powerShell ou Azure CLI em um cofre de chave novo ou existente.

### <a name="generate-a-new-key"></a>Gerar uma nova chave 

Assim que o seu cofre chave for criado, navegue para o recurso no portal Azure. No menu de navegação à esquerda da lâmina de recurso, em Definições, clique em **Teclas**. Na vista para "Keys", clique em "Generate/Import" para gerar uma nova tecla. Utilize qualquer Nome único para esta chave e quaisquer outras preferências com base nos seus requisitos. 

![Gerar uma nova chave](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>Definir política de acesso

Crie uma nova política de acesso para permitir que o serviço ACI aceda à sua Chave.

* Uma vez gerada a sua chave, de volta à sua lâmina de recurso de cofre chave, em Definições, clique em Políticas de **Acesso**.
* Na página "Políticas de Acesso" para o seu cofre de chaves, clique em **Adicionar Política**de Acesso .
* Detete as *permissões chave* para incluir **obter** e **desembrulhar as** ![definir permissões de chave](./media/container-instances-encrypt-data/set-key-permissions.png)
* Para *selecionar o principal,* selecione Serviço de **Instância de Contentores Azure**
* Clique em **Adicionar** na parte inferior 

A política de acesso deve agora aparecer nas políticas de acesso do seu cofre chave.

![Nova política de acesso](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>Modifique o seu modelo de implementação JSON

> [!IMPORTANT]
> Encriptar dados de implementação com uma chave gerida pelo cliente está disponível na versão Mais recente da API (2019-12-01) que está atualmente a ser lançada. Especifique esta versão API no seu modelo de implementação. Se tiver algum problema com isto, contacte o Azure Support.

Assim que a chave do cofre e a política de acesso estiverem configuradas, adicione as seguintes propriedades ao seu modelo de implementação ACI. Saiba mais sobre a implementação de recursos ACI com um modelo no [Tutorial: Implante um grupo multi-contentor usando um modelo](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group)de Gestor de Recursos . 
* Em `resources`, `apiVersion` definida para `2019-12-01`.
* Sob a secção de propriedades do grupo de contentores do modelo de implantação, adicione um `encryptionProperties`, que contenha os seguintes valores:
  * `vaultBaseUrl`: o Nome DNS do seu cofre chave, pode ser encontrado na lâmina geral do recurso do cofre chave no Portal
  * `keyName`: o nome da chave gerada anteriormente
  * `keyVersion`: a versão atual da chave. Isto pode ser encontrado clicando na própria chave (em "Keys" na secção Definições do seu recurso de cofre chave)
* Sob as propriedades do grupo de contentores, adicione uma propriedade `sku` com valor `Standard`. A propriedade `sku` é exigida na versão API 2019-12-01.

O seguinte modelo de corte mostra estas propriedades adicionais para encriptar dados de implementação:

```json
[...]
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
            "sku": "Standard",
            "containers": {
                [...]
            }
        }
    }
]
```

Segue-se um modelo completo, adaptado a partir do modelo no [Tutorial: Implante um grupo multi-contentorusando um modelo](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group)de Gestor de Recursos . 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
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
        "sku": "Standard",  
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

### <a name="deploy-your-resources"></a>Implante os seus recursos

Se criou e editou o ficheiro de modelo no seu ambiente de trabalho, pode carregá-lo para o seu diretório Cloud Shell arrastando o ficheiro para o mesmo. 

Crie um grupo de recursos com o comando [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Desloque o modelo com a implementação do [grupo AZ criar][az-group-deployment-create] comando.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

Dentro de alguns segundos, deverá receber uma resposta inicial do Azure. Uma vez concluída a implementação, todos os dados relacionados com o mesmo persistiram pelo serviço ACI serão encriptados com a chave que forneceu.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
