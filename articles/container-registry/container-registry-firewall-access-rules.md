---
title: Regras de acesso à firewall
description: Configure as regras para aceder a um registo de contentores Azure por trás de uma firewall, permitindo o acesso a ("whitelisting") nomes de domínio de ponto final rest API e armazenamento ou intervalos de endereços IP específicos do serviço.
ms.topic: article
ms.date: 02/11/2020
ms.openlocfilehash: 06fedea2adf5e73929f5752279f2bd7e7227e570
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77168023"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Configure regras para aceder a um registo de contentores Azure atrás de uma firewall

Este artigo explica como configurar regras na sua firewall para permitir o acesso a um registo de contentores Azure. Por exemplo, um dispositivo Azure IoT Edge atrás de uma firewall ou servidor proxy pode precisar de aceder a um registo de contentores para puxar uma imagem de recipiente. Ou, um servidor bloqueado numa rede no local pode precisar de acesso para empurrar uma imagem.

Se pretender configurar as regras de acesso à rede de entrada num registo de contentores apenas dentro de uma rede virtual Azure ou de uma gama pública de endereços IP, consulte Restringir o [acesso a um registo de contentores Azure a partir de uma rede virtual](container-registry-vnet.md).

## <a name="about-registry-endpoints"></a>Sobre os pontos finais do registo

Para puxar ou empurrar imagens ou outros artefactos para um registo de contentores Azure, um cliente como um daemon Docker precisa interagir sobre HTTPS com dois pontos finais distintos.

* Ponto final da **API DO REGISTO -** As operações de autenticação e gestão do registo são tratadas através do ponto final da API do registo. Este ponto final é o nome do servidor de login do registo, ou um intervalo de endereço IP associado. 

* **Ponto final** de armazenamento - O Azure [atribui o armazenamento de blob](container-registry-storage.md) em contas de Armazenamento Azure em nome de cada registo para gerir os dados para imagens de contentores e outros artefactos. Quando um cliente acede a camadas de imagem num registo de contentores Azure, faz pedidos utilizando um ponto final de conta de armazenamento fornecido pelo registo.

Se o seu registo for [geo-replicado,](container-registry-geo-replication.md)um cliente poderá ter de interagir com rest e pontos finais de armazenamento numa região específica ou em várias regiões replicadas.

## <a name="allow-access-to-rest-and-storage-domain-names"></a>Permitir o acesso aos nomes de domínio REST e armazenamento

* **Ponto final REST** - Permitir o acesso ao nome do servidor de login de registo totalmente qualificado, como`myregistry.azurecr.io`
* **Ponto final de armazenamento (dados)** - Permitir o acesso a todas as contas de armazenamento de blob Azure utilizando o wildcard`*.blob.core.windows.net`


## <a name="allow-access-by-ip-address-range"></a>Permitir o acesso através da gama de endereços IP

Se a sua organização tiver políticas para permitir o acesso apenas a endereços IP específicos ou intervalos de endereços, baixe [as gamas ip e etiquetas](https://www.microsoft.com/download/details.aspx?id=56519)de serviço do Azure IP – Nuvem Pública .

Para encontrar as gamas IP do ponto final do ACR REST para as quais necessita de permitir o acesso, procure o **AzureContainerRegistry** no ficheiro JSON.

> [!IMPORTANT]
> As gamas de endereços IP para serviços Azure podem ser alteradas e as atualizações são publicadas semanalmente. Faça o download regular do ficheiro JSON e faça as atualizações necessárias nas suas regras de acesso. Se o seu cenário implicar configurar as regras do grupo de segurança da rede numa rede virtual Azure para aceder ao Registo de Contentores Azure, utilize a etiqueta de [serviço](#allow-access-by-service-tag) **AzureContainerRegistry.**
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

Pesquisa rumo à região específica, como O Registo de **Contentores.AustraliaEast**.

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

Pesquisa rumo à região específica, como **Storage.AustraliaCentral**.

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

Numa rede virtual Azure, utilize regras de segurança da rede para filtrar o tráfego de um recurso, como uma máquina virtual para um registo de contentores. Para simplificar a criação das regras da rede Azure, utilize a etiqueta de [serviço](../virtual-network/security-overview.md#service-tags) **AzureContainerRegistry** . Uma etiqueta de serviço representa um grupo de prefixos de endereço IP para aceder a um serviço Azure globalmente ou por região de Azure. A etiqueta é automaticamente atualizada quando os endereços mudam. 

Por exemplo, criar uma regra de grupo de segurança de rede de saída com o destino **AzureContainerRegistry** para permitir o tráfego para um registo de contentores Azure. Para permitir o acesso à etiqueta de serviço apenas numa região específica, especifique a região no seguinte formato: **AzureContainerRegistry**. [nome da*região].*

## <a name="configure-client-firewall-rules-for-mcr"></a>Configure as regras de firewall do cliente para MCR

Se precisar de aceder ao Microsoft Container Registry (MCR) por trás de uma firewall, consulte as orientações para configurar as regras de firewall do [cliente MCR](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md). McR é o registo principal de todas as imagens de docker publicadas pela Microsoft, como imagens do Windows Server.

## <a name="next-steps"></a>Passos seguintes

* Conheça [as melhores práticas do Azure para a segurança da rede](../security/fundamentals/network-best-practices.md)

* Saiba mais sobre [grupos de segurança](/azure/virtual-network/security-overview) numa rede virtual Azure



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

