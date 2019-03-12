---
title: Políticas de ponto final de serviço da Rede Virtual do Azure | Microsoft Docs
description: Saiba como utilizar Políticas de Ponto Final de Serviço para filtrar o tráfego da Rede Virtual para os recursos de serviços do Azure
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sumeet.mittal
ms.openlocfilehash: a5df9215aec0b6c774b37f17b699e91ef813754d
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57771078"
---
# <a name="virtual-network-service-endpoint-policies-preview"></a>Políticas de ponto final de serviço de rede virtual (pré-visualização)

Com as políticas de ponto final de serviço da Rede Virtual (VNet), pode filtrar o tráfego da rede virtual para os serviços do Azure, o que lhe permite especificar apenas os recursos dos serviços do Azure, através de pontos finais de serviço. As políticas de ponto final proporcionam um controlo de acesso granular sobre o tráfego da rede virtual para os serviços do Azure.

Esta funcionalidade está disponível na __pré-visualização__ para os seguintes serviços e regiões do Azure:

__O armazenamento do Azure__: WestCentralUS, WestUS2.

Para obter as notificações mais atualizadas para pré-visualização, veja a página [Atualizações da Rede Virtual do Azure](https://azure.microsoft.com/updates/?product=virtual-network).

> [!NOTE]  
> Durante a pré-visualização, as políticas de ponto final de serviço da rede virtual poderão não ter o mesmo nível de disponibilidade e fiabilidade das funcionalidades que estão na versão de disponibilidade geral. Para obter mais informações, consulte [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="key-benefits"></a>Principais vantagens

As políticas de ponto final de serviço da rede virtual oferecem as seguintes vantagens:

- __Segurança melhorada para o tráfego da Rede Virtual para os Serviços do Azure__

  As [etiquetas de serviço do Azure para grupos de segurança de rede](https://aka.ms/servicetags) permitem-lhe restringir o tráfego de saída da rede virtual para serviços do Azure específicos. Contudo, permite o tráfego para todos os recursos desse serviço. 
  
  Com as políticas de ponto final, agora pode restringir o acesso de saída da rede virtual a apenas recursos específicos do Azure. Isto dá um controlo de segurança muito mais granular para proteger os dados acedidos na rede virtual. 

- __Políticas dimensionáveis e de elevada disponibilidade para filtrar o tráfego dos serviços do Azure__

   As políticas de ponto final proporcionam uma solução dimensionável horizontalmente e de elevada disponibilidade para filtrar o tráfego dos serviços do Azure a partir de redes virtuais através de pontos finais de serviço. Não são necessárias sobrecargas adicionais para manter dispositivos de rede centrais para esse tráfego nas suas redes virtuais.

## <a name="configuration"></a>Configuração

- Pode configurar as políticas de ponto final para restringir o tráfego da rede virtual a recursos de serviços do Azure específicos. Na pré-visualização, suportamos políticas de ponto final para o Armazenamento do Azure. 
- A política de ponto final é configurada numa sub-rede numa rede virtual. Os pontos finais de serviço devem ser ativados na sub-rede para aplicar a política a todos os serviços do Azure listados na mesma.
- A política de ponto final permite-lhe adicionar à lista de permissões recursos de serviços do Azure específicos com o formato resourceID. Pode restringir o acesso a todos os recursos numa subscrição ou num grupo de recursos. Também o pode restringir a recursos específicos. 
- Por predefinição, se não forem anexadas políticas a uma sub-rede com pontos finais, pode aceder a todos os recursos do serviço. Quando for configurada uma política nessa sub-rede, só será possível aceder aos recursos especificados na política a partir das instâncias de computação na sub-rede. O acesso a todos os outros recursos, relativamente ao serviço específico, será recusado. 
- Pode filtrar o tráfego para os recursos de serviços do Azure nas regiões em que o ponto final de serviço está configurado. Por predefinição, o acesso aos recursos de serviços noutras regiões será permitido. 

  > [!NOTE]  
  > Para bloquear completamente o acesso de saída da rede virtual aos recursos do Azure nas regiões do ponto final de serviço, também tem de configurar regras de grupos segurança de rede de modo a permitir o tráfego apenas a essas regiões, mediante a utilização de [etiquetas de serviço](security-overview.md#service-tags).

- Pode aplicar várias políticas a uma sub-rede. Se forem associadas várias políticas a uma sub-rede, relativamente ao mesmo serviço, o tráfego da rede virtual para os recursos especificados em qualquer uma dessas políticas será permitido. O acesso aos outros recursos de serviços não especificados em nenhuma das políticas será recusado. 

  > [!NOTE]  
  > A política só permite o acesso aos recursos de serviços listados a partir de uma rede virtual. Todo o outro tráfego para o serviço é recusado automaticamente se adicionar recursos específicos à mesma. Confirme que é possível identificar e listar na política todas as dependências dos recursos de serviços das suas aplicações.

  > [!WARNING]  
  > Os serviços do Azure implementados na sua rede virtual, como o Azure HDInsight, acedem a outros serviços do Azure, como o Armazenamento do Azure, por motivos de requisitos de infraestrutura. Restringir a política de ponto final a recursos específicos pode interromper o acesso aos recursos de infraestrutura dos serviços implementados na rede virtual. Relativamente a serviços específicos, veja [Limitações](#limitations). Durante a pré-visualização, as políticas de ponto final de serviço não são suportadas em nenhum serviço gerido do Azure que esteja implementado na sua rede virtual.

- Relativamente ao Armazenamento do Azure: 
  -  Pode listar o *resourceId* do Azure Resource Manager da conta de armazenamento para restringir o acesso. Abrange o tráfego para blobs, tabelas, filas, ficheiros e Azure Data Lake Storage Gen2.

     Por exemplo, as contas de armazenamento do Azure podem ser listadas na definição da política de ponto final da seguinte forma:
    
     Para permitir uma conta de armazenamento específica:         
     `subscriptions/subscriptionId/resourceGroups/resourceGroup/providers/Microsoft.Storage/storageAccounts/storageAccountName`
      
     Para permitir todas as contas numa subscrição e num grupo de recursos: `/subscriptions/subscriptionId/resourceGroups/resourceGroup`
     
     Para permitir todas as contas numa subscrição: `/subscriptions/subscriptionId`
    
- Só podem ser especificadas na política de ponto final contas de armazenamento que utilizem o Azure Resource Manager.  

  > [!NOTE]  
  > Com as políticas de ponto final, o acesso às contas de armazenamento clássicas é bloqueado.

- A localização principal da conta listada deve estar nas regiões de par geográfico do ponto final de serviço da sub-rede. 

  > [!NOTE]  
  > As políticas permitem que sejam especificados recursos de serviços de outras regiões. O acesso da rede virtual aos serviços do Azure só é filtrado para as regiões de par geográfico. Se os grupos de segurança de rede não forem restringidos às regiões de par geográfico do Armazenamento do Azure, a rede virtual pode aceder a todas as contas de armazenamento fora daquelas regiões.

- Se a primeira conta for listada, o acesso secundário RA-GRS será permitido automaticamente. 
- As contas de armazenamento podem estar na mesma subscrição ou numa subscrição diferente ou no mesmo inquilino do Azure Active Directory que a rede virtual. 

## <a name="limitations"></a>Limitações

- Só pode implementar políticas de ponto final de serviço em redes virtuais implementadas através do modelo de implementação Azure Resource Manager.
- As redes virtuais devem existir na mesma região que a política de ponto final de serviço.
- Apenas pode aplicar a política de ponto final de serviço a uma sub-rede se os pontos finais de serviço estiverem configurados para os serviços do Azure listados na política.
- Não pode utilizar políticas de ponto final de serviço para tráfego da sua rede no local para os serviços do Azure.
- As políticas de ponto final não devem ser aplicadas a sub-redes com serviços do Azure geridos com dependência de serviços do Azure quanto a requisitos de infraestrutura. 

  > [!WARNING]  
  > Os serviços do Azure implementados na sua rede virtual, como o Azure HDInsight, acedem a outros serviços do Azure, como o Armazenamento do Azure, por motivos de requisitos de infraestrutura. Restringir a política de ponto final a recursos específicos pode interromper o acesso aos recursos de infraestrutura dos serviços do Azure implementados na rede virtual.
  
  - Alguns serviços do Azure podem ser implementados em sub-redes com outras instâncias de computação. Certifique-se de que as políticas de ponto final não são aplicadas à sub-rede, caso os serviços geridos mostrados abaixo estejam implementados na mesma.
   
    - Azure HDInsight
    - Azure Batch (Azure Resource Manager)
    - Azure Active Directory Domain Services (Azure Resource Manager)
    - Gateway de Aplicação do Azure (Azure Resource Manager)
    - Gateway de VPN do Azure (Azure Resource Manager)
    - Azure Firewall

  - Alguns serviços do Azure são implementados em sub-redes dedicadas. Durante a pré-visualização, as políticas de ponto final são bloqueadas em todos os serviços indicados abaixo: 

     - Ambiente de Serviço de Aplicações
     - Azure Rediscache
     - Gestão de API do Azure
     - Instância Gerida do Azure SQL
     - Azure Active Directory Domain Services
     - Gateway de Aplicação do Azure (Clássica)
     - Gateway de VPN do Azure (Clássico)

- Armazenamento do Azure: Contas de armazenamento clássicas não são suportadas nas políticas de ponto final. Por predefinição, as políticas recusarão o acesso a todas estas contas. Se a sua aplicação precisar de acesso a contas de armazenamento Azure Resource Manager e clássicas, não devem ser utilizadas políticas de ponto final para esse tráfego. 

## <a name="nsgs-with-service-endpoint-policies"></a>NSGs com Políticas de Ponto Final de Serviço
- Por predefinição, os NSGs permitem o tráfego de saída da Internet, incluindo o tráfego da rede virtual para os serviços do Azure.
- Se quiser recusar todo o tráfego de saída da Internet e permitir apenas o tráfego para serviços específicos do Azure: 

  Passo 1: Configure os NSGs para permitir o tráfego de saída apenas para serviços do Azure em regiões de ponto final, usando *etiquetas de serviço do Azure*. Para obter mais informações, veja [Etiquetas de serviços para NSGs.](https://aka.ms/servicetags)
      
  Por exemplo, as regras de grupos de segurança de rede que restringem o acesso a apenas regiões do ponto final têm o seguinte aspeto:

  ```
  Allow AzureStorage.WestUS2,
  Allow AzureStorage.WestCentralUS,
  Deny all
  ```

  Passo 2: Aplica a política de ponto final de serviço com acesso a apenas os recursos de serviço do Azure específico.

  > [!WARNING]  
  > Se o grupo de segurança de rede não estiver configurado para limitar o acesso a um serviço do Azure por parte de uma rede virtual, pode aceder aos recursos do serviço noutras regiões, mesmo que a política de ponto final de serviço seja aplicada.

## <a name="scenarios"></a>Cenários

- **Em modo de peering, ligadas ou várias redes virtuais**: Para filtrar o tráfego nas redes virtuais em modo de peering, as políticas de ponto final devem ser aplicadas individualmente a estas redes virtuais.
- **Filtragem de tráfego de Internet com aplicações de rede ou Firewall do Azure**: Filtrar o tráfego de serviço do Azure com as políticas, através de pontos de extremidade e filtrar o restante do tráfego de Internet ou do Azure através de aplicações ou de Firewall do Azure. 
- **Filtragem de tráfego em serviços do Azure implementadas em redes virtuais**: Durante a pré-visualização, as políticas de ponto final de serviço não são suportadas para quaisquer serviços do Azure geridos que são implementados na sua rede virtual. 
 Relativamente a serviços específicos, veja as [limitações.](#Limitations)
- **Filtragem de tráfego para serviços do Azure no local**: Políticas de ponto final de serviço aplicam-se apenas ao tráfego de sub-redes associadas às políticas. Para permitir o acesso a recursos de serviços do Azure no local, o tráfego deve ser filtrado mediante a utilização de dispositivos de rede virtual ou de firewalls.

## <a name="logging-and-troubleshooting"></a>Registo e resolução de problemas
Não estão disponíveis registos centralizados para as políticas de ponto final de serviço. Relativamente a registos de diagnósticos de serviço, veja [Service endpoints logging](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting) (Registo de pontos finais de serviço).

### <a name="troubleshooting-scenarios"></a>Cenários de resolução de problemas
- É permitido o acesso a contas de armazenamento não indicadas nas políticas de ponto final
  - É possível que os grupos de segurança de rede estejam a permitir o acesso à Internet ou a contas de Armazenamento do Azure noutras regiões.
  - Os grupos de segurança de rede devem ser configurados para recusarem todo o tráfego de saída da Internet e permitirem apenas aquele específico das regiões do Armazenamento do Azure. Para obter detalhes, consulte grupos de segurança de rede.
- É recusado o acesso a contas indicadas nas políticas de ponto final
  - A filtragem dos grupos de segurança de rede ou da firewall podem estar a bloquear o acesso
  - Se remover/reaplicar a política resultar em perda de conectividade:
   - Confirme se o serviço do Azure está configurado para permitir o acesso a partir da rede virtual, através de pontos finais, ou se a política predefinida do recurso está definida como *Permitir Tudo*.
      > [!NOTE]      
      > Para terem acesso através de políticas de ponto final, os recursos dos serviços não precisam de ser protegidos em redes virtuais. No entanto, como melhor prática de segurança, recomendamos que sejam protegidos nas suas redes fidedignas, como as redes virtuais do Azure, através de pontos finais de serviço, e no local, através de uma firewall de IP.
  
   - Confirme se os diagnósticos dos serviços mostram o tráfego através dos pontos finais.
    - Verifique se os registos de fluxos do grupo de segurança de rede mostram o acesso e se os registos de armazenamento mostram o acesso, conforme esperado, através dos pontos finais de serviço.
    - Contacte o suporte do Azure.
- É recusado o acesso a contas não indicadas nas políticas de ponto final de serviço
  - A filtragem dos grupos de segurança de rede ou da firewall podem estar a bloquear o acesso. Confirme que a etiqueta de serviço do *Armazenamento do Azure* é permitida nas regiões do ponto final. Relativamente a restrições das políticas, veja as [limitações](#limitations).
  Por exemplo, se for aplicada uma política, o acesso das contas de armazenamento clássicas é recusado.
  - Confirme se o serviço do Azure está configurado para permitir o acesso a partir da rede virtual, através de pontos finais, ou se a política predefinida do recurso está definida como *Permitir Tudo*.

## <a name="provisioning"></a>Aprovisionamento

As políticas de ponto final de serviço podem ser configuradas em sub-redes por um utilizador com acesso de escrita numa rede virtual. Saiba mais sobre [funções incorporadas](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure e a atribuição de permissões específicas a [funções personalizadas](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

As redes virtuais e os recursos de serviços do Azure podem pertencer às mesmas subscrições ou a subscrições diferentes ou a inquilinos do Azure Active Directory. 

## <a name="pricing-and-limits"></a>Preços e limites

Não há encargos adicionais para a utilização de políticas de ponto final de serviço. O modelo de preços atual dos serviços do Azure (como o Armazenamento do Azure) aplica-se tal e qual como atualmente, através dos pontos finais de serviço.

São aplicados os limites seguintes às políticas de ponto final de serviço: 

 |Recurso | Limite predefinido |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpintPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>Próximos Passos

- Saiba [como configurar políticas de ponto final de serviço de rede virtual](virtual-network-service-endpoint-policies-portal.md)
- Saiba mais sobre os [Pontos finais de serviço de rede virtual](virtual-network-service-endpoints-overview.md)

