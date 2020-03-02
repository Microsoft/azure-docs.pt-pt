---
title: 'Referência: Questões conhecidas e resolução de problemas'
titleSuffix: Azure Data Science Virtual  Machine
description: Obtenha uma lista dos problemas conhecidos, saroundaround sques e resolução de problemas para A Máquina Virtual de Ciência de Dados Azure
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: b83effa95b17d712d4019f8ab5bf13c4f02a7d2b
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206525"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Questões conhecidas e resolução de problemas na Máquina Virtual de Ciência de Dados Azure

Este artigo ajuda-o a encontrar e corrigir erros ou falhas que poderá encontrar ao utilizar a Máquina Virtual de Ciência de Dados Azure.

## <a name="python-package-installation-issues"></a>Problemas de instalação de pacotes Python

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Instalar pacotes com pip quebra dependências no Linux

Utilize `sudo pip install` em vez de `pip install` ao instalar pacotes.

## <a name="disk-encryption-issues"></a>Problemas de encriptação de discos

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Encriptação de disco falha no Ubuntu DSVM

A Encriptação do Disco Azure (ADE) não é atualmente suportada no Ubuntu DSVM. Como uma suposição, considere configurar a [encriptação do lado do servidor dos discos geridos pelo Azure](../../virtual-machines/windows/disk-encryption.md).

## <a name="tool-appears-disabled"></a>A ferramenta parece desativada

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-V não funciona no Windows DSVM

Que o Hyper-V inicialmente não funcione no Windows é um comportamento esperado. Para o desempenho do arranque, desativamos alguns serviços. Para ativar o Hyper-V:

1. Abra a barra de pesquisa no seu Windows DSVM
1. Digite em "Serviços",
1. Desajuste todos os serviços Hyper-V para "Manual"
1. Definir "Hiper-V Gestão virtual de máquinas" para "Automático"

O seu ecrã final deve ser assim:

   ![Ativar Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

