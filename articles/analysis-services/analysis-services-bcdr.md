---
title: Disponibilidade elevada do Analysis Services do Azure | Documentos da Microsoft
description: Garantindo a alta disponibilidade do Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 51a0f560a0e4b6ff791d5ed3f9f221eb2eeb9b4d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61036051"
---
# <a name="analysis-services-high-availability"></a>Elevada disponibilidade dos serviços de análise

Este artigo descreve a garantir a elevada disponibilidade dos servidores do Azure Analysis Services. 

## <a name="assuring-high-availability-during-a-service-disruption"></a>Garantindo a alta disponibilidade durante uma interrupção de serviço

Embora seja raro, um centro de dados do Azure pode ficar indisponível. Quando a falha ocorre, ele faz com que uma interrupção do negócio que pode durar alguns minutos ou pode durar horas. Elevada disponibilidade costuma ser obtida com redundância de servidor. Com o Azure Analysis Services, pode obter redundância através da criação de servidores secundários adicionais num ou mais regiões. Quando criar servidores redundantes, para garantir que os dados e metadados nesses servidores está sincronizado com o servidor numa região que ficou offline, pode:

* Implemente modelos em servidores redundantes em outras regiões. Este método requer que os dados de processamento no seu servidor primário e de servidores redundantes em paralelo, garantindo a todos os servidores estão sincronizados.

* [Cópia de segurança](analysis-services-backup.md) bases de dados do seu servidor primário e o restauro em servidores redundantes. Por exemplo, pode automatizar cópias de segurança noturnas ao armazenamento do Azure e restaurar para outros servidores redundantes em outras regiões. 

Em ambos os casos, se o servidor primário sofrer uma interrupção, tem de alterar as cadeias de ligação na geração de relatórios de clientes para ligar ao servidor noutro Datacenter regional. Esta alteração deve ser considerada um último recurso e ocorre apenas se uma falha catastrófica de dados regional do centro. É mais provável, uma indisponibilidade do Centro de dados que aloja o servidor primário seria fique novamente online antes de poderia atualizar ligações em todos os clientes. 

Para evitar a necessidade de alterar as cadeias de ligação nos clientes de geração de relatórios, pode criar um servidor [alias](analysis-services-server-alias.md) para o servidor primário. Se o servidor primário ficar inativo, pode alterar o alias para apontar para um servidor redundante noutra região. Pode automatizar alias para o nome do servidor por programar uma verificação de estado de funcionamento do ponto final no servidor primário. Se a verificação de estado de funcionamento falhar, pode direcionar o mesmo ponto final para um servidor redundante noutra região. 

## <a name="related-information"></a>Informações relacionadas

[Cópia de segurança e restauro](analysis-services-backup.md)   
[Gerir o Azure Analysis Services](analysis-services-manage.md)   
[Nomes de alias de servidor](analysis-services-server-alias.md) 

