---
title: Ligue ao Windows Virtual Desktop Windows 10 ou 7 - Azure
description: Como ligar-se ao Windows Virtual Desktop utilizando o cliente Windows Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 09/22/2020
ms.author: helohr
manager: femila
ms.custom: template-how-to
ms.openlocfilehash: 625662a6b67e7d30e6320fe7831e4fa7793b9c30
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447884"
---
# <a name="connect-with-the-windows-desktop-client"></a>Ligar ao cliente de Ambiente de Trabalho do Windows

Pode aceder aos recursos de Desktop Virtual do Windows em dispositivos com Windows 10, Windows 10 IoT Enterprise e Windows 7 utilizando o cliente Windows Desktop. 

> [!IMPORTANT]
> Isto não suporta o Windows 8 ou o Windows 8.1.
> 
> Isto só suporta objetos Azure Resource Manager, para suportar objetos sem O Gestor de Recursos Azure, ver [Connect com o cliente Windows Desktop (clássico).](./virtual-desktop-fall-2019/connect-windows-7-10-2019.md)
> 
> Isto não suporta o cliente RemoteApp e Desktop Connections (RADC) ou o cliente Remote Desktop Connection (MSTSC).

## <a name="install-the-windows-desktop-client"></a>Instale o cliente windows desktop

Descarregue o cliente com base na sua versão Windows:

- [Windows 64-bit](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32-bit](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Janelas ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

Durante a instalação para determinar o acesso, selecione:

- **Instale apenas para si**
- **Instalação para todos os utilizadores desta máquina** (requer direitos de administração)

Para lançar o cliente após a instalação, utilize o menu **Iniciar** e procure por **Desktop Remoto**.

## <a name="subscribe-to-a-workspace"></a>Subscreva um espaço de trabalho

Para subscrever um espaço de trabalho, escolha:

- Use uma conta de trabalho ou escola e faça com que o cliente descubra os recursos disponíveis para si
- Utilize o URL específico do recurso

Para lançar o recurso uma vez subscrito, vá ao **Centro de Ligação** e clique duas vezes no recurso.

> [!TIP]
> Para lançar um recurso a partir do menu **Iniciar,** pode encontrar a pasta com o nome Workspace ou introduzir o nome do recurso na barra de pesquisa.

### <a name="use-a-user-account"></a>Utilize uma conta de utilizador

1. **Selecione Subscreva** a partir da página principal.
1. Inscreva-se na sua conta de utilizador quando solicitado.

Os recursos agrupados pelo espaço de trabalho aparecerão no **Centro de Ligação.**

   > [!NOTE]
   > O cliente Do Windows falha automaticamente no Windows Virtual Desktop (clássico). 
   > 
   > No entanto, se o cliente detetar recursos adicionais do Azure Resource Manager, adiciona-os automaticamente ou notifica o utilizador de que estão disponíveis.

### <a name="use-a-specific-url"></a>Use um URL específico

1. **Selecione Subscrever com URL** na página principal.
1. Insira o *URL do Espaço de Trabalho* ou um endereço de *e-mail:*
   - Para **URL Workspace,** utilize o URL fornecido pelo seu administrador.

   |Recursos Disponíveis|URL|
   |-|-|
   |Windows Virtual Desktop (clássico)|`https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`|
   |Windows Virtual Desktop|`https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`|
   |Windows Virtual Desktop (US Gov)|`https://rdweb.wvd.azure.us/api/arm/feeddiscovery`|
   
   - Para **e-mail,** use o seu endereço de e-mail. 
      
   O cliente encontrará o URL associado ao seu e-mail, desde que o seu administrador tenha ativado a [descoberta de e-mail](/windows-server/remote/remote-desktop-services/rds-email-discovery).

1. Selecione **Seguinte**.
1. Inscreva-se na sua conta de utilizador quando solicitado.

Os recursos agrupados pelo espaço de trabalho aparecerão no **Centro de Ligação.**

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como usar o cliente, confira [Consulte o cliente do Windows Desktop.](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/)

Se é um administrador interessado em saber mais sobre as funcionalidades do cliente, consulte o [cliente do Windows Desktop para obter administradores.](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-admin)
