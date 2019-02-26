---
title: Descrição geral das etiquetas de serviço de Firewall do Azure
description: Este artigo é uma descrição geral das etiquetas de serviço de Firewall do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 2/25/2019
ms.author: victorh
ms.openlocfilehash: 1d03d896de947fcc938619c52a3690962a0d2d6c
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805625"
---
# <a name="azure-firewall-service-tags"></a>Etiquetas de serviço de Firewall do Azure

As etiquetas de serviço representam um grupo de prefixos de endereços IP, que ajudam a minimizar a complexidade da criação de regras de segurança. Não pode criar as suas próprias etiquetas de serviço nem especificar que endereços IP estão incluídos nas mesmas. A Microsoft gere os prefixos de endereços que as etiquetas abrangem e atualiza-as automaticamente à medida que os endereços são alterados.

Etiquetas de serviço de Firewall do Azure podem ser utilizadas no campo de destino de regras de rede. Pode usá-los em vez de endereços IP específicos.

## <a name="supported-service-tags"></a>Etiquetas de serviço de suporte

As etiquetas de serviço seguintes estão disponíveis para utilização em regras de rede de firewall do Azure:

* **AzureCloud** (apenas Resource Manager): Esta etiqueta denota o espaço de endereços IP para o Azure, incluindo todos [os endereços IP públicos do datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Se especificar *AzureCloud* no valor, o tráfego é permitido ou negado para os endereços IP públicos do Azure. Se quiser apenas permitir o acesso ao AzureCloud numa determinada [região](https://azure.microsoft.com/regions), poderá especificar a região. Por exemplo, se pretender permitir o acesso ao AzureCloud do Azure apenas na região EUA Leste, poderá especificar *AzureCloud.EastUS* como a etiqueta de serviço. 
* **AzureTrafficManager** (apenas Resource Manager): Esta etiqueta denota o espaço de endereços IP para os endereços IP de sonda do Gestor de tráfego do Azure. Pode encontrar mais informações sobre os endereços IP da pesquisa do Gestor de Tráfego nas [FAQ do Gestor de Tráfego do Microsoft Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). 
* **Armazenamento** (apenas Resource Manager): Esta etiqueta denota o espaço de endereços IP para o serviço de armazenamento do Azure. Se especificar *Storage* no valor, o tráfego é permitido ou negado para o armazenamento. Se quiser apenas permitir o acesso ao armazenamento numa determinada [região](https://azure.microsoft.com/regions), pode especificá-la. Por exemplo, se pretender permitir o acesso ao Armazenamento do Azure apenas na região E.U.A. Leste, pode especificar *Storage.EastUS* como a etiqueta de serviço. A etiqueta representa o serviço, mas não instâncias específicas do mesmos. Por exemplo, representa o serviço Armazenamento do Azure, mas não uma conta do Armazenamento do Azure específica.
* **SQL** (apenas Resource Manager): Esta etiqueta denota os prefixos de endereços dos serviços do Azure SQL Database e o Azure SQL Data Warehouse. Se especificar *Sql* no valor, o tráfego é permitido ou negado para o Sql. Se quiser apenas permitir o acesso ao Sql numa determinada [região](https://azure.microsoft.com/regions), pode especificá-la. Por exemplo, se pretender permitir o acesso à Base de Dados SQL do Azure apenas na região E.U.A. Leste, pode especificar *Sql.EastUS* como a etiqueta de serviço. A etiqueta representa o serviço, mas não instâncias específicas do mesmos. Por exemplo, representa o serviço da Base de Dados SQL do Azure, mas não uma base de dados ou um servidor SQL.
* **AzureCosmosDB** (apenas Resource Manager): Esta etiqueta denota os prefixos de endereço do serviço de base de dados do Azure Cosmos. Se especificar *AzureCosmosDB* no valor, o tráfego é permitido ou negado para o AzureCosmosDB. Se quiser apenas permitir o acesso ao AzureCosmosDB numa determinada [região](https://azure.microsoft.com/regions), pode especificar a região utilizando o formato AzureCosmosDB. [ nome da região].
* **AzureKeyVault** (apenas Resource Manager): Esta etiqueta denota os prefixos de endereço do serviço Cofre de chaves do Azure. Se especificar *AzureKeyVault* no valor, o tráfego é permitido ou negado para o AzureKeyVault. Se quiser apenas permitir o acesso para AzureKeyVault numa determinada [região](https://azure.microsoft.com/regions), pode especificar a região utilizando o formato AzureKeyVault. [ nome da região].
* **EventHub** (apenas Resource Manager): Esta etiqueta denota os prefixos de endereço do serviço Azure EventHub. Se especificar *EventHub* no valor, o tráfego será permitido ou negado para o EventHub. Se quiser apenas permitir o acesso a EventHub numa determinada [região](https://azure.microsoft.com/regions), pode especificar a região utilizando o formato de EventHub. [ nome da região]. 
* **ServiceBus** (apenas Resource Manager): Esta etiqueta denota os prefixos de endereço do serviço de ServiceBus do Azure. Se especificar *ServiceBus* no valor, o tráfego será permitido ou negado para o ServiceBus. Se quiser apenas permitir o acesso a ServiceBus numa determinada [região](https://azure.microsoft.com/regions), pode especificar a região utilizando o formato de ServiceBus. [ nome da região].
* **MicrosoftContainerRegistry** (apenas Resource Manager): Esta etiqueta denota os prefixos de endereço do serviço de registo de contentor do Microsoft. Se especificar *MicrosoftContainerRegistry* no valor, o tráfego será permitido ou negado para o MicrosoftContainerRegistry. Se quiser apenas permitir o acesso ao MicrosoftContainerRegistry numa determinada [região](https://azure.microsoft.com/regions), pode especificar a região utilizando o formato MicrosoftContainerRegistry. [ nome da região].
* **AzureContainerRegistry** (apenas Resource Manager): Esta etiqueta denota os prefixos de endereço do serviço do Azure Container Registry. Se especificar *AzureContainerRegistry* no valor, o tráfego será permitido ou negado para o AzureContainerRegistry. Se quiser apenas permitir o acesso ao AzureContainerRegistry numa determinada [região](https://azure.microsoft.com/regions), pode especificar a região utilizando o formato AzureContainerRegistry. [ nome da região]. 
* **AppService** (apenas Resource Manager): Esta etiqueta denota os prefixos de endereço do serviço AppService do Azure. Se especificar *AppService* no valor, o tráfego será permitido ou negado para o AppService. Se quiser apenas permitir o acesso ao serviço de aplicações numa determinada [região](https://azure.microsoft.com/regions), pode especificar a região utilizando o formato do serviço de aplicações. [ nome da região]. 
* **AppServiceManagement** (apenas Resource Manager): Esta etiqueta denota os prefixos de endereço do serviço de gestão de serviço de aplicações do Azure. Se especificar *AppServiceManagement* no valor, o tráfego será permitido ou negado para o AppServiceManagement. 
* **ApiManagement** (apenas Resource Manager): Esta etiqueta denota os prefixos de endereço do serviço de gestão de API do Azure. Se especificar *ApiManagement* no valor, o tráfego será permitido ou negado para ApiManagement.  
* **AzureConnectors** (apenas Resource Manager): Esta etiqueta denota os prefixos de endereço do serviço de conectores do Azure. Se especificar *AzureConnectors* no valor, o tráfego será permitido ou negado para o AzureConnectors. Se quiser apenas permitir o acesso ao AzureConnectors numa determinada [região](https://azure.microsoft.com/regions), pode especificar a região utilizando o formato AzureConnectors. [ nome da região].
* **AzureDataLake** (apenas Resource Manager): Esta etiqueta denota os prefixos de endereço do serviço Azure Data Lake. Se especificar *AzureDataLake* no valor, o tráfego será permitido ou negado para o AzureDataLake.
* **AzureActiveDirectory** (apenas Resource Manager): Esta etiqueta denota os prefixos de endereço do serviço AzureActiveDirectory. Se especificar *AzureActiveDirectory* no valor, o tráfego será permitido ou negado para o AzureActiveDirectory.
* **AzureMonitor** (apenas Resource Manager): Esta etiqueta denota os prefixos de endereço do serviço AzureMonitor. Se especificar *AzureMonitor* no valor, o tráfego é permitido ou negado para AzureMonitor.
* **ServiceFabric** (apenas Resource Manager): Esta etiqueta denota os prefixos de endereço do serviço do ServiceFabric. Se especificar *ServiceFabric* no valor, o tráfego é permitido ou negado para ServiceFabric.
* **AzureMachineLearning** (apenas Resource Manager): Esta etiqueta denota os prefixos de endereço do serviço AzureMachineLearning. Se especificar *AzureMachineLearning* no valor, o tráfego é permitido ou negado para AzureMachineLearning.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre regras de Firewall do Azure, veja [lógica de processamento de regra de Firewall do Azure](rule-processing.md).