---
title: Use uma firewall
titleSuffix: Azure Machine Learning
description: Controle o acesso a espaços de trabalho de aprendizagem automática Azure com firewalls Azure. Saiba mais sobre os anfitriões que deve permitir através da firewall para a Azure Machine Learning funcionar corretamente.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 04/27/2020
ms.custom: tracking-python
ms.openlocfilehash: ab2bb3d94a740ca25be15b64895c52d5c038add3
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84552451"
---
# <a name="use-workspace-behind-azure-firewall-for-azure-machine-learning"></a>Use espaço de trabalho atrás do Azure Firewall para a aprendizagem de máquinas Azure

Neste artigo, aprenda a configurar a Azure Firewall para uso com um espaço de trabalho de Aprendizagem automática Azure.

O Azure Firewall pode ser usado para controlar o acesso ao seu espaço de trabalho Azure Machine Learning e à internet pública. Se não estiver configurada corretamente, a firewall pode causar problemas utilizando o seu espaço de trabalho.

## <a name="network-rules"></a>Regras de rede

Na sua firewall, crie uma regra de rede que permite o tráfego de e para os endereços deste artigo.

> [!TIP]
> Ao adicionar a regra da rede, desa um __protocolo__ a qualquer e às portas para `*` .
>
> Para obter mais informações sobre a configuração do Azure Firewall, consulte [implementar e configurar a Firewall Azure](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule).

## <a name="microsoft-hosts"></a>Anfitriões da Microsoft

Os anfitriões desta secção são propriedade da Microsoft e fornecem serviços necessários para o bom funcionamento do seu espaço de trabalho.

| **Nome do anfitrião** | **Objetivo** |
| ---- | ---- |
| **\*.batchai.core.windows.net** | Agrupamentos de formação |
| **ml.azure.com** | Azure Machine Learning studio |
| **\*.azureml.ms** | Usado por Azure Machine Learning APIs |
| **\*.experiments.azureml.net** | Usado por experiências em execução em Azure Machine Learning|
| **\*.modelmanagement.azureml.net** | Usado para registar e implantar modelos|
| **mlworkspace.azure.ai** | Usado pelo portal Azure ao ver um espaço de trabalho |
| **\*.aether.ms** | Usado ao executar gasodutos de aprendizagem de máquinas Azure |
| **\*.instances.azureml.net** | Azure Machine Learning computação de instâncias |
| **windows.net** | Armazenamento de Blobs do Azure |
| **vault.azure.net** | Azure Key Vault |
| **microsoft.com** | Imagens base de estivador |
| **azurecr.io** | Registo de Contentores do Azure |

## <a name="python-hosts"></a>Anfitriões python

Os anfitriões desta secção são utilizados para instalar pacotes Python. São necessários durante o desenvolvimento, formação e implantação. 

| **Nome do anfitrião** | **Objetivo** |
| ---- | ---- |
| **anaconda.com** | Usado na instalação de pacotes conda |
| **pypi.org** | Utilizado na instalação de pacotes de pip |

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
