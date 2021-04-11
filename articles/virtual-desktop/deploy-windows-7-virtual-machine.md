---
title: Implementar máquina virtual Windows 7 Windows Virtual Desktop - Azure
description: Como configurar e implementar uma máquina virtual do Windows 7 no Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 07/11/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 65ef7af1247fdb38a7c85c1a8134257942f5ef5b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447034"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>Implementar uma máquina virtual do Windows 7 no Windows Virtual Desktop

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop com objetos de ambiente de trabalho virtuais do Windows, gestor de recursos do Azure. Se estiver a utilizar o Windows Virtual Desktop (clássico) sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md).

O processo de implantação de uma máquina virtual (VM) do Windows 7 no Windows Virtual Desktop é ligeiramente diferente do que para os VMs que executam versões posteriores do Windows. Este guia irá dizer-lhe como implementar o Windows 7.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, siga as instruções em [Criar uma piscina de anfitriões com PowerShell](create-host-pools-powershell.md) para criar uma piscina de anfitriões. Se estiver a utilizar o portal, siga as instruções nos passos 1 a 9 de [Criar uma piscina de anfitriões utilizando o portal Azure](create-host-pools-azure-marketplace.md). Depois disso, selecione **Review + Create** para criar uma piscina de anfitriões vazia.

## <a name="configure-a-windows-7-virtual-machine"></a>Configurar uma máquina virtual do Windows 7

Depois de ter feito os pré-requisitos, está pronto para configurar o seu VM Windows 7 para ser implantado no Windows Virtual Desktop.

Para configurar um VM Windows 7 no Windows Virtual Desktop:

1. Faça o sômis no portal Azure e procure a imagem do Windows 7 Enterprise ou carrede a sua própria imagem personalizada do Windows 7 Enterprise (x64).
2. Implementar uma ou várias máquinas virtuais com o Windows 7 Enterprise como o seu sistema operativo anfitrião. Certifique-se de que as máquinas virtuais permitem o Protocolo de Ambiente de Trabalho Remoto (PDR) (a porta TCP/3389).
3. Ligue-se ao anfitrião da Empresa Windows 7 utilizando o RDP e autente com as credenciais definidas enquanto configura a sua implementação.
4. Adicione a conta que utilizou durante a ligação ao anfitrião com RDP ao grupo "Utilizador de ambiente de trabalho remoto". Se não adicionar a conta, poderá não conseguir ligar-se ao VM depois de se juntar ao seu domínio ative directory.
5. Aceda ao Windows Update no seu VM.
6. Instale todas as atualizações do Windows na categoria Importante.
7. Instale todas as Atualizações do Windows na categoria Opcional (excluindo pacotes de idiomas). Este processo instala a atualização 8.0 do Protocolo de Ambiente de Trabalho Remoto[(KB2592687)](https://www.microsoft.com/download/details.aspx?id=35387)que necessita para completar estas instruções.
8. Abra o Editor de Política de Grupo Local e navegue para modelos administrativos **de configuração de**  >    >  **computador, windows components**  >  **Remote Desktop Services** Remote Desktop Session  >  **Host** Remote  >  **Session Environment**.
9. Ativar a política do Protocolo de Ambiente de Trabalho Remoto 8.0.
10. Junte este VM ao seu domínio Ative Directory.
11. Reinicie a máquina virtual executando o seguinte comando:

     ```cmd
     shutdown /r /t 0
     ```

12. Siga as instruções [aqui](/powershell/module/az.desktopvirtualization/new-azwvdregistrationinfo) para obter um sinal de inscrição.

      - Se preferir usar o portal Azure, também pode ir à página geral da piscina de anfitrião a que pretende adicionar o VM e criar um símbolo lá.

13. [Descarregue o Windows Virtual Desktop Agent para o Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm).
14. [Descarregue o Gestor de Agente virtual do Windows para o Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3).
15. Abra o instalador virtual do Windows Desktop Agent e siga as instruções. Quando solicitado, dê a chave de registo que criou no passo 12.
16. Abra o Gestor de Agente de Secretária Virtual do Windows e siga as instruções.
17. Opcionalmente, bloqueie a porta TCP/3389 para remover o acesso direto ao Protocolo de Ambiente de Trabalho Remoto ao VM.
18. Opcionalmente, confirme que o seu quadro .NET é pelo menos a versão 4.7.2. Atualizar a sua estrutura é especialmente importante se estiver a criar uma imagem personalizada.

## <a name="next-steps"></a>Passos seguintes

A sua implementação virtual do Windows Desktop está agora pronta a ser utilizada. [Descarregue a versão mais recente do cliente virtual do Windows desktop](https://aka.ms/wvd/clients/windows) para começar.

Para obter uma lista de problemas conhecidos e instruções de resolução de problemas para o Windows 7 no Windows Virtual Desktop, consulte o nosso artigo de resolução de problemas em [máquinas virtuais do Windows 7 no Windows Virtual Desktop](./virtual-desktop-fall-2019/troubleshoot-windows-7-vm.md).
