---
title: Implementar máquina virtual Windows 7 Windows Virtual Desktop (clássico) - Azure
description: Como configurar e implementar uma máquina virtual do Windows 7 no Windows Virtual Desktop Virtual Desktop (clássico).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 259e49fbdd6a0eb392ddf6a3cd3c318798cfabd0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88005069"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop-classic"></a>Implementar uma máquina virtual do Windows 7 no Windows Virtual Desktop (clássico)

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows. Se estiver a tentar gerir os objetos virtuais do Windows Desktop do Azure Resource Manager introduzidos na versão atual do Windows Virtual Desktop, consulte [este artigo](../deploy-windows-7-virtual-machine.md).

O processo de implantação de uma máquina virtual (VM) do Windows 7 no Windows Virtual Desktop é ligeiramente diferente do que para os VMs que executam versões posteriores do Windows. Este guia irá dizer-lhe como implementar o Windows 7.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, siga as instruções em [Criar uma piscina de anfitriões com PowerShell](create-host-pools-powershell-2019.md) para criar uma piscina de anfitriões. Depois disso, siga as instruções em [Criar piscinas de anfitrião no Azure Marketplace](create-host-pools-azure-marketplace-2019.md#optional-assign-additional-users-to-the-desktop-application-group) para atribuir um ou mais utilizadores ao grupo de aplicações de desktop.

## <a name="configure-a-windows-7-virtual-machine"></a>Configurar uma máquina virtual do Windows 7

Depois de ter feito os pré-requisitos, está pronto para configurar o seu VM Windows 7 para ser implantado no Windows Virtual Desktop.

Para configurar um VM Windows 7 no Windows Virtual Desktop:

1. Faça o sômis no portal Azure e procure a imagem do Windows 7 Enterprise ou carrede a sua própria imagem personalizada do Windows 7 Enterprise (x64).
2. Implementar uma ou várias máquinas virtuais com o Windows 7 Enterprise como o seu sistema operativo anfitrião. Certifique-se de que as máquinas virtuais permitem o Protocolo de Ambiente de Trabalho Remoto (PDR) (a porta TCP/3389).
3. Ligue-se ao anfitrião da Empresa Windows 7 utilizando o RDP e autente com as credenciais definidas enquanto configura a sua implementação.
4. Adicione a conta que utilizou durante a ligação ao anfitrião com RDP ao grupo "Utilizador de ambiente de trabalho remoto". Se não o fizer, poderá não conseguir ligar-se ao VM depois de o juntar ao seu domínio ative directory.
5. Aceda ao Windows Update no seu VM.
6. Instale todas as atualizações do Windows na categoria Importante.
7. Instale todas as Atualizações do Windows na categoria Opcional (excluindo pacotes de idiomas). Isto instala a atualização 8.0 do Remote Desktop Protocol 8.0[(KB2592687)](https://www.microsoft.com/download/details.aspx?id=35387)que necessita para completar estas instruções.
8. Abra o Editor de Política de Grupo Local e navegue para modelos administrativos **de configuração de**  >    >  **computador, windows components**  >  **Remote Desktop Services** Remote Desktop Session  >  **Host** Remote  >  **Session Environment**.
9. Ativar a política do Protocolo de Ambiente de Trabalho Remoto 8.0.
10. Junte este VM ao seu domínio Ative Directory.
11. Reinicie a máquina virtual executando o seguinte comando:

     ```cmd
     shutdown /r /t 0
     ```

12. Siga as instruções [aqui](/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo/) para obter um sinal de inscrição.
13. [Descarregue o Windows Virtual Desktop Agent para o Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm).
14. [Descarregue o Gestor de Agente virtual do Windows para o Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3).
15. Abra o instalador virtual do Windows Desktop Agent e siga as instruções. Quando solicitado, dê a chave de registo que criou no passo 12.
16. Abra o Gestor de Agente de Secretária Virtual do Windows e siga as instruções.
17. Opcionalmente, bloqueie a porta TCP/3389 para remover o acesso direto ao Protocolo de Ambiente de Trabalho Remoto ao VM.
18. Opcionalmente, confirme que o seu quadro .NET é pelo menos a versão 4.7.2. Isto é especialmente importante se estiver a criar uma imagem personalizada.

## <a name="next-steps"></a>Passos seguintes

A sua implementação virtual do Windows Desktop está agora pronta a ser utilizada. [Descarregue a versão mais recente do cliente virtual do Windows desktop](https://aka.ms/wvd/clients/windows) para começar.

Para obter uma lista de problemas conhecidos e instruções de resolução de problemas para o Windows 7 no Windows Virtual Desktop, consulte o nosso artigo de resolução de problemas em [máquinas virtuais do Windows 7 no Windows Virtual Desktop](troubleshoot-windows-7-vm.md).
