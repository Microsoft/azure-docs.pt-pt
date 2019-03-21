---
title: Resolver problemas de ativação pós-falha para falhas do Azure | Documentos da Microsoft
description: Este artigo descreve formas de resolver erros comuns na ativação pós-falha para o Azure
author: ponatara
manager: abhemraj
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/04/2019
ms.author: mayg
ms.openlocfilehash: 75c97a7feb63a100d322610b7e6d2e5c57bebda2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57889697"
---
# <a name="troubleshoot-errors-when-failing-over-vmware-vm-or-physical-machine-to-azure"></a>Resolver erros ao efetuar a ativação pós-falha da VM de VMware ou máquina física para o Azure

Poderá receber um dos seguintes erros durante o processo de ativação pós-falha de uma máquina virtual para o Azure. Para resolver problemas, utilize os passos descritos para cada condição de erro.

## <a name="failover-failed-with-error-id-28031"></a>Falha na ativação pós-falha com o ID do erro 28031

Recuperação de sites não foi possível criar um com falha através de máquina virtual no Azure. Isso pode acontecer devido a uma das seguintes razões:

* Não existem é uma quota suficiente disponível para criar a máquina virtual: Pode verificar a quota disponível ao aceder à subscrição -> utilização e quotas. Pode abrir um [novo pedido de suporte](https://aka.ms/getazuresupport) para aumentar a quota.

* Está a tentar máquinas de virtuais de ativação pós-falha de famílias de tamanhos diferentes no mesmo conjunto de disponibilidade. Certifique-se de que escolha mesma família de tamanho para todas as máquinas virtuais no mesmo conjunto de disponibilidade. Alterar tamanho acedendo a definições de computação e rede da máquina virtual e, em seguida, repita a ativação pós-falha.

* Há uma diretiva na subscrição que impede a criação de uma máquina virtual. Altere a política para permitir a criação de uma máquina virtual e, em seguida, repita a ativação pós-falha.

## <a name="failover-failed-with-error-id-28092"></a>Falha na ativação pós-falha com o ID do erro 28092

Recuperação de sites não foi possível criar uma interface de rede para a máquina virtual com ativação pós-falha. Certifique-se de que tem quota suficiente disponível para criar interfaces de rede na subscrição. Pode verificar a quota disponível ao aceder à subscrição -> utilização e quotas. Pode abrir um [novo pedido de suporte](https://aka.ms/getazuresupport) para aumentar a quota. Se tiver quota suficiente, em seguida, pode ser um intermitente emitir, repita a operação. Se o problema persistir, mesmo após as repetições, em seguida, deixe um comentário no final deste documento.  

## <a name="failover-failed-with-error-id-70038"></a>Falha na ativação pós-falha com o ID do erro 70038

Recuperação de sites não foi possível criar um com falha através de máquinas de virtuais clássicas no Azure. Ele pode ocorrer porque:

* Um dos recursos, como uma rede virtual que é necessário para a máquina virtual a ser criada não existe. Criar a rede virtual, conforme indicado nas definições de computação e rede da máquina virtual ou modificar a definição a uma rede virtual que já existe e, em seguida, repita a ativação pós-falha.

## <a name="failover-failed-with-error-id-170010"></a>Falha na ativação pós-falha com 170010 de ID de erro

Recuperação de sites não foi possível criar um com falha através de máquina virtual no Azure. Pode ter ocorrido porque uma atividade interna de hidratação falhou para a máquina virtual no local.

Para abrir qualquer máquina no Azure, o ambiente do Azure requer alguns dos controladores no arranque iniciar estado e serviços, como o DHCP esteja no estado de início automático. Portanto, atividade de hidratação, no momento da ativação pós-falha, converte o tipo de arranque de **drivers atapi, intelide, storflt, vmbus e storvsc** arranque inicial. Além disso, converte o tipo de arranque de alguns serviços, como o DHCP para inicialização automática. Esta atividade pode falhar devido a problemas específicos do ambiente. 

Para alterar o tipo de arranque de drivers para manualmente **SO de convidado do Windows**, siga os passos abaixo:

1. [Transferir](https://download.microsoft.com/download/5/D/6/5D60E67C-2B4F-4C51-B291-A97732F92369/Script-no-hydration.ps1) o script de não-hidratação e executá-la como se segue. Este script verifica se a VM requer hidratação.

    `.\Script-no-hydration.ps1`

    Ele fornece o seguinte resultado se hidratação é necessária:

        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

        This system doesn't meet no-hydration requirement.

    No caso da VM cumpre o requisito de não-hidratação, o script fornecerá o resultado de "este sistema cumpre o requisito de não-hidratação". Neste caso, todos os drivers e serviços estão no estado conforme exigido pelo Azure e hidratação na VM não é necessária.

2. Execute o script de não-hidratação-conjunto da seguinte forma, se a VM não cumpre o requisito de não-hidratação.

    `.\Script-no-hydration.ps1 -set`
    
    Isto irá converter o tipo de arranque de drivers e fornecerá o resultado, conforme mostrado abaixo:
    
        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0 

        Updating registry:  REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc   start =  0 

        This system is now no-hydration compatible. 

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Não é possível ligar/RDP/SSH na máquina virtual devido a ativação pós-falha cinzento botão Conectar na máquina virtual

Se o **Connect** botão na ativação pós-falha VM no Azure está a cinzento e não estiver ligado ao Azure através de uma Express Route ou VPN de Site a Site ligação, em seguida,

1. Aceda a **Máquina Virtual** > **redes**, clique no nome da interface de rede necessária.  ![network-interface](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Navegue para **configurações de Ip**, em seguida, clique no campo de nome de configuração de IP necessárias. ![IPConfigurations](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Para ativar o endereço IP público, clique em **ativar**. ![Ativar o IP](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Clique em **configurar definições necessárias** > **criar novo**. ![Criar um novo](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Introduza o nome do endereço público, escolha as opções predefinidas para **SKU** e **atribuição**, em seguida, clique em **OK**.
6. Agora, para guardar as alterações feitas, clique em **guardar**.
7. Feche os painéis e navegue para **descrição geral** secção de máquina virtual para ligar/RDP.

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>Não é possível ligar/RDP/SSH - botão ligação da VM disponível

Se o **Connect** botão na ativação pós-falha VM no Azure está disponível (não a cinzento), em seguida, verificar **diagnósticos de arranque** na sua máquina Virtual e a verificação de erros, conforme listado na [neste artigo](../virtual-machines/windows/boot-diagnostics.md).

1. Se a máquina virtual tiver sido iniciada, experimente fazer failover para um ponto de recuperação mais antigo.
2. Se o aplicativo dentro da máquina virtual não estiver em execução, tente a efetuar ativação pós-falha para um ponto de recuperação consistente com a aplicação.
3. Se a máquina virtual está associado a um domínio, em seguida, certifique-se que esse controlador de domínio está a funcionar com precisão. Isso pode ser feito ao seguir a seguir uma lista de passos:

    a. Crie uma nova máquina virtual na mesma rede.

    b.  Certifique-se de que é capaz de aderir ao mesmo domínio em que a máquina virtual com ativação pós-falha é esperado que surgem.

    c. Se o controlador de domínio estiver **não** funcionar com precisão, em seguida, tente o registo para a máquina virtual através de uma conta de administrador local com ativação pós-falha.
4. Se estiver a utilizar um servidor DNS personalizado, em seguida, certifique-se de que está acessível. Isso pode ser feito ao seguir a seguir uma lista de passos:

    a. Criar uma nova máquina virtual na mesma rede e

    b. Verifique se a máquina virtual é capaz de resolver nomes com o servidor DNS personalizado

>[!Note]
>Ativação de qualquer definição que não seja o diagnóstico de arranque exigiria que o agente da VM do Azure ser instalado na máquina virtual antes da ativação pós-falha

## <a name="unexpected-shutdown-message-event-id-6008"></a>Mensagem de encerramento inesperado (6008 de ID de evento)

Quando a arrancar, uma VM do Windows após a ativação pós-falha, se receber uma mensagem de encerramento inesperado na VM recuperada, ele indica que um Estado de encerramento VM não foi capturado no ponto de recuperação utilizado para a ativação pós-falha. Isto acontece quando recuperar para um ponto quando a VM tinha não foi totalmente encerrada.

Isso normalmente não é preocupante e geralmente pode ser ignorado para ativações pós-falha não planeadas. Se está prevista a ativação pós-falha, certifique-se de que a VM está corretamente encerrada antes da ativação pós-falha e fornecer tempo suficiente para pendentes replicação dados no local a serem enviados para o Azure. Em seguida, utilize o **mais recente** opção a [ecrã de ativação pós-falha](site-recovery-failover.md#run-a-failover) para que todos os dados pendentes no Azure são processados num ponto de recuperação, que, em seguida, é utilizado para a ativação pós-falha da VM.

## <a name="unable-to-select-the-datastore"></a>Não é possível selecionar o arquivo de dados

Este problema é indicado quando não é possível ver o arquivo de dados no Azure portal ao tentar voltar a proteger a máquina virtual que sofreu uma ativação pós-falha. Isto acontece porque o mestre de destino não é reconhecido como uma máquina virtual ao abrigo vCenters adicionado ao Azure Site Recovery.

Para obter mais informações sobre como proteger novamente uma máquina virtual, consulte [voltar a proteger e efetuar a ativação de back-computadores para um site no local após a ativação pós-falha para o Azure](vmware-azure-reprotect.md).

Para resolver o problema:

Criar manualmente o mestre de destino no vCenter que gere a sua máquina de origem. O arquivo de dados estarão disponível após as próxima vCenter deteção e de atualização de recursos de infraestrutura operações.

> [!Note]
> 
> As operações de recursos de infraestrutura de deteção e a atualização podem demorar até 30 minutos a concluir. 

## <a name="linux-master-target-registration-with-cs-fails-with-an-ssl-error-35"></a>Registo de destino principal do Linux com o CS falha com um erro SSL 35 

Falhar o registo de destino de mestre de recuperação de Site do Azure com o servidor de configuração devido ao Proxy autenticado, a ser ativada no destino mestre. 
 
Este erro é indicado pelas seguintes cadeias de caracteres no registo de instalação: 

RegisterHostStaticInfo encontrou exceção config/talwrapper.cpp(107) [postagem] CurlWrapper Post falhou: servidor: 10.38.229.221, porta: phpUrl 443,: request_handler.php, seguro: true, ignoreCurlPartialError: false com o erro: [em curlwrapperlib/curlwrapper.cpp:processCurlResponse:231] Falha ao publicar o pedido: (35) - erro de ligação de SSL. 
 
Para resolver o problema:
 
1. No servidor de configuração VM, abra um prompt de comando e verifique as definições de proxy através dos seguintes comandos:

    cat /etc/environment  echo $http_proxy  echo $https_proxy 

2. Se o resultado dos comandos anteriores mostra que o http_proxy ou https_proxy definições são definidas, utilize um dos seguintes métodos para desbloquear as comunicações de destino principal com o servidor de configuração:
   
   - Transfira o [PsExec ferramenta](https://aka.ms/PsExec).
   - Utilize a ferramenta para acessar o contexto de utilizador do sistema e determinar se o endereço de proxy está configurado. 
   - Se o proxy está configurado, abra o IE num contexto de utilizador do sistema usando a ferramenta PsExec.
  
     **psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"**

   - Para se certificar de que o servidor de destino mestre consegue comunicar com o servidor de configuração:
  
     - Modifica as definições de proxy no Internet Explorer para ignorar o endereço IP do servidor de destino principal através do proxy.   
     Ou
     - Desative o proxy no servidor de destino principal. 


## <a name="next-steps"></a>Passos Seguintes
- Resolver problemas de [ligação de RDP à VM do Windows](../virtual-machines/windows/troubleshoot-rdp-connection.md)
- Resolver problemas de [ligação SSH à VM do Linux](../virtual-machines/linux/detailed-troubleshoot-ssh-connection.md)

Se precisar de mais ajuda, em seguida, publique sua consulta no [fórum de recuperação de Site](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) ou deixe um comentário no final deste documento. Temos uma Comunidade ativa que deve ser capaz de ajudá-lo.
