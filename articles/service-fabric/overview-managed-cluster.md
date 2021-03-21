---
title: Clusters geridos por tecido de serviço (pré-visualização)
description: Os clusters geridos pela Service Fabric são uma evolução do modelo de recursos de cluster Azure Service Fabric que dinamiza a implementação e gestão de clusters.
ms.topic: overview
ms.date: 02/15/2021
ms.openlocfilehash: 271852214097ee96ba6b10de7a94904981cd8ef8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041234"
---
# <a name="service-fabric-managed-clusters-preview"></a>Clusters geridos por tecido de serviço (pré-visualização)

Os clusters geridos pela Service Fabric são uma evolução do modelo de recursos de cluster Azure Service Fabric que dinamiza a sua experiência de implementação e gestão de clusters.

O modelo Azure Resource Model (ARM) para os agrupamentos tradicionais de tecido de serviço requer que você defina um recurso de cluster ao lado de uma série de recursos de apoio, todos os quais devem ser "ligados" corretamente (após a implantação e ao longo do ciclo de vida do cluster) para que o cluster e os seus serviços funcionem corretamente. Em contraste, o modelo de encapsulamento para clusters geridos de Tecido de Serviço é composto por um único recurso de cluster gerido pelo *Service Fabric.* Todos os recursos subjacentes para o cluster são resumidos e geridos pelo Azure em seu nome.

**Modelo tradicional de cluster de tecido de** 
 ![ serviço Modelo tradicional de cluster de tecido de serviço][sf-composition]

**Modelo de cluster** 
 ![ gerido de tecido de serviço Modelo de cluster encapsulado de tecido de serviço][sf-encapsulation]

Em termos de tamanho e complexidade, o modelo ARM para um cluster gerido por Tecido de Serviço é de cerca de 100 linhas de JSON, contra cerca de 1000 linhas necessárias para definir um cluster típico de Tecido de Serviço:

| Recursos do Service Fabric | Service Fabric geriu recursos de cluster |
|----------|-----------|
| Cluster do Service Fabric | Cluster gerido de tecido de serviço |
| Conjuntos de escala de máquina virtual | |
| Balanceador de carga | |
| Endereço IP público | |
| Contas de armazenamento | |
| Rede virtual | |

Os clusters geridos pelo Tecido de Serviço proporcionam uma série de vantagens sobre os clusters tradicionais:

**Implantação e gestão de clusters simplificados**
- Implementar e gerir um único recurso Azure
- Gestão e autorotização de certificados
- Operações de escala simplificadas

**Prevenir erros operacionais**
- Prevenir desencontros de configuração com recursos subjacentes
- Bloquear operações inseguras (como eliminar um nó de sementes)

**Boas práticas por defeito**
- Regulações simplificadas de fiabilidade e durabilidade

Não existe um custo adicional para clusters geridos pela Service Fabric para além do custo dos recursos subjacentes necessários para o cluster.

## <a name="service-fabric-managed-cluster-skus"></a>Tecido de Serviço gerido cluster SKUs

Os clusters geridos do Service Fabric estão disponíveis tanto em SKUs Básicos como standard.

| Funcionalidade | Básica | Standard |
| ------- | ----- | -------- |
| Recurso de rede (SKU para [Balanceador de Carga,](../load-balancer/skus.md) [IP Público)](../virtual-network/public-ip-addresses.md) | Básica | Standard |
| Contagem de nó min (exemplo VM) | 3 | 5 |
| Contagem de nó máximo por tipo de nó | 100 | 100 |
| Contagem de tipo de nó máximo | 1 | 20 |
| Adicionar/remover tipos de nó | No | Yes |
| Redundância entre zonas | No | Yes |

## <a name="whats-new-for-service-fabric-managed-clusters"></a>Quais as novidades para clusters geridos de Tecido de Serviço

As funcionalidades mais recentes para a pré-visualização de clusters geridos por Tecido de Serviço incluem suporte para:

* [Implementação de aplicações utilizando modelos ARM](how-to-managed-cluster-app-deployment-template.md)
* [Atualizações automáticas do SO](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades)
* [Encriptação do disco](how-to-enable-managed-cluster-disk-encryption.md)
* [Aplicação das regras NSG](how-to-managed-cluster-networking.md)

As funcionalidades a adicionar nas próximas versões incluem:

* Implementação de aplicações utilizando o Visual Studio
* Suporte de identidades geridas
* Zonas de Disponibilidade
* Procuração inversa
* Dimensionamento automático

## <a name="next-steps"></a>Passos seguintes

Para começar com clusters geridos de Service Fabric, experimente o arranque rápido:

> [!div class="nextstepaction"]
> [Criar um cluster gerido por Tecido de Serviço (pré-visualização)](quickstart-managed-cluster-template.md)


[sf-composition]: ./media/overview-managed-cluster/sfrp-composition-resource.png
[sf-encapsulation]: ./media/overview-managed-cluster/sfrp-encapsulated-resource.png