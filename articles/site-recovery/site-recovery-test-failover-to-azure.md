---
title: Faça um teste de failover (broca de recuperação de desastres) para Azure em Recuperação do Local azure
description: Saiba mais sobre a execução de um teste de failover de in-instalações para Azure, utilizando o serviço de recuperação do site Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 26c734b7a2e9f5592ee6d51dfee4650a3998ab1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257527"
---
# <a name="run-a-test-failover-disaster-recovery-drill-to-azure"></a>Faça um teste failover (broca de recuperação de desastres) para Azure 


Este artigo descreve como executar um exercício de recuperação de desastres para Azure, usando um teste de recuperação do site failover.  

Executa um teste falhado para validar a sua estratégia de replicação e recuperação de desastres, sem qualquer perda de dados ou tempo de inatividade. Uma falha no teste não afeta a replicação em curso, nem o seu ambiente de produção. Pode executar uma falha de teste numa máquina virtual específica (VM), ou num plano de [recuperação](site-recovery-create-recovery-plans.md) contendo vários VMs.


## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste
Este procedimento descreve como executar um teste falhado para um plano de recuperação. Se quiser fazer um teste falhado para um único VM, siga os passos descritos [aqui](tutorial-dr-drill-azure.md#run-a-test-failover-for-a-single-vm)

![Ativação Pós-falha de Teste](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. Na recuperação do site no portal Azure, clique em **Planos** > de Recuperação*recoveryplan_name* > **Falha de Teste**.
2. Selecione um **Ponto de Recuperação** para o qual falhar. Pode utilizar uma das opções seguintes:
    - **Mais recente processado**: Esta opção falha em todos os VMs no plano para o mais recente ponto de recuperação processado pela Recuperação do Site. Para ver o mais recente ponto de recuperação de um VM específico, verifique os **últimos pontos** de recuperação nas definições vM. Esta opção proporciona um RTO (Objetivo de Tempo de Recuperação) baixo, porque não é despendido tempo ao processar os dados não processados.
    - **Mais recente aplicação consistente**: Esta opção falha em todos os VMs no plano para o mais recente ponto de recuperação consistente de aplicações processado pela Recovery do Site. Para ver o mais recente ponto de recuperação de um VM específico, verifique os **últimos pontos** de recuperação nas definições vM.
    - **Mais recente**: Esta opção processa primeiro todos os dados que foram enviados para o serviço de Recuperação do Site, para criar um ponto de recuperação para cada VM antes de falhar. Esta opção fornece o RPO mais baixo (Objetivo do Ponto de Recuperação), porque o VM criado após a falha terá todos os dados replicados para a Recuperação do Local quando a falha foi desencadeada.
    - **Mais recente processo multi-VM**: Esta opção está disponível para planos de recuperação com um ou mais VMs que têm consistência multi-VM ativada. Os VMs com a definição ativada falham até ao último ponto de recuperação consistente multi-VM comum. Outros VMs falham até ao último ponto de recuperação processado.  
    - **Mais recente aplicação multi-VM consistente**: Esta opção está disponível para planos de recuperação com um ou mais VMs que têm consistência multi-VM ativada. VMs que fazem parte de um grupo de replicação falham até ao mais recente ponto de recuperação consistente com aplicação multi-VM comum. Outros VMs falham no seu mais recente ponto de recuperação consistente com aplicações.
    - **Personalizado**: Utilize esta opção para falhar sobre um VM específico para um determinado ponto de recuperação.
3. Selecione uma rede virtual Azure na qual serão criados VMs de teste.

    - A Recuperação do Site tenta criar VMs de teste numa subnet com o mesmo nome e mesmo endereço IP que o fornecido nas definições de **Compute e Rede** do VM.
    - Se uma sub-rede com o mesmo nome não estiver disponível na rede virtual Azure utilizada para o teste failover, então o VM de teste é criado na primeira sub-rede alfabéticamente.
    - Se o mesmo endereço IP não estiver disponível na sub-rede, então o VM recebe outro endereço IP disponível na sub-rede. [Saiba mais](#create-a-network-for-test-failover).
4. Se estiver a falhar com o Azure e a encriptação de dados estiver ativada, na **Chave de Encriptação,** selecione o certificado que foi emitido quando ativou a encriptação durante a instalação do Fornecedor. Pode ignorar este passo se a encriptação não estiver ativada.
5. Falha no progresso no separador **Jobs.** Deve poder ver a máquina de réplica de teste no portal Azure.
6. Para iniciar uma ligação RDP ao VM Azure, é necessário [adicionar um endereço IP público](https://aka.ms/addpublicip) na interface de rede do VM falhado.
7. Quando tudo estiver a funcionar como esperado, clique em **failover**do teste de limpeza . Isto elimina os VMs que foram criados durante a falha do teste.
8. Em **Notas,** grave e guarde quaisquer observações associadas ao failover do teste.


![Ativação Pós-falha de Teste](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

Quando um teste é acionado, ocorre o seguinte:

1. **Pré-requisitos**: Uma verificação prévia corre para garantir que todas as condições necessárias para a falha são satisfeitas.
2. **Failover**: Os processos de failover e preparados os dados, para que um VM Azure possa ser criado a partir dele.
3. **Mais recente**: Se escolheu o último ponto de recuperação, é criado um ponto de recuperação a partir dos dados enviados para o serviço.
4. **Início**: Este passo cria uma máquina virtual Azure utilizando os dados tratados no passo anterior.

### <a name="failover-timing"></a>Tempo de failover

Nos seguintes cenários, a failover requer um passo intermédio extra que normalmente demora cerca de 8 a 10 minutos para completar:

* VMware VMs executando uma versão do serviço mobility com mais de 9.8
* Servidores físicos
* VMware Linux VMs
* VM hiper-V protegido como servidores físicos
* VMware VM onde os seguintes condutores não são condutores de botas:
    * storvsc
    * vmbus
    * storflt
    * intelide
    * atapi
* VMware VM que não tem DHCP ativado , independentemente de estarem a usar endereços IP DHCP ou estáticos.

Em todos os outros casos, não é necessário um passo intermédio e a falha demora significativamente menos tempo.


## <a name="create-a-network-for-test-failover"></a>Criar uma rede para ativação pós-falha de teste

Recomendamos que, para a ativação pós-falha de teste, escolha uma rede isolada da rede do site de recuperação de produção específica nas definições **Computação e Rede** para cada VM. Por predefinição, quando cria uma rede virtual do Azure, esta fica isolada das outras redes. A rede de teste deve imitar a sua rede de produção:

- A rede de teste deve ter o mesmo número de sub-redes que a sua rede de produção. As sub-redes devem ter os mesmos nomes.
- A rede de teste deve utilizar o mesmo intervalo de endereços IP.
- Atualize o DNS da rede de teste com o endereço IP especificado para a VM de DNS nas definições **Computação e Rede**. Leia as [considerações sobre a ativação pós-falha do Active Directory](site-recovery-active-directory.md#test-failover-considerations) para obter mais detalhes.


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>Falha de teste numa rede de produção no local de recuperação

Embora tenhamos recomendado que utilize uma rede de teste separada da sua rede de produção, se pretender testar um exercício de recuperação de desastres na sua rede de produção, note o seguinte:

- Certifique-se de que o VM primário é desligado quando executar o teste falhar. Caso contrário, haverá dois VMs com a mesma identidade, funcionando na mesma rede ao mesmo tempo. Isto pode levar a consequências inesperadas.
- Quaisquer alterações aos VMs criadas para falhas no teste perdem-se quando se limpa a falha. Estas alterações não são replicadas de volta ao VM primário.
- Os testes no seu ambiente de produção conduzem a um tempo de inatividade da sua aplicação de produção. Os utilizadores não devem utilizar aplicações em VMs quando o teste está em curso.  



## <a name="prepare-active-directory-and-dns"></a>Preparar Diretório Ativo e DNS

Para executar uma falha no teste de teste de aplicação, precisa de uma cópia do seu ambiente de diretório ativo de produção no seu ambiente de teste. Leia as considerações de [failover do teste para o Diretório Ativo](site-recovery-active-directory.md#test-failover-considerations) para saber mais.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar a ligação para VMs do Azure após a ativação pós-falha

Se pretender ligar-se aos VMs Azure utilizando RDP/SSH após a falha, siga os requisitos resumidos na tabela.

**Ativação pós-falha** | **Localização** | **Ações**
--- | --- | ---
**Vitrina Azure VM executando janelas** | Máquina no local antes da falha | Para aceder ao Azure VM através da internet, ativar o RDP e certificar-se de que as regras de TCP e UDP são adicionadas para **publicamente**, e que o RDP é permitido para todos os perfis em**Aplicações permitidas**pelo **Windows Firewall** > .<br/><br/> Para aceder ao Azure VM sobre uma ligação site-to-site, ative rdP na máquina e certifique-se de que o RDP é permitido nas**aplicações e funcionalidades permitidas**pelo **Windows Firewall,** -> para redes **De domínio e privadas.**<br/><br/>  Certifique-se de que a política SAN do sistema operativo está definida para **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135).<br/><br/> Certifique-se de que não existem atualizações do Windows pendentes no VM quando acionar uma falha. A atualização do Windows poderá começar quando falhar novamente e não poderá entrar no VM até que a atualização esteja concluída.
**Vitrina Azure VM executando janelas** | VM Azure após falha |  [Adicione um endereço IP público](https://aka.ms/addpublicip) para a VM.<br/><br/> As regras do grupo de segurança da rede sobre o VM falhado (e a subnet Azure a que está ligada) precisam de permitir a entrada de ligações à porta RDP.<br/><br/> Verifique os **diagnósticos da Boot** para verificar uma imagem do VM.<br/><br/> Se não conseguir ligar, verifique se o VM está em execução e reveja estas dicas de resolução de [problemas.](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)
**Azure VM executando Linux** | Máquina no local antes da falha | Certifique-se de que o serviço Secure Shell no VM está programado para começar automaticamente no arranque do sistema.<br/><br/> Verifique se as regras de firewall permitem uma ligação SSH ao mesmo.
**Azure VM executando Linux** | VM Azure após falha | As regras do grupo de segurança da rede sobre o VM falhado (e a subnet Azure a que está ligada) precisam de permitir a entrada de ligações à porta SSH.<br/><br/> [Adicione um endereço IP público](https://aka.ms/addpublicip) para a VM.<br/><br/> Verifique os **diagnósticos** da Boot para obter uma imagem do VM.<br/><br/>

Siga os passos descritos [aqui](site-recovery-failover-to-azure-troubleshoot.md) para resolver quaisquer problemas de conectividade após a ativação pós-falha.

## <a name="next-steps"></a>Passos seguintes
Depois de completar um exercício de recuperação de desastres, saiba mais sobre outros tipos de [falhas.](site-recovery-failover.md)
