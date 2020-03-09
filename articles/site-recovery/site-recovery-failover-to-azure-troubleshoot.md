---
title: Problemas falham nas falhas de Azure Microsoft Docs
description: Este artigo descreve formas de resolver erros comuns ao falhar com o Azure
author: ponatara
manager: abhemraj
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 01/08/2020
ms.author: mayg
ms.openlocfilehash: 6de37daa0b9e0ebc711a5dacbdce352e3675a3db
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363005"
---
# <a name="troubleshoot-errors-when-failing-over-vmware-vm-or-physical-machine-to-azure"></a>Erros de resolução de problemas ao falhar em VMware VM ou máquina física para o Azure

Pode receber um dos seguintes erros ao mesmo tempo que faz a falha de uma máquina virtual para o Azure. Para resolução de problemas, utilize os passos descritos para cada condição de erro.

## <a name="failover-failed-with-error-id-28031"></a>Falha falhou com Erro ID 28031

A Recuperação do Site não foi capaz de criar uma máquina virtual falhada em Azure. Pode acontecer por uma das seguintes razões:

* Não existe quota suficiente para criar a máquina virtual: Pode verificar a quota disponível indo para Subscrição -> Utilização + quotas. Pode abrir um [novo pedido](https://aka.ms/getazuresupport) de apoio para aumentar a quota.

* Está a tentar falhar máquinas virtuais de diferentes famílias de tamanhos no mesmo conjunto de disponibilidade. Certifique-se de que escolhe a família do mesmo tamanho para todas as máquinas virtuais no mesmo conjunto de disponibilidade. Mude o tamanho indo para as definições de Compute e Rede da máquina virtual e, em seguida, retentar a falha.

* Existe uma política sobre a subscrição que impede a criação de uma máquina virtual. Mude a política para permitir a criação de uma máquina virtual e, em seguida, retentar a falha.

## <a name="failover-failed-with-error-id-28092"></a>Falha falhou com error ID 28092

A Recuperação do Site não foi capaz de criar uma interface de rede para a falha sobre a máquina virtual. Certifique-se de que dispõe de quota suficiente para criar interfaces de rede na subscrição. Pode verificar a quota disponível indo para Subscrição -> Utilização + quotas. Pode abrir um [novo pedido](https://aka.ms/getazuresupport) de apoio para aumentar a quota. Se tiver quota suficiente, então esta pode ser uma questão intermitente, tente novamente a operação. Se a questão persistir mesmo após as tentativas de tentativas, deixe um comentário no final deste documento.  

## <a name="failover-failed-with-error-id-70038"></a>Falha falhou com erro ID 70038

A Recuperação do Site não foi capaz de criar uma máquina virtual clássica falhada em Azure. Pode acontecer porque:

* Um dos recursos, como uma rede virtual que é necessária para a criação da máquina virtual, não existe. Crie a rede virtual conforme fornecido nas definições computacionais e de rede da máquina virtual ou modifique a definição para uma rede virtual que já existe e, em seguida, retentar a falha.

## <a name="failover-failed-with-error-id-170010"></a>Falha falhou com erro ID 170010

A Recuperação do Site não foi capaz de criar uma máquina virtual falhada em Azure. Pode acontecer porque uma atividade interna de hidratação falhou na máquina virtual no local.

Para criar qualquer máquina em Azure, o ambiente Azure exige que alguns dos condutores estejam no arranque do estado e serviços como o DHCP estejam em estado de arranque automático. Assim, a atividade de hidratação, no momento do failover, converte o tipo de arranque de **atapi, intelide, storflt, vmbus e storvsc para** iniciar. Também converte o tipo de arranque de alguns serviços como o DHCP para iniciar automaticamente. Esta atividade pode falhar devido a questões específicas do ambiente. 

Para alterar manualmente o tipo de controladores de arranque para **o Windows Guest OS,** siga os passos seguintes:

1. [Descarregue](https://download.microsoft.com/download/5/D/6/5D60E67C-2B4F-4C51-B291-A97732F92369/Script-no-hydration.ps1) o script sem hidratação e execute-o da seguinte forma. Este guião verifica se a VM necessita de hidratação.

    `.\Script-no-hydration.ps1`

    Dá o seguinte resultado se for necessária hidratação:

        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

        This system doesn't meet no-hydration requirement.

    Caso o VM cumpra o requisito de não hidratação, o script dará o resultado "Este sistema cumpre os requisitos de não hidratação". Neste caso, todos os motoristas e serviços estão no estado, conforme exigido pelo Azure e não é necessária hidratação no VM.

2. Execute o script sem hidratação da seguinte forma se o VM não cumprir o requisito de não hidratação.

    `.\Script-no-hydration.ps1 -set`
    
    Isto converterá o tipo de motorização de arranque e dará o resultado como abaixo:
    
        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0 

        Updating registry:  REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc   start =  0 

        This system is now no-hydration compatible. 

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Incapaz de ligar/RDP/SSH à máquina virtual falhada devido ao botão de ligação acinzentado na máquina virtual

Se o botão **Connect** no VM falhado em Azure estiver acinzentado e não estiver ligado ao Azure através de uma rota expressa ou ligação VPN local-para-local, então,

1. Vá à **máquina Virtual** > **Networking,** clique no nome da interface de rede necessária.  ![](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG) de interface de rede
2. Navegue para **configurações ip**e clique no campo de nome da configuração IP necessária. ![IPConfigurações](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Para ativar o endereço IP público, clique em **Ativar**. ![ativar](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png) IP
4. Clique em **Configurar as definições necessárias** > **Criar novas**. ![Criar novos](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Introduza o nome do endereço público, escolha as opções predefinidas para **SKU** e **atribuição,** em seguida, **clique**OK .
6. Agora, para guardar as alterações feitas, clique em **Guardar**.
7. Feche os painéis e navegue até à secção **de visão geral** da máquina virtual para ligar/RDP.

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>Incapaz de ligar/RDP/SSH - Botão VM Connect disponível

Se o botão **Connect** no VM falhado em Azure estiver disponível (não está acinzentado), verifique os **diagnósticos da Bota** na sua Máquina Virtual e verifique se há erros listados [neste artigo](../virtual-machines/windows/boot-diagnostics.md).

1. Se a máquina virtual não tiver começado, tente falhar num ponto de recuperação mais antigo.
2. Se a aplicação dentro da máquina virtual não estiver em alta, tente falhar num ponto de recuperação consistente com aplicações.
3. Se a máquina virtual for unida ao domínio, certifique-se de que o controlador de domínio está a funcionar com precisão. Isto pode ser feito seguindo os passos seguintes:

    a. Crie uma nova máquina virtual na mesma rede.

    b.  Certifique-se de que é capaz de se juntar ao mesmo domínio em que se espera que a máquina virtual falhada venha a surgir.

    c. Se o controlador de domínio **não** estiver a funcionar com precisão, tente iniciar sessão na máquina virtual falhada utilizando uma conta de administrador local.
4. Se estiver a utilizar um servidor DNS personalizado, certifique-se de que é acessível. Isto pode ser feito seguindo os passos seguintes:

    a. Criar uma nova máquina virtual na mesma rede e

    b. Verifique se a máquina virtual é capaz de fazer a resolução de nomeusando o Servidor DNS personalizado

>[!Note]
>Permitir qualquer definição que não seja o Boot Diagnostics exigiria que o Agente Azure VM fosse instalado na máquina virtual antes da falha

## <a name="unable-to-open-serial-console-after-failover-of-a-uefi-based-machine-into-azure"></a>Incapaz de abrir consola em série após falha de uma máquina baseada na UEFI em Azure

Se conseguir ligar-se à máquina utilizando RDP ou não conseguir abrir a consola em série, siga os passos abaixo:

* Se a máquina OS for Red Hat ou Oracle Linux 7.*/8.0, execute o seguinte comando no Failover Azure VM com permissões de raiz. Reinicie o VM após o comando.

        grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg

* Se a máquina OS for CentOS 7.*, percorra o seguinte comando no Failover Azure VM com permissões de raiz. Reinicie o VM após o comando.

        grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg

## <a name="unexpected-shutdown-message-event-id-6008"></a>Mensagem de encerramento inesperada (Id do evento 6008)

Ao iniciar uma falha no post do Windows VM, se receber uma mensagem de encerramento inesperada no VM recuperado, indica que um estado de paragem vM não foi capturado no ponto de recuperação utilizado para a falha. Isto acontece quando se recupera a um ponto em que o VM não foi totalmente desligado.

Isto normalmente não é motivo de preocupação e geralmente pode ser ignorado por falhas não planeadas. Se a falha estiver prevista, certifique-se de que o VM é corretamente desligado antes da falha e fornecer tempo suficiente para que os dados de replicação pendentes no local sejam enviados para o Azure. Em seguida, utilize a opção **Mais recente** no [ecrã Failover](site-recovery-failover.md#run-a-failover) para que quaisquer dados pendentes no Azure sejam processados num ponto de recuperação, que é então utilizado para falha de VM.

## <a name="unable-to-select-the-datastore"></a>Incapaz de selecionar a Datastore

Este problema é indicado quando não consegue ver a loja de dados em Azure o portal ao tentar reproteger a máquina virtual que sofreu uma falha. Isto porque o alvo Principal não é reconhecido como uma máquina virtual sob vCenters adicionado à Recuperação do Site Azure.

Para obter mais informações sobre a reproteção de uma máquina vitual, consulte [Reprotect e fail back machines to a site in-local after failover to Azure](vmware-azure-reprotect.md).

Para resolver a questão:

Crie manualmente o alvo Master no vCenter que gere a sua máquina de origem. A loja de dados estará disponível após as próximas operações de descoberta e atualização do tecido vCenter.

> [!Note]
> 
> As operações de descoberta e atualização do tecido podem demorar até 30 minutos para ser concluídas. 

## <a name="linux-master-target-registration-with-cs-fails-with-an-ssl-error-35"></a>Registo de Linux Master Target com CS falha com um erro SSL 35 

O registo principal de recuperação do site Azure com o servidor de configuração falha devido ao proxy autenticado ser ativado no Alvo Principal. 
 
Este erro é indicado pelas seguintes cordas no registo de instalação: 

```
RegisterHostStaticInfo encountered exception config/talwrapper.cpp(107)[post] CurlWrapper Post failed : server : 10.38.229.221, port : 443, phpUrl : request_handler.php, secure : true, ignoreCurlPartialError : false with error: [at curlwrapperlib/curlwrapper.cpp:processCurlResponse:231]   failed to post request: (35) - SSL connect error. 
```

Para resolver a questão:
 
1. No VM do servidor de configuração, abra um pedido de comando e verifique as definições de procuração utilizando os seguintes comandos:

    cat /etc/environment  echo $http_proxy  echo $https_proxy 

2. Se a saída dos comandos anteriores mostrar que as definições de http_proxy ou https_proxy são definidas, utilize um dos seguintes métodos para desbloquear as comunicações Master Target com o servidor de configuração:
   
   - Descarregue a [ferramenta PsExec](https://aka.ms/PsExec).
   - Utilize a ferramenta para aceder ao contexto do utilizador do Sistema e determinar se o endereço proxy está configurado. 
   - Se o proxy estiver configurado, abra o IE num contexto de utilizador do sistema utilizando a ferramenta PsExec.
  
     **psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"**

   - Para garantir que o servidor alvo principal pode comunicar com o servidor de configuração:
  
     - Modifique as definições de procuração no Internet Explorer para contornar o endereço IP do servidor Master Target através do proxy.   
     Ou
     - Desative o proxy no servidor Master Target. 


## <a name="next-steps"></a>Passos seguintes
- Ligação RDP de resolução de problemas [ao Windows VM](../virtual-machines/windows/troubleshoot-rdp-connection.md)
- Ligação SSH de resolução de problemas [ao Linux VM](../virtual-machines/linux/detailed-troubleshoot-ssh-connection.md)

Se precisar de mais ajuda, poste a sua consulta no Fórum de [Recuperação](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) do Site ou deixe um comentário no final deste documento. Temos uma comunidade ativa que deve ser capaz de ajudá-lo.
