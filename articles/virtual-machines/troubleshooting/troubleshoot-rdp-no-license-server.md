---
title: O servidor de licença Área de Trabalho Remota não está disponível quando você se conecta a uma VM do Azure | Microsoft Docs
description: Saiba como solucionar problemas de falha de RDP porque não há Área de Trabalho Remota servidor de licença disponível | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 154160f9a3fbd485ee6383bf3d5ff1c291520a75
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088525"
---
# <a name="remote-desktop-license-server-isnt-available-when-you-connect-to-an-azure-vm"></a>Área de Trabalho Remota servidor de licença não está disponível quando você se conecta a uma VM do Azure

Este artigo ajuda a resolver o problema quando você não pode se conectar a uma VM (máquina virtual) do Azure porque nenhum servidor de licença Área de Trabalho Remota está disponível para fornecer uma licença.

## <a name="symptoms"></a>Sintomas

Ao tentar se conectar a uma VM (máquina virtual), você terá os seguintes cenários:

- A captura de tela da VM mostra que o sistema operacional está totalmente carregado e aguardando credenciais.
- Você recebe as seguintes mensagens de erro ao tentar fazer uma conexão de protocolo de Área de Trabalho Remota da Microsoft (RDP):

  - A sessão remota foi desconectada porque não há servidores de licença Área de Trabalho Remota disponíveis para fornecer uma licença.

  - Não há Área de Trabalho Remota servidor de licença disponível. Serviços de Área de Trabalho Remota deixará de funcionar porque este computador ultrapassou seu período de carência e não contatou pelo menos um servidor de licença válido do Windows Server 2008. Selecione esta mensagem para abrir Host da Sessão RD configuração do servidor para usar o diagnóstico de licenciamento.

No entanto, você pode se conectar à VM normalmente usando uma sessão administrativa:

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>Causa

Esse problema ocorre se um servidor de licença Área de Trabalho Remota não estiver disponível para fornecer uma licença para iniciar uma sessão remota. Pode ser causado por vários cenários, embora uma função de Host da Sessão da Área de Trabalho Remota tenha sido configurada na VM:

- Nunca havia uma Área de Trabalho Remota função de licenciamento no ambiente, e o período de carência, 180 dias, terminará.
- Uma licença Área de Trabalho Remota foi instalada no ambiente, mas ela nunca é ativada.
- Uma licença de Área de Trabalho Remota no ambiente não tem CALs (licenças de acesso para cliente) injetadas para configurar a conexão.
- Uma licença de Área de Trabalho Remota foi instalada no ambiente. Há CALs disponíveis, mas elas não foram configuradas corretamente.
- Uma licença Área de Trabalho Remota tem CALs e foi ativada. No entanto, alguns outros problemas na Área de Trabalho Remota servidor de licença impedem que ele forneça licenças no ambiente.

## <a name="solution"></a>Solução

Para resolver esse problema, [faça backup do disco do sistema operacional](../windows/snapshot-copy-managed-disk.md) e siga estas etapas:

1. Conecte-se à VM usando uma sessão administrativa:

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

    Se você não conseguir se conectar à VM usando uma sessão administrativa, poderá usar o [console serial da máquina virtual no Azure](serial-console-windows.md) para acessar a VM da seguinte maneira:

    1. Acesse o console serial selecionando **suporte & solução de problemas** > **console serial (versão prévia)** . Se o recurso estiver habilitado na VM, você poderá conectar a VM com êxito.

    2. Crie um novo canal para uma instância CMD. Insira **cmd** para iniciar o canal e obter o nome do canal.

    3. Alterne para o canal que executa a instância CMD. Nesse caso, deve ser o canal 1:

       ```
       ch -si 1
       ```

    4. Selecione **Enter** novamente e insira um nome de usuário e senha, local ou ID de domínio válidos para a VM.

2. Verifique se a VM tem uma função de Host da Sessão da Área de Trabalho Remota habilitada. Se a função estiver habilitada, verifique se ela está funcionando corretamente. Abra uma instância de CMD elevada e siga estas etapas:

    1. Use o seguinte comando para verificar o status da função de Host da Sessão da Área de Trabalho Remota:

       ```
        reg query "HKLM\SOFTWARE\Microsoft\ServerManager\ServicingStorage\ServerComponentCache\RDS-RD-Server" /v InstallState
        ```

        Se esse comando retornar um valor de 0, significa que a função está desabilitada e você pode ir para a etapa 3.

    2. Use o seguinte comando para verificar as políticas e reconfigurar conforme necessário:

       ```
        reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode reg query "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers
       ```

        Se o valor **LicensingMode** for definido como qualquer valor diferente de 4, por usuário, defina o valor como 4:

         ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
        ```

       Se o valor de **SpecifiedLicenseServers** não existir ou se tiver informações incorretas do servidor de licença, altere-o da seguinte maneira:

       ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
       ```

    3. Depois de fazer alterações no registro, reinicie a VM.

    4. Se você não tiver CALs, remova a função de Host da Sessão da Área de Trabalho Remota. Em seguida, o RDP será configurado de volta para normal. Ele só permite duas conexões simultâneas RDP para a VM:

        ```
       dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
        ```

        Se a VM tiver a função de licenciamento Área de Trabalho Remota e não for usada, você também poderá remover essa função:

       ```
        dism /ONLINE /Disable-feature /FeatureName:Licensing
       ```

    5. Verifique se a VM pode se conectar ao servidor de licença Área de Trabalho Remota. Você pode testar a conectividade com a porta 135 entre a VM e o servidor de licença: 

       ```
       telnet <FQDN / IP License Server> 135
       ```

3. Se não houver um servidor de licença Área de Trabalho Remota no ambiente e você quiser um, você poderá [instalar um serviço de função de licenciamento área de trabalho remota](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731765(v=ws.11)). Em seguida, [Configure o licenciamento RDS](https://techcommunity.microsoft.com/t5/Ask-The-Performance-Team/RD-Licensing-Configuration-on-Windows-Server-2012/ba-p/375383).

4. Se um servidor de licença Área de Trabalho Remota estiver configurado e íntegro, verifique se o servidor de licença Área de Trabalho Remota está ativado com CALs.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte

Se você ainda precisar de ajuda, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o problema.
