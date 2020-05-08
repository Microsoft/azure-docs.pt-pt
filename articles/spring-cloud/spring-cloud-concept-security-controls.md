---
title: Conceito - Controlos de segurança para o Serviço de Nuvem de primavera Azure
description: Utilize controlos de segurança incorporados no Serviço de Nuvem de primavera Azure.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: 5b459ef57d0e8a22ce1cd53f56c44d31e53c7c93
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594989"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Controlos de segurança para o Serviço de Nuvem de primavera Azure
Os controlos de segurança são integrados no Serviço de Nuvem de primavera Azure.

Um controlo de segurança é uma qualidade ou característica de um serviço Azure que contribui para a capacidade do serviço de prevenir, detetar e responder a vulnerabilidades de segurança.  Para cada controlo, usamos *Sim* ou *Não* para indicar se está atualmente em vigor para o serviço.  Utilizamos *N/A* para um controlo que não é aplicável ao serviço. 

**Controlos de segurança de proteção de dados**

| Controlo de segurança | Sim/Não | Notas | Documentação |
|:-------------|:-------|:-------------------------------|:----------------------|
| Encriptação do lado do servidor em repouso: Chaves geridas pela Microsoft | Sim | O utilizador carregou fonte e artefactos, definições do servidor config, definições de aplicativos e dados em armazenamento persistente são armazenados no Armazenamento Azure, que encripta automaticamente o conteúdo em repouso.<br><br>A cache do servidor Config, os binários de tempo de funcionamento construídos a partir de fonte supértuada e os registos de aplicação durante o período de vida da aplicação são guardados para o Disco Gerido pelo Azure, que encripta automaticamente o conteúdo em repouso.<br><br>As imagens de contentores construídas a partir da fonte carregada pelo utilizador são guardadas no Registo de Contentores Azure, que encripta automaticamente o conteúdo da imagem em repouso. | [Azure Storage encryption for data at rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (Encriptação do Armazenamento do Azure para dados inativos)<br><br>[Encriptação do lado do servidor dos discos geridos pelo Azure](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption)<br><br>[Armazenamento de imagem de contentor no Registo de Contentores de Azure](https://docs.microsoft.com/azure/container-registry/container-registry-storage) |
| Encriptação em transiente | Sim | Os pontos finais da aplicação do utilizador utilizam https para o tráfego de entrada por defeito. |  |
| Chamadas api encriptadas | Sim | As chamadas de gestão para configurar o serviço Azure Spring Cloud ocorrem através de chamadas do Gestor de Recursos Azure através de HTTPS. | [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) |

