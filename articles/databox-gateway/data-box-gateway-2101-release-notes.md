---
title: Notas de lançamento do Azure Data Box Gateway 2101| Microsoft Docs
description: Descreve questões e resoluções abertas críticas para o Gateway Azure Data Box que executa a versão 2101.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/29/2021
ms.author: alkohli
ms.openlocfilehash: 510f2677673363791ab5eb0f2c4dbcd25b697934
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072731"
---
# <a name="azure-data-box-gateway-2101-release-notes"></a>Notas de lançamento do Azure Data Box Gateway 2101

As seguintes notas de lançamento identificam as questões críticas em aberto e as questões resolvidas para a libertação de 2101 do Azure Data Box Gateway.

As notas de lançamento são continuamente atualizadas. À medida que são descobertas questões críticas que requerem uma solução alternativa, são adicionadas. Antes de implementar o seu Gateway Azure Data Box, reveja cuidadosamente as informações nas notas de lançamento.  

Esta versão corresponde às versões de software:

- **Porta de dados Gateway 2101 (1.6.1475.2528)** - KB 4603462

> [!NOTE]
> A atualização 2101 só pode ser aplicada a todos os dispositivos que estejam a executar versões gerais de disponibilidade (GA) do software ou posteriores.

## <a name="whats-new"></a>Novidades

Esta versão contém a seguinte correção de erro:

- **Problema de upload** - Este lançamento corrige um problema de upload onde o upload recomeça devido a falhas pode abrandar a taxa de conclusão do upload. Este problema pode ocorrer ao carregar um conjunto de dados que consiste principalmente em ficheiros de grande dimensão em relação à largura de banda disponível, particularmente, mas não se limitando a, quando o estrangulamento da largura de banda está ativo. Esta alteração garante oportunidade suficiente para a conclusão do upload antes de reiniciar o upload para um determinado ficheiro.

Esta versão também contém as seguintes atualizações:

- Todas as atualizações cumulativas do Windows e atualizações de quadros .NET lançadas até outubro de 2020.
- O endereço IP estático do Azure Data Box Gateway é mantido através de atualizações de software.

## <a name="known-issues-in-this-release"></a>Questões conhecidas neste lançamento

Não são divulgados novos problemas para este lançamento. Todas as questões notadas pela libertação foram transitadas das versões anteriores. Para ver uma lista de questões conhecidas, vá a [questões conhecidas no lançamento da AG](data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Passos seguintes

- [Preparar para implementar o Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
