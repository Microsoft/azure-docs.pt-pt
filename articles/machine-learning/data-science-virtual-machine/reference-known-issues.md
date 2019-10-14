---
title: Problemas conhecidos & solução de problemas
titleSuffix: Azure Data Science Virtual  Machine
description: Obtenha uma lista dos problemas conhecidos, soluções alternativas e solução de problemas do Azure Máquina Virtual de Ciência de Dados
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: a0b22326a429edfa2f2b8741453215b42910891c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301922"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Problemas conhecidos e solução de problemas do Máquina Virtual de Ciência de Dados do Azure

Este artigo ajuda você a encontrar e corrigir erros ou falhas encontrados ao usar o Máquina Virtual de Ciência de Dados do Azure.

## <a name="python-package-installation-issues"></a>Problemas de instalação do pacote do Python

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Instalando pacotes com dependências de quebras de pip no Linux

Use `sudo pip install` em vez de `pip install` ao instalar pacotes.

## <a name="disk-encryption-issues"></a>Problemas de criptografia de disco

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>A criptografia de disco falha no Ubuntu DSVM

No momento, não há suporte para Azure Disk Encryption (ADE) no DSVM do Ubuntu. Como alternativa, considere configurar a [criptografia de armazenamento do Azure com chaves gerenciadas pelo cliente](../../storage/common/storage-encryption-keys-portal.md).

## <a name="tool-appears-disabled"></a>A ferramenta aparece desabilitada

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>O Hyper-V não funciona no DSVM do Windows

Esse é um comportamento esperado, como para o desempenho de inicialização, desabilitamos alguns serviços. Para reabilitar, abra a barra de pesquisa no DSVM do Windows, digite "serviços" e, em seguida, defina todos os serviços do Hyper-V como "manual" e defina "gerenciamento de máquina virtual do Hyper-V" como "automático".

Sua tela final deve ter a seguinte aparência:

   ![Ativar Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

