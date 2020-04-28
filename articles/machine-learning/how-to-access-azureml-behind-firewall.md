---
title: Use a aprendizagem automática azure atrás de uma firewall
titleSuffix: Azure Machine Learning
description: Utilize de forma segura o Azure Machine Learning atrás da Firewall Azure. Saiba mais sobre os anfitriões que deve permitir que através da firewall para o Azure Machine Learning funcione corretamente.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 04/27/2020
ms.openlocfilehash: 2c5fe9d46ef408775cc4b99d4820a652a91683c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196325"
---
# <a name="use-azure-machine-learning-workspace-behind-azure-firewall"></a>Utilize espaço de trabalho azure machine learning atrás da Firewall Azure

Este artigo contém informações sobre a configuração da Firewall Azure para utilização com o Azure Machine Learning.

O Azure Firewall pode ser usado para controlar o acesso ao seu espaço de trabalho Azure Machine Learning e à internet pública. Se não estiver configurada corretamente, a firewall pode causar problemas utilizando o seu espaço de trabalho.

## <a name="network-rules"></a>Regras de rede

Na sua firewall, crie uma regra de rede que permita o tráfego de e para os endereços deste artigo.

> [!TIP]
> Ao adicionar a regra __Protocol__ da rede, dereo `*`o Protocolo a qualquer, e as portas a .
>
> Para obter mais informações sobre a configuração da Firewall Azure, consulte [O Conjunto e configure](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)a Firewall Azure .

## <a name="microsoft-hosts"></a>Anfitriões da Microsoft

Os anfitriões desta secção são propriedade da Microsoft e fornecem serviços necessários para o bom funcionamento do seu espaço de trabalho.

| **Nome do anfitrião** | **Objetivo** |
| ---- | ---- |
| **\*.batchai.core.windows.net** | Agrupamentos de formação |
| **ml.azure.com** | Azure Machine Learning studio |
| **\*.azureml.ms** | Usado por APIs de aprendizagem automática azure |
| **\*.experiments.azureml.net** | Usado por experiências em execução em Azure Machine Learning|
| **\*.modelmanagement.azureml.net** | Usado para registar e implementar modelos|
| **mlworkspace.azure.ai** | Usado pelo portal Azure ao visualizar um espaço de trabalho |
| **\*.aether.ms** | Usado ao executar gasodutos azure machine learning |
| **\*.instances.azureml.net** | Casos de computação de aprendizagem automática azure |
| **windows.net** | Armazenamento de Blobs do Azure |
| **vault.azure.net** | Azure Key Vault |
| **microsoft.com** | Imagens de estivador de base |
| **azurecr.io** | Registo de Contentores do Azure |

## <a name="python-hosts"></a>Anfitriões python

Os anfitriões desta secção são utilizados para instalar pacotes Python. São necessários durante o desenvolvimento, formação e implantação. 

| **Nome do anfitrião** | **Objetivo** |
| ---- | ---- |
| **anaconda.com** | Usado na instalação de pacotes de conda |
| **pypi.org** | Utilizado na instalação de pacotes de pip |

## <a name="r-hosts"></a>Anfitriões R

Os anfitriões desta secção são utilizados para instalar pacotes R. São necessários durante o desenvolvimento, formação e implantação.

> [!IMPORTANT]
> Internamente, o R SDK para Aprendizagem automática Azure utiliza pacotes Python. Por isso, também deve permitir que os anfitriões Python passem pela firewall.

| **Nome do anfitrião** | **Objetivo** |
| ---- | ---- |
| **cloud.r-project.org** | Utilizado na instalação de pacotes CRAN. |

Passos seguintes

* [[Implementar e configurar firewall Azure](../firewall/tutorial-firewall-deploy-portal.md)]
* [Empregos de experimentação e inferência secure Azure ML dentro de uma Rede Virtual Azure](how-to-enable-virtual-network.md)