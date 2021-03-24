---
title: Controlos de segurança do Serviço Azure Spring Cloud
description: Utilize controlos de segurança incorporados no Serviço de Nuvem de primavera do Azure.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 62a0bd19f6b10bbe6561f5587ed85d4d1e5880b3
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878625"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Controlos de segurança do Serviço Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java ✔️ C #

Os controlos de segurança são incorporados no Azure Spring Cloud Service.

Um controlo de segurança é uma qualidade ou característica de um serviço Azure que contribui para a capacidade do serviço de prevenir, detetar e responder a vulnerabilidades de segurança.  Para cada controlo, utilizamos *Sim* ou *Não* para indicar se está atualmente em vigor para o serviço.  Utilizamos *N/A* para um controlo que não é aplicável ao serviço. 

**Controlos de segurança da proteção de dados**

| Controlo de segurança | Sim/Não | Notas | Documentação |
|:-------------|:-------|:-------------------------------|:----------------------|
| Encriptação do lado do servidor em repouso: teclas geridas pela Microsoft | Sim | Fontes e artefactos carregados pelo utilizador, configurações de servidores config, definições de aplicações e dados em armazenamento persistente são armazenados no Azure Storage, que encripta automaticamente o conteúdo em repouso.<br><br>Cache do servidor Config, binários de tempo de execução construídos a partir de fontes carregadas e registos de aplicações durante o tempo de vida útil da aplicação são guardados para o disco gerido Azure, que encripta automaticamente o conteúdo em repouso.<br><br>As imagens de contentores construídas a partir de uma fonte de carregamento do utilizador são guardadas no Registo do Contentor Azure, que encripta automaticamente o conteúdo da imagem em repouso. | [Azure Storage encryption for data at rest](../storage/common/storage-service-encryption.md) (Encriptação do Armazenamento do Azure para dados inativos)<br><br>[Encriptação do lado do servidor dos discos geridos pelo Azure](../virtual-machines/disk-encryption.md)<br><br>[Armazenamento de imagem de contentor no registo do contentor de Azure](../container-registry/container-registry-storage.md) |
| Encriptação em transitório | Sim | Os pontos finais públicos da aplicação do utilizador utilizam HTTPS para tráfego de entrada por padrão. |  |
| Chamadas da API encriptadas | Sim | As chamadas de gestão para configurar o serviço Azure Spring Cloud ocorrem através de chamadas do Azure Resource Manager através de HTTPS. | [Azure Resource Manager](../azure-resource-manager/index.yml) |

**Controlos de segurança de acesso à rede**

| Controlo de segurança | Sim/Não | Notas | Documentação |
|:-------------|:-------|:-------------------------------|:----------------------|
| Etiqueta de serviço | Sim | Utilize a etiqueta de serviço **AzureSpringCloud** para definir controlos de acesso à rede de saída em [grupos de segurança](../virtual-network/network-security-groups-overview.md#security-rules) de rede ou [Azure Firewall](../firewall/service-tags.md), para permitir o tráfego para aplicações Azure Spring Cloud.<br><br>*Nota:* Atualmente apenas a nova instância de serviço Azure Spring Cloud criada após 2020/07/14 suporta a tag de serviço **AzureSpringCloud.** | [Etiquetas de serviço](../virtual-network/service-tags-overview.md) |

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Implemente a sua primeira aplicação Azure Spring Cloud](spring-cloud-quickstart.md)