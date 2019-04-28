---
title: Executar um teste de recuperação após desastre para o Azure com o Azure Site Recovery | Documentos da Microsoft
description: Saiba mais sobre como executar um teste de recuperação após desastre no local para o Azure com o serviço Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: 6d8ad71894444b3759e506c50244b592ac1f8aac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61471490"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Executar um teste de recuperação após desastre para o Azure 


Este artigo descreve como executar um teste de recuperação após desastre para o Azure com uma ativação pós-falha de teste de recuperação de sites.  

Execute uma ativação pós-falha de teste para validar os replicação e a estratégia de recuperação após desastre, sem qualquer perda de dados ou de indisponibilidade. Uma ativação pós-falha de teste não tem impacto na replicação em curso ou o seu ambiente de produção. Pode executar uma ativação pós-falha de teste numa máquina virtual específica (VM) ou num [plano de recuperação](site-recovery-create-recovery-plans.md) que contém várias VMs.


## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste
Este procedimento descreve como executar uma ativação pós-falha de teste para um plano de recuperação. Se quiser executar uma ativação pós-falha para uma única VM, siga os passos descritos [aqui](tutorial-dr-drill-azure.md#run-a-test-failover-for-a-single-vm)

![Ativação Pós-falha de Teste](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. No Site Recovery no portal do Azure, clique em **planos de recuperação** > *recoveryplan_name* > **ativação pós-falha de teste**.
2. Selecione um **ponto de recuperação** ao qual pretende efetuar a ativação pós-falha. Pode utilizar uma das opções seguintes:
    - **Processado mais recentemente**: Esta opção faz a ativação pós-falha de todas as VMs no plano do ponto de recuperação mais recente processado pelo Site Recovery. Para ver o ponto para uma VM específica de recuperação mais recente, verifique **pontos de recuperação mais recentes** nas definições de VM. Esta opção proporciona um RTO (Objetivo de Tempo de Recuperação) baixo, porque não é despendido tempo ao processar os dados não processados.
    - **Mais recente consistente com a aplicação**: Esta opção faz a ativação pós-falha de todas as VMs no plano para o ponto mais recente recuperação consistente com a aplicação processado pelo Site Recovery. Para ver o ponto para uma VM específica de recuperação mais recente, verifique **pontos de recuperação mais recentes** nas definições de VM.
    - **Mais recente**: Esta opção processa primeiro todos os dados que tenham sido enviados para o serviço de recuperação de Site, para criar um ponto de recuperação para cada VM antes de realizar a ativação pós-falha para o mesmo. Esta opção disponibiliza o último RPO (objetivo de ponto de recuperação), uma vez que a VM criada após a ativação pós-falha terá todos os dados replicados para o Site Recovery quando a ativação pós-falha foi acionada.
    - **MULTI-VM mais recente processado**: Esta opção está disponível para planos de recuperação com uma ou mais VMs que tenham a consistência multi VM ativada. As VMs com a definição ativada com a ativação pós-falha do ponto de recuperação consistente de várias VMS mais recente. Outras VMs com a ativação pós-falha do ponto de recuperação processado mais recente.  
    - **MULTI-VM mais recente consistente com a aplicação**: Esta opção está disponível para planos de recuperação com uma ou mais VMs que tenham a consistência multi VM ativada. VMs que fazem parte de um grupo de replicação a ativação pós-falha do ponto de recuperação consistentes com aplicações de várias VMS mais recente. Outras VMs com a ativação pós-falha para o ponto de recuperação consistente com a aplicação mais recente.
    - **Custom**: Utilize esta opção para efetuar a ativação pós-falha de uma VM específica para um ponto de recuperação específico.
3. Selecione uma rede virtual do Azure na qual as VMs de teste será criado.

    - Testar a tentativas de recuperação de site para criar VMs numa sub-rede com o mesmo nome e o mesmo endereço IP que foi fornecido no **computação e rede** definições da VM.
    - Se uma sub-rede com o mesmo nome não está disponível na rede virtual do Azure utilizada para a ativação pós-falha de teste, em seguida, o teste de VM é criada na primeira sub-rede por ordem alfabética.
    - Se o mesmo endereço IP não está disponível na sub-rede, VM recebe outro endereço IP disponível na sub-rede. [Saiba mais](#create-a-network-for-test-failover).
4. Se estiver a fazer failover para o Azure e a encriptação de dados estiver ativada, no **chave de encriptação**, selecione o certificado que foi emitido quando ativou a encriptação durante a instalação do fornecedor. Pode ignorar este passo se não estiver ativada a encriptação.
5. Controlar o progresso de ativação pós-falha no **tarefas** separador. Deverá conseguir ver a máquina de réplica de teste no portal do Azure.
6. Para iniciar uma ligação de RDP à VM do Azure, precisa [adicionar um endereço IP público](https://aka.ms/addpublicip) na interface de rede da VM.
7. Quando está tudo a funcionar conforme esperado, clique em **ativação pós-falha de teste de limpeza**. Esta ação elimina as VMs que foram criadas durante a ativação pós-falha de teste.
8. Em **Notas**, registe e guarde todas as observações associadas à ativação pós-falha de teste.


![Ativação Pós-falha de Teste](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

Quando uma ativação pós-falha de teste é acionada, ocorre o seguinte:

1. **Pré-requisitos**: Uma verificação dos pré-requisitos é executado para certificar-se de que todas as condições necessárias para a ativação pós-falha são cumpridas.
2. **Ativação pós-falha**: A ativação pós-falha processa e preparar os dados, para que uma VM do Azure podem ser criada a partir do mesmo.
3. **Mais recente**: Se tiver escolhido o último ponto de recuperação, é criado um ponto de recuperação dos dados foi enviado para o serviço.
4. **Iniciar**: Este passo cria uma máquina virtual do Azure com os dados processados no passo anterior.

### <a name="failover-timing"></a>Tempo de ativação pós-falha

Nos seguintes cenários, a ativação pós-falha requer uma etapa extra intermediária que normalmente demora cerca de 8 a 10 minutos para concluir:

* VMs VMware que executam uma versão do serviço de mobilidade com mais de 9.8
* Servidores físicos
* VMs do Linux do VMware
* VM de Hyper-V protegido como servidores físicos
* VM de VMware, onde os seguintes controladores não são controladores de arranque:
    * storvsc
    * VMBus
    * storflt
    * intelide
    * atapi
* VM de VMware que não têm o DHCP ativado, deixou de se estão a utilizar DHCP ou IP estático de endereços.

Em todos os outros casos, não é necessária nenhuma etapa intermediária e ativação pós-falha demora muito menos tempo.


## <a name="create-a-network-for-test-failover"></a>Criar uma rede para ativação pós-falha de teste

Recomendamos que, para a ativação pós-falha de teste, escolha uma rede isolada da rede do site de recuperação de produção específica nas definições **Computação e Rede** para cada VM. Por predefinição, quando cria uma rede virtual do Azure, esta fica isolada das outras redes. A rede de teste deve imitar a sua rede de produção:

- A rede de teste deve ter o mesmo número de sub-redes que a sua rede de produção. As sub-redes devem ter os mesmos nomes.
- A rede de teste deve utilizar o mesmo intervalo de endereços IP.
- Atualize o DNS da rede de teste com o endereço IP especificado para a VM de DNS nas definições **Computação e Rede**. Leia as [considerações sobre a ativação pós-falha do Active Directory](site-recovery-active-directory.md#test-failover-considerations) para obter mais detalhes.


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>Ativação pós-falha de teste para uma rede de produção no site de recuperação

Embora, recomendamos que utilize uma rede de teste separada da sua rede de produção, se pretender testar um teste de recuperação após desastre na sua rede de produção, tenha em atenção o seguinte:

- Certifique-se de que a VM principal está encerrada ao executar a ativação pós-falha de teste. Caso contrário, haverá duas VMs com a mesma identidade, em execução na mesma rede, ao mesmo tempo. Isso pode levar a conseqüências inesperadas.
- Todas as alterações para as VMs criadas para ativação pós-falha de teste serão perdidas quando limpar a ativação pós-falha. Estas alterações não são replicadas para a VM principal.
- Teste no seu ambiente de produção nos leva um tempo de inatividade do seu aplicativo de produção. Os utilizadores não devem utilizar aplicações em execução em VMs quando a ativação pós-falha de teste está em curso.  



## <a name="prepare-active-directory-and-dns"></a>Preparar o Active Directory e DNS

Para executar uma ativação pós-falha de teste para teste de aplicativos, precisa de uma cópia do seu ambiente do Active Directory de produção no seu ambiente de teste. Leia [considerações de ativação pós-falha para o Active Directory de teste](site-recovery-active-directory.md#test-failover-considerations) para saber mais.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar a ligação para VMs do Azure após a ativação pós-falha

Se pretender ligar a VMs do Azure com RDP/SSH após a ativação pós-falha, siga os requisitos resumidos na tabela.

**Ativação pós-falha** | **Localização** | **Ações**
--- | --- | ---
**VM do Azure a executar o Windows** | No computador local antes da ativação pós-falha | Para aceder à VM do Azure através da internet, ative o protocolo RDP e certifique-se de que as regras TCP e UDP estão adicionadas para **pública**, e que o RDP é permitido para todos os perfis de **Firewall do Windows**  >  **Aplicações permitidas**.<br/><br/> Para aceder à VM do Azure através de uma ligação site a site, ative o RDP na máquina e certifique-se de que o RDP é permitido na **Firewall do Windows** -> **aplicações e funcionalidades permitidas**, para **Domínio e privadas** redes.<br/><br/>  Certifique-se de que o sistema operativo de política de SAN está definido como **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135).<br/><br/> Certifique-se de que não há Windows atualizações pendentes na VM quando aciona uma ativação pós-falha. Atualização do Windows pode começar quando efetuar a ativação pós-falha, e não será possível iniciar sessão na VM enquanto a atualização for concluída.
**VM do Azure a executar o Windows** | VM do Azure após a ativação pós-falha |  [Adicione um endereço IP público](https://aka.ms/addpublicip) para a VM.<br/><br/> As regras de grupo de segurança de rede na ativação pós-falha VM (e a sub-rede do Azure à qual está ligado) tem de permitir ligações de entrada para a porta RDP.<br/><br/> Verifique **diagnósticos de arranque** para verificar uma captura de ecrã da VM.<br/><br/> Se não conseguir ligar, verifique que a VM está em execução e revisá-los [sugestões de resolução de problemas](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**VM do Azure em execução no Linux** | No computador local antes da ativação pós-falha | Certifique-se de que o serviço de Secure Shell na VM está definido para ser iniciado automaticamente no arranque do sistema.<br/><br/> Verifique se as regras de firewall permitem uma ligação SSH ao mesmo.
**VM do Azure em execução no Linux** | VM do Azure após a ativação pós-falha | As regras de grupo de segurança de rede na ativação pós-falha VM (e a sub-rede do Azure à qual está ligado) tem de permitir ligações de entrada na porta SSH.<br/><br/> [Adicione um endereço IP público](https://aka.ms/addpublicip) para a VM.<br/><br/> Verifique **diagnósticos de arranque** para uma captura de ecrã da VM.<br/><br/>

Siga os passos descritos [aqui](site-recovery-failover-to-azure-troubleshoot.md) para resolver quaisquer problemas de conectividade após a ativação pós-falha.

## <a name="next-steps"></a>Passos Seguintes
Depois de concluir um teste de recuperação após desastre, saiba mais sobre outros tipos de [ativação pós-falha](site-recovery-failover.md).
