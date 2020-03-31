---
title: Histórico de versões
titleSuffix: Microsoft Genomics
description: O histórico de lançamento de atualizações para o cliente da Microsoft Genomics Python para correções e novas funcionalidades.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 01/11/2019
ms.openlocfilehash: 20475e2cde1b42790740889cf341b3a0a0afccc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76991087"
---
# <a name="version-release-history"></a>Histórico de versões
A equipa da Microsoft Genomics atualiza regularmente o cliente da Microsoft Genomics Python para correções e novas funcionalidades. 

## <a name="latest-release"></a>Última versão
O cliente python atual é a versão 0.9.0. Foi lançado a 6 de fevereiro de 2019 e suporta fluxos de trabalho com GATK 3.5 e GATK4. Suporta a saída de gVCF e pode aceitar um argumento opcional para a compressão da saída.


## <a name="release-history"></a>História de lançamento 
Novas versões do cliente da Microsoft Genomics Python são lançadas cerca de uma vez por ano. À medida que são lançadas novas versões do cliente da Microsoft Genomics Python, uma lista de correções e funcionalidades é atualizada aqui. Quando novas versões forem lançadas, as versões anteriores devem continuar a ser suportadas durante pelo menos 90 dias. Quando as versões anteriores já não forem suportadas, será indicada nesta página. 

### <a name="version-090"></a>Versão 0.9.0
A versão 0.9.0 inclui suporte para a compressão da saída. Isto equivale a `-bgzip` funcionamento seguido de saída `-tabix` vcf ou gvcf. Para mais informações, consulte [frequentemente perguntas](frequently-asked-questions-genomics.md). 

### <a name="version-081"></a>Versão 0.8.1
A versão 0.8.1 inclui pequenas correções de erros.  

### <a name="version-080"></a>Versão 0.8.0
A versão 0.8.0 inclui suporte para GATK4 e gVCFs de saída.  

### <a name="version-074"></a>Versão 0.7.4
A versão 0.7.4 inclui suporte para aceitar tokens `config.txt` SAS em vez de chaves de conta na entrada. Para mais informações, consulte [input SAS tokens quickstart](quickstart-input-sas.md). 

### <a name="version-073"></a>Versão 0.7.3
A versão 0.7.3 inclui pequenas correções de erros.

### <a name="version-072"></a>Versão 0.7.2
A versão 0.7.2 é a versão inicial. Foi lançado a 1 de novembro de 2017.
