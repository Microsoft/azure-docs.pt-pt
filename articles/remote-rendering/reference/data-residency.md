---
title: Residência dos dados
description: Descreve a residência de dados ao utilizar a renderização remota Azure.
author: rapete
ms.author: rapete
ms.date: 02/04/2021
ms.topic: reference
ms.openlocfilehash: f20b3bb3de877ac627f5f6909c98cb9e1553f2a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "99576897"
---
# <a name="azure-remote-rendering-data-residency"></a>Residência de dados de renderização remota Azure 
Este artigo explica o conceito de residência de dados e como se aplica à Renderização Remota Azure. 

## <a name="data-residency"></a>Residência dos dados 
A Azure Remote Rendering utiliza o utilizador fornecido contentores Azure Blob para armazenamento de modelos. Quando o modelo não está a ser utilizado, permanece no utilizador desde que a região de Armazenamento Azure Blob. Quando o modelo é utilizado para renderização, os dados são copiados para a região que o utilizador escolhe ao iniciar a sessão de renderização.

## <a name="next-steps"></a>Passos seguintes
Se pretender aprender a configurar um recipiente de armazenamento Azure Blob para renderização remota Azure, consulte [converter um modelo para renderização](../quickstarts/convert-model.md).
