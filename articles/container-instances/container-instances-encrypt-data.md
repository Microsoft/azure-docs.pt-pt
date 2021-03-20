---
title: Encriptar dados de implementação
description: Saiba mais sobre a encriptação de dados persistidos para os recursos de exemplo do seu contentor e como encriptar os dados com uma chave gerida pelo cliente
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 1b73ce5c994231a1c7b2f26ad702f2ad5880ba44
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94686281"
---
# <a name="encrypt-deployment-data"></a>Encriptar dados de implementação

Ao executar os recursos do Azure Container Instances (ACI) na nuvem, o serviço ACI recolhe e persiste dados relacionados com os seus contentores. O ACI encripta automaticamente estes dados quando estes são persistidos na nuvem. Esta encriptação protege os seus dados para ajudar a cumprir os compromissos de segurança e conformidade da sua organização. O ACI também lhe dá a opção de encriptar estes dados com a sua própria chave, dando-lhe um maior controlo sobre os dados relacionados com as suas implementações de ACI.

## <a name="about-aci-data-encryption"></a>Sobre encriptação de dados ACI 

Os dados em ACI são encriptados e desencriptados utilizando encriptação AES de 256 bits. Está ativado para todas as implementações do ACI, e não precisa modificar a sua implantação ou recipientes para tirar partido desta encriptação. Isto inclui metadados sobre a implantação, variáveis ambientais, chaves que são passadas para os seus contentores, e registos persistidos após a paragem dos seus contentores para que ainda possa vê-los. A encriptação não afeta o desempenho do grupo de contentores e não existe um custo adicional para a encriptação.

## <a name="encryption-key-management"></a>Gestão de chaves de encriptação

Pode confiar nas teclas geridas pela Microsoft para a encriptação dos dados do seu contentor, ou pode gerir a encriptação com as suas próprias chaves. A tabela a seguir compara estas opções: 

|    |    Chaves geridas pela Microsoft     |     Chaves geridas pelo cliente     |
|----|----|----|
|    **Operações de encriptação/desencriptação**    |    Azure    |    Azure    |
|    **Armazenamento de chaves**    |    Loja de chaves da Microsoft    |    Azure Key Vault    |
|    **Responsabilidade de rotação chave**    |    Microsoft    |    Cliente    |
|    **Acesso à chave**    |    Apenas microsoft    |    Microsoft, Cliente    |

O resto do documento cobre os passos necessários para encriptar os seus dados de implementação do ACI com a sua chave (chave gerida pelo cliente). 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>Criptografe os dados com uma chave gerida pelo cliente

### <a name="create-service-principal-for-aci"></a>Criar Diretor de Serviços para ACI

O primeiro passo é garantir que o seu [inquilino Azure](../active-directory/develop/quickstart-create-new-tenant.md) tenha um principal serviço designado para a concessão de permissões ao serviço Azure Container Instances. 

> [!IMPORTANT]
> Para executar o seguinte comando e criar um diretor de serviço com sucesso, confirme que tem permissões para criar diretores de serviço no seu inquilino.
>

O seguinte comando CLI irá configurar o ACI SP no seu ambiente Azure:

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

A saída de funcionamento deste comando deve mostrar-lhe um principal de serviço que foi criado com "displayName": "Serviço de Instância de Contentores Azure".

Caso não seja capaz de criar com sucesso o principal de serviço:
* confirmar que tem permissões para fazê-lo no seu inquilino
* verifique se já existe um diretor de serviço no seu inquilino para implantação no ACI. Pode fazê-lo executando `az ad sp show --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9` e usar o principal de serviço em vez

### <a name="create-a-key-vault-resource"></a>Criar um recurso Key Vault

Crie um cofre de chaves Azure utilizando [o portal Azure,](../key-vault/general/quick-create-portal.md) [Azure CLI](../key-vault/general/quick-create-cli.md)ou [Azure PowerShell](../key-vault/general/quick-create-powershell.md).

Para as propriedades do seu cofre chave, utilize as seguintes diretrizes: 
* Nome: é necessário um nome exclusivo. 
* Subscrição: selecione uma subscrição.
* No Grupo de Recursos, escolha um grupo de recursos existente ou crie um novo nome de grupo de recursos.
* No menu pendente Localização, selecione uma localização.
* Pode deixar as outras opções para os seus padrão ou escolher com base em requisitos adicionais.

