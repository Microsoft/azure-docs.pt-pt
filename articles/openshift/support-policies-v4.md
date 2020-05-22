---
title: Política de apoio ao cluster Azure Red Hat OpenShift 4
description: Compreenda os requisitos da política de apoio para o Red Hat OpenShift 4.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: ec27d054055866c72148ad6eb024d4324f063ce8
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774397"
---
# <a name="azure-red-hat-openshift-support-policy"></a>Política de apoio openshift do chapéu vermelho azure

Certas configurações para os clusters OpenShift 4 do Chapéu Vermelho Azure podem afetar a capacidade de suporte do seu cluster. O Azure Red Hat OpenShift 4 permite que os administradores do cluster façam alterações nos componentes internos do cluster, mas nem todas as alterações são suportadas. A política de apoio abaixo partilha quais as modificações que violam a política e o apoio nulo da Microsoft e da Red Hat.

> [!NOTE]
> As funcionalidades marcadas para a pré-visualização da tecnologia na Plataforma de Contentores OpenShift não são suportadas no OpenShift do Chapéu Vermelho Azure.

## <a name="cluster-configuration-requirements"></a>Requisitos de configuração do cluster

* Todos os operadores do Cluster OpenShift devem permanecer num estado gerido. A lista de operadores de cluster pode ser devolvida em `oc get clusteroperators` execução.
* Não remova ou modifique os serviços de cluster Prometheus e Alertmanager.
* Não remova as regras do Service Alertmanager.
* Não modifique a versão do cluster OpenShift.
* Não remova ou modifique o serviço de registo de serviço Saque de Serviço Azure Red Hat OpenShift (mdsd pods).
* Não remova ou modifique o cluster 'arosvc.azurecr.io' retire segredo.
* Todas as máquinas virtuais do cluster devem ter acesso direto à Internet, pelo menos para o Gestor de Recursos Azure (ARM) e para os pontos finais de registo de serviços (Genebra).  Não é suportada nenhuma forma de procuração HTTPS.
* Não modifique a configuração DNS da rede virtual do cluster. O resolver DeS DNS de Azure por defeito deve ser utilizado.
* Não sobreponha nenhum dos objetos MachineConfig do cluster (por exemplo, a configuração kubelet) de qualquer forma.
* O serviço Azure Red Hat OpenShift acede ao seu cluster através do Private Link Service.  Não remova ou modifique o acesso ao serviço.
* Os nós de computação não RHCOS não são suportados. Por exemplo, não se pode usar um nó de cálculo RHEL.

## <a name="supported-virtual-machine-sizes"></a>Tamanhos de máquina virtual suportado

O Azure Red Hat OpenShift 4 suporta as instâncias do nó dos trabalhadores nos seguintes tamanhos de máquinavirtual:

### <a name="general-purpose"></a>Fins gerais

|Série|Tamanho|vCPU|Memória: GiB|
|-|-|-|-|
|Rio Dasv4|Standard_D4as_v4|4|16|
|Rio Dasv4|Standard_D8as_v4|8|32|
|Rio Dasv4|Standard_D16as_v4|16|64|
|Rio Dasv4|Standard_D32as_v4|32|128|
|Dsv3|Standard_D4s_v3|4|16|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|

### <a name="memory-optimized"></a>Com otimização de memória

|Série|Tamanho|vCPU|Memória: GiB|
|-|-|-|-|
|Esv3|Standard_E4s_v3|4|32|
|Esv3|Standard_E8s_v3|8|64|
|Esv3|Standard_E16s_v3|16|128|
|Esv3|Standard_E32s_v3|32|256|

### <a name="compute-optimized"></a>Com otimização de computação

|Série|Tamanho|vCPU|Memória: GiB|
|-|-|-|-|
|Fsv2|Standard_F4s_v2|4|8|
|Fsv2|Standard_F8s_v2|8|16|
|Fsv2|Standard_F16s_v2|16|32|
|Fsv2|Standard_F32s_v2|32|64|

### <a name="master-nodes"></a>Mestres de narizes

|Série|Tamanho|vCPU|Memória: GiB|
|-|-|-|-|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|
