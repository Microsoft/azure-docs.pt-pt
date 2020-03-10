---
title: Ver e gerir alertas para StorSimple Virtual Array
description: Descreve as condições de alerta e gravidade do StorSimple Virtual Array e como utilizar o serviço StorSimple Manager para gerir alertas.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 97ee25a1-0ec3-4883-9a0a-54b722598462
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/12/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a7c469be4a1d8aba23857b1ba52ee829c126a431
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78365725"
---
# <a name="use-storsimple-device-manager-to-manage-alerts-for-the-storsimple-virtual-array"></a>Utilize o StorSimple Device Manager para gerir alertas para o StorSimple Virtual Array

## <a name="overview"></a>Descrição geral

A funcionalidade de alertas no serviço StorSimple Device Manager fornece uma forma de rever e limpar alertas relacionados com o StorSimple Virtual Arrays numa base em tempo real. Pode utilizar os alertas na lâmina **de resumo** do Serviço para monitorizar centralmente os problemas de saúde das suas Matrizes Virtuais StorSimple e a solução Global Microsoft Azure StorSimple.

Este tutorial descreve como configurar notificações de alerta, condições comuns de alerta, níveis de gravidade de alerta e como visualizar e rastrear alertas. Além disso, inclui tabelas de referência rápida de alerta, que lhe permitem localizar rapidamente um alerta específico e responder adequadamente.