> [!IMPORTANT]
> Ao utilizar as teclas geridas pelo cliente para encriptar um modelo de implementação ACI, recomenda-se que as duas propriedades seguintes sejam definidas no cofre da chave, Soft Delete e Não Purga. Estas propriedades não são ativadas por padrão, mas podem ser ativadas usando o PowerShell ou o Azure CLI num cofre de chaves novo ou existente.

### <a name="generate-a-new-key"></a>Gerar uma nova chave 

Assim que o seu cofre chave for criado, navegue para o recurso no portal Azure. No menu de navegação à esquerda da lâmina de recurso, em Definições, clique em **Teclas**. Na vista para "Keys", clique em "Gerar/Importar" para gerar uma nova chave. Use qualquer nome único para esta chave, e quaisquer outras preferências com base nos seus requisitos. 

![Gerar uma nova chave](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>Definir política de acesso

Crie uma nova política de acesso para permitir que o serviço ACI aceda à sua Chave.

* Uma vez gerada a sua chave, de volta à sua lâmina de recurso de cofre chave, em Definições, clique em **Políticas de Acesso**.
* Na página "Políticas de Acesso" para o seu cofre de chaves, clique em **Adicionar Política de Acesso**.
* Descreva as *permissões-chave* para incluir permissões de chaves **de chaves de obter** e **desembrulhar** ![ chaves](./media/container-instances-encrypt-data/set-key-permissions.png)
* Para *Select Principal*, selecione serviço de instância de **contentores Azure**
* Clique em **Adicionar** na parte inferior 

A política de acesso deve agora aparecer nas políticas de acesso do seu cofre principal.

![Nova política de acesso](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>Modifique o seu modelo de implementação JSON

> [!IMPORTANT]
> A encriptação de dados de implementação com uma chave gerida pelo cliente está disponível na versão mais recente da API (2019-12-01) que está atualmente a ser lançada. Especifique esta versão API no seu modelo de implementação. Se tiver algum problema com isso, por favor contacte o Suporte Azure.

Assim que a chave do cofre e a política de acesso estiverem configurados, adicione as seguintes propriedades ao seu modelo de implementação ACI. Saiba mais sobre a implantação de recursos ACI com um modelo no [Tutorial: Implemente um grupo multi-contentores utilizando um modelo de Gestor de Recursos](./container-instances-multi-container-group.md). 
* Em `resources` baixo , definido para `apiVersion` `2019-12-01` .
* Sob a secção de propriedades do grupo de contentores do modelo de implantação, adicione um `encryptionProperties` , que contém os seguintes valores:
  * `vaultBaseUrl`: o nome DNS do seu cofre chave, pode ser encontrado na lâmina geral do recurso do cofre chave no Portal
  * `keyName`: o nome da chave gerada anteriormente
  * `keyVersion`: a versão atual da chave. Isto pode ser encontrado clicando na própria tecla (em "Chaves" na secção Definições do seu recurso de cofre chave)
* Sob as propriedades do grupo de contentores, adicione uma `sku` propriedade com `Standard` valor. O `sku` imóvel é requerido na versão API 2019-12-01.

O seguinte corte de modelo mostra estas propriedades adicionais para encriptar dados de implementação:

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

Segue-se um modelo completo, adaptado do modelo em [Tutorial: Implemente um grupo multi-contentores utilizando um modelo de Gestor de Recursos](./container-instances-multi-container-group.md). 

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

### <a name="deploy-your-resources"></a>Implemente os seus recursos

Se criou e editou o ficheiro de modelo no seu ambiente de trabalho, pode carregá-lo para o seu diretório Cloud Shell arrastando o ficheiro para o mesmo. 

Crie um grupo de recursos com o comando [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implementar o modelo com o [grupo de implementação az criar][az-deployment-group-create] comando.

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

Dentro de alguns segundos, deverá receber uma resposta inicial do Azure. Uma vez concluída a implementação, todos os dados relacionados com o mesmo persistidos pelo serviço ACI serão encriptados com a chave fornecida.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group/#az-deployment-group-create
