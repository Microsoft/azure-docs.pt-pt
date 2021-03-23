---
title: Configurar o acesso ao registo público
description: Configurar regras IP para permitir o acesso a um registo de contentores Azure a partir de endereços IP públicos selecionados ou intervalos de endereços.
ms.topic: article
ms.date: 03/08/2021
ms.openlocfilehash: 727aa1dc028b5f52a022e54c2cd252ae372e78fe
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773067"
---
# <a name="configure-public-ip-network-rules"></a>Configurar regras públicas de rede IP

Um registo de contentores Azure por padrão aceita ligações através da internet de anfitriões em qualquer rede. Este artigo mostra como configurar o seu registo de contentores para permitir o acesso a partir de apenas endereços IP públicos específicos ou intervalos de endereços. São fornecidos passos equivalentes utilizando o portal Azure CLI e Azure.

As regras da rede IP estão configuradas no ponto final do registo público. As regras da rede IP não se aplicam a pontos finais privados configurados com [Ligação Privada](container-registry-private-link.md)

As regras de acesso IP configurantes estão disponíveis no nível de serviço de registo de contentores **Premium.** Para obter informações sobre os níveis e limites do serviço de registo, consulte [os níveis de registo do contentor Azure](container-registry-skus.md).

Cada registo suporta um máximo de 100 regras de acesso à rede.

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="access-from-selected-public-network---cli"></a>Acesso a partir de rede pública selecionada - CLI

### <a name="change-default-network-access-to-registry"></a>Alterar o acesso da rede padrão ao registo

Para limitar o acesso a uma rede pública selecionada, altere primeiro a ação padrão para negar o acesso. Substitua o nome do seu registo no seguinte comando [de atualização az acr:][az-acr-update]

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Adicione a regra da rede ao registo

Utilize o comando de adicionar a [regra de rede az acr][az-acr-network-rule-add] para adicionar uma regra de rede ao seu registo que permite o acesso a partir de um endereço ou alcance IP público. Por exemplo, substitua o nome do registo do contentor e o endereço IP público de um VM numa rede virtual.

```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --ip-address <public-IP-address>
```

> [!NOTE]
> Depois de adicionar uma regra, leva alguns minutos para que a regra faça efeito.

## <a name="access-from-selected-public-network---portal"></a>Acesso a partir de rede pública selecionada - portal

1. No portal, navegue para o registo do seu contentor.
1. Em **Definições**, selecione **Networking**.
1. No separador **de acesso público,** selecione para permitir o acesso público a partir de **redes Selecionadas.**
1. Em **Firewall**, insira um endereço IP público, como o endereço IP público de um VM numa rede virtual. Ou introduza um intervalo de endereços na notação CIDR que contenha o endereço IP do VM.
1. Selecione **Guardar**.

![Regra de firewall de configuração para registo de contentores][acr-access-selected-networks]

> [!NOTE]
> Depois de adicionar uma regra, leva alguns minutos para que a regra faça efeito.

> [!TIP]
> Opcionalmente, permitir o acesso ao registo a partir de um computador cliente local ou alcance de endereço IP. Para permitir este acesso, precisa do endereço IPv4 público do computador. Você pode encontrar este endereço pesquisando "qual é o meu endereço IP" em um navegador de internet. O endereço IPv4 do cliente atual também aparece automaticamente quando configura as definições de firewall na página **de Networking** no portal.

## <a name="disable-public-network-access"></a>Desativar o acesso à rede pública

Opcionalmente, desative o ponto final público do registo. A desativação do ponto final público substitui todas as configurações de firewall. Por exemplo, é melhor desativar o acesso público a um registo protegido numa rede virtual utilizando [o Private Link](container-registry-private-link.md).

> [!NOTE]
> Se o registo for criado numa rede virtual com um ponto final de [serviço,](container-registry-vnet.md)desativar o acesso ao ponto final público do registo também desativa o acesso ao registo dentro da rede virtual.

### <a name="disable-public-access---cli"></a>Desativar o acesso público - CLI

Para desativar o acesso público utilizando o Azure CLI, execute [a atualização az acr][az-acr-update] e desative `--public-network-enabled` para `false` . O `public-network-enabled` argumento requer Azure CLI 2.6.0 ou mais tarde. 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled false
```

### <a name="disable-public-access---portal"></a>Desativar o acesso público - portal

1. No portal, navegue para o registo do seu contentor e selecione **Definições > Networking**.
1. No separador **de acesso público,** em **Permitir o acesso à rede pública,** selecione **Disabled**. Em seguida, selecione **Guardar**.

![Desativar o acesso público][acr-access-disabled]


## <a name="restore-public-network-access"></a>Restaurar o acesso à rede pública

Para voltar a ativar o ponto final público, atualize as definições de rede para permitir o acesso do público. Permitir que o ponto final público substitua todas as configurações de firewall. 

### <a name="restore-public-access---cli"></a>Restaurar o acesso público - CLI

Executar [a atualização az acr][az-acr-update] e definir `--public-network-enabled` para `true` . 

> [!NOTE]
> O `public-network-enabled` argumento requer Azure CLI 2.6.0 ou mais tarde. 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled true
```

### <a name="restore-public-access---portal"></a>Restaurar o acesso público - portal

1. No portal, navegue para o registo do seu contentor e selecione **Definições > Networking**.
1. No separador **de acesso público,** em **Permitir o acesso à rede pública,** selecione **Todas as redes**. Em seguida, selecione **Guardar**.

![Acesso público de todas as redes][acr-access-all-networks]

## <a name="troubleshoot"></a>Resolução de problemas

Se uma regra de rede pública for definida, ou o acesso público ao registo for negado, as tentativas de login no registo a partir de uma rede pública não permitida falharão. O acesso ao cliente por trás de um representante HTTPS também falhará se não for definida uma regra de acesso para o proxy. Verá uma mensagem de erro semelhante a `Error response from daemon: login attempt failed with status: 403 Forbidden` ou `Looks like you don't have access to registry` .

Estes erros também podem ocorrer se utilizar um proxy HTTPS que é permitido por uma regra de acesso à rede, mas o proxy não está devidamente configurado no ambiente do cliente. Verifique se tanto o seu cliente Docker como o daemon do Docker estão configurados para comportamento de procuração. Para mais informações, consulte [http/HTTPS proxy](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) na documentação do Docker.


## <a name="next-steps"></a>Passos seguintes

* Para restringir o acesso a um registo utilizando um ponto final privado numa rede virtual, consulte o [Link Privado Configure Azure para um registo de contentores Azure](container-registry-private-link.md).
* Se precisar de configurar regras de acesso ao registo por detrás de uma firewall do cliente, consulte [as regras de Configuração para aceder a um registo de contentores Azure atrás de uma firewall](container-registry-firewall-access-rules.md).

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