![Página de alertas](./media/storsimple-virtual-array-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Configurar definições de alerta

Pode escolher se pretende ser notificado por e-mail das condições de alerta para cada um dos seus Arrays Virtuais StorSimple. Além disso, pode identificar outros destinatários de notificação de alerta introduzindo os seus endereços de e-mail na caixa adicional de destinatários de **e-mail,** separados por pontos evícolas.

> [!NOTE]
> Pode introduzir um máximo de 20 endereços de e-mail por matriz virtual.

Depois de ativar a notificação por e-mail para um conjunto virtual, os membros da lista de notificações receberão uma mensagem de e-mail sempre que ocorrer um alerta crítico. As mensagens serão enviadas a partir de *alertas simples-storsimple-noreply\@mail.windowsazure.com* e descreverão a condição de alerta. Os destinatários podem clicar em Cancelar a **subscrição** para se retirarem da lista de notificação de e-mail.

#### <a name="to-enable-email-notification-for-alerts"></a>Para ativar a notificação por e-mail para alertas

1. Vá ao serviço StorSimple Device Manager e na secção **Gestão,** selecione e clique em **Dispositivos**. A partir da lista de dispositivos apresentados, selecione e clique no seu dispositivo.
   
    ![definições de alerta](./media/storsimple-virtual-array-manage-alerts/alerts2.png)
2. Isto abre a lâmina **Definições.** Na secção **de definições** do Dispositivo, selecione **General**. Isto abre a lâmina De **Definições Gerais.**
   
    ![alerta sem configuração de notificação](./media/storsimple-virtual-array-manage-alerts/alerts4.png)
3. Na lâmina de **definições gerais,** vá à secção de **definições de alerta** e desloque o seguinte:
   
   1. No campo de notificação de **e-mail Enable,** selecione **SIM**.
   2. No campo de administradores de **serviços de e-mail,** selecione **SIM** se desejar ter o administrador de serviço e todos os coadministradores recebem as notificações de alerta.
   3. No campo adicional de destinatários de **email,** insira os endereços de e-mail de todos os outros destinatários que deverão receber as notificações de alerta. Introduza nomes no formato *que alguém\@somewhere.com.* Utilize pontos evívias para separar os endereços de e-mail. Pode configurar um máximo de 20 endereços de e-mail por dispositivo virtual.
      
       ![alerta sem configuração de notificação](./media/storsimple-virtual-array-manage-alerts/alerts6.png)
   4. Para enviar uma notificação de e-mail de teste, clique **em Enviar o email**do teste . O serviço StorSimple Device Manager apresentará mensagens de estado à medida que reencaminha a notificação do teste.
      
       ![Alertas de notificação de teste enviados](./media/storsimple-virtual-array-manage-alerts/alerts7.png)
      
      > [!NOTE]
      > Se a mensagem de notificação de teste não puder ser enviada, o serviço StorSimple Device Manager apresentará uma mensagem adequada. Clique **ok,** aguarde alguns minutos e tente enviar novamente a sua mensagem de notificação de teste.
      >
      >
   5. Na parte inferior da página, clique em **Guardar** para salvar a sua configuração. Quando lhe for pedida a confirmação, clique em **Sim**.
      
      ![Alertas de notificação de teste enviados](./media/storsimple-virtual-array-manage-alerts/alerts10.png)

## <a name="common-alert-conditions"></a>Condições comuns de alerta

O seu StorSimple Virtual Array gera alertas em resposta a uma variedade de condições. Seguem-se os tipos mais comuns de condições de alerta:

* **Problemas de conectividade** – Estes alertas ocorrem quando há dificuldade em transferir dados. Os problemas de comunicação podem ocorrer durante a transferência de dados de e para a conta de armazenamento Do Azure ou devido à falta de conectividade entre os dispositivos virtuais e o serviço StorSimple Device Manager. As questões de comunicação são algumas das mais difíceis de resolver porque há tantos pontos de falha. Deve verificar sempre primeiro que a conectividade da rede e o acesso à Internet estão disponíveis antes de continuar a ter problemas mais avançados. Para obter informações sobre portas e configurações de firewall, vá aos [requisitos do sistema StorSimple Virtual Array](storsimple-ova-system-requirements.md). Para obter ajuda na resolução de problemas, vá a [Troubleshoot com o cmdlet](storsimple-troubleshoot-deployment.md)de Ligação de Teste .
* **Problemas de desempenho** – Estes alertas são causados quando o seu sistema não está a funcionar da melhor forma, como quando está sob uma carga pesada.

Além disso, pode ver alertas relacionados com segurança, atualizações ou falhas de emprego.

## <a name="alert-severity-levels"></a>Níveis de gravidade do alerta

Os alertas têm diferentes níveis de gravidade, dependendo do impacto que a situação de alerta terá e da necessidade de uma resposta ao alerta. Os níveis de gravidade são:

* **Critical** – Este alerta é em resposta a uma condição que está a afetar o desempenho bem-sucedido do seu sistema. É necessária uma ação para garantir que o serviço StorSimple não seja interrompido.
* **Aviso** – Esta condição pode tornar-se crítica se não for resolvida. Deve investigar a situação e tomar todas as medidas necessárias para resolver a questão.
* **Informação** – Este alerta contém informações que podem ser úteis no rastreio e gestão do seu sistema.

## <a name="view-and-track-alerts"></a>Ver e rastrear alertas

A lâmina de resumo do serviço StorSimple Device Manager proporciona-lhe uma rápida olhada no número de alertas nos seus dispositivos virtuais, organizados pelo nível de gravidade.

![Painel de alertas](./media/storsimple-virtual-array-manage-alerts/alerts14.png)

Clique no nível de gravidade para abrir a lâmina **alertas.** Os resultados incluem apenas os alertas que correspondem ao nível de gravidade.

![Relatório de alertas com mira para o tipo de alerta](./media/storsimple-virtual-array-manage-alerts/alerts15.png)

Clique num alerta na lista para obter detalhes adicionais para o alerta, incluindo a última vez que o alerta foi reportado, o número de ocorrências do alerta no dispositivo e a ação recomendada para resolver o alerta.

![Lista de alertas e detalhes](./media/storsimple-virtual-array-manage-alerts/alerts16.png)

Pode copiar os dados de alerta para um ficheiro de texto se precisar enviar as informações para o Microsoft Support. Depois de ter seguido a recomendação e resolvido a condição de alerta no local, deverá retirar o alerta da lista. Selecione o alerta da lista e, em seguida, clique em **Clear**. Para limpar vários alertas, selecione cada alerta, clique em qualquer coluna exceto na coluna **'Alerta'** e, em seguida, clique em **Clear** depois de ter selecionado todos os alertas a serem apurados.

Quando clicar em **Clear,** terá a oportunidade de fornecer comentários sobre o alerta e os passos que tomou para resolver o problema.

![comentários alerta](./media/storsimple-virtual-array-manage-alerts/alerts17.png)

Alguns eventos serão apurados pelo sistema se outro evento for desencadeado com novas informações.

## <a name="sort-and-review-alerts"></a>Ordenar e rever alertas

A lâmina **alertapode** apresentar até 250 alertas. Se tiver excedido esse número de alertas, nem todos os alertas serão apresentados na vista padrão. Pode combinar os seguintes campos para personalizar quais os alertas apresentados:

* **Estado** – Pode apresentar alertas **Ativos** ou **Limpos.** Os alertas ativos continuam a ser acionados no seu sistema, enquanto os alertas limpos foram limpos manualmente por um administrador ou programaticamente limpos porque o sistema atualizou a condição de alerta com novas informações.
* **Gravidade** – Pode apresentar alertas de todos os níveis de gravidade (crítico, aviso, informação) ou apenas uma certa gravidade, como apenas alertas críticos.
* **Fonte** – Pode apresentar alertas de todas as fontes ou limitar os alertas a quem provém do serviço ou de um ou de todos os dispositivos virtuais.
* **Intervalo** de tempo – Especificando as datas **de From** e **To** e os selos de horário, pode ver alertas durante o período de tempo em que está interessado.

## <a name="alerts-quick-reference"></a>Alertas referência rápida

As tabelas seguintes listam alguns dos alertas StorSimple que poderá encontrar, bem como informações e recomendações adicionais sempre que disponível. Os alertas StorSimple Virtual Array enquadram-se numa das seguintes categorias:

* [Alertas de conectividade em nuvem](#cloud-connectivity-alerts)
* [Alertas de configuração](#configuration-alerts)
* [Alertas de insuficiência de emprego](#job-failure-alerts)
* [Alertas de desempenho](#performance-alerts)
* [Alertas de segurança](#security-alerts)

### <a name="cloud-connectivity-alerts"></a>Alertas de conectividade em nuvem

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Dispositivo < nome do*dispositivo*> não está ligado à nuvem. |O dispositivo nomeado não pode ligar-se à nuvem. |Não podia ligar-se à nuvem. Isto pode dever-se a um dos seguintes motivos:<ul><li>Pode haver um problema com as definições de rede no seu dispositivo.</li><li>Pode haver um problema com as credenciais da conta de armazenamento.</li></ul>Para obter mais informações sobre problemas de conectividade, vá à [Web UI local](storsimple-ova-web-ui-admin.md) do dispositivo. |

### <a name="configuration-alerts"></a>Alertas de configuração

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Configuração do dispositivo virtual no local sem suporte. |Desempenho lento. |A configuração atual pode resultar em degradação do desempenho. Certifique-se de que o seu servidor cumpre os requisitos mínimos de configuração. Para mais informações, vá ao [StorSimple Virtual Array Requirements](storsimple-ova-system-requirements.md). |
| Está a ficar sem espaço de disco aprovisionado em < nome do*dispositivo*\>. |Aviso de espaço de disco. |Está sem espaço em disco. Para libertar o espaço, considere mover cargas de trabalho para outro volume ou partilhar ou apagar dados. |

### <a name="job-failure-alerts"></a>Alertas de insuficiência de emprego

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Backup de\> de nome do*dispositivo* não pôde ser completado. |Falha no trabalho de reserva. |Não podia criar uma cópia de segurança. Considere um dos seguintes:<ul><li>Problemas de conectividade podem estar a impedir que a operação de backup esteja a ser concluída com sucesso. Certifique-se de que não existem problemas de conectividade. Para obter mais informações sobre problemas de conectividade, vá à [UI web local](storsimple-ova-web-ui-admin.md) para o seu dispositivo virtual.</li><li>Atingiu o limite de armazenamento disponível. Para libertar espaço, considere apagar quaisquer cópias de segurança que já não sejam necessárias.</li></ul> Resolva os problemas, limpe o alerta e tente novamente a operação. |
| Clone de\> de*dispositivo* não pôde ser completado. |Falha no trabalho de clone. |Não podia criar um clone. Considere um dos seguintes:<ul><li>A sua lista de cópias de segurança pode não ser válida. Refresque a lista para verificar se ainda é válida.</li><li>Problemas de conectividade podem estar a impedir que a operação do clone esteja a ser concluída com sucesso. Certifique-se de que não existem problemas de conectividade.</li><li>Atingiu o limite de armazenamento disponível. Para libertar espaço, considere apagar quaisquer cópias de segurança que já não sejam necessárias.</li></ul>Resolva os problemas, limpe o alerta e tente novamente a operação. |

### <a name="networking-alerts"></a>Alertas de rede

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Não conseguiu ligar-se ao serviço de autenticação. |Erro de datapath |O URL que é usado para autenticar não é alcançável. Certifique-se de que as suas regras de firewall incluem os padrões de URL especificados para o dispositivo StorSimple. Para obter mais informações sobre os padrões de URL no portal Azure, vá aos requisitos de [rede storSimple Virtual Array](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).|

### <a name="performance-alerts"></a>Alertas de desempenho

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Está a sofrer atrasos inesperados na transferência de dados. |Transferência lenta de dados. |Erros de estrangulamento ocorrem quando excede os alvos de escalabilidade de um serviço de armazenamento. O serviço de armazenamento faz isso para garantir que nenhum cliente ou inquilino possa usar o serviço em detrimento de outros. Para obter mais informações sobre a resolução de problemas da sua conta de armazenamento Azure, vá ao [Monitor, diagnostice e saque de problemas o Microsoft Azure Storage](../storage/common/storage-monitoring-diagnosing-troubleshooting.md). |
| Você está ficando sem espaço de disco de reserva local em < nome do*dispositivo*\>. |Tempo de resposta lenta. |10% do tamanho total previsto para o\> de*dispositivo* seleção  está reservado no dispositivo local e está agora a esgotar-se no espaço reservado. A carga de trabalho no\> do*dispositivo* está a gerar uma taxa mais elevada de churn ou pode ter migrado recentemente uma grande quantidade de dados. Isto pode resultar numa redução do desempenho. Considere uma das seguintes ações para resolver isto:<ul><li>Aumente a largura de banda da nuvem para este dispositivo.</li><li>Reduza ou mova as cargas de trabalho para outro volume ou partilha.</li></ul> |

### <a name="security-alerts"></a>Alertas de segurança

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| A palavra-passe para <*o nome* do dispositivo\> expirará em número *\>* dias. |Aviso de senha. |A sua palavra-passe expirará em <*número*\> dias. Considere mudar a sua senha. Para mais informações, vá alterar [a palavra-passe do administrador do dispositivo StorSimple Virtual Array](storsimple-virtual-array-change-device-admin-password.md). |

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre o StorSimple Virtual Array](storsimple-ova-overview.md).
