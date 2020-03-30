---
title: Ver e gerir alertas para dispositivo da série StorSimple 8000
description: Descreve as condições de alerta e a gravidade do StorSimple, como configurar notificações de alerta e como utilizar o serviço StorSimple Device Manager para gerir alertas.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: ff50836e1438b8d35f26ddfdf165084406f52faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267823"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-storsimple-alerts"></a>Utilize o serviço StorSimple Device Manager para visualizar e gerir alertas StorSimple

## <a name="overview"></a>Descrição geral

A lâmina **alerts** no serviço StorSimple Device Manager fornece uma forma de rever e limpar os alertas relacionados com o dispositivo StorSimple numa base em tempo real. A partir desta lâmina, pode monitorizar centralmente os problemas de saúde dos seus dispositivos StorSimple e a solução Global Microsoft Azure StorSimple.

Este tutorial descreve condições comuns de alerta, níveis de gravidade de alerta e como configurar notificações de alerta. Além disso, inclui tabelas de referência rápida de alerta, que lhe permitem localizar rapidamente um alerta específico e responder adequadamente.

![Página de alertas](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="common-alert-conditions"></a>Condições comuns de alerta

O seu dispositivo StorSimple gera alertas em resposta a uma variedade de condições. Seguem-se os tipos mais comuns de condições de alerta:

* **Problemas** de hardware – Estes alertas contam-lhe sobre a saúde do seu hardware. Eles informam-no se são necessárias atualizações de firmware, se uma interface de rede tem problemas, ou se há algum problema com uma das suas unidades de dados.
* **Problemas de conectividade** – Estes alertas ocorrem quando há dificuldade em transferir dados. Os problemas de comunicação podem ocorrer durante a transferência de dados de e para a conta de armazenamento Do Azure ou devido à falta de conectividade entre os dispositivos e o serviço StorSimple Device Manager. As questões de comunicação são algumas das mais difíceis de resolver porque há tantos pontos de falha. Deve verificar sempre primeiro que a conectividade da rede e o acesso à Internet estão disponíveis antes de continuar a ter problemas mais avançados. Para obter ajuda na resolução de problemas, vá a [Troubleshoot com o cmdlet](storsimple-8000-troubleshoot-deployment.md)de Ligação de Teste .
* **Problemas de desempenho** – Estes alertas são causados quando o seu sistema não está a funcionar da melhor forma, como quando está sob uma carga pesada.

Além disso, pode ver alertas relacionados com segurança, atualizações ou falhas de emprego.

## <a name="alert-severity-levels"></a>Níveis de gravidade do alerta

Os alertas têm diferentes níveis de gravidade, dependendo do impacto que a situação de alerta terá e da necessidade de uma resposta ao alerta. Os níveis de gravidade são:

* **Critical** – Este alerta é em resposta a uma condição que está a afetar o desempenho bem-sucedido do seu sistema. É necessária uma ação para garantir que o serviço StorSimple não seja interrompido.
* **Aviso** – Esta condição pode tornar-se crítica se não for resolvida. Deve investigar a situação e tomar todas as medidas necessárias para resolver a questão.
* **Informação** – Este alerta contém informações que podem ser úteis no rastreio e gestão do seu sistema.

## <a name="configure-alert-settings"></a>Configurar definições de alerta

Pode escolher se pretende ser notificado por e-mail de condições de alerta para cada um dos seus dispositivos StorSimple. Além disso, pode identificar outros destinatários de notificação de alerta introduzindo os seus endereços de e-mail na caixa **de destinatários de outros e-mails,** separados por pontos evícolas.

> [!NOTE]
> Pode introduzir um máximo de 20 endereços de e-mail por dispositivo.

Depois de ativar a notificação por e-mail de um dispositivo, os membros da lista de notificação receberão uma mensagem de e-mail sempre que ocorrer um alerta crítico. As mensagens serão enviadas a partir de *alertas\@storsimple-noreply mail.windowsazure.com* e descreverão a condição de alerta. Os destinatários podem clicar em Cancelar a **subscrição** para se retirarem da lista de notificação de e-mail.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Para ativar a notificação por e-mail de alertas para um dispositivo
1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. A partir da lista de dispositivos, selecione e clique no dispositivo que pretende configurar.
2. Vá ao **Definições** > **Gerais** para o dispositivo.

   ![Lâmina de alerta](./media/storsimple-8000-manage-alerts/configure-alerts-email2.png)
   
2. Na lâmina de **definições gerais,** vá às **definições** de Alerta e desloque o seguinte:
   
   1. No campo de notificação de **e-mail Enviar,** selecione **SIM**.
   2. No campo de administradores de **serviços de e-mail,** selecione **SIM** para que o administrador de serviço e todos os coadministradores recebam as notificações de alerta.
   3. No campo de outros destinatários de **e-mail,** insira os endereços de e-mail de todos os outros destinatários que deverão receber as notificações de alerta. Introduza nomes no formato *que alguém\@somewhere.com*. Utilize pontos evívias para separar os endereços de e-mail. Pode configurar um máximo de 20 endereços de e-mail por dispositivo. 
      
3. Para enviar uma notificação de e-mail de teste, clique **em Enviar o email**do teste . O serviço StorSimple Device Manager apresentará mensagens de estado à medida que reencaminha a notificação do teste.

    ![Definições de alerta](./media/storsimple-8000-manage-alerts/configure-alerts-email3.png)

4. Você vê uma notificação quando o e-mail de teste é enviado. 
   
    ![Alertas de notificação de teste enviados](./media/storsimple-8000-manage-alerts/configure-alerts-email4.png)
   
   > [!NOTE]
   > Se a mensagem de notificação de teste não puder ser enviada, o serviço StorSimple Device Manager apresentará uma mensagem de erro apropriada. Aguarde alguns minutos e tente enviar novamente a sua mensagem de notificação de teste. 

5. Depois de ter concluído a configuração, clique em **Guardar**. Quando lhe for pedida a confirmação, clique em **Sim**.

     ![Alertas de notificação de teste enviados](./media/storsimple-8000-manage-alerts/configure-alerts-email5.png)

## <a name="view-and-track-alerts"></a>Ver e acompanhar os alertas

A lâmina de resumo do serviço StorSimple Device Manager proporciona-lhe uma rápida olhada no número de alertas nos seus dispositivos, organizados pelo nível de gravidade.

![Painel de alertas](./media/storsimple-8000-manage-alerts/device-summary4.png)

Clicar no nível de gravidade abre a lâmina **alertas.** Os resultados incluem apenas os alertas que correspondem ao nível de gravidade.

Clicar num alerta na lista fornece-lhe detalhes adicionais para o alerta, incluindo a última vez que o alerta foi reportado, o número de ocorrências do alerta no dispositivo e a ação recomendada para resolver o alerta. Se for um alerta de hardware, também identificará o componente de hardware.

![Exemplo de alerta de hardware](./media/storsimple-8000-manage-alerts/configure-alerts-email14.png)

Pode copiar os dados de alerta para um ficheiro de texto se precisar enviar as informações para o Microsoft Support. Depois de ter seguido a recomendação e resolvido a condição de alerta no local, deverá limpar o alerta do dispositivo selecionando o alerta na lâmina **alerta e** clicando em **Clear**. Para limpar vários alertas, selecione cada alerta, clique em qualquer coluna exceto na coluna **'Alerta'** e, em seguida, clique em **Clear** depois de ter selecionado todos os alertas a serem apurados. Note que alguns alertas são automaticamente apurados quando o problema é resolvido ou quando o sistema atualiza o alerta com novas informações.

Quando clicar em **Clear,** terá a oportunidade de fornecer comentários sobre o alerta e os passos que tomou para resolver o problema. Alguns eventos serão apurados pelo sistema se outro evento for desencadeado com novas informações. Nesse caso, verá a seguinte mensagem.

![Mensagem de alerta clara](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Ordenar e rever alertas

Você pode achar mais eficiente executar relatórios sobre alertas para que você possa revê-los e limpá-los em grupos. Além disso, a lâmina **alertas** pode apresentar até 250 alertas. Se tiver excedido esse número de alertas, nem todos os alertas serão apresentados na vista padrão. Pode combinar os seguintes campos para personalizar quais os alertas apresentados:

* **Estado** – Pode apresentar alertas **Ativos** ou **Limpos.** Os alertas ativos continuam a ser acionados no seu sistema, enquanto os alertas limpos foram limpos manualmente por um administrador ou programaticamente limpos porque o sistema atualizou a condição de alerta com novas informações.
* **Gravidade** – Pode apresentar alertas de todos os níveis de gravidade (crítico, aviso, informação) ou apenas uma certa gravidade, como apenas alertas críticos.
* **Fonte** – Pode apresentar alertas de todas as fontes ou limitar os alertas a quem provém do serviço ou de um ou de todos os dispositivos.
* **Intervalo** de tempo – Especificando as datas **de From** e **To** e os selos de horário, pode ver alertas durante o período de tempo em que está interessado.

![Lista de alertas](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="alerts-quick-reference"></a>Alertas referência rápida

As tabelas seguintes listam alguns dos alertas Microsoft Azure StorSimple que poderá encontrar, bem como informações e recomendações adicionais sempre que disponível. Os alertas de dispositivoS StorSimple enquadram-se numa das seguintes categorias:

* [Alertas de conectividade em nuvem](#cloud-connectivity-alerts)
* [Alertas de cluster](#cluster-alerts)
* [Alertas de recuperação de desastres](#disaster-recovery-alerts)
* [Alertas de hardware](#hardware-alerts)
* [Alertas de insuficiência de emprego](#job-failure-alerts)
* [Alertas de volume fixados localmente](#locally-pinned-volume-alerts)
* [Alertas de rede](#networking-alerts)
* [Alertas de desempenho](#performance-alerts)
* [Alertas de segurança](#security-alerts)
* [Alertas de pacotes de apoio](#support-package-alerts)

### <a name="cloud-connectivity-alerts"></a>Alertas de conectividade em nuvem

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| A conectividade com <*nome da credencial da nuvem*> não pode ser estabelecida. |Não é possível ligar-se à conta de armazenamento. |Parece que pode haver um problema de conectividade com o seu dispositivo. Por favor, execute o `Test-HcsmConnection` cmdlet a partir da Interface Windows PowerShell para storSimple no seu dispositivo para identificar e corrigir o problema. Se as definições estiverem corretas, o problema pode estar nas credenciais da conta de armazenamento para a qual o alerta foi levantado. Neste caso, utilize `Test-HcsStorageAccountCredential` o cmdlet para determinar se existem problemas que possa resolver.<ul><li>Verifique as definições da sua rede.</li><li>Verifique as credenciais da sua conta de armazenamento.</li></ul> |
| Não recebemos um batimento cardíaco do seu dispositivo nos últimos <*número*> minutos. |Não é possível ligar ao dispositivo. |Parece que há um problema de conectividade com o seu dispositivo. Utilize o `Test-HcsmConnection` cmdlet da Interface Windows PowerShell para o StorSimple no seu dispositivo para identificar e corrigir o problema ou contactar o administrador de rede. |

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>Comportamento storSimple quando a conectividade na nuvem falha

O que acontece se a conectividade na nuvem falhar no meu dispositivo StorSimple em produção?

Se a conectividade da nuvem falhar no seu dispositivo de produção StorSimple, dependendo do estado do seu dispositivo, pode ocorrer o seguinte:

* **Para os dados locais no seu dispositivo**: Durante algum tempo, não haverá interrupções e as leituras continuarão a ser servidas. No entanto, à medida que o número de IO pendentes aumenta e excede um limite, as leituras podem começar a falhar.

    Dependendo da quantidade de dados no seu dispositivo, as escritas também continuarão a ocorrer durante as primeiras horas após a interrupção na conectividade da nuvem. As escritas irão então abrandar e eventualmente começar a falhar se a conectividade da nuvem for interrompida por várias horas. (Existe armazenamento temporário no dispositivo para dados que serão empurrados para a nuvem. Esta área é lavada quando os dados são enviados. Se a conectividade falhar, os dados nesta área de armazenamento não serão empurrados para a nuvem, e iO falhará.)
* **Para os dados na nuvem**: Para a maioria dos erros de conectividade na nuvem, um erro é devolvido. Uma vez restaurada a conectividade, os IOs são retomados sem que o utilizador tenha de colocar o volume online. Em casos raros, a intervenção do utilizador pode ser necessária para trazer de volta o volume on-line do portal Azure.
* **Para imagens em nuvem em andamento**: A operação é novamente experimentada algumas vezes dentro de 4-5 horas e se a conectividade não for restaurada, as imagens da nuvem falharão.

### <a name="cluster-alerts"></a>Alertas de cluster

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| O dispositivo falhou na <nome do *dispositivo*>. |O dispositivo está em modo de manutenção. |O dispositivo falhou devido à entrada ou saída do modo de manutenção. Isto é normal e não é necessária nenhuma ação. Depois de ter reconhecido este alerta, limpe-o da página de alertas. |
| O dispositivo falhou na <nome do *dispositivo*>. |O firmware ou software do dispositivo acabou de ser atualizado. |Houve uma falha de aglomerado devido a uma atualização. Isto é normal e não é necessária nenhuma ação. Depois de ter reconhecido este alerta, limpe-o da página de alertas. |
| O dispositivo falhou na <nome do *dispositivo*>. |O controlador foi desligado ou reiniciado. |O dispositivo falhou porque o controlador ativo foi desligado ou reiniciado por um administrador. Não é necessária nenhuma ação. Depois de ter reconhecido este alerta, limpe-o da página de alertas. |
| O dispositivo falhou na <nome do *dispositivo*>. |Falha planeada. |Verifique se isto foi uma falha planeada. Depois de ter tomado as medidas adequadas, limpe este alerta da página de alertas. |
| O dispositivo falhou na <nome do *dispositivo*>. |Falha não planeada. |O StorSimple foi construído para recuperar automaticamente de falhas não planeadas. Se vir um grande número destes alertas, contacte o Microsoft Support. |
| O dispositivo falhou na <nome do *dispositivo*>. |Outra causa/causa desconhecida. |Se vir um grande número destes alertas, contacte o Microsoft Support. Depois de o problema ser resolvido, limpe este alerta da página de alertas. |
| Um serviço de dispositivo crítico reporta o estado como falhado. |Falha no serviço datapath. |Contacte o Microsoft Support para obter assistência. |
| O endereço IP virtual para interface de rede <*DATA #*> refere o estado como falhado. |Outra causa/causa desconhecida. |Às vezes, as condições temporárias podem causar estes alertas. Se for esse o caso, este alerta será automaticamente apagado após algum tempo. Se o problema persistir, contacte o Microsoft Support. |
| O endereço IP virtual para interface de rede <*DATA #*> refere o estado como falhado. |Nome da interface: <`<IP address>` DATA *#*> endereço IP não pode ser colocado online porque foi detetado um endereço IP duplicado na rede. |Certifique-se de que o endereço IP duplicado é removido da rede ou reconfigura a interface com um endereço IP diferente. |

### <a name="disaster-recovery-alerts"></a>Alertas de recuperação de desastres

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| As operações de recuperação não puderam restaurar todas as configurações deste serviço. Os dados de configuração do dispositivo estão num estado inconsistente para alguns dispositivos. |Inconsistência de dados detetada após recuperação de desastres. |Os dados encriptados do serviço não são sincronizados com os dados no dispositivo. Autorize o dispositivo <nome do *dispositivo*> do StorSimple Device Manager para iniciar o processo de sincronização. Utilize a Interface Windows PowerShell para o `Restore-HcsmEncryptedServiceData` StorSimple para executar o nome <*dispositivo*> cmdlet, fornecendo a antiga palavra-passe como entrada para este cmdlet para restaurar o perfil de segurança. Em seguida, executar o `Invoke-HcsmServiceDataEncryptionKeyChange` cmdlet para atualizar a chave de encriptação de dados do serviço. Depois de ter tomado as medidas adequadas, limpe este alerta da página de alertas. |

### <a name="hardware-alerts"></a>Alertas de hardware

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| O componente de hardware <*o iD*> de identificação do componente> de relatórios como> *de estado* <. | |Às vezes, as condições temporárias podem causar estes alertas. Em caso afirmativo, este alerta será automaticamente apagado após algum tempo. Se o problema persistir, contacte o Microsoft Support. |
| Avaria do controlador passivo. |O controlador passivo (secundário) não está a funcionar. |O seu dispositivo está operacional, mas um dos seus controladores está avariado. Tente reiniciar o controlador. Se o problema não for resolvido, contacte o Microsoft Support. |

### <a name="job-failure-alerts"></a>Alertas de insuficiência de emprego

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| A cópia de segurança do> de *identificação* do grupo de volume de origem <falhou. |Trabalho de reserva falhou. |Problemas de conectividade podem estar a impedir que a operação de backup esteja a ser concluída com sucesso. Se não houver problemas de conectividade, pode ter atingido o número máximo de backups. Elimine quaisquer cópias de segurança que já não sejam necessárias e tente novamente a operação. Depois de ter tomado as medidas adequadas, limpe este alerta da página de alertas. |
| Clone de <elemento de *backup de <iDs*> para <*números* de série de volume de destino> falhou. |O trabalho de clone falhou. |Refresque a lista de cópias de segurança para verificar se a cópia de segurança ainda é válida. Se a cópia de segurança for válida, é possível que problemas de conectividade na nuvem estejam a impedir que a operação do clone esteja a ser concluída com sucesso. Se não houver problemas de conectividade, pode ter atingido o limite de armazenamento. Elimine quaisquer cópias de segurança que já não sejam necessárias e tente novamente a operação. Depois de ter tomado as medidas adequadas para resolver o problema, limpe este alerta da página de alertas. |
| Restaurar <elemento de *backup de iDs*> falhou. |Restaurar o trabalho falhou. |Refresque a lista de cópias de segurança para verificar se a cópia de segurança ainda é válida. Se a cópia de segurança for válida, é possível que problemas de conectividade em nuvem estejam a impedir que a operação de restauro esteja a ser concluída com sucesso. Se não houver problemas de conectividade, pode ter atingido o limite de armazenamento. Elimine quaisquer cópias de segurança que já não sejam necessárias e tente novamente a operação. Depois de ter tomado as medidas adequadas para resolver o problema, limpe este alerta da página de alertas. |

### <a name="locally-pinned-volume-alerts"></a>Alertas de volume fixados localmente

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| A criação de volume local <*nome de volume*> falhou. |O trabalho de criação de volume falhou. <*A mensagem de erro correspondente ao código de erro falhado*>. |Problemas de conectividade podem estar a impedir que a operação de criação espacial esteja a ser concluída com sucesso. Os volumes fixados localmente são densamente provisionados e o processo de criação de espaço envolve derramar volumes hierárquicos para a nuvem. Se não houver problemas de conectividade, pode ter esgotado o espaço local no dispositivo. Determine se existe espaço no dispositivo antes de voltar a tentar esta operação. |
| A expansão do volume local <*o nome* de volume> falhou. |O trabalho de modificação de volume falhou devido à <mensagem de *erro correspondente ao código* de erro falhado>. |Problemas de conectividade podem estar a impedir que a operação de expansão do volume esteja a ser concluída com sucesso. Os volumes fixados localmente são densamente provisionados e o processo de extensão do espaço existente envolve derramar volumes hierárquicos para a nuvem. Se não houver problemas de conectividade, pode ter esgotado o espaço local no dispositivo. Determine se existe espaço no dispositivo antes de voltar a tentar esta operação. |
| A conversão do volume <nome de *volume*> falhou. |O trabalho de conversão de volume para converter o tipo de volume de fixação local para tiered falhou. |A conversão do volume do tipo fixado localmente para o tiered não pôde ser concluída. Certifique-se de que não existem problemas de conectividade que impeçam a operação de completar com sucesso. Para problemas de resolução de problemas de conectividade vá para [Troubleshoot com o cmdlet Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>O volume original fixado localmente foi agora marcado como um volume hierárquico, uma vez que alguns dos dados do volume localmente fixado derramaram para a nuvem durante a conversão. O volume de nível resultante continua a ocupar o espaço local no dispositivo que não pode ser recuperado para futuros volumes locais.<br>Resolva quaisquer problemas de conectividade, limpe o alerta e converta este volume de volta para o tipo de volume original fixado localmente para garantir que todos os dados são disponibilizados localmente novamente. |
| A conversão do volume <nome de *volume*> falhou. |O trabalho de conversão de volume para converter o tipo de volume de tiered para localmente fixado falhou. |A conversão do volume de tipo tiered para fixação local não pôde ser concluída. Certifique-se de que não existem problemas de conectividade que impeçam a operação de completar com sucesso. Para problemas de resolução de problemas de conectividade vá para [Troubleshoot com o cmdlet Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>O volume original de camadas agora marcado como um volume fixado localmente como parte do processo de conversão continua a ter dados residentes na nuvem, enquanto o espaço densamente provisionado no dispositivo para este volume já não pode ser recuperado para futuros volumes locais.<br>Resolva quaisquer problemas de conectividade, limpe o alerta e converta este volume de volta para o tipo de volume original tiered para garantir que o espaço local aprovisionado no dispositivo pode ser recuperado. |
| Aproximando-se do consumo do espaço local para instantâneos locais de <nome de grupo de *volume*> |As imagens locais para a política de backup poderão em breve ficar sem espaço e ser invalidadas para evitar falhas de escrita do hospedeiro. |Fotografias locais frequentes ao lado de um alto volume de dados associados a este grupo de política de backup estão a fazer com que o espaço local no dispositivo seja consumido rapidamente. Elimine quaisquer instantâneos locais que já não sejam necessários. Além disso, atualize os seus horários locais para esta política de backup para tirar fotos locais menos frequentes e certifique-se de que as imagens de nuvem são tiradas regularmente. Se estas ações não forem tomadas, o espaço local para estas imagens poderá em breve ser esgotado e o sistema irá eliminá-los automaticamente para garantir que as escritas do hospedeiro continuem a ser processadas com sucesso. |
| Foram invalidadas fotografias locais para <nome de grupo de *volume>.* |As imagens locais para <nome de grupo de *volume*> foram invalidadas e depois apagadas porque estavam a exceder o espaço local no dispositivo. |Para garantir que isto não se repita no futuro, reveja os horários locais de instantâneo para esta política de backup e elimine quaisquer instantâneos locais que já não sejam necessários. Instantâneos locais frequentes ao lado de um alto volume de dados associados a este grupo de política de backup podem fazer com que o espaço local no dispositivo seja consumido rapidamente. |
| Restaurar <elemento de *backup de iDs*> falhou. |O trabalho de restauro falhou. |Se tiver fixado localmente ou uma mistura de volumes fixados e hierárquicos locais nesta política de backup, refresque a lista de cópias de segurança para verificar se a cópia de segurança ainda é válida. Se a cópia de segurança for válida, é possível que problemas de conectividade em nuvem estejam a impedir que a operação de restauro esteja a ser concluída com sucesso. Os volumes locais que estavam a ser restaurados como parte deste grupo instantâneo não têm todos os seus dados descarregados para o dispositivo e, se tiver uma mistura de volumes nigrindos e fixados localmente neste grupo de instantâneos, eles não estarão sincronizados uns com os outros. Para completar com sucesso a operação de restauro, desative os volumes deste grupo no hospedeiro e volte a tentar a operação de restauro. Note que quaisquer modificações nos dados de volume que foram realizadas durante o processo de restauro serão perdidas. |

### <a name="networking-alerts"></a>Alertas de rede

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Não poderia iniciar o serviço StorSimple(s). |Erro de datapath |Se o problema persistir, contacte o Microsoft Support. |
| Endereço IP duplicado detetado para 'Data0'. | |O sistema detetou um conflito para o endereço IP '10.0.0.1'. O recurso de rede 'Data0' no * \<dispositivo dispositivo1>* está offline. Certifique-se de que este endereço IP não é utilizado por nenhuma outra entidade nesta rede. Para resolver problemas de rede, vá a [Troubleshoot com o cmdlet Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Contacte o seu administrador de rede para obter ajuda para resolver este problema. Se o problema persistir, contacte o Microsoft Support. |
| O endereço IPv4 (ou IPv6) para 'Data0' está offline. | |O recurso de rede 'Data0' com endereço IP '10.0.0.1'. e pré-fixação do comprimento '22' no * \<dispositivo dispositivo1>* está offline. Certifique-se de que as portas de comutação às quais esta interface está ligada estão operacionais. Para resolver problemas de rede, vá a [Troubleshoot com o cmdlet Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
| Não conseguiu ligar-se ao serviço de autenticação. |Erro de datapath |O URL que é usado para autenticar não é alcançável. Certifique-se de que as suas regras de firewall incluem os padrões de URL especificados para o dispositivo StorSimple. Para obter mais informações sobre os padrões\/de URL no portal Azure, vá a https: /aka.ms/ss-8000-network-reqs. Se utilizar a Nuvem governamental Azure, vá\/aos padrões de URL em https: /aka.ms/ss8000-gov-network-reqs.|

### <a name="performance-alerts"></a>Alertas de desempenho

| Texto de alerta | Evento | Mais informações / ações recomendadas | |
|:--- |:--- |:--- | --- |
| A carga do dispositivo ultrapassou <*limiar*>. |Tempos de resposta mais lentos do que o esperado. |O seu dispositivo reporta a utilização sob uma carga de entrada/saída pesada. Isto pode fazer com que o seu dispositivo não funcione tão bem como deveria. Reveja as cargas de trabalho que tem ligado ao dispositivo e determine se existem alguma que possa ser transferida para outro dispositivo ou que já não seja necessária.|
| Não poderia iniciar o serviço StorSimple(s). |Erro de datapath |Se o problema persistir, contacte o Microsoft Support. |

### <a name="security-alerts"></a>Alertas de segurança

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| A sessão de Suporte do Microsoft já começou. |Sessão de apoio a terceiros acedeu. |Por favor, confirme que este acesso está autorizado. Depois de ter tomado as medidas adequadas, limpe este alerta da página de alertas. |
| A palavra-passe para <*elemento*> expirará em <*período de tempo*>. |A expiração da palavra-passe aproxima-se. |Mude a sua palavra-passe antes de expirar. |
| Informações de configuração de segurança em falta para <*elemento>.* | |Os volumes associados a este recipiente de volume não podem ser utilizados para replicar a sua configuração StorSimple. Para garantir que os seus dados estão armazenados com segurança, recomendamos que elimine o recipiente de volume e quaisquer volumes associados ao recipiente de volume. Depois de ter tomado as medidas adequadas, limpe este alerta da página de alertas. |
| <*número*> tentativas de login falharam para <*elemento id*>. |Várias tentativas falhadas de logon. |O seu dispositivo pode estar sob ataque ou um utilizador autorizado está a tentar ligar-se a uma palavra-passe incorreta.<ul><li>Contacte os seus utilizadores autorizados e verifique se estas tentativas foram de origem legítima. Se continuar a ver um grande número de tentativas falhadas de login, considere desativar a gestão remota e contacte o administrador da rede. Depois de ter tomado as medidas adequadas, limpe este alerta da página de alertas.</li><li>Verifique se as instâncias do Photo Manager estão configuradas com a palavra-passe correta. Depois de ter tomado as medidas adequadas, limpe este alerta da página de alertas.</li></ul>Para mais informações, vá alterar [uma senha de dispositivo expirada](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password). |
| Uma ou mais falhas ocorreram ao alterar a chave de encriptação de dados do serviço. | |Foram encontrados erros ao alterar a chave de encriptação de dados do serviço. Depois de ter abordado as condições de erro, execute o `Invoke-HcsmServiceDataEncryptionKeyChange` cmdlet a partir da Interface Windows PowerShell para o StorSimple no seu dispositivo para atualizar o serviço. Se este problema persistir, contacte o suporte da Microsoft. Depois de resolver o problema, limpe este alerta na página de alertas. |

### <a name="support-package-alerts"></a>Alertas de pacotes de apoio

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| O pacote de criação de apoio falhou. |O StorSimple não conseguiu gerar o pacote. |Tente novamente esta operação. Se o problema persistir, contacte o Microsoft Support. Depois de ter resolvido o problema, limpe este alerta na página de alertas. |

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [erros StorSimple e problemas](storsimple-8000-troubleshoot-deployment.md)de implementação de dispositivos de resolução de problemas .

