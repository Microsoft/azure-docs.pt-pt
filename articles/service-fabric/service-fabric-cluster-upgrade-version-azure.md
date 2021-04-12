---
title: Gerir atualizações de cluster de tecido de serviço
description: Gerir quando e como o seu cluster de tecido de serviço é atualizado
ms.topic: how-to
ms.date: 03/26/2021
ms.openlocfilehash: 98c3300e5cc51c32d894397839879e25190d979b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731172"
---
# <a name="manage-service-fabric-cluster-upgrades"></a>Gerir atualizações de cluster de tecido de serviço

Um cluster Azure Service Fabric é um recurso que possui, mas é parcialmente gerido pela Microsoft. Eis como gerir quando e como a Microsoft atualiza o seu cluster de Tecidos de Serviço Azure.

Para obter mais informações sobre conceitos e processos de upgrade de clusters, consulte [upgrade e atualização de clusters de tecidos de serviço Azure](service-fabric-cluster-upgrade.md)

## <a name="set-upgrade-mode"></a>Definir o modo de atualização

Pode configurar o seu cluster para receber atualizações automáticas do Service Fabric à medida que forem lançadas pela Microsoft, ou pode escolher manualmente entre uma lista de versões suportadas atualmente, definindo o modo de atualização para o seu cluster. Isto pode ser feito através do controlo do *modo de atualização do Tecido* no portal Azure ou da `upgradeMode` definição no seu modelo de implementação do cluster.

### <a name="azure-portal"></a>Portal do Azure

Utilizando o portal Azure, escolherá entre atualizações automáticas ou manuais ao criar um novo cluster de Tecido de Serviço.

:::image type="content" source="media/service-fabric-cluster-upgrade/portal-new-cluster-upgrade-mode.png" alt-text="Escolha entre atualizações automáticas ou manuais ao criar um novo cluster no portal Azure a partir das opções 'Avançadas'":::

Também pode alternar entre atualizações automáticas ou manuais da secção de **atualizações do Tecido** de um recurso de cluster existente.

:::image type="content" source="./media/service-fabric-cluster-upgrade/fabric-upgrade-mode.png" alt-text="Selecione atualizações automáticas ou manuais na secção 'Upgrades de tecido' do seu recurso cluster no portal Azure":::

### <a name="manual-upgrades-with-azure-portal"></a>Atualizações manuais com portal Azure

Quando seleciona a opção de atualização manual, tudo o que é necessário para iniciar uma atualização é selecionar a partir das versões disponíveis dropdown e, em seguida, *guardar*. A partir daí, a atualização do cluster é iniciada imediatamente.

