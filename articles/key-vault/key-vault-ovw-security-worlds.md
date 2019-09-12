---
title: Azure Key Vault mundos de segurança | Microsoft Docs
ms.service: key-vault
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 07/03/2017
ms.openlocfilehash: 1e18befe05e5a1d33aa4e5445b80c6d6fd7239af
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883185"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Azure Key Vault os mundos de segurança e os limites geográficos

Azure Key Vault é um serviço multilocatário e usa um pool de HSMs (módulos de segurança de hardware) em cada local do Azure. 

Todos os HSMs em locais do Azure na mesma região geográfica compartilham o mesmo limite de criptografia (Thales Security World). Por exemplo, leste dos EUA e oeste dos EUA compartilham o mesmo mundo de segurança porque eles pertencem à localização geográfica dos EUA. Da mesma forma, todos os locais do Azure no Japão compartilham o mesmo mundo de segurança e todos os locais do Azure na Austrália, na Índia e assim por diante. 

## <a name="backup-and-restore-behavior"></a>Comportamento de backup e restauração

Um backup feito de uma chave de um cofre de chaves em um local do Azure pode ser restaurado para um cofre de chaves em outro local do Azure, desde que ambas as condições sejam verdadeiras:

- Os dois locais do Azure pertencem à mesma localização geográfica
- Ambos os cofres de chaves pertencem à mesma assinatura do Azure

Por exemplo, um backup feito por uma determinada assinatura de uma chave em um cofre de chaves na Índia ocidental, só pode ser restaurado para outro cofre de chaves na mesma assinatura e localização geográfica; Índia ocidental, Índia central ou sul da Índia.

## <a name="regions-and-products"></a>Regiões e produtos

- [Regiões do Azure](https://azure.microsoft.com/regions/)
- [Produtos da Microsoft por região](https://azure.microsoft.com/regions/services/)

As regiões são mapeadas para os mundos de segurança, mostrados como principais títulos nas tabelas:

No artigo produtos por região, por exemplo, a guia **Américas** contém leste dos EUA, EUA Central, oeste dos EUA todos mapeando para a região das Américas. 

>[!NOTE]
>Uma exceção é que US DOD leste e US DOD CENTRAL têm seus próprios mundos de segurança. 

Da mesma forma, na guia **Europa** , norte da Europa e Europa Ocidental mapeiam para a região da Europa. O mesmo também é verdadeiro na guia **Pacífico Asiático** .



