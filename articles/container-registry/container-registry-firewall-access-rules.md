---
title: Regras de acesso à firewall
description: Configure as regras para aceder a um registo de contentores Azure por trás de uma firewall, permitindo o acesso a ("whitelisting") nomes de domínio de ponto final de dados e endereços IP específicos do serviço.
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: 109764a5697920547230530de41a3e5acfe0117d
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701934"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Configure regras para aceder a um registo de contentores Azure atrás de uma firewall

Este artigo explica como configurar regras na sua firewall para permitir o acesso a um registo de contentores Azure. Por exemplo, um dispositivo Azure IoT Edge atrás de uma firewall ou servidor proxy pode precisar de aceder a um registo de contentores para puxar uma imagem de recipiente. Ou, um servidor bloqueado numa rede no local pode precisar de acesso para empurrar uma imagem.

Se pretender configurar o acesso à rede de entrada a um registo de contentores apenas dentro de uma rede virtual Azure, consulte [o Configure Azure Private Link para um registo de contentores Azure](container-registry-private-link.md).

## <a name="about-registry-endpoints"></a>Sobre os pontos finais do registo

Para puxar ou empurrar imagens ou outros artefactos para um registo de contentores Azure, um cliente como um daemon Docker precisa interagir sobre HTTPS com dois pontos finais distintos. Para clientes que acedem a um registo por detrás de uma firewall, é necessário configurar regras de acesso para ambos os pontos finais. Ambos os pontos finais são alcançados sobre a porta 443.

* Ponto final da **API DO REGISTO -** As operações de autenticação e gestão do registo são tratadas através do ponto final da API do registo. Este ponto final é o nome do servidor de login do registo. Exemplo: `myregistry.azurecr.io`

* Ponto final de **armazenamento (dados)** - O Azure [atribui o armazenamento de blob](container-registry-storage.md) nas contas de Armazenamento Azure em nome de cada registo para gerir os dados para imagens de contentores e outros artefactos. Quando um cliente acede a camadas de imagem num registo de contentores Azure, faz pedidos utilizando um ponto final de conta de armazenamento fornecido pelo registo.

Se o seu registo for [geo-replicado,](container-registry-geo-replication.md)um cliente poderá ter de interagir com o ponto final dos dados numa região específica ou em várias regiões replicadas.

## <a name="allow-access-to-rest-and-data-endpoints"></a>Permitir o acesso ao REST e aos pontos finais dos dados

