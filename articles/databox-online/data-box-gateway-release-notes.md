---
title: Notas de lançamento da Disponibilização Geral da Caixa de Dados azure Gateway Microsoft Docs
description: Descreve questões e resoluções críticas abertas para o Portal de Dados Azure que executa o lançamento geral da disponibilidade.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/26/2019
ms.author: alkohli
ms.openlocfilehash: f4ee3a5bd754335ab1c7f124671e9c37307a6a28
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79265405"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Notas de lançamento da Caixa de Dados Azure Edge/Azure Data Box Gateway

## <a name="overview"></a>Descrição geral

As seguintes notas de lançamento identificam as questões críticas em aberto e as questões resolvidas para a libertação da Disponibilidade Geral (GA) para o Azure Data Box Edge e o Azure Data Box Gateway.

As notas de lançamento são continuamente atualizadas e, à medida que são descobertas questões críticas que exigem uma suver, são adicionadas. Antes de implementar o seu Portal de Data Box Edge/Data Box, reveja cuidadosamente as informações contidas nas notas de lançamento.

A versão GA corresponde às versões do software:

- **Data Box Gateway 1903 (1.5.814.447)**
- **Borda da caixa de dados 1903 (1.5.814.447)**


## <a name="whats-new"></a>Novidades

- **Novas imagens** de disco virtual - New VHDX e VMDK estão agora disponíveis no portal Azure. Descarregue estas imagens para fornecer, configurar e implementar novos dispositivos Data Box Gateway GA. Os dispositivos Data Box Gateway criados nas versões de pré-visualização anteriores não podem ser atualizados para esta versão. Para mais informações, vá à [Prepare-se para implementar o Portal](data-box-gateway-deploy-prep.md)da Caixa de Dados Do Azure .
- **Suporte NFS** - Suporte NFS está atualmente em pré-visualização e disponível para clientes v3.0 e v4.1 que acedem aos dispositivos Data Box Edge e Data Box Gateway.
- **Resiliência de armazenamento** - O dispositivo Data Box Edge pode resistir à falha de um disco de dados com a funcionalidade de resiliência do Armazenamento. Esta funcionalidade encontra-se em pré-visualização. Pode ativar a resiliência do armazenamento selecionando a opção **Resilient** nas **definições** de Armazenamento na UI web local.


## <a name="known-issues-in-ga-release"></a>Questões conhecidas no lançamento da GA

A tabela seguinte fornece um resumo de questões conhecidas para o lançamento em execução data Box Gateway.

| Não. | Funcionalidade | Problema | Seleção/comentários |
| --- | --- | --- | --- |
| **1.** |Tipos de ficheiros | Os seguintes tipos de ficheiros não são suportados: ficheiros de caracteres, ficheiros de blocos, tomadas, tubos, links simbólicos.  |Copiar estes ficheiros resulta na criação de ficheiros de 0 comprimentos na parte NFS. Estes ficheiros permanecem num estado de erro e também são reportados em *erro.xml*. <br> Ligações simbólicas aos diretórios resultam em diretórios nunca serem marcados offline. Como resultado, pode não ver a cruz cinzenta nos diretórios que indica que os diretórios estão offline e todo o conteúdo associado foi completamente enviado para o Azure. |
| **2.** |Eliminação | Devido a um bug nesta versão, se uma parte nFS for eliminada, então a parte não pode ser eliminada. O estado da partilha mostrará *a deleting*.  |Isto ocorre apenas quando a parte está usando um nome de ficheiro não suportado. |
| **3.** |Copiar | A cópia de dados falha com o Error: A operação solicitada não pôde ser concluída devido a uma limitação do sistema de ficheiros.  |O Fluxo de Dados Alternativos (ADS) associado ao tamanho do ficheiro superior a 128 KB não é suportado.   |


## <a name="next-steps"></a>Passos Seguintes

- [Prepare-se para implementar o Portal](data-box-gateway-deploy-prep.md)da Caixa de Dados Azure .
- [Prepare-se para implantar o Edge da Caixa](data-box-edge-deploy-prep.md)de Dados Azure .
