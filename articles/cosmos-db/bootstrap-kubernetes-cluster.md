---
title: Como utilizar o Kubernetes do Azure com o Azure Cosmos DB
description: Saiba como iniciar um cluster de Kubernetes no Azure que utiliza o Azure Cosmos DB (pré-visualização)
author: SnehaGunda
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/06/2019
ms.author: sngun
ms.openlocfilehash: 7d60e71fa0768b46042c31f9d79dd8ca6a41b2cc
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/13/2019
ms.locfileid: "65080857"
---
# <a name="how-to-use-azure-kubernetes-with-azure-cosmos-db-preview"></a>Como utilizar o Kubernetes do Azure com o Azure Cosmos DB (pré-visualização)

O etcd API no Azure Cosmos DB permite-lhe utilizar o Azure Cosmos DB como o armazenamento de back-end para Kubernetes do Azure. O Azure Cosmos DB implementa o protocolo de transmissão de etcd, que permite que os servidores de API do nó principal utilizar o Azure Cosmos DB tal como acederia a um etcd instalado localmente. etcd API no Azure Cosmos DB está atualmente em pré-visualização. Quando utiliza o Azure Cosmos etcd API como armazenamento de backup para Kubernetes, obtenha os seguintes benefícios: 

* Não é necessário configurar e gerir etcd manualmente.
* Elevada disponibilidade de etcd, garantido por Cosmos (99,99% numa única região, 99,999% em várias regiões).
* Escalabilidade elástica de etcd.
* Seguro por padrão e pronto para as empresas.
* SLAs líder do setor e abrangentes.

Para saber mais sobre etcd API no Azure Cosmos DB, veja a [descrição geral](etcd-api-introduction.md) artigo. Este artigo mostra-lhe como utilizar [motor de Kubernetes do Azure](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md) (aks-engine) para inicializar um cluster de Kubernetes no Azure que utiliza [do Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) em vez de um etcd localmente instalado e configurado. 

## <a name="prerequisites"></a>Pré-requisitos

