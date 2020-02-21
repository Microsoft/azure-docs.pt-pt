---
title: 'Referência: Data Science Virtual Machine Image Deprecation'
titleSuffix: Azure Data Science Virtual Machine
description: Detalhes sobre depreciações que afetam a Máquina Virtual da Ciência dos Dados Do Azure
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 71ce8519dca6fa71340b1c34fe3a6891ccf5753f
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77525792"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Referência: Depreciação das imagens DSVM

Abaixo discutimos sugestões para lidar com as próximas depreciações na Máquina Virtual de Ciência de Dados Azure.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: Discos de dados migratórios

Deixaremos de suportar a imagem DSVM do Windows 2012 no dia 31 de dezembro de 2019. Para migrar um disco de dados do DSVM do Windows 2012 existente para um DSVM windows 2016, tome os seguintes passos:

1. Crie um novo DSVM do Windows 2016, seguindo as instruções [aqui](./provision-vm.md#create-your-dsvm)apresentadas .
1. Retire os discos de dados existentes da imagem do Windows 2012 utilizando [estas instruções](../../virtual-machines/windows/detach-disk.md).
1. Fixe o disco do passo anterior à imagem do Windows 2016 utilizando [estas instruções](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
