---
title: Políticas de ponto final de serviço de Rede Virtual do Azure | Microsoft Docs
description: Saiba como utilizar Políticas de Ponto Final de Serviço para filtrar o tráfego da Rede Virtual para os recursos de serviços do Azure
services: virtual-network
documentationcenter: na
author: RDhillon
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: 495d0bce905a980f840527f4cc8cd9e2116e3e66
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133756"
---
# <a name="virtual-network-service-endpoint-policies-for-azure-storage"></a>Políticas de endpoint de serviço de rede virtual para armazenamento Azure

As políticas de fim do serviço da Rede Virtual (VNet) permitem filtrar o tráfego de rede virtual para contas de Armazenamento Azure sobre o ponto final do serviço, e permitir a exfiltração de dados apenas contas específicas do Armazenamento Do Azure. As políticas endpoint fornecem o controlo de acesso granular para tráfego de rede virtual para o Armazenamento Azure ao ligar-se sobre o ponto final do serviço.

![Garantir o tráfego de saída da rede virtual para contas de Armazenamento Azure](./media/virtual-network-service-endpoint-policies-overview/vnet-service-endpoint-policies-overview.png)

Esta funcionalidade está geralmente disponível para __o Armazenamento Azure__ em __todas as regiões globais do Azure.__

## <a name="key-benefits"></a>Principais vantagens

As políticas de ponto final de serviço de rede virtual oferecem as seguintes vantagens:

