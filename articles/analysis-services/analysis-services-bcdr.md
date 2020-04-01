---
title: Serviços de Análise Azure elevada disponibilidade Microsoft Docs
description: Este artigo descreve como os Serviços de Análise Azure proporcionam uma elevada disponibilidade durante a interrupção do serviço.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 78a6d41b638d79111a58830f0cb0d5190ea0796c
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408672"
---
# <a name="analysis-services-high-availability"></a>Serviços de Análise alta disponibilidade

Este artigo descreve a elevada disponibilidade para servidores dos Serviços de Análise Azure. 

## <a name="assuring-high-availability-during-a-service-disruption"></a>Garantir a elevada disponibilidade durante uma interrupção do serviço

Embora raro, um centro de dados Azure pode ter uma paragem. Quando ocorre uma paragem, causa uma perturbação no negócio que pode durar alguns minutos ou pode durar horas. A elevada disponibilidade é mais frequentemente conseguida com a redundância do servidor. Com os Serviços de Análise Azure, pode obter redundância criando servidores secundários adicionais em uma ou mais regiões. Ao criar servidores redundantes, para garantir que os dados e metadados desses servidores estão em sintonia com o servidor numa região que ficou offline, pode:

* Implementar modelos para servidores redundantes noutras regiões. Este método requer o processamento de dados tanto no seu servidor primário como em servidores redundantes em paralelo, garantindo que todos os servidores estão em sincronização.

* [Backup](analysis-services-backup.md) bases de dados do seu servidor principal e restaurar em servidores redundantes. Por exemplo, pode automatizar cópias de segurança noturnas para o armazenamento do Azure e restaurar outros servidores redundantes noutras regiões. 

Em qualquer dos casos, se o seu servidor principal experimentar uma falha, deve alterar as cadeias de ligação em reportar clientes para se conectar ao servidor num centro de dados regional diferente. Esta alteração deve ser considerada um último recurso e apenas se ocorrer uma interrupção catastrófica do centro de dados regional. É mais provável que uma falha no data center que aloja o seu servidor principal volte a estar online antes de poder atualizar as ligações em todos os clientes. 

Para evitar ter de alterar as cadeias de ligação em clientes reportados, pode criar um [pseudónimo](analysis-services-server-alias.md) de servidor para o seu servidor principal. Se o servidor primário cair, pode alterar o pseudónimo para apontar para um servidor redundante noutra região. Pode automatizar pseudónimos ao nome do servidor, codificando uma verificação de saúde no servidor primário. Se o exame de saúde falhar, o mesmo ponto final pode dirigir-se a um servidor redundante noutra região. 

## <a name="related-information"></a>Informações relacionadas

[Backup e restauro](analysis-services-backup.md)   
[Gerir serviços de análise azure](analysis-services-manage.md)   
[Nomes de servidores de Alias](analysis-services-server-alias.md) 

