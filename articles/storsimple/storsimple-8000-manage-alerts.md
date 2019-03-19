---
title: Ver e gerir alertas para o dispositivo da série StorSimple 8000 | Documentos da Microsoft
description: Descreve as condições de alerta do StorSimple e gravidade, como configurar notificações de alerta e como utilizar o serviço StorSimple Device Manager para gerir alertas.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: 4128ad53f30e74fb72f6256b78587cc222f4c282
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57838719"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-storsimple-alerts"></a>Utilizar o serviço StorSimple Device Manager para ver e gerir alertas do StorSimple

## <a name="overview"></a>Descrição geral

O **alertas** painel no serviço StorSimple Device Manager fornece uma forma para que Revise e limpar alertas relacionados com o dispositivo do StorSimple de forma em tempo real. A partir deste painel, pode monitorizar centralmente os problemas de estado de funcionamento dos seus dispositivos do StorSimple e a solução geral do Microsoft Azure StorSimple.

Este tutorial descreve as condições de alerta comuns, níveis de gravidade do alerta e como configurar notificações de alerta. Além disso, ele inclui tabelas de referência rápida de alerta, permitindo-lhe rapidamente localize um alerta específico e responder de forma apropriada.

![Página de alertas](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="common-alert-conditions"></a>Condições de alerta comuns

O dispositivo StorSimple gera alertas em resposta a uma variedade de condições. Seguem-se os tipos mais comuns das condições de alerta:

* **Problemas de hardware** – estes alertas falar sobre o estado de funcionamento do seu hardware. Eles permitem-lhe saber se são necessárias atualizações de firmware, se uma interface de rede tem problemas, ou se existe um problema com uma das suas unidades de dados.
* **Problemas de conectividade** – estes alertas ocorrem quando há dificuldade na transferência de dados. Podem ocorrer problemas de comunicação durante a transferência de dados para e da conta de armazenamento do Azure ou devido à falta de conectividade entre os dispositivos e o serviço StorSimple Device Manager. Problemas de comunicação são alguns dos mais difíceis de corrigir porque existem tantos pontos de falha. Deve sempre primeiro certifique-se de que o acesso à Internet e de conectividade de rede estão disponíveis antes de continuar para a resolução de problemas mais avançados. Para obter ajuda na resolução de problemas, aceda a [resolver problemas com o cmdlet Test-Connection](storsimple-8000-troubleshoot-deployment.md).
* **Problemas de desempenho** – estes alertas são causados quando de seu sistema não estiver a executar de forma otimizada, como quando ele é sob uma carga pesada.

Além disso, poderá ver alertas relacionados com segurança, atualizações ou falhas de tarefas.

## <a name="alert-severity-levels"></a>Níveis de gravidade do alerta

Os alertas têm níveis de gravidade de diferente, consoante o impacto que terá a situação de alerta e a necessidade de uma resposta ao alerta. Os níveis de gravidade são:

* **Crítico** – este alerta ocorre em resposta a uma condição que está a afetar o desempenho com êxito de seu sistema. Para garantir que o serviço não é interrompido o StorSimple, é necessária ação.
* **Aviso** – esta condição pode se tornar crítica, se não resolver. Deve investigar a situação e tomar nenhuma ação necessária para limpar o problema.
* **Informações** – este alerta contém informações que podem ser útil para controlar e gerir o sistema.

## <a name="configure-alert-settings"></a>Configurar definições de alerta

Pode escolher se pretende ser notificado por e-mail das condições de alerta para cada um dos seus dispositivos do StorSimple. Além disso, pode identificar outros destinatários de notificação de alerta, introduzindo os respetivos endereços de e-mail no **outros destinatários de e-mail** caixa, separada por ponto e vírgula.

> [!NOTE]
> Pode introduzir um máximo de 20 endereços de e-mail por dispositivo.

Depois de ativar notificações por e-mail para um dispositivo, os membros da lista de notificação irão receber uma mensagem de e-mail sempre que ocorre de um alerta crítico. As mensagens serão enviadas *storsimple-alertas-noreply\@mail.windowsazure.com* e descreve a condição do alerta. Os destinatários podem clicam **Unsubscribe** para remover da lista de notificação de e-mail.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Para ativar o e-mail de notificação de alertas para um dispositivo
1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Na lista de dispositivos, selecione e clique no dispositivo que pretende configurar.
2. Aceda a **configurações** > **geral** para o dispositivo.

   ![Painel de alertas](./media/storsimple-8000-manage-alerts/configure-alerts-email2.png)
   
2. Na **definições gerais** painel, aceda à **definições de alerta** e defina o seguinte:
   
   1. Na **Enviar notificação por e-mail** campo, selecione **Sim**.
   2. Na **E-mail aos administradores de serviço** campo, selecione **Sim** para que o administrador de serviço e todos os colegas administradores recebam notificações de alerta.
   3. Na **outros destinatários de e-mail** , insira os endereços de e-mail de todos os outros destinatários que devem receber notificações de alerta. Introduza os nomes no formato *alguém\@somewhere.com*. Utilize ponto e vírgula para separar os endereços de e-mail. Pode configurar um máximo de 20 endereços de e-mail por dispositivo. 
      
3. Para enviar uma notificação de e-mail de teste, clique em **enviar e-mail de teste**. O serviço StorSimple Device Manager irá apresentar mensagens de estado, como ele encaminha a notificação de teste.

    ![Definições de alerta](./media/storsimple-8000-manage-alerts/configure-alerts-email3.png)

4. Verá uma notificação quando é enviado o e-mail de teste. 
   
    ![Alertas de e-mail de notificação enviado de teste](./media/storsimple-8000-manage-alerts/configure-alerts-email4.png)
   
   > [!NOTE]
   > Se não é possível enviar a mensagem de notificação de teste, o serviço StorSimple Device Manager exibirá uma mensagem de erro apropriada. Aguarde alguns minutos e volte a tentar enviar a mensagem de notificação de teste novamente. 

5. Depois de concluir a configuração, clique em **guardar**. Quando lhe for pedida a confirmação, clique em **Sim**.

     ![Alertas de e-mail de notificação enviado de teste](./media/storsimple-8000-manage-alerts/configure-alerts-email5.png)

## <a name="view-and-track-alerts"></a>Ver e acompanhar os alertas

Painel de resumo do serviço StorSimple Device Manager fornece uma rápida olhada no número de alertas nos seus dispositivos, organizados por nível de gravidade.

![Dashboard de alertas](./media/storsimple-8000-manage-alerts/device-summary4.png)

Clicar o nível de gravidade abre o **alertas** painel. Os resultados incluem apenas os alertas que correspondam a esse nível de gravidade.

Clicar num alerta na lista fornece detalhes adicionais para o alerta, incluindo a última vez que o alerta foi reportado, o número de ocorrências do alerta no dispositivo e a ação recomendada para resolver o alerta. Se for um alerta de hardware, ele também identificará o componente de hardware.

![Exemplo de alerta de hardware](./media/storsimple-8000-manage-alerts/configure-alerts-email14.png)

Pode copiar os detalhes do alerta para um arquivo de texto, se tiver de enviar as informações ao Support da Microsoft. Depois de seguir a recomendação e resolver a condição do alerta no local, deve limpar o alerta do dispositivo ao selecionar o alerta na **alertas** painel e clicando em **limpar**. Para limpar vários alertas, selecione cada alerta, clique em qualquer coluna, exceto os **alerta** coluna e clique em **limpar** depois de selecionar todos os alertas para ser limpo. Tenha em atenção que alguns alertas automaticamente são eliminadas quando o problema estiver resolvido ou quando o sistema atualiza o alerta com novas informações.

Quando clica em **clara**, terá a oportunidade de fornecer comentários sobre o alerta e os passos que tirou para resolver o problema. Alguns eventos serão limpos pelo sistema se outro evento for acionado com novas informações. Nesse caso, verá a seguinte mensagem.

![Mensagem de alerta Clear](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Alertas de ordenação e revisão

Talvez ache mais eficiente para executar relatórios sobre alertas, para que possa rever e limpar estes grupos. Além disso, o **alertas** painel pode apresentar até 250 alertas. Se tiver excedido o número de alertas, nem todos os alertas serão apresentados na vista predefinida. Pode combinar os campos seguintes para personalizar os alertas são apresentados:

* **Estado** – pode mostrar **Active Directory** ou **compensado** alertas. Ainda estão sendo acionados alertas ativos no seu sistema, enquanto os alertas desmarcadas foram seja limpos manualmente por um administrador ou por meio de programação apagadas porque o sistema atualizado a condição de alerta com novas informações.
* **Gravidade** – pode exibir os alertas de todos os níveis de gravidade (críticos, aviso, informações) ou apenas uma determinada gravidade, como alertas críticos apenas.
* **Origem** – pode apresentar os alertas de todas as origens ou limitar os alertas para os que vêm do serviço ou um ou todos os dispositivos.
* **Intervalo de tempo** – ao especificar o **da** e **para** datas e carimbos de data / hora, pode olhar alertas durante o período de tempo que está interessado.

![Lista de alertas](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="alerts-quick-reference"></a>Referência rápida de alertas

As tabelas seguintes listam alguns dos alertas do Microsoft Azure StorSimple que poderá encontrar, bem como recomendações e informações adicionais que estiverem disponíveis. Alertas de dispositivo do StorSimple enquadram-se uma das seguintes categorias:

* [Alertas de conectividade de cloud](#cloud-connectivity-alerts)
* [Alertas de cluster](#cluster-alerts)
* [Alertas de recuperação após desastre](#disaster-recovery-alerts)
* [Alertas de hardware](#hardware-alerts)
* [Alertas de falha da tarefa](#job-failure-alerts)
* [Alertas de volume afixado localmente](#locally-pinned-volume-alerts)
* [Alertas de sistema de rede](#networking-alerts)
* [Alertas de desempenho](#performance-alerts)
* [Alertas de segurança](#security-alerts)
* [Alertas de pacote de suporte](#support-package-alerts)

### <a name="cloud-connectivity-alerts"></a>Alertas de conectividade de cloud

| Texto de alerta | Evento | Obter mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Conectividade para <*nome da credencial de nuvem*> não é possível estabelecer. |Não é possível ligar à conta de armazenamento. |Parece que poderá haver um problema de conectividade com o seu dispositivo. Execute o `Test-HcsmConnection` cmdlet a partir do Interface do Windows PowerShell para StorSimple no seu dispositivo para identificar e corrigir o problema. Se as definições estão corretas, o problema poderá ser com as credenciais da conta do storage para o qual o alerta foi gerado. Neste caso, utilize o `Test-HcsStorageAccountCredential` cmdlet para determinar se existem problemas que possa resolver.<ul><li>Verifique as definições de rede.</li><li>Verifique as credenciais da conta de armazenamento.</li></ul> |
| Não recebemos um heartbeat do seu dispositivo para a última <*número*> minutos. |Não é possível ligar ao dispositivo. |Parece que existe um problema de conectividade com o seu dispositivo. Utilize o `Test-HcsmConnection` cmdlet a partir do Interface do Windows PowerShell para StorSimple no seu dispositivo para identificar e corrigir o problema ou contacte o administrador de rede. |

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>Comportamento de StorSimple quando ocorre uma falha de conectividade na cloud

O que acontece se falhar de conectividade na cloud para o meu dispositivo StorSimple em execução na produção?

Se falhar a conectividade de cloud no seu dispositivo de produção do StorSimple, em seguida, dependendo do Estado do seu dispositivo, o seguinte pode ocorrer:

* **Para os dados locais no seu dispositivo**: Há algum tempo, não haverá nenhuma interrupção e leituras irão continuar a ser atendidos. No entanto, como o número de e/s pendentes aumenta e excede um limite, as leituras poderiam começam a falhar.

    Dependendo da quantidade de dados no seu dispositivo, as gravações continuará também ocorra para as primeiras horas após a interrupção na conectividade da cloud. As gravações irão, em seguida, mais lentos e, eventualmente, começam a falhar se a conectividade da cloud é interrompida durante várias horas. (Há armazenamento temporário no dispositivo para dados que está a ser enviado para a cloud. Esta área é descarregada quando os dados são enviados. Se falhar a conectividade, dados nesta área de armazenamento não são enviados para a cloud e falharão em e/s.)
* **Para os dados na cloud**: Para a maioria dos erros de conectividade de cloud, é devolvido um erro. Assim que a conectividade é restaurada, os IOs são retomados sem que o utilizador ter de colocar o volume online. Em situações raras, intervenção do utilizador poderá ser necessária para trazer de volta o volume online no portal do Azure.
* **Para instantâneos de cloud em curso**: A operação será repetida algumas vezes dentro de 4 a 5 horas e se a conectividade não é restaurada, os instantâneos de cloud irão falhar.

### <a name="cluster-alerts"></a>Alertas de cluster

| Texto de alerta | Evento | Obter mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Dispositivo foi efetuada para <*nome do dispositivo*>. |Dispositivo estiver no modo de manutenção. |Dispositivo ativação pós-falha porque entrou ou saiu do modo de manutenção. Esta situação é normal e é necessária nenhuma ação. Depois de confirmar este alerta, limpe-o da página de alertas. |
| Dispositivo foi efetuada para <*nome do dispositivo*>. |Firmware do dispositivo ou de software apenas foi atualizado. |Ocorreu uma ativação pós-falha do cluster devido a uma atualização. Esta situação é normal e é necessária nenhuma ação. Depois de confirmar este alerta, limpe-o da página de alertas. |
| Dispositivo foi efetuada para <*nome do dispositivo*>. |Controlador foi encerrado ou reiniciado. |Dispositivo ativação pós-falha porque o controlador ativo foi encerrado ou reiniciado por um administrador. Não é necessária nenhuma ação. Depois de confirmar este alerta, limpe-o da página de alertas. |
| Dispositivo foi efetuada para <*nome do dispositivo*>. |Ativação pós-falha planeada. |Certifique-se de que isso era uma ativação pós-falha planeada. Depois de tomar as medidas adequadas, limpe este alerta da página de alertas. |
| Dispositivo foi efetuada para <*nome do dispositivo*>. |Ativação pós-falha não planeada. |StorSimple foi concebido para recuperar automaticamente de ativações pós-falha não planeadas. Se vir um grande número destes alertas, contacte Support da Microsoft. |
| Dispositivo foi efetuada para <*nome do dispositivo*>. |Causa outra/desconhecida. |Se vir um grande número destes alertas, contacte Support da Microsoft. Depois do problema for resolvido, limpe este alerta da página de alertas. |
| Um serviço de dispositivo comunica o estado como falhado. |Falha no serviço de DataPath. |Entre em contato com Support da Microsoft para obter assistência. |
| Endereço IP virtual para a interface de rede <*dados #*> relatórios de estado como falhado. |Causa outra/desconhecida. |Condições algumas vezes temporárias podem provocar estes alertas. Se for este o caso, em seguida, este alerta será limpo automaticamente após algum tempo. Se o problema persistir, contacte o Suporte da Microsoft. |
| Endereço IP virtual para a interface de rede <*dados #*> relatórios de estado como falhado. |Nome da interface: <*dados n. º*> endereço IP <IP address> não pode ser colocado online, porque foi detetado um endereço IP duplicado na rede. |Certifique-se de que o endereço IP duplicado é removido da rede ou reconfigurar a interface com um endereço IP diferente. |

### <a name="disaster-recovery-alerts"></a>Alertas de recuperação após desastre

| Texto de alerta | Evento | Obter mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Operações de recuperação não foi possível restaurar todas as definições para este serviço. Dados de configuração do dispositivo estão em estado inconsistente em alguns dispositivos. |Dados foi detetada uma inconsistência após a recuperação após desastre. |Os dados encriptados no serviço não estão sincronizados com o mesmo no dispositivo. Autorizar o dispositivo <*nome do dispositivo*> do StorSimple Device Manager para iniciar o processo de sincronização. Utilize a Interface do Windows PowerShell para StorSimple para executar o `Restore-HcsmEncryptedServiceData` no dispositivo <*nome do dispositivo*> cmdlet, fornecendo a palavra-passe antiga como entrada para este cmdlet para restaurar o perfil de segurança. Em seguida, execute o `Invoke-HcsmServiceDataEncryptionKeyChange` cmdlet para atualizar a chave de encriptação de dados do serviço. Depois de tomar as medidas adequadas, limpe este alerta da página de alertas. |

### <a name="hardware-alerts"></a>Alertas de hardware

| Texto de alerta | Evento | Obter mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Componente de hardware <*ID de componente*> comunica o estado como <*estado*>. | |Condições algumas vezes temporárias podem provocar estes alertas. Se assim for, este alerta será limpo automaticamente após algum tempo. Se o problema persistir, contacte o Suporte da Microsoft. |
| Controlador passivo a funcionar incorretamente. |O controlador passivo (secundário) não está a funcionar. |O dispositivo está operacional, mas um dos controladores está a funcionar incorretamente. Tente reiniciar esse controlador. Se o problema não for resolvido, contacte o Support da Microsoft. |

### <a name="job-failure-alerts"></a>Alertas de falha da tarefa

| Texto de alerta | Evento | Obter mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Cópia de segurança de <*ID de grupo do volume de origem*> falha. |Falha na tarefa de cópia de segurança. |Problemas de conectividade podem estar a impedir a operação de cópia de segurança conclusão com êxito. Se não existirem não existem problemas de conectividade, poderá ter atingido o número máximo de cópias de segurança. Elimine quaisquer cópias de segurança que já não são necessários e repita a operação. Depois de tomar as medidas adequadas, limpe este alerta da página de alertas. |
| Clone de <*IDs de elemento de cópia de segurança de origem*> para <*números de série do volume de destino*> falha. |Falha na tarefa de clone. |Atualize a lista de cópia de segurança para verificar que a cópia de segurança ainda é válida. Se a cópia de segurança for válida, é possível que os problemas de conectividade de cloud estão a impedir a operação de clonagem conclusão com êxito. Se não existirem não existem problemas de conectividade, poderá ter atingido o limite de armazenamento. Elimine quaisquer cópias de segurança que já não são necessários e repita a operação. Depois de tomar as medidas adequadas para resolver o problema, limpe este alerta da página de alertas. |
| Restauro de <*IDs de elemento de cópia de segurança de origem*> falha. |Falha na tarefa de restauro. |Atualize a lista de cópia de segurança para verificar que a cópia de segurança ainda é válida. Se a cópia de segurança for válida, é possível que os problemas de conectividade de cloud estão a impedir a operação de restauro conclusão com êxito. Se não existirem não existem problemas de conectividade, poderá ter atingido o limite de armazenamento. Elimine quaisquer cópias de segurança que já não são necessários e repita a operação. Depois de tomar as medidas adequadas para resolver o problema, limpe este alerta da página de alertas. |

### <a name="locally-pinned-volume-alerts"></a>Alertas de volume afixado localmente

| Texto de alerta | Evento | Obter mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Criação do local volume <*nome do volume*> falha. |A tarefa de criação do volume falhou. <*Mensagem de erro correspondente ao código de erro com falha*>. |Problemas de conectividade podem estar a impedir a operação de criação de espaço conclusão com êxito. Os volumes afixados localmente são amplamente aprovisionados e o processo de criação de espaço envolve a passagem de volumes escalonados para a cloud. Se não existirem não existem problemas de conectividade, pode ter esgotado o espaço local no dispositivo. Determine se existe espaço no dispositivo antes de repetir esta operação. |
| A expansão do local volume <*nome do volume*> falha. |A tarefa de modificação de volume falhou devido a <*mensagem de erro correspondente ao código de erro com falha*>. |Problemas de conectividade podem estar a impedir a operação de expansão do volume conclusão com êxito. Os volumes afixados localmente são amplamente aprovisionados e o processo de expansão de espaço existente envolve a passagem de volumes escalonados para a cloud. Se não existirem não existem problemas de conectividade, pode ter esgotado o espaço local no dispositivo. Determine se existe espaço no dispositivo antes de repetir esta operação. |
| Conversão do volume <*nome do volume*> falha. |A tarefa de conversão do volume para converter o tipo de volume de localmente afixado para camadas falhou. |Conversão do volume do tipo localmente afixado em camadas não foi possível concluir. Certifique-se de que não existem não existem problemas de conectividade a impedir a operação a conclusão com êxito. Para resolução de problemas de conectividade, problemas aceda a [resolver problemas com o cmdlet Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>O volume afixado localmente original agora foi marcado como um volume em camadas, uma vez que alguns dos dados do volume afixado localmente tem vazam para a cloud durante a conversão. O volume em camadas resultante continua a ocupar espaço local no dispositivo que não pode ser recuperado para futuras volumes locais.<br>Resolva os problemas de conectividade, limpe o alerta e converter este volume para o tipo original do volume afixado localmente para garantir que todos os dados ficam disponíveis localmente novamente. |
| Conversão do volume <*nome do volume*> falha. |A tarefa de conversão do volume para converter o tipo de volume de camadas para localmente afixado falhou. |Conversão do volume do tipo em camadas para localmente afixado não foi possível concluir. Certifique-se de que não existem não existem problemas de conectividade a impedir a operação a conclusão com êxito. Para resolução de problemas de conectividade, problemas aceda a [resolver problemas com o cmdlet Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>O volume em camadas original agora marcado como um volume localmente afixado, como parte do processo de conversão continua a ter dados que residem na cloud, enquanto o espaço amplamente aprovisionado no dispositivo para este volume já não pode ser recuperado para futuras volumes locais.<br>Resolva os problemas de conectividade, limpe o alerta e converter este volume para o tipo de volume em camadas original para garantir que pode ser recuperado espaço local amplamente aprovisionado no dispositivo. |
| Próximo do consumo de espaço local para instantâneos locais de <*nome do grupo de volume*> |Os instantâneos locais para a política de cópia de segurança em breve poderão ficar sem espaço e ser invalidados para evitar falhas de escrita do anfitrião. |Os instantâneos locais frequentes juntamente com inúmeras alterações a dados nos volumes associados a este grupo de política de cópia de segurança estão a causar o espaço local no dispositivo seja consumido rapidamente. Elimine quaisquer instantâneos locais que já não são necessários. Além disso, Atualize as agendas de instantâneos locais para esta política de cópia de segurança tirar instantâneos locais menos frequentes e certifique-se de que os instantâneos de cloud são efetuados regularmente. Se estas ações não são executadas, em breve poderá ser esgotado o espaço local para estes instantâneos e o sistema será automaticamente eliminá-los para garantir que gravações de anfitrião continuam a ser processado com êxito. |
| Os instantâneos locais para <*nome do grupo de volume*> foram invalidados. |Os instantâneos locais para <*nome do grupo de volume*> foram invalidados e eliminados, em seguida, porque eles foram a exceder o espaço local no dispositivo. |Para garantir que isto não volta a ocorrer no futuro, reveja as agendas de instantâneo local para esta política de cópia de segurança e elimine quaisquer instantâneos locais que já não são necessários. Os instantâneos locais frequentes juntamente com inúmeras alterações a dados nos volumes associados a este grupo de política de cópia de segurança poderão provocar o espaço local no dispositivo seja consumido rapidamente. |
| Restauro de <*IDs de elemento de cópia de segurança de origem*> falha. |A tarefa de restauro falhou. |Se tiver afixado localmente ou uma combinação de volumes localmente afixados e em camadas nesta política de cópia de segurança, atualize a lista de cópia de segurança para verificar se a cópia de segurança ainda é válida. Se a cópia de segurança for válida, é possível que os problemas de conectividade de cloud estão a impedir a operação de restauro conclusão com êxito. Os volumes afixados localmente a ser restaurados como parte deste grupo de instantâneo não tem todos os dados transferidos para o dispositivo e, se tiver uma mistura de volumes em camadas e afixados localmente neste grupo de instantâneo, eles não serão sincronizados entre si. Para concluir com êxito a operação de restauro, coloque os volumes neste grupo offline no anfitrião e repita a operação de restauro. Tenha em atenção que quaisquer modificações para os dados de volume que tenham sido efetuadas durante o processo de restauração serão perdidas. |

### <a name="networking-alerts"></a>Alertas de sistema de rede

| Texto de alerta | Evento | Obter mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Não foi possível iniciar o(s) serviço(s) do StorSimple. |Erro de DataPath |Se o problema persistir, contacte o Suporte da Microsoft. |
| Endereço IP duplicado detetado para 'Data0'. | |O sistema detetou um conflito no endereço IP "10.0.0.1". O recurso de rede 'Data0' no dispositivo *<device1>* está offline. Certifique-se de que este endereço IP não está a ser utilizado por qualquer outra entidade nesta rede. Para resolver problemas de rede, aceda a [resolver problemas com o cmdlet Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Contacte o administrador de rede para ajudar a resolver este problema. Se o problema persistir, contacte o Suporte da Microsoft. |
| Endereço IPv4 (ou IPv6) para 'Data0' está offline. | |O recurso de rede 'Data0' com o endereço IP "10.0.0.1". e o comprimento "22" no dispositivo do prefixo *<device1>* está offline. Certifique-se de que as portas de comutador a que esta interface está ligada estão operacionais. Para resolver problemas de rede, aceda a [resolver problemas com o cmdlet Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
| Não foi possível ligar ao serviço de autenticação. |Erro de DataPath |O URLthat é utilizado para autenticar não está acessível. Certifique-se de que as regras da firewall incluem os padrões de URL especificados para o dispositivo StorSimple. Para obter mais informações sobre padrões de URL no portal do Azure, aceda a https://aka.ms/ss-8000-network-reqs. Se utilizar o Azure Government Cloud, vá para os padrões de URL no https://aka.ms/ss8000-gov-network-reqs.|

### <a name="performance-alerts"></a>Alertas de desempenho

| Texto de alerta | Evento | Obter mais informações / ações recomendadas | |
|:--- |:--- |:--- | --- |
| A carga do dispositivo excedeu <*limiar*>. |Mais lento do que os tempos de resposta esperada. |O dispositivo comunica a utilização com uma carga de entrada/saída pesada. Isso poderia fazer com que o seu dispositivo para não funcionar tão bem como deveria. Reveja as cargas de trabalho que ligou ao dispositivo e determine se existir alguma que podem ser movidas para outro dispositivo ou que já não são necessários.|
| Não foi possível iniciar o(s) serviço(s) do StorSimple. |Erro de DataPath |Se o problema persistir, contacte o Suporte da Microsoft. |

### <a name="security-alerts"></a>Alertas de segurança

| Texto de alerta | Evento | Obter mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Foi iniciada uma sessão de Support da Microsoft. |Sessão de suporte de acesso de terceiros. |Confirme que este acesso está autorizado. Depois de tomar as medidas adequadas, limpe este alerta da página de alertas. |
| Palavra-passe para <*elemento*> irá expirar dentro de <*período de tempo*>. |Expiração de palavra-passe está a aproximar-se. |Altere a palavra-passe antes de expirar. |
| Informações de configuração de segurança em falta para <*ID de elemento*>. | |Os volumes associados este contentor de volume não podem ser utilizados para replicar a configuração do StorSimple. Para garantir que os dados estarão armazenados com segurança, recomendamos que elimine o contentor de volumes e todos os volumes associados com o contentor de volumes. Depois de tomar as medidas adequadas, limpe este alerta da página de alertas. |
| <*número*> tentativas de início de sessão falharam para <*ID do elemento*>. |Tentativas de logon vários falharem. |O dispositivo pode estar a sofrer um ataque ou um utilizador autorizado está a tentar estabelecer ligação com uma palavra-passe incorreta.<ul><li>Entre em contato com os utilizadores autorizados e certifique-se de que estas tentativas eram de uma origem legítima. Se continuar a ver um grande número de tentativas de início de sessão falhadas, considere desativar a gestão remota e entrar em contato com o administrador da rede. Depois de tomar as medidas adequadas, limpe este alerta da página de alertas.</li><li>Verifique que as instâncias do Snapshot Manager estão configuradas com a palavra-passe correta. Depois de tomar as medidas adequadas, limpe este alerta da página de alertas.</li></ul>Para obter mais informações, aceda a [alterar uma senha de dispositivo expiradas](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password). |
| Ocorreram uma ou mais falhas ao alterar a chave de encriptação de dados do serviço. | |Foram encontrados erros ao alterar a chave de encriptação de dados do serviço. Após ter resolvido as condições de erro, execute o `Invoke-HcsmServiceDataEncryptionKeyChange` cmdlet a partir do Interface do Windows PowerShell para StorSimple no seu dispositivo para atualizar o serviço. Se este problema persistir, contacte o suporte da Microsoft. Depois de resolver o problema, limpe este alerta da página de alertas. |

### <a name="support-package-alerts"></a>Alertas de pacote de suporte

| Texto de alerta | Evento | Obter mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Falha na criação do pacote de suporte. |StorSimple não foi possível gerar o pacote. |Repita esta operação. Se o problema persistir, contacte o Suporte da Microsoft. Depois de resolver o problema, limpe este alerta da página de alertas. |

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [StorSimple erros e resolução de problemas de implementação do dispositivo](storsimple-8000-troubleshoot-deployment.md).

