---
title: Use uma firewall
titleSuffix: Azure Machine Learning
description: Controle o acesso a espaços de trabalho de aprendizagem automática Azure com firewalls Azure. Saiba mais sobre os anfitriões que deve permitir através da firewall.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/18/2020
ms.custom: how-to, devx-track-python
ms.openlocfilehash: cf89532fc41b10d6fbcba57963ebe30a361a2e6f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95012973"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>Use espaço de trabalho atrás de uma firewall para aprendizagem de máquinas Azure

Neste artigo, aprenda a configurar o Azure Firewall para controlar o acesso ao seu espaço de trabalho Azure Machine Learning e à internet pública. Para saber mais sobre a garantia de Azure Machine Learning, consulte [a segurança da Empresa para a Azure Machine Learning](concept-enterprise-security.md)

## <a name="azure-firewall"></a>Azure Firewall

Ao utilizar o Azure Firewall, utilize __a tradução de endereços de rede de destino (DNAT)__ para criar regras NAT para o tráfego de entrada. Para o tráfego de saída, crie regras __de rede__ e/ou __aplicação.__ Estas coleções de regras são descritas mais detalhadamente em [Quais são alguns conceitos de Azure Firewall](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts).

### <a name="inbound-configuration"></a>Configuração de entrada

Se utilizar um conjunto de __cálculos__ ou __de cálculo__ de Azure Machine Learning, adicione uma rota definida pelo utilizador [(UDRs)](../virtual-network/virtual-networks-udr-overview.md) para a sub-rede que contém os recursos de Aprendizagem da Máquina Azure. Esta rota força o tráfego __dos__ endereços IP do `BatchNodeManagement` e recursos para o IP público do seu caso de `AzureMachineLearning` computação e cluster de computação.

Estes UDRs permitem ao serviço Batch comunicar com nós computacional para agendamento de tarefas. Adicione também o endereço IP para o serviço de Aprendizagem automática Azure onde existem os recursos, uma vez que este é necessário para o acesso a Instâncias computacional. Para obter uma lista de endereços IP do serviço Batch e do serviço Azure Machine Learning, utilize um dos seguintes métodos:

