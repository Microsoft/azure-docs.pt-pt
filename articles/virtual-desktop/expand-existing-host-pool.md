---
title: Expandir o pool de anfitriões existente com novos anfitriões de sessão - Azure
description: Como expandir um pool de anfitriões existente com novos anfitriões de sessão no Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 9c73d7434a002a5efc7d058095eb9743a7f3ebf8
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446830"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>Expandir uma piscina de anfitriões existente com novos anfitriões de sessão

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop com objetos de ambiente de trabalho virtuais do Windows, gestor de recursos do Azure. Se estiver a utilizar o Windows Virtual Desktop (clássico) sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/expand-existing-host-pool-2019.md).

À medida que aumenta a utilização dentro da sua piscina de anfitriões, poderá ter de expandir a sua piscina de anfitriões existente com novos anfitriões de sessão para lidar com a nova carga.

Este artigo dir-lhe-á como pode expandir uma piscina de anfitriões existente com novos anfitriões de sessão.

## <a name="what-you-need-to-expand-the-host-pool"></a>O que você precisa para expandir a piscina de anfitriões

Antes de começar, certifique-se de que criou uma piscina de anfitriões e máquinas virtuais de anfitrião (VMs) utilizando um dos seguintes métodos:

- [Portal do Azure](./create-host-pools-azure-marketplace.md)
- [Criar um conjunto de anfitriões com o PowerShell](./create-host-pools-powershell.md)

Você também precisará das seguintes informações a partir de quando criou o anfitrião pool e vMs anfitrião de sessão:

- Tamanho vM, imagem e prefixo de nome
- Domínio junta credenciais de administrador
- Nome de rede virtual e nome da sub-rede

## <a name="add-virtual-machines-with-the-azure-portal"></a>Adicionar máquinas virtuais com o portal Azure

Para expandir a sua piscina de anfitriões adicionando máquinas virtuais:

1. Inicie sessão no portal do Azure.

2. Procure e selecione **Windows Virtual Desktop**.

3. No menu do lado esquerdo do ecrã, selecione **as piscinas host,** selecione o nome da piscina hospedeira a que pretende adicionar máquinas virtuais.

4. Selecione **os anfitriões** de Sessão do menu no lado esquerdo do ecrã.

5. **Selecione +Adicionar** para começar a criar a sua piscina de anfitriões.

6. Ignore o separador Básicos e, em vez disso, selecione o separador **detalhes VM.** Aqui pode ver e editar os detalhes da máquina virtual (VM) que pretende adicionar à piscina anfitriã.

7. Selecione o grupo de recursos que pretende criar os VMs e, em seguida, selecione a região. Pode escolher a região atual que está a usar ou uma nova região.

8. Introduza o número de anfitriões de sessão que pretende adicionar à sua piscina de anfitriões em **Número de VMs**. Por exemplo, se estiver a expandir a sua piscina de anfitriões por cinco anfitriões, insira **5**.

    >[!NOTE]
    >Embora seja possível editar a imagem e o prefixo dos VMs, não recomendamos editá-los se tiver VMs com imagens diferentes na mesma piscina de anfitrião. Edite a imagem e o prefixo apenas se planeia remover VMs com imagens mais antigas da piscina hospedeira afetada.

9. Para obter **informações sobre rede virtual**, selecione a rede virtual e a sub-rede à qual pretende que as máquinas virtuais sejam associadas. Pode selecionar a mesma rede virtual que as máquinas existentes utilizam atualmente ou escolher uma diferente que seja mais adequada à região selecionada no passo 7.

10. Para a **conta Administrador,** insira o nome de utilizador e palavra-passe do domínio do Diretório Ativo associados à rede virtual selecionada. Estas credenciais serão usadas para juntar as máquinas virtuais à rede virtual.

      >[!NOTE]
      >Certifique-se de que os seus nomes administrativos cumprem as informações aqui dadas. E que não há MFA habilitado na conta.

11. Selecione o separador **Tag** se tiver alguma etiqueta com as quais pretende agrupar as máquinas virtuais. Caso contrário, ignore esta aba.

12. Selecione o **separador 'Rever + Criar'.** Reveja as suas escolhas e, se tudo estiver bem, **selecione Criar**.

## <a name="next-steps"></a>Passos seguintes

Agora que expandiu o seu pool de anfitriões existente, pode iniciar sessão num cliente do Windows Virtual Desktop para testá-los como parte de uma sessão de utilizador. Pode ligar-se a uma sessão com qualquer um dos seguintes clientes:

- [Ligar ao cliente de Ambiente de Trabalho do Windows](./connect-windows-7-10.md)
- [Ligar com o cliente web](./connect-web.md)
- [Ligar ao cliente Android](./connect-android.md)
- [Ligar ao cliente de macOS](./connect-macos.md)
- [Ligar ao cliente de iOS](./connect-ios.md)
