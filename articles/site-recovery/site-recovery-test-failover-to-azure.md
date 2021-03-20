---
title: Executar um teste de failover (exercício de recuperação de desastres) para Azure na Recuperação do Local de Azure
description: Saiba como executar um teste de failover de no local para Azure, utilizando o serviço de Recuperação do Local Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 815a35c167bbcd3ac03dfdaaf6d699e58a791f33
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92369391"
---
# <a name="run-a-test-failover-disaster-recovery-drill-to-azure"></a>Executar um teste de failover (simulacro de recuperação de desastres) para Azure 


Este artigo descreve como executar um exercício de recuperação de desastres para Azure, usando um teste de recuperação do local.  

Faz um teste falhado para validar a sua estratégia de replicação e recuperação de desastres, sem qualquer perda de dados ou tempo de inatividade. Um teste de falha não afeta a replicação contínua, ou o seu ambiente de produção. Pode executar um teste de falha numa máquina virtual específica (VM), ou num [plano de recuperação](site-recovery-create-recovery-plans.md) contendo vários VMs.


## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste
Este procedimento descreve como executar um teste de failover para um plano de recuperação. Se quiser executar um teste de failover para um único VM, siga os passos descritos [aqui](tutorial-dr-drill-azure.md#run-a-test-failover-for-a-single-vm)

![Screenshot da página de failover test no portal Azure.](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. Na Recuperação do Local no portal Azure, clique em **Planos de Recuperação**  >  *recoveryplan_name*  >  **Teste Failover**.
2. Selecione um **Ponto de Recuperação** para o qual falhar. Pode utilizar uma das opções seguintes:
    - **Mais recente processo**: Esta opção falha em todos os VMs do plano até ao último ponto de recuperação processado pela Recuperação do Site. Para ver o último ponto de recuperação de um VM específico, consulte os **pontos de recuperação mais recentes** nas definições de VM. Esta opção proporciona um RTO (Objetivo de Tempo de Recuperação) baixo, porque não é despendido tempo ao processar os dados não processados.
    - **Aplicações mais recentes consistentes**: Esta opção falha em todos os VMs do plano para o mais recente ponto de recuperação consistente com aplicações processado pela Recuperação do Site. Para ver o último ponto de recuperação de um VM específico, consulte os **pontos de recuperação mais recentes** nas definições de VM.
    - **Mais recente**: Esta opção processa primeiro todos os dados enviados para o serviço de Recuperação de Sítios, para criar um ponto de recuperação para cada VM antes de falhar. Esta opção fornece o RPO mais baixo (Objetivo de Ponto de Recuperação), porque o VM criado após o failover terá todos os dados replicados para a Recuperação do Local quando a falha foi desencadeada.
    - **Processo multi-VM mais recente**: Esta opção está disponível para planos de recuperação com um ou mais VMs que têm consistência multi-VM ativada. Os VMs com a definição ativa falham no último ponto de recuperação consistente multi-VM comum. Outros VMs falham no último ponto de recuperação processado.  
    - **Aplicações multi-VM mais recentes consistentes**: Esta opção está disponível para planos de recuperação com um ou mais VMs que têm consistência multi-VM ativada. Os VMs que fazem parte de um grupo de replicação falham no mais recente ponto de recuperação comum multi-VM consistente com aplicações. Outros VMs falham no seu último ponto de recuperação consistente com aplicações.
    - **Costume**: Utilize esta opção para falhar sobre um VM específico para um determinado ponto de recuperação.
3. Selecione uma rede virtual Azure na qual serão criados VMs de teste.

    - A Recuperação do Site tenta criar VMs de teste numa sub-rede com o mesmo nome e mesmo endereço IP que o fornecido nas definições de **Computação e Rede** do VM.
    - Se uma sub-rede com o mesmo nome não estiver disponível na rede virtual Azure utilizada para o teste de failover, então o VM de teste é criado na primeira sub-rede alfabeticamente.
    - Se o mesmo endereço IP não estiver disponível na sub-rede, então o VM recebe outro endereço IP disponível na sub-rede. [Saiba mais](#create-a-network-for-test-failover).
4. Se estiver a falhar no Azure e a encriptação de dados estiver ativada, na **Chave de Encriptação,** selecione o certificado que foi emitido quando ativar a encriptação durante a instalação do Fornecedor. Pode ignorar este passo se a encriptação não estiver ativada.
5. Track failover progress on the **Jobs** tab. Deverá ser capaz de ver a máquina de réplica de teste no portal Azure.
6. Para iniciar uma ligação RDP ao Azure VM, é necessário [adicionar um endereço IP público](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) na interface de rede do VM falhado.
7. Quando tudo estiver a funcionar como esperado, clique em **Teste de Limpeza failover**. Isto elimina os VMs que foram criados durante o teste failover.
8. Em **Notas,** registem e guarde quaisquer observações associadas ao teste.


![Screenshot do teste falhando a conta Jobs.](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

Quando um teste de falha é desencadeado, ocorre o seguinte:

1. **Pré-requisitos**: Uma verificação prévia dos requisitos é para garantir que todas as condições necessárias para o failover sejam satisfeitas.
2. **Failover**: Os processos de failover e prepararam os dados, para que um VM Azure possa ser criado a partir do mesmo.
3. **Mais recente**: Se escolheu o último ponto de recuperação, é criado um ponto de recuperação a partir dos dados enviados para o serviço.
4. **Início**: Este passo cria uma máquina virtual Azure utilizando os dados processados no passo anterior.

### <a name="failover-timing"></a>Tempo de failover

Nos seguintes cenários, o failover requer um passo intermédio extra que normalmente demora cerca de 8 a 10 minutos para completar:

* VMware VMs executando uma versão do serviço de Mobilidade com mais de 9.8
* Servidores físicos
* VMware Linux VMs
* VM hiper-V protegido como servidores físicos
* VMware VM onde os seguintes condutores não são condutores de arranque:
    * storvsc
    * vmbus
    * storflt
    * inteleto
    * atapi
* VMware VM que não tem DHCP habilitado , independentemente de estarem a usar endereços IP DHCP ou estáticos.

Em todos os outros casos, não é necessário nenhum passo intermédio, e o failover leva significativamente menos tempo.


## <a name="create-a-network-for-test-failover"></a>Criar uma rede para ativação pós-falha de teste

Recomendamos que, para a ativação pós-falha de teste, escolha uma rede isolada da rede do site de recuperação de produção específica nas definições **Computação e Rede** para cada VM. Por predefinição, quando cria uma rede virtual do Azure, esta fica isolada das outras redes. A rede de teste deve imitar a sua rede de produção:

- A rede de teste deve ter o mesmo número de sub-redes que a sua rede de produção. As sub-redes devem ter os mesmos nomes.
- A rede de teste deve utilizar o mesmo intervalo de endereços IP.
- Atualize o DNS da rede de teste com o endereço IP especificado para a VM de DNS nas definições **Computação e Rede**. Leia as [considerações sobre a ativação pós-falha do Active Directory](site-recovery-active-directory.md#test-failover-considerations) para obter mais detalhes.


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>Teste falha numa rede de produção no local de recuperação

Embora tenhamos recomendado que utilize uma rede de teste separada da sua rede de produção, se pretender testar uma broca de recuperação de desastres na sua rede de produção, note o seguinte:

- Certifique-se de que o VM primário é desligado quando executar o teste de falha. Caso contrário, haverá dois VMs com a mesma identidade, funcionando na mesma rede ao mesmo tempo. Isto pode levar a consequências inesperadas.
- Quaisquer alterações aos VMs criadas para a falha do teste perdem-se quando limpa a falha. Estas alterações não são replicadas de volta ao VM primário.
- Os testes no seu ambiente de produção conduzem a um tempo de inatividade da sua aplicação de produção. Os utilizadores não devem utilizar aplicações em execução em VMs quando o teste está em curso.  



## <a name="prepare-active-directory-and-dns"></a>Preparar Diretório Ativo e DNS

Para executar um teste de failover para testes de aplicação, precisa de uma cópia do ambiente ative de produção no seu ambiente de teste. Leia [considerações de failover de teste para o Ative Directory](site-recovery-active-directory.md#test-failover-considerations) para saber mais.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar a ligação para VMs do Azure após a ativação pós-falha

Se pretender ligar-se aos VMs Azure utilizando RDP/SSH após a falha, siga os requisitos resumidos na tabela.

**Ativação pós-falha** | **Localização** | **Ações**
--- | --- | ---
**Azure VM executando janelas** | Máquina no local antes do failover | Para aceder ao Azure VM através da internet, ative o RDP e certifique-se de que as regras TCP e UDP são adicionadas ao **Público**, e que o PDR é permitido para todos os perfis em Apps Permitidas do Windows **Firewall**  >  .<br/><br/> Para aceder ao Azure VM sobre uma ligação site-to-site, ative o RDP na máquina e certifique-se de que o RDP é permitido nas  ->  **aplicações e funcionalidades permitidas** pelo Windows Firewall , para redes **de domínio e privado.**<br/><br/>  Certifique-se de que a política do sistema operativo SAN está definida para **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135).<br/><br/> Certifique-se de que não existem atualizações do Windows pendentes no VM quando desencadear uma falha. A atualização do Windows pode começar quando falhar e não poderá iniciar sessão no VM até que a atualização esteja concluída.
**Azure VM executando janelas** | Azure VM após falha |  [Adicione um endereço IP público](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) para a VM.<br/><br/> As regras do grupo de segurança da rede sobre o falhado sobre o VM (e a sub-rede Azure à qual está ligada) precisam de permitir a entrada de ligações à porta RDP.<br/><br/> Verifique **os diagnósticos da Boot** para verificar uma imagem do VM.<br/><br/> Se não conseguir ligar, verifique se o VM está em funcionamento e reveja estas [dicas de resolução de problemas](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Azure VM executando Linux** | Máquina no local antes do failover | Certifique-se de que o serviço Secure Shell no VM está programado para iniciar automaticamente no arranque do sistema.<br/><br/> Verifique se as regras de firewall permitem uma ligação SSH ao mesmo.
**Azure VM executando Linux** | Azure VM após falha | As regras do grupo de segurança da rede sobre o falhado sobre o VM (e a sub-rede Azure à qual está ligada) precisam de permitir a entrada de ligações à porta SSH.<br/><br/> [Adicione um endereço IP público](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) para a VM.<br/><br/> Verifique **os diagnósticos da Boot** para obter uma imagem do VM.<br/><br/>

Siga os passos descritos [aqui](site-recovery-failover-to-azure-troubleshoot.md) para resolver quaisquer problemas de conectividade após a ativação pós-falha.

## <a name="next-steps"></a>Passos seguintes
Depois de completar um exercício de recuperação de desastres, saiba mais sobre outros tipos de [falha.](site-recovery-failover.md)