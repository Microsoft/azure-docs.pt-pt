---
title: Como usar O Azure Kubernetes com O Azure Cosmos DB
description: Aprenda a arrancar um cluster Kubernetes em Azure que usa O Azure Cosmos DB (pré-visualização)
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: sngun
ms.openlocfilehash: 9dbbc914580d8d80a3f9b7d730574e24b44827c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "70093734"
---
# <a name="how-to-use-azure-kubernetes-with-azure-cosmos-db-preview"></a>Como utilizar O Azure Kubernetes com Azure Cosmos DB (pré-visualização)

A API etcd em Azure Cosmos DB permite-lhe usar o Azure Cosmos DB como reserva para Azure Kubernetes. O Azure Cosmos DB implementa o protocolo de arame etcd, que permite que os servidores API do nó mestre utilizem o Azure Cosmos DB tal como acederia a um etc.instalado localmente. A API etcd em Azure Cosmos DB está atualmente em pré-visualização. Quando utiliza a Azure Cosmos etcd API como reserva para kubernetes, obtém-se os seguintes benefícios: 

* Não é necessário configurar e gerir manualmente etc.
* Elevada disponibilidade de etcd, garantida pela Cosmos (99,99% em região única, 99,999% em várias regiões).
* Escalabilidade elástica de etc.
* Seguro por padrão & empresa pronta.
* SLAs líderes na indústria e abrangentes.

Para saber mais sobre a API etcd em Azure Cosmos DB, consulte o artigo [sobre visão geral.](etcd-api-introduction.md) Este artigo mostra-lhe como usar o [Motor Azure Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md) (aks-engine) para arrancar um cluster Kubernetes em Azure que usa [o Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) em vez de um etcinstalado e configurado localmente. 

## <a name="prerequisites"></a>Pré-requisitos

1. Instale a versão mais recente do [Azure CLI.](/cli/azure/install-azure-cli?view=azure-cli-latest) Pode baixar o Azure CLI específico para o seu sistema operativo e instalar.

