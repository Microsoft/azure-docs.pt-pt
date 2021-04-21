---
title: Políticas de tiering de nuvem de arquivo Azure Sync | Microsoft Docs
description: Detalhes sobre como as políticas de espaço livre de data e volume funcionam em conjunto para diferentes cenários.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4376a57b677b95b2de7d261b30ac4c0ad24956cc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797365"
---
# <a name="cloud-tiering-policies"></a>Políticas de nivelamento em nuvem

O tiering em nuvem tem duas políticas que determinam quais os ficheiros que estão nivelados para a nuvem: a **política de espaço livre** de volume e a política de **datas**.

A **política de espaço livre** de volume garante que uma percentagem especificada do volume local em que o ponto final do servidor está localizado é sempre mantido livre. 

Os ficheiros de níveis de **data** foram acedidos pela última vez a x dias ou mais tarde. A política de espaço livre de volume terá sempre precedência; quando não houver espaço livre suficiente no volume para armazenar tantos dias de ficheiros como descrito pela política de data, o Azure File Sync irá anular a política de data e continuar a nivelar os ficheiros mais frios até que a percentagem de espaço livre de volume seja satisfeita.

## <a name="how-both-policies-work-together"></a>Como ambas as políticas funcionam em conjunto

Vamos usar um exemplo para ilustrar como estas políticas funcionam: Digamos que configuraste o Azure File Sync num volume local de 500 GB, e o tiering de nuvens nunca foi ativado. Estes são os ficheiros da sua parte do ficheiro:

|Nome de Ficheiro |Última hora de acesso  |Tamanho do Ficheiro  |Armazenado em |
|----------|------------------|-----------|----------|
|Arquivo 1    | Há 2 dias  | 10 GB | Partilha de ficheiros server e Azure
|Arquivo 2    | há 10 dias | 30 GB | Partilha de ficheiros server e Azure
|Arquivo 3    | Há 1 ano | 200 GB | Partilha de ficheiros server e Azure
|Arquivo 4    | 1 ano, 2 dias atrás | 130 GB | Partilha de ficheiros server e Azure
|Arquivo 5    | 2 anos, 1 dia atrás | 140 GB | Partilha de ficheiros server e Azure

**Alteração 1:** Permitiu o tiering de nuvens, definiu uma política de espaço livre de volume de 20%, e manteve a política de data desativada. Com esta configuração, o tiering em nuvem garante que 20% (neste caso 100 GB) de espaço é mantido livre e disponível na máquina local. Como resultado, a capacidade total da cache local é de 400 GB. Que 400 GB irá armazenar os ficheiros mais recentemente e frequentemente acedidos sobre o volume local.

Com esta configuração, apenas os ficheiros 1 a 4 seriam armazenados na cache local, e o ficheiro 5 seria hierárquico. Isto é apenas 370 GB dos 400 GB que poderiam ser usados. O ficheiro 5 é de 140 GB e excederia o limite de 400 GB se fosse em cache local. 

**Alteração 2:** Digamos que um utilizador acede ao ficheiro 5. Isto faz do ficheiro 5 o ficheiro mais recentemente acedido na partilha. Como resultado, o Ficheiro 5 seria armazenado na cache local e para caber abaixo do limite de 400 GB, o ficheiro 4 seria hierárquico. A tabela a seguir mostra onde os ficheiros são armazenados, com estas atualizações:

|Nome de Ficheiro |Última hora de acesso  |Tamanho do Ficheiro  |Armazenado em |
|----------|------------------|-----------|----------|
|Arquivo 5    | Há 2 horas | 140 GB | Partilha de ficheiros server e Azure
|Arquivo 1    | Há 2 dias  | 10 GB | Partilha de ficheiros server e Azure
|Arquivo 2    | há 10 dias | 30 GB | Partilha de ficheiros server e Azure
|Arquivo 3    | Há 1 ano | 200 GB | Partilha de ficheiros server e Azure
|Arquivo 4    | 1 ano, 2 dias atrás | 130 GB | Azure file share, tiered localmente

**Alteração 3:** Digamos que atualizou as políticas para que a política de tiering baseada em datas seja de 60 dias e a política de espaço livre de volume seja de 70%. Agora, apenas até 150 GB podem ser armazenados na cache local. Embora o Ficheiro 2 tenha sido acedido há menos de 60 dias, a política de espaço livre de volume irá anular a política de data, e o ficheiro 2 está hierárquico para manter o espaço livre local de 70%.

**Alteração 4:** Se mudasse a política de espaço livre de volume para 20% e depois `Invoke-StorageSyncFileRecall` se lembrasse de todos os ficheiros que se encaixam na unidade local enquanto adere às políticas de nivelamento da nuvem, a tabela seria assim:

|Nome de Ficheiro |Última hora de acesso  |Tamanho do Ficheiro  |Armazenado em |
|----------|------------------|-----------|----------|
|Arquivo 5    | Há uma hora  | 140 GB | Partilha de ficheiros server e Azure
|Arquivo 1    | Há 2 dias  | 10 GB | Partilha de ficheiros server e Azure
|Arquivo 2    | há 10 dias | 30 GB | Partilha de ficheiros server e Azure
|Arquivo 3    | Há 1 ano | 200 GB | Azure file share, tiered localmente
|Arquivo 4    | 1 ano, 2 dias atrás | 130 GB | Azure file share, tiered localmente

Neste caso, os ficheiros 1, 2 e 5 seriam localmente cached e os ficheiros 3 e 4 seriam hierárquicos. Como a política da data é de 60 dias, os ficheiros 3 e 4 são hierárquicos, mesmo que a política de espaço livre de volume permita até 400 GB localmente.

> [!NOTE] 
> Os ficheiros não são automaticamente recolhidos quando os clientes mudam a política de espaço livre de volume para um valor menor (por exemplo, de 20% para 10%) ou alterar a política da data para um valor maior (por exemplo, de 20 dias para 50 dias).

## <a name="multiple-server-endpoints-on-a-local-volume"></a>Vários pontos finais do servidor num volume local

O tiering da nuvem pode ser ativado para vários pontos finais do servidor num único volume local. Para esta configuração, deverá definir o espaço livre de volume na mesma quantidade para todos os pontos finais do servidor no mesmo volume. Se definir diferentes políticas de espaço livre de volume para vários pontos finais do servidor no mesmo volume, a maior percentagem de espaço livre de volume terá precedência. Isto chama-se a política efetiva de espaço livre de **volume.** Por exemplo, se tiver três pontos finais do servidor no mesmo volume local, um definido para 15%, outro definido para 20%, e um terceiro definido para 30%, todos começarão a nivelar os ficheiros mais frios quando tiverem menos de 30% de espaço livre disponível.

## <a name="next-steps"></a>Passos seguintes

* [Monitorizar o nível da nuvem](file-sync-monitor-cloud-tiering.md)
