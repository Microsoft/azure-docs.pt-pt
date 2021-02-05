---
title: App de acesso Azure Spring Cloud em rede virtual
description: Aceder a aplicativo numa Nuvem de primavera Azure em rede virtual.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/11/2020
ms.custom: devx-track-java
ms.openlocfilehash: 37c8b4bc186c217ecb27638f5f50297102345de7
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576536"
---
# <a name="access-your-application-in-a-private-network"></a>Aceda à sua aplicação numa rede privada

Este documento explica como aceder a um ponto final para a sua aplicação numa rede privada.  Para ter acesso, precisa de criar uma **Zona DE DNS Privada Azure** na sua subscrição para traduzir/resolver o nome de domínio privado totalmente qualificado (FQDN) para o seu endereço IP.

Quando **atribuir o Ponto Final** para aplicações numa instância de serviço Azure Spring Cloud é implantado na sua rede virtual, o ponto final é um FQDN privado. O domínio só é acessível na rede privada. As aplicações e serviços utilizam o ponto final da aplicação. Incluem o **Ponto final de teste** descrito em [aplicações e implementações ver.](spring-cloud-howto-staging-environment.md#view-apps-and-deployments) **O streaming de registos**, descrito nos [registos de aplicações stream Azure Spring Cloud em tempo real,](spring-cloud-howto-log-streaming.md)também funciona apenas dentro da rede privada.

## <a name="create-a-private-dns-zone"></a>Criar uma zona privada de DNS

O procedimento a seguir cria uma zona privada de DNS para uma aplicação na rede privada.

1. Abra o portal do Azure. A partir da caixa de pesquisa superior, procure **por zonas privadas de DNS** e selecione **as zonas DNS privadas** a partir do resultado.

2. Na página **de zonas privadas de DNS,** selecione **+ Adicionar**.

3. Preencha o formulário na página da **zona do DNS Privado.** Introduza **<span>private.azuremicroservices.io</span>** como **o Nome** da zona.

4. Selecione **Rever + Criar**.

5. Selecione **Criar**.

A criação da zona pode demorar alguns minutos.

## <a name="link-the-virtual-network"></a>Ligue a rede virtual

Para ligar a zona privada de DNS à rede virtual, é necessário criar uma ligação de rede virtual.

1. Selecione o recurso de zona dNS privado criado acima: **<span>private.azuremicroservices.io</span>** 

2. No painel esquerdo, selecione **links de rede virtual.**

3. Selecione **Adicionar**.

4. Introduza **a ligação azure-spring-cloud-dns-link** para o **nome Link**.

5. Para **a rede Virtual**, selecione a rede virtual que criou como explicado na Deploy [Azure Spring Cloud na sua rede virtual Azure (injeção VNet)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md).

    ![Adicionar link de rede virtual](media/spring-cloud-access-app-vnet/add-virtual-network-link.png)

6. Clique em **OK**.

## <a name="create-dns-record"></a>Criar registo DNS

Para utilizar a zona privada de DNS para traduzir/resolver DNS, tem de criar um registo do tipo "A" na zona.

1. Selecione o recurso de rede virtual que criou como explicado na [Deploy Azure Spring Cloud na sua rede virtual Azure (injeção VNet)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md).

2. Na caixa de pesquisa de **dispositivos ligados,** introduza *kubernetes-internos*.

3. No resultado filtrado, encontre o **Dispositivo** ligado à **sub-rede** de tempo de funcionaamento do serviço e copie o seu **Endereço IP**. Nesta amostra, o Endereço IP é *10.1.0.7*.

    [![Criar registo ](media/spring-cloud-access-app-vnet/create-dns-record.png) DNS](media/spring-cloud-access-app-vnet/create-dns-record.png)

Ou, você pode obter o IP usando o seguinte comando AZ CLI:

```azurecli
SPRING_CLOUD_RG= # Resource group name of your Azure Spring Cloud service instance
SPRING_CLOUD= # Name of your Azure Spring Cloud service instance

SERVICE_RUNTIME_RG=`az spring-cloud show -g $SPRING_CLOUD_RG -n $SPRING_CLOUD --query \
"properties.networkProfile.serviceRuntimeNetworkResourceGroup" -o tsv`

IP_ADDRESS=`az network lb frontend-ip list --lb-name kubernetes-internal -g \
$SERVICE_RUNTIME_RG --query "[0].privateIpAddress" -o tsv`
```

4. Selecione o recurso de zona dNS privado criado acima: **<span>private.azuremicroservices.io</span>**.

5. Selecione **+ Conjunto de registos**.

6. In **Adicionar recorde,** insira ou selecione estas informações:

    |Definição     |Valor                                                                      |
    |------------|---------------------------------------------------------------------------|
    |Nome        |Inserir *\**                                                                 |
    |Tipo        |Selecione **A**                                                               |
    |TTL         |Insira *1*                                                                  |
    |Unidade de TTL    |**Horas selecionadas**                                                           |
    |Endereço IP  |Introduza o endereço IP copiado no passo 3. Na amostra, insira *10.1.0.7*.    |

    Em seguida, selecione **OK**.

    ![Adicionar registo privado de zona de DNS](media/spring-cloud-access-app-vnet/private-dns-zone-add-record.png)

## <a name="assign-private-fqdn-for-your-application"></a>Atribua FQDN privado para a sua aplicação

Depois de seguir o procedimento em [Build e implementar aplicações de microserviços,](spring-cloud-tutorial-deploy-in-azure-virtual-network.md)pode atribuir FQDN privado para a sua aplicação.

1. Selecione a instância de serviço Azure Spring Cloud implantada na sua rede virtual e abra o **separador Apps** no menu à esquerda.

2. Selecione a aplicação para mostrar a página **'Vista Geral'.**

3. Selecione **Atribuir Ponto final** para atribuir um FQDN privado à sua aplicação. A implementação pode demorar alguns minutos.

    ![Atribuir ponto final privado](media/spring-cloud-access-app-vnet/assign-private-endpoint.png)

4. O FQDN privado designado **(URL** rotulado) está agora disponível. Só pode ser acedido dentro da rede privada, mas não na Internet.

## <a name="access-application-private-fqdn"></a>Aplicação de acesso privada FQDN

Após a atribuição, pode aceder ao FQDN privado da sua aplicação em rede privada. Por exemplo, pode criar uma máquina de salto na mesma rede virtual, ou numa rede virtual espreitada, e nessa máquina de salto o FQDN privado está acessível.

![Acesso ao ponto final privado em vnet](media/spring-cloud-access-app-vnet/access-private-endpoint.png)

## <a name="next-steps"></a>Passos seguintes

- [Expor aplicações à Internet - usando o Gateway de Aplicações e o Azure Firewall](spring-cloud-expose-apps-gateway-azure-firewall.md)

## <a name="see-also"></a>Ver também

- [Resolução de problemas Azure Spring Cloud em VNET](spring-cloud-troubleshooting-vnet.md)
- [Responsabilidades do cliente para correr Azure Spring Cloud em VNET](spring-cloud-vnet-customer-responsibilities.md)