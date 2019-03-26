---
title: Notas de versão de disponibilidade de geral do Gateway de caixa de dados do Azure | Documentos da Microsoft
description: Descreve problemas em aberto críticos e resoluções para o Gateway de caixa de dados do Azure a executar a versão de disponibilidade geral.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 3f3e07d745d83041afc9dafd64678a3ac4a65012
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418037"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Notas de versão de disponibilidade de dados de caixa Edge/Azure dados caixa Gateway geral do Azure

## <a name="overview"></a>Descrição geral

As seguintes notas de versão identificam os problemas em aberto críticos e os problemas resolvidos para disponibilidade geral (GA) de versão do Edge de caixa de dados do Azure e o Gateway de caixa de dados do Azure.

As notas de versão são atualizadas continuamente e, à medida que são descobertos problemas críticos que requerem uma solução, eles são adicionados. Antes de implementar o Gateway de caixa do Edge/dados de caixa de dados, reveja com atenção as informações contidas nas notas de versão.

Corresponde a versão de disponibilidade geral para as versões de software:

- **Gateway de caixa de dados 1903 (1.5.810.441)**
- **Dados caixa Edge 1903 (1.5.810.441)**


## <a name="whats-new"></a>Novidades

- **Novas imagens de disco virtual** -novo VHDX e VMDK estão agora disponíveis no portal do Azure. Baixe estas imagens para aprovisionar, configurar e implementar novos dispositivos de Gateway de caixa de dados GA. Os dispositivos de Gateway de caixa de dados criados na pré-visualização do anterior versões não podem ser atualizadas para esta versão. Para obter mais informações, aceda a [preparar a implementação de Gateway de caixa de dados do Azure](data-box-gateway-deploy-prep.md).
- **Suporte de NFS** -suporte NFS está atualmente em pré-visualização e disponível para v3.0 e v4.1 clientes que acedem os dispositivos Edge de caixa de dados e dados de caixa de Gateway.
- **Resiliência de armazenamento** -dispositivo de limite de caixa de dados Your possa suportar a falha de um disco de dados com a funcionalidade de resiliência de armazenamento. Esta funcionalidade encontra-se em pré-visualização. Pode ativar a resiliência de armazenamento ao selecionar o **resilientes** opção a **as definições de armazenamento** locais da interface do Usuário da web.


## <a name="known-issues-in-ga-release"></a>Problemas conhecidos da versão GA

A tabela seguinte fornece um resumo dos problemas conhecidos para o Gateway de caixa de dados a executar a versão.

| Não. | Funcionalidade | Problema | Solução ou enviar comentários |
| --- | --- | --- | --- |
| **1.** |Tipos de ficheiro | Não são suportados os seguintes tipos de ficheiro: arquivos, os ficheiros de bloco, soquetes, pipes, links simbólicos de caracteres.  |Copiar esses arquivos partilham resultados nos ficheiros de comprimento 0, que são criados no NFS. Estes ficheiros permanecem no estado de erro e também são apresentados na *error.xml*. <br> Links simbólicos aos diretórios resultam em diretórios nunca introdução marcada como offline. Como resultado, não poderá ver em cinzento vários nos diretórios que indica que os diretórios estão offline e todo o conteúdo associado foi completamente carregado para o Azure. |
| **2.** |Eliminação | Devido a um erro nesta versão, se eliminar uma partilha NFS, em seguida, a partilha não pode ser eliminada. Apresenta o estado de partilha *a eliminação*.  |Isto ocorre apenas quando a partilha está a utilizar um nome de ficheiro não suportado. |
| **3.** |Copiar | Cópia de dados pode falhar com o erro:  Não foi possível concluir a operação pedida devido a uma limitação do sistema de ficheiros.  |A alternativa dados Stream (ADS) associado a mais de 128 KB de tamanho de ficheiro não é suportada.   |


## <a name="next-steps"></a>Passos Seguintes

- [Preparar a implementação de Gateway de caixa de dados do Azure](data-box-gateway-deploy-prep.md).
- [Preparar a implementação de borda de caixa de dados do Azure](data-box-edge-deploy-prep.md).
