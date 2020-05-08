---
title: Configure as regras de firewall de serviço
description: Configure as regras ip para permitir o acesso a um registo de contentores Azure a partir de endereços IP públicos selecionados ou intervalos de endereços.
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: f6459061ca486b4bf229409e6ec1ed1bd808a474
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984619"
---
# <a name="configure-public-ip-network-rules"></a>Configurar as regras da rede IP pública

Um registo de contentores Azure por padrão aceita ligações através da internet a partir de anfitriões em qualquer rede. Este artigo mostra como configurar o seu registo de contentores para permitir o acesso a partir de endereços IP públicos específicos ou intervalos de endereços. São fornecidos passos equivalentes utilizando o portal Azure CLI e Azure.

As regras da rede IP estão configuradas no ponto final do registo público. As regras da rede IP não se aplicam a pontos finais privados configurados com [Private Link](container-registry-private-link.md)

Configurar as regras de acesso IP está disponível no nível de serviço de registo de contentores **Premium.** Para obter informações sobre os níveis e limites de serviço de registo, consulte os níveis de registo de [contentores de Azure](container-registry-skus.md).

## <a name="access-from-selected-public-network---cli"></a>Acesso a partir de rede pública selecionada - CLI

### <a name="change-default-network-access-to-registry"></a>Alterar o acesso padrão da rede ao registo

Para limitar o acesso a uma rede pública selecionada, altere primeiro a ação predefinida para negar o acesso. Substitua o nome do seu registo no seguinte comando de [atualização az acr:][az-acr-update]

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Adicionar regra de rede ao registo

Utilize o comando de adição de uma regra de [rede az acr][az-acr-network-rule-add] para adicionar uma regra de rede ao seu registo que permite o acesso a partir de um endereço ou intervalo IP público. Por exemplo, substitua o nome do registo de contentores e o endereço IP público de um VM numa rede virtual.

```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --ip-address <public-IP-address>
```

> [!NOTE]
> Depois de adicionar uma regra, leva alguns minutos para a regra fazer efeito.

## <a name="access-from-selected-public-network---portal"></a>Acesso a partir de rede pública selecionada - portal

1. No portal, navegue para o seu registo de contentores.
1. Em **Definições,** selecione **Networking**.
1. No separador **de acesso público,** selecione para permitir o acesso do público a partir de **redes Selecionadas**.
1. Em **Firewall,** introduza um endereço IP público, como o endereço IP público de um VM numa rede virtual. Ou, insira uma gama de endereços na notação CIDR que contenha o endereço IP do VM.
1. Selecione **Guardar**.

![Configure a regra da firewall para o registo de contentores][acr-access-selected-networks]

> [!NOTE]
> Depois de adicionar uma regra, leva alguns minutos para a regra fazer efeito.

> [!TIP]
> Opcionalmente, permita o acesso ao registo a partir de um computador cliente local ou intervalo de endereçoIP. Para permitir este acesso, precisa do endereço IPv4 público do computador. Pode encontrar este endereço pesquisando "qual é o meu endereço IP" num navegador de Internet. O endereço IPv4 do cliente atual também aparece automaticamente quando configura as definições de firewall na página **de Rede** no portal.

## <a name="disable-public-network-access"></a>Desativar o acesso à rede pública

Para limitar o tráfego a redes virtuais utilizando [o Private Link,](container-registry-private-link.md)desative o ponto final do registo. Desativar o ponto final do público sobrepõe-se a todas as configurações de firewall.

### <a name="disable-public-access---portal"></a>Desativar o acesso público - Portal

1. No portal, navegue para o registo do seu contentor e selecione **Definições > Networking**.
1. No separador de **acesso público,** em **Permitir o acesso do público,** selecione **Disabled**. Em seguida, selecione **Guardar**.

![Desativar o acesso público][acr-access-disabled]

## <a name="restore-default-registry-access"></a>Restaurar o acesso ao registo predefinido

Para restaurar o registo para permitir o acesso por predefinição, atualize a ação predefinida. 

### <a name="restore-default-registry-access---portal"></a>Restaurar o acesso ao registo predefinido - portal

1. No portal, navegue para o registo do seu contentor e selecione **Definições > Networking**.
1. Em **Firewall,** selecione cada intervalo de endereços e, em seguida, selecione o ícone Eliminar.
1. No separador **de acesso público,** em **Permitir o acesso do público,** selecione **Todas as redes**. Em seguida, selecione **Guardar**.

![Acesso público de todas as redes][acr-access-all-networks]

## <a name="next-steps"></a>Passos seguintes

* Para restringir o acesso a um registo utilizando um ponto final privado numa rede virtual, consulte [o Configure Azure Private Link para um registo de contentores Azure](container-registry-private-link.md).
* Se precisar de configurar regras de acesso ao registo por detrás de uma firewall de cliente, consulte [as regras do Configure para aceder a um registo de contentores Azure atrás](container-registry-firewall-access-rules.md)de uma firewall .

[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com

[acr-access-selected-networks]: ./media/container-registry-access-selected-networks/acr-access-selected-networks.png
[acr-access-disabled]: ./media/container-registry-access-selected-networks/acr-access-disabled.png
[acr-access-all-networks]: ./media/container-registry-access-selected-networks/acr-access-all-networks.png
