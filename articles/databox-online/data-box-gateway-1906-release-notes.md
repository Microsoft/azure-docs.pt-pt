---
title: Azure Data Box Gateway & Notas de lançamento da Caixa de Dados Azure Edge 1906 Microsoft Docs
description: Descreve questões e resoluções críticas abertas para o Portal de Dados Azure e o Lançamento da Caixa de Dados Azure em 1906.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/18/2019
ms.author: alkohli
ms.openlocfilehash: 69c905176a5beb1c7d442cd5f1a69161cd520c5a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "71099489"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1906-release-notes"></a>Notas de lançamento da Caixa de Dados Azure Edge e Azure Data Box Gateway 1906

As seguintes notas de lançamento identificam as questões críticas em aberto e as questões resolvidas para o lançamento de 1906 para Azure Data Box Edge e Azure Data Box Gateway.

As notas de lançamento são continuamente atualizadas e, à medida que são descobertas questões críticas que exigem uma suver, são adicionadas. Antes de implementar o seu Portal de Data Box Edge/Data Box, reveja cuidadosamente as informações contidas nas notas de lançamento.

Esta versão corresponde às versões do software:

- **Data Box Gateway 1906 (1.6.978.743)**
- **Borda da caixa de dados 1906 (1.6.978.743)**

> [!NOTE]
> A atualização 1906 só pode ser aplicada aos dispositivos Data Box Edge que estejam a executar a disponibilidade geral (GA) ou versão de 1905 do software.

## <a name="whats-new"></a>Novidades

- Correção de bugs no fluxo de trabalho de **gestão da chave** de recuperação - No lançamento anterior, houve um bug devido ao qual a chave de recuperação não estava a ser aplicada. Este bug está fixado nesta libertação. Recomendamos vivamente que aplique esta atualização, uma vez que a chave de recuperação permite recuperar os dados do dispositivo, caso o dispositivo não arranque. Para mais informações, consulte como guardar a chave de [recuperação ao implementar](data-box-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device)data Box Edge ou Data Box Gateway .
- Foram efetuadas melhorias de registo de **portaprogramáveis de campo (FPGA)** - Foram efetuadas melhorias de lançamento, registo e alerta de 1905 relacionadas com a FPGA. Esta continua a ser uma atualização necessária para data box edge se estiver a utilizar a funcionalidade de computação Edge com a FPGA. Para mais informações, consulte como [transformar dados com a computação Edge na sua Data Box Edge](data-box-edge-deploy-configure-compute-advanced.md).

## <a name="known-issues-in-ga-release"></a>Questões conhecidas no lançamento da GA

Não são divulgados novos problemas para esta libertação. Todas as questões de lançamento foram transmitidas dos lançamentos anteriores. Para ver uma lista de questões conhecidas, vá a [questões conhecidas no lançamento da AG](data-box-gateway-release-notes.md#known-issues-in-ga-release).


## <a name="next-steps"></a>Passos seguintes

- [Preparar para implementar o Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
- [Preparar para implementar o Azure Data Box Edge](data-box-edge-deploy-prep.md)
