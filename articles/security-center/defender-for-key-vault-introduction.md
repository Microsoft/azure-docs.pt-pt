---
title: Azure Defender for Key Vault - os benefícios e funcionalidades
description: Conheça os benefícios e funcionalidades do Azure Defender para o Key Vault.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 06818e443568918e2ee87bbfbec81836ea85648b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939877"
---
# <a name="introduction-to-azure-defender-for-key-vault"></a>Introdução ao Azure Defender para o Cofre de Chaves

Azure Key Vault é um serviço de nuvem que protege chaves de encriptação e segredos como certificados, cadeias de conexão e senhas. 

Ativar **o Azure Defender para Key Vault** para a proteção de ameaças avançadas e nativa do Azure para o Azure Key Vault, fornecendo uma camada adicional de inteligência de segurança. 

## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Geralmente disponível (GA)|
|Preços:|**Azure Defender for Key Vault** é faturado como mostrado na página de [preços](security-center-pricing.md)|
|Nuvens:|![Yes](./media/icons/yes-icon.png) Nuvens comerciais<br>![No](./media/icons/no-icon.png) Nacional/Soberano (Gov dos EUA, China Gov, Outro Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-key-vault"></a>Quais são os benefícios do Azure Defender para o Key Vault?

O Azure Defender deteta tentativas incomuns e potencialmente nocivas de aceder ou explorar contas key vault. Esta camada de proteção permite-lhe lidar com ameaças sem ser um perito em segurança, e sem a necessidade de gerir sistemas de monitorização de segurança de terceiros.  

Quando ocorrem atividades anómalas, o Azure Defender mostra alertas e envia-os opcionalmente por e-mail para membros relevantes da sua organização. Estes alertas incluem os detalhes da atividade suspeita e recomendações sobre como investigar e remediar ameaças. 

## <a name="azure-defender-for-key-vault-alerts"></a>Azure Defender para alertas de cofre de chaves
Quando receber um alerta do Azure Defender para o Key Vault, recomendamos que investigue e responda ao alerta como descrito no [Respond to Azure Defender for Key Vault](defender-for-key-vault-usage.md). O Azure Defender for Key Vault protege aplicações e credenciais, por isso mesmo que esteja familiarizado com a aplicação ou utilizador que desencadeou o alerta, é importante verificar a situação em torno de cada alerta.

Os alertas aparecem na página de **Segurança** do Key Vault, no painel de instrumentos do Azure Defender e na página de alertas do Centro de Segurança.

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Página de segurança do Azure Key Vault":::

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre o Azure Defender para o Key Vault.

Para obter material relacionado, consulte os seguintes artigos: 

- [Alertas de segurança key Vault](alerts-reference.md#alerts-azurekv)-- A secção do Cofre chave da tabela de referência para todos os alertas do Centro de Segurança Azure
- [Alertas de exportação para um SIEM](continuous-export.md)
- [Alertas de Supressão do Azure Defender](alerts-suppression-rules.md)