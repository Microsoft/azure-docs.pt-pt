---
title: Hana failover procedure to a disaster site for SAP HANA on Azure (Large Instances) Microsoft Docs
description: Como executar failover para um local de recuperação de desastres para SAP HANA em Azure (Grandes Instâncias)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6454903a7c37da30e317e29c126109b39b14efbc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83660574"
---
# <a name="disaster-recovery-failover-procedure"></a>Procedimento de ativação pós-falha de recuperação após desastre


>[!IMPORTANT]
>Este artigo não substitui a documentação da administração SAP HANA ou as Notas SAP. Esperamos que tenha uma compreensão sólida e experiência na administração e operações sap HANA, especialmente para apoio, restauro, alta disponibilidade e recuperação de desastres (DR). Neste artigo, são mostradas imagens do ESTÚDIO SAP HANA. O conteúdo, a estrutura e a natureza dos ecrãs das ferramentas de administração SAP e das próprias ferramentas podem mudar de lançamento SAP HANA para lançamento.

Há dois casos a considerar quando falha num site dr:

- Precisa da base de dados SAP HANA para voltar ao estado mais recente dos dados. Neste caso, existe um script de self-service com o qual pode executar o failover sem a necessidade de contactar a Microsoft. Para falhar, tens de trabalhar com a Microsoft.
- Você quer restaurar para um instantâneo de armazenamento que não é o último instantâneo replicado. Neste caso, tens de trabalhar com a Microsoft. 

>[!NOTE]
>Devem ser dados os seguintes passos na unidade HANA Large Instance, que representa a unidade DR. 
 
Para restaurar as mais recentes imagens de armazenamento replicadas, siga os passos em "Perform full DR failover - azure_hana_dr_failover" em [ferramentas instantâneas da Microsoft para SAP HANA em Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf). 

Se pretender que várias instâncias SAP HANA tenham falhado, executar o comando azure_hana_dr_failover várias vezes. Quando solicitado, insira o SAP HANA SID que pretende falhar e restaurar. 


Pode testar o failover dr também sem afetar a relação real de replicação. Para realizar um teste de failover, siga os passos em "Executar um teste DR failover - azure_hana_test_dr_failover" em [ferramentas instantâneas da Microsoft para SAP HANA em Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf). 

>[!IMPORTANT]
>*Não efete* quaisquer transações de produção sobre o caso que criou no site DR através do processo de teste de **uma falha.** O comando azure_hana_test_dr_failover cria um conjunto de volumes que não têm qualquer relação com o local primário. Como resultado, a sincronização de volta ao local primário *não* é possível. 

Se pretender ter várias instâncias SAP HANA para testar, execute o script várias vezes. Quando solicitado, insira o SAP HANA SID da instância que pretende testar para o failover. 

>[!NOTE]
>Se precisar de ir ao site dr para resgatar alguns dados que foram apagados há horas e precisar que os volumes de DR sejam definidos para uma imagem anterior, este procedimento aplica-se. 

1. Desligue o caso de não-produção da HANA na unidade de recuperação de desastres da HANA Large Instances que estás a executar. Uma instância de produção hana dormente é pré-instalada.
1. Certifique-se de que não estão a decorrer processos SAP HANA. Utilize o seguinte comando para esta verificação:

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      A saída deve mostrar-lhe o processo **hdbdaemon** em um estado parado e nenhum outro processo HANA em um estado de execução ou iniciado.
1. Determine qual o nome instantâneo ou o ID de backup SAP HANA que deseja que o local de recuperação de desastres seja restaurado. Em casos reais de recuperação de desastres, este instantâneo é geralmente o último instantâneo. Se precisar de recuperar dados perdidos, escolha uma foto anterior.
1. Contacte o Suporte Azure através de um pedido de apoio de alta prioridade. Peça a restauração do instantâneo com o nome e a data do instantâneo ou o ID de reserva HANA no site DR. O padrão é que o lado das operações restaura apenas o volume /hana/dados. Se também quiser ter os volumes /hana/logbacks, tem de indicar especificamente isso. *Não restaure o volume /hana/partilhado.* Em vez disso, escolha ficheiros específicos como global.ini fora do **diretório .snapshot** e suas subdireções depois de voltar a montar o volume /hana/shared para PRD. 

   Do lado das operações, ocorrem os seguintes passos:

   a. A replicação de instantâneos do volume de produção para os volumes de recuperação de desastres é interrompida. Esta perturbação pode já ter ocorrido se uma paragem no local de produção for a razão pela qual precisa de realizar o procedimento de recuperação de desastres.
   
   b. O nome do instantâneo de armazenamento ou instantâneo com o ID de reserva que escolheu é restaurado nos volumes de recuperação de desastres.
   
   c. Após o restauro, os volumes de recuperação de desastres estão disponíveis para serem montados nas unidades de Grande Instância HANA na região de recuperação de desastres.
      
