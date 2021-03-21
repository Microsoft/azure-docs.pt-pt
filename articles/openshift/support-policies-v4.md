---
title: Política de suporte ao cluster Azure Red Hat OpenShift 4
description: Compreenda os requisitos de política de apoio para o Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 30579536b8051e9a045c217751871287636a3976
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102454283"
---
# <a name="azure-red-hat-openshift-support-policy"></a>Política de suporte Azure Red Hat OpenShift

Certas configurações para a azure Red Hat OpenShift 4 clusters podem afetar a capacidade de suporte do seu cluster. O Azure Red Hat OpenShift 4 permite que os administradores de clusters façam alterações nos componentes internos do cluster, mas nem todas as alterações são suportadas. A política de apoio abaixo partilha quais as modificações que violam a política e o apoio nulo da Microsoft e da Red Hat.

> [!NOTE]
> As funcionalidades marcadas de pré-visualização de tecnologia na Plataforma de Contentores OpenShift não são suportadas no Azure Red Hat OpenShift.

## <a name="cluster-configuration-requirements"></a>Requisitos de configuração do cluster

* Todos os operadores do Agrupamento OpenShift devem permanecer em estado de gestão. A lista de operadores de cluster pode ser devolvida em `oc get clusteroperators` funcionamento.
* O cluster deve ter um mínimo de três nós operários e três nós de gerente. Não tenha manchas que impeçam a programação de componentes OpenShift. Não escalone os trabalhadores do agrupamento a zero, nem tente uma paragem graciosa do cluster.
* Não remova nem modifique os serviços prometheus e alertmanager.
* Não remova as regras do Service Alertmanager.
* Não remova nem modifique grupos de segurança de rede.
* Não remova nem modifique o registo do serviço Azure Red Hat OpenShift (cápsulas mdsd).
* Não remova nem modifique o conjunto de "arosvc.azurecr.io" retire o segredo.
* Todas as máquinas virtuais de cluster devem ter acesso direto à Internet, pelo menos ao Gestor de Recursos Azure (ARM) e aos pontos finais de registo de serviços (Genebra).  Não é suportada qualquer forma de procuração HTTPS.
* Não modifique a configuração DNS da rede virtual do cluster. Deve ser utilizado o resolver Azure DNS predefinido.
* Não substitua nenhum dos objetos MachineConfig do cluster (por exemplo, a configuração do kubelet) de qualquer forma.
* Não defina opções não suportadas deConfigOverrides. A definição destas opções impede pequenas atualizações de versão.
* O serviço Azure Red Hat OpenShift acede ao seu cluster através do Private Link Service.  Não remova nem modifique o acesso ao serviço.
* Os nós computacional não-RHCOS não são suportados. Por exemplo, não pode usar um nó de computação RHEL.

## <a name="supported-virtual-machine-sizes"></a>Tamanhos de máquinas virtuais suportados

Azure Red Hat OpenShift 4 suporta instâncias de nó de trabalhadores nos seguintes tamanhos de máquina virtual:

### <a name="general-purpose"></a>Fins gerais

|Série|Tamanho|vCPU|Memória: GiB|
|-|-|-|-|
|Dasv4|Standard_D4as_v4|4|16|
|Dasv4|Standard_D8as_v4|8|32|
|Dasv4|Standard_D16as_v4|16|64|
|Dasv4|Standard_D32as_v4|32|128|
|Dsv3|Standard_D4s_v3|4|16|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|

### <a name="memory-optimized"></a>Otimizada para memória

|Série|Tamanho|vCPU|Memória: GiB|
|-|-|-|-|
|Esv3|Standard_E4s_v3|4|32|
|Esv3|Standard_E8s_v3|8|64|
|Esv3|Standard_E16s_v3|16|128|
|Esv3|Standard_E32s_v3|32|256|

### <a name="compute-optimized"></a>Otimizado para computação

|Série|Tamanho|vCPU|Memória: GiB|
|-|-|-|-|
|Fsv2|Standard_F4s_v2|4|8|
|Fsv2|Standard_F8s_v2|8|16|
|Fsv2|Standard_F16s_v2|16|32|
|Fsv2|Standard_F32s_v2|32|64|

### <a name="master-nodes"></a>Mestres nosdes

|Série|Tamanho|vCPU|Memória: GiB|
|-|-|-|-|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|
