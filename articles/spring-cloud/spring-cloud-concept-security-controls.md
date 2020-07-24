---
title: Conceito - Controlos de segurança para O Serviço de Nuvem de primavera Azure
description: Utilize controlos de segurança incorporados no Serviço de Nuvem de primavera do Azure.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 2e001e5e927d9d4c5dc4c3eb74f7b5ad33617b99
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87037581"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Controlos de segurança do Serviço Azure Spring Cloud
Os controlos de segurança são incorporados no Azure Spring Cloud Service.

Um controlo de segurança é uma qualidade ou característica de um serviço Azure que contribui para a capacidade do serviço de prevenir, detetar e responder a vulnerabilidades de segurança.  Para cada controlo, utilizamos *Sim* ou *Não* para indicar se está atualmente em vigor para o serviço.  Utilizamos *N/A* para um controlo que não é aplicável ao serviço. 

**Controlos de segurança da proteção de dados**

| Controlo de segurança | Sim/Não | Notas | Documentação |
|:-------------|:-------|:-------------------------------|:----------------------|
| Encriptação do lado do servidor em repouso: teclas geridas pela Microsoft | Yes | Fontes e artefactos carregados pelo utilizador, configurações de servidores config, definições de aplicações e dados em armazenamento persistente são armazenados no Azure Storage, que encripta automaticamente o conteúdo em repouso.<br><br>Cache do servidor Config, binários de tempo de execução construídos a partir de fontes carregadas e registos de aplicações durante o tempo de vida útil da aplicação são guardados para O Disco Gerido pelo Azure, que encripta automaticamente o conteúdo em repouso.<br><br>As imagens de contentores construídas a partir de uma fonte de carregamento do utilizador são guardadas no Registo do Contentor Azure, que encripta automaticamente o conteúdo da imagem em repouso. | [Azure Storage encryption for data at rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (Encriptação do Armazenamento do Azure para dados inativos)<br><br>[Encriptação do lado do servidor dos discos geridos pelo Azure](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption)<br><br>[Armazenamento de imagem de contentor no registo do contentor de Azure](https://docs.microsoft.com/azure/container-registry/container-registry-storage) |
| Encriptação em transitório | Yes | Os pontos finais públicos da aplicação do utilizador utilizam HTTPS para tráfego de entrada por padrão. |  |
| Chamadas da API encriptadas | Yes | As chamadas de gestão para configurar o serviço Azure Spring Cloud ocorrem através de chamadas do Azure Resource Manager através de HTTPS. | [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) |

**Controlos de segurança de acesso à rede**

| Controlo de segurança | Sim/Não | Notas | Documentação |
|:-------------|:-------|:-------------------------------|:----------------------|
| Etiqueta de serviço | Yes | Utilize a etiqueta de serviço **AzureSpringCloud** para definir controlos de acesso à rede de saída em [grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) de rede ou [Azure Firewall](https://docs.microsoft.com/azure/firewall/service-tags), para permitir o tráfego para aplicações Azure Spring Cloud.<br><br>*Nota:* Atualmente apenas a nova instância de serviço Azure Spring Cloud criada após 2020/07/14 suporta a tag de serviço **AzureSpringCloud.** | [Etiquetas de serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview) |
