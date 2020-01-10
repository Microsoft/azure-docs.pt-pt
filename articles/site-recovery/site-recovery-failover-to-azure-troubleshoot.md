---
title: Solucionar problemas de failover para falhas do Azure | Microsoft Docs
description: Este artigo descreve maneiras de solucionar erros comuns no failover para o Azure
author: ponatara
manager: abhemraj
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 01/08/2020
ms.author: mayg
ms.openlocfilehash: 6de37daa0b9e0ebc711a5dacbdce352e3675a3db
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754419"
---
# <a name="troubleshoot-errors-when-failing-over-vmware-vm-or-physical-machine-to-azure"></a>Solucionar erros ao fazer failover da VM VMware ou da máquina física para o Azure

Você pode receber um dos seguintes erros durante o failover de uma máquina virtual para o Azure. Para solucionar problemas, use as etapas descritas para cada condição de erro.

## <a name="failover-failed-with-error-id-28031"></a>Falha no failover com a ID de erro 28031

Site Recovery não pôde criar uma máquina virtual com failover no Azure. Isso pode ocorrer devido a um dos seguintes motivos:

* Não há cota suficiente disponível para criar a máquina virtual: você pode verificar a cota disponível acessando assinatura-> uso + cotas. Você pode abrir uma [nova solicitação de suporte](https://aka.ms/getazuresupport) para aumentar a cota.

* Você está tentando fazer failover de máquinas virtuais de famílias de tamanho diferentes no mesmo conjunto de disponibilidade. Certifique-se de escolher a mesma família de tamanho para todas as máquinas virtuais no mesmo conjunto de disponibilidade. Altere o tamanho indo para as configurações de computação e rede da máquina virtual e, em seguida, repita o failover.

* Há uma política na assinatura que impede a criação de uma máquina virtual. Altere a política para permitir a criação de uma máquina virtual e, em seguida, repita o failover.

## <a name="failover-failed-with-error-id-28092"></a>Falha no failover com a ID de erro 28092

Site Recovery não pôde criar um adaptador de rede para a máquina virtual com failover. Verifique se você tem uma cota suficiente disponível para criar interfaces de rede na assinatura. Você pode verificar a cota disponível acessando assinatura-> uso + cotas. Você pode abrir uma [nova solicitação de suporte](https://aka.ms/getazuresupport) para aumentar a cota. Se você tiver uma cota suficiente, isso pode ser um problema intermitente, tente a operação novamente. Se o problema persistir mesmo após novas tentativas, deixe um comentário no final deste documento.  

## <a name="failover-failed-with-error-id-70038"></a>Falha no failover com a ID de erro 70038

Site Recovery não pôde criar uma máquina virtual clássica com failover no Azure. Isso pode acontecer porque:

* Um dos recursos, como uma rede virtual necessária para a criação da máquina virtual, não existe. Crie a rede virtual conforme fornecido nas configurações de computação e rede da máquina virtual ou modifique a configuração para uma rede virtual que já existe e, em seguida, repita o failover.

## <a name="failover-failed-with-error-id-170010"></a>Falha no failover com a ID de erro 170010

Site Recovery não pôde criar uma máquina virtual com failover no Azure. Isso pode acontecer porque uma atividade interna de hidratação falhou para a máquina virtual local.

Para abrir qualquer computador no Azure, o ambiente do Azure exige que alguns dos drivers estejam no estado de inicialização e serviços como o DHCP para estar no estado de inicialização automática. Portanto, a atividade hidratação, no momento do failover, converte o tipo de inicialização dos **drivers ATAPI, intelide, storflt, VMBus e storvsc** para inicialização. Ele também converte o tipo de inicialização de alguns serviços como DHCP para inicialização automática. Essa atividade pode falhar devido a problemas específicos do ambiente. 

Para alterar manualmente o tipo de inicialização de drivers para o **sistema operacional convidado do Windows**, siga as etapas abaixo:

1. [Baixe](https://download.microsoft.com/download/5/D/6/5D60E67C-2B4F-4C51-B291-A97732F92369/Script-no-hydration.ps1) o script no-hidratação e execute-o da seguinte maneira. Esse script verifica se a VM requer hidratação.

    `.\Script-no-hydration.ps1`

    Isso fornecerá o seguinte resultado se hidratação for necessário:

        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

        This system doesn't meet no-hydration requirement.

    Caso a VM atenda a um requisito no-hidratação, o script fornecerá o resultado "este sistema atende ao requisito no-hidratação". Nesse caso, todos os drivers e serviços estão no estado conforme exigido pelo Azure e hidratação na VM não é necessário.

2. Execute o script no-hidratação-set da seguinte maneira se a VM não atender ao requisito no-hidratação.

    `.\Script-no-hydration.ps1 -set`
    
    Isso converterá o tipo de inicialização de drivers e fornecerá o resultado como abaixo:
    
        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0 

        Updating registry:  REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc   start =  0 

        This system is now no-hydration compatible. 

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Não é possível conectar/RDP/SSH à máquina virtual com failover devido ao botão de conexão esmaecida na máquina virtual

Se o botão **conectar** na VM com failover no Azure estiver esmaecido e você não estiver conectado ao Azure por meio de uma rota expressa ou conexão VPN site a site, então

1. Vá para **máquina Virtual** > **rede**, clique no nome da interface de rede necessária.  ![network-interface](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Navegue até **configurações de IP**e clique no campo nome da configuração de IP necessária. ![IPConfigurations](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Para habilitar o endereço IP público, clique em **habilitar**. ![habilitar](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png) IP
4. Clique em **definir as configurações necessárias** > **criar nova**. ![criar novo](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Digite o nome do endereço público, escolha as opções padrão para **SKU** e **atribuição**e clique em **OK**.
6. Agora, para salvar as alterações feitas, clique em **salvar**.
7. Feche os painéis e navegue até a seção **visão geral** da máquina virtual para conectar/RDP.

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>Não é possível conectar/RDP/SSH-botão conectar da VM disponível

Se o botão **conectar** na VM com failover no Azure estiver disponível (não esmaecido), verifique o **diagnóstico de inicialização** em sua máquina virtual e verifique se há erros, conforme listado neste [artigo](../virtual-machines/windows/boot-diagnostics.md).

1. Se a máquina virtual não tiver sido iniciada, tente fazer failover para um ponto de recuperação mais antigo.
2. Se o aplicativo dentro da máquina virtual não estiver ativo, tente fazer failover para um ponto de recuperação consistente com o aplicativo.
3. Se a máquina virtual estiver ingressada no domínio, verifique se o controlador de domínio está funcionando com precisão. Isso pode ser feito seguindo as etapas fornecidas abaixo:

    a. Crie uma nova máquina virtual na mesma rede.

    b.  Verifique se é possível ingressar no mesmo domínio em que se espera que a máquina virtual com failover apareça.

    c. Se o controlador de domínio **não** estiver funcionando corretamente, tente fazer logon na máquina virtual com failover usando uma conta de administrador local.
4. Se você estiver usando um servidor DNS personalizado, verifique se ele está acessível. Isso pode ser feito seguindo as etapas fornecidas abaixo:

    a. Criar uma nova máquina virtual na mesma rede e

    b. Verifique se a máquina virtual é capaz de realizar a resolução de nomes usando o servidor DNS personalizado

>[!Note]
>Habilitar qualquer configuração diferente do diagnóstico de inicialização exigiria que o agente de VM do Azure fosse instalado na máquina virtual antes do failover

## <a name="unable-to-open-serial-console-after-failover-of-a-uefi-based-machine-into-azure"></a>Não é possível abrir o console serial após o failover de um computador baseado em UEFI no Azure

Se você conseguir se conectar ao computador usando o RDP, mas não puder abrir o console serial, siga as etapas abaixo:

* Se o sistema operacional do computador for Red Hat ou Oracle Linux 7. */8.0, execute o seguinte comando na VM do Azure de failover com permissões de raiz. Reinicialize a VM após o comando.

        grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg

* Se o sistema operacional do computador for CentOS 7. *, execute o comando a seguir na VM do Azure de failover com permissões de raiz. Reinicialize a VM após o comando.

        grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg

## <a name="unexpected-shutdown-message-event-id-6008"></a>Mensagem de desligamento inesperada (ID do evento 6008)

Ao inicializar uma VM do Windows após o failover, se você receber uma mensagem de desligamento inesperada na VM recuperada, isso indica que um estado de desligamento da VM não foi capturado no ponto de recuperação usado para failover. Isso acontece quando você se recupera em um ponto em que a VM não foi completamente desligada.

Isso normalmente não é uma causa de preocupação e, em geral, pode ser ignorado para failovers não planejados. Se o failover for planejado, verifique se a VM está desligada corretamente antes do failover e forneça tempo suficiente para os dados de replicação pendentes locais a serem enviados ao Azure. Em seguida, use a opção **mais recente** na [tela de failover](site-recovery-failover.md#run-a-failover) para que todos os dados pendentes no Azure sejam processados em um ponto de recuperação, que é usado para failover de VM.

## <a name="unable-to-select-the-datastore"></a>Não é possível selecionar o repositório de armazenamento

Esse problema é indicado quando você não consegue ver o repositório de armazenamento no portal do Azure ao tentar proteger novamente a máquina virtual que sofreu um failover. Isso ocorre porque o destino mestre não é reconhecido como uma máquina virtual em vCenters adicionado a Azure Site Recovery.

Para obter mais informações sobre como proteger novamente um computador virtual, consulte [proteger novamente e executar failback de computadores em um site local após o failover para o Azure](vmware-azure-reprotect.md).

Para resolver o problema:

Crie manualmente o destino mestre no vCenter que gerencia o computador de origem. O repositório de armazenamento estará disponível após as próximas operações do vCenter Discovery e do Refresh Fabric.

> [!Note]
> 
> As operações de descoberta e de malha de atualização podem levar até 30 minutos para serem concluídas. 

## <a name="linux-master-target-registration-with-cs-fails-with-an-ssl-error-35"></a>O registro de destino mestre do Linux com o CS falha com um erro de SSL 35 

O registro de destino mestre Azure Site Recovery com o servidor de configuração falha devido à habilitação do proxy autenticado no destino mestre. 
 
Esse erro é indicado pelas seguintes cadeias de caracteres no log de instalação: 

```
RegisterHostStaticInfo encountered exception config/talwrapper.cpp(107)[post] CurlWrapper Post failed : server : 10.38.229.221, port : 443, phpUrl : request_handler.php, secure : true, ignoreCurlPartialError : false with error: [at curlwrapperlib/curlwrapper.cpp:processCurlResponse:231]   failed to post request: (35) - SSL connect error. 
```

Para resolver o problema:
 
1. Na VM do servidor de configuração, abra um prompt de comando e verifique as configurações de proxy usando os seguintes comandos:

    cat /etc/environment  echo $http_proxy  echo $https_proxy 

2. Se a saída dos comandos anteriores mostrar que as configurações http_proxy ou https_proxy estão definidas, use um dos seguintes métodos para desbloquear as comunicações de destino mestre com o servidor de configuração:
   
   - Baixe a [ferramenta PsExec](https://aka.ms/PsExec).
   - Use a ferramenta para acessar o contexto de usuário do sistema e determinar se o endereço do proxy está configurado. 
   - Se o proxy estiver configurado, abra o IE em um contexto de usuário do sistema usando a ferramenta PsExec.
  
     **PsExec-s-i "%programfiles%\Internet Explorer\iexplore.exe"**

   - Para garantir que o servidor de destino mestre possa se comunicar com o servidor de configuração:
  
     - Modifique as configurações de proxy no Internet Explorer para ignorar o endereço IP do servidor de destino mestre por meio do proxy.   
     Ou
     - Desabilite o proxy no servidor de destino mestre. 


## <a name="next-steps"></a>Passos seguintes
- Solucionar problemas [de conexão RDP com a VM do Windows](../virtual-machines/windows/troubleshoot-rdp-connection.md)
- Solucionar problemas [de conexão SSH para VM Linux](../virtual-machines/linux/detailed-troubleshoot-ssh-connection.md)

Se precisar de mais ajuda, poste sua consulta no [fórum site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) ou deixe um comentário no final deste documento. Temos uma comunidade ativa que deve ser capaz de ajudá-lo.
