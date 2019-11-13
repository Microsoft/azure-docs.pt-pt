---
title: Atualizar a versão de Service Fabric do Azure de um cluster
description: Atualize o código de Service Fabric e/ou a configuração que executa um Cluster Service Fabric, incluindo a definição do modo de atualização de cluster, a atualização de certificados, a adição de portas de aplicativo, a realização de patches de so e assim por diante. O que você pode esperar quando as atualizações são executadas?
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: atsenthi
ms.openlocfilehash: 067bf654928240ffe9651d4acf1933a81e631146
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013349"
---
# <a name="upgrade-the-service-fabric-version-of-a-cluster"></a>Atualizar a versão Service Fabric de um cluster

Para qualquer sistema moderno, a criação de uma grande possibilidade é a chave para atingir o sucesso de longo prazo de seu produto. Um Cluster Service Fabric do Azure é um recurso que você possui, mas é parcialmente gerenciado pela Microsoft. Este artigo descreve como atualizar a versão do Service Fabric em execução no cluster do Azure.

Você pode definir seu cluster para receber atualizações automáticas de malha à medida que elas são lançadas pela Microsoft ou você pode selecionar uma versão de malha com suporte na qual você deseja que o cluster esteja.

Você faz isso definindo a configuração de cluster "upgrademode" no portal ou usando o Gerenciador de recursos no momento da criação ou posterior em um cluster ao vivo 

> [!NOTE]
> Certifique-se de manter o cluster executando uma versão de malha com suporte sempre. Como e quando anunciamos o lançamento de uma nova versão do Service Fabric, a versão anterior é marcada para o fim do suporte após um mínimo de 60 dias a partir dessa data. As novas versões são anunciadas [no blog da equipe do Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). A nova versão está disponível para escolha. 
> 
> 

14 dias antes da expiração da liberação que o cluster está executando, é gerado um evento de integridade que coloca o cluster em um estado de integridade de aviso. O cluster permanece em um estado de aviso até que você atualize para uma versão de malha com suporte.

## <a name="set-the-upgrade-mode-in-the-azure-portal"></a>Definir o modo de atualização no portal do Azure
Você pode definir o cluster como automático ou manual ao criar o cluster.

![Create_Manualmode][Create_Manualmode]

Você pode definir o cluster como automático ou manual quando estiver em um cluster ativo, usando a experiência de gerenciamento. 

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Atualizando para uma nova versão em um cluster que está definido como modo manual por meio do Portal.
Para atualizar para uma nova versão, tudo o que você precisa fazer é selecionar a versão disponível na lista suspensa e salvar. A atualização do Fabric é iniciada automaticamente. As políticas de integridade do cluster (uma combinação de integridade do nó e a integridade de todos os aplicativos em execução no cluster) são seguidas durante a atualização.

Se as políticas de integridade do cluster não forem atendidas, a atualização será revertida. Role para baixo neste documento para ler mais sobre como definir as políticas de integridade personalizadas. 

Depois de corrigir os problemas que resultaram na reversão, você precisa iniciar a atualização novamente, seguindo as mesmas etapas de antes.

![Manage_Automaticmode][Manage_Automaticmode]

## <a name="set-the-upgrade-mode-using-a-resource-manager-template"></a>Definir o modo de atualização usando um modelo do Resource Manager
Adicione a configuração "upgrademode" à definição de recurso Microsoft. infabric/clusters e defina "clusterCodeVersion" como uma das versões de malha com suporte, conforme mostrado abaixo e, em seguida, implante o modelo. Os valores válidos para "upgrademode" são "manual" ou "Automatic"

![ARMUpgradeMode][ARMUpgradeMode]

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Atualizando para uma nova versão em um cluster que está definido para o modo manual por meio de um modelo do Resource Manager.
Quando o cluster estiver no modo manual, para atualizar para uma nova versão, altere o "clusterCodeVersion" para uma versão com suporte e implante-o. A implantação do modelo inicia a atualização da malha é iniciada automaticamente. As políticas de integridade do cluster (uma combinação de integridade do nó e a integridade de todos os aplicativos em execução no cluster) são seguidas durante a atualização.

Se as políticas de integridade do cluster não forem atendidas, a atualização será revertida.  

Depois de corrigir os problemas que resultaram na reversão, você precisa iniciar a atualização novamente, seguindo as mesmas etapas de antes.

## <a name="set-custom-health-polices-for-upgrades"></a>Definir políticas de integridade personalizadas para atualizações
Você pode especificar políticas de integridade personalizadas para atualização do fabric. Se você tiver definido o cluster para atualizações automáticas de malha, essas políticas serão aplicadas à [fase 1 das atualizações automáticas de malha](service-fabric-cluster-upgrade.md#fabric-upgrade-behavior-during-automatic-upgrades).
Se você tiver definido o cluster para atualizações de malha manual, essas políticas serão aplicadas cada vez que você selecionar uma nova versão disparando o sistema para iniciar a atualização do Fabric no cluster. Se você não substituir as políticas, os padrões serão usados.

Você pode especificar as políticas de integridade personalizadas ou examinar as configurações atuais na folha "atualização da malha", selecionando as configurações de atualização avançadas. Examine a imagem a seguir sobre como. 

![Gerenciar políticas de integridade personalizadas][HealthPolices]

## <a name="list-all-available-versions-for-all-environments-for-a-given-subscription"></a>Listar todas as versões disponíveis para todos os ambientes de uma determinada assinatura
Execute o comando a seguir e você deverá obter uma saída semelhante a esta.

"supportExpiryUtc" informa quando uma determinada versão está expirando ou expirou. A versão mais recente não tem uma data válida-ela tem um valor de "9999-12-31T23:59:59.9999999", que significa apenas que a data de expiração ainda não está definida.

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

## <a name="next-steps"></a>Passos seguintes
* Saiba como personalizar algumas das configurações de [malha de cluster do Service Fabric](service-fabric-cluster-fabric-settings.md)
* Saiba como [dimensionar o cluster para dentro e para fora](service-fabric-cluster-scale-up-down.md)
* Saiba mais sobre [atualizações de aplicativos](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
