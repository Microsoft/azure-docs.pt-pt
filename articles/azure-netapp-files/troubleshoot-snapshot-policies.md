---
title: As políticas de instantâneo de resolução de problemas para ficheiros Azure NetApp | Microsoft Docs
description: Descreve mensagens de erro e resoluções que podem ajudá-lo a resolver problemas de gestão de políticas instantâneas para ficheiros Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: 6ba8b18876bdae2754a6a772ce3909ff2f5a71b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91651009"
---
# <a name="troubleshoot-snapshot-policies"></a>Resolver problemas das políticas de instantâneo

Este artigo descreve cenários de erro que poderá encontrar ao gerir as políticas de snapshot do Azure NetApp Files. Também fornece soluções que podem ajudá-lo a resolver os problemas.

## <a name="error-conditions-and-resolutions"></a>Condições e resoluções de erros 

|     Condição de erro    |     Resolução    |
|-|-|
| A criação de políticas instantâneas falha com o nome de política de instantâneo inválido. | Um erro ocorre durante a criação de políticas instantâneas se o seu nome de política instantâneo for inválido. As seguintes diretrizes aplicam-se aos nomes das políticas instantâneas:  <ul><li> O nome da política instantânea não pode conter caracteres não ASCII ou caracteres especiais. </li> <li> O nome da política instantânea deve começar com uma letra ou um número e pode conter letras, números, sublinhar ('_') e hífens ('')apenas. </li> <li> O nome da política instantânea deve ter entre 1 e 64 caracteres.  </li></ul> Reveja o nome da política instantânea de acordo com as diretrizes.  |
| A criação de políticas instantâneas falha com valores inválidos. | O Azure NetApp Files não cria uma política de instantâneo se introduzir um valor inválido para um campo como `Number of snapshots to keep` ou `Minute on the hour to take snapshot` . Os valores válidos são os seguintes:  <ul><li>O valor deve ser um número válido.</li> <li>O valor deve ser entre 0 e 59.</li></ul> Certifique-se de que é fornecido um valor válido para os campos. | 
| A criação de políticas instantâneas falha com o erro `Total number of snapshots to keep exceeds 255` . | Cada volume pode ter um [máximo de 255 instantâneos](azure-netapp-files-resource-limits.md). O máximo inclui a soma de todos os instantâneos de hora, dia, semanais e mensais. <br> Diminua o `Snapshots to keep` valor e tente novamente. |
| Atribuir a política a um volume falha com o erro `Total snapshot policy is over the max '255'` . | Cada volume pode ter um [máximo de 255 instantâneos](azure-netapp-files-resource-limits.md). Quando a soma de todos os instantâneos on-demand, de hora, de hora, de dia, de dia, de semana e de mensalmente exceder o máximo, ocorre um erro. <br> Diminua o `snapshots to keep` valor ou elimine algumas imagens a pedido e tente novamente. | 

## <a name="next-steps"></a>Passos seguintes  

* [Gerir políticas de instantâneo](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)
