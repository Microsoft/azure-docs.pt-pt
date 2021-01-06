---
title: Regras de acesso a firewall
description: Configure as regras de acesso a um registo de contentores Azure por trás de uma firewall, permitindo o acesso à API REST e aos nomes de domínio de ponto final de dados ou intervalos de endereços IP específicos do serviço.
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: 548d64632c1d726111770dfb49f705d31f5ca714
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935993"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Configure regras para aceder a um registo de contentores Azure atrás de uma firewall

Este artigo explica como configurar regras na sua firewall para permitir o acesso a um registo de contentores Azure. Por exemplo, um dispositivo Azure IoT Edge atrás de uma firewall ou servidor de procuração pode precisar de aceder a um registo de contentores para tirar uma imagem de um recipiente. Ou, um servidor bloqueado numa rede no local pode precisar de acesso para empurrar uma imagem.

Se, em vez disso, pretender configurar o acesso à rede de entrada a um registo de contentores apenas dentro de uma rede virtual Azure, consulte [o Link Privado Configure Azure para um registo de contentores Azure](container-registry-private-link.md).

## <a name="about-registry-endpoints"></a>Sobre os pontos finais do registo

Para puxar ou empurrar imagens ou outros artefactos para um registo de contentores Azure, um cliente como um daemon Docker precisa interagir sobre HTTPS com dois pontos finais distintos. Para os clientes que acedem a um registo por detrás de uma firewall, é necessário configurar regras de acesso para ambos os pontos finais. Ambos os pontos finais são alcançados sobre o porto 443.

* **Recenseado RÍP** do Registo - As operações de autenticação e gestão de registo são tratadas através do ponto final público da API REST do registo. Este ponto final é o nome do servidor de login do registo. Exemplo: `myregistry.azurecr.io`

* **Ponto final de armazenamento (dados)** - A Azure [atribui o armazenamento de bolhas](container-registry-storage.md) nas contas de Armazenamento Azure em nome de cada registo para gerir os dados para imagens de contentores e outros artefactos. Quando um cliente acede a camadas de imagem num registo de contentores Azure, faz pedidos utilizando um ponto final de conta de armazenamento fornecido pelo registo.

Se o seu registo for [geo-replicado,](container-registry-geo-replication.md)um cliente poderá ter de interagir com o ponto final de dados numa região específica ou em várias regiões replicadas.

## <a name="allow-access-to-rest-and-data-endpoints"></a>Permitir o acesso ao REST e aos pontos finais de dados

