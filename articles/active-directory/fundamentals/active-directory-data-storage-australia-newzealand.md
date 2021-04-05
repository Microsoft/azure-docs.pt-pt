---
title: Armazenamento de dados de clientes para clientes australianos e neozelandeses - Azure AD
description: Saiba onde o Azure Ative Directory armazena dados relacionados com o cliente para os seus clientes australianos e neozelandeses.
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 7/21/2020
ms.custom: it-pro, seodec18, references_regions
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d7c37e64e4f1b339ae66fe3d9135b1874476eb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836976"
---
# <a name="customer-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Armazenamento de dados de clientes para clientes australianos e neozelandeses em Diretório Ativo Azure 

O Azure Ative Directory (Azure AD) armazena os seus Dados de Clientes numa localização geográfica baseada no país que forneceu quando se inscreveu para um serviço Microsoft Online. Os serviços da Microsoft Online incluem o Microsoft 365 e o Azure. 

Para obter informações sobre onde está localizado o Azure AD e outros dados dos serviços da Microsoft, consulte a secção [Onde estão localizados os seus dados?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

A partir de 26 de fevereiro de 2020, a Microsoft começou a armazenar dados de clientes da Azure AD para novos inquilinos com um endereço de faturação australiano ou neozelandês dentro dos datacenters australianos. Entre 1 de maio de 2020 e 31 de março de 2021, a Microsoft migrará os inquilinos existentes que tenham um endereço de faturação australiano ou neozelandês para os datacenters australianos sem precisar de qualquer ação do cliente. O processo de migração não envolve nenhum tempo de inatividade para os clientes e não terá impacto em nenhuma funcionalidade de um inquilino durante a migração.

Além disso, certas funcionalidades da AD Azure ainda não suportam o armazenamento de dados do cliente na Austrália. Por favor, vá ao mapa de [dados Azure AD,](https://msit.powerbi.com/view?r=eyJrIjoiYzEyZTc5OTgtNTdlZS00ZTVkLWExN2ItOTM0OWU4NjljOGVjIiwidCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsImMiOjV9)para obter informações específicas sobre o recurso. Por exemplo, a Autenticação Multi-Factor AD do Microsoft Azure armazena dados de clientes nos EUA e processa-os globalmente. Consulte [os dados de residência de dados e os dados do cliente para autenticação multi-factor Azure AD](../authentication/concept-mfa-data-residency.md).

> [!NOTE]
> Os produtos, serviços e aplicações de terceiros da Microsoft que se integram com a Azure AD têm acesso aos Dados do Cliente. Avalie cada produto, serviço e aplicação que utiliza para determinar como os Dados do Cliente são tratados por esse produto, serviço e aplicação específicos, e se cumprem os requisitos de armazenamento de dados da sua empresa. Para obter mais informações sobre a residência de dados dos serviços Microsoft, veja a secção [Where is your data located?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) (Onde estão localizados os seus dados) do Microsoft Trust Center.