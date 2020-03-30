---
title: O servidor de licença Remote Desktop não está disponível quando se conecta a um Azure VM [ Microsoft Docs
description: Saiba como resolver problemas de falha no RDP porque não está disponível nenhum servidor de licença de Desktop Remoto [ Desktop' remoto ] Microsoft Docs
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71088525"
---
# <a name="remote-desktop-license-server-isnt-available-when-you-connect-to-an-azure-vm"></a>O servidor de licença seleção remote não está disponível quando se conecta a um VM Azure

Este artigo ajuda a resolver o problema quando não é possível ligar-se a uma máquina virtual Azure (VM) porque nenhum servidor de licença de Ambiente de Trabalho Remoto está disponível para fornecer uma licença.

## <a name="symptoms"></a>Sintomas

Quando tenta ligar-se a uma máquina virtual (VM), experimenta os seguintes cenários:

- A imagem vM mostra que o sistema operativo está completamente carregado e à espera de credenciais.
- Recebe as seguintes mensagens de erro quando tenta efar uma ligação do Protocolo de Ambiente de Trabalho Remoto da Microsoft (RDP):

  - A sessão remota foi desligada porque não existem servidores de licença seletiva remota disponíveis para fornecer uma licença.

  - Não existe nenhum servidor de licença de Ambiente de Trabalho Remoto disponível. Os Serviços de Ambiente de Trabalho Remoto deixarão de funcionar porque este computador já passou do seu período de carência e não contactou pelo menos um servidor de licença válido do Windows Server 2008. Selecione esta mensagem para abrir a configuração do servidor do anfitrião rd session para utilizar o diagnóstico de licenciamento.

No entanto, pode ligar-se normalmente ao VM através de uma sessão administrativa:

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>Causa

Este problema ocorre se um servidor de licença de Ambiente de Trabalho Remoto não estiver disponível para fornecer uma licença para iniciar uma sessão remota. Pode ser causado por vários cenários, mesmo que tenha sido criado um papel de anfitrião de sessão de ambiente de trabalho remoto no VM:

- Nunca houve um papel de licenciamento remoto no ambiente, e o período de graça, de 180 dias, acabou.
- Uma licença de Ambiente de Trabalho Remoto foi instalada no ambiente, mas nunca foi ativada.
- Uma licença de ambiente de trabalho remoto no ambiente não tem licenças de acesso ao cliente (CALs) injetadas para configurar a ligação.
- Foi instalada uma licença de Ambiente de Trabalho Remoto no ambiente. Existem CALs disponíveis, mas não foram configurados corretamente.
- Uma licença de ambiente de trabalho remoto tem CALs, e foi ativada. No entanto, alguns outros problemas no servidor de licença seleção remota impedem-no de fornecer licenças no ambiente.

## <a name="solution"></a>Solução

Para resolver este problema, [volte a fazer o disquedo](../windows/snapshot-copy-managed-disk.md) e siga estes passos:

1. Ligue-se ao VM através de uma sessão administrativa:

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

    Se não conseguir ligar-se ao VM utilizando uma sessão administrativa, pode utilizar a Consola de Série de [Máquina Virtual em Azure](serial-console-windows.md) para aceder ao VM da seguinte forma:

    1. Aceda à Consola em Série selecionando suporte & consola série de **resolução** > de problemas **(pré-visualização)**. Se a funcionalidade estiver ativada no VM, pode ligar o VM com sucesso.

    2. Crie um novo canal para uma instância CMD. Introduza a **CMD** para iniciar o canal e obter o nome do canal.

    3. Mude para o canal que executa a instância CMD. Neste caso, deve ser o canal 1:

       ```
       ch -si 1
       ```

    4. Selecione **Entrar** novamente e introduzir um nome de utilizador e senha válidos, id local ou de domínio, para o VM.

2. Verifique se o VM tem uma função de anfitrião de sessão de ambiente de trabalho remota ativada. Se o papel estiver ativado, certifique-se de que está a funcionar corretamente. Abra uma instância CMD elevada e siga estes passos:

    1. Utilize o seguinte comando para verificar o estado da função anfitrião da sessão de ambiente de trabalho remoto:

       ```
        reg query "HKLM\SOFTWARE\Microsoft\ServerManager\ServicingStorage\ServerComponentCache\RDS-RD-Server" /v InstallState
        ```

        Se este comando devolver um valor de 0, significa que o papel está desativado, e pode ir para o passo 3.

    2. Utilize o seguinte comando para verificar as políticas e reconfigurar conforme necessário:

       ```
        reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode reg query "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers
       ```

        Se o valor do **Modo de Licenciamento** for definido para qualquer valor diferente de 4, por utilizador, então detete o valor para 4:

         ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
        ```

       Se o valor **dos Servidores Licenciados Especificados** não existir, ou tiver informações incorretas sobre o servidor da licença, então altere-as da seguinte forma:

       ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
       ```

    3. Depois de efazer quaisquer alterações no registo, reinicie o VM.

    4. Se não tiver CALs, retire a função de anfitrião da sessão de ambiente de trabalho remoto. Em seguida, o PDR será reposto ao normal. Só permite duas ligações pDR simultâneas ao VM:

        ```
       dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
        ```

        Se o VM tiver a função de licenciamento remote Desktop e não for utilizado, também pode remover essa função:

       ```
        dism /ONLINE /Disable-feature /FeatureName:Licensing
       ```

    5. Certifique-se de que o VM pode ligar-se ao servidor de licença seleção remota. Pode testar a conectividade com a porta 135 entre o VM e o servidor de licença: 

       ```
       telnet <FQDN / IP License Server> 135
       ```

3. Se não houver um servidor de licença de ambiente de trabalho remoto no ambiente e quiser um, pode instalar um serviço de [licenciamento de desktop remoto.](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731765(v=ws.11)) Em seguida, [configurar o licenciamento RDS](https://techcommunity.microsoft.com/t5/Ask-The-Performance-Team/RD-Licensing-Configuration-on-Windows-Server-2012/ba-p/375383).

4. Se um servidor de licença de ambiente de trabalho remoto estiver configurado e saudável, certifique-se de que o servidor de licença de ambiente de trabalho remoto é ativado com CALs.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se ainda precisar de ajuda, [contacte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) o suporte para resolver o seu problema.
