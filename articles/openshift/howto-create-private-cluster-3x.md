---
title: Crie um cluster privado com Azure Red Hat OpenShift 3.11 | Microsoft Docs
description: Criar um cluster privado com Azure Red Hat OpenShift 3.11
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 03/02/2020
keywords: aro, openshift, cluster privado, chapéu vermelho
ms.openlocfilehash: 28a21635da770a990a21e0da2a8034c754f4f7aa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "100633245"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>Criar um cluster privado com Azure Red Hat OpenShift 3.11

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11 será retirado a 30 de junho de 2022. O apoio à criação de novos clusters Azure Red Hat OpenShift 3.11 continua até 30 de novembro de 2020. Após a reforma, os restantes clusters Azure Red Hat OpenShift 3.11 serão encerrados para evitar vulnerabilidades de segurança.
> 
> Siga este guia para [criar um cluster Azure Red Hat OpenShift 4](tutorial-create-cluster.md).
> Se tiver questões específicas, [contacte-nos.](mailto:arofeedback@microsoft.com)

Os agrupamentos privados proporcionam os seguintes benefícios:

* Os clusters privados não expõem componentes de planos de controlo de cluster (como os servidores API) num endereço IP público.
* A rede virtual de um cluster privado é configurável pelos clientes, permitindo a criação de redes para permitir o observamento com outras redes virtuais, incluindo ambientes ExpressRoute. Também pode configurar DNS personalizados na rede virtual para integrar-se com serviços internos.

## <a name="before-you-begin"></a>Antes de começar

Os campos na configuração seguinte são novos e devem ser incluídos na configuração do seu cluster. `managementSubnetCidr` deve estar dentro da rede virtual do cluster e é usado pela Azure para gerir o cluster.

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

Um cluster privado pode ser implantado usando os scripts de amostra fornecidos abaixo. Uma vez implantado o cluster, execute o `cluster get` comando e veja a propriedade para determinar o endereço IP privado do servidor `properties.FQDN` API OpenShift.

A rede virtual de cluster terá sido criada com permissões para que possa modificá-la. Em seguida, pode configurar o networking para aceder à rede virtual (ExpressRoute, VPN, observação de redes virtuais) conforme necessário para as suas necessidades.

Se alterar os servidores de nomes DNS na rede virtual do cluster, terá de emitir uma atualização sobre o cluster com a `properties.RefreshCluster` propriedade definida para que os `true` VMs possam ser remimagemed. Esta atualização permitir-lhes-á recolher os novos serviçais.

## <a name="sample-configuration-scripts"></a>Scripts de configuração da amostra

Utilize os scripts de amostra nesta secção para configurar e implantar o seu cluster privado.

### <a name="environment"></a>Ambiente

Preencha as variáveis ambientais abaixo como usando os seus próprios valores.

> [!NOTE]
> A localização deve ser definida, uma `eastus2` vez que este é atualmente o único local suportado para aglomerados privados.

``` bash
export CLUSTER_NAME=
export LOCATION=eastus2
export TOKEN=$(az account get-access-token --query 'accessToken' -o tsv)
export SUBID=
export TENANT_ID=
export ADMIN_GROUP=
export CLIENT_ID=
export SECRET=
```

### <a name="private-clusterjson"></a>private-cluster.jsem

Utilizando as variáveis ambientais definidas acima, aqui está uma configuração de cluster de amostra com cluster privado ativado.

```json
{
   "location": "$LOCATION",
   "name": "$CLUSTER_NAME",
   "properties": {
       "openShiftVersion": "v3.11",
       "networkProfile": {
           "vnetCIDR": "10.0.0.0/8",
           "managementSubnetCIDR" : "10.0.1.0/24"
       },
       "authProfile": {
           "identityProviders": [
               {
                   "name": "Azure AD",
                   "provider": {
                       "kind": "AADIdentityProvider",
                       "clientId": "$CLIENT_ID",
                       "secret": "$SECRET",
                       "tenantId": "$TENANT_ID",
                       "customerAdminGroupID": "$ADMIN_GROUP"
                   }
               }
           ]
       },
       "masterPoolProfile": {
           "name": "master",
           "count": 3,
           "vmSize": "Standard_D4s_v3",
           "osType": "Linux",
           "subnetCIDR": "10.0.0.0/24",
           "apiProperties": {
                "privateApiServer": true
            }
       },
       "agentPoolProfiles": [
           {
               "role": "compute",
               "name": "compute",
               "count": 1,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           },
           {
               "role": "infra",
               "name": "infra",
               "count": 3,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           }
       ],
       "routerProfiles": [
           {
               "name": "default"
           }
       ]
   }
}
```

## <a name="deploy-a-private-cluster"></a>Implementar um cluster privado

Depois de configurar o seu cluster privado com os scripts de amostra acima, executar o seguinte comando para implantar o seu cluster privado.

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>Passos seguintes

Para saber como aceder à consola OpenShift, consulte [Web Console Walkthrough](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html).
