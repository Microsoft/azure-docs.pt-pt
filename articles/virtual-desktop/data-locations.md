---
title: Localizações de dados para Windows Virtual Desktop - Azure
description: Uma breve visão geral das localizações que localiza os dados e metadados do Windows Virtual Desktop são armazenados.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 853f5766c4fd6344eecfd7be1d7911163133a8a5
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611539"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Localizações de dados para Windows Virtual Desktop

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização da primavera de 2020 com os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows. Se estiver a utilizar o lançamento do Windows Virtual Desktop Fall 2019 sem objetos do Gestor de Recursos Azure, consulte [este artigo](./virtual-desktop-fall-2019/data-locations-2019.md).
>
> A atualização Do Windows Virtual Desktop Spring 2020 encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos usá-la para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

O Windows Virtual Desktop está atualmente disponível para todos os locais geográficos. Os administradores podem escolher a localização para armazenar dados dos utilizadores quando criam o conjunto de anfitriões máquinas virtuais e serviços associados, como servidores de ficheiros. Saiba mais sobre geografias Azure no mapa do [datacenter azure.](https://azuredatacentermap.azurewebsites.net/)

>[!NOTE]
>A Microsoft não controla nem limita as regiões onde você ou os seus utilizadores podem aceder aos seus dados específicos de utilizador e aplicações.

>[!IMPORTANT]
>O Windows Virtual Desktop armazena informações globais de metadados como nomes de inquilinos, nomes de piscinas, nomes de grupos de aplicações e nomes principais do utilizador num datacenter. Sempre que um cliente cria um objeto de serviço, deve entrar num local para o objeto de serviço. A localização em que entram determina onde os metadados para o objeto serão armazenados. O cliente escolherá uma região azure e os metadados serão armazenados na geografia relacionada. Para obter uma lista de todas as regiões azure e geografias relacionadas, consulte [geografias Azure.](https://azure.microsoft.com/global-infrastructure/geographies/)

Neste momento, apenas apoiamos o armazenamento de metadados na geografia azure dos Estados Unidos (EUA). Os metadados armazenados são encriptados em repouso, e os espelhos geo-redundantes são mantidos dentro da geografia. Todos os dados do cliente, tais como configurações de apps e dados do utilizador, residem no local que o cliente escolhe e não é gerido pelo serviço. Mais geografias ficarão disponíveis à medida que o serviço crescer.

Os metadados de serviço são replicados dentro da geografia Azure para fins de recuperação de desastres.