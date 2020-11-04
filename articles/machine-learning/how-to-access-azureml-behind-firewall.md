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
ms.date: 07/17/2020
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 5ba1804630649044d29dd2919ef8375265a69a08
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93320882"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>Use espaço de trabalho atrás de uma firewall para aprendizagem de máquinas Azure

Neste artigo, aprenda a configurar o Azure Firewall para controlar o acesso ao seu espaço de trabalho Azure Machine Learning e à internet pública.   Para saber mais sobre a garantia de Azure Machine Learning, consulte [a segurança da Empresa para a Azure Machine Learning](concept-enterprise-security.md)

Embora as informações neste documento se baseiem na utilização [do Azure Firewall,](../firewall/tutorial-firewall-deploy-portal.md)deverá poder usá-la com outros produtos de firewall. Se tiver dúvidas sobre como permitir a comunicação através da sua firewall, consulte a documentação para a firewall que está a utilizar.

## <a name="application-rules"></a>Regras de aplicação

Na sua firewall, crie uma _regra de aplicação_ que permite o tráfego de e para os endereços deste artigo.

> [!TIP]
> Ao adicionar a regra da rede, desa um __protocolo__ a qualquer e às portas para `*` .
>
> Para obter mais informações sobre a configuração do Azure Firewall, consulte [implementar e configurar a Firewall Azure](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule).

## <a name="routes"></a>Rotas

Ao configurar a rota de saída para a sub-rede que contém recursos de Aprendizagem de Máquinas Azure, utilize a orientação na secção [de túneis forçados](how-to-secure-training-vnet.md#forced-tunneling) para assegurar o ambiente de treino.

## <a name="microsoft-hosts"></a>Anfitriões da Microsoft

Se não estiver configurada corretamente, a firewall pode causar problemas utilizando o seu espaço de trabalho. Há uma variedade de nomes hospedeiros que são usados tanto pelo espaço de trabalho Azure Machine Learning.

Os anfitriões desta secção são propriedade da Microsoft e fornecem serviços necessários para o bom funcionamento do seu espaço de trabalho.

| **Nome do anfitrião** | **Objetivo** |
| ---- | ---- |
| **login.microsoftonline.com** | Autenticação |
| **management.azure.com** | Usado para obter a informação do espaço de trabalho |
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
| **\*.file.core.windows.net** | Necessário pelo explorador de ficheiros no estúdio Azure Machine Learning. |
| **\*.dfs.core.windows.net** | Necessário pelo explorador de ficheiros no estúdio Azure Machine Learning. |
| **graph.windows.net** | Necessário para cadernos |

> [!TIP]
> Se planeia utilizar a identidade federada, siga as Melhores Práticas para garantir o artigo [da Federação de Diretórios Ativos.](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs)

## <a name="python-hosts"></a>Anfitriões python

Os anfitriões desta secção são utilizados para instalar pacotes Python. São necessários durante o desenvolvimento, formação e implantação. 

| **Nome do anfitrião** | **Objetivo** |
| ---- | ---- |
| **anaconda.com**</br>**\*.anaconda.com** | Usado para instalar pacotes predefinidos. |
| **\*.anaconda.org** | Usado para obter dados de repo. |
| **pypi.org** | Usado para listar dependências do índice padrão, se houver, e o índice não é substituído pelas definições do utilizador. Se o índice for substituído, também deve permitir **\* que .pythonhosted.org**. |

## <a name="r-hosts"></a>Anfitriões R

Os anfitriões desta secção são utilizados para instalar pacotes R. São necessários durante o desenvolvimento, formação e implantação.

> [!IMPORTANT]
> Internamente, o R SDK para Azure Machine Learning utiliza pacotes Python. Por isso, também deve permitir que os anfitriões python passem pela firewall.

| **Nome do anfitrião** | **Objetivo** |
| ---- | ---- |
| **cloud.r-project.org** | Utilizado na instalação de embalagens CRAN. |

## <a name="azure-government-region"></a>Região do Governo de Azure

URLs necessários para as regiões do Governo de Azure.

| **Nome do anfitrião** | **Objetivo** |
| ---- | ---- |
| **usgovarizona.api.ml.azure.us** | A região US-Arizona |
| **usgovvirginia.api.ml.azure.us** | A região US-Virginia |

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: Implementar e configurar o Azure Firewall com o portal do Azure](../firewall/tutorial-firewall-deploy-portal.md)
* [Proteger as tarefas de experimentação e de inferência do Azure ML numa Rede Virtual do Azure](how-to-network-security-overview.md)
