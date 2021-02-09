---
title: Visão geral do isolamento da rede virtual e da segurança
titleSuffix: Azure Machine Learning
description: Utilize uma rede virtual Azure isolada com Azure Machine Learning para garantir recursos de espaço de trabalho e ambientes de computação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 10/06/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, references_regions, contperf-fy21q1
ms.openlocfilehash: 857fba6dfa6191163c06c423cefb42d57f25dc1d
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980580"
---
# <a name="virtual-network-isolation-and-privacy-overview"></a>Isolamento de rede virtual e visão geral da privacidade

Neste artigo, aprende-se a usar redes virtuais (VNets) para garantir a comunicação de rede no Azure Machine Learning. Este artigo usa um cenário de exemplo para mostrar como configurar uma rede virtual completa.

Este artigo é parte uma de uma série de cinco partes que o acompanha através da garantia de um fluxo de trabalho de Aprendizagem automática Azure. Recomendamos vivamente que leia este artigo de visão geral para entender primeiro os conceitos. 

Aqui estão os outros artigos desta série:

**1. Visão geral da VNet**  >  [2. Proteja o espaço de trabalho](how-to-secure-workspace-vnet.md)  >  [3. Proteja o ambiente de treino](how-to-secure-training-vnet.md)  >  [4. Fixe o ambiente de inferencção](how-to-secure-inferencing-vnet.md)  >  [5. Ativar a funcionalidade do estúdio](how-to-enable-studio-virtual-network.md)

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que tem familiaridade com os seguintes tópicos:
+ [Redes Virtuais Azure](../virtual-network/virtual-networks-overview.md)
+ [Rede IP](../virtual-network/public-ip-addresses.md)
+ [Azure Private Link](how-to-configure-private-link.md)
+ [Grupos de segurança de rede (NSG)](../virtual-network/network-security-groups-overview.md)
+ [Firewalls de rede](../firewall/overview.md)

## <a name="example-scenario"></a>Cenário de exemplo

Nesta secção, você aprende como um cenário de rede comum é configurado para garantir a comunicação Azure Machine Learning com endereços IP privados.

A tabela abaixo compara como os serviços acedem a diferentes partes de uma rede de Machine Learning Azure, tanto com um VNet como sem um VNet.

| Scenario | Área de trabalho | Recursos associados | Ambiente computacional de formação | Ambiente computacional inferencing |
|-|-|-|-|-|-|
|**Sem rede virtual**| IP público | IP público | IP público | IP público |
|**Recursos seguros numa rede virtual**| IP privado (ponto final privado) | IP público (ponto final de serviço) <br> **- ou -** <br> IP privado (ponto final privado) | IP privado | IP privado  | 

* **Workspace** - Crie um ponto final privado a partir do seu VNet para ligar ao Link Privado no espaço de trabalho. O ponto final privado liga o espaço de trabalho à vnet através de vários endereços IP privados.
* **Recurso associado** - Utilize pontos finais de serviço ou pontos finais privados para ligar a recursos do espaço de trabalho como o armazenamento Azure, Cofre da Chave Azure e Serviços de Contentores Azure.
    * **Os pontos finais** de serviço fornecem a identidade da sua rede virtual ao serviço Azure. Uma vez que ative os pontos finais do serviço na sua rede virtual, pode adicionar uma regra de rede virtual para proteger os recursos de serviço Azure à sua rede virtual. Os pontos finais de serviço utilizam endereços IP públicos.
    * **Os pontos finais privados** são interfaces de rede que o ligam de forma segura a um serviço alimentado pela Azure Private Link. O ponto final privado utiliza um endereço IP privado a partir do seu VNet, efetivamente trazendo o serviço para o seu VNet.
* **Acesso ao computação de formação** - Aceda a metas de computação de treinamento como Azure Machine Learning Compute Instance e Azure Machine Learning Compute Clusters de forma segura com endereços IP privados. 
* **Inferencing computação acesso** - Access Azure Kubernetes Services (AKS) computa clusters com endereços IP privados.


As próximas cinco secções mostram-lhe como proteger o cenário de rede acima descrito. Para proteger a sua rede, tem de:

