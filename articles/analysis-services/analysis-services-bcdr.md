---
title: Azure Analysis Services alta disponibilidade | Microsoft Docs
description: Garantindo Azure Analysis Services alta disponibilidade.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9947ab24262c6b92457bcd858bbf03d21eb317a2
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619304"
---
# <a name="analysis-services-high-availability"></a>Analysis Services alta disponibilidade

Este artigo descreve como garantir a alta disponibilidade para servidores de Azure Analysis Services. 

## <a name="assuring-high-availability-during-a-service-disruption"></a>Garantindo alta disponibilidade durante uma interrupção do serviço

Embora seja raro, um data center do Azure pode ter uma interrupção. Quando ocorre uma interrupção, ela causa uma interrupção de negócios que pode durar alguns minutos ou pode durar horas. A alta disponibilidade geralmente é obtida com a redundância do servidor. Com o Azure Analysis Services, você pode obter redundância criando servidores secundários adicionais em uma ou mais regiões. Ao criar servidores redundantes, para garantir que os dados e metadados nesses servidores estejam em sincronia com o servidor em uma região que ficou offline, você pode:

* Implante modelos em servidores redundantes em outras regiões. Esse método requer o processamento de dados no servidor primário e servidores redundantes em paralelo, garantindo que todos os servidores estejam em sincronia.

* [Faça backup](analysis-services-backup.md) de bancos de dados do seu servidor primário e restaure em servidores redundantes. Por exemplo, você pode automatizar backups noturnos para o armazenamento do Azure e restaurar para outros servidores redundantes em outras regiões. 

Em ambos os casos, se o servidor primário sofrer uma interrupção, você deverá alterar as cadeias de conexão em clientes de relatório para se conectar ao servidor em um datacenter regional diferente. Essa alteração deve ser considerada um último recurso e somente se ocorrer uma interrupção de data center regional catastrófica. É mais provável que uma interrupção de data center que hospeda seu servidor primário volte a ficar online antes de você poder atualizar as conexões em todos os clientes. 

Para evitar a alteração de cadeias de conexão em clientes de relatório, você pode criar um [alias](analysis-services-server-alias.md) de servidor para o servidor primário. Se o servidor primário falhar, você poderá alterar o alias para apontar para um servidor redundante em outra região. Você pode automatizar o alias para o nome do servidor codificando uma verificação de integridade do ponto de extremidade no servidor primário. Se a verificação de integridade falhar, o mesmo ponto de extremidade poderá direcionar para um servidor redundante em outra região. 

## <a name="related-information"></a>Informações relacionadas

[Backup e restauração](analysis-services-backup.md)   
[Gerenciar Azure Analysis Services](analysis-services-manage.md)   
[Nomes de servidor de alias](analysis-services-server-alias.md) 

