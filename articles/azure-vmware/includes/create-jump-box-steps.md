---
title: Crie a caixa de salto Azure VMware Solution
description: Passos para criar a caixa de salto Azure VMware Solution.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: f746e11763e1df1686f3134960dea167bf1c9908
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462266"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-access-private-cloud.md -->

1. No grupo de recursos, **selecione + Adicione** em seguida procurar e selecionar o Microsoft Windows **10** e, em seguida, selecione **Criar**.

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
   | **Portas de entrada públicas** | Selecione **Nenhuma**. Se selecionar Nenhum, pode utilizar [o acesso JIT](../../security-center/security-center-just-in-time.md#jit-configure) para controlar o acesso ao VM apenas quando pretende aceder ao mesmo. Em alternativa, pode utilizar um [Bastião Azure](../../bastion/tutorial-create-host-portal.md) se pretender aceder ao servidor de caixa de salto de forma segura a partir da internet sem expor qualquer porta de rede.  |


1. Assim que a validação passar, **selecione Criar** para iniciar o processo de criação de máquinas virtuais.

