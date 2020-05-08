---
title: Expandir a piscina de anfitriões existente com novos anfitriões de sessão - Azure
description: Como expandir um pool de anfitriões existente com novos anfitriões de sessão no Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: be76c665e1f5319b3e1ff1976e44fee9cd90ea6b
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82607203"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>Expandir uma piscina de anfitriões existente com novos anfitriões de sessão

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização da primavera de 2020 com os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows. Se estiver a utilizar o lançamento do Windows Virtual Desktop Fall 2019 sem objetos do Gestor de Recursos Azure, consulte [este artigo](./virtual-desktop-fall-2019/expand-existing-host-pool-2019.md).
>
> A atualização Do Windows Virtual Desktop Spring 2020 encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos usá-la para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

À medida que aumenta o uso dentro da sua piscina de anfitriões, poderá ter de expandir a sua piscina de anfitriões existente com novos anfitriões de sessão para lidar com a nova carga.

Este artigo irá dizer-lhe como você pode expandir uma piscina de anfitriões existente com novos anfitriões de sessão.

## <a name="what-you-need-to-expand-the-host-pool"></a>O que você precisa para expandir a piscina anfitriã

Antes de começar, certifique-se de que criou uma piscina de anfitriões e máquinas virtuais de anfitrião de sessão (VMs) utilizando um dos seguintes métodos:

- [Portal do Azure](./create-host-pools-azure-marketplace.md)
- [Criar um conjunto de anfitriões com o PowerShell](./create-host-pools-powershell.md)

Você também vai precisar das seguintes informações a partir de quando você criou o pool anfitrião e vMs anfitrião de sessão:

- VM tamanho, imagem e prefixo de nome
- Domínio aderir credenciais de administrador
- Nome de rede virtual e nome da sub-rede

## <a name="add-virtual-machines-with-the-azure-portal"></a>Adicione máquinas virtuais com o portal Azure

Para expandir a sua piscina de acolhimento adicionando máquinas virtuais:

1. Inicie sessão no Portal do Azure.

2. Procure e selecione **Windows Virtual Desktop**.

3. No menu do lado esquerdo do ecrã, selecione **pools host**e, em seguida, selecione o nome da piscina anfitriã a que pretende adicionar máquinas virtuais.

4. Selecione **máquinas virtuais** do menu do lado esquerdo do ecrã.

5. Selecione **+Adicione** para começar a criar a sua piscina anfitriã.

6. Ignore o separador Basics e, em vez disso, selecione o separador de **detalhes VM.** Aqui pode ver e editar os detalhes da máquina virtual (VM) que pretende adicionar à piscina anfitriã.

7. Selecione o grupo de recursos que pretende criar os VMs abaixo e, em seguida, selecione a região. Pode escolher a região atual que está a usar ou uma nova região.
   
8. Insira o novo número total de anfitriões de sessão que deseja em **Número de VMs**. Por exemplo, se estiver a expandir a sua piscina de anfitriões de cinco anfitriões para oito sessões, insira **8**. 
   
    >[!NOTE]
    >Não é possível editar o tamanho ou imagem dos VMs porque é importante garantir que todos os VMs na piscina anfitriã têm o mesmo tamanho.
    
9. Para **obter informações**de rede virtual, selecione a rede virtual e a subnet a que pretende que as máquinas virtuais sejam unidas. Pode selecionar a mesma rede virtual que as suas máquinas existentes utilizam atualmente ou escolher uma diferente que seja mais adequada à região selecionada no passo 7.

10. Para a **conta 'Administrador',** introduza o nome de utilizador do domínio Ative Directory e a palavra-passe associada à rede virtual selecionada. Estas credenciais serão usadas para juntar as máquinas virtuais à rede virtual.

      >[!NOTE]
      >Certifique-se de que os seus nomes de administrador estão em conformidade com as informações aqui dadas. E que não há MFA ativado na conta.

11. Selecione o separador **Tag** se tiver alguma etiqueta com a quais queira agrupar as máquinas virtuais. Caso contrário, ignore esta conta. 

12. Selecione o **separador Review + Criar.** Reveja as suas escolhas e, se tudo estiver bem, selecione **Criar**. 

## <a name="next-steps"></a>Passos seguintes

Agora que expandiu o seu pool de anfitriões existente, pode iniciar sessão com um cliente do Windows Virtual Desktop para os testar como parte de uma sessão de utilizador. Pode ligar-se a uma sessão com qualquer um dos seguintes clientes:

- [Ligar ao cliente de Ambiente de Trabalho do Windows](./connect-windows-7-and-10.md)
- [Ligar com o cliente web](./connect-web.md)
- [Ligar ao cliente Android](./connect-android.md)
- [Ligar ao cliente de macOS](./connect-macos.md)
- [Ligar ao cliente de iOS](./connect-ios.md)
