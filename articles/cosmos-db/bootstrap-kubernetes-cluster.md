---
title: Como usar Azure Kubernetes com Azure Cosmos DB
description: Saiba como arrancar um cluster Kubernetes no Azure que usa Azure Cosmos DB (pré-visualização)
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/06/2019
ms.author: sngun
ms.openlocfilehash: dbfa780269f28e269fed51a25ec4255d18d8ab39
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92277877"
---
# <a name="how-to-use-azure-kubernetes-with-azure-cosmos-db-preview"></a>Como utilizar Azure Kubernetes com Azure Cosmos DB (pré-visualização)

A API etcd em Azure Cosmos DB permite-lhe usar a Azure Cosmos DB como loja de backend para Azure Kubernetes. A Azure Cosmos DB implementa o protocolo de fio etcd, que permite que os servidores API do nó primário utilizem o Azure Cosmos DB tal como acederia a um etcd instalado localmente. etcD API em Azure Cosmos DB está atualmente em pré-visualização. Quando utiliza a Azure Cosmos etcd API como loja de apoio para Kubernetes, obtém os seguintes benefícios: 

* Não é necessário configurar manualmente e gerir etc.
* Alta disponibilidade de etcd, garantida pela Cosmos (99,99% na região única, 99,999% em várias regiões).
* Escalabilidade elástica de etcd.
* Proteja por defeito & empresa pronta.
* SLAs abrangentes e líderes da indústria.

Para saber mais sobre a API etcd em Azure Cosmos DB, consulte o artigo [geral.](etcd-api-introduction.md) Este artigo mostra-lhe como usar [o Motor Azure Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md) (motor aks) para arrancar um cluster Kubernetes em Azure que usa [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) em vez de um etcd instalado localmente e configurado. 

## <a name="prerequisites"></a>Pré-requisitos

1. Instale a versão mais recente do [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true). Pode baixar o Azure CLI específico para o seu sistema operativo e instalar.

