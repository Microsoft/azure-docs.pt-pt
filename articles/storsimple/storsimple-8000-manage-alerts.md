---
title: Ver e gerir alertas para o dispositivo da série StorSimple 8000
description: Descreve as condições de alerta e a gravidade do StorSimple, como configurar notificações de alerta e como utilizar o serviço StorSimple Device Manager para gerir alertas.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: bf41232026fcb51e63cb68d6f42b7b66d14022e1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86207795"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-storsimple-alerts"></a>Utilize o serviço StorSimple Device Manager para visualizar e gerir alertas StorSimple

## <a name="overview"></a>Descrição geral

A lâmina **alerts** no serviço StorSimple Device Manager fornece uma forma de rever e limpar alertas relacionados com dispositivos StorSimple em tempo real. A partir desta lâmina, pode monitorizar centralmente os problemas de saúde dos seus dispositivos StorSimple e a solução geral microsoft Azure StorSimple.

Este tutorial descreve condições de alerta comuns, níveis de gravidade de alerta e como configurar notificações de alerta. Além disso, inclui tabelas de referência rápidas de alerta, que permitem localizar rapidamente um alerta específico e responder adequadamente.

![Página de alertas](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="common-alert-conditions"></a>Condições de alerta comuns

O seu dispositivo StorSimple gera alertas em resposta a uma variedade de condições. Seguem-se os tipos mais comuns de condições de alerta:

* **Problemas de hardware** – Estes alertas dizem-lhe sobre a saúde do seu hardware. Eles avisam-no se são necessárias atualizações de firmware, se uma interface de rede tem problemas, ou se há algum problema com uma das suas unidades de dados.
* **Problemas de conectividade** – Estes alertas ocorrem quando há dificuldade em transferir dados. Problemas de comunicação podem ocorrer durante a transferência de dados de e para a conta de armazenamento Azure ou devido à falta de conectividade entre os dispositivos e o serviço StorSimple Device Manager. As questões de comunicação são algumas das mais difíceis de corrigir, porque há tantos pontos de falha. Deve verificar-se sempre primeiro se a conectividade da rede e o acesso à Internet estão disponíveis antes de continuar a uma resolução de problemas mais avançada. Para obter ajuda na resolução de problemas, vá à [resolução de problemas com o Test-Connection cmdlet](storsimple-8000-troubleshoot-deployment.md).
* **Problemas de desempenho** – Estes alertas são causados quando o seu sistema não está a funcionar da melhor forma, como quando está sob uma carga pesada.

Além disso, poderá ver alertas relacionados com segurança, atualizações ou falhas de emprego.

## <a name="alert-severity-levels"></a>Níveis de gravidade de alerta

Os alertas têm diferentes níveis de gravidade, dependendo do impacto que a situação de alerta terá e da necessidade de uma resposta ao alerta. Os níveis de gravidade são:

* **Crítico** – Este alerta é uma resposta a uma condição que está a afetar o desempenho bem sucedido do seu sistema. São necessárias medidas para garantir que o serviço StorSimple não seja interrompido.
* **Aviso** – Esta condição pode tornar-se crítica se não for resolvida. Deve investigar a situação e tomar todas as medidas necessárias para resolver o problema.
* **Informação** – Este alerta contém informações que podem ser úteis no rastreio e gestão do seu sistema.

## <a name="configure-alert-settings"></a>Configurar as definições de alertas

Pode escolher se pretende ser notificado por e-mail de condições de alerta para cada um dos seus dispositivos StorSimple. Além disso, pode identificar outros destinatários de notificação de alerta inserindo os seus endereços de e-mail na caixa de destinatários de **e-mail,** separada por pontos de e-mail.

> [!NOTE]
> Pode introduzir um máximo de 20 endereços de e-mail por dispositivo.

Depois de ativar a notificação por e-mail de um dispositivo, os membros da lista de notificações receberão uma mensagem de correio eletrónico sempre que ocorrer um alerta crítico. As mensagens serão *enviadas de alertas de storsimple-nor \@ profundamente mail.windowsazure.com* e descreverão a condição de alerta. Os destinatários podem clicar em Cancelar a **subscrição** para se removerem da lista de notificações por e-mail.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Para ativar a notificação por e-mail de alertas para um dispositivo
1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. A partir da lista de dispositivos, selecione e clique no dispositivo que deseja configurar.
2. Aceda às **definições**  >  **gerais** para o dispositivo.

   ![Lâmina de alerta](./media/storsimple-8000-manage-alerts/configure-alerts-email2.png)
   
2. Na lâmina **de definições gerais,** aceda às **definições de Alerta** e defina o seguinte:
   
   1. No campo **de notificação de email Enviar,** selecione **SIM**.
   2. No campo **de administradores de serviços de e-mail,** selecione **SIM** para que o administrador de serviço e todos os coadministradores recebam as notificações de alerta.
   3. No campo De destinatários de **e-mail, insira** os endereços de e-mail de todos os outros destinatários que devem receber as notificações de alerta. Introduza nomes no formato *que alguém \@ somewhere.com*. Utilize pontos de segurção para separar os endereços de e-mail. Pode configurar um máximo de 20 endereços de e-mail por dispositivo. 
      
3. Para enviar uma notificação de email de teste, clique em **Enviar email de teste**. O serviço StorSimple Device Manager apresentará mensagens de estado à medida que reencaminha a notificação de teste.

    ![Definições de alerta](./media/storsimple-8000-manage-alerts/configure-alerts-email3.png)

4. Vê uma notificação quando o e-mail de teste é enviado. 
   
    ![Alertas enviaram e-mail de notificação de teste](./media/storsimple-8000-manage-alerts/configure-alerts-email4.png)
   
   > [!NOTE]
   > Se a mensagem de notificação de teste não puder ser enviada, o serviço StorSimple Device Manager apresentará uma mensagem de erro apropriada. Aguarde alguns minutos e tente enviar novamente a sua mensagem de notificação de teste. 

5. Uma vez concluída a configuração, clique em **Guardar**. Quando lhe for pedida a confirmação, clique em **Sim**.

     ![Alertas enviaram e-mail de notificação de teste](./media/storsimple-8000-manage-alerts/configure-alerts-email5.png)

## <a name="view-and-track-alerts"></a>Ver e acompanhar os alertas

A lâmina de resumo do serviço StorSimple Device Manager proporciona-lhe um rápido olhar para o número de alertas nos seus dispositivos, organizados pelo nível de gravidade.

![Painel de alertas](./media/storsimple-8000-manage-alerts/device-summary4.png)

Clicar no nível de severidade abre a lâmina **alertas.** Os resultados incluem apenas os alertas que correspondem ao nível de gravidade.

Clicar num alerta na lista fornece-lhe detalhes adicionais para o alerta, incluindo a última vez que o alerta foi reportado, o número de ocorrências do alerta no dispositivo, e a ação recomendada para resolver o alerta. Se for um alerta de hardware, também identificará o componente de hardware.

![Exemplo de alerta de hardware](./media/storsimple-8000-manage-alerts/configure-alerts-email14.png)

Pode copiar os dados de alerta para um ficheiro de texto se precisar de enviar as informações para o Microsoft Support. Depois de ter seguido a recomendação e resolvido a condição de alerta no local, deverá limpar o alerta do dispositivo selecionando o alerta na lâmina **alerta e** clicando em **Limpar**. Para limpar vários alertas, selecione cada alerta, clique em qualquer coluna exceto na coluna **Alerta** e, em seguida, clique em **Limpar** depois de ter selecionado todos os alertas a serem limpos. Note que alguns alertas são automaticamente apurados quando o problema é resolvido ou quando o sistema atualiza o alerta com novas informações.

Quando clicar em **Limpar,** terá a oportunidade de fornecer comentários sobre o alerta e os passos que tomou para resolver o problema. Alguns eventos serão apurados pelo sistema se outro evento for desencadeado com novas informações. Nesse caso, verá a seguinte mensagem.

![Mensagem de alerta clara](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Ordenar e rever alertas

Você pode achar mais eficiente executar relatórios sobre alertas para que você possa revê-los e limpá-los em grupos. Além disso, a lâmina **alerta pode** exibir até 250 alertas. Se tiver excedido esse número de alertas, nem todos os alertas serão apresentados na vista predefinida. Pode combinar os seguintes campos para personalizar quais os alertas apresentados:

* **Estado** – Pode apresentar alertas **ativos** ou **limpos.** Os alertas ativos ainda estão a ser ativados no seu sistema, enquanto os alertas limpos foram limpos manualmente por um administrador ou eliminados programáticamente porque o sistema atualizou a condição de alerta com novas informações.
* **Gravidade** – Pode apresentar alertas de todos os níveis de gravidade (críticos, avisos, informações) ou apenas uma certa gravidade, como apenas alertas críticos.
* **Fonte** – Pode apresentar alertas de todas as fontes, ou limitar os alertas aos que vêm do serviço ou de um ou de todos os dispositivos.
* **Intervalo de tempo** – Ao especificar as **datas** e datas e datas de **início,** pode olhar para os alertas durante o período de tempo em que está interessado.

![Lista de alertas](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="alerts-quick-reference"></a>Alertas de referência rápida

As tabelas que se seguem listam alguns dos alertas Microsoft Azure StorSimple que poderá encontrar, bem como informações e recomendações adicionais sempre que disponível. Os alertas de dispositivo storSimple enquadram-se numa das seguintes categorias:

* [Alertas de conectividade na nuvem](#cloud-connectivity-alerts)
* [Alertas de cluster](#cluster-alerts)
* [Alertas de recuperação de desastres](#disaster-recovery-alerts)
* [Alertas de hardware](#hardware-alerts)
* [Alertas de insuficiência de emprego](#job-failure-alerts)
* [Alertas de volume fixados localmente](#locally-pinned-volume-alerts)
* [Alertas de rede](#networking-alerts)
* [Alertas de desempenho](#performance-alerts)
* [Alertas de segurança](#security-alerts)
* [Alertas de pacote de apoio](#support-package-alerts)
* [Alertas ambientais do recinto](#enclosure-environment-alerts)

### <a name="cloud-connectivity-alerts"></a>Alertas de conectividade na nuvem

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| A conectividade para <*nome credencial de nuvem*> não pode ser estabelecida. |Não é possível ligar à conta de armazenamento. |Parece que pode haver um problema de conectividade com o seu dispositivo. Por favor, execute o `Test-HcsmConnection` cmdlet a partir da Interface Windows PowerShell para StorSimple no seu dispositivo para identificar e corrigir o problema. Se as definições estiverem corretas, a questão poderá estar relacionada com as credenciais da conta de armazenamento para a qual o alerta foi levantado. Neste caso, utilize o `Test-HcsStorageAccountCredential` cmdlet para determinar se existem problemas que possa resolver.<ul><li>Verifique as definições da sua rede.</li><li>Verifique as credenciais da sua conta de armazenamento.</li></ul> |
| Não recebemos batimentos cardíacos do seu dispositivo nos últimos <*número*> minutos. |Não é possível ligar-se ao dispositivo. |Parece que há um problema de conectividade com o seu dispositivo. Utilize o `Test-HcsmConnection` cmdlet da Interface Windows PowerShell para StorSimple no seu dispositivo para identificar e corrigir o problema ou contactar o administrador da rede. |

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>StorSimple comportamento quando a conectividade na nuvem falha

O que acontece se a conectividade na nuvem falhar para o meu dispositivo StorSimple em funcionamento?

Se a conectividade em nuvem falhar no seu dispositivo de produção StorSimple, então, dependendo do estado do seu dispositivo, pode ocorrer o seguinte:

* **Para os dados locais do seu dispositivo**: Durante algum tempo, não haverá interrupções e as leituras continuarão a ser servidas. No entanto, à medida que o número de IOs pendentes aumenta e excede um limite, as leituras podem começar a falhar.

    Dependendo da quantidade de dados no seu dispositivo, as gravações também continuarão a ocorrer durante as primeiras horas após a interrupção na conectividade da nuvem. As gravações irão então abrandar e eventualmente começar a falhar se a conectividade da nuvem for interrompida por várias horas. (Existe armazenamento temporário no dispositivo para dados que devem ser empurrados para a nuvem. Esta área é lavada quando os dados são enviados. Se a conectividade falhar, os dados nesta área de armazenamento não serão empurrados para a nuvem, e a IO falhará.)
* **Para os dados na nuvem**: Para a maioria dos erros de conectividade na nuvem, é devolvido um erro. Uma vez restaurada a conectividade, os IOs são retomados sem que o utilizador tenha de colocar o volume online. Em casos raros, a intervenção do utilizador pode ser necessária para repor o volume online a partir do portal Azure.
* **Para instantâneos em nuvem em curso**: A operação é novamente experimentada algumas vezes dentro de 4-5 horas e se a conectividade não for restaurada, as imagens da nuvem falharão.

### <a name="cluster-alerts"></a>Alertas de cluster

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| O dispositivo falhou <*nome* do dispositivo>. |O dispositivo está em modo de manutenção. |O dispositivo falhou devido ao modo de manutenção de entrada ou saída. Isto é normal e não é necessária nenhuma ação. Depois de reconhecer este alerta, limpe-o na página de alertas. |
| O dispositivo falhou <*nome* do dispositivo>. |O firmware ou software do dispositivo acaba de ser atualizado. |Houve uma falha no cluster devido a uma atualização. Isto é normal e não é necessária nenhuma ação. Depois de reconhecer este alerta, limpe-o na página de alertas. |
| O dispositivo falhou <*nome* do dispositivo>. |O controlador foi desligado ou reiniciado. |O dispositivo falhou porque o controlador ativo foi desligado ou reiniciado por um administrador. Não é necessária nenhuma ação. Depois de reconhecer este alerta, limpe-o na página de alertas. |
| O dispositivo falhou <*nome* do dispositivo>. |Falha planeada. |Verifique se isto foi uma falha planeada. Depois de tomar as medidas apropriadas, limpe este alerta na página de alertas. |
| O dispositivo falhou <*nome* do dispositivo>. |Falha não planeada. |StorSimple é construído para recuperar automaticamente de falhas não planeadas. Se vir um grande número destes alertas, contacte o Microsoft Support. |
| O dispositivo falhou <*nome* do dispositivo>. |Outra causa/desconhecida. |Se vir um grande número destes alertas, contacte o Microsoft Support. Depois de resolvido o problema, limpe este alerta na página de alertas. |
| Um serviço de serviço crítico do dispositivo reporta o estado como falhado. |Falha no serviço datapath. |Contacte o Microsoft Support para obter assistência. |
| Endereço IP virtual para interface de rede <*DATA #*> reporta o estado como falhado. |Outra causa/desconhecida. |Às vezes, as condições temporárias podem causar estes alertas. Se for esse o caso, este alerta será automaticamente apurado após algum tempo. Se o problema persistir, contacte o Microsoft Support. |
| Endereço IP virtual para interface de rede <*DATA #*> reporta o estado como falhado. |Nome da interface: <*DATA #*> endereço IP não pode ser `<IP address>` trazido on-line porque um endereço IP duplicado foi detetado na rede. |Certifique-se de que o endereço IP duplicado é removido da rede ou reconfigure a interface com um endereço IP diferente. |

### <a name="disaster-recovery-alerts"></a>Alertas de recuperação de desastres

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| As operações de recuperação não conseguiram restaurar todas as configurações deste serviço. Os dados de configuração do dispositivo estão num estado inconsistente para alguns dispositivos. |Inconsistência de dados detetada após recuperação de desastres. |Os dados encriptados do serviço não são sincronizados com os do dispositivo. Autorizar o <nome do *dispositivo*> do StorSimple Device Manager para iniciar o processo de sincronização. Utilize a Interface Windows PowerShell para storSimple para executar o dispositivo <nome do `Restore-HcsmEncryptedServiceData` *dispositivo*> cmdlet, fornecendo a senha antiga como entrada para este cmdlet para restaurar o perfil de segurança. Em seguida, executar o `Invoke-HcsmServiceDataEncryptionKeyChange` cmdlet para atualizar a chave de encriptação de dados de serviço. Depois de tomar as medidas apropriadas, limpe este alerta na página de alertas. |

### <a name="hardware-alerts"></a>Alertas de hardware

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Componente de hardware <*componente>* reporta o estado como> *<estado.* | |Às vezes, as condições temporárias podem causar estes alertas. Em caso afirmativo, este alerta será automaticamente apurado após algum tempo. Se o problema persistir, contacte o Microsoft Support. |
| Avaria do controlador passivo. |O controlador passivo (secundário) não está a funcionar. |O seu dispositivo está operacional, mas um dos seus controladores está avariado. Tente reiniciar o controlador. Se o problema não for resolvido, contacte o Microsoft Support. |

### <a name="job-failure-alerts"></a>Alertas de insuficiência de emprego

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| A cópia de segurança do <> de *volume de origem* falhou. |O trabalho de reserva falhou. |Problemas de conectividade podem estar a impedir que a operação de backup seja concluída com sucesso. Se não houver problemas de conectividade, pode ter atingido o número máximo de backups. Elimine as cópias de segurança que já não sejam necessárias e relemisse a operação. Depois de tomar as medidas apropriadas, limpe este alerta na página de alertas. |
| O clone de <*elemento de backup de>* para <*números de série de volume de destino*> falhados. |O trabalho de clone falhou. |Refresque a lista de backup para verificar se a cópia de segurança ainda é válida. Se a cópia de segurança for válida, é possível que problemas de conectividade na nuvem estejam a impedir que a operação do clone seja concluída com sucesso. Se não houver problemas de conectividade, pode ter atingido o limite de armazenamento. Elimine as cópias de segurança que já não sejam necessárias e relemisse a operação. Depois de ter tomado as medidas adequadas para resolver o problema, limpe este alerta na página de alertas. |
| A restauração dos *IDs* de elemento de backup de <> falhou. |Restaurar o trabalho falhou. |Refresque a lista de backup para verificar se a cópia de segurança ainda é válida. Se a cópia de segurança for válida, é possível que problemas de conectividade na nuvem estejam a impedir que a operação de restauro seja concluída com sucesso. Se não houver problemas de conectividade, pode ter atingido o limite de armazenamento. Elimine as cópias de segurança que já não sejam necessárias e relemisse a operação. Depois de ter tomado as medidas adequadas para resolver o problema, limpe este alerta na página de alertas. |

### <a name="locally-pinned-volume-alerts"></a>Alertas de volume fixados localmente

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| A criação de volume local <nome de *volume*> falhou. |O emprego de criação de volume falhou. <*Mensagem de erro correspondente ao código de erro falhado*>. |Problemas de conectividade podem estar a impedir que a operação de criação espacial seja concluída com sucesso. Os volumes fixados localmente são densamente a provisionados e o processo de criação de espaço envolve derramar volumes hierárquicos para a nuvem. Se não houver problemas de conectividade, pode ter esgotado o espaço local do dispositivo. Determine se existe espaço no dispositivo antes de voltar a tentar esta operação. |
| A expansão do volume local <nome de *volume*> falhou. |O trabalho de modificação de volume falhou devido a <*mensagem de erro correspondente ao código* de erro falhado>. |Os problemas de conectividade podem estar a impedir que a operação de expansão do volume seja concluída com sucesso. Os volumes fixados localmente são densamente a provisionados e o processo de extensão do espaço existente envolve derramar volumes hierárquicos para a nuvem. Se não houver problemas de conectividade, pode ter esgotado o espaço local do dispositivo. Determine se existe espaço no dispositivo antes de voltar a tentar esta operação. |
| A conversão do volume <*> de nome* de volume falhou. |A função de conversão de volume para converter o tipo de volume de fixado localmente para tiered falhou. |Não foi possível concluir a conversão do volume do tipo fixado localmente para o tiered. Certifique-se de que não existem problemas de conectividade que impeçam o funcionamento com sucesso. Para resolver problemas de conectividade, vá para [a resolução de problemas com o cmdlet Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>O volume original fixado localmente foi agora marcado como um volume hierarquizado, uma vez que alguns dos dados do volume fixado localmente derramaram para a nuvem durante a conversão. O volume hierárquico resultante ainda está a ocupar espaço local no dispositivo que não pode ser recuperado para futuros volumes locais.<br>Resolva quaisquer problemas de conectividade, limpe o alerta e converta este volume de volta ao tipo de volume original fixado localmente para garantir que todos os dados são disponibilizados localmente novamente. |
| A conversão do volume <*> de nome* de volume falhou. |A função de conversão de volume para converter o tipo de volume de tiered para fixado localmente falhou. |Não foi possível concluir a conversão do volume do tipo hierárquico para o fixado localmente. Certifique-se de que não existem problemas de conectividade que impeçam o funcionamento com sucesso. Para resolver problemas de conectividade, vá para [a resolução de problemas com o cmdlet Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>O volume hierárquico original agora marcado como um volume fixado localmente como parte do processo de conversão continua a ter dados que residem na nuvem, enquanto o espaço densamente a provisionado no dispositivo para este volume já não pode ser recuperado para futuros volumes locais.<br>Resolva quaisquer problemas de conectividade, limpe o alerta e converta este volume de volta ao tipo de volume hierárquico original para garantir que o espaço local espesso a provisionado no dispositivo pode ser recuperado. |
| Aproximando-se do consumo de espaço local para instantâneos locais de <*nome do grupo de volume*> |As imagens locais para a política de backup podem em breve ficar sem espaço e ser invalidadas para evitar falhas de escrita do anfitrião. |Imagens locais frequentes ao lado de um alto volume de dados nos volumes associados a este grupo de política de backup estão a fazer com que o espaço local no dispositivo seja consumido rapidamente. Elimine quaisquer instantâneos locais que já não sejam necessários. Além disso, atualize os seus horários de instantâneos locais para esta política de backup para tirar fotos locais menos frequentes e certifique-se de que as fotos em nuvem são tiradas regularmente. Se estas ações não forem tomadas, o espaço local para estas imagens pode em breve ser esgotado e o sistema irá apagá-los automaticamente para garantir que as escritas do anfitrião continuem a ser processadas com sucesso. |
| Foram invalidadas as imagens locais para <*nome do grupo* de volume>. |As imagens locais para <*nome do grupo* de volume> foram invalidadas e depois apagadas porque estavam a exceder o espaço local do dispositivo. |Para garantir que isso não se repita no futuro, reveja os horários locais para esta política de backup e elimine quaisquer imagens locais que já não sejam necessárias. Imagens locais frequentes ao lado de um alto volume de dados nos volumes associados a este grupo de política de backup podem fazer com que o espaço local no dispositivo seja consumido rapidamente. |
| A restauração dos *IDs* de elemento de backup de <> falhou. |O trabalho de restauro falhou. |Se tiver fixado localmente ou uma mistura de volumes fixados localmente e hierarquizados nesta política de backup, reaque a lista de backup para verificar se a cópia de segurança ainda é válida. Se a cópia de segurança for válida, é possível que problemas de conectividade na nuvem estejam a impedir que a operação de restauro seja concluída com sucesso. Os volumes fixados localmente que estavam a ser restaurados como parte deste grupo instantâneo não têm todos os seus dados descarregados para o dispositivo, e, se tiver uma mistura de volumes hierárquicos e fixados localmente neste grupo instantâneo, eles não estarão sincronizados entre si. Para completar com sucesso a operação de restauro, desative os volumes deste grupo no hospedeiro e redoça a operação de restauro. Note que quaisquer modificações nos dados de volume que foram realizados durante o processo de restauração serão perdidas. |

### <a name="networking-alerts"></a>Alertas de rede

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Não foi possível iniciar o serviço StorSimple. |Erro do Datapath |Se o problema persistir, contacte o Microsoft Support. |
| Endereço IP duplicado detetado para 'Dados0'. | |O sistema detetou um conflito para o endereço IP '10.0.0.1'. O recurso de rede 'Data0' do dispositivo *\<device1>* está offline. Certifique-se de que este endereço IP não é utilizado por nenhuma outra entidade nesta rede. Para resolver problemas de rede, vá à [resolução de problemas com o Get-NetAdapter cmdlet](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Contacte o administrador da rede para ajudar a resolver este problema. Se o problema persistir, contacte o Microsoft Support. |
| O endereço IPv4 (ou IPv6) para 'Data0' está offline. | |O recurso de rede 'Data0' com endereço IP '10.0.0.1.' e o comprimento do prefixo '22' no dispositivo *\<device1>* está offline. Certifique-se de que as portas do interruptor a que esta interface está ligada estão operacionais. Para resolver problemas de rede, vá à [resolução de problemas com o Get-NetAdapter cmdlet](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
| Não foi possível ligar ao serviço de autenticação. |Erro do Datapath |O URL que é utilizado para autenticar não é acessível. Certifique-se de que as suas regras de firewall incluem os padrões de URL especificados para o dispositivo StorSimple. Para obter mais informações sobre padrões de URL no portal Azure, vá a https: \/ /aka.ms/ss-8000-network-reqs. Se utilizar a Nuvem do Governo de Azure, aceda aos padrões de URL em https: \/ /aka.ms/ss8000-gov-network-reqs.|

### <a name="performance-alerts"></a>Alertas de desempenho

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| A carga do dispositivo excedeu <*limiar*>. |Tempos de resposta mais lentos do que o esperado. |O seu dispositivo relata a utilização sob uma carga de entrada/saída pesada. Isto pode fazer com que o seu dispositivo não funcione tão bem como deveria. Reveja as cargas de trabalho que tem anexado ao dispositivo e determine se existem alguma que possa ser movida para outro dispositivo ou que já não sejam necessárias.|
| Não foi possível iniciar o serviço StorSimple. |Erro do Datapath |Se o problema persistir, contacte o Microsoft Support. |

### <a name="security-alerts"></a>Alertas de segurança

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| A sessão de suporte da Microsoft já começou. |Sessão de apoio a terceiros. |Por favor, confirme que este acesso está autorizado. Depois de tomar as medidas apropriadas, limpe este alerta na página de alertas. |
| A palavra-passe para <*elemento*> expirará em <*período de tempo*>. |A expiração da palavra-passe está a aproximar-se. |Altere a sua palavra-passe antes de expirar. |
| Falta informações de configuração de segurança para <*> de identificação do elemento.* | |Os volumes associados a este recipiente de volume não podem ser utilizados para replicar a sua configuração StorSimple. Para garantir que os seus dados sejam armazenados com segurança, recomendamos que elimine o recipiente de volume e quaisquer volumes associados ao recipiente de volume. Depois de tomar as medidas apropriadas, limpe este alerta na página de alertas. |
| <*tentativas* de login> de número falharam para <*> de identificação do elemento.* |Várias tentativas falhadas de início de súns. |O seu dispositivo pode estar a ser atacado ou um utilizador autorizado está a tentar ligar-se a uma palavra-passe incorreta.<ul><li>Contacte os seus utilizadores autorizados e verifique se estas tentativas eram de uma fonte legítima. Se continuar a ver um grande número de tentativas falhadas de login, considere desativar a gestão remota e contactar o administrador da rede. Depois de tomar as medidas apropriadas, limpe este alerta na página de alertas.</li><li>Verifique se as instâncias do seu Snapshot Manager estão configuradas com a palavra-passe correta. Depois de tomar as medidas apropriadas, limpe este alerta na página de alertas.</li></ul>Para mais informações, aceda a [alterar a palavra-passe do dispositivo expirada.](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password) |
| Uma ou mais falhas ocorreram durante a alteração da chave de encriptação de dados de serviço. | |Houve erros encontrados ao alterar a chave de encriptação de dados de serviço. Depois de ter abordado as condições de erro, execute o `Invoke-HcsmServiceDataEncryptionKeyChange` cmdlet a partir da Interface Windows PowerShell para StorSimple no seu dispositivo para atualizar o serviço. Se este problema persistir, contacte o suporte da Microsoft. Depois de resolver o problema, limpe este alerta na página de alertas. |

### <a name="support-package-alerts"></a>Alertas de pacote de apoio

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| A criação do pacote de apoio falhou. |StorSimple não conseguiu gerar o pacote. |Recandidutar esta operação. Se o problema persistir, contacte o Microsoft Support. Depois de ter resolvido o problema, limpe este alerta na página de alertas. |

### <a name="enclosure-environment-alerts"></a>Alertas ambientais do recinto

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Componente de hardware O sensor de temperatura ambiente reporta o estado falhado.  | Tipo de recinto: Recinto principal | Este alerta é acionado quando a temperatura ambiente exterior em torno de StorSimple está acima de um alcance aceitável. Verifique a temperatura exterior ambiente ou o fluxo de ar da ventilação cacífa no centro de dados. Quando a temperatura volta ao normal, o alerta é automaticamente apagado após algum tempo decorrido. Se o problema persistir, contacte o suporte da Microsoft.   |

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [erros StorSimple e problemas de implementação do dispositivo de resolução de problemas](storsimple-8000-troubleshoot-deployment.md).
