---
title: Histórico de lançamento de versões
titleSuffix: Microsoft Genomics
description: O histórico de lançamentos de atualizações para o cliente Microsoft Genomics Python para correções e novas funcionalidades.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 01/11/2019
ms.openlocfilehash: 20475e2cde1b42790740889cf341b3a0a0afccc0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76991087"
---
# <a name="version-release-history"></a>Histórico de lançamento de versões
A equipa da Microsoft Genomics atualiza regularmente o cliente Microsoft Genomics Python para correções e novas funcionalidades. 

## <a name="latest-release"></a>Última versão
O cliente python atual é a versão 0.9.0. Foi lançado a 6 de fevereiro de 2019 e suporta a execução de fluxos de trabalho com GATK 3.5 e GATK4. Suporta a saída gVCF e pode aceitar um argumento opcional para a compressão da saída.


## <a name="release-history"></a>História do lançamento 
Novas versões do cliente Microsoft Genomics Python são lançadas cerca de uma vez por ano. À medida que novas versões do cliente Microsoft Genomics Python são lançadas, uma lista de correções e funcionalidades é atualizada aqui. Quando as novas versões forem lançadas, as versões anteriores devem continuar a ser suportadas durante pelo menos 90 dias. Quando as versões anteriores deixarem de ser suportadas, será indicado nesta página. 

### <a name="version-090"></a>Versão 0.9.0
A versão 0.9.0 inclui suporte para compressão de saída. Isto equivale a correr `-bgzip` seguido por `-tabix` vcf ou gvcf output. Para mais informações, consulte [perguntas frequentes.](frequently-asked-questions-genomics.md) 

### <a name="version-081"></a>Versão 0.8.1
A versão 0.8.1 inclui pequenas correções de erros.  

### <a name="version-080"></a>Versão 0.8.0
A versão 0.8.0 inclui suporte para GATK4 e gVCFs de saída.  

### <a name="version-074"></a>Versão 0.7.4
A versão 0.7.4 inclui suporte para a aceitação de fichas SAS em vez de chaves de conta na `config.txt` entrada. Para obter mais informações, consulte [o quickstart input SAS](quickstart-input-sas.md). 

### <a name="version-073"></a>Versão 0.7.3
A versão 0.7.3 inclui pequenas correções de erros.

### <a name="version-072"></a>Versão 0.7.2
A versão 0.7.2 é a versão inicial. Foi lançado a 1 de novembro de 2017.
