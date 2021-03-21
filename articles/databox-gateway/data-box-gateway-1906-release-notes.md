---
title: Azure Data Box Gateway & notas de lançamento Azure Data Box Edge 1906| Microsoft Docs
description: Descreve questões e resoluções abertas críticas para o Gateway da Caixa de Dados Azure e para o Azure Data Box Edge em execução de 1906.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 51f4995ffd6a86022d95df15ae0eb7694d878c60
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96582837"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1906-release-notes"></a>Azure Data Box Edge e Azure Data Box Gateway 1906 notas de lançamento

As seguintes notas de lançamento identificam as questões abertas críticas e os problemas resolvidos para a versão de 1906 para Azure Data Box Edge e Azure Data Box Gateway. 

As notas de lançamento são continuamente atualizadas, e à medida que são descobertas questões críticas que requerem uma solução alternativa, são adicionadas. Antes de implementar o seu Data Box Edge/Data Box Gateway, reveja cuidadosamente as informações contidas nas notas de libertação.

Esta versão corresponde às versões de software:

- **Porta de dados Gateway 1906 (1.6.978.743)**
- **Borda da Caixa de Dados 1906 (1.6.978.743)**

> [!NOTE]
> A atualização 1906 só pode ser aplicada em dispositivos Data Box Edge que estejam a executar a disponibilidade geral (GA) ou a versão de 1905 do software.

## <a name="whats-new"></a>Novidades

- **Correção de bugs no fluxo de trabalho de gestão da chave de recuperação** - No lançamento anterior, houve um bug devido ao qual a chave de recuperação não estava a ser aplicada. Este bug está fixo nesta versão. Recomendamos vivamente que aplique esta atualização, uma vez que a chave de recuperação permite recuperar os dados do dispositivo, caso o dispositivo não seja iniciado. Para obter mais informações, consulte como guardar a chave de recuperação ao [implementar a Borda da Caixa de Dados ou o Gateway da Caixa de Dados](../databox-online/azure-stack-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device).
- **Field Programmable Gate Array (FPGA) registando melhorias** - A partir de 1905 foram feitas melhorias de lançamento, registo e alerta relacionadas com a FPGA. Esta continua a ser uma atualização necessária para data box edge se estiver a utilizar a função de computação Edge com a FPGA. Para obter mais informações, consulte como [transformar dados com o edge compute no seu Data Box Edge](../databox-online/azure-stack-edge-deploy-configure-compute-advanced.md).

## <a name="known-issues-in-ga-release"></a>Questões conhecidas no lançamento da GA

Não são divulgados novos problemas para este lançamento. Todas as questões notadas pela libertação foram transitadas das versões anteriores. Para ver uma lista de questões conhecidas, vá a [questões conhecidas no lançamento da AG](data-box-gateway-release-notes.md#known-issues-in-ga-release).


## <a name="next-steps"></a>Passos seguintes

- [Preparar para implementar o Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
- [Preparar para implementar o Azure Data Box Edge](../databox-online/azure-stack-edge-deploy-prep.md)
