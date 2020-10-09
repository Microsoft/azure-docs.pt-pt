---
title: 'Referência: Questões conhecidas & resolução de problemas'
titleSuffix: Azure Data Science Virtual  Machine
description: Obtenha uma lista dos problemas conhecidos, soluções alternativas e resolução de problemas para a Azure Data Science Virtual Machine
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: b83effa95b17d712d4019f8ab5bf13c4f02a7d2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "78206525"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Problemas conhecidos e resolução de problemas da Máquina Virtual Azure Data Science

Este artigo ajuda-o a encontrar e corrigir erros ou falhas que poderá encontrar ao utilizar a Máquina Virtual Azure Data Science.

## <a name="python-package-installation-issues"></a>Problemas de instalação de pacote python

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Instalar pacotes com pip quebra dependências no Linux

Utilize `sudo pip install` em vez de instalar `pip install` pacotes.

## <a name="disk-encryption-issues"></a>Problemas de encriptação de discos

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>A encriptação do disco falha no Ubuntu DSVM

A encriptação do disco Azure (ADE) não é atualmente suportada no Ubuntu DSVM. Como uma solução alternativa, considere configurar a [encriptação do lado do servidor dos discos geridos pelo Azure](../../virtual-machines/windows/disk-encryption.md).

## <a name="tool-appears-disabled"></a>A ferramenta aparece desativada

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hiper-V não funciona no Windows DSVM

O hiper-V inicialmente não funciona no Windows é um comportamento esperado. Para o desempenho do boot, desativamos alguns serviços. Para ativar o Hiper-V:

1. Abra a barra de pesquisa no seu Windows DSVM
1. Digite em "Serviços",
1. Definir todos os serviços de Hiper-V para "Manual"
1. Desconte "Hyper-V Virtual Machine Management" para "Automatic"

O seu ecrã final deve ser assim:

   ![Ativar Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

