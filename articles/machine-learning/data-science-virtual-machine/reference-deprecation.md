---
title: 'Referência: substituição de imagem DSVM'
description: Detalhes sobre as reprovações que afetam o Máquina Virtual de Ciência de Dados do Azure (DSVM)
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 4968ac08b75141ed84994ca287215a34728232c4
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333342"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Referência: substituição de imagens DSVM

Abaixo, discutimos sugestões para lidar com as futuras substituições no Máquina Virtual de Ciência de Dados do Azure.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: migrando discos de dados

Vamos parar de dar suporte à imagem do Windows 2012 DSVM em 5 de novembro de 2019. Para migrar um disco de dados de seu DSVM do Windows 2012 existente para um DSVM do Windows 2016, execute as seguintes etapas:

1. Crie um novo DSVM do Windows 2016, seguindo as instruções mostradas [aqui](./provision-vm.md#create-your-dsvm).
1. Desanexe discos de dados existentes da imagem do Windows 2012 usando [estas instruções](../../virtual-machines/windows/detach-disk.md).
1. Anexe o disco da etapa anterior à sua imagem do Windows 2016 usando [estas instruções](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
