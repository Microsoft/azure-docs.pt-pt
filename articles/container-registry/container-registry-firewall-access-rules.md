---
title: Regras de acesso de firewall
description: Configure regras para acessar um registro de contêiner do Azure por trás de um firewall, permitindo o acesso a API REST ("lista de permissões") e nomes de domínio de ponto de extremidade de armazenamento ou intervalos de endereços IP específicos do serviço.
ms.topic: article
ms.date: 07/17/2019
ms.openlocfilehash: 4d3c4ff4ca19d8b563c185e5c314011823081df1
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75745199"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Configurar regras para acessar um registro de contêiner do Azure atrás de um firewall

Este artigo explica como configurar regras no firewall para permitir o acesso a um registro de contêiner do Azure. Por exemplo, um dispositivo Azure IoT Edge por trás de um firewall ou servidor proxy pode precisar acessar um registro de contêiner para efetuar pull de uma imagem de contêiner. Ou, um servidor bloqueado em uma rede local pode precisar de acesso para enviar por push uma imagem.

Se, em vez disso, você quiser configurar regras de acesso à rede de entrada em um registro de contêiner somente dentro de uma rede virtual do Azure ou de um intervalo de endereços IP públicos, consulte [restringir o acesso a um registro de contêiner do Azure de uma rede virtual](container-registry-vnet.md).

## <a name="about-registry-endpoints"></a>Sobre os pontos de extremidade do registro

Para efetuar pull ou enviar por push imagens ou outros artefatos para um registro de contêiner do Azure, um cliente como um daemon do Docker precisa interagir via HTTPS com dois pontos de extremidade distintos.

* **Ponto de extremidade da API REST do registro** -as operações de gerenciamento de autenticação e registro são manipuladas por meio do ponto de extremidade da API REST pública do registro Esse ponto de extremidade é o nome do servidor de logon do registro ou um intervalo de endereços IP associado. 

* **Ponto de extremidade de armazenamento** -o Azure [aloca o armazenamento de BLOBs](container-registry-storage.md) nas contas de armazenamento do Azure em nome de cada registro para gerenciar os dados de imagens de contêiner e outros artefatos. Quando um cliente acessa camadas de imagem em um registro de contêiner do Azure, ele faz solicitações usando um ponto de extremidade de conta de armazenamento fornecido pelo registro.

Se o registro for [replicado geograficamente](container-registry-geo-replication.md), um cliente poderá precisar interagir com pontos de extremidade de armazenamento e REST em uma região específica ou em várias regiões replicadas.

## <a name="allow-access-to-rest-and-storage-domain-names"></a>Permitir acesso a nomes de domínio de armazenamento e REST

* **Ponto de extremidade REST** -permitir acesso ao nome do servidor de logon totalmente qualificado do registro, como `myregistry.azurecr.io`
* **Ponto de extremidade de armazenamento (dados)** – permitir acesso a todas as contas de armazenamento de BLOBs do Azure usando o curinga `*.blob.core.windows.net`


## <a name="allow-access-by-ip-address-range"></a>Permitir acesso por intervalo de endereços IP

Se sua organização tiver políticas para permitir o acesso somente a endereços IP ou intervalos de endereços específicos, baixe [intervalos de IP do Azure e marcas de serviço – nuvem pública](https://www.microsoft.com/download/details.aspx?id=56519).

Para localizar os intervalos de IP do ponto de extremidade REST do ACR para o qual você precisa permitir o acesso, procure **AzureContainerRegistry** no arquivo JSON.

> [!IMPORTANT]
> Os intervalos de endereços IP para os serviços do Azure podem ser alterados e as atualizações são publicadas semanalmente. Baixe o arquivo JSON regularmente e faça as atualizações necessárias em suas regras de acesso. Se seu cenário envolve a configuração de regras de grupo de segurança de rede em uma rede virtual do Azure para acessar o registro de contêiner do Azure, use a [marca de serviço](#allow-access-by-service-tag) **AzureContainerRegistry** em vez disso.
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

Pesquise a região específica, como **AzureContainerRegistry. AustraliaEast**.

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

Pesquise a região específica, como **Storage. AustraliaCentral**.

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

## <a name="allow-access-by-service-tag"></a>Permitir acesso por marca de serviço

Em uma rede virtual do Azure, use as regras de segurança de rede para filtrar o tráfego de um recurso, como uma máquina virtual, para um registro de contêiner. Para simplificar a criação das regras de rede do Azure, use a [marca de serviço](../virtual-network/security-overview.md#service-tags) **AzureContainerRegistry** . Uma marca de serviço representa um grupo de prefixos de endereço IP para acessar um serviço do Azure globalmente ou por região do Azure. A marca é atualizada automaticamente quando os endereços são alterados. 

Por exemplo, crie uma regra de grupo de segurança de rede de saída com **AzureContainerRegistry** de destino para permitir o tráfego para um registro de contêiner do Azure. Para permitir o acesso à marca de serviço somente em uma região específica, especifique a região no seguinte formato: **AzureContainerRegistry**. [*nome da região*].

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [as práticas recomendadas do Azure para segurança de rede](../security/fundamentals/network-best-practices.md)

* Saiba mais sobre [grupos de segurança](/azure/virtual-network/security-overview) em uma rede virtual do Azure



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

