---
title: Visão geral do HSM gerido Azure - Azure Managed HSM | Microsoft Docs
description: O Azure Managed HSM é um serviço de nuvem que protege as suas chaves criptográficas para aplicações em nuvem.
services: key-vault
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: overview
ms.custom: mvc
ms.date: 04/01/2021
ms.author: mbaldwin
author: msmbaldwin
ms.openlocfilehash: a9df83535bf0ee7a6c484a7682392d2ad0ef59f7
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815329"
---
# <a name="what-is-azure-key-vault-managed-hsm"></a>O que é o HSM Gerido do Azure Key Vault?

Azure Key Vault Managed HSM é um serviço de nuvem totalmente gerido, altamente disponível, de inquilino único, que permite proteger chaves criptográficas para as suas aplicações em nuvem, utilizando HSMs validados **FIPS 140-2 Nível 3.** Para obter informações sobre preços, consulte a secção De Pools HSM Gerido na [página de preços do Cofre da Chave Azure](https://azure.microsoft.com/pricing/details/key-vault/). 

## <a name="why-use-managed-hsm"></a>Porquê utilizar o HSM gerido?

### <a name="fully-managed-highly-available-single-tenant-hsm-as-a-service"></a>Totalmente gerido, altamente disponível, único inquilino HSM como um serviço

- **Totalmente gerido**: O fornecimento, configuração, remendos e manutenção do HSM são manuseados pelo serviço. 
- **Altamente disponível e zona resiliente** (onde as zonas de disponibilidade são suportadas): Cada cluster HSM é composto por múltiplas divisórias HSM que se estendem por pelo menos duas zonas de disponibilidade. Se o hardware falhar, as divisórias dos membros do seu cluster HSM serão automaticamente migradas para nós saudáveis.
- **Inquilino único**: Cada instância gerida do HSM é dedicada a um único cliente e consiste num aglomerado de múltiplas divisórias HSM. Cada cluster HSM utiliza um domínio de segurança específico do cliente que isola criptograficamente o cluster HSM de cada cliente.


### <a name="access-control-enhanced-data-protection--compliance"></a>Controlo de acesso, proteção de dados melhorada & conformidade

- **Gestão centralizada de chaves**: Gerencie chaves críticas e de alto valor em toda a sua organização num só local. Com permissões granulares por chave, controle o acesso a cada chave no princípio do "acesso menos privilegiado".
- **Controlo de acesso isolado**: O modelo de controlo de acesso "local RBAC" gerido do HSM permite que os administradores de cluster hsm designados tenham controlo total sobre os HSMs que mesmo os administradores do grupo de gestão, subscrição ou grupo de recursos não podem sobrepor-se.
- **HSMs validados de nível 3 FIPS 140-2**: Proteja os seus dados e cumpra os requisitos de conformidade com o FIPS ((Federal Information Protection Standard)) 140-2 Nível 3 HSMs validados. Os HSMs geridos utilizam adaptadores HSM da Marvell LiquidSecurity.
- **Monitor e auditoria:** totalmente integrado com monitor Azure. Obtenha registos completos de toda a atividade através do Azure Monitor. Utilize a Azure Log Analytics para análise e alertas.
- **Residência de dados**: A HSM gerida não armazena/processa os dados dos clientes fora da região onde o cliente implementa a instância HSM.

### <a name="integrated-with-azure-and-microsoft-paassaas-services"></a>Integrado com serviços Azure e Microsoft PaaS/SaaS 

- Gere (ou importe usando as teclas [BYOK)](hsm-protected-keys-byok.md)e use-as para encriptar os seus dados em repouso em serviços Azure, tais como [Azure Storage,](../../storage/common/customer-managed-keys-overview.md) [Azure SQL,](../../azure-sql/database/transparent-data-encryption-byok-overview.md)e [Azure Information Protection](/azure/information-protection/byok-price-restrictions).

### <a name="uses-same-api-and-management-interfaces-as-key-vault"></a>Usa a mesma API e interfaces de gestão como Key Vault

- Migrar facilmente as suas aplicações existentes que usam um cofre (um multi-inquilino) para utilizar HSMs geridos.
- Utilize os mesmos padrões de desenvolvimento e implantação de aplicações para todas as suas aplicações, independentemente da solução de gestão chave em uso: cofres multi-inquilinos ou HSMs geridos por um único inquilino

### <a name="import-keys-from-your-on-premise-hsms"></a>Chaves de importação dos seus HSMs no local

- Gerencie as chaves protegidas pelo HSM no seu HSM no local e importe-as de forma segura em HSM gerido

## <a name="next-steps"></a>Passos seguintes
- Ver [Quickstart: Provisão e ativação de um HSM gerido utilizando o Azure CLI](quick-create-cli.md) para criar e ativar um HSM gerido
- Ver [Boas Práticas usando Azure Key Vault Gerido HSM](best-practices.md)
