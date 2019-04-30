---
title: Procedimento de ativação pós-falha do HANA para o site de desastres para SAP HANA no Azure (instâncias grandes) | Documentos da Microsoft
description: Como realizar a ativação pós-falha para o site de recuperação após desastre para o SAP HANA no Azure (instâncias grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 76d8bb816bdf229d13a49fa61337899a8bf29ecd
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098291"
---
# <a name="disaster-recovery-failover-procedure"></a>Procedimento de ativação pós-falha de recuperação após desastre


>[!IMPORTANT]
>Esta documentação não é nenhuma substituição da documentação de administração do SAP HANA ou SAP Notes. Espera-se que o leitor tenha uma compreensão sólida do e experiência em administração de SAP HANA e operações, especialmente com os tópicos de cópia de segurança, restauro, elevada disponibilidade e recuperação após desastre. Nesta documentação, capturas de ecrã do SAP HANA Studio são apresentadas. Conteúdo, estrutura e a natureza das telas de ferramentas de administração do SAP e das ferramentas propriamente ditas podem ser alteradas a partir do SAP HANA versão para versão.

Existem dois casos a serem consideradas ao fazer failover para o site de DR:

- Terá da base de dados do SAP HANA voltar para o estado mais recente dos dados. Neste caso, é um script de self-service com a qual pode realizar a ativação pós-falha sem a necessidade de contactar a Microsoft. No entanto, para a reativação pós-falha, terá de trabalhar com a Microsoft.
- Que pretende restaurar para um instantâneo de armazenamento que não seja o instantâneo mais recente replicado. Neste caso, terá de trabalhar com a Microsoft. 

>[!NOTE]
>As etapas a seguir precisam ser executadas na unidade instância grande do HANA, que representa a unidade de DR. 
 
Para restaurar para os instantâneos de armazenamento mais recente replicado, execute os passos conforme indicado na secção **'Executar total DR ativação pós-falha - azure_hana_dr_failover'** do documento [Microsoft instantâneo ferramentas para o SAP HANA no Azure ](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 

Se pretender ter várias instâncias do SAP HANA a ativação pós-falha, terá de executar o comando azure_hana_dr_failover várias vezes. Quando solicitado, introduza o SID do HANA SAP que pretende efetuar a ativação pós-falha e a restaurar. 


Também pode testar a ativação pós-falha de DR sem afetar a relação de replicação real. Para efetuar uma ativação pós-falha de teste, siga os passos em **'Executar um teste de ativação pós-falha de DR - azure_hana_test_dr_failover'** do documento [Microsoft instantâneo ferramentas para o SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 

>[!IMPORTANT]
>Fazer *não* executar quaisquer transações de produção na instância que criou no site através do processo de DR **uma ativação pós-falha de teste**. Azure_hana_test_dr_failover esse comando cria um conjunto de volumes que não têm qualquer relação para o site primário. Como resultado, é a sincronização de volta para o site primário *não* possíveis. 

Se pretender ter várias instâncias do SAP HANA para testar, terá de executar o script várias vezes. Quando solicitado, escreva SID SAP HANA de instância que pretende testar ativação pós-falha. 

>[!NOTE]
>Se precisar de fazer a ativação pós-falha para o site de DR para resgatar a alguns dados que foi eliminados há horas e tem dos volumes de DR para ser definido como um instantâneo anterior, este procedimento aplica-se. 

1. Encerre a instância de não produção do HANA na unidade de recuperação após desastre de instâncias grandes do HANA que estiver a executar. Isto acontece porque existe uma instância de produção de HANA Inativas pré-instalado.
1. Certifique-se de que não existem processos de SAP HANA estão em execução. Utilize o seguinte comando para esta verificação: `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. A saída deve mostrar-lhe a **hdbdaemon** processos no estado parado e outros processos HANA num Estado em execução ou iniciado.
1. Determine para qual nome de instantâneo ou um ID de cópia de segurança do SAP HANA que pretende ter o site de recuperação após desastre restaurado. Em casos de recuperação após desastre real, este instantâneo é normalmente o instantâneo mais recente. Se precisar de recuperar dados perdidos, escolha um instantâneo anterior.
1. Contacte o suporte do Azure através de um pedido de suporte de alta prioridade. Pedir o restauro de nesse instantâneo (com o nome e data do instantâneo) ou o ID de cópia de segurança do HANA no site de DR. A predefinição é que o lado de operações restaura/hana/volume de dados apenas. Se quiser ter os/hana/logbackups volumes também, terá de estado de especificamente. *Não restaure o volume de /hana/shared.* Em vez disso, deve escolher ficheiros específicos, como global.ini fora do **.snapshot** diretório e respetivos subdiretórios depois de voltar a montar o/hana/volume partilhado de PRD=IIS&sbp=&PVER=5.0&ID=500;100&cat. 

   No lado de operações, ocorrem os seguintes passos:

   a. A replicação de instantâneos do volume de produção para os volumes de recuperação após desastre está parada. Este interrupção pode ter acontecido já se uma falha no site de produção é o motivo pelo qual que precisa executar o procedimento de recuperação após desastre.
   
   b. O armazenamento de nome de instantâneo ou instantâneo com o ID de cópia de segurança que escolheu é restaurado em volumes de recuperação após desastre.
   
   c. Após o restauro, os volumes de recuperação após desastre estão disponíveis para ser montado para as unidades de instância grande do HANA na região de recuperação após desastre.
      
1. Monte os volumes de recuperação após desastre para a unidade de instância grande do HANA no site de recuperação após desastre. 
1. Inicie a instância de produção do SAP HANA Inativas.
1. Se optar por copiar registos de cópia de segurança de registo de transações para reduzir o tempo RPO, terá de unir essas cópias de segurança do registo de transação para o diretório de logbackups montado recentemente DR/hana /. Não substitua as cópias de segurança existentes. Copie as cópias de segurança mais recente não tem sido replicadas com a replicação mais recentes de um instantâneo de armazenamento.
1. Também pode restaurar ficheiros únicos fora os instantâneos que tem sido replicados para o volume de /hana/shared/PRD na região do Azure de DR.

A seguinte seqüência de etapas envolve a recuperar a instância de produção do SAP HANA com base no instantâneo de armazenamento restaurada e os backups de log de transação que estão disponíveis:

1. Altere a localização de cópia de segurança para **/hana/logbackups** com o SAP HANA Studio.
   ![Alterar a localização de cópia de segurança para a recuperação de DR](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. Examina as localizações de ficheiros de cópia de segurança e sugere o backup de log de transação mais recente para restaurar para o SAP HANA. A análise pode demorar alguns minutos até que uma tela, como aparece o seguinte: ![Lista de backups de log de transação para a recuperação de DR](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Ajuste algumas das configurações padrão:

      - Limpar **utilizar cópias de segurança de Delta**.
      - Selecione **inicializar a área de Log**.

   ![Definir a área de log de inicialização](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Selecione **Concluir**.

   ![Concluir o restauro de DR](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Uma janela de progresso, como mostrado aqui, deve aparecer. Tenha em atenção que o exemplo é de um restauro de recuperação após desastre de uma configuração de SAP HANA de escalamento horizontal de três nós.

![Progresso de restauro](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Se o restauro parece deixe de responder com o **concluir** ecrã e não mostrar a tela de progresso, confirme que todas as instâncias do SAP HANA em nós de trabalho estão em execução. Se necessário, inicie manualmente o SAP HANA nas instâncias.


## <a name="failback-from-a-dr-to-a-production-site"></a>Reativação pós-falha a partir de um DR para um site de produção
Pode a reativação pós-falha a partir de um DR para um site de produção. Vamos examinar um cenário em que a ativação pós-falha para o site de recuperação após desastre foi causada por problemas na região do Azure de produção e não pela sua necessidade de recuperar dados perdidos. Tenham sido executados sua carga de trabalho de produção do SAP durante algum tempo no site de recuperação após desastre. Como são resolvidos os problemas do site de produção, pretende reativação pós-falha para o seu site de produção. Porque não pode perder os dados, o passo de volta para o site de produção envolve vários passos e cooperação íntima com o SAP HANA na equipe de operações do Azure. Cabe-lhe a si para acionar a equipe de operações para iniciar a sincronização para o site de produção após os problemas são resolvidos.

Esta é a seqüência de etapas a serem executadas:

1. O SAP HANA na equipe de operações do Azure obtém o acionador para sincronizar os volumes de armazenamento de produção dos volumes de armazenamento de recuperação após desastre, que agora representam o estado de produção. Neste estado, a unidade de instância grande do HANA no site de produção é encerrada.
1. O SAP HANA na equipe de operações do Azure monitoriza a replicação e certifica-se de que ela é atualizada com antes informando-o.
1. Encerrar as aplicações que utilizam a instância HANA de produção no local de recuperação de desastres. É, em seguida, executar uma cópia de segurança do registo de transações do HANA. Em seguida, parar a instância HANA em execução nas unidades de instância grande do HANA no site de recuperação após desastre.
1. Depois da instância HANA em execução na unidade instância grande do HANA no local de recuperação de desastres é encerrada, a equipe de operações manualmente sincroniza os volumes de disco novamente.
1. O SAP HANA na equipe de operações do Azure começa novamente a unidade de instância grande do HANA no site de produção e distribui-lo para. Certifique-se que a instância do SAP HANA está num Estado de encerramento no tempo de inicialização da unidade de instância grande do HANA.
1. Execute os mesmos passos de restauro de base de dados como fez quando anteriormente fazer failover para o site de recuperação após desastre.

## <a name="monitor-disaster-recovery-replication"></a>Monitorizar a replicação de recuperação após desastre

Pode monitorizar o estado de seu progresso de replicação de armazenamento ao executar o script `azure_hana_replication_status`. Este comando deve ser executado a partir de uma unidade em execução na localização de recuperação após desastre a funcionar conforme esperado. O comando funciona independentemente se a replicação está ativa. O comando pode ser executado para cada unidade de instância grande do HANA do seu inquilino na localização de recuperação após desastre. Ele não pode ser usado para obter detalhes sobre o volume de arranque. Para obter detalhes sobre o comando e o respetivo resultado ler **'Obter estado de replicação de DR - azure_hana_replication_status'** do documento [Microsoft instantâneo ferramentas para o SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).


## <a name="next-steps"></a>Passos Seguintes
- Consulte a [monitorização e resolução de problemas do lado do HANA](hana-monitor-troubleshoot.md).
