---
title: Executar uma análise de recuperação de desastre no Azure usando Azure Site Recovery
description: Saiba mais sobre como executar uma análise de recuperação de desastre do local para o Azure, usando o serviço de Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 8342f60d8a0f91cc4807d25307510c1cbe7ee5c8
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814366"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Executar um teste de recuperação após desastre para o Azure 


Este artigo descreve como executar uma análise de recuperação de desastre no Azure, usando um failover de teste Site Recovery.  

Você executa um failover de teste para validar sua estratégia de replicação e recuperação de desastre, sem nenhuma perda de dados ou tempo de inatividade. Um failover de teste não afeta a replicação em andamento ou o ambiente de produção. Você pode executar um failover de teste em uma VM (máquina virtual) específica ou em um [plano de recuperação](site-recovery-create-recovery-plans.md) que contém várias VMS.


## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste
Este procedimento descreve como executar um failover de teste para um plano de recuperação. Se você quiser executar um failover de teste para uma única VM, siga as etapas descritas [aqui](tutorial-dr-drill-azure.md#run-a-test-failover-for-a-single-vm)

![Ativação Pós-falha de Teste](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. Em site Recovery no portal do Azure, clique em **planos** > de recuperação*recoveryplan_name* > **failover de teste**.
2. Selecione um **ponto de recuperação** para o qual fazer failover. Pode utilizar uma das opções seguintes:
    - **Mais recente processado**: Essa opção faz failover de todas as VMs no plano para o último ponto de recuperação processado pelo Site Recovery. Para ver o ponto de recuperação mais recente de uma VM específica, verifique os **pontos de recuperação mais recentes** nas configurações da VM. Esta opção proporciona um RTO (Objetivo de Tempo de Recuperação) baixo, porque não é despendido tempo ao processar os dados não processados.
    - **Consistente com o aplicativo mais recente**: Essa opção faz failover de todas as VMs no plano para o último ponto de recuperação consistente com o aplicativo processado pelo Site Recovery. Para ver o ponto de recuperação mais recente de uma VM específica, verifique os **pontos de recuperação mais recentes** nas configurações da VM.
    - **Mais recente**: Essa opção primeiro processa todos os dados que foram enviados para Site Recovery serviço, para criar um ponto de recuperação para cada VM antes de fazer failover para ela. Essa opção fornece o RPO mais baixo (objetivo de ponto de recuperação), pois a VM criada após o failover terá todos os dados replicados para Site Recovery quando o failover foi disparado.
    - **Várias VMs processadas mais recentemente**: Essa opção está disponível para planos de recuperação com uma ou mais VMs com consistência de várias VMs habilitada. As VMs com a configuração habilitada fazem failover para o ponto de recuperação consistente de várias VMs comum mais recente. Outras VMs fazem failover para o último ponto de recuperação processado.  
    - **Várias VMs mais recentes consistentes com o aplicativo**: Essa opção está disponível para planos de recuperação com uma ou mais VMs com consistência de várias VMs habilitada. As VMs que fazem parte de um grupo de replicação fazem failover para o ponto de recuperação mais recente consistente com o aplicativo de várias VMs comum. Outras VMs fazem failover para o ponto de recuperação consistente com o aplicativo mais recente.
    - **Personalizado**: Use esta opção para fazer failover de uma VM específica para um ponto de recuperação específico.
3. Selecione uma rede virtual do Azure na qual as VMs de teste serão criadas.

    - Site Recovery tenta criar VMs de teste em uma sub-rede com o mesmo nome e o mesmo endereço IP que o fornecido nas configurações de **computação e rede** da VM.
    - Se uma sub-rede com o mesmo nome não estiver disponível na rede virtual do Azure usada para failover de teste, a VM de teste será criada na primeira sub-rede em ordem alfabética.
    - Se o mesmo endereço IP não estiver disponível na sub-rede, a VM receberá outro endereço IP disponível na sub-rede. [Saiba mais](#create-a-network-for-test-failover).
4. Se você estiver fazendo failover no Azure e a criptografia de dados estiver habilitada, em **chave de criptografia**, selecione o certificado que foi emitido quando você habilitou a criptografia durante a instalação do provedor. Você poderá ignorar essa etapa se a criptografia não estiver habilitada.
5. Acompanhe o progresso do failover na guia **trabalhos** . Você deve ser capaz de ver o computador de réplica de teste no portal do Azure.
6. Para iniciar uma conexão RDP com a VM do Azure, você precisa [Adicionar um endereço IP público](https://aka.ms/addpublicip) na interface de rede da VM com failover.
7. Quando tudo estiver funcionando conforme o esperado, clique em **limpar failover de teste**. Isso exclui as VMs que foram criadas durante o failover de teste.
8. Em **Notas**, registe e guarde todas as observações associadas à ativação pós-falha de teste.


![Ativação Pós-falha de Teste](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

Quando um failover de teste é disparado, ocorre o seguinte:

1. **Pré-requisitos**: Uma verificação de pré-requisitos é executada para garantir que todas as condições necessárias para o failover sejam atendidas.
2. **Failover**: O failover processa e prepara os dados para que uma VM do Azure possa ser criada a partir dele.
3. **Mais recente**: Se você tiver escolhido o ponto de recuperação mais recente, um ponto de recuperação será criado a partir dos dados que foram enviados para o serviço.
4. **Início**: Esta etapa cria uma máquina virtual do Azure usando os dados processados na etapa anterior.

### <a name="failover-timing"></a>Tempo de failover

Nos cenários a seguir, o failover requer uma etapa intermediária extra que geralmente leva cerca de 8 a 10 minutos para ser concluído:

* VMs VMware executando uma versão do serviço de mobilidade com mais de 9,8
* Servidores físicos
* VMs VMware Linux
* VM Hyper-V protegida como servidores físicos
* VM do VMware em que os drivers a seguir não são drivers de inicialização:
    * storvsc
    * VMBus
    * storflt
    * intelide
    * atapi
* VM do VMware que não tem o DHCP habilitado, independentemente de estar usando endereços IP estáticos ou DHCP.

Em todos os outros casos, nenhuma etapa intermediária não é necessária e o failover leva significativamente menos tempo.


## <a name="create-a-network-for-test-failover"></a>Criar uma rede para ativação pós-falha de teste

Recomendamos que, para a ativação pós-falha de teste, escolha uma rede isolada da rede do site de recuperação de produção específica nas definições **Computação e Rede** para cada VM. Por predefinição, quando cria uma rede virtual do Azure, esta fica isolada das outras redes. A rede de teste deve imitar a sua rede de produção:

- A rede de teste deve ter o mesmo número de sub-redes que a sua rede de produção. As sub-redes devem ter os mesmos nomes.
- A rede de teste deve utilizar o mesmo intervalo de endereços IP.
- Atualize o DNS da rede de teste com o endereço IP especificado para a VM de DNS nas definições **Computação e Rede**. Leia as [considerações sobre a ativação pós-falha do Active Directory](site-recovery-active-directory.md#test-failover-considerations) para obter mais detalhes.


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>Failover de teste para uma rede de produção no local de recuperação

Embora seja recomendável usar uma rede de teste separada da sua rede de produção, se você quiser testar uma análise de recuperação de desastre em sua rede de produção, observe o seguinte:

- Certifique-se de que a VM primária seja desligada quando você executar o failover de teste. Caso contrário, haverá duas VMs com a mesma identidade, em execução na mesma rede ao mesmo tempo. Isso pode levar a consequências inesperadas.
- As alterações nas VMs criadas para o failover de teste são perdidas quando você limpa o failover. Essas alterações não são replicadas de volta para a VM primária.
- O teste em seu ambiente de produção leva a um tempo de inatividade do seu aplicativo de produção. Os usuários não devem usar aplicativos em execução em VMs quando o failover de teste estiver em andamento.  



## <a name="prepare-active-directory-and-dns"></a>Preparar Active Directory e DNS

Para executar um failover de teste para testes de aplicativos, você precisa de uma cópia do ambiente de Active Directory de produção em seu ambiente de teste. Leia [considerações de failover de teste para Active Directory](site-recovery-active-directory.md#test-failover-considerations) para saber mais.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar a ligação para VMs do Azure após a ativação pós-falha

Se você quiser se conectar a VMs do Azure usando RDP/SSH após o failover, siga os requisitos resumidos na tabela.

**Ativação pós-falha** | **Location** | **Ações**
--- | --- | ---
**VM do Azure executando o Windows** | Computador local antes do failover | Para acessar a VM do Azure pela Internet, habilite o RDP e verifique se as regras de TCP e UDP foram adicionadas para o **público**e se o RDP é permitido para todos os perfis em**aplicativos permitidos**pelo **Firewall** > do Windows.<br/><br/> Para acessar a VM do Azure em uma conexão site a site, habilite o RDP no computador e verifique se o RDP é permitido no **Firewall** -> do Windows**aplicativos e recursos permitidos**para redes **privadas** e de domínio.<br/><br/>  Verifique se a política de SAN do sistema operacional está definida como **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135).<br/><br/> Certifique-se de que não haja nenhuma atualização do Windows pendente na VM quando você disparar um failover. O Windows Update pode ser iniciado durante o failover e você não poderá fazer logon na VM até que a atualização seja concluída.
**VM do Azure executando o Windows** | VM do Azure após o failover |  [Adicione um endereço IP público](https://aka.ms/addpublicip) para a VM.<br/><br/> As regras do grupo de segurança de rede na VM com failover (e a sub-rede do Azure à qual ela está conectada) precisam permitir conexões de entrada para a porta RDP.<br/><br/> Verifique o **diagnóstico de inicialização** para verificar uma captura de tela da VM.<br/><br/> Se você não conseguir se conectar, verifique se a VM está em execução e examine essas [dicas de solução de problemas](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**VM do Azure executando Linux** | Computador local antes do failover | Verifique se o serviço de Secure Shell na VM está definido para iniciar automaticamente na inicialização do sistema.<br/><br/> Verifique se as regras de firewall permitem uma ligação SSH ao mesmo.
**VM do Azure executando Linux** | VM do Azure após o failover | As regras do grupo de segurança de rede na VM com failover (e a sub-rede do Azure à qual ela está conectada) precisam permitir conexões de entrada para a porta SSH.<br/><br/> [Adicione um endereço IP público](https://aka.ms/addpublicip) para a VM.<br/><br/> Verifique o **diagnóstico de inicialização** para obter uma captura de tela da VM.<br/><br/>

Siga os passos descritos [aqui](site-recovery-failover-to-azure-troubleshoot.md) para resolver quaisquer problemas de conectividade após a ativação pós-falha.

## <a name="next-steps"></a>Passos seguintes
Depois de concluir uma análise de recuperação de desastres, saiba mais sobre outros tipos de [failover](site-recovery-failover.md).