1. Instale a [versão mais recente](https://github.com/Azure/aks-engine/releases) do Motor Azure Kubernetes. As instruções de instalação para diferentes sistemas operativos estão disponíveis na página [do Motor Azure Kubernetes.](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install-aks-engine) Basta os passos da secção de **motor AKS** de instalação do doc ligado. Depois de descarregar, extrai o ficheiro zip.

   O Motor Azure Kubernetes (**aks-engine)** gera modelos de Gestor de Recursos Azure para clusters Kubernetes em Azure. A entrada para o motor aks é um ficheiro de definição de cluster que descreve o cluster desejado, incluindo orquestrador, funcionalidades e agentes. A estrutura dos ficheiros de entrada é semelhante à API pública para o Serviço Azure Kubernetes.

1. A API etcd em Azure Cosmos DB está atualmente em pré-visualização. Inscreva-se para utilizar a https://aka.ms/cosmosetcdapi-signupversão de pré-visualização em: . Depois de submeter o formulário, a sua subscrição será listada para utilizar a API Azure Cosmos etcd. 

## <a name="deploy-the-cluster-with-azure-cosmos-db"></a>Implementar o cluster com Azure Cosmos DB

1. Abra uma janela de comando e assine em Azure com o seguinte comando:

   ```azurecli-interactive
   az login 
   ```

1. Se tiver mais de uma subscrição, mude para a subscrição que foi listada de branco para API Azure Cosmos DB etc. Pode mudar para a subscrição necessária utilizando o seguinte comando:

   ```azurecli-interactive
   az account set --subscription "<Name of your subscription>"
   ```
1. Em seguida, crie um novo grupo de recursos onde irá implementar os recursos necessários pelo cluster Azure Kubernetes. Certifique-se de criar o grupo de recursos na região "centralus". No entanto, não é obrigatório que o grupo de recursos esteja na região "central" no entanto, a Azure Cosmos etcd API está atualmente disponível para implantar apenas na região "central" . Então é melhor ter o cluster Kubernetes para ser colocalizado com o cosmos etc:

   ```azurecli-interactive
   az group create --name <Name> --location "centralus"
   ```

1. Em seguida, crie um diretor de serviço para o cluster Azure Kubernetes para que possa comunicar com os recursos que fazem parte do mesmo grupo de recursos. Pode criar um diretor de serviço utilizando o portal Azure CLI, PowerShell ou Azure, neste exemplo irá si clI para criá-lo.

   ```azurecli-interactive
   az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<Your_Azure_subscription_ID>/resourceGroups/<Your_resource_group_name>"
   ```
   Este comando produz os detalhes de um diretor de serviço, por exemplo:
   
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
   
   Tome nota das **aplicações** e dos campos de **palavra-passe,** uma vez que utilizará estes parâmetros nos próximos passos. 

1. A partir do pedido de comando, navegue até à pasta onde está localizado o motor Azure Kubernetes. Por exemplo, no seu pedido de comando pode navegar para a pasta como:

   ```cmd
   cd "\aks-engine-v0.36.3-windows-amd64\aks-engine-v0.36.3-windows-amd64"
   ```

1. Abra um editor de texto à sua escolha e defina um modelo de Gestor de Recursos que implemente o cluster Azure Kubernetes com a Azure Cosmos DB etcd API. Copie a seguinte definição JSON para `apiModel.json`o seu editor de texto e guarde o ficheiro como:

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

   No ficheiro de definição JSON/cluster, o parâmetro-chave a notar é **"cosmosEtcd": verdadeiro**. Este parâmetro encontra-se nas propriedades "masterProfile" e indica a implementação para utilizar a API Azure Cosmos etcd em vez de etcd regular. 

1. Implante o cluster Azure Kubernetes que utiliza o Azure Cosmos DB com o seguinte comando:

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

   O Motor Azure Kubernetes consome uma definição de cluster que descreve a forma, tamanho e configuração desejados dos Kubernetes Azure. Existem várias funcionalidades que podem ser ativadas através da definição de cluster. Neste exemplo utilizará os seguintes parâmetros:

   * **assinatura-id:** Id de subscrição Azure que tem Azure Cosmos DB etcd API habilitado.
   * **cliente-id:** A appId do diretor de serviço. O `appId` foi devolvido como saída no passo 4.
   * **Segredo de cliente:** A palavra-passe do diretor de serviço ou uma senha gerada aleatoriamente. Este valor foi devolvido como saída no parâmetro 'password' no passo 4. 
   * **dnsPrefix:** Um nome DNS único na região. Este valor fará parte do nome de anfitrião (os valores de exemplo são- myprod1, encenação).
   * **localização:**  Localização para onde o cluster deve ser implantado, atualmente apenas é suportado "centralus".

   > [!Note]
   > A Azure Cosmos etc. API está atualmente disponível para implantar apenas na região "centralus". 
 
   * **api-modelo:** Caminho totalmente qualificado para o ficheiro do modelo.
   * **sobrepor a força:** Esta opção é utilizada para substituir automaticamente os ficheiros existentes no diretório de saída.
 
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

A implementação do modelo leva vários minutos para ser concluída. Após a implementação ser concluída com sucesso, verá a seguinte saída no pedido de comandos:

```cmd
WARN[0006] apimodel: missing masterProfile.dnsPrefix will use "aks-sg-test"
WARN[0006] --resource-group was not specified. Using the DNS prefix from the apimodel as the resource group name: aks-sg-test
INFO[0025] Starting ARM Deployment (aks-sg-test-546247491). This will take some time...
INFO[0587] Finished ARM Deployment (aks-sg-test-546247491). Succeeded
```

O grupo de recursos contém agora recursos como máquina virtual, conta Azure Cosmos (etcd API), rede virtual, conjunto de disponibilidade e outros recursos exigidos pelo cluster Kubernetes. 

O nome da conta Azure Cosmos corresponderá ao prefixo dNS especificado anexado com k8s. A sua conta Azure Cosmos será automaticamente aprovisionada com uma base de dados chamada **EtcdDB** e um recipiente chamado **EtcdData**. O recipiente armazenará todos os dados relacionados com o etcd. O recipiente é aprovisionado com um certo número de unidades de pedido e pode [escalar (aumentar/diminuir) a entrada](scaling-throughput.md) com base na sua carga de trabalho. 

## <a name="next-steps"></a>Passos seguintes

* Saiba como trabalhar com base [de dados da Azure Cosmos, contentores e itens](databases-containers-items.md)
* Saiba como [otimizar os custos de entrada provisionados](optimize-cost-throughput.md)