* **PONTO final REST** - Permitir o acesso ao nome do servidor de login de registo totalmente `<registry-name>.azurecr.io` qualificado, ou a uma gama de endereços IP associada
* **Ponto final de armazenamento (dados)** - Permitir o acesso a todas as contas de armazenamento de blob Azure usando o wildcard `*.blob.core.windows.net` , ou uma gama de endereços IP associada.
> [!NOTE]
> O Registo de Contentores Azure está a introduzir [pontos finais de dados dedicados,](#enable-dedicated-data-endpoints)permitindo-lhe estender rigorosamente as regras de firewall do cliente para o armazenamento do seu registo. Opcionalmente, permitir pontos finais de dados em todas as regiões onde o registo está localizado ou replicado, utilizando o formulário `<registry-name>.<region>.data.azurecr.io` .

## <a name="allow-access-by-ip-address-range"></a>Permitir o acesso através do intervalo de endereços IP

Se a sua organização tiver políticas que permitam o acesso apenas a endereços IP específicos ou intervalos de endereços, baixe [as gamas Azure IP e tags](https://www.microsoft.com/download/details.aspx?id=56519)de serviço – Public Cloud .

Para encontrar as gamas IP do ponto final ACR REST para as quais necessita de acesso, procure **o AzureContainerRegistry** no ficheiro JSON.

> [!IMPORTANT]
> Os intervalos de endereços IP para serviços Azure podem ser alterados e as atualizações são publicadas semanalmente. Descarregue regularmente o ficheiro JSON e faça as atualizações necessárias nas suas regras de acesso. Se o seu cenário envolver configurar as regras do grupo de segurança de rede numa rede virtual Azure ou utilizar o Azure Firewall, utilize a [etiqueta de serviço](#allow-access-by-service-tag) **AzureContainerRegistry.**
>

### <a name="rest-ip-addresses-for-all-regions"></a>Endereços IP REST para todas as regiões

```json
{
  "name": "AzureContainerRegistry",
  "id": "AzureContainerRegistry",
  "properties": {
    "changeNumber": 10,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.66.140.72/29",
    [...]
```

### <a name="rest-ip-addresses-for-a-specific-region"></a>Endereços IP REST para uma região específica

Procure a região específica, como **AzureContainerRegistry.AustraliaEast**.

```json
{
  "name": "AzureContainerRegistry.AustraliaEast",
  "id": "AzureContainerRegistry.AustraliaEast",
  "properties": {
    "changeNumber": 1,
    "region": "australiaeast",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.70.72.136/29",
    [...]
```

### <a name="storage-ip-addresses-for-all-regions"></a>Endereços IP de armazenamento para todas as regiões

```json
{
  "name": "Storage",
  "id": "Storage",
  "properties": {
    "changeNumber": 19,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "13.65.107.32/28",
    [...]
```

### <a name="storage-ip-addresses-for-specific-regions"></a>Endereços IP de armazenamento para regiões específicas

Procure a região específica, como **Storage.AustraliaCentral.**

```json
{
  "name": "Storage.AustraliaCentral",
  "id": "Storage.AustraliaCentral",
  "properties": {
    "changeNumber": 1,
    "region": "australiacentral",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "52.239.216.0/23"
    [...]
```

## <a name="allow-access-by-service-tag"></a>Permitir o acesso por etiqueta de serviço

Numa rede virtual Azure, utilize regras de segurança de rede para filtrar o tráfego de um recurso como uma máquina virtual para um registo de contentores. Para simplificar a criação das regras da rede Azure, utilize a etiqueta de [serviço](../virtual-network/network-security-groups-overview.md#service-tags) **AzureContainerRegistry** . Uma etiqueta de serviço representa um grupo de prefixos de endereço IP para aceder a um serviço Azure globalmente ou por região de Azure. A etiqueta é automaticamente atualizada quando os endereços mudam. 

Por exemplo, crie uma regra de grupo de segurança de rede de saída com destino **AzureContainerRegistry** para permitir o tráfego a um registo de contentores Azure. Para permitir o acesso à etiqueta de serviço apenas numa região específica, especifique a região no seguinte formato: **AzureContainerRegistry**. [nome *da região].*

## <a name="enable-dedicated-data-endpoints"></a>Ativar pontos finais de dados dedicados 

> [!WARNING]
> Se previamente configurado o acesso ao firewall do cliente aos `*.blob.core.windows.net` pontos finais existentes, a mudança para pontos finais de dados dedicados afetará a conectividade do cliente, causando falhas de puxar. Para garantir que os clientes têm acesso consistente, adicione as novas regras do ponto final de dados às regras de firewall do cliente. Uma vez concluído, ative os pontos finais de dados dedicados para os seus registos utilizando o CLI Azure ou outras ferramentas.

Os pontos finais de dados dedicados são uma característica opcional do nível de serviço de registo de contentores **Premium.** Para obter informações sobre os níveis e limites do serviço de registo de registo, consulte [os níveis de serviço de registo de contentores Azure](container-registry-skus.md). 

Pode ativar pontos finais de dados dedicados utilizando o portal Azure ou o CLI Azure. Os pontos finais de dados seguem um padrão regional, `<registry-name>.<region>.data.azurecr.io` . Num registo geo-replicado, permitir pontos finais de dados permite pontos finais em todas as regiões réplicas.

### <a name="portal"></a>Portal

Para ativar os pontos finais de dados utilizando o portal:

1. Navegue para o seu registo de contentores.
1. **Selecione acesso** público em rede  >  .
1. Selecione a caixa de verificação **de ponto final de dados dedicada a Ativa.**
1. Selecione **Guardar**.

Os pontos finais de dados ou pontos finais aparecem no portal.

:::image type="content" source="media/container-registry-firewall-access-rules/dedicated-data-endpoints-portal.png" alt-text="Pontos finais de dados dedicados no portal":::

### <a name="azure-cli"></a>CLI do Azure

Para ativar os pontos finais de dados utilizando o Azure CLI, utilize a versão Azure CLI 2.4.0 ou superior. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

O seguinte comando [de atualização az acr][az-acr-update] permite pontos finais de dados dedicados num *mioctório* de registo . 

```azurecli
az acr update --name myregistry --data-endpoint-enabled
```

Para visualizar os pontos finais dos dados, utilize o comando [az acr show-endpoints:][az-acr-show-endpoints]

```azurecli
az acr show-endpoints --name myregistry
```

Saída para fins de demonstração mostra dois pontos finais regionais

```
{
    "loginServer": "myregistry.azurecr.io",
    "dataEndpoints": [
        {
            "region": "eastus",
            "endpoint": "myregistry.eastus.data.azurecr.io",
        },
        {
            "region": "westus",
            "endpoint": "myregistry.westus.data.azurecr.io",
        }
    ]
}
```

Depois de configurar pontos finais de dados dedicados para o seu registo, pode ativar as regras de acesso ao firewall do cliente para os pontos finais de dados. Permitir regras de acesso ao ponto final de dados para todas as regiões de registo requeridas.

## <a name="configure-client-firewall-rules-for-mcr"></a>Configure as regras de firewall do cliente para MCR

Se precisar de aceder ao Microsoft Container Registry (MCR) por detrás de uma firewall, consulte as orientações para configurar as regras de [firewall do cliente MCR](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md). MCR é o registo primário de todas as imagens de estivadores publicadas pela Microsoft, como imagens do Windows Server.

## <a name="next-steps"></a>Passos seguintes

* Conheça as [melhores práticas do Azure para a segurança da rede](../security/fundamentals/network-best-practices.md)

* Saiba mais sobre [grupos de segurança](../virtual-network/network-security-groups-overview.md) numa rede virtual Azure

* Saiba mais sobre a criação [do Private Link](container-registry-private-link.md) para um registo de contentores

* Saiba mais sobre [os pontos finais de dados dedicados](https://azure.microsoft.com/blog/azure-container-registry-mitigating-data-exfiltration-with-dedicated-data-endpoints/) para o Registo de Contentores Azure



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

[az-acr-update]: /cli/azure/acr#az-acr-update
[az-acr-show-endpoints]: /cli/azure/acr#az-acr-show-endpoints