---
title: Implementar máquina virtual Windows 7 Windows Ambiente de trabalho virtual - Azure
description: Como configurar e implementar uma máquina virtual do Windows 7 no Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0cb5b2ee8b8391dc4fcb78cc1d3bd212c44f1803
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615412"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>Implementar uma máquina virtual do Windows 7 no Windows Virtual Desktop

>[!IMPORTANT]
>Este conteúdo aplica-se à versão outono 2019 que não suporta objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure.

O processo de implementação de uma máquina virtual do Windows 7 (VM) no Windows Virtual Desktop é ligeiramente diferente do que para os VMs que executam versões posteriores do Windows. Este guia dir-lhe-á como implementar o Windows 7.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, siga as instruções em [Criar uma piscina de anfitriões com](create-host-pools-powershell-2019.md) a PowerShell para criar uma piscina de anfitriões. Depois disso, siga as instruções em [Create host pools no Azure Marketplace](create-host-pools-azure-marketplace-2019.md#optional-assign-additional-users-to-the-desktop-application-group) para atribuir um ou mais utilizadores ao grupo de aplicações de ambiente de trabalho.

## <a name="configure-a-windows-7-virtual-machine"></a>Configure uma máquina virtual do Windows 7

Depois de ter feito os pré-requisitos, está pronto para configurar o VM do Windows 7 para implementação no Windows Virtual Desktop.

Para configurar um VM Windows 7 no Windows Virtual Desktop:

1. Inscreva-se no portal Azure e procure a imagem do Windows 7 Enterprise ou carregue a sua própria imagem personalizada do Windows 7 Enterprise (x64).  
2. Implemente uma ou várias máquinas virtuais com o Windows 7 Enterprise como o seu sistema operativo anfitrião. Certifique-se de que as máquinas virtuais permitem o Protocolo de Ambiente de Trabalho Remoto (RDP) (a porta TCP/3389).
3. Ligue-se ao anfitrião da Empresa Windows 7 utilizando o RDP e autenticar com as credenciais que definiu enquanto configura a sua implementação. 
4. Adicione a conta que usou durante a ligação ao anfitrião com RDP ao grupo "Remote Desktop User". Se não o fizer, poderá não conseguir ligar-se ao VM depois de se juntar ao seu domínio de Diretório Ativo.
5. Vá ao Windows Update no seu VM.
6. Instale todas as Atualizações do Windows na categoria Importante.
7. Instale todas as Atualizações do Windows na categoria Opcional (excluindo pacotes de idiomas). Isto instala a atualização Do Protocolo de Ambiente de Trabalho Remoto 8.0[(KB2592687)](https://www.microsoft.com/download/details.aspx?id=35387)que necessita para completar estas instruções.
8. Open the Local Group Policy Editor and navigate to **Computer Configuration** > **Administrative Templates** > **Windows Components** > **Remote Desktop Services** > **Remote Desktop Session Host** > **Remote Session Environment**.
9. Ativar a política do Protocolo de Ambiente de Trabalho Remoto 8.0.
10. Junte-se a este VM ao seu domínio de Diretório Ativo.
11. Reiniciar a máquina virtual executando o seguinte comando:
    
     ```cmd
     shutdown /r /t 0
     ```
    
12. Siga as instruções [aqui](/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo/) para obter um sinal de registo.
13. [Descarregue o Windows Virtual Desktop Agent para o Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm).
14. [Descarregue o Gestor de Agente](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3)de Ambiente de Trabalho Virtual do Windows para o Windows 7 .
15. Abra o instalador do Windows Virtual Desktop Agent e siga as instruções. Quando solicitado, dê a chave de registo que criou no passo 12.
16. Abra o instalador de Ambiente de Trabalho Virtual do Windows e siga as instruções.
17. Opcionalmente, bloqueie a porta TCP/3389 para remover o acesso direto ao Protocolo de Ambiente de Trabalho Remoto ao VM.

## <a name="next-steps"></a>Passos seguintes

A implementação do Windows Virtual Desktop está agora pronta a ser utilizada. [Descarregue a versão mais recente do cliente do Windows Virtual Desktop](https://aka.ms/wvd/clients/windows) para começar.

Para obter uma lista de problemas conhecidos e instruções de resolução de problemas para o Windows 7 no Windows Virtual Desktop, consulte o nosso artigo de resolução de problemas nas [máquinas virtuais Do Windows7 no Windows Virtual Desktop](troubleshoot-windows-7-vm.md).