1. Monte os volumes de recuperação de desastres na unidade HANA Large Instance no local de recuperação de desastres. 
1. Inicie a produção dormente DA SAP HANA.
1. Se optar por copiar registos de cópias de registo de transações para reduzir o tempo de RPO, misture as cópias de segurança do registo de transações no recém-montado diretório DR/hana/logbackups. Não substitua os reforços existentes. Copie cópias de cópias de segurança que não foram replicadas com a mais recente replicação de um instantâneo de armazenamento.
1. Também pode restaurar ficheiros individuais a partir dos instantâneos que não foram replicados para o volume /hana/shared/PRD na região dr Azure.

Os passos seguintes mostram como recuperar a instância de produção do SAP HANA com base no instantâneo de armazenamento restaurado e nas cópias de segurança do registo de transações disponíveis.

1. Altere a localização de backup para **/hana/logbackups** utilizando o Estúdio SAP HANA.

   ![Alterar a localização de backup para a recuperação de DR](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. O SAP HANA verifica as localizações do ficheiro de reserva e sugere a cópia de segurança mais recente do registo de transações para restaurar. A digitalização pode demorar alguns minutos até aparecer um ecrã como o seguinte:

   ![Lista de backups de registos de transações para recuperação de DR](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Ajuste algumas das definições predefinições:

      - **Cópias de segurança delta de utilização clara**.
      - **Selecione Inicialize a área de registo**.

   ![Definir a área de registo inicializ](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Selecione **Concluir**.

   ![Termine o restauro dr](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Uma janela de progresso, como a mostrada aqui, deve aparecer. Tenha em mente que o exemplo é de uma recuperação de desastres restaurar uma configuração SAP HANA de três nós.

![Restaurar o progresso](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Se a restauração deixar de responder no ecrã **Finish** e não mostrar o ecrã de progresso, confirme que todas as instâncias SAP HANA nos nós dos trabalhadores estão em funcionamento. Se necessário, inicie manualmente as instâncias SAP HANA.


## <a name="failback-from-a-dr-to-a-production-site"></a>Recuo de um DR para um local de produção
Pode falhar de um DR para um local de produção. Vejamos um cenário em que a falha no local de recuperação de desastres foi causada por problemas na região de produção Azure, e não pela necessidade de recuperar dados perdidos. 

Tens estado a executar a tua carga de trabalho de produção da SAP há algum tempo no local de recuperação de desastres. À medida que os problemas no local de produção estão resolvidos, pretende-se que volte ao seu local de produção. Como não se pode perder dados, o passo de volta ao local de produção envolve vários passos e estreita cooperação com a equipa de operações da SAP HANA. Cabe-lhe a si desencadear a equipa de operações para começar a sincronizar o local de produção depois de os problemas estarem resolvidos.

Siga estes passos:

1. A equipa de operações sap hana da Azure obtém o gatilho para sincronizar os volumes de armazenamento de produção dos volumes de armazenamento de recuperação de desastres, que representam agora o estado de produção. Neste estado, a unidade HANA Large Instance no local de produção é encerrada.
1. A equipa de operações da SAP HANA na Azure monitoriza a replicação e certifica-se de que é apanhada antes de o informarem.
1. Encerraste as aplicações que usam a produção HANA Instance no local de recuperação de desastres. Em seguida, efetue uma cópia de segurança do registo de transações HANA. Em seguida, você para o caso HANA que está a decorrer nas unidades de HANA Large Instance no local de recuperação de desastres.
1. Após o caso HANA que está a decorrer na unidade HANA Large Instance no local de recuperação de desastres ser desligado, a equipa de operações sincroniza manualmente os volumes do disco novamente.
1. A equipa de operações SAP HANA on Azure volta a iniciar a unidade HANA Large Instance no local de produção. Eles entregam-te. Certifique-se de que a instância SAP HANA está em estado de paragem na hora de arranque da unidade HANA Large Instance.
1. Executa a mesma base de dados que restaurou os passos que fez quando falhou anteriormente no local de recuperação de desastres.

## <a name="monitor-disaster-recovery-replication"></a>Monitorizar a replicação da recuperação de desastres

Para monitorizar o estado do progresso da sua replicação de armazenamento, execute o script `azure_hana_replication_status` . Este comando deve ser executado a partir de uma unidade que funciona no local de recuperação de desastres para funcionar como esperado. O comando funciona independentemente de a replicação estar ativa. O comando pode ser executado para cada unidade de HANA Large Instance do seu inquilino no local de recuperação de desastres. Não pode ser usado para obter detalhes sobre o volume de arranque. 

Para obter mais informações sobre o comando e a sua saída, consulte "Get DR replication status - azure_hana_replication_status" em [ferramentas instantâneas da Microsoft para SAP HANA em Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf).


## <a name="next-steps"></a>Passos seguintes
- Consulte [monitor e resolução de problemas do lado HANA](hana-monitor-troubleshoot.md).