1. Instale a versão mais recente do [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Pode transferir o CLI do Azure específica para seu sistema operativo e instalar.

1. Instalar o [v0.32.3](https://github.com/Azure/aks-engine/releases/tag/v0.32.3) versão do motor de Kubernetes do Azure. As instruções de instalação para sistemas operativos diferentes estão disponíveis no [motor de Kubernetes do Azure](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install-aks-engine) página. Precisa apenas os passos da **instalar o motor de AKS** secção de documentos vinculada. Depois de transferir, extrair o ficheiro zip.

   O motor de Kubernetes do Azure (**mecanismo de aks**) gera modelos Azure Resource Manager para clusters do Kubernetes no Azure. A entrada para o motor de aks é um ficheiro de definição de cluster que descreve o cluster pretendido, incluindo o orchestrator, recursos e agentes. A estrutura dos ficheiros de entrada é semelhante para a API pública do serviço Kubernetes do Azure.

1. O etcd API no Azure Cosmos DB está atualmente em pré-visualização. Inscreva-se para utilizar a versão de pré-visualização em: https://aka.ms/cosmosetcdapi-signup. Depois de submeter o formulário, a sua subscrição será na lista de permissões para utilizar a API de etcd do Cosmos do Azure. 

## <a name="deploy-the-cluster-with-azure-cosmos-db"></a>Implementar o cluster com o Azure Cosmos DB

1. Abra uma janela da linha de comando e iniciar sessão no Azure com o seguinte comando:

   ```azurecli-interactive
   az login 
   ```

1. Se tiver mais de uma assinatura, mude para a subscrição que estava na lista de permissões para o Azure Cosmos DB etcd API. Pode mudar para a subscrição necessária com o seguinte comando:

   ```azurecli-interactive
   az account set --subscription "<Name of your subscription>"
   ```
1. Em seguida, crie um novo grupo de recursos onde irá implementar os recursos exigidos pelo cluster de Kubernetes do Azure. Certifique-se criar o grupo de recursos na região "centralus". Não é obrigatório para o grupo de recursos ser na região "centralus", no entanto, o Azure Cosmos etcd API está atualmente disponível para implementar na região de "centralus" apenas. Portanto, é recomendável ter o cluster de Kubernetes para ser foi colocalizado com a instância de etcd Cosmos:

   ```azurecli-interactive
   az group create --name <Name> --location "centralus"
   ```

1. Em seguida crie um principal de serviço para o cluster de Kubernetes do Azure para que este possa comunicar com os recursos que fazem parte do mesmo grupo de recursos. Pode criar um principal de serviço com a CLI do Azure, o PowerShell ou o portal do Azure, neste exemplo, irá CLI para criá-lo.

   ```azurecli-interactive
   az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<Your_Azure_subscription_ID>/resourceGroups/<Your_resource_group_name>"
   ```
   Este comando devolve os detalhes de um serviço principal, por exemplo:
   
   ```cmd
   Retrying role assignment creation: 1/36
   {
     "appId": "8415a4e9-4f83-46ca-a704-107457b2e3ab",
     "displayName": "azure-cli-2019-04-19-19-01-46",
     "name": "http://azure-cli-2019-04-19-19-01-46",
     "password": "102aecd3-5e37-4f3d-8738-2ac348c2e6a7",
     "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
   }
   ```
   
   Anote o **appId** e o **palavra-passe** campos, porque irá utilizar estes parâmetros nos passos seguintes. 

1. Na linha de comandos, navegue para a pasta onde está localizado o executável do motor de Kubernetes do Azure. Por exemplo, na sua linha de comandos pode navegar para a pasta como:

   ```cmd
   cd "\aks-engine-v0.32.3-windows-amd64\aks-engine-v0.32.3-windows-amd64"
   ```

1. Abra um editor de texto à sua escolha e definir um modelo do Resource Manager que implementa o cluster de Kubernetes do Azure com o Azure Cosmos DB etcd API. Copie a seguinte definição JSON para o seu editor de texto e guarde o ficheiro como `apiModel.json`:

   ```json

   {
    "apiVersion": "vlabs",
    "properties": {
        "orchestratorProfile": {
            "orchestratorType": "Kubernetes",
            "kubernetesConfig": {
                "useManagedIdentity": false
            }
        },
        "masterProfile": {
            "count": 1,
            "dnsPrefix": "",
            "vmSize": "Standard_D2_v3",
            "cosmosEtcd": true
        },
        "agentPoolProfiles": [
            {
                "name": "agent",
                "count": 1,
                "vmSize": "Standard_D2_v3",
                "availabilityProfile": "AvailabilitySet"
            }
        ],
        "linuxProfile": {
            "adminUsername": "azureuser",
            "ssh": {
                "publicKeys": [
                    {
                        "keyData": ""
                    }
                ]
            }
        }
    }
   }
   ```

   No ficheiro de definição de JSON/cluster, é o parâmetro de chave observar **"cosmosEtcd": true**. Este parâmetro é em Propriedades "masterProfile" e indica a implementação para utilizar a API do Azure Cosmos etcd em vez de regular etcd. 

1. Implemente o cluster de Kubernetes do Azure que utiliza o Azure Cosmos DB com o seguinte comando:

   ```cmd
   aks-engine deploy \
     --subscription-id <Your_Azure_subscription_ID> \
     --client-id <Service_ principal_appId> \
     --client-secret <Service_ principal_password> \
     --dns-prefix <Region_unique_dns_name > \
     --location centralus \
     --api-model <Fully_qualified_path_to_the_template_file>  \
     --force-overwrite
   ```

   Motor de Kubernetes do Azure consome uma definição de cluster que descreve a forma desejado, o tamanho e a configuração do Kubernetes do Azure. Existem vários recursos que podem ser ativados através da definição do cluster. Neste exemplo irá utilizar os seguintes parâmetros:

   * **subscription-id:** ID de subscrição do Azure com a API do Azure Cosmos DB etcd ativada.
   * **client-id:** AppId do principal de serviço. O `appId` foi devolvido como saída no passo 4.
   * **Segredo do cliente:** Palavra-passe o principal de serviço ou uma palavra-passe gerada aleatoriamente. Este valor foi devolvido como saída no parâmetro 'password' no passo 4. 
   * **dnsPrefix:** Um nome DNS exclusivo para a região. Este valor farão parte do nome de anfitrião (valores de exemplo são-myprod1, teste).
   * **Localização:**  Localização onde o cluster deve ser "centralus" implementado para, atualmente, apenas é suportada.

   > [!Note]
   > API de etcd do Cosmos do Azure está atualmente disponível para implementar na região de "centralus" apenas. 
 
   * **api-model:** Caminho totalmente qualificado para o ficheiro de modelo.
   * **force-overwrite:** Esta opção é utilizada para substituir automaticamente os ficheiros existentes no diretório de saída.
 
   O comando seguinte mostra um exemplo de implementação:

   ```cmd
   aks-engine deploy \
     --subscription-id 1234fc61-1234-1234-1234-be1234d12c1b \
     --client-id 1234a4e9-4f83-46ca-a704-107457b2e3ab \
     --client-secret 123aecd3-5e37-4f3d-8738-2ac348c2e6a7 \
     --dns-prefix aks-sg-test \
     --location centralus \
     --api-model "C:\Users\demouser\Downloads\apiModel.json"  \
     --force-overwrite
   ```

## <a name="verify-the-deployment"></a>Verificar a implementação

A implementação do modelo demora vários minutos a concluir. Após a implementação é concluída com êxito, irá ver o resultado seguinte no prompt de comandos:

```cmd
WARN[0006] apimodel: missing masterProfile.dnsPrefix will use "aks-sg-test"
WARN[0006] --resource-group was not specified. Using the DNS prefix from the apimodel as the resource group name: aks-sg-test
INFO[0025] Starting ARM Deployment (aks-sg-test-546247491). This will take some time...
INFO[0587] Finished ARM Deployment (aks-sg-test-546247491). Succeeded
```

O grupo de recursos agora contém recursos como - à rede virtual (etcd API), o conjunto de disponibilidade, de contas de máquina virtual, do Azure Cosmos e outros recursos necessária para o cluster de Kubernetes. 

Nome da conta do Azure Cosmos corresponderá ao seu prefixo DNS especificado anexado com k8s. Sua conta do Cosmos do Azure será aprovisionada automaticamente com uma base de dados com o nome **EtcdDB** e um contentor com o nome **EtcdData**. O contentor irá armazenar todos o etcd dados relacionados. O contentor está aprovisionado com um determinado número de unidades de pedido e pode [(aumentar/diminuir) o débito de escala](scaling-throughput.md) com base na carga de trabalho. 

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [funcionam com a base de dados Azure Cosmos, contentores e itens](databases-containers-items.md)
* Saiba como [otimizar os custos de débito aprovisionado](optimize-cost-throughput.md)

