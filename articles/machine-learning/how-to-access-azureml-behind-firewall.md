---
title: Use uma firewall
titleSuffix: Azure Machine Learning
description: Controle o acesso a espaços de trabalho de aprendizagem automática Azure com firewalls Azure. Saiba mais sobre os anfitriões que deve permitir através da firewall para a Azure Machine Learning funcionar corretamente.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: 23ec12daa2e5c236da482615228b7c44037282fb
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87808121"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>Use espaço de trabalho atrás de uma firewall para aprendizagem de máquinas Azure

Neste artigo, aprenda a configurar a Azure Firewall para uso com um espaço de trabalho de Aprendizagem automática Azure.

> [!IMPORTANT]
> Embora as informações neste documento se baseiem na utilização do Azure Firewall, deverá poder usá-la com outros produtos de firewall. Se tiver dúvidas sobre como permitir a comunicação através da sua firewall, consulte a documentação para a firewall que está a utilizar.

O Azure Firewall pode ser usado para controlar o acesso ao seu espaço de trabalho Azure Machine Learning e à internet pública. Se não estiver configurada corretamente, a firewall pode causar problemas utilizando o seu espaço de trabalho. Há uma variedade de nomes hospedeiros que são usados tanto pelo espaço de trabalho Azure Machine Learning, que são descritos neste artigo.

## <a name="network-rules"></a>Regras de rede

Na sua firewall, crie uma regra de rede que permite o tráfego de e para os endereços deste artigo.

> [!TIP]
> Ao adicionar a regra da rede, desa um __protocolo__ a qualquer e às portas para `*` .
>
> Para obter mais informações sobre a configuração do Azure Firewall, consulte [implementar e configurar a Firewall Azure](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule).

## <a name="microsoft-hosts"></a>Anfitriões da Microsoft

Os anfitriões desta secção são propriedade da Microsoft e fornecem serviços necessários para o bom funcionamento do seu espaço de trabalho.

| **Nome do anfitrião** | **Objetivo** |
| ---- | ---- |
| **\*.batchai.core.windows.net** | Agrupamentos de formação |
| **ml.azure.com** | Azure Machine Learning studio |
| **default.exp-tas.com** | Usado pelo estúdio Azure Machine Learning |
| **\*.azureml.ms** | Usado por Azure Machine Learning APIs |
| **\*.experiments.azureml.net** | Usado por experiências em execução em Azure Machine Learning |
| **\*.modelmanagement.azureml.net** | Usado para registar e implantar modelos|
| **mlworkspace.azure.ai** | Usado pelo portal Azure ao ver um espaço de trabalho |
| **\*.aether.ms** | Usado ao executar gasodutos de aprendizagem de máquinas Azure |
| **\*.instances.azureml.net** | Azure Machine Learning computação de instâncias |
| **\*.instances.azureml.ms** | Azure Machine Learning computa casos em que o espaço de trabalho tem ligação privada ativada |
| **windows.net** | Armazenamento de Blobs do Azure |
| **vault.azure.net** | Azure Key Vault |
| **azurecr.io** | Registo de Contentores do Azure |
| **mcr.microsoft.com** | Registo de contentores da Microsoft para imagens base do estivador |
| **your-acr-server-name.azurecr.io** | Só é necessário se o registo do seu contentor Azure estiver por detrás da rede virtual. Nesta configuração, é criado um link privado do ambiente da Microsoft para a instância ACR na sua subscrição. Utilize o nome do servidor ACR para o seu espaço de trabalho Azure Machine Learning. |
| **\*.notebooks.azure.net** | Necessário pelos cadernos no estúdio Azure Machine Learning. |
## <a name="python-hosts"></a>Anfitriões python

Os anfitriões desta secção são utilizados para instalar pacotes Python. São necessários durante o desenvolvimento, formação e implantação. 

| **Nome do anfitrião** | **Objetivo** |
| ---- | ---- |
| **anaconda.com** | Usado para instalar pacotes predefinidos. |
| **\*.anaconda.org** | Usado para obter dados de repo. |
| **pypi.org** | Usado para listar dependências do índice padrão, se houver, e o índice não é substituído pelas definições do utilizador. Se o índice for substituído, também deve permitir ** \* que .pythonhosted.org**. |

## <a name="r-hosts"></a>Anfitriões R

Os anfitriões desta secção são utilizados para instalar pacotes R. São necessários durante o desenvolvimento, formação e implantação.

> [!IMPORTANT]
> Internamente, o R SDK para Azure Machine Learning utiliza pacotes Python. Por isso, também deve permitir que os anfitriões python passem pela firewall.

| **Nome do anfitrião** | **Objetivo** |
| ---- | ---- |
| **cloud.r-project.org** | Utilizado na instalação de embalagens CRAN. |

Passos seguintes

* [[Implementar e configurar firewall Azure](../firewall/tutorial-firewall-deploy-portal.md)]
* [Secure Azure ML experimentação e inferências de empregos dentro de uma Rede Virtual Azure](how-to-enable-virtual-network.md)