* **Ponto final** DO REST - Permitir o acesso ao nome do servidor de login de registo totalmente qualificado, ou uma gama de `<registry-name>.azurecr.io` endereços IP associado
* **Ponto final de armazenamento (dados)** - Permitir o acesso a todas as contas de armazenamento de blob Azure utilizando o wildcard, ou uma gama de `*.blob.core.windows.net` endereços IP associado.
> [!NOTE]
> O Registo de Contentores Azure está a introduzir [pontos finais de dados dedicados,](#enable-dedicated-data-endpoints)permitindo-lhe analisar rigorosamente as regras de firewall do cliente para o seu armazenamento de registo. Faculte opcionalmente os pontos finais dos dados em todas as regiões onde o registo está localizado ou replicado, utilizando o formulário `<registry-name>.<region>.data.azurecr.io` .

## <a name="allow-access-by-ip-address-range"></a>Permitir o acesso através da gama de endereços IP

Se a sua organização tiver políticas para permitir o acesso apenas a endereços IP específicos ou intervalos de endereços, baixe [as gamas ip e etiquetas](https://www.microsoft.com/download/details.aspx?id=56519)de serviço do Azure IP – Nuvem Pública .

Para encontrar as gamas IP do ponto final do ACR REST para as quais necessita de permitir o acesso, procure o **AzureContainerRegistry** no ficheiro JSON.

> [!IMPORTANT]
> As gamas de endereços IP para serviços Azure podem ser alteradas e as atualizações são publicadas semanalmente. Faça o download regular do ficheiro JSON e faça as atualizações necessárias nas suas regras de acesso. Se o seu cenário implicar configurar as regras do grupo de segurança da rede numa rede virtual Azure ou utilizar o Azure Firewall, utilize a etiqueta de [serviço](#allow-access-by-service-tag) **AzureContainerRegistry.**
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

## <a name="enable-dedicated-data-endpoints"></a>Ativar pontos finais de dados dedicados 

> [!WARNING]
> Se configurar previamente o acesso à firewall do cliente aos `*.blob.core.windows.net` pontos finais existentes, mudar para pontos finais de dados dedicados terá impacto na conectividade do cliente, causando falhas de pull. Para garantir que os clientes têm acesso consistente, adicione as novas regras de ponto final de dados às regras de firewall do cliente. Uma vez concluído, ative pontos finais de dados dedicados para os seus registos utilizando o Azure CLI ou outras ferramentas.

Pontos finais de dados dedicados é uma característica opcional do nível de serviço de registo de contentores **Premium.** Para obter informações sobre os níveis e limites de serviço de registo, consulte os níveis de serviço do Registo de [Contentores De Azure](container-registry-skus.md). 

Pode ativar pontos finais dedicados de dados utilizando o portal Azure ou o Azure CLI. Os pontos finais dos dados seguem um padrão regional, `<registry-name>.<region>.data.azurecr.io` . Num registo geo-replicado, permitir pontos finais de dados permite pontos finais em todas as regiões de réplica.

### <a name="portal"></a>Portal

Para ativar pontos finais de dados utilizando o portal:

1. Navegue para o seu registo de contentores.
1. Selecione Acesso público **em rede**  >  **Public access**.
1. Selecione a caixa de verificação **de ponto final de dados ativada.**
1. Selecione **Guardar**.

Os pontos finais ou pontos finais dos dados aparecem no portal.

![Pontos finais de dados dedicados no portal](./media/container-registry-firewall-access-rules/dedicated-data-endpoints-portal.png)

### <a name="azure-cli"></a>CLI do Azure

Para ativar os pontos finais de dados utilizando o Azure CLI, utilize a versão Azure CLI 2.4.0 ou superior. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

O comando de [atualização az acr az][az-acr-update] permite pontos finais de dados dedicados num *registo de registo*. 

```azurecli
az acr update --name myregistry --data-endpoint-enabled
```

Para visualizar os pontos finais dos dados, utilize o comando [az acr show-endpoints:][az-acr-show-endpoints]

```azurecli
az acr show-endpoints --name myregistry
```

Produção para efeitos de demonstração mostra dois pontos finais regionais

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

Depois de configurar pontos finais dedicados para o seu registo, pode ativar as regras de acesso à firewall do cliente para os pontos finais dos dados. Ativar as regras de acesso ao ponto final dos dados para todas as regiões de registo necessárias.

## <a name="configure-client-firewall-rules-for-mcr"></a>Configure as regras de firewall do cliente para MCR

Se precisar de aceder ao Microsoft Container Registry (MCR) por trás de uma firewall, consulte as orientações para configurar as regras de firewall do [cliente MCR](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md). McR é o registo principal de todas as imagens de docker publicadas pela Microsoft, como imagens do Windows Server.

## <a name="next-steps"></a>Passos seguintes

* Conheça [as melhores práticas do Azure para a segurança da rede](../security/fundamentals/network-best-practices.md)

* Saiba mais sobre [grupos de segurança](/azure/virtual-network/security-overview) numa rede virtual Azure

* Saiba mais sobre a criação [de Link Privado](container-registry-private-link.md) para um registo de contentores

* Saiba mais sobre [pontos finais dedicados](https://azure.microsoft.com/blog/azure-container-registry-mitigating-data-exfiltration-with-dedicated-data-endpoints/) para o Registo de Contentores do Azure



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

[az-acr-update]: /cli/azure/acr#az-acr-update
[az-acr-show-endpoints]: /cli/azure/acr#az-acr-show-endpoints