- __Segurança melhorada para o tráfego da rede virtual para o Armazenamento Azure__

  [As etiquetas de serviço Azure para grupos](https://aka.ms/servicetags) de segurança de rede permitem-lhe restringir o tráfego de saída da rede virtual a regiões específicas do Armazenamento Azure. No entanto, isto permite tráfego para qualquer conta dentro da região de Armazenamento Azure selecionada.
  
  As políticas endpoint permitem especificar as contas de Armazenamento Azure que são permitidas de acesso à saída da rede virtual e restringe o acesso a todas as outras contas de armazenamento. Isto dá muito mais controlo de segurança granular para proteger a exfiltração de dados da sua rede virtual.

- __Políticas dimensionáveis e de elevada disponibilidade para filtrar o tráfego dos serviços do Azure__

   As políticas de ponto final proporcionam uma solução dimensionável horizontalmente e de elevada disponibilidade para filtrar o tráfego dos serviços do Azure a partir de redes virtuais através de pontos finais de serviço. Não são necessárias sobrecargas adicionais para manter dispositivos de rede centrais para esse tráfego nas suas redes virtuais.

## <a name="json-object-for-service-endpoint-policies"></a>Objeto JSON para políticas de ponto final de serviço
Vamos ver o objeto da Política de Ponto final de Serviço.

```json
"serviceEndpointPolicyDefinitions": [
    {
            "description": null,
            "name": "MySEP-Definition",
            "resourceGroup": "MySEPDeployment",
            "service": "Microsoft.Storage",
            "serviceResources": [ 
                    "/subscriptions/subscriptionID/resourceGroups/MySEPDeployment/providers/Microsoft.Storage/storageAccounts/mystgacc"
            ],
            "type": "Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions"
    }
]
```

## <a name="configuration"></a>Configuração

-   Pode configurar as políticas de ponto final para restringir o tráfego de rede virtual a contas específicas do Armazenamento Do Azure.
-   A política de ponto final é configurada numa sub-rede numa rede virtual. Os pontos finais de serviço para o Armazenamento Azure devem ser ativados na subnet para aplicar a política.
-   A política endpoint permite-lhe adicionar contas específicas de Armazenamento Azure para permitir a lista, utilizando o formato de ID de recursos. Você pode restringir o acesso a
    - todas as contas de armazenamento em uma subscrição<br>
      `E.g. /subscriptions/subscriptionId`

    - todas as contas de armazenamento em um grupo de recursos<br>
      `E.g. subscriptions/subscriptionId/resourceGroups/resourceGroupName`
     
    - uma conta de armazenamento individual, listando o recurso correspondente do Gestor de Recursos AzureId. Abrange o tráfego para blobs, tabelas, filas, ficheiros e Azure Data Lake Storage Gen2. <br>
    `E.g. /subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/storageAccountName`
-   Por predefinição, se não houver políticas anexas a uma subnet com pontos finais, pode aceder a todas as contas de armazenamento do serviço. Quando for configurada uma política nessa sub-rede, só será possível aceder aos recursos especificados na política a partir das instâncias de computação na sub-rede. O acesso a todas as outras contas de armazenamento será negado.
-   Ao aplicar as políticas de Ponto final de serviço numa subnet, o *âmbito final* do serviço de armazenamento Azure é atualizado de regional para **global**. Isto significa que todo o tráfego para o Armazenamento Azure é assegurado sobre o ponto final do serviço posteriormente. As políticas de ponto final do Serviço também são aplicáveis globalmente, pelo que quaisquer contas de armazenamento, que não são explicitamente permitidas, serão negados acesso. 
-   Pode aplicar várias políticas a uma sub-rede. Quando várias políticas estiverem associadas à subrede, será permitido o tráfego de rede virtual aos recursos especificados em qualquer uma destas políticas. O acesso aos outros recursos de serviços não especificados em nenhuma das políticas será recusado.

    > [!NOTE]  
    > As políticas de ponto final de serviço são **permitidas políticas**, por isso, para além dos recursos especificados, todos os outros recursos são restringidos. Certifique-se de que todas as dependências de recursos de serviço para as suas aplicações são identificadas e listadas na política.

- Só podem ser especificadas na política de ponto final contas de armazenamento que utilizem o Azure Resource Manager. As suas contas clássicas de Armazenamento Azure não suportam as Políticas de Endpoint do Serviço Azure.
- Se a primeira conta for listada, o acesso secundário RA-GRS será permitido automaticamente.
- As contas de armazenamento podem estar na mesma subscrição ou numa subscrição diferente ou no mesmo inquilino do Azure Active Directory que a rede virtual.

## <a name="scenarios"></a>Cenários

- **Redes virtuais em peering, ligadas ou múltiplas redes virtuais**: para filtrar o tráfego em redes virtuais em peering, as políticas de ponto final devem ser aplicadas individualmente às mesmas.
- Filtrar o tráfego de **Internet com eletrodomésticos de rede ou Firewall Azure**: Filtrar o tráfego de serviço sintetizador azure com políticas, sobre pontos finais de serviço e filtrar o resto do tráfego da Internet ou Azure através de eletrodomésticos ou Firewall Azure.
- **Filtrar o tráfego nos serviços Azure implantados em Redes Virtuais**: Neste momento, as Políticas de Endpoint de Serviço Azure não são suportadas para quaisquer serviços Azure geridos que sejam implantados na sua rede virtual. 
- **Filtrar o tráfego para serviços do Azure no local**: as políticas de ponto final de serviço só se aplicam ao tráfego das sub-redes associadas a essas políticas. Para permitir o acesso a recursos de serviços do Azure no local, o tráfego deve ser filtrado mediante a utilização de dispositivos de rede virtual ou de firewalls.

## <a name="logging-and-troubleshooting"></a>Registo e resolução de problemas
Não estão disponíveis registos centralizados para as políticas de ponto final de serviço. Para registos de recursos de serviço, consulte a [exploração de pontos finais do serviço](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting).

### <a name="troubleshooting-scenarios"></a>Cenários de resolução de problemas
- Acesso negado a contas de armazenamento que estavam a funcionar em pré-visualização (não em região geo-emparelhada)
  - Com o upgrade de Armazenamento Azure para usar etiquetas de serviço globais, o âmbito do Service Endpoint e, portanto, as políticas de Endpoint de Serviço são agora globais. Assim, qualquer tráfego para o Armazenamento Azure é encriptado sobre pontos finais de serviço e apenas as contas de Armazenamento que estão explicitamente listadas na política são permitidas de acesso.
  - Autorize explicitamente a lista de todas as contas de Armazenamento necessárias para restaurar o acesso.  
  - Contacte o suporte do Azure.
- É recusado o acesso a contas indicadas nas políticas de ponto final
  - A filtragem dos grupos de segurança de rede ou da firewall podem estar a bloquear o acesso
  - Se remover/reaplicar a política resultar em perda de conectividade:
    - Validar se o serviço Azure está configurado para permitir o acesso da rede virtual sobre pontos finais, ou se a política padrão para o recurso está definida para *permitir tudo*.
    - Confirme se os diagnósticos dos serviços mostram o tráfego através dos pontos finais.
    - Verifique se os registos de fluxos do grupo de segurança de rede mostram o acesso e se os registos de armazenamento mostram o acesso, conforme esperado, através dos pontos finais de serviço.
    - Contacte o suporte do Azure.
- É recusado o acesso a contas não indicadas nas políticas de ponto final de serviço
  - Validar se o Armazenamento Azure está configurado para permitir o acesso da rede virtual sobre pontos finais, ou se a política padrão para o recurso está definida para *permitir tudo*.
  - Certifique-se de que as contas não são **contas de armazenamento clássicas** com políticas de ponto final de serviço na subnet.
- Um Serviço Azure gerido deixou de trabalhar após aplicar uma Política de Ponto final de Serviço sobre a subnet
  - Os serviços geridos não são suportados com políticas de ponto final de serviço neste momento. *Assista a este espaço para obter atualizações.*

## <a name="provisioning"></a>Aprovisionamento

As políticas de ponto final de serviço podem ser configuradas em sub-redes por um utilizador com acesso de escrita numa rede virtual. Saiba mais sobre [funções incorporadas](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure e a atribuição de permissões específicas a [funções personalizadas](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Redes virtuais e contas de Armazenamento Azure podem estar nas mesmas subscrições ou diferentes, ou inquilinos do Azure Ative Directory.

## <a name="limitations"></a>Limitações

- Só pode implementar políticas de ponto final de serviço em redes virtuais implementadas através do modelo de implementação Azure Resource Manager.
- As redes virtuais devem existir na mesma região que a política de ponto final de serviço.
- Apenas pode aplicar a política de ponto final de serviço a uma sub-rede se os pontos finais de serviço estiverem configurados para os serviços do Azure listados na política.
- Não pode utilizar políticas de ponto final de serviço para tráfego da sua rede no local para os serviços do Azure.
- Os serviços geridos pela Azure não apoiam atualmente as políticas de Endpoint. Isto inclui serviços geridos implantados nas subredes partilhadas (por exemplo, *Azure HDInsight, Azure Batch, Azure ADDS, Azure APplication Gateway, Azure VPN gateway, Azure Firewall*) ou nas subredes dedicadas (por *exemplo, Azure App Service Environment, Azure Redis Cache, Azure API Management, Azure SQL MI, serviços geridos clássicos).*

 > [!WARNING]
 > Os serviços do Azure implementados na sua rede virtual, como o Azure HDInsight, acedem a outros serviços do Azure, como o Armazenamento do Azure, por motivos de requisitos de infraestrutura. Restringir a política de ponto final a recursos específicos pode interromper o acesso aos recursos de infraestrutura dos serviços do Azure implementados na rede virtual.

- As contas clássicas de armazenamento não são suportadas em políticas de ponto final. Por predefinição, as políticas recusarão o acesso a todas estas contas. Se a sua aplicação precisar de acesso a contas de armazenamento Azure Resource Manager e clássicas, não devem ser utilizadas políticas de ponto final para esse tráfego.

## <a name="pricing-and-limits"></a>Preços e limites

Não há encargos adicionais para a utilização de políticas de ponto final de serviço. O modelo de preços atual dos serviços do Azure (como o Armazenamento do Azure) aplica-se tal e qual como atualmente, através dos pontos finais de serviço.

São aplicados os limites seguintes às políticas de ponto final de serviço: 

 |Recurso | Limite predefinido |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpintPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>Passos Seguintes

- Saiba [como configurar políticas de ponto final de serviço de rede virtual](virtual-network-service-endpoint-policies-portal.md)
- Saiba mais sobre os [Pontos finais de serviço de rede virtual](virtual-network-service-endpoints-overview.md)
