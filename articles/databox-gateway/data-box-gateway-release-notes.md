---
title: Azure Data Box Gateway Geral Disponibilidade nota de lançamento Microsoft Docs
description: Descreve questões e resoluções abertas críticas para o Gateway Azure Data Box com lançamento geral de disponibilidade.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 2984f7990b9570c5ec57633de7f7e50162fb6f46
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96583017"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Azure Data Box Edge/Azure Data Box Gateway Notas de lançamento geral de disponibilidade

## <a name="overview"></a>Descrição geral

As seguintes notas de lançamento identificam as questões abertas críticas e os problemas resolvidos para a libertação de Disponibilidade Geral (GA) para Azure Data Box Edge e Azure Data Box Gateway. 

As notas de lançamento são continuamente atualizadas, e à medida que são descobertas questões críticas que requerem uma solução alternativa, são adicionadas. Antes de implementar o seu Data Box Edge/Data Box Gateway, reveja cuidadosamente as informações contidas nas notas de libertação.

A versão GA corresponde às versões de software:

- **Porta de caixa de dados 1903 (1.5.814.447)**
- **Borda da Caixa de Dados 1903 (1.5.814.447)**


## <a name="whats-new"></a>Novidades

- **Novas imagens de disco virtual** - Novos VHDX e VMDK já estão disponíveis no portal Azure. Descarregue estas imagens para provisões, configurar e implementar novos dispositivos Data Box Gateway GA. Os dispositivos Data Box Gateway criados nas versões de pré-visualização anteriores não podem ser atualizados para esta versão. Para mais informações, vá a [Preparar-se para implementar o Gateway da Caixa de Dados Azure](data-box-gateway-deploy-prep.md).
- **Suporte NFS** - O suporte NFS está atualmente em pré-visualização e disponível para clientes v3.0 e v4.1 que acedem aos dispositivos Data Box Edge e Data Box Gateway.
- **Resiliência de armazenamento** - O seu dispositivo Data Box Edge pode resistir à falha de um disco de dados com a funcionalidade de resiliência de Armazenamento. Esta funcionalidade encontra-se em pré-visualização. Pode ativar a resiliência do armazenamento selecionando a opção **Resilient** nas **definições de Armazenamento** na UI web local.


## <a name="known-issues-in-ga-release"></a>Questões conhecidas no lançamento da GA

A tabela seguinte fornece um resumo de problemas conhecidos para a versão de execução do Data Box Gateway.

| Não. | Funcionalidade | Problema | Soluções/comentários |
| --- | --- | --- | --- |
| **1.** |Tipos de ficheiros | Os seguintes tipos de ficheiros não são suportados: ficheiros de caracteres, ficheiros de blocos, tomadas, tubos, ligações simbólicas.  |Copiar estes ficheiros resulta na criação de ficheiros de 0 comprimentos na partilha NFS. Estes ficheiros permanecem em estado de erro e também são reportados em *error.xml*. <br> Ligações simbólicas a diretórios resultam em diretórios nunca ficarem marcados offline. Como resultado, pode não ver a cruz cinzenta nos diretórios que indica que os diretórios estão offline e todo o conteúdo associado foi completamente enviado para a Azure. |
| **2.** |Eliminação | Devido a um erro nesta versão, se uma parte NFS for eliminada, então a parte não pode ser eliminada. O estado da partilha mostrará *a Eliminação*.  |Isto ocorre apenas quando a ação está usando um nome de ficheiro não suportado. |
| **3.** |Copiar | A cópia de dados falha com Erro: A operação solicitada não pôde ser concluída devido a uma limitação do sistema de ficheiros.  |O Fluxo de Dados Alternativo (ADS) associado ao tamanho do ficheiro superior a 128 KB não é suportado.   |


## <a name="next-steps"></a>Passos seguintes

- [Prepare-se para implantar o Gateway da Caixa de Dados Azure](data-box-gateway-deploy-prep.md).
- [Prepare-se para implantar a borda da caixa de dados Azure](../databox-online/azure-stack-edge-deploy-prep.md).
