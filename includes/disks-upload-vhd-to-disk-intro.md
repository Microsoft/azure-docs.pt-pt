---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e96d205ef1a8f94baa3a0cfe6c5127b6cf570e5a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80420964"
---
Este artigo explica como enviar um VHD da sua máquina local para um disco gerido pelo Azure ou copiar um disco gerido para outra região, utilizando o AzCopy. Este processo, upload direto, também permite fazer o upload de um VHD até 32 TiB em tamanho diretamente para um disco gerido. Atualmente, o upload direto é suportado para discos hdd padrão, SSD padrão e sSD premium. Ainda não é suportado para discos ultra.

Se estiver a fornecer uma solução de backup para VMs IaaS em Azure, recomendamos a utilização de upload direto para restaurar as cópias de segurança dos clientes para os discos geridos. Ao carregar um VHD de uma fonte externa para Azure, as velocidades dependeriam da largura de banda local. Ao carregar ou copiar de um VM Azure, então a largura de banda seria a mesma que os HDDs padrão.