As políticas de [saúde](#custom-policies-for-manual-upgrades) do cluster (uma combinação de saúde do nó e a saúde a todas as aplicações em execução no cluster) são cumpridas durante a atualização. Se as políticas de saúde do cluster não forem cumpridas, a atualização será revertida.

Depois de ter corrigido os problemas que resultaram na reversão, terá de iniciar novamente a atualização, seguindo os mesmos passos que antes.

### <a name="resource-manager-template"></a>Modelo do Resource Manager

Para alterar o modo de atualização do cluster utilizando um modelo de Gestor de Recursos, especifique o *Automático* ou o *Manual* para a `upgradeMode` propriedade da definição de recursos *Microsoft.ServiceFabric/clusters.* Se escolher atualizações manuais, também desa ajuste `clusterCodeVersion` a uma versão de tecido [suportada](#query-for-supported-cluster-versions)atualmente.

:::image type="content" source="./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG" alt-text="A screenshot mostra um modelo, que é simples texto recuado para refletir a estrutura. Destacam-se as propriedades 'clusterCodeVersion' e 'upgradeMode'.":::

Após a implementação bem sucedida do modelo, serão aplicadas alterações ao modo de atualização do cluster. Se o seu cluster estiver em modo manual, a atualização do cluster arrancará automaticamente.

As políticas de [saúde](#custom-policies-for-manual-upgrades) do cluster (uma combinação de saúde do nó e a saúde a todas as aplicações em execução no cluster) são cumpridas durante a atualização. Se as políticas de saúde do cluster não forem cumpridas, a atualização é revertida.

Depois de ter corrigido os problemas que resultaram na reversão, terá de iniciar novamente a atualização, seguindo os mesmos passos que antes.

## <a name="wave-deployment-for-automatic-upgrades"></a>Implementação de ondas para upgrades automáticos

Com o modo de atualização automática, tem a opção de ativar o seu cluster para a implementação de ondas. Com a implementação de ondas, pode criar um oleoduto para atualizar os seus clusters de teste, fase e produção em sequência, separados por "tempo de cozedura" incorporado para validar as próximas versões do Service Fabric antes de os seus clusters de produção serem atualizados.

### <a name="enable-wave-deployment"></a>Ativar a implantação de ondas

> [!NOTE]
> A implementação de ondas requer a `2020-12-01-preview` versão API (ou posterior) para o seu recurso *Microsoft.ServiceFabric/clusters.*

Para ativar a implementação de ondas para atualização automática, determine primeiro qual a onda para atribuir o seu cluster:

* **Onda 0** `Wave0` (): Os clusters são atualizados assim que uma nova construção do Tecido de Serviço for lançada. Destinado a agrupamentos de teste/dev.
* **Onda 1** `Wave1` (): Os clusters são atualizados uma semana (sete dias) após o lançamento de uma nova construção. Destinado a agrupamentos pré-prod/staging.
* **Onda 2** `Wave2` (): Os clusters são atualizados duas semanas (14 dias) após o lançamento de uma nova construção. Destinado a clusters de produção.

Em seguida, basta adicionar uma `upgradeWave` propriedade ao seu modelo de recursos de cluster com um dos valores de onda listados acima. Certifique-se de que a versão API do seu recurso de cluster é `2020-12-01-preview` ou mais tarde.

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
     ...
        "fabricSettings": [...],
        "managementEndpoint": ...,
        "nodeTypes": [...],
        "provisioningState": ...,
        "reliabilityLevel": ...,
        "upgradeMode": "Automatic",
        "upgradeWave": "Wave1",
       ...
```

Assim que implementar o modelo atualizado, o seu cluster será inscrito na onda especificada para o próximo período de atualização e depois disso.

Pode [registar-se para notificações por e-mail](#register-for-notifications) com links para ajudar ainda mais se uma atualização do cluster falhar.

### <a name="register-for-notifications"></a>Registar-se para receber notificações

Pode registar-se para notificações quando uma atualização do cluster falha. Será enviado um e-mail para o seu endereço de e-mail designado com mais detalhes sobre a falha de upgrade e links para mais ajuda.

> [!NOTE]
> A inscrição na implementação de ondas não é necessária para receber notificações para falhas de atualização.

Para se inscrever em notificações, adicione uma `notifications` secção ao seu modelo de recursos de cluster e designe um ou mais endereços de e-mail *(recetores)* para receber notificações:

```json
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
     ...
        "upgradeMode": "Automatic",
        "upgradeWave": "Wave1",
        "notifications": [
        {
            "isEnabled": true,
            "notificationCategory": "WaveProgress",
            "notificationLevel": "Critical",
            "notificationTargets": [
            {
                "notificationChannel": "EmailUser",
                "receivers": [
                    "devops@contoso.com"
                ]
            }]
        }]
```

Assim que implementar o seu modelo atualizado, será inscrito para notificações de falha de upgrade.

## <a name="custom-policies-for-manual-upgrades"></a>Políticas personalizadas para atualizações manuais

Pode especificar políticas de saúde personalizadas para atualizações manuais de clusters. Estas políticas são aplicadas sempre que seleciona uma nova versão de tempo de execução, que desencadeia o sistema para iniciar a atualização do seu cluster. Se não anular as apólices, os predefinidos são utilizados.

Pode especificar as políticas de saúde personalizadas ou rever as definições atuais sob a secção de **atualizações** do Tecido do seu recurso de cluster no portal Azure, selecionando a opção *Custom* para **a política de upgrade**.

:::image type="content" source="./media/service-fabric-cluster-upgrade/custom-upgrade-policy.png" alt-text="Selecione a opção de atualização 'Personalizada' na secção 'Upgrades de tecido' do seu recurso cluster no portal Azure, de modo a definir políticas de saúde personalizadas durante a atualização":::

## <a name="query-for-supported-cluster-versions"></a>Consulta para versões de cluster suportadas

Pode utilizar [a Azure REST API](/rest/api/azure/) para listar todas as versões de tempo de execução do Tecido de Serviço disponíveis[(clusterVersions](/rest/api/servicefabric/sfrp-api-clusterversions_list)) disponíveis para a localização especificada e a sua subscrição.

Também é possível fazer referência às [versões do Service Fabric](service-fabric-versions.md) para mais detalhes sobre versões e sistemas operativos suportados.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2018-02-01

"value": [
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
    "name": "5.0.1427.9490",
    "type": "Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "5.0.1427.9490",
      "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
      "environment": "Windows"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
    "name": "5.1.1427.9490",
    "type": " Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "5.1.1427.9490",
      "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
      "environment": "Windows"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
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

O `supportExpiryUtc` relatório de saída quando uma determinada libertação expira ou expirou. As versões mais recentes não terão uma data válida, mas sim um valor de *9999-12-31T23:59.99999999*, o que significa apenas que a data de validade ainda não está definida.


## <a name="next-steps"></a>Passos seguintes

* [Gerir atualizações de tecido de serviço](service-fabric-cluster-upgrade-version-azure.md)
* Personalize as [definições do cluster de tecido de serviço](service-fabric-cluster-fabric-settings.md)
* [Escalar o seu cluster para dentro e para fora](service-fabric-cluster-scale-in-out.md)
* Saiba mais [sobre atualizações de aplicações](service-fabric-application-upgrade.md)


<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
