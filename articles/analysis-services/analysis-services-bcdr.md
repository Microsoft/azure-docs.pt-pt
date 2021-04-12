---
title: Serviços de Análise Azure alta disponibilidade | Microsoft Docs
description: Este artigo descreve como os Serviços de Análise Azure proporcionam uma elevada disponibilidade durante a interrupção do serviço.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 762a5b3c1a5111d164cd61f3811c96aa85406561
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732096"
---
# <a name="analysis-services-high-availability"></a>Elevada disponibilidade do Analysis Services

Este artigo descreve assegurando alta disponibilidade para servidores Azure Analysis Services. 

## <a name="assuring-high-availability-during-a-service-disruption"></a>Assegurar alta disponibilidade durante uma interrupção de serviço

Embora raro, um centro de dados Azure pode ter uma paragem. Quando ocorre uma paralisação, causa uma perturbação de negócio que pode durar alguns minutos ou pode durar horas. A alta disponibilidade é mais frequentemente conseguida com a redundância do servidor. Com os Serviços de Análise Azure, pode obter redundância criando servidores secundários adicionais em uma ou mais regiões. Ao criar servidores redundantes, para assegurar que os dados e metadados desses servidores estão em sincronização com o servidor numa região que ficou offline, pode:

* Implementar modelos para servidores redundantes noutras regiões. Este método requer o processamento de dados tanto no seu servidor primário como em servidores redundantes em paralelo, garantindo que todos os servidores estão sincronizados.

* [Bases](analysis-services-backup.md) de dados de backup do seu servidor primário e restauro em servidores redundantes. Por exemplo, pode automatizar backups noturnos para o armazenamento do Azure e restaurar para outros servidores redundantes noutras regiões. 

Em qualquer dos casos, se o seu servidor primário sofrer uma falha, tem de alterar as cadeias de ligação ao reportar clientes para se ligar ao servidor num centro de dados regional diferente. Esta alteração deve ser considerada um último recurso e apenas se ocorrer uma falha catastrófica do centro de dados regional. É mais provável que uma falha no centro de dados que hospede o seu servidor primário volte a estar on-line antes de poder atualizar as ligações em todos os clientes. 

Para evitar ter de alterar as cadeias de ligação dos clientes inquiridos, pode criar [um pseudónimo de](analysis-services-server-alias.md) servidor para o seu servidor primário. Se o servidor primário cair, pode alterar o pseudónimo para apontar para um servidor redundante noutra região. Pode automatizar o nome do servidor codificando uma verificação de saúde no servidor primário. Se o exame de saúde falhar, o mesmo ponto final pode dirigir-se a um servidor redundante noutra região. 

## <a name="related-information"></a>Informações relacionadas

[Backup e restauro](analysis-services-backup.md)   
[Gerir serviços de análise Azure](analysis-services-manage.md)   
[Nomes do servidor de pseudónimos](analysis-services-server-alias.md) 

