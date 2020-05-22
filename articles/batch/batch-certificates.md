---
title: Utilização de certificados - Lote Azure
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
ms.openlocfilehash: 4da5fad63b148fa054eefb7f13424b46dc43bf29
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83764339"
---
# <a name="using-certificates-with-batch"></a>Utilização de certificados com Lote

Atualmente, a principal razão para usar certificados com Lote é se você tem aplicações em execução em Pools que precisam autenticar com um ponto final. 

Se ainda não tiver um certificado, pode criar um certificado auto-assinado utilizando a `makecert` ferramenta linha de comando.

## <a name="upload-certificates-manually-through-the-azure-portal"></a>Faça upload de certificados manualmente através do portal Azure

1. A partir da conta 'Lote' pretende fazer o upload de um certificado, selecionar **Certificados** e, em seguida, selecionar **Adicionar**. 

2. Faça upload do certificado com uma extensão .pfx ou .cer. 

Uma vez carregado, o certificado é adicionado a uma lista de certificados, e pode verificar a impressão digital.

Agora, quando criar uma piscina de Lote, pode navegar para Certificados dentro da piscina e atribuir o certificado que carregou para essa piscina.

## <a name="next-steps"></a>Passos seguintes

Lote tem um certificado API, [certificado de lote AZ criar](https://docs.microsoft.com/cli/azure/batch/certificate?view=azure-cli-latest#az-batch-certificate-create)

Para obter informações sobre a utilização do Cofre de Chaves, consulte o cofre de [chaves de acesso seguro com lote](credential-access-key-vault.md).
