---
title: Azure Key Vault mundos de segurança | Microsoft Docs
description: Azure Key Vault é um serviço de vários inquilinos. Utiliza uma piscina de HSMs em cada região de Azure. Todas as regiões de uma região geográfica partilham um limite criptográfico.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 07/03/2017
ms.openlocfilehash: 0d82a3cb4c08d47b6827072378b9827037d32412
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751819"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Mundos de segurança Azure Key Vault e fronteiras geográficas

Os produtos Azure estão disponíveis em [várias geografias Azure,](https://azure.microsoft.com/en-us/global-infrastructure/geographies/)com cada geografia Azure contendo uma ou mais regiões. Por exemplo, a geografia europeia contém duas regiões -- Norte da Europa e Europa Ocidental -- enquanto a única região da geografia brasileira é o Brasil Sul.

Azure Key Vault é um serviço multi-inquilino que utiliza um conjunto de Módulos de Segurança de Hardware (HSMs). Todos os HSMs numa geografia partilham a mesma fronteira criptográfica, referida como um "mundo de segurança". Cada geografia corresponde a um único mundo de segurança, e vice-versa.

Os EUA orientais e os EUA ocidentais partilham o mesmo mundo de segurança porque pertencem à geografia (Estados Unidos). Da mesma forma, todas as regiões de Azure no Japão partilham o mesmo mundo de segurança, como todas as regiões de Azure na Austrália, etc.

>[!NOTE]
> Uma exceção é que os EUA DOD EAST e US DOD CENTRAL têm os seus próprios mundos de segurança.

## <a name="backup-and-restore-behavior"></a>Backup e restaurar comportamento

Um backup tirado de uma chave de um cofre chave em uma região de Azure pode ser restaurado para um cofre chave em outra região de Azure, desde que ambas as condições sejam verdadeiras:

- Ambas as regiões do Azure pertencem à mesma geografia.
- Ambos os cofres pertencem à mesma assinatura Azure.

Por exemplo, uma cópia de segurança tirada de uma chave num cofre-chave da Índia Ocidental pode ser restaurada para outro cofre chave na mesma subscrição na geografia da Índia (as regiões da Índia Ocidental, Índia Central e Índia do Sul).

## <a name="next-steps"></a>Passos seguintes

- Ver [produtos da Microsoft por região](https://azure.microsoft.com/regions/services/)
