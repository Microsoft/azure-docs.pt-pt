---
title: O servidor de licenças de ambiente de trabalho remoto não está disponível quando se liga a uma VM do Azure | Documentos da Microsoft
description: Aprenda a solucionar problemas do RDP falhar porque não está disponível nenhum servidor de licenciamento de área de trabalho remota | Documentos da Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 550b971602d1736e0ba3981a5b7ca546862ea034
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60318957"
---
# <a name="remote-desktop-license-server-isnt-available-when-you-connect-to-an-azure-vm"></a>Servidor de licenças de ambiente de trabalho remoto não está disponível quando se liga a uma VM do Azure

Este artigo ajuda a resolver o problema quando não é possível ligar a uma máquina virtual do Azure (VM), porque nenhum servidor de licenças de ambiente de trabalho remoto não está disponível para fornecer uma licença.

## <a name="symptoms"></a>Sintomas

Quando tentar ligar a uma máquina virtual (VM), ocorrer os seguintes cenários:

- A captura de ecrã da VM mostra que o sistema operacional está totalmente carregado e esperar até que as credenciais.
- As seguintes mensagens de erro é exibida ao tentar estabelecer uma ligação de Microsoft protocolo RDP (Remote Desktop):

  - A sessão remota foi desligada porque existem não existem servidores de licença de ambiente de trabalho remoto disponíveis para fornecer uma licença.

  - Nenhum servidor de licenças de ambiente de trabalho remoto está disponível. Os serviços de ambiente de trabalho remoto para de funcionar porque este computador passou o respetivo período de tolerância e não contacte, pelo menos, um servidor de licenças do Windows Server 2008 válido. Selecione esta mensagem para abrir a configuração do servidor de anfitrião de sessão do RD para utilizar o diagnóstico de licenciamento.

No entanto, pode ligar à VM normalmente através de uma sessão administrativa:

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>Causa

Este problema ocorre se um servidor de licenças de ambiente de trabalho remoto não está disponível para fornecer uma licença para iniciar uma sessão remota. Pode ser causado por vários cenários, mesmo que uma função de anfitrião de sessões de ambiente de trabalho remoto foi configurada na VM:

- Nunca houve uma função de licenciamento de ambiente de trabalho remoto no ambiente e o período de tolerância é de 180 dias, ao longo.
- Uma licença de ambiente de trabalho remoto foi instalada no ambiente, mas nunca é ativado.
- Uma licença de ambiente de trabalho remoto no ambiente não tem licenças de acesso de cliente (CALs) injetadas para configurar a ligação.
- Uma licença de ambiente de trabalho remoto foi instalada no ambiente. Há CALs disponíveis, mas eles não foram configurados corretamente.
- Uma licença de ambiente de trabalho remoto tem CALs, e ele foi ativado. No entanto, há alguns outros problemas no servidor de licenças de ambiente de trabalho remoto que impedem-lo de licenças no ambiente.

## <a name="solution"></a>Solução

Para resolver este problema, [cópia de segurança de disco do SO](../windows/snapshot-copy-managed-disk.md) e siga estes passos:

1. Ligar à VM através de uma sessão administrativa:

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

    Se não conseguir ligar à VM através de uma sessão administrativa, pode utilizar o [consola de série de Máquina Virtual no Azure](serial-console-windows.md) para aceder à VM da seguinte forma:

    1. Aceder à consola de série, selecionando **suporte e resolução de problemas** > **consola de série (pré-visualização)** . Se a funcionalidade está ativada na VM, pode ligar a VM com êxito.

    2. Crie um novo canal para uma instância CMD. Introduza **CMD** para iniciar o canal e obter o nome do canal.

    3. Mude para o canal que executa a instância CMD. Neste caso, deve ser canal 1:

       ```
       ch -si 1
       ```

    4. Selecione **Enter** novamente e introduza um nome de utilizador válido e o ID de palavra-passe, local ou num domínio, para a VM.

2. Verifique se a VM tem uma função de anfitrião de sessões de ambiente de trabalho remoto ativada. Se a função estiver ativada, certifique-se de que está a funcionar corretamente. Abra uma instância CMD elevada e siga estes passos:

    1. Utilize o seguinte comando para verificar o estado da função de anfitrião de sessões de ambiente de trabalho remoto:

       ```
        reg query "HKLM\SOFTWARE\Microsoft\ServerManager\ServicingStorage\ServerComponentCache\RDS-RD-Server" /v InstallState
        ```

        Se este comando devolve um valor de 0, significa que a função está desativada, e pode ir para o passo 3.

    2. Utilize o seguinte comando para verificar as políticas e reconfigurar conforme necessário:

       ```
        reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode reg query "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers
       ```

        Se o **LicensingMode** valor é definido como qualquer valor diferente de 4, por usuário, em seguida, defina o valor como 4:

         ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
        ```

       Se o **SpecifiedLicenseServers** valor não existe ou tem informações de licença incorreto do servidor, em seguida, altere-o da seguinte forma:

       ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
       ```

    3. Depois de efetuar quaisquer alterações no Registro, reinicie a VM.

    4. Se não tem CALs, remova a função de anfitrião de sessões de ambiente de trabalho remoto. Em seguida, o RDP será definido volta ao normal. Apenas permite que duas conexões simultâneas de RDP à VM:

        ```
       dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
        ```

        Se a VM tem a função de licenciamento de ambiente de trabalho remoto e não é utilizado, também pode remover essa função:

       ```
        dism /ONLINE /Disable-feature /FeatureName:Licensing
       ```

    5. Certifique-se de que a VM pode ligar ao servidor de licenças de ambiente de trabalho remoto. Pode testar a conectividade com a porta 135 entre a VM e o servidor de licenças: 

       ```
       telnet <FQDN / IP License Server> 135
       ```

3. Se não existe nenhum servidor de licenciamento de ambiente de trabalho remoto no ambiente e desejar uma, pode [instalar um serviço de função de licenciamento de ambiente de trabalho remoto](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731765(v=ws.11)). Em seguida, [configurar o licenciamento RDS](https://blogs.technet.microsoft.com/askperf/2013/09/20/rd-licensing-configuration-on-windows-server-2012/).

4. Se um servidor de licenças de ambiente de trabalho remoto está configurado e em bom estado, certifique-se de que o servidor de licenças de ambiente de trabalho remoto está ativado com CALs.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte

Se precisar de ajuda, ainda [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema.
