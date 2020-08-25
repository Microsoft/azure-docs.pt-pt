---
title: 'Referência: Data Science Virtual Machine Image Retirements'
titleSuffix: Azure Data Science Virtual Machine
description: Detalhes sobre as reformas que afetam a Máquina Virtual Azure Data Science
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 07/17/2020
ms.topic: reference
ms.openlocfilehash: ac56e2e69cf62c8afae73e0a7616e7e7f80c0493
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816325"
---
# <a name="reference-retirements-of-dsvm-images"></a>Referência: Aposentadorias de Imagens DSVM

Abaixo, discutimos a aposentadoria (depreciação) de imagens, e sugestões para lidar com as próximas reformas na Azure Data Science Virtual Machine.

## <a name="why-we-retire-dsvm-images"></a>Por que retiramos imagens DSVM

A Máquina Virtual de Ciência de Dados tem atualmente duas edições:

* Ubuntu
* Windows Server

A imagem DSVM para estas edições é construída numa versão específica do sistema operativo, por exemplo Ubuntu 18.04 LTS. Com o tempo, a janela de manutenção da _versão_ do sistema operativo terminará e/ou as ferramentas de ciência de dados deixarão de suportar versões mais antigas do sistema operativo. Para manter a imagem DSVM atualizada com os mais recentes sistemas operativos e ferramentas de ciência de dados, lançamos uma nova imagem DSVM baseada em versões mais recentes do sistema operativo.

## <a name="how-we-retire-dsvm-images"></a>Como retiramos imagens DSVM

Emitimos um anúncio de _aposentadoria_ onde os utilizadores dSVM existentes são notificados (via e-mail) de uma próxima reforma de imagem DSVM. O anúncio de aposentadoria irá detalhar a data de _aposentação_ (após esta data a imagem não está disponível) e recomendações de mitigação (por exemplo, atualização para uma nova imagem DSVM).

Na data do _anúncio_ esconderemos a imagem no mercado para que:

1. os novos utilizadores estão impedidos de provisões inadvertidamente uma imagem DSVM aposentada.
2. os utilizadores existentes podem utilizar as implementações ARM até à data de reforma.

A imagem oculta receberá patches do sistema operativo até à data da _reforma,_ mas __não__ receberá atualizações para as ferramentas e quadros da ciência dos dados. Na data da _reforma,_ a imagem não estará disponível para implementações da ARM.

Qualquer imagem DSVM a provisionada na sua subscrição continuará a funcionar após a data de aposentação. No entanto, recomendamos o upgrade para a mais recente imagem DSVM para que tenha os mais recentes sistemas operativos e ferramentas de ciência de dados.

## <a name="impact"></a>Impacto

As imagens existentes do DSVM na sua subscrição continuarão a funcionar após a data de aposentação. No entanto, recomendamos que os utilizadores atualizem a sua imagem DSVM para a versão mais recente utilizando o portal Azure ou o modelo ARM.

> [!WARNING]
> As imagens DSVM aposentadas a provisionadas utilizando conjuntos de escala de máquina virtual não irão aumentar após a data de reforma.
>
> Os modelos ARM que não tenham sido atualizados com os novos detalhes de imagem DSVM não serão implementados após a data de aposentação.

## <a name="mitigating-upcoming-retirements"></a>Atenuar as próximas reformas

Nesta secção, discutimos a mitigação das próximas reformas.

### <a name="upgrade-windows-2016-dsvm"></a>Atualização Do Windows 2016 DSVM

Para migrar um disco de dados do seu DSVM existente no Windows 2016 para um DSVM windows 2019, tome as seguintes medidas:

1. Crie um novo DSVM Windows 2019, seguindo as instruções [aqui](./provision-vm.md#create-your-dsvm)apresentadas.
1. Retire os discos de dados existentes da sua imagem do Windows 2016 utilizando [estas instruções](../../virtual-machines/windows/detach-disk.md).
1. Fixe o disco do passo anterior à sua imagem do Windows 2019 utilizando [estas instruções.](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)

### <a name="upgrade-ubuntu-1604-dsvm"></a>Upgrade Ubuntu 16.04 DSVM

Recomendamos a atualização dos DSVMs existentes ubuntu 16.04 para a [edição DSVM Ubuntu 18.04](./dsvm-ubuntu-intro.md).
