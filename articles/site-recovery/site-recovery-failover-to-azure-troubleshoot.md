---
title: Falha na resolução de problemas a falhas do Azure | Microsoft Docs
description: Este artigo descreve formas de resolver erros comuns ao falhar com Azure
author: ponatara
manager: abhemraj
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 01/08/2020
ms.author: mayg
ms.openlocfilehash: 8fe21ce5b92d672a2e025e0b45b8cbaea5951a8b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105043908"
---
# <a name="troubleshoot-errors-when-failing-over-vmware-vm-or-physical-machine-to-azure"></a>Resolver erros ao efetuar a ativação pós-falha da VM VMware ou da máquina física do Azure

Pode receber um dos seguintes erros ao fazer a falha de uma máquina virtual para a Azure. Para resolver problemas, utilize os passos descritos para cada condição de erro.

## <a name="failover-failed-with-error-id-28031"></a>Falha falhou com Error ID 28031

A Recuperação do Site não foi capaz de criar uma falha sobre a máquina virtual em Azure. Pode acontecer por causa de uma das seguintes razões:

* Não existe quota suficiente para criar a máquina virtual: Pode verificar a quota disponível indo para subscrição -> Usage + quotas. Pode abrir um [novo pedido de apoio](https://aka.ms/getazuresupport) para aumentar a quota.

* Está a tentar falhar com máquinas virtuais de diferentes famílias de tamanhos diferentes no mesmo conjunto de disponibilidade. Certifique-se de que escolhe a família do mesmo tamanho para todas as máquinas virtuais no mesmo conjunto de disponibilidade. Altere o tamanho indo para as definições de Computação e Rede da máquina virtual e, em seguida, retentou falhar.

* Existe uma política sobre a subscrição que impede a criação de uma máquina virtual. Mude a política para permitir a criação de uma máquina virtual e, em seguida, relemque o failover.

## <a name="failover-failed-with-error-id-28092"></a>Falha falhou com Error ID 28092

A Recuperação do Site não foi capaz de criar uma interface de rede para a falha da máquina virtual. Certifique-se de que tem quota suficiente disponível para criar interfaces de rede na subscrição. Pode verificar a quota disponível indo para subscrição -> as quotas Usage +. Pode abrir um [novo pedido de apoio](https://aka.ms/getazuresupport) para aumentar a quota. Se tiver quota suficiente, então este pode ser um problema intermitente, tente a operação novamente. Se a questão persistir mesmo após as retrações, então deixe um comentário no final deste documento.  

## <a name="failover-failed-with-error-id-70038"></a>Falha falhou com Error ID 70038

A Recuperação do Site não foi capaz de criar uma falha sobre a máquina virtual Clássica em Azure. Pode acontecer porque:

* Um dos recursos, como uma rede virtual que é necessária para a criação da máquina virtual, não existe. Crie a rede virtual conforme fornecido nas definições de Compute e Rede da máquina virtual ou modifique a definição para uma rede virtual que já existe e, em seguida, relemque o failover.

## <a name="failover-failed-with-error-id-170010"></a>Falha falhou com Erro ID 170010

A Recuperação do Site não foi capaz de criar uma falha sobre a máquina virtual em Azure. Pode acontecer porque uma atividade interna de hidratação falhou para a máquina virtual no local.

Para criar qualquer máquina em Azure, o ambiente Azure requer que alguns dos condutores estejam no estado de arranque e serviços como o DHCP para estar em estado de arranque automático. Assim, a atividade de hidratação, no momento do failover, converte o tipo de arranque de **atapi, intelide, storflt, vmbus e storvsc drivers** para arranque. Também converte o tipo de startup de alguns serviços como o DHCP para o arranque automático. Esta atividade pode falhar devido a questões específicas do ambiente. 

Para alterar manualmente o tipo de arranque de controladores para **o Windows Guest OS,** siga os passos abaixo:

1. [Descarregue](https://download.microsoft.com/download/5/D/6/5D60E67C-2B4F-4C51-B291-A97732F92369/Script-no-hydration.ps1) o script sem hidratação e execute-o da seguinte forma. Este script verifica se o VM requer hidratação.

    `.\Script-no-hydration.ps1`

    Dá o seguinte resultado se for necessária hidratação:

    ```output
    REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

    This system doesn't meet no-hydration requirement.
    ```

    Caso o VM cumpra o requisito de não hidratação, o script dará o resultado "Este sistema não cumpre os requisitos de não hidratação". Neste caso, todos os motoristas e serviços estão no estado conforme exigido pelo Azure e a hidratação no VM não é necessária.

2. Executar o script sem hidratação da seguinte forma se o VM não cumprir o requisito de não hidratação.

    `.\Script-no-hydration.ps1 -set`
    
    Isto converterá o tipo de arranque de motoristas e dará o resultado como abaixo:

    ```output
    REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

    Updating registry:  REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc   start =  0

    This system is now no-hydration compatible.
    ```

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Incapaz de ligar/RDP/SSH à máquina virtual falhada devido ao botão de ligação acinzentado na máquina virtual

Para obter instruções detalhadas sobre resolução de problemas em questões de PDR, consulte [aqui](/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection)a nossa documentação.

Para obter instruções detalhadas sobre resolução de problemas em questões de SSH, consulte [aqui](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection)a nossa documentação.

Se o botão **'Ligar'** na VM em Azure estiver acinzentado e não estiver ligado ao Azure através de uma rota expressa ou ligação VPN local-a-local, então,

1. Aceda à **rede de máquinas virtuais,**  >  clique no nome da interface de rede necessária.  ![A screenshot mostra a página de Networking para uma máquina virtual com o nome de interface de rede selecionado.](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Navegue para **Configurações Ip,** em seguida, clique no campo nome da configuração IP necessária. ![A screenshot mostra a página de configurações I P para a interface de rede com o nome de configuração I P selecionado.](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Para ativar o endereço IP público, clique em **Ativar**. ![Ativar IP](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Clique em **Configurar as definições necessárias**  >  **Criar novos**. ![Criar novo](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Insira o nome do endereço público, escolha as opções padrão para **SKU** e **atribuição,** em seguida, clique em **OK**.
6. Agora, para guardar as alterações es feitas, clique em **Guardar**.
7. Feche os painéis e navegue para a secção **de visão geral** da máquina virtual para ligar/RDP.

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>Não é possível ligar/RDP/SSH - Botão VM Connect disponível

Se o botão **Ligar** no VM falhado em Azure estiver disponível (não acinzentado), verifique os **diagnósticos de Boot** na sua Máquina Virtual e verifique se existem erros tal como listados [neste artigo](/troubleshoot/azure/virtual-machines/boot-diagnostics).

1. Se a máquina virtual não tiver começado, tente falhar até um ponto de recuperação mais antigo.
2. Se a aplicação dentro da máquina virtual não estiver em cima, tente falhar num ponto de recuperação consistente da aplicação.
3. Se a máquina virtual estiver unida ao domínio, certifique-se de que o controlador de domínio está a funcionar com precisão. Isto pode ser feito seguindo os passos abaixo:

    a. Crie uma nova máquina virtual na mesma rede.

    b.  Certifique-se de que é capaz de se juntar ao mesmo domínio em que se espera que a máquina virtual falhou.

    c. Se o controlador de domínio **não** estiver a funcionar com precisão, tente iniciar sessão na máquina virtual falhada utilizando uma conta de administrador local.
4. Se estiver a utilizar um servidor DNS personalizado, certifique-se de que está acessível. Isto pode ser feito seguindo os passos abaixo:

    a. Criar uma nova máquina virtual na mesma rede e

    b. Verifique se a máquina virtual é capaz de fazer a resolução de nomes usando o servidor DNS personalizado

>[!Note]
>Permitir qualquer definição que não seja o Boot Diagnostics exigiria que o Agente VM do Azure fosse instalado na máquina virtual antes da falha.

## <a name="unable-to-open-serial-console-after-failover-of-a-uefi-based-machine-into-azure"></a>Incapaz de abrir consola em série após falha de uma máquina baseada na UEFI em Azure

Se conseguir ligar-se à máquina utilizando RDP mas não conseguir abrir a consola em série, siga os passos abaixo:

* Se a máquina OS for Red Hat ou Oracle Linux 7.*/8.0, executar o seguinte comando no Failover Azure VM com permissões de raiz. Reinicie o VM após o comando.

  ```console
  grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg
  ```

* Se a máquina OS for CentOS 7.*, executar o seguinte comando no Azure VM de failover com permissões de raiz. Reinicie o VM após o comando.

  ```console
  grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg
  ```

## <a name="unexpected-shutdown-message-event-id-6008"></a>Mensagem de encerramento inesperada (ID do evento 6008)

Ao iniciar um failover do post Windows VM, se receber uma mensagem de paragem inesperada no VM recuperado, indica que um estado de paragem VM não foi capturado no ponto de recuperação utilizado para o failover. Isto acontece quando se recupera a um ponto em que o VM não tinha sido totalmente desligado.

Isto normalmente não é motivo de preocupação e pode geralmente ser ignorado por falhas não planeadas. Se o failover for planeado, certifique-se de que o VM é corretamente desligado antes do failover e fornece tempo suficiente para que os dados de replicação pendentes no local sejam enviados para Azure. Em seguida, utilize a opção **Mais recente** no [ecrã Failover](site-recovery-failover.md#run-a-failover) de modo a que quaisquer dados pendentes sobre o Azure sejam processados num ponto de recuperação, que é depois utilizado para o failover VM.

## <a name="unable-to-select-the-datastore"></a>Não é possível selecionar a Datastore

Este problema é indicado quando não consegue ver a datastore em Azure o portal ao tentar reprotegir a máquina virtual que sofreu uma falha. Isto porque o alvo Master não é reconhecido como uma máquina virtual sob vCenters adicionados à Recuperação do Site Azure.

Para obter mais informações sobre a reprotecção de uma máquina virtual, consulte [Reprotect e falhe as máquinas traseiras para um local no local após a falha em Azure](vmware-azure-reprotect.md).

Para resolver o problema:

Crie manualmente o alvo Master no vCenter que gere a sua máquina de origem. A datastore estará disponível após a próxima descoberta do vCenter e atualização de operações de tecido.

> [!Note]
> 
> As operações de tecido de descoberta e refrescamento podem demorar até 30 minutos para ser concluída. 

## <a name="linux-master-target-registration-with-cs-fails-with-a-tls-error-35"></a>O registo Linux Master Target com CS falha com um erro TLS 35 

O registo do Alvo Mestre de Recuperação do Local Azure com o servidor de configuração falha devido à ativação do Proxy autenticado no Alvo Principal. 
 
Este erro é indicado pelas seguintes cordas no registo de instalação: 

```
RegisterHostStaticInfo encountered exception config/talwrapper.cpp(107)[post] CurlWrapper Post failed : server : 10.38.229.221, port : 443, phpUrl : request_handler.php, secure : true, ignoreCurlPartialError : false with error: [at curlwrapperlib/curlwrapper.cpp:processCurlResponse:231]   failed to post request: (35) - SSL connect error. 
```

Para resolver o problema:
 
1. No servidor de configuração VM, abra um pedido de comando e verifique as definições de procuração utilizando os seguintes comandos:

    gato /etc/eco ambiente $http_proxy eco $https_proxy 

2. Se a saída dos comandos anteriores mostrar que as definições http_proxy ou https_proxy são definidas, utilize um dos seguintes métodos para desbloquear as comunicações Master Target com o servidor de configuração:
   
   - Descarregue a [ferramenta PsExec](/sysinternals/downloads/psexec).
   - Utilize a ferramenta para aceder ao contexto do utilizador do Sistema e determinar se o endereço de procuração está configurado. 
   - Se o proxy estiver configurado, abra o IE num contexto de utilizador do sistema utilizando a ferramenta PsExec.
  
     **psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"**

   - Para garantir que o servidor alvo principal pode comunicar com o servidor de configuração:
  
     - Modifique as definições de procuração no Internet Explorer para contornar o endereço IP do servidor Master Target através do proxy.   
     Ou
     - Desative o representante no servidor Target Principal. 


## <a name="next-steps"></a>Passos seguintes
- Resolução de problemas [ligação RDP ao Windows VM](/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection)
- Resolução de [problemas Ligação SSH ao Linux VM](/troubleshoot/azure/virtual-machines/detailed-troubleshoot-ssh-connection)

Se precisar de mais ajuda, em seguida, publique a sua consulta no [Microsoft Q&Uma página de perguntas para recuperação](/answers/topics/azure-site-recovery.html) do site ou deixe um comentário no final deste documento. Temos uma comunidade ativa que deve ser capaz de ajudá-lo.