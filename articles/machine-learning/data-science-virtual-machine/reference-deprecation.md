---
title: 'Referência: Data Science Virtual Machine Image Deprecation'
titleSuffix: Azure Data Science Virtual Machine
description: Detalhes sobre depreciações que afetam a Máquina Virtual Azure Data Science
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 04/03/2020
ms.topic: reference
ms.openlocfilehash: 6004ae0c1fd2fa25a07ab84776ab74789f6e7da9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80754763"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Referência: Depreciação das Imagens DSVM

Abaixo, discutimos sugestões para lidar com as próximas depreciações na Máquina Virtual Azure Data Science.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: Discos de dados migratórios

Deixaremos de suportar a imagem DSVM do Windows 2012 no dia 31 de dezembro de 2019. Para migrar um disco de dados do seu DSVM existente do Windows 2012 para um DSVM windows 2016, tome as seguintes medidas:

1. Crie um novo DSVM Windows 2016, seguindo as instruções [aqui](./provision-vm.md#create-your-dsvm)apresentadas.
1. Retire os discos de dados existentes da sua imagem do Windows 2012 utilizando [estas instruções](../../virtual-machines/windows/detach-disk.md).
1. Fixe o disco do passo anterior à sua imagem do Windows 2016 utilizando [estas instruções](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).

## <a name="centos"></a>CentOS

Os novos utilizadores devem utilizar as imagens mais recentes do Ubuntu ou do Windows. O CentOS permanecerá disponível para utilização com os modelos de solução existentes.