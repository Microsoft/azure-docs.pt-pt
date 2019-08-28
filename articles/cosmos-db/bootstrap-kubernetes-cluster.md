---
title: Como usar o kubernetes do Azure com o Azure Cosmos DB
description: Saiba como inicializar um cluster kubernetes no Azure que usa Azure Cosmos DB (versão prévia)
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: sngun
ms.openlocfilehash: 9dbbc914580d8d80a3f9b7d730574e24b44827c1
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093734"
---
# <a name="how-to-use-azure-kubernetes-with-azure-cosmos-db-preview"></a>Como usar o kubernetes do Azure com o Azure Cosmos DB (versão prévia)

A API etcd no Azure Cosmos DB permite que você use Azure Cosmos DB como o armazenamento de back-end para o Azure kubernetes. Azure Cosmos DB implementa o protocolo etcd Wire, que permite que os servidores de API do nó mestre usem Azure Cosmos DB assim como acessaria um etcd instalado localmente. a API etcd no Azure Cosmos DB está atualmente em visualização. Ao usar a API do etcd do Azure cosmos como o armazenamento de backup do kubernetes, você obtém os seguintes benefícios: 

* Não é necessário configurar e gerenciar o etcd manualmente.
* Alta disponibilidade de etcd, garantida pelo Cosmos (99,99% em uma única região, 99,999% em várias regiões).
* Escalabilidade elástica do etcd.
* Seguro por padrão & Enterprise pronto.
* SLAs abrangentes e líderes do setor.

Para saber mais sobre a API do etcd no Azure Cosmos DB, consulte o artigo [visão geral](etcd-api-introduction.md) . Este artigo mostra como usar o [mecanismo de kubernetes do Azure](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md) (AKs-Engine) para inicializar um cluster kubernetes no Azure que usa [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) em vez de um etcd instalado e configurado localmente. 

## <a name="prerequisites"></a>Pré-requisitos

1. Instale a versão mais recente do [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Você pode baixar CLI do Azure específicas para o seu sistema operacional e instalar o.

1. Instale a [versão mais recente](https://github.com/Azure/aks-engine/releases) do mecanismo de kubernetes do Azure. As instruções de instalação para diferentes sistemas operacionais estão disponíveis na página [do mecanismo de kubernetes do Azure](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install-aks-engine) . Você só precisa das etapas da seção **instalar mecanismo do AKS** do documento vinculado. Após o download, extraia o arquivo zip.

   O mecanismo de kubernetes do Azure (**AKs-Engine**) gera Azure Resource Manager modelos para clusters kubernetes no Azure. A entrada para AKs é um arquivo de definição de cluster que descreve o cluster desejado, incluindo orquestrador, recursos e agentes. A estrutura dos arquivos de entrada é semelhante à API pública para o serviço kubernetes do Azure.

1. A API etcd no Azure Cosmos DB está atualmente em visualização. Inscreva-se para usar a versão de https://aka.ms/cosmosetcdapi-signup visualização em:. Depois de enviar o formulário, sua assinatura ficará na lista de permissões para usar a API do etcd do Azure Cosmos. 

## <a name="deploy-the-cluster-with-azure-cosmos-db"></a>Implantar o cluster com Azure Cosmos DB

1. Abra uma janela de prompt de comando e entre no Azure com o seguinte comando:

   ```azurecli-interactive
   az login 
   ```

1. Se você tiver mais de uma assinatura, alterne para a assinatura que está na lista de permissões para Azure Cosmos DB API etcd. Você pode alternar para a assinatura necessária usando o seguinte comando:

   ```azurecli-interactive
   az account set --subscription "<Name of your subscription>"
   ```
1. Em seguida, crie um novo grupo de recursos em que você implantará os recursos exigidos pelo cluster kubernetes do Azure. Certifique-se de criar o grupo de recursos na região "centralus". Não é obrigatório que o grupo de recursos esteja na região "centralus", no entanto, a API etcd do Azure Cosmos está disponível atualmente para ser implantada apenas na região "centralus". Portanto, é melhor fazer com que o cluster kubernetes seja colocalizado com a instância Cosmos etcd:

   ```azurecli-interactive
   az group create --name <Name> --location "centralus"
   ```

1. Em seguida, crie uma entidade de serviço para o cluster kubernetes do Azure para que ele possa se comunicar com os recursos que fazem parte do mesmo grupo de recursos. Você pode criar uma entidade de serviço usando CLI do Azure, PowerShell ou portal do Azure, neste exemplo, você será a CLI para criá-la.

   ```azurecli-interactive
   az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<Your_Azure_subscription_ID>/resourceGroups/<Your_resource_group_name>"
   ```
   Esse comando gera os detalhes de uma entidade de serviço, por exemplo:
   
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
   
   Anote os campos **AppID** e **password** , pois você usará esses parâmetros nas próximas etapas. 

1. No prompt de comando, navegue até a pasta em que o executável do Azure kubernetes Engine está localizado. Por exemplo, no prompt de comando, você pode navegar para a pasta como:

   ```cmd
   cd "\aks-engine-v0.36.3-windows-amd64\aks-engine-v0.36.3-windows-amd64"
   ```

1. Abra um editor de texto de sua escolha e defina um modelo do Resource Manager que implanta o cluster kubernetes do Azure com a API do etcd do Azure Cosmos DB. Copie a definição JSON a seguir para seu editor de texto e salve o `apiModel.json`arquivo como:

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

   No arquivo de definição de cluster/JSON, o parâmetro de chave a ser observado é **"cosmosEtcd": true**. Esse parâmetro está nas propriedades "masterProfile" e indica que a implantação deve usar a API do Azure Cosmos etcd em vez de etcd regulares. 

1. Implante o cluster kubernetes do Azure que usa Azure Cosmos DB com o seguinte comando:

   ```cmd
   aks-engine deploy \
     --subscription-id <Your_Azure_subscription_ID> \
     --client-id <Service_ principal_appId> \
     --client-secret <Service_ principal_password> \
     --dns-prefix <Region_unique_dns_name > \
     --location centralus \
     --resource-group <Resource_Group_Name> \
     --api-model <Fully_qualified_path_to_the_template_file>  \
     --force-overwrite
   ```

   O mecanismo de kubernetes do Azure consome uma definição de cluster que descreve a forma, o tamanho e a configuração desejados do kubernetes do Azure. Há vários recursos que podem ser habilitados por meio da definição de cluster. Neste exemplo, você usará os seguintes parâmetros:

   * **ID da assinatura:** ID de assinatura do Azure que tem Azure Cosmos DB API etcd habilitada.
   * **client-id:** A appId da entidade de serviço. O `appId` foi retornado como saída na etapa 4.
   * **Segredo do cliente:** A senha da entidade de serviço ou uma senha gerada aleatoriamente. Esse valor foi retornado como saída no parâmetro ' password ' na etapa 4. 
   * **dnsPrefix:** Um nome DNS exclusivo da região. Esse valor fará parte do nome do host (os valores de exemplo são-myprod1, preparo).
   * **local**  Local em que o cluster deve ser implantado, atualmente há suporte apenas para "centralus".

   > [!Note]
   > A API do Azure Cosmos etcd está disponível atualmente para ser implantada somente na região "centralus". 
 
   * **api-model:** Caminho totalmente qualificado para o arquivo de modelo.
   * **force-overwrite:** Essa opção é usada para substituir automaticamente os arquivos existentes no diretório de saída.
 
   O comando a seguir mostra um exemplo de implantação:

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

A implantação do modelo leva vários minutos para ser concluída. Depois que a implantação for concluída com êxito, você verá a seguinte saída no prompt de comandos:

```cmd
WARN[0006] apimodel: missing masterProfile.dnsPrefix will use "aks-sg-test"
WARN[0006] --resource-group was not specified. Using the DNS prefix from the apimodel as the resource group name: aks-sg-test
INFO[0025] Starting ARM Deployment (aks-sg-test-546247491). This will take some time...
INFO[0587] Finished ARM Deployment (aks-sg-test-546247491). Succeeded
```

O grupo de recursos agora contém recursos como máquina virtual, conta do Azure Cosmos (API do etcd), rede virtual, conjunto de disponibilidade e outros recursos exigidos pelo cluster kubernetes. 

O nome da conta do Azure Cosmos corresponderá ao prefixo DNS especificado anexado a K8S. Sua conta do Azure Cosmos será provisionada automaticamente com um banco de dados chamado **EtcdDB** e um contêiner chamado **EtcdData**. O contêiner armazenará todos os dados relacionados ao etcd. O contêiner é provisionado com um determinado número de unidades de solicitação e você pode [dimensionar (aumentar/diminuir) a taxa de transferência](scaling-throughput.md) com base em sua carga de trabalho. 

## <a name="next-steps"></a>Passos seguintes

* Saiba como [trabalhar com o banco de dados, contêineres e itens do Azure Cosmos](databases-containers-items.md)
* Saiba como [otimizar os custos de taxa de transferência](optimize-cost-throughput.md) provisionada

