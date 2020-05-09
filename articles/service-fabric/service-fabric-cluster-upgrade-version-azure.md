---
title: Atualizar a versão Azure Service Fabric de um cluster
description: Atualize o código e/ou configuração do Tecido de Serviço que executa um cluster de tecido de serviço, incluindo a definição do modo de atualização do cluster, a atualização de certificados, a adição de portas de aplicação, a realização de patches de OS, e assim por diante. O que se pode esperar quando as atualizações forem realizadas?
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 802e7402f60370b7151c5e373c8a4921a5af7c80
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82789605"
---
# <a name="upgrade-the-service-fabric-version-of-a-cluster"></a>Atualizar a versão do Service Fabric de um cluster

Para qualquer sistema moderno, conceber para a graduação é fundamental para alcançar o sucesso a longo prazo do seu produto. Um cluster Azure Service Fabric é um recurso que possui, mas é parcialmente gerido pela Microsoft. Este artigo descreve como atualizar a versão do Tecido de Serviço em execução no seu cluster Azure.

Pode configurar o seu cluster para receber atualizações automáticas de tecido saem à medida que são lançadas pela Microsoft ou pode selecionar uma versão de tecido suportado em que pretende que o seu cluster esteja ligado.

Faça-o definindo a configuração do cluster "upgradeMode" no portal ou utilizando o Gestor de Recursos no momento da criação ou mais tarde num cluster ao vivo 

> [!NOTE]
> Certifique-se de que mantém o seu cluster sempre com uma versão de tecido suportado. À medida que anunciamos o lançamento de uma nova versão do tecido de serviço, a versão anterior está marcada para fim do suporte após um mínimo de 60 dias a partir dessa data. Os novos lançamentos são anunciados no blog da equipa de tecidos de [serviço.](https://blogs.msdn.microsoft.com/azureservicefabric/) O novo lançamento está disponível para escolher. 
> 
> 

14 dias antes do termo da libertação que o seu cluster está a executar, é gerado um evento de saúde que coloca o seu cluster num estado de saúde de alerta. O cluster permanece em estado de alerta até atualizar para uma versão de tecido suportado.

## <a name="set-the-upgrade-mode-in-the-azure-portal"></a>Desajuste o modo de atualização no portal Azure
Pode configurar o cluster de forma automática ou manual quando estiver a criar o cluster.

![Create_Manualmode][Create_Manualmode]

Pode configurar o cluster para automático ou manual quando estiver num cluster vivo, utilizando a experiência de gestão. 

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Upgrade para uma nova versão num cluster que está definido para modo Manual via portal.
Para fazer o upgrade para uma nova versão, tudo o que precisa de fazer é selecionar a versão disponível a partir do dropdown e economizar. A atualização do Tecido é iniciada automaticamente. As políticas de saúde do cluster (uma combinação de saúde nó e a saúde de todas as aplicações em execução no cluster) são cumpridas durante a atualização.

Se as políticas de saúde do cluster não forem cumpridas, a atualização é revertida. Percorra este documento para ler mais sobre como definir essas políticas de saúde personalizadas. 

Depois de ter corrigido os problemas que resultaram na reversão, tem de reiniciar a atualização, seguindo os mesmos passos que antes.

![Manage_Automaticmode][Manage_Automaticmode]

## <a name="set-the-upgrade-mode-using-a-resource-manager-template"></a>Desajuste o modo de atualização usando um modelo de Gestor de Recursos
Adicione a configuração "upgradeMode" à definição de recursos microsoft.ServiceFabric/clusters e detetete a "clusterCodeVersion" para uma das versões de tecido suportado, como mostrado abaixo e, em seguida, implemente o modelo. Os valores válidos para "upgradeMode" são "Manual" ou "Automático"

![ArmupgradeMode][ARMUpgradeMode]

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Upgrade para uma nova versão num cluster que é definido para modo Manual através de um modelo de Gestor de Recursos.
Quando o cluster estiver em modo Manual, para atualizar para uma nova versão, altere o "clusterCodeVersion" para uma versão suportada e implemente-a. A implementação do modelo, os pontapés da atualização do Tecido são iniciados automaticamente. As políticas de saúde do cluster (uma combinação de saúde nó e a saúde de todas as aplicações em execução no cluster) são cumpridas durante a atualização.

Se as políticas de saúde do cluster não forem cumpridas, a atualização é revertida.  

Depois de ter corrigido os problemas que resultaram na reversão, tem de reiniciar a atualização, seguindo os mesmos passos que antes.

## <a name="set-custom-health-polices-for-upgrades"></a>Detete polícias de saúde personalizados para upgrades
Pode especificar polícias de saúde personalizados para upgrade de tecido. Se tiver definido o seu cluster para atualizações automáticas de tecidos, então estas políticas são aplicadas à [Fase-1 das atualizações automáticas de tecidos](service-fabric-cluster-upgrade.md#fabric-upgrade-behavior-during-automatic-upgrades).
Se tiver definido o seu cluster para atualizações de tecidomanual, estas políticas são aplicadas cada vez que seleciona uma nova versão que aciona o sistema para iniciar a atualização do tecido no seu cluster. Se não anular as políticas, os incumprimentos são utilizados.

Pode especificar as políticas de saúde personalizadas ou rever as definições atuais sob a lâmina de "upgrade de tecido", selecionando as definições avançadas de atualização. Reveja a seguinte imagem sobre como. 

![Gerir políticas de saúde personalizadas][HealthPolices]

## <a name="list-all-available-versions-for-all-environments-for-a-given-subscription"></a>Listar todas as versões disponíveis para todos os ambientes para uma determinada subscrição
Execute o seguinte comando, e deve obter uma saída semelhante a esta.

"supportExpiryUtc" diz ao seu quando uma dada libertação está expirando ou expirou. O último lançamento não tem uma data válida - tem um valor de "9999-12-31T23:59:59.999999999", o que significa apenas que a data de validade ainda não está definida.

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
* Aprenda a personalizar algumas das [configurações](service-fabric-cluster-fabric-settings.md) de tecido de tecido de serviço
* Aprenda a [escalar o seu cluster para dentro e para fora](service-fabric-cluster-scale-in-out.md)
* Conheça as atualizações de [aplicações](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
