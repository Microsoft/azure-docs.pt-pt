---
title: Crie a caixa de salto Azure VMware Solution
description: Passos para criar a caixa de salto Azure VMware Solution.
ms.topic: include
ms.date: 09/21/2020
ms.openlocfilehash: c46bd61d9003f157877a1fd0df9adb9e8143a63f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91298508"
---
<!-- Used in avs-deployment.md and tutorial-access-private-cloud.md -->

1. No grupo de recursos, **selecione + Adicione** em seguida procurar e selecionar o Microsoft Windows **10**e, em seguida, selecione **Criar**.

   :::image type="content" source="../media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="Adicione um novo Windows 10 VM para uma caixa de salto." border="true":::

1. Introduza as informações necessárias nos campos e, em seguida, **selecione Review + create**. 

   Para mais informações sobre os campos, consulte a tabela seguinte.

   | Campo | Valor |
   | --- | --- |
   | **Subscrição** | O valor é pré-povoado com a Subscrição pertencente ao Grupo de Recursos. |
   | **Grupo de recursos** | O valor é pré-povoado para o grupo de recursos atual, que criou no tutorial anterior.  |
   | **Nome da máquina virtual** | Insira um nome único para o VM. |
   | **Região** | Selecione a localização geográfica do VM. |
   | **Opções de disponibilidade** | Deixe selecionado o valor predefinido. |
   | **Imagem** | Selecione a imagem VM. |
   | **Tamanho** | Deixe o valor de tamanho padrão. |
   | **Tipo de autenticação**  | Selecione **palavra-passe**. |
   | **Nome de Utilizador** | Introduza o nome de utilizador para iniciar sessão no VM. |
   | **Palavra-passe** | Introduza a palavra-passe para iniciar sessão no VM. |
   | **Confirmar palavra-passe** | Introduza a palavra-passe para iniciar sessão no VM. |
   | **Portas de entrada públicas** | Selecione **Nenhuma**. Se selecionar Nenhum, pode utilizar [o acesso JIT](../../security-center/security-center-just-in-time.md#jit-configure) para controlar o acesso ao VM apenas quando pretende aceder ao mesmo.  |


1. Assim que a validação passar, **selecione Criar** para iniciar o processo de criação de máquinas virtuais.

   :::image type="content" source="../media/tutorial-access-private-cloud/ss11-review-create-wjb01.png" alt-text="Crie um novo Windows 10 VM para uma caixa de salto." border="true":::