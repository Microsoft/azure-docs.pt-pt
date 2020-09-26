---
title: Resolução de problemas de políticas instantâneas para ficheiros Azure NetApp Microsoft Docs
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
ms.openlocfilehash: 1d7a91de8fa505fe4c2b06eea59f3acc2ae1f7e8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91345497"
---
# <a name="troubleshoot-snapshot-policies"></a>Políticas de instantâneo de resolução de problemas

Este artigo descreve cenários de erro que poderá encontrar ao gerir as políticas de snapshot do Azure NetApp Files. Também fornece soluções que podem ajudá-lo a resolver os problemas.

## <a name="snapshot-policy-creation-failing-with-invalid-snapshot-policy-name"></a>Criação de política instantânea falhando com nome de política de instantâneo inválido

Um erro ocorre durante a criação de políticas instantâneas se o seu nome de política instantâneo for inválido. As seguintes diretrizes aplicam-se aos nomes das políticas instantâneas:  

* O nome da política instantânea não pode conter caracteres não ASCII ou caracteres especiais.
* O nome da política instantânea deve começar com uma letra ou um número e pode conter letras, números, sublinhar ('_') e hífens ('')apenas.
* O nome da política instantânea deve ter entre 1 e 64 caracteres.  

Deve rever o nome da política de instantâneo de acordo com as diretrizes.  

## <a name="snapshot-policy-creation-failing-with-invalid-values"></a>Criação de política instantânea falhando com valores inválidos 

O Azure NetApp Files não cria uma política de instantâneo se introduzir um valor inválido para um campo como `Number of snapshots to keep` ou `Minute on the hour to take snapshot` .  
 
Os valores válidos são os seguintes:   

* O valor deve ser um número válido.
* O valor deve ser entre 0 e 59.

Certifique-se de que é fornecido um valor válido para os campos.

## <a name="snapshot-policy-creation-failing-with-total-number-of-snapshots-to-keep-exceeds-255-error"></a>Criação de política instantânea falha com erro "Número total de instantâneos a manter excede 255" erro 

Cada volume pode ter um [máximo de 255 instantâneos](azure-netapp-files-resource-limits.md). O máximo inclui a soma de todos os instantâneos de hora, dia, semanais e mensais. Deve diminuir o `Snapshots to keep` valor e tentar de novo.

## <a name="assigning-policy-to-a-volume-failing-with-total-snapshot-policy-is-over-the-max-255-error"></a>Atribuir a política a um volume que falhe com "A política total de instantâneos está acima do erro máximo de '255'

Cada volume pode ter um [máximo de 255 instantâneos](azure-netapp-files-resource-limits.md). Quando a soma de todos os instantâneos on-demand, de hora, de hora, de dia, de dia, de semana e de mensalmente exceder o máximo, ocorre um erro. Diminua o `snapshots to keep` valor ou elimine algumas imagens a pedido e tente novamente.

## <a name="next-steps"></a>Passos seguintes  

* [Gerir políticas de instantâneo](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)
