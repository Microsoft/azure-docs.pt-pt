---
title: 'Referência: Data Science Virtual Machine Image Deprecation'
titleSuffix: Azure Data Science Virtual Machine
description: Detalhes sobre depreciações que afetam a Máquina Virtual da Ciência dos Dados Do Azure
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 04/03/2020
ms.topic: reference
ms.openlocfilehash: 6004ae0c1fd2fa25a07ab84776ab74789f6e7da9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80754763"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Referência: Depreciação das imagens DSVM

Abaixo discutimos sugestões para lidar com as próximas depreciações na Máquina Virtual de Ciência de Dados Azure.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: Discos de dados migratórios

Deixaremos de suportar a imagem DSVM do Windows 2012 no dia 31 de dezembro de 2019. Para migrar um disco de dados do DSVM do Windows 2012 existente para um DSVM windows 2016, tome os seguintes passos:

1. Crie um novo DSVM do Windows 2016, seguindo as instruções [aqui](./provision-vm.md#create-your-dsvm)apresentadas .
1. Retire os discos de dados existentes da imagem do Windows 2012 utilizando [estas instruções](../../virtual-machines/windows/detach-disk.md).
1. Fixe o disco do passo anterior à imagem do Windows 2016 utilizando [estas instruções](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).

## <a name="centos"></a>CentOS

Os novos utilizadores devem utilizar as imagens Ubuntu ou Windows mais recentes. O CentOS permanecerá disponível para utilização com modelos de solução existentes.