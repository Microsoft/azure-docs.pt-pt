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
ms.openlocfilehash: d022b1124146a1e506401e6cee257805e3a38fd3
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526556"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Questões conhecidas e resolução de problemas na Máquina Virtual de Ciência de Dados Azure

Este artigo ajuda-o a encontrar e corrigir erros ou falhas encontrados ao utilizar a Máquina Virtual de Ciência de Dados Azure.

## <a name="python-package-installation-issues"></a>Problemas de instalação de pacotes Python

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Instalar pacotes com pip quebra dependências no Linux

Utilize `sudo pip install` em vez de `pip install` ao instalar pacotes.

## <a name="disk-encryption-issues"></a>Problemas de encriptação do disco

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Encriptação de disco falha no Ubuntu DSVM

A encriptação do disco azure (ADE) não é suportada atualmente no Ubuntu DSVM. Como uma suposição, considere configurar [a encriptação do Armazenamento Azure com chaves geridas pelo cliente](../../storage/common/storage-encryption-keys-portal.md).

## <a name="tool-appears-disabled"></a>A ferramenta parece desativada

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-V não funciona no Windows DSVM

Este comportamento é esperado, quanto ao desempenho do arranque, desativamos alguns serviços. Para reativar, abra a barra de pesquisa no seu Windows DSVM, digite "Services", em seguida, coloque todos os serviços Hyper-V em "Manual" e coloque "Hyper-V Virtual Machine Management" para "Automatic".

O seu ecrã final deve ser assim:

   ![Ativar Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

