---
title: Utilização de certificados - Azure Batch
description: Utilizar certificados para permitir a autenticação de aplicações
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 5f7d547ad549b2747ae41182ee4058b001d9e78a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89146390"
---
# <a name="using-certificates-with-batch"></a>Utilizar certificados com o Batch

Atualmente, a principal razão para usar certificados com o Batch é se tiver aplicações em execução em Pools que precisam de autenticar com um ponto final. 

Se ainda não tiver um certificado, pode criar um certificado auto-assinado utilizando a `makecert` ferramenta de linha de comando.

## <a name="upload-certificates-manually-through-the-azure-portal"></a>Faça upload manualmente dos certificados através do portal Azure

1. A partir da conta Lote pretende carregar um certificado para, selecionar **Certificados** e, em seguida, selecionar **Adicionar**. 

2. Faça o upload do certificado com uma extensão .pfx ou .cer. 

Uma vez carregado, o certificado é adicionado a uma lista de certificados, e você pode verificar a impressão digital.

Agora, quando criar uma piscina de Lote, pode navegar para certificados dentro da piscina e atribuir o certificado que carregou para essa piscina.

## <a name="next-steps"></a>Passos seguintes

Batch tem um certificado API, [certificado de lote AZ criar](/cli/azure/batch/certificate)

Para obter informações sobre a utilização do Key Vault, consulte [o Cofre de Chaves de acesso Seguro com o Lote](credential-access-key-vault.md).