1. Instale a [versão mais recente](https://github.com/Azure/aks-engine/releases) do Motor Azure Kubernetes. As instruções de instalação de diferentes sistemas operativos estão disponíveis na página [do Motor Azure Kubernetes.](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install-aks-engine) Só precisa dos passos da secção de **motor AKS** do doc ligado. Depois de descarregar, extraia o ficheiro zip.

   O Motor Azure Kubernetes **(motor aks)** gera modelos Azure Resource Manager para clusters Kubernetes em Azure. A entrada para o motor aks é um ficheiro de definição de cluster que descreve o aglomerado desejado, incluindo orquestrador, características e agentes. A estrutura dos ficheiros de entrada é semelhante à API pública para o Serviço Azure Kubernetes.

1. A API etcd em Azure Cosmos DB está atualmente em pré-visualização. Inscreva-se para utilizar a versão de pré-visualização em: https://aka.ms/cosmosetcdapi-signup . Depois de submeter o formulário, a sua subscrição será autorizada a utilizar a API Azure Cosmos etcd. 

## <a name="deploy-the-cluster-with-azure-cosmos-db"></a>Implementar o cluster com Azure Cosmos DB

1. Abra uma janela de ordem de comando e inscreva-se em Azure com o seguinte comando:

   ```azurecli-interactive
   az login 
   ```

1. Se tiver mais de uma subscrição, mude para a subscrição que foi permitida para Azure Cosmos DB etcd API. Pode mudar para a subscrição necessária utilizando o seguinte comando:

   ```azurecli-interactive
   az account set --subscription "<Name of your subscription>"
   ```
1. Em seguida, crie um novo grupo de recursos onde irá utilizar os recursos necessários pelo cluster Azure Kubernetes. Certifique-se de criar o grupo de recursos na região "central". Não é obrigatório que o grupo de recursos esteja na região "central", no entanto, a Azure Cosmos etcd API está atualmente disponível para implantar apenas na região "central". Então é melhor ter o cluster Kubernetes para ser cotado com o exemplo cosmos etc:

   ```azurecli-interactive
   az group create --name <Name> --location "centralus"
   ```

1. Em seguida, crie um principal de serviço para o cluster Azure Kubernetes para que possa comunicar com os recursos que fazem parte do mesmo grupo de recursos. Pode criar um principal de serviço utilizando o portal Azure CLI, PowerShell ou Azure, neste exemplo irá criar o CLI.

   ```azurecli-interactive
   az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<Your_Azure_subscription_ID>/resourceGroups/<Your_resource_group_name>"
   ```
   Este comando fornece os detalhes de um principal de serviço, por exemplo:
   
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
   
   Tome nota dos **campos de appId** e **password,** pois utilizará estes parâmetros nos próximos passos. 

1. A partir da posição de comando, navegue até à pasta onde está localizado o motor Azure Kubernetes. Por exemplo, no seu pedido de comando pode navegar para a pasta como:

   ```cmd
   cd "\aks-engine-v0.36.3-windows-amd64\aks-engine-v0.36.3-windows-amd64"
   ```

1. Abra um editor de texto à sua escolha e defina um modelo de Gestor de Recursos que implementa o cluster Azure Kubernetes com Azure Cosmos DB etcd API. Copie a seguinte definição JSON para o seu editor de texto e guarde o ficheiro como `apiModel.json` :

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

   No ficheiro de definição JSON/cluster, o parâmetro-chave a notar é **"cosmosEtcd": verdadeiro**. Este parâmetro está nas propriedades "masterProfile" e indica a implementação para usar Azure Cosmos etcd API em vez de etcd regular. 

1. Implemente o cluster Azure Kubernetes que utiliza a Azure Cosmos DB com o seguinte comando:

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

   O Motor Azure Kubernetes consome uma definição de cluster que descreve a forma, tamanho e configuração desejadas dos Azure Kubernetes. Existem várias funcionalidades que podem ser ativadas através da definição de cluster. Neste exemplo utilizará os seguintes parâmetros:

   * **id de subscrição:** ID de assinatura Azure que tem Azure Cosmos DB etcd API ativado.
   * **id cliente:** O diretor de serviço é o AppId. O `appId` foi devolvido como saída no passo 4.
   * **Segredo de cliente:** A senha do chefe de serviço ou uma senha gerada aleatoriamente. Este valor foi devolvido como saída no parâmetro 'password' no passo 4. 
   * **dnsPrefix:** Um nome DNS único na região. Este valor fará parte do nome de anfitrião (os valores de exemplo são- myprod1, encenação).
   * **Localização:**  Local para onde o cluster deve ser implantado, atualmente apenas é suportado "central".

   > [!Note]
   > A Azure Cosmos etcd API está atualmente disponível para implantação apenas na região "central". 
 
   * **modelo api:** Caminho totalmente qualificado para o arquivo do modelo.
   * **excesso de força:** Esta opção é utilizada para substituir automaticamente os ficheiros existentes no diretório de saída.
 
   O seguinte comando mostra uma implantação de exemplo:

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

A implementação do modelo leva vários minutos para ser concluída. Após a implementação concluída com sucesso, verá a seguinte saída no aviso de comandos:

```cmd
WARN[0006] apimodel: missing masterProfile.dnsPrefix will use "aks-sg-test"
WARN[0006] --resource-group was not specified. Using the DNS prefix from the apimodel as the resource group name: aks-sg-test
INFO[0025] Starting ARM Deployment (aks-sg-test-546247491). This will take some time...
INFO[0587] Finished ARM Deployment (aks-sg-test-546247491). Succeeded
```

O grupo de recursos agora contém recursos como máquina virtual, conta Azure Cosmos (etcd API), rede virtual, conjunto de disponibilidade e outros recursos necessários pelo cluster Kubernetes. 

O nome da conta Azure Cosmos corresponderá ao prefixo DNS especificado anexado com k8s. A sua conta Azure Cosmos será automaticamente abastada com uma base de dados chamada **EtcdDB** e um contentor chamado **EtcdData**. O recipiente armazenará todos os dados relacionados com etc. O contentor é a provisionado com um certo número de unidades de pedido e pode [escalar (aumentar/diminuir) a produção](scaling-throughput.md) com base na sua carga de trabalho. 

## <a name="next-steps"></a>Passos seguintes

* Saiba como trabalhar com a [base de dados, contentores e itens da Azure Cosmos](account-databases-containers-items.md)
* Saiba como [otimizar os custos de produção a provisionados](optimize-cost-throughput.md)

