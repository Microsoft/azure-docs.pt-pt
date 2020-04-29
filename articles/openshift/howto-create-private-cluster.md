---
title: Crie um cluster privado com o Azure Red Hat OpenShift 3.11 [ Microsoft Docs
description: Crie um cluster privado com o Azure Red Hat OpenShift 3.11
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/02/2020
keywords: aro, openshift, cluster privado, chapéu vermelho
ms.openlocfilehash: f4ce6c79fa9fe6d05fdea4b877a8aa7faf404a9b
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204173"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>Crie um cluster privado com o Azure Red Hat OpenShift 3.11

Os clusters privados proporcionam os seguintes benefícios:

* Os clusters privados não expõem componentes de planos de controlo de cluster (como os servidores API) num endereço IP público.
* A rede virtual de um cluster privado é configurável pelos clientes, permitindo-lhe configurar networking para permitir o peering com outras redes virtuais, incluindo ambientes ExpressRoute. Também pode configurar DNS personalizados na rede virtual para integrar com serviços internos.

## <a name="before-you-begin"></a>Antes de começar

Os campos do seguinte corte de configuração são novos e devem ser incluídos na configuração do seu cluster. `managementSubnetCidr`deve estar dentro da rede virtual do cluster e é utilizado pelo Azure para gerir o cluster.

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

Um cluster privado pode ser implantado usando os scripts de amostra fornecidos abaixo. Uma vez que o cluster `cluster get` é implantado, execute o comando e veja a `properties.FQDN` propriedade para determinar o endereço IP privado do servidor OpenShift API.

A rede virtual do cluster terá sido criada com permissões para que possa modificá-la. Em seguida, pode configurar a rede de rede para aceder à rede virtual (ExpressRoute, VPN, virtual network peering) conforme necessário para as suas necessidades.

Se alterar os servidores de nomes DNS na rede virtual do cluster, `properties.RefreshCluster` terá de `true` emitir uma atualização no cluster com a propriedade definida para que os VMs possam ser reimagemdos. Esta atualização permitir-lhes-á recolher os novos servidores de nomes.

## <a name="sample-configuration-scripts"></a>Scripts de configuração de amostras

Utilize os scripts de amostra nesta secção para configurar e implementar o seu cluster privado.

### <a name="environment"></a>Ambiente

Preencha as variáveis ambientais abaixo como usando os seus próprios valores.

> [!NOTE]
> A localização deve `eastus2` ser definida, uma vez que esta é atualmente a única localização suportada para clusters privados.

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

### <a name="private-clusterjson"></a>cluster privado.json

Utilizando as variáveis ambientais definidas acima, aqui está uma configuração de cluster de amostra com cluster privado habilitado.

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

Depois de configurar o seu cluster privado com os scripts de amostra acima, execute o seguinte comando para implantar o seu cluster privado.

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>Passos seguintes

Para saber como aceder à consola OpenShift, consulte [o Web Console Walkthrough](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html).
