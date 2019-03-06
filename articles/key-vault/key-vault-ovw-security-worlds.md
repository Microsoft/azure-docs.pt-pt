---
ms.assetid: ''
title: Mundos de segurança do Azure Key Vault | Documentos da Microsoft
ms.service: key-vault
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 07/03/2017
ms.openlocfilehash: 86438ab7ebecc53aa716e72662965276b19f1b96
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57406163"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Mundos de segurança do Cofre de chaves do Azure e limites geográficos

O Azure Key Vault é um serviço de multi-inquilino e utiliza um conjunto de módulos de segurança de Hardware (HSMs) em cada localização do Azure. 

Todos os HSMs nas localizações do Azure na mesma região geográfica partilham o mesmo limite de criptografia (universo de segurança da Thales). Por exemplo, E.U.A. leste e E.U.A. oeste partilham o mesmo universo de segurança porque pertencem à geolocalização dos EUA. Da mesma forma, todas as localizações do Azure no Japão partilham o mesmo universo de segurança e todas as localizações do Azure na Austrália, Índia e assim por diante. 

## <a name="backup-and-restore-behavior"></a>Comportamento de cópia de segurança e restauro

Uma cópia de segurança retirada de uma chave de um cofre de chaves num único local do Azure pode ser restaurada para um cofre de chaves em outro local do Azure, desde que ambas estas condições forem verdadeiras:

- Ambas as localizações do Azure pertencem à mesma localização geográfica
- Ambos os cofres de chaves de pertencer à mesma subscrição do Azure

Por exemplo, uma cópia de segurança criada por uma determinada subscrição de uma chave num cofre de chaves na Índia Ocidental, só pode ser restaurado para outro Cofre de chaves na mesma subscrição e localização geográfica; Índia Ocidental, Índia Central ou sul da Índia.

## <a name="regions-and-products"></a>Produtos e regiões

- [Regiões do Azure](https://azure.microsoft.com/regions/)
- [Produtos da Microsoft por região](https://azure.microsoft.com/regions/services/)

Regiões são mapeadas para os mundos de segurança, mostrados como cabeçalhos principais nas tabelas:

Nos produtos de artigo de região, por exemplo, o **Americas** separador contém Leste dos E.U.A. CENTRAL, E.U.A. oeste todos os mapeamentos para a região do Americas. 

>[!NOTE]
>Uma exceção é que o US DOD EAST e US DOD CENTRAL tem seus próprios universos de segurança. 

Da mesma forma, sobre o **Europa** separador, Europa do Norte e Europa Ocidental ambos mapeados para a região da Europa. O mesmo também se aplica no **Ásia-Pacífico** separador.