1. Garantir o [**espaço de trabalho e os recursos associados.**](#secure-the-workspace-and-associated-resources)
1. Proteja o [**ambiente de treino.**](#secure-the-training-environment)
1. Proteja o [**ambiente de inferenculação**](#secure-the-inferencing-environment).
1. Opcionalmente: [**ativar a funcionalidade do estúdio**](#optional-enable-studio-functionality).
1. Configurar [ **definições** de firewall](#configure-firewall-settings)

## <a name="secure-the-workspace-and-associated-resources"></a>Garantir o espaço de trabalho e os recursos associados

Use os seguintes passos para garantir o seu espaço de trabalho e recursos associados. Estes passos permitem que os seus serviços se comuniquem na rede virtual.

1. Crie um [espaço de trabalho com ligações privadas](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint) para permitir a comunicação entre o seu VNet e o espaço de trabalho.
1. Adicione o Cofre da Chave Azure à rede virtual com um [ponto final](../key-vault/general/overview-vnet-service-endpoints.md) de serviço ou um ponto [final privado](../key-vault/general/private-link-service.md). Definir o Cofre da Chave para ["Permitir que serviços confiáveis da Microsoft contornem esta firewall".](how-to-secure-workspace-vnet.md#secure-azure-key-vault)
1. Adicione-lhe conta de armazenamento Azure à rede virtual com um [ponto final](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints) de serviço ou um ponto [final privado.](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints)
1. [Configure o registo do contentor Azure para utilizar um ponto final privado](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr) e permitir a [delegação de sub-redes em instâncias de contentores de Azure](how-to-secure-inferencing-vnet.md#enable-azure-container-instances-aci).

![Diagrama de arquitetura mostrando como o espaço de trabalho e os recursos associados comunicam uns aos outros sobre pontos finais de serviço ou pontos finais privados dentro de um VNet](./media/how-to-network-security-overview/secure-workspace-resources.png)

Para obter instruções detalhadas sobre como completar estes passos, consulte um espaço de trabalho de [aprendizagem de máquinas Azure](how-to-secure-workspace-vnet.md). 

### <a name="limitations"></a>Limitações

Garantir o seu espaço de trabalho e os recursos associados dentro de uma rede virtual têm as seguintes limitações:
- A utilização de um espaço de trabalho de aprendizagem automática Azure com ligação privada não está disponível nas regiões do Governo Azure ou Azure China 21Vianet.
- Todos os recursos devem estar por trás do mesmo VNet. No entanto, são permitidas sub-redes dentro do mesmo VNet.

## <a name="secure-the-training-environment"></a>Garantir o ambiente de treino

Nesta secção, você aprende a garantir o ambiente de treino em Azure Machine Learning. Você também aprende como Azure Machine Learning completa um trabalho de formação para entender como as configurações de rede funcionam em conjunto.

Para garantir o ambiente de treino, utilize os seguintes passos:

1. Crie um azure machine [learning computação e cluster de computador na rede virtual](how-to-secure-training-vnet.md#compute-instance) para executar o trabalho de formação.
1. [Permitir a comunicação de entrada do Serviço de Lote Azure](how-to-secure-training-vnet.md#mlcports) para que o Serviço de Lote possa submeter empregos aos seus recursos de computação. 

![Diagrama de arquitetura mostrando como garantir clusters e instâncias de computação gerida](./media/how-to-network-security-overview/secure-training-environment.png)

Para obter instruções detalhadas sobre como completar estes passos, consulte [Secure a training environment](how-to-secure-training-vnet.md). 

### <a name="example-training-job-submission"></a>Exemplo de submissão de emprego de formação 

Nesta secção, você aprende como a Azure Machine Learning comunica de forma segura entre serviços para submeter um trabalho de formação. Isto mostra-lhe como todas as suas configurações funcionam em conjunto para garantir a comunicação.

1. O cliente envia scripts de formação e dados de formação para contas de armazenamento que são protegidas com um serviço ou ponto final privado.

1. O cliente submete um trabalho de formação ao espaço de trabalho Azure Machine Learning através do ponto final privado.

1. Os serviços Azure Batch recebem o trabalho do espaço de trabalho e submetem o trabalho de formação ao ambiente de computação através do equilibrador de carga pública que é aprovisionado com o recurso compute. 

1. O recurso computacional recebe o trabalho e começa a formação. Os recursos compute acedem a contas de armazenamento seguras para descarregar ficheiros de treino e carregar a saída. 

![Diagrama de arquitetura mostrando como um trabalho de formação de Azure Machine Learning é submetido ao usar um VNet](./media/how-to-network-security-overview/secure-training-job-submission.png)


### <a name="limitations"></a>Limitações

- A azure Compute Instance e a Azure Compute Clusters devem estar no mesmo VNet, região e subscrição que o espaço de trabalho e os seus recursos associados. 

## <a name="secure-the-inferencing-environment"></a>Proteja o ambiente de inferenculação

Nesta secção, você aprende as opções disponíveis para garantir um ambiente inferencionado. Recomendamos que utilize clusters Azure Kubernetes Services (AKS) para implantações de produção de alta escala.

Tem duas opções para clusters AKS numa rede virtual:

- Implemente ou anexe um cluster AKS predefinido ao seu VNet.
- Anexe um cluster AKS privado ao seu VNet.

**Os clusters AKS predefinidos** têm um plano de controlo com endereços IP públicos. Pode adicionar um cluster AKS predefinido ao seu VNet durante a implementação ou anexar um cluster após a sua criação.

**Os clusters privados AKS** têm um plano de controlo, que só pode ser acedido através de IPs privados. Os agrupamentos privados AKS devem ser ligados após a criação do cluster.

Para obter instruções detalhadas sobre como adicionar agrupamentos predefinidos e privados, consulte [Um ambiente de inferencção](how-to-secure-inferencing-vnet.md). 

O diagrama de rede que se segue mostra um espaço de trabalho seguro de Aprendizagem de Máquinas Azure com um cluster AKS privado ligado à rede virtual.

![Diagrama de arquitetura mostrando como anexar um cluster AKS privado à rede virtual. O avião de controlo AKS é colocado fora do cliente VNet](./media/how-to-network-security-overview/secure-inferencing-environment.png)

### <a name="limitations"></a>Limitações
- Os clusters AKS devem pertencer ao mesmo VNet que o espaço de trabalho e os seus recursos associados. 

## <a name="optional-enable-public-access"></a>Opcional: Permitir o acesso do público

Pode proteger o espaço de trabalho por trás de um VNet usando um ponto final privado e ainda permitir o acesso através da internet pública. A configuração inicial é a mesma que [assegurar o espaço de trabalho e os recursos associados.](#secure-the-workspace-and-associated-resources) 

Depois de garantir o espaço de trabalho com uma ligação privada, então [ative o acesso público](how-to-configure-private-link.md#enable-public-access). Depois disso, pode aceder ao espaço de trabalho tanto a partir da internet pública como do VNet.

### <a name="limitations"></a>Limitações

- Se utilizar o estúdio Azure Machine Learning através da internet pública, algumas funcionalidades, como o designer, podem não conseguir aceder aos seus dados. Este problema acontece quando os dados são armazenados num serviço que é protegido por trás do VNet. Por exemplo, uma Conta de Armazenamento Azure.
## <a name="optional-enable-studio-functionality"></a>Opcional: ativar a funcionalidade do estúdio

[Proteger o espaço de](#secure-the-workspace-and-associated-resources)  >  trabalho [Garantir o ambiente](#secure-the-training-environment)  >  de treino [Proteja o ambiente](#secure-the-inferencing-environment)  >  de inferenculação **Ativar a funcionalidade do**  >  estúdio [Configurar definições de firewall](#configure-firewall-settings)

Se o seu armazenamento estiver num VNet, primeiro deve executar etapas de configuração adicionais para permitir a plena funcionalidade [no estúdio.](overview-what-is-machine-learning-studio.md) Por predefinição, a seguinte função é desativada:

* Pré-visualizar dados no estúdio.
* Visualizar dados no designer.
* Implementar um modelo no designer.
* Submeta uma experiência AutoML.
* Inicie um projeto de rotulagem.

Para ativar a funcionalidade completa do estúdio dentro de um VNet, consulte [o estúdio Use Azure Machine Learning numa rede virtual.](how-to-enable-studio-virtual-network.md#configure-data-access-in-the-studio) O estúdio suporta contas de armazenamento usando pontos finais de serviço ou pontos finais privados.

### <a name="limitations"></a>Limitações

[A rotulagem de dados assistidas pelo ML](how-to-create-labeling-projects.md#use-ml-assisted-data-labeling) não suporta contas de armazenamento predefinidas protegidas por trás de uma rede virtual. Deve utilizar uma conta de armazenamento não padrão para a rotulagem de dados assistidos ML. Note que a conta de armazenamento não padrão pode ser protegida por trás da rede virtual. 

## <a name="configure-firewall-settings"></a>Configurar as definições da firewall

Configure a sua firewall para controlar o acesso aos recursos do seu espaço de trabalho Azure Machine Learning e à internet pública. Embora recomendemos o Azure Firewall, deverá ser capaz de utilizar outros produtos de firewall para proteger a sua rede. Se tiver dúvidas sobre como permitir a comunicação através da sua firewall, consulte a documentação para a firewall que está a utilizar.

Para obter mais informações sobre as definições de firewall, consulte [use espaço de trabalho por trás de uma firewall](how-to-access-azureml-behind-firewall.md).

## <a name="custom-dns"></a>DNS Personalizado

Se precisar de utilizar uma solução DE DNS personalizada para a sua rede virtual, tem de adicionar registos de anfitriões para o seu espaço de trabalho.

Para obter mais informações sobre os nomes de domínio e endereços IP necessários, consulte [como utilizar um espaço de trabalho com um servidor DNS personalizado](how-to-custom-dns.md).

## <a name="next-steps"></a>Passos seguintes

Este artigo é parte de uma série de rede virtual de quatro partes. Veja o resto dos artigos para aprender a proteger uma rede virtual:

* [Parte 2: Visão geral da rede virtual](how-to-secure-workspace-vnet.md)
* [Parte 3: Assegurar o ambiente de treino](how-to-secure-training-vnet.md)
* [Parte 4: Assegurar o ambiente de inferencção](how-to-secure-inferencing-vnet.md)
* [Parte 5:Ativar a funcionalidade do estúdio](how-to-enable-studio-virtual-network.md)