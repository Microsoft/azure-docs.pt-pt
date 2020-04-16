---
title: Mundos de segurança do Cofre Chave Azure Microsoft Docs
description: Azure Key Vault é um serviço multi-inquilinos. Usa uma piscina de HSMs em cada local Azure. Todos os locais de uma região geográfica partilham uma fronteira criptográfica.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 07/03/2017
ms.openlocfilehash: 3584f83c5e1a5e83d069373395227b70c084eae9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428956"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Mundos de segurança do Cofre-Chave Azure e fronteiras geográficas

O Azure Key Vault é um serviço multi-inquilino e utiliza uma piscina de Módulos de Segurança de Hardware (HSMs) em cada local do Azure. 

Todos os HSMs em localizações de Azure na mesma região geográfica partilham a mesma fronteira criptográfica (Thales Security World). Por exemplo, os EUA Orientais e os EUA Ocidentais partilham o mesmo mundo de segurança porque pertencem à localização geosnorte dos EUA. Da mesma forma, todas as localizações do Azure no Japão partilham o mesmo mundo de segurança e todas as localizações do Azure na Austrália, Índia, e assim por diante. 

## <a name="backup-and-restore-behavior"></a>Backup e restaurar o comportamento

Uma cópia de segurança tirada de uma chave de um cofre chave em uma localização Azure pode ser restaurada para um cofre chave em outro local Azure, desde que ambas as condições sejam verdadeiras:

- Ambas as localizações do Azure pertencem à mesma localização geográfica
- Ambos os cofres principais pertencem à mesma assinatura Azure

Por exemplo, uma cópia de segurança tomada por uma dada assinatura de uma chave num cofre chave na Índia Ocidental, só pode ser restaurada para outro cofre chave na mesma subscrição e localização geo; Índia Ocidental, Índia Central ou Sul da Índia.

## <a name="regions-and-products"></a>Regiões e produtos

- [Regiões de Azure](https://azure.microsoft.com/regions/)
- [Produtos microsoft por região](https://azure.microsoft.com/regions/services/)

As regiões são mapeadas para mundos de segurança, mostrados como principais títulos nas tabelas:

No artigo de produtos por região, por exemplo, o separador **Americas** contém LESTE DOS, EUA Centrais, OESTE DOS EUA todos mapeando para a região das Américas. 

>[!NOTE]
>Uma exceção é que o DOD EAST e o US DOD CENTRAL têm os seus próprios mundos de segurança. 

Do mesmo modo, no separador **Europa,** a EUROPA DO Norte e a EUROPA Ocidental mapeiam ambos para a região da Europa. O mesmo acontece no separador **Ásia-Pacífico.**



