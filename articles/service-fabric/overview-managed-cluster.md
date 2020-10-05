---
title: Clusters geridos por tecido de serviço (pré-visualização)
description: Os clusters geridos pela Service Fabric são uma evolução do modelo de recursos de cluster Azure Service Fabric que dinamiza a implementação e gestão de clusters.
ms.topic: overview
ms.date: 09/28/2020
ms.openlocfilehash: 3d26a92126491662d5c51b3c4e8900ffa547f830
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91410496"
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
| Adicionar/remover tipos de nó | Não | Sim |
| Redundância entre zonas | Não | Sim |

## <a name="service-fabric-managed-cluster-feature-roadmap"></a>Roteiro de funcionalidade de cluster gerido de tecido de serviço
Como esta é uma versão de pré-visualização antecipada dos clusters geridos por Service Fabric existem algumas lacunas a ter em conta. Estas funcionalidades ficarão disponíveis em futuras versões. 

* Publicar aplicações para clusters diretamente do Visual Studio
* Identidades geridas 
* Implementações de aplicações ARM 
* Zonas de Disponibilidade 
* Procuração inversa 
* Auto escalonamento 
* Atualizar as regras do NSG 
* Atualizações auto-OS

## <a name="next-steps"></a>Passos seguintes

Para começar com clusters geridos pela Service Fabric, experimente o arranque rápido:

> [!div class="nextstepaction"]
> [Criar um cluster gerido por Tecido de Serviço (pré-visualização)](quickstart-managed-cluster-template.md)


[sf-composition]: ./media/overview-managed-cluster/sfrp-composition-resource.png
[sf-encapsulation]: ./media/overview-managed-cluster/sfrp-encapsulated-resource.png