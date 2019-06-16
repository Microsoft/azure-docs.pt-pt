---
title: Atualizar um cluster do Azure Service Fabric | Documentos da Microsoft
description: Atualize o código do Service Fabric e/ou a configuração que executa um cluster do Service Fabric, incluindo a definição modo de atualização do cluster, atualizar certificados, adicionando as portas da aplicação, fazendo patches de SO, e assim por diante. O que esperar quando as atualizações são efetuadas?
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: aljo
ms.openlocfilehash: 234bff5049babf0c4b1d036b40201720b2736228
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60714718"
---
# <a name="upgrade-the-service-fabric-version-of-a-cluster"></a>Atualizar a versão de Service Fabric de um cluster

Para qualquer sistema moderno, a criação de uma upgradability é a chave para alcançar o sucesso a longo prazo de seu produto. Um cluster do Azure Service Fabric é um recurso que o proprietário, mas está parcialmente gerida pela Microsoft. Este artigo descreve como atualizar a versão do Service Fabric em execução no cluster do Azure.

Pode definir o seu cluster para receber atualizações de recursos de infraestrutura automático à medida que são lançadas pela Microsoft ou pode selecionar uma versão de recursos de infraestrutura suportadas que pretende que o cluster na.

Para tal, definir a configuração do cluster "upgradeMode" no portal ou com o Resource Manager no momento da criação ou mais tarde um cluster em direto 

> [!NOTE]
> Certifique-se manter o seu cluster sempre a executar uma versão de recursos de infraestrutura suportadas. Como e quando podemos anunciar o lançamento de uma nova versão do service fabric, a versão anterior está marcada para o fim do suporte após um mínimo de 60 dias dessa data. As novas versões sejam anunciadas [no blog da equipe de recursos de infraestrutura do serviço](https://blogs.msdn.microsoft.com/azureservicefabric/). A nova versão está disponível para, em seguida, escolha. 
> 
> 

14 dias antes da expiração da versão do que seu cluster está em execução, estado de funcionamento é gerado um evento que coloca o seu cluster num Estado de funcionamento de aviso. O cluster permanecer num Estado de aviso enquanto não atualizar para uma versão de recursos de infraestrutura suportadas.

## <a name="set-the-upgrade-mode-in-the-azure-portal"></a>Definir o modo de atualização no portal do Azure
Pode configurar o cluster para automático ou manual quando estiver a criar o cluster.

![Create_Manualmode][Create_Manualmode]

Pode configurar o cluster para automático ou manual quando num cluster em direto, utilizando a experiência de gerir. 

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>A atualizar para uma nova versão de um cluster que está definido para o modo Manual através do portal.
Para atualizar para uma nova versão, tudo o que precisa fazer é selecionar a versão disponível no menu pendente e guardar. A atualização de recursos de infraestrutura é iniciada automaticamente. As políticas de estado de funcionamento do cluster (uma combinação de estado de funcionamento do nó e o estado de funcionamento todos os aplicativos em execução no cluster) sejam seguidos durante a atualização.

Se as políticas de estado de funcionamento do cluster não forem cumpridas, a atualização é revertida. Desloque para baixo deste documento para ler mais sobre como configurar as políticas de estado de funcionamento personalizados. 

Depois de corrigir os problemas que resultaram na reversão, terá de iniciar a atualização novamente, ao seguir os mesmos passos.

![Manage_Automaticmode][Manage_Automaticmode]

## <a name="set-the-upgrade-mode-using-a-resource-manager-template"></a>Definir o modo de atualização utilizando um modelo do Resource Manager
Adicionar a configuração de "upgradeMode" para a definição do recurso Microsoft.ServiceFabric/clusters e defina "clusterCodeVersion" para uma das versões de recursos de infraestrutura suportadas, conforme mostrado abaixo e, em seguida, implementar o modelo. Os valores válidos para "upgradeMode" são "Manual" ou "Automático"

![ARMUpgradeMode][ARMUpgradeMode]

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>A atualizar para uma nova versão de um cluster que está definido para o modo Manual através de um modelo do Resource Manager.
Quando o cluster está no modo Manual, para atualizar para uma nova versão, altere o "clusterCodeVersion" para uma versão suportada e implementá-lo. A implementação do modelo, entra em ação da atualização do Fabric é iniciada automaticamente. As políticas de estado de funcionamento do cluster (uma combinação de estado de funcionamento do nó e o estado de funcionamento todos os aplicativos em execução no cluster) sejam seguidos durante a atualização.

Se as políticas de estado de funcionamento do cluster não forem cumpridas, a atualização é revertida.  

Depois de corrigir os problemas que resultaram na reversão, terá de iniciar a atualização novamente, ao seguir os mesmos passos.

## <a name="set-custom-health-polices-for-upgrades"></a>Estado de funcionamento personalizado do conjunto de políticas para as atualizações
Pode especificar políticas de estado de funcionamento personalizado para atualização do fabric. Se tiver definido o seu cluster a atualizações de recursos de infraestrutura automática, em seguida, estas políticas são aplicadas a [fase 1 das atualizações automáticas de recursos de infraestrutura](service-fabric-cluster-upgrade.md#fabric-upgrade-behavior-during-automatic-upgrades).
Se tiver definido o cluster para recursos de infraestrutura Manual atualizações, estas políticas são aplicadas sempre que selecione uma nova versão do sistema para iniciar a atualização de recursos de infraestrutura no seu cluster a acionar. Se não substituir as políticas, são utilizadas as predefinições.

Pode especificar as políticas de estado de funcionamento personalizado ou reveja as definições atuais no painel de "atualização do fabric", ao selecionar as definições avançadas de atualização. Reveja a imagem seguinte sobre como. 

![Gerir políticas de estado de funcionamento personalizados][HealthPolices]

## <a name="list-all-available-versions-for-all-environments-for-a-given-subscription"></a>Listar todas as versões disponíveis para todos os ambientes de uma determinada subscrição
Execute o seguinte comando, e deverá obter um resultado semelhante ao seguinte.

"supportExpiryUtc" informa ao seu quando uma determinada versão está prestes a expirar ou expirou. A versão mais recente não tem uma data válida – ele tem um valor de "9999-12-31T23:59:59.9999999", que significa apenas que a data de expiração não estiver ainda definida.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2016-09-01

Example: https://management.azure.com/subscriptions/1857f442-3bce-4b96-ad95-627f76437a67/providers/Microsoft.ServiceFabric/locations/eastus/clusterVersions?api-version=2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }
```

## <a name="next-steps"></a>Passos Seguintes
* Saiba como personalizar algumas do [definições de recursos de infraestrutura de cluster de recursos de infraestrutura do serviço](service-fabric-cluster-fabric-settings.md)
* Saiba como [dimensionar e reduzir o seu cluster](service-fabric-cluster-scale-up-down.md)
* Saiba mais sobre [as atualizações de aplicações](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
