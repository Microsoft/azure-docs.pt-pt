---
title: O servidor de licença Remote Desktop não está disponível quando se conecta a um VM Azure Microsoft Docs
description: Saiba como resolver problemas com os problemas de falha do RDP porque não há nenhum servidor de licença de desktop remoto disponível Microsoft Docs
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
ms.openlocfilehash: 6b5864d40d2a4d8f8d6cf404df29f909a73f04e2
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97832048"
---
# <a name="remote-desktop-license-server-isnt-available-when-you-connect-to-an-azure-vm"></a>O servidor de licença de ambiente de trabalho remoto não está disponível quando se conecta a um VM Azure

Este artigo ajuda a resolver o problema quando não é possível ligar-se a uma máquina virtual Azure (VM) porque nenhum servidor de licença de ambiente de trabalho remoto está disponível para fornecer uma licença.

## <a name="symptoms"></a>Sintomas

Quando tenta ligar-se a uma máquina virtual (VM), experimenta os seguintes cenários:

- A imagem em VM mostra que o sistema operativo está completamente carregado e à espera de credenciais.
- Recebe as seguintes mensagens de erro quando tenta fazer uma ligação com o Microsoft Remote Desktop Protocol (RDP):

  - A sessão remota foi desligada porque não existem servidores de licença remote desktop disponíveis para fornecer uma licença.

  - Não está disponível nenhum servidor de licença de ambiente de trabalho remoto. Os Serviços remotos de Desktop deixarão de funcionar porque este computador já passou do seu período de carência e não contactou pelo menos um servidor de licença válido do Windows Server 2008. Selecione esta mensagem para abrir a configuração do servidor do anfitrião da sessão RD para utilizar o diagnóstico de licenciamento.

No entanto, pode ligar-se normalmente ao VM utilizando uma sessão administrativa:

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>Causa

Este problema ocorre se um servidor de licença de ambiente de trabalho remoto não estiver disponível para fornecer uma licença para iniciar uma sessão remota. Pode ser causado por vários cenários, embora tenha sido criado um papel de Anfitrião de Sessão de Secretária remota no VM:

- Nunca houve um papel de licenciamento remoto no ambiente, e o período de graça, 180 dias, acabou.
- Uma licença de ambiente de trabalho remoto foi instalada no ambiente, mas nunca foi ativada.
- Uma licença de ambiente de trabalho remoto no ambiente não tem licenças de acesso ao cliente (CALs) injetadas para configurar a ligação.
- Foi instalada uma licença de ambiente de trabalho remoto no ambiente. Existem CALs disponíveis, mas não foram configurados corretamente.
- Uma licença de ambiente de trabalho remoto tem CALs, e foi ativada. No entanto, alguns outros problemas no servidor de licença remote desktop impedem-no de fornecer licenças no ambiente.

## <a name="solution"></a>Solução

Para resolver este problema, [volte a fazer uma operação de segurança no disco de oss](../windows/snapshot-copy-managed-disk.md) e siga estes passos:

1. Ligue-se ao VM utilizando uma sessão administrativa:

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

    Se não conseguir ligar-se ao VM utilizando uma sessão administrativa, pode utilizar a [Consola Virtual Machine Serial on Azure](serial-console-windows.md) para aceder ao VM da seguinte forma:

    1. Aceda à Consola em Série selecionando suporte & consola em série **de resolução de**  >  **problemas (Preview)**. Se a função estiver ativada no VM, pode ligar o VM com sucesso.

    2. Criar um novo canal para uma instância CMD. Insira **o CMD** para iniciar o canal e obter o nome do canal.

    3. Mude para o canal que executa a instância CMD. Neste caso, deve ser o canal 1:

       ```
       ch -si 1
       ```

    4. Selecione **Introduza** novamente e introduza um nome de utilizador válido e senha, ID local ou de domínio, para o VM.

2. Verifique se o VM tem uma função de Anfitrião de Sessão de Secretária remota ativada. Se a função estiver ativada, certifique-se de que está a funcionar corretamente. Abra uma instância CMD elevada e siga estes passos:

    1. Utilize o seguinte comando para verificar o estado da função "Anfitrião da Sessão de Secretária remota":

       ```
        reg query "HKLM\SOFTWARE\Microsoft\ServerManager\ServicingStorage\ServerComponentCache\RDS-RD-Server" /v InstallState
        ```

        Se este comando devolver um valor de 0, significa que a função está desativada, e pode ir para o passo 3.

    2. Utilize o seguinte comando para verificar as políticas e reconfigurar conforme necessário:

       ```
        reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode

        reg query "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers
       ```

        Se o valor **De LicenciamentoMode** for definido para qualquer valor que não seja 4, por utilizador, então desaprote o valor para 4:

         ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
        ```

       Se o valor **SpecificLicenseServers** não existir, ou tiver informações incorretas do servidor de licença, altere-o da seguinte forma:

       ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
       ```

    3. Depois de escoar quaisquer alterações ao registo, reinicie o VM.

    4. Se não tiver CALs, remova o papel de Anfitrião de Sessão de Secretária Remota. Em seguida, o PDR será reposto ao normal. Só permite duas ligações RDP simultâneas ao VM:

        ```
       dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
        ```

        Se o VM tiver a função de licenciamento de ambiente de trabalho remoto e não for utilizado, também pode remover essa função:

       ```
        dism /ONLINE /Disable-feature /FeatureName:Licensing
       ```

    5. Certifique-se de que o VM pode ligar-se ao servidor de licença remote desktop. Pode testar a conectividade com a porta 135 entre o VM e o servidor de licença: 

       ```
       telnet <FQDN / IP License Server> 135
       ```

3. Se não houver um servidor de licença de ambiente de trabalho remoto no ambiente e quiser um, pode [instalar um serviço de função de licenciamento de desktop remoto](/previous-versions/windows/it-pro/windows-server-2008-r2-and-2008/cc731765(v=ws.11)). Em [seguida, configurar o licenciamento RDS](https://techcommunity.microsoft.com/t5/Ask-The-Performance-Team/RD-Licensing-Configuration-on-Windows-Server-2012/ba-p/375383).

4. Se um servidor de licença de ambiente de trabalho remoto estiver configurado e saudável, certifique-se de que o servidor de licença de ambiente de trabalho remoto é ativado com CALs.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema.