* Faça o download das [Gamas IP E Tags de Serviço Azure](https://www.microsoft.com/download/details.aspx?id=56519) e procure no ficheiro `BatchNodeManagement.<region>` `AzureMachineLearning.<region>` e, onde `<region>` fica a sua região Azure.

* Utilize o [CLI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) para descarregar as informações. O exemplo a seguir descarrega as informações do endereço IP e filtra as informações para a região leste dos EUA 2:

    ```azurecli-interactive
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
    ```

    > [!TIP]
    > Se estiver a utilizar as regiões EUA-Virgínia, US-Arizona ou China-Leste-2, estes comandos não devolvem endereços IP. Em vez disso, utilize um dos seguintes links para descarregar uma lista de endereços IP:
    >
    > * [Gamas IP Azure e tags de serviço para o Governo Azure](https://www.microsoft.com/download/details.aspx?id=57063)
    > * [Gamas Azure IP e tags de serviço para Azure China](https://www.microsoft.com//download/details.aspx?id=57062)

Quando adicionar os UDRs, defina a rota para cada prefixo de endereço IP do lote relacionado e defina __o tipo de próximo lúpulo__ para a __Internet__. A imagem a seguir mostra um exemplo desta UDR no portal Azure:

![Exemplo de um UDR para um prefixo de endereço](./media/how-to-enable-virtual-network/user-defined-route.png)

> [!IMPORTANT]
> Os endereços IP podem ser alterados ao longo do tempo.

Para obter mais informações, consulte [criar uma piscina Azure Batch numa rede virtual.](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)

### <a name="outbound-configuration"></a>Configuração de saída

1. Adicionar __regras de rede,__ permitindo o tráfego de e __para__ as seguintes etiquetas __de__ serviço:

    * AzureActiveDirectory
    * AzureMachineLearning
    * AzureResourceManager
    * Armazenamento.região
    * KeyVault.região
    * ContainerRegistry.região

    Se planeia utilizar as imagens padrão do Docker fornecidas pela Microsoft e permitir dependências geridas pelo utilizador, também deve adicionar as seguintes tags de serviço:

    * MicrosoftContainerRegistry.região
    * AzureFrontDoor.FirstParty

    Para as entradas que `region` contenham, substitua-a pela região Azure que está a utilizar. Por exemplo, `keyvault.westus`.

    Para o __protocolo,__ selecione `TCP` . Para as __portas__ de origem e destino, selecione `*` .

1. Adicionar __regras de aplicação__ para os seguintes anfitriões:

    > [!NOTE]
    > Esta não é uma lista completa dos anfitriões necessários para todos os recursos Python na internet, apenas os mais utilizados. Por exemplo, se precisar de acesso a um repositório GitHub ou a outro hospedeiro, deve identificar e adicionar os anfitriões necessários para esse cenário.

    | **Nome do anfitrião** | **Objetivo** |
    | ---- | ---- |
    | **anaconda.com**</br>**\*.anaconda.com** | Usado para instalar pacotes predefinidos. |
    | **\*.anaconda.org** | Usado para obter dados de repo. |
    | **pypi.org** | Usado para listar dependências do índice padrão, se houver, e o índice não é substituído pelas definições do utilizador. Se o índice for substituído, também deve permitir **\* que .pythonhosted.org**. |
    | **cloud.r-project.org** | Utilizado na instalação de pacotes CRAN para desenvolvimento R. |
    | **\*pytorch.org** | Usado por alguns exemplos baseados em PyTorch. |
    | **\*.tensorflow.org** | Usado por alguns exemplos baseados no Tensorflow. |

    Para __protocolo:Porta__, selecione __http, https__.

    Para obter mais informações sobre a configuração das regras de aplicação, consulte [implementar e configurar a Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule).

1. Para restringir o acesso aos modelos implantados no Serviço Azure Kubernetes (AKS), consulte [o tráfego de esrupção no Serviço Azure Kubernetes](../aks/limit-egress-traffic.md).

## <a name="other-firewalls"></a>Outras firewalls

A orientação nesta secção é genérica, uma vez que cada firewall tem a sua própria terminologia e configurações específicas. Se tiver dúvidas sobre como permitir a comunicação através da sua firewall, consulte a documentação para a firewall que está a utilizar.

Se não estiver configurada corretamente, a firewall pode causar problemas utilizando o seu espaço de trabalho. Há uma variedade de nomes hospedeiros que são usados tanto pelo espaço de trabalho Azure Machine Learning. As seguintes secções listam anfitriões que são necessários para a aprendizagem automática Azure.

### <a name="microsoft-hosts"></a>Anfitriões da Microsoft

Os anfitriões desta secção são propriedade da Microsoft e fornecem serviços necessários para o bom funcionamento do seu espaço de trabalho. As tabelas que se seguem listam os nomes dos anfitriões para o público de Azure, governo Azure e regiões Azure China 21Vianet.

**Anfitriões do General Azure**

| **Necessário para** | **Azure público** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Active Directory | login.microsoftonline.com | login.microsoftonline.us | login.chinacloudapi.cn |
| Portal do Azure | management.azure.com | management.azure.us | management.azure.cn |

**Anfitriões de Aprendizagem automática Azure**

| **Necessário para** | **Azure público** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning studio | ml.azure.com | ml.azure.us | studio.ml.azure.cn |
| API |\*.azureml.ms | \*.ml.azure.us | \*.ml.azure.cn |
| Experimentação, História, Hiperdrive, rotulagem | \*.experiments.azureml.net | \*.ml.azure.us | \*.ml.azure.cn |
| Gestão de modelos | \*.modelmanagement.azureml.net | \*.ml.azure.us | \*.ml.azure.cn |
| Pipeline | \*.aether.ms | \*.ml.azure.us | \*.ml.azure.cn |
| Designer (serviço de estúdio) | \*.studioservice.azureml.com | \*.ml.azure.us | \*.ml.azure.cn |
| Caderno integrado | \*.notebooks.azure.net | \*.notebooks.usgovcloudapi.net |\*.notebooks.chinacloudapi.cn |
| Caderno integrado | \*.file.core.windows.net | \*.file.core.usgovcloudapi.net | \*.file.core.chinacloudapi.cn |
| Caderno integrado | \*.dfs.core.windows.net | \*.dfs.core.usgovcloudapi.net | \*.dfs.core.chinacloudapi.cn |
| Caderno integrado | \*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net | \*.blob.core.chinacloudapi.cn |
| Caderno integrado | graph.microsoft.com | graph.microsoft.us | graph.chinacloudapi.cn |
| Caderno integrado | \*.aznbcontent.net |  | |

**Azure Machine Learning computação instância e hospedeiros de cluster compute**

| **Necessário para** | **Azure público** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Cluster computacional/instância | \*.batchai.core.windows.net | \*.batchai.core.usgovcloudapi.net |\*.batchai.ml.azure.cn |
| Instância de computação | \*.instances.azureml.net | \*.instances.azureml.us | \*.instances.azureml.cn |
| Instância de computação | \*.instances.azureml.ms |  |  |

**Recursos associados utilizados pela Azure Machine Learning**

| **Necessário para** | **Azure público** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Conta de Armazenamento do Azure | core.windows.net | core.usgovcloudapi.net | core.chinacloudapi.cn |
| Azure Key Vault | vault.azure.net | vault.usgovcloudapi.net | vault.azure.cn |
| Registo de Contentores do Azure | azurecr.io | azurecr.us | azurecr.cn |
| Registo de contentores da Microsoft | mcr.microsoft.com | mcr.microsoft.com | mcr.microsoft.com |


> [!TIP]
> Se planeia utilizar a identidade federada, siga as Melhores Práticas para garantir o artigo [da Federação de Diretórios Ativos.](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs)

Além disso, utilize as informações em [túneis forçados](how-to-secure-training-vnet.md#forced-tunneling) para adicionar endereços IP para `BatchNodeManagement` e `AzureMachineLearning` .

Para obter informações sobre restringir o acesso a modelos implantados no Serviço Azure Kubernetes (AKS), consulte [o tráfego de saídas de restrição no Serviço Azure Kubernetes](../aks/limit-egress-traffic.md).

### <a name="python-hosts"></a>Anfitriões python

Os anfitriões desta secção são utilizados para instalar pacotes Python. São necessários durante o desenvolvimento, formação e implantação. 

> [!NOTE]
> Esta não é uma lista completa dos anfitriões necessários para todos os recursos Python na internet, apenas os mais utilizados. Por exemplo, se precisar de acesso a um repositório GitHub ou a outro hospedeiro, deve identificar e adicionar os anfitriões necessários para esse cenário.

| **Nome do anfitrião** | **Objetivo** |
| ---- | ---- |
| **anaconda.com**</br>**\*.anaconda.com** | Usado para instalar pacotes predefinidos. |
| **\*.anaconda.org** | Usado para obter dados de repo. |
| **pypi.org** | Usado para listar dependências do índice padrão, se houver, e o índice não é substituído pelas definições do utilizador. Se o índice for substituído, também deve permitir **\* que .pythonhosted.org**. |
| **\*pytorch.org** | Usado por alguns exemplos baseados em PyTorch. |
| **\*.tensorflow.org** | Usado por alguns exemplos baseados no Tensorflow. |

### <a name="r-hosts"></a>Anfitriões R

Os anfitriões desta secção são utilizados para instalar pacotes R. São necessários durante o desenvolvimento, formação e implantação.

> [!NOTE]
> Esta não é uma lista completa dos anfitriões necessários para todos os recursos R na internet, apenas os mais utilizados. Por exemplo, se precisar de acesso a um repositório GitHub ou a outro hospedeiro, deve identificar e adicionar os anfitriões necessários para esse cenário.

| **Nome do anfitrião** | **Objetivo** |
| ---- | ---- |
| **cloud.r-project.org** | Utilizado na instalação de embalagens CRAN. |

> [!IMPORTANT]
> Internamente, o R SDK para Azure Machine Learning utiliza pacotes Python. Por isso, também deve permitir que os anfitriões python passem pela firewall.
## <a name="next-steps"></a>Próximos passos

* [Tutorial: Implementar e configurar o Azure Firewall com o portal do Azure](../firewall/tutorial-firewall-deploy-portal.md)
* [Proteger as tarefas de experimentação e de inferência do Azure ML numa Rede Virtual do Azure](how-to-network-security-overview.md)
