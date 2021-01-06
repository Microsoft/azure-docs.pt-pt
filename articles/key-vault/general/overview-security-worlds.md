---
title: Mundos de segurança Azure Key Vault Microsoft Docs
description: Azure Key Vault é um serviço de vários inquilinos. Utiliza uma piscina de HSMs em cada local de Azure. Todas as localizações de uma região geográfica partilham um limite criptográfico.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 07/03/2017
ms.openlocfilehash: d21deea13aac3d40c452a183c340d3108a1a01f4
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936333"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Mundos de segurança Azure Key Vault e fronteiras geográficas

Azure Key Vault é um serviço multi-inquilino e utiliza um conjunto de Módulos de Segurança de Hardware (HSMs) em cada local de Azure. 

Todos os HSMs em locais de Azure na mesma região geográfica partilham a mesma fronteira criptográfica (Thales Security World). Por exemplo, os EUA orientais e os EUA ocidentais partilham o mesmo mundo de segurança porque pertencem à localização geo-americana. Da mesma forma, todas as localizações de Azure no Japão partilham o mesmo mundo de segurança e todas as localizações de Azure na Austrália, Índia, e assim por diante. 

## <a name="backup-and-restore-behavior"></a>Backup e restaurar comportamento

Uma cópia de segurança tirada de uma chave de um cofre chave em um local de Azure pode ser restaurada para um cofre chave em outra localização Azure, desde que ambas estas condições sejam verdadeiras:

- Ambas as localizações do Azure pertencem à mesma localização geográfica
- Ambos os cofres pertencem à mesma assinatura Azure

Por exemplo, uma cópia de segurança tomada por uma determinada subscrição de uma chave num cofre chave na Índia Ocidental, só pode ser restaurada para outro cofre chave na mesma subscrição e localização geo; Índia Ocidental, Índia Central ou Sul da Índia.

## <a name="regions-and-products"></a>Regiões e produtos

- [Regiões do Azure](https://azure.microsoft.com/regions/)
- [Produtos da Microsoft por região](https://azure.microsoft.com/regions/services/)

As regiões são mapeadas para mundos de segurança, mostradas como principais rubricas nas tabelas:

No artigo de região, por exemplo, o separador **Americas** contém OS EUA, CENTRO DOS EUA, EUA Ocidentais todos mapeando para a região das Américas. 

>[!NOTE]
>Uma exceção é que os EUA DOD EAST e US DOD CENTRAL têm os seus próprios mundos de segurança. 

Da mesma forma, no **que** dizê-lo, a EUROPA DO NORTE e a Europa Ocidental ambos mapeiam para a região europeia. O mesmo acontece também com a **guia da Ásia-Pacífico.**
