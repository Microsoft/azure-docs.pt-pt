---
title: HANA failover procedure to a disaster site for SAP HANA on Azure (Grandes Instâncias) [ Microsoft Docs
description: Como realizar falhas num local de recuperação de desastres para o SAP HANA em Azure (Grandes Instâncias)
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
ms.openlocfilehash: 3f3b37a6336c578ed25d8ab9553bc1ea9c79872f
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83117212"
---
# <a name="disaster-recovery-failover-procedure"></a>Procedimento de ativação pós-falha de recuperação após desastre


>[!IMPORTANT]
>Este artigo não substitui a documentação da administração SAP HANA ou as Notas SAP. Esperamos que tenha uma compreensão sólida e experiência na administração e operações da SAP HANA, especialmente para backup, restauro, alta disponibilidade e recuperação de desastres (DR). Neste artigo, são mostradas imagens do Estúdio SAP HANA. Conteúdo, estrutura e natureza dos ecrãs das ferramentas de administração SAP e das próprias ferramentas podem mudar a partir da versão SAP HANA para lançar.

Há dois casos a considerar quando falha num site de DR:

- Precisa da base de dados SAP HANA para voltar ao estado mais recente dos dados. Neste caso, existe um script de self-service com o qual pode executar a falha sem a necessidade de contactar a Microsoft. Para o failback, tens de trabalhar com a Microsoft.
- Você quer restaurar para um instantâneo de armazenamento que não é o mais recente instantâneo replicado. Neste caso, tens de trabalhar com a Microsoft. 

>[!NOTE]
>Os seguintes passos devem ser feitos na unidade HANA Large Instance, que representa a unidade DR. 
 
Para restaurar as mais recentes imagens de armazenamento replicadas, siga os passos em "Execute full DR failover - azure_hana_dr_failover" nas [ferramentas instantâneas da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf). 

Se quiser ter várias instâncias SAP HANA falhadas, corra o comando azure_hana_dr_failover várias vezes. Quando solicitado, insira o SID SAP HANA que pretende falhar e restaurar. 


Pode testar a falha do DR também sem afetar a relação real de replicação. Para efetuar uma falha no teste, siga os passos em "Execute um teste DR failover - azure_hana_test_dr_failover" nas [ferramentas instantâneas da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf). 

>[!IMPORTANT]
>*Não* ecorra quaisquer transações de produção na instância que criou no site dr através do processo de **teste de uma falha**. O comando azure_hana_test_dr_failover cria um conjunto de volumes que não têm relação com o local primário. Como resultado, a sincronização de volta ao local primário *não* é possível. 

Se quiser ter várias instâncias SAP HANA para testar, faça o guião várias vezes. Quando solicitado, insira o SID SAP HANA da instância que pretende testar para a falha. 

>[!NOTE]
>Se precisar de falhar no site da DR para resgatar alguns dados que foram apagados há horas e precisar que os volumes de DR sejam definidos para uma imagem mais precoce, este procedimento aplica-se. 

1. Desligue a instância de não produção da HANA na unidade de recuperação de desastres da HANA Large Instances que está a executar. Uma instância de produção HANA dormente é pré-instalada.
1. Certifique-se de que não estão a decorrer processos SAP HANA. Utilize o seguinte comando para esta verificação:

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      A saída deve mostrar-lhe o processo **hdbdaemon** em estado de paragem e nenhum outro processo HANA em estado de corrida ou iniciado.
1. Determine qual o nome instantâneo ou id de backup SAP HANA que pretende restaurar o local de recuperação de desastres. Em casos reais de recuperação de desastres, este instantâneo é geralmente o último instantâneo. Se precisar de recuperar os dados perdidos, escolha uma foto mais cedo.
1. Contacte o Suporte Azure através de um pedido de apoio de alta prioridade. Peça a restauração desse instantâneo com o nome e a data do instantâneo ou o ID de reserva HANA no site dr. A predefinição é que o lado das operações restaura apenas o volume /hana/dados. Se também quiser ter os volumes de backups /hana/logbackups, tem de o indicar especificamente. *Não restaure o volume /hana/partilhado.* Em vez disso, escolha ficheiros específicos como global.ini do diretório **.snapshot** e seus subdiretórios depois de remontar o volume /hana/partilhado para PRD. 

   Do lado das operações, ocorrem os seguintes passos:

   a. A replicação de instantâneos do volume de produção para os volumes de recuperação de desastres é interrompida. Esta perturbação pode já ter acontecido se uma paragem no local de produção for a razão pela qual precisa realizar o procedimento de recuperação de desastres.
   
   b. O nome instantâneo de armazenamento ou instantâneo com o ID de reserva que escolheu é restaurado nos volumes de recuperação de desastres.
   
   c. Após o restauro, os volumes de recuperação de desastres estão disponíveis para serem montados nas unidades hana large instance na região de recuperação de desastres.
      
1. Monte os volumes de recuperação de desastres para a unidade HANA Large Instance no local de recuperação de desastres. 
1. Inicie a ocorrência de produção sap HANA dormente.
1. Se optou por copiar registos de cópia de registos de transação para reduzir o tempo de RPO, misture as cópias de segurança do registo de transações no novo diretório DR/hana/logbackups. Não sobrepense os reforços existentes. Copie cópias de cópias que não foram replicadas com a mais recente replicação de um instantâneo de armazenamento.
1. Também pode restaurar ficheiros individuais das imagens que não foram replicadas para o volume /hana/compartilhado/PRD na região DR Azure.

Os seguintes passos mostram como recuperar a instância de produção do SAP HANA com base no instantâneo de armazenamento restaurado e nas cópias de segurança do registo de transações disponíveis.

1. Altere a localização de reserva para **/hana/logbackups** utilizando o Estúdio SAP HANA.

   ![Alterar a localização de backup para recuperação de DR](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA digitaliza através dos locais de ficheiros de reserva e sugere a mais recente cópia de segurança de registo de transações para restaurar. A varredura pode demorar alguns minutos até aparecer um ecrã como o seguinte:

   ![Lista de backups de registo de transações para recuperação de DR](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Ajuste algumas das definições predefinidas:

      - Utilização clara **Cópias de Segurança Delta**.
      - **Selecione Inicialize a Área de Registo**.

   ![Definir a área de registo inicializar](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Selecione **Concluir**.

   ![Termine o restauro dr](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Uma janela de progresso, como a mostrada aqui, deve aparecer. Tenha em mente que o exemplo é de uma recuperação de desastres restaurar uma configuração sap HANA de três nodos.

![Restaurar o progresso](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Se o restauro parar de responder no ecrã **'Acabamento'** e não mostrar o ecrã de progresso, confirme que todas as instâncias sap HANA nos nós dos trabalhadores estão em execução. Se necessário, inicie manualmente as instâncias SAP HANA.


## <a name="failback-from-a-dr-to-a-production-site"></a>Failback de um DR para um local de produção
Pode falhar de um DR para um local de produção. Vejamos um cenário em que a falha no local de recuperação de desastres foi causada por problemas na produção da região de Azure, e não pela sua necessidade de recuperar dados perdidos. 

Tens estado a gerir a tua carga de trabalho de produção da SAP há algum tempo no local de recuperação de desastres. À medida que os problemas no local de produção são resolvidos, pretende falhar no seu local de produção. Como não se pode perder dados, o passo de volta ao local de produção envolve várias etapas e estreita cooperação com a equipa de operações da SAP HANA na equipa de operações da Azure. Cabe-lhe a si desencadear a equipa de operações para começar a sincronizar de volta ao local de produção depois de os problemas serem resolvidos.

Siga estes passos.

1. A equipa de operações da SAP HANA sobre o Azure recebe o gatilho para sincronizar os volumes de armazenamento de produção dos volumes de armazenamento de recuperação de desastres, que representam agora o estado de produção. Neste estado, a unidade HANA Large Instance no local de produção é encerrada.
1. A equipa de operações da SAP HANA no Azure monitoriza a replicação e certifica-se de que é apanhada antes de o informarem.
1. Encerra as aplicações que usam a produção HANA Instance no local de recuperação de desastres. Em seguida, efetue uma cópia de segurança de registo de transações HANA. Em seguida, para si a instância HANA que está a funcionar nas unidades de grande instância da HANA no local de recuperação de desastres.
1. Após a ocorrência de HANA que está a funcionar na unidade HANA Large Instance no local de recuperação de desastres, a equipa de operações sincroniza manualmente os volumes do disco novamente.
1. A equipa de operações da SAP HANA sobre o Azure volta a iniciar a unidade HANA Large Instance no local de produção. Eles entregam-te. Certifique-se de que o caso SAP HANA está em estado de encerramento no momento de arranque da unidade HANA Large Instance.
1. Executa a mesma base de dados restaurar os passos que fez quando falhou anteriormente no local de recuperação de desastres.

## <a name="monitor-disaster-recovery-replication"></a>Monitorizar a replicação da recuperação de desastres

Para monitorizar o estado do progresso da sua replicação de armazenamento, execute o script `azure_hana_replication_status` . Este comando deve ser executado a partir de uma unidade que funciona no local de recuperação de desastres para funcionar como esperado. O comando funciona independentemente de a replicação estar ativa. O comando pode ser executado para cada unidade hana grande instância do seu inquilino no local de recuperação de desastres. Não pode ser usado para obter detalhes sobre o volume de botas. 

Para obter mais informações sobre o comando e a sua saída, consulte "Obter o estado de replicação de DR - azure_hana_replication_status" nas [ferramentas instantâneas da Microsoft para SAP HANA no Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf).


## <a name="next-steps"></a>Passos seguintes
- Consulte [monitor e resolução de problemas do lado HANA](hana-monitor-troubleshoot.md).
