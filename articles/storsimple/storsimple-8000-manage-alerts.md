---
title: Exibir e gerenciar alertas para o dispositivo StorSimple da série 8000
description: Descreve as condições de alerta e a gravidade do StorSimple, como configurar notificações de alerta e como usar o serviço de Device Manager do StorSimple para gerenciar alertas.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: ff50836e1438b8d35f26ddfdf165084406f52faf
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76270831"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-storsimple-alerts"></a>Usar o serviço de Device Manager do StorSimple para exibir e gerenciar alertas do StorSimple

## <a name="overview"></a>Visão geral

A folha **alertas** no serviço de Device Manager do StorSimple fornece uma maneira de examinar e limpar os alertas relacionados ao dispositivo storsimple em tempo real. Nessa folha, você pode monitorar centralmente os problemas de integridade de seus dispositivos StorSimple e a solução de Microsoft Azure StorSimple geral.

Este tutorial descreve as condições de alerta comuns, os níveis de severidade de alerta e como configurar notificações de alerta. Além disso, ele inclui tabelas de referência rápida de alerta, que permitem localizar rapidamente um alerta específico e responder adequadamente.

![Página de alertas](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="common-alert-conditions"></a>Condições de alerta comuns

Seu dispositivo StorSimple gera alertas em resposta a uma variedade de condições. Estes são os tipos mais comuns de condições de alerta:

* **Problemas de hardware** – esses alertas informam sobre a integridade do seu hardware. Eles permitem que você saiba se as atualizações de firmware são necessárias, se uma interface de rede tiver problemas ou se houver um problema com uma de suas unidades de dados.
* **Problemas de conectividade** – esses alertas ocorrem quando há dificuldade na transferência de dados. Problemas de comunicação podem ocorrer durante a transferência de dados de e para a conta de armazenamento do Azure ou devido à falta de conectividade entre os dispositivos e o serviço StorSimple Device Manager. Os problemas de comunicação são alguns dos mais difíceis de corrigir, pois há muitos pontos de falha. Você sempre deve primeiro verificar se a conectividade de rede e o acesso à Internet estão disponíveis antes de continuar a solução de problemas mais avançada. Para obter ajuda com a solução de problemas, acesse [solucionar problemas com o cmdlet Test-Connection](storsimple-8000-troubleshoot-deployment.md).
* **Problemas de desempenho** – esses alertas são causados quando o sistema não está sendo executado de forma ideal, como quando ele está sob uma carga pesada.

Além disso, você pode ver alertas relacionados a falhas de segurança, atualizações ou trabalhos.

## <a name="alert-severity-levels"></a>Níveis de severidade do alerta

Os alertas têm diferentes níveis de severidade, dependendo do impacto que a situação de alerta terá e da necessidade de uma resposta ao alerta. Os níveis de gravidade são:

* **Crítico** – esse alerta está em resposta a uma condição que está afetando o desempenho bem-sucedido do seu sistema. A ação é necessária para garantir que o serviço StorSimple não seja interrompido.
* **Aviso** – essa condição pode se tornar crítica se não for resolvida. Você deve investigar a situação e executar qualquer ação necessária para apagar o problema.
* **Informações** – esse alerta contém informações que podem ser úteis para acompanhar e gerenciar seu sistema.

## <a name="configure-alert-settings"></a>Definir configurações de alerta

Você pode escolher se deseja ser notificado por email de condições de alerta para cada um de seus dispositivos StorSimple. Além disso, você pode identificar outros destinatários de notificação de alerta inserindo seus endereços de email na caixa **outros destinatários de email** , separados por ponto e vírgula.

> [!NOTE]
> Você pode inserir um máximo de 20 endereços de email por dispositivo.

Depois de habilitar a notificação por email para um dispositivo, os membros da lista de notificação receberão uma mensagem de email sempre que um alerta crítico ocorrer. As mensagens serão enviadas de *storsimple-Alerts-noreply\@mail.WindowsAzure.com* e descreverá a condição de alerta. Os destinatários podem clicar em **cancelar assinatura** para se removerem da lista de notificação por email.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Para habilitar a notificação por email de alertas para um dispositivo
1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Na lista de dispositivos, selecione e clique no dispositivo que você deseja configurar.
2. Vá para **configurações** > **geral** para o dispositivo.

   ![Folha alertas](./media/storsimple-8000-manage-alerts/configure-alerts-email2.png)
   
2. Na folha **configurações gerais** , vá para **configurações de alerta** e defina o seguinte:
   
   1. No campo **enviar notificação por email** , selecione **Sim**.
   2. No campo **Administradores do serviço de email** , selecione **Sim** para fazer com que o administrador de serviços e todos os coadministradores recebam as notificações de alerta.
   3. No campo **outros destinatários de email** , insira os endereços de email de todos os outros destinatários que devem receber as notificações de alerta. Insira nomes no formato *alguém\@Somewhere.com*. Use ponto e vírgula para separar os endereços de email. Você pode configurar um máximo de 20 endereços de email por dispositivo. 
      
3. Para enviar uma notificação de email de teste, clique em **Enviar email de teste**. O serviço StorSimple Device Manager exibirá mensagens de status à medida que ele encaminha a notificação de teste.

    ![Configurações de alerta](./media/storsimple-8000-manage-alerts/configure-alerts-email3.png)

4. Você verá uma notificação quando o email de teste for enviado. 
   
    ![Email de notificação de teste de alertas enviado](./media/storsimple-8000-manage-alerts/configure-alerts-email4.png)
   
   > [!NOTE]
   > Se a mensagem de notificação de teste não puder ser enviada, o serviço de Device Manager do StorSimple exibirá uma mensagem de erro apropriada. Aguarde alguns minutos e tente enviar a mensagem de notificação de teste novamente. 

5. Depois de concluir a configuração, clique em **salvar**. Quando lhe for pedida a confirmação, clique em **Sim**.

     ![Email de notificação de teste de alertas enviado](./media/storsimple-8000-manage-alerts/configure-alerts-email5.png)

## <a name="view-and-track-alerts"></a>Exibir e acompanhar alertas

A folha de resumo do serviço StorSimple Device Manager fornece uma visão rápida do número de alertas em seus dispositivos, organizados por nível de severidade.

![Painel de alertas](./media/storsimple-8000-manage-alerts/device-summary4.png)

Clicar no nível de severidade abre a folha **alertas** . Os resultados incluem apenas os alertas que correspondem a esse nível de severidade.

Clicar em um alerta na lista fornece detalhes adicionais para o alerta, incluindo a última vez em que o alerta foi relatado, o número de ocorrências do alerta no dispositivo e a ação recomendada para resolver o alerta. Se for um alerta de hardware, ele também identificará o componente de hardware.

![Exemplo de alerta de hardware](./media/storsimple-8000-manage-alerts/configure-alerts-email14.png)

Você pode copiar os detalhes do alerta para um arquivo de texto se precisar enviar as informações para Suporte da Microsoft. Depois de ter seguido a recomendação e resolvido a condição de alerta local, você deve limpar o alerta do dispositivo selecionando o alerta na folha **alertas** e clicando em **limpar**. Para limpar vários alertas, selecione cada alerta, clique em qualquer coluna, exceto na coluna **alerta** e, em seguida, clique em **limpar** depois de selecionar todos os alertas a serem limpos. Observe que alguns alertas são automaticamente apagados quando o problema é resolvido ou quando o sistema atualiza o alerta com novas informações.

Ao clicar em **limpar**, você terá a oportunidade de fornecer comentários sobre o alerta e as etapas necessárias para resolver o problema. Alguns eventos serão apagados pelo sistema se outro evento for disparado com novas informações. Nesse caso, a mensagem a seguir será exibida.

![Limpar mensagem de alerta](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Classificar e examinar alertas

Você pode achar mais eficiente executar relatórios sobre alertas para poder revisá-los e limpá-los em grupos. Além disso, a folha **alertas** pode exibir até 250 alertas. Se você excedeu esse número de alertas, nem todos os alertas serão exibidos no modo de exibição padrão. Você pode combinar os seguintes campos para personalizar quais alertas são exibidos:

* **Status** – você pode exibir alertas **ativos** ou **limpos** . Os alertas ativos ainda estão sendo disparados no sistema, enquanto os alertas limpos foram limpos manualmente por um administrador ou por meio de programação, pois o sistema atualizou a condição de alerta com novas informações.
* **Severidade** – você pode exibir alertas de todos os níveis de severidade (crítico, aviso, informações) ou apenas uma determinada gravidade, como apenas alertas críticos.
* **Fonte** – você pode exibir alertas de todas as fontes ou limitar os alertas àqueles provenientes do serviço ou de um ou de todos os dispositivos.
* **Intervalo de tempo** – especificando os carimbos de data e hora **de** e **até** , você pode examinar os alertas durante o período de tempo em que está interessado.

![Lista de alertas](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="alerts-quick-reference"></a>Referência rápida de alertas

As tabelas a seguir listam alguns dos alertas Microsoft Azure StorSimple que você pode encontrar, bem como informações adicionais e recomendações quando disponíveis. Os alertas do dispositivo StorSimple se enquadram em uma das seguintes categorias:

* [Alertas de conectividade de nuvem](#cloud-connectivity-alerts)
* [Alertas de cluster](#cluster-alerts)
* [Alertas de recuperação de desastre](#disaster-recovery-alerts)
* [Alertas de hardware](#hardware-alerts)
* [Alertas de falha de trabalho](#job-failure-alerts)
* [Alertas de volume localmente afixados](#locally-pinned-volume-alerts)
* [Alertas de rede](#networking-alerts)
* [Alertas de desempenho](#performance-alerts)
* [Alertas de segurança](#security-alerts)
* [Alertas do pacote de suporte](#support-package-alerts)

### <a name="cloud-connectivity-alerts"></a>Alertas de conectividade de nuvem

| Texto do alerta | Evento | Mais informações/ações recomendadas |
|:--- |:--- |:--- |
| Não é possível estabelecer conectividade com <*nome da credencial de nuvem*>. |Não é possível conectar-se à conta de armazenamento. |Parece que pode haver um problema de conectividade com seu dispositivo. Execute o cmdlet `Test-HcsmConnection` da interface do Windows PowerShell para StorSimple em seu dispositivo para identificar e corrigir o problema. Se as configurações estiverem corretas, o problema poderá ser com as credenciais da conta de armazenamento para a qual o alerta foi gerado. Nesse caso, use o cmdlet `Test-HcsStorageAccountCredential` para determinar se há problemas que você pode resolver.<ul><li>Verifique as configurações de rede.</li><li>Verifique suas credenciais de conta de armazenamento.</li></ul> |
| Não recebemos uma pulsação do seu dispositivo para o último*número*de < > minutos. |Não é possível se conectar ao dispositivo. |Parece que há um problema de conectividade com seu dispositivo. Use o cmdlet `Test-HcsmConnection` da interface do Windows PowerShell para StorSimple em seu dispositivo para identificar e corrigir o problema ou contate o administrador da rede. |

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>Comportamento do StorSimple quando a conectividade de nuvem falha

O que acontece se a conectividade de nuvem falhar para meu dispositivo StorSimple em execução na produção?

Se a conectividade de nuvem falhar em seu dispositivo de produção StorSimple, dependendo do estado do dispositivo, o seguinte pode ocorrer:

* **Para os dados locais em seu dispositivo**: há algum tempo, não haverá interrupção e as leituras continuarão a ser servidas. No entanto, como o número de IOs pendentes aumenta e excede um limite, as leituras podem começar a falhar.

    Dependendo da quantidade de dados em seu dispositivo, as gravações também continuarão ocorrendo nas primeiras horas após a interrupção na conectividade de nuvem. As gravações ficarão mais lentas e, eventualmente, começarão a falhar se a conectividade de nuvem for interrompida por várias horas. (Há um armazenamento temporário no dispositivo para dados que serão enviados por push para a nuvem. Essa área é liberada quando os dados são enviados. Se a conectividade falhar, os dados nessa área de armazenamento não serão enviados para a nuvem e a e/s falhará.)
* **Para os dados na nuvem**: para a maioria dos erros de conectividade de nuvem, um erro é retornado. Depois que a conectividade for restaurada, o IOs será retomado sem que o usuário precise colocar o volume online. Em casos raros, a intervenção do usuário pode ser necessária para retornar o volume online do portal do Azure.
* **Para instantâneos de nuvem em andamento**: a operação é repetida algumas vezes dentro de 4-5 horas e, se a conectividade não for restaurada, os instantâneos de nuvem falharão.

### <a name="cluster-alerts"></a>Alertas de cluster

| Texto do alerta | Evento | Mais informações/ações recomendadas |
|:--- |:--- |:--- |
| O dispositivo passou por failover para <*nome do dispositivo*>. |O dispositivo está no modo de manutenção. |O dispositivo passou por failover devido à inserção ou saída do modo de manutenção. Isso é normal e nenhuma ação é necessária. Depois de confirmar esse alerta, desmarque-o na página alertas. |
| O dispositivo passou por failover para <*nome do dispositivo*>. |O software ou o firmware do dispositivo acabou de ser atualizado. |Houve um failover de cluster devido a uma atualização. Isso é normal e nenhuma ação é necessária. Depois de confirmar esse alerta, desmarque-o na página alertas. |
| O dispositivo passou por failover para <*nome do dispositivo*>. |O controlador foi desligado ou reiniciado. |O dispositivo passou por failover porque o controlador ativo foi desligado ou reiniciado por um administrador. Nenhuma ação é necessária. Depois de confirmar esse alerta, desmarque-o na página alertas. |
| O dispositivo passou por failover para <*nome do dispositivo*>. |Failover planejado. |Verifique se esse foi um failover planejado. Depois de executar a ação apropriada, desmarque este alerta na página de alertas. |
| O dispositivo passou por failover para <*nome do dispositivo*>. |Failover não planejado. |O StorSimple foi criado para se recuperar automaticamente de failovers não planejados. Se você vir um grande número desses alertas, entre em contato com Suporte da Microsoft. |
| O dispositivo passou por failover para <*nome do dispositivo*>. |Outro/causa desconhecida. |Se você vir um grande número desses alertas, entre em contato com Suporte da Microsoft. Depois que o problema for resolvido, desmarque esse alerta na página alertas. |
| Um serviço de dispositivo crítico relata o status como com falha. |Falha de serviço de caminho de data. |Contate a Suporte da Microsoft para obter assistência. |
| O endereço IP virtual para o adaptador de rede <*Data #* > relata o status como com falha. |Outro/causa desconhecida. |Às vezes, as condições temporárias podem causar esses alertas. Se esse for o caso, esse alerta será limpo automaticamente após algum tempo. Se o problema persistir, entre em contato com Suporte da Microsoft. |
| O endereço IP virtual para o adaptador de rede <*Data #* > relata o status como com falha. |Nome da interface: <*Data #* > endereço IP `<IP address>` não pode ser colocado online porque um endereço IP duplicado foi detectado na rede. |Verifique se o endereço IP duplicado foi removido da rede ou RECONFIGURE a interface com um endereço IP diferente. |

### <a name="disaster-recovery-alerts"></a>Alertas de recuperação de desastre

| Texto do alerta | Evento | Mais informações/ações recomendadas |
|:--- |:--- |:--- |
| As operações de recuperação não puderam restaurar todas as configurações para este serviço. Os dados de configuração do dispositivo estão em um estado inconsistente para alguns dispositivos. |Inconsistência de dados detectada após a recuperação de desastre. |Os dados criptografados no serviço não estão sincronizados com isso no dispositivo. Autorize o dispositivo <*nome do dispositivo*> do StorSimple Device Manager para iniciar o processo de sincronização. Use a interface do Windows PowerShell para o StorSimple para executar o `Restore-HcsmEncryptedServiceData` no dispositivo <*nome do dispositivo*> cmdlet, fornecendo a senha antiga como uma entrada para este cmdlet para restaurar o perfil de segurança. Em seguida, execute o cmdlet `Invoke-HcsmServiceDataEncryptionKeyChange` para atualizar a chave de criptografia de dados de serviço. Depois de executar a ação apropriada, desmarque este alerta na página de alertas. |

### <a name="hardware-alerts"></a>Alertas de hardware

| Texto do alerta | Evento | Mais informações/ações recomendadas |
|:--- |:--- |:--- |
| O componente de hardware <*ID de componente*> status de relatórios < > de*status*. | |Às vezes, as condições temporárias podem causar esses alertas. Nesse caso, esse alerta será limpo automaticamente após algum tempo. Se o problema persistir, entre em contato com Suporte da Microsoft. |
| Controlador passivo com funcionamento incorreto. |O controlador passivo (secundário) não está funcionando. |O dispositivo está funcionando, mas um de seus controladores está com problemas de funcionamento. Tente reiniciar esse controlador. Se o problema não for resolvido, entre em contato com Suporte da Microsoft. |

### <a name="job-failure-alerts"></a>Alertas de falha de trabalho

| Texto do alerta | Evento | Mais informações/ações recomendadas |
|:--- |:--- |:--- |
| Falha no backup da*ID do grupo de volumes de origem*< >. |Falha no trabalho de backup. |Problemas de conectividade podem estar impedindo que a operação de backup seja concluída com êxito. Se não houver nenhum problema de conectividade, você pode ter atingido o número máximo de backups. Exclua todos os backups que não são mais necessários e repita a operação. Depois de executar a ação apropriada, desmarque este alerta na página de alertas. |
| O clone de <*IDs de elemento de backup de origem*> para < números de série de volume de*destino*> falhou. |Falha no trabalho de clonagem. |Atualize a lista de backup para verificar se o backup ainda é válido. Se o backup for válido, é possível que problemas de conectividade de nuvem estejam impedindo que a operação de clonagem seja concluída com êxito. Se não houver nenhum problema de conectividade, você pode ter atingido o limite de armazenamento. Exclua todos os backups que não são mais necessários e repita a operação. Depois de executar a ação apropriada para resolver o problema, desmarque este alerta na página de alertas. |
| Falha na restauração de <*IDs de elemento de backup de origem*>. |Falha no trabalho de restauração. |Atualize a lista de backup para verificar se o backup ainda é válido. Se o backup for válido, é possível que problemas de conectividade de nuvem estejam impedindo que a operação de restauração seja concluída com êxito. Se não houver nenhum problema de conectividade, você pode ter atingido o limite de armazenamento. Exclua todos os backups que não são mais necessários e repita a operação. Depois de executar a ação apropriada para resolver o problema, desmarque este alerta na página de alertas. |

### <a name="locally-pinned-volume-alerts"></a>Alertas de volume localmente afixados

| Texto do alerta | Evento | Mais informações/ações recomendadas |
|:--- |:--- |:--- |
| Falha na criação do volume local <*nome do volume*>. |Falha no trabalho de criação de volume. <*mensagem de erro correspondente ao código de erro com falha*>. |Problemas de conectividade podem estar impedindo que a operação de criação de espaço seja concluída com êxito. Os volumes fixados localmente são provisionados de forma densa e o processo de criação de espaço envolve despejar volumes em camadas para a nuvem. Se não houver nenhum problema de conectividade, você poderá ter esgotado o espaço local no dispositivo. Determine se há espaço no dispositivo antes de repetir esta operação. |
| Falha na expansão do volume local < o*nome do volume*>. |O trabalho de modificação de volume falhou devido a <*mensagem de erro correspondente ao código de erro com falha*>. |Problemas de conectividade podem estar impedindo que a operação de expansão do volume seja concluída com êxito. Os volumes fixados localmente são provisionados de forma densa e o processo de estender o espaço existente envolve despejar volumes em camadas para a nuvem. Se não houver nenhum problema de conectividade, você poderá ter esgotado o espaço local no dispositivo. Determine se há espaço no dispositivo antes de repetir esta operação. |
| Falha na conversão do volume < o*nome do volume*>. |O trabalho de conversão de volume para converter o tipo de volume de fixado localmente para em camadas falhou. |Não foi possível concluir a conversão do volume do tipo fixado localmente em camadas. Verifique se não há problemas de conectividade impedindo que a operação seja concluída com êxito. Para solucionar problemas de conectividade, vá para [solucionar problemas com o cmdlet Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>O volume original fixado localmente agora foi marcado como um volume em camadas, pois alguns dos dados do volume fixado localmente foram despejados para a nuvem durante a conversão. O volume em camadas resultante ainda ocupa o espaço local no dispositivo que não pode ser recuperado para futuros volumes locais.<br>Resolva quaisquer problemas de conectividade, desmarque o alerta e converta esse volume de volta para o tipo de volume original fixado localmente para garantir que todos os dados sejam disponibilizados localmente novamente. |
| Falha na conversão do volume < o*nome do volume*>. |O trabalho de conversão de volume para converter o tipo de volume de em camadas para localmente afixado falhou. |Não foi possível concluir a conversão do volume do tipo em camadas para fixado localmente. Verifique se não há problemas de conectividade impedindo que a operação seja concluída com êxito. Para solucionar problemas de conectividade, vá para [solucionar problemas com o cmdlet Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>O volume em camadas original agora marcado como um volume fixado localmente como parte do processo de conversão continua a ter dados que residem na nuvem, enquanto o espaço provisionado espesso no dispositivo para esse volume não pode mais ser recuperado para futuros volumes locais.<br>Resolva quaisquer problemas de conectividade, desmarque o alerta e converta esse volume de volta para o tipo de volume original em camadas para garantir que o espaço local provisionado de forma espessa no dispositivo possa ser recuperado. |
| Perto do consumo de espaço local para instantâneos locais de <*nome do grupo de volumes*> |Os instantâneos locais para a política de backup podem ficar sem espaço em breve e ser invalidados para evitar falhas de gravação do host. |Os instantâneos locais frequentes juntamente com uma variação de dados alta nos volumes associados a esse grupo de políticas de backup estão fazendo com que o espaço local no dispositivo seja consumido rapidamente. Exclua todos os instantâneos locais que não são mais necessários. Além disso, atualize suas agendas de instantâneos locais para essa política de backup para obter instantâneos locais menos frequentes e verifique se os instantâneos de nuvem são feitos regularmente. Se essas ações não forem realizadas, o espaço local para esses instantâneos poderá ser esgotado em breve e o sistema os excluirá automaticamente para garantir que as gravações do host continuem sendo processadas com êxito. |
| Os instantâneos locais para <*nome do grupo de volumes*> foram invalidados. |Os instantâneos locais para <*nome do grupo de volumes*> foram invalidados e, em seguida, foram excluídos porque estavam excedendo o espaço local no dispositivo. |Para garantir que isso não ocorra no futuro, examine os agendamentos do instantâneo local para essa política de backup e exclua os instantâneos locais que não são mais necessários. Os instantâneos locais frequentes juntamente com uma variação de dados alta nos volumes associados a esse grupo de políticas de backup podem fazer com que o espaço local no dispositivo seja consumido rapidamente. |
| Falha na restauração de <*IDs de elemento de backup de origem*>. |Falha no trabalho de restauração. |Se você tiver fixado localmente ou uma combinação de volumes fixados localmente e em camadas nessa política de backup, atualize a lista de backup para verificar se o backup ainda é válido. Se o backup for válido, é possível que problemas de conectividade de nuvem estejam impedindo que a operação de restauração seja concluída com êxito. Os volumes localmente afixados que estavam sendo restaurados como parte desse grupo de instantâneos não têm todos os seus dados baixados para o dispositivo e, se você tiver uma combinação de volumes em camadas e fixados localmente nesse grupo de instantâneos, eles não estarão sincronizados entre si. Para concluir a operação de restauração com êxito, coloque os volumes nesse grupo offline no host e repita a operação de restauração. Observe que qualquer modificação nos dados de volume que foram executados durante o processo de restauração será perdida. |

### <a name="networking-alerts"></a>Alertas de rede

| Texto do alerta | Evento | Mais informações/ações recomendadas |
|:--- |:--- |:--- |
| Não foi possível iniciar serviço (s) StorSimple. |Erro de caminho de |Se o problema persistir, entre em contato com Suporte da Microsoft. |
| Endereço IP duplicado detectado para ' Data0 '. | |O sistema detectou um conflito para o endereço IP ' 10.0.0.1 '. O recurso de rede ' Data0 ' no dispositivo *\<device1 >* está offline. Verifique se esse endereço IP não é usado por nenhuma outra entidade nesta rede. Para solucionar problemas de rede, acesse [solucionar problemas com o cmdlet Get-netadapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Contate o administrador de rede para obter ajuda para resolver esse problema. Se o problema persistir, entre em contato com Suporte da Microsoft. |
| O endereço IPv4 (ou IPv6) para ' Data0 ' está offline. | |O recurso de rede ' Data0 ' com o endereço IP ' 10.0.0.1. ' e o comprimento do prefixo ' 22 ' no dispositivo *\<device1 >* está offline. Verifique se as portas do comutador às quais essa interface está conectada estão operacionais. Para solucionar problemas de rede, acesse [solucionar problemas com o cmdlet Get-netadapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
| Não foi possível conectar-se ao serviço de autenticação. |Erro de caminho de |O URL é usado para autenticar não está acessível. Verifique se as regras de firewall incluem os padrões de URL especificados para o dispositivo StorSimple. Para obter mais informações sobre padrões de URL no portal do Azure, Acesse https:\//aka.ms/ss-8000-network-reqs. Se estiver usando a nuvem do Azure governamental, vá para os padrões de URL em https:\//aka.ms/ss8000-gov-network-reqs.|

### <a name="performance-alerts"></a>Alertas de desempenho

| Texto do alerta | Evento | Mais informações/ações recomendadas | |
|:--- |:--- |:--- | --- |
| A carga do dispositivo excedeu <*limite*>. |Mais lento do que os tempos de resposta esperados. |Seu dispositivo relata a utilização em uma carga pesada de entrada/saída. Isso pode fazer com que o dispositivo não funcione tão bem quanto deveria. Examine as cargas de trabalho que você anexou ao dispositivo e determine se há alguma que possa ser movida para outro dispositivo ou que não é mais necessária.|
| Não foi possível iniciar serviço (s) StorSimple. |Erro de caminho de |Se o problema persistir, entre em contato com Suporte da Microsoft. |

### <a name="security-alerts"></a>Alertas de segurança

| Texto do alerta | Evento | Mais informações/ações recomendadas |
|:--- |:--- |:--- |
| Suporte da Microsoft sessão foi iniciada. |Sessão de suporte de terceiros acessados. |Confirme se este acesso está autorizado. Depois de executar a ação apropriada, desmarque este alerta na página de alertas. |
| A senha para o*elemento*< > expirará em <*período de tempo*>. |A expiração da senha está se aproximando. |Altere sua senha antes que ela expire. |
| Informações de configuração de segurança ausentes para <*ID do elemento*>. | |Os volumes associados a este contêiner de volume não podem ser usados para replicar a configuração do StorSimple. Para garantir que seus dados sejam armazenados com segurança, recomendamos que você exclua o contêiner de volume e todos os volumes associados ao contêiner de volume. Depois de executar a ação apropriada, desmarque este alerta na página de alertas. |
| *número*de <> tentativas de logon com falha para <*ID do elemento*>. |Várias tentativas de logon com falha. |Seu dispositivo pode estar sob ataque ou um usuário autorizado está tentando se conectar com uma senha incorreta.<ul><li>Entre em contato com seus usuários autorizados e verifique se essas tentativas foram provenientes de uma fonte legítima. Se você continuar a ver um grande número de tentativas de logon com falha, considere desabilitar o gerenciamento remoto e entrar em contato com o administrador da rede. Depois de executar a ação apropriada, desmarque este alerta na página de alertas.</li><li>Verifique se suas instâncias de Snapshot Manager estão configuradas com a senha correta. Depois de executar a ação apropriada, desmarque este alerta na página de alertas.</li></ul>Para obter mais informações, vá para [alterar uma senha de dispositivo expirada](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password). |
| Uma ou mais falhas ocorreram ao alterar a chave de criptografia de dados de serviço. | |Erros encontrados ao alterar a chave de criptografia de dados de serviço. Depois de solucionar as condições de erro, execute o cmdlet `Invoke-HcsmServiceDataEncryptionKeyChange` da interface do Windows PowerShell para StorSimple em seu dispositivo para atualizar o serviço. Se esse problema persistir, entre em contato com o suporte da Microsoft. Depois de resolver o problema, desmarque este alerta na página de alertas. |

### <a name="support-package-alerts"></a>Alertas do pacote de suporte

| Texto do alerta | Evento | Mais informações/ações recomendadas |
|:--- |:--- |:--- |
| Falha na criação do pacote de suporte. |O StorSimple não pôde gerar o pacote. |Repita a operação. Se o problema persistir, entre em contato com Suporte da Microsoft. Depois de resolver o problema, desmarque este alerta na página de alertas. |

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [erros do StorSimple e solução de problemas de implantação de dispositivo](storsimple-8000-troubleshoot-deployment.md).

