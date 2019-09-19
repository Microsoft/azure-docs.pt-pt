---
title: Notas de versão do Gateway do Azure Data Box & Azure Data Box Edge 1906 | Microsoft Docs
description: Descreve os problemas e as resoluções críticas em aberto para o Gateway do Azure Data Box e Azure Data Box Edge a versão 1906 em execução.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/18/2019
ms.author: alkohli
ms.openlocfilehash: 69c905176a5beb1c7d442cd5f1a69161cd520c5a
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71099489"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1906-release-notes"></a>Notas de versão do Azure Data Box Edge e Gateway do Azure Data Box 1906

As notas de versão a seguir identificam os problemas críticos abertos e os problemas resolvidos para a versão 1906 para Azure Data Box Edge e Gateway do Azure Data Box.

As notas de versão são atualizadas continuamente, e como problemas críticos que exigem uma solução alternativa são descobertos, eles são adicionados. Antes de implantar seu Data Box Edge/Gateway do Data Box, examine atentamente as informações contidas nas notas de versão.

Esta versão corresponde às versões de software:

- **Gateway do Data Box 1906 (1.6.978.743)**
- **Data Box Edge 1906 (1.6.978.743)**

> [!NOTE]
> A atualização 1906 pode ser aplicada somente a dispositivos Data Box Edge que estejam executando a versão de disponibilidade geral (GA) ou 1905 do software.

## <a name="whats-new"></a>Novidades

- **Correção de bug no fluxo de trabalho de gerenciamento de chaves de recuperação** – na versão anterior, houve um bug devido ao qual a chave de recuperação não estava sendo aplicada. Esse bug foi corrigido nesta versão. É altamente recomendável que você aplique essa atualização, pois a chave de recuperação permite recuperar os dados no dispositivo, caso o dispositivo não inicialize. Para obter mais informações, consulte como [salvar a chave de recuperação ao implantar data Box Edge ou gateway do data Box](data-box-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device).
- **Melhorias no log de FPGA (matriz de portão) de campo** – iniciando a versão 1905, os aprimoramentos de log e alertas relacionados ao FPGA foram feitos. Isso continuará a ser uma atualização necessária para Data Box Edge se você estiver usando o recurso de computação de borda com o FPGA. Para obter mais informações, consulte como [transformar dados com a computação de borda em seu data Box Edge](data-box-edge-deploy-configure-compute-advanced.md).

## <a name="known-issues-in-ga-release"></a>Problemas conhecidos na versão GA

Não foram observados novos problemas para esta versão. Toda a versão observou que os problemas foram transferidos das versões anteriores. Para ver uma lista de problemas conhecidos, vá para [problemas conhecidos na versão GA](data-box-gateway-release-notes.md#known-issues-in-ga-release).


## <a name="next-steps"></a>Passos seguintes

- [Preparar para implementar o Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
- [Preparar para implementar o Azure Data Box Edge](data-box-edge-deploy-prep.md)
