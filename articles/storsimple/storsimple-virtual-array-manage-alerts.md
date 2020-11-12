---
title: Ver e gerir alertas para StorSimple Virtual Array
description: Descreve as condições e gravidade do alerta virtual StorSimple e como utilizar o serviço StorSimple Manager para gerir alertas.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 97ee25a1-0ec3-4883-9a0a-54b722598462
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/12/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a24890f42b795bb4b9a7d187f74ceb9a4f8557f5
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94539231"
---
# <a name="use-storsimple-device-manager-to-manage-alerts-for-the-storsimple-virtual-array"></a>Utilize o Gestor de Dispositivos StorSimple para gerir alertas para o StorSimple Virtual Array

## <a name="overview"></a>Descrição Geral

A funcionalidade de alertas no serviço StorSimple Device Manager fornece uma forma de rever e limpar alertas relacionados com as Matrizes Virtuais StorSimple em tempo real. Pode utilizar os alertas na lâmina **de resumo** do Serviço para monitorizar centralmente os problemas de saúde das suas Matrizes Virtuais StorSimple e da solução geral microsoft Azure StorSimple.

Este tutorial descreve como configurar notificações de alerta, condições de alerta comuns, níveis de gravidade de alerta e como visualizar e rastrear alertas. Além disso, inclui tabelas de referência rápidas de alerta, que permitem localizar rapidamente um alerta específico e responder adequadamente.

![Página de alertas](./media/storsimple-virtual-array-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Configurar as definições de alertas

Pode escolher se pretende ser notificado por e-mail das condições de alerta de cada uma das suas Matrizes Virtuais StorSimple. Além disso, pode identificar outros destinatários de notificação de alerta inserindo os seus endereços de e-mail na caixa adicional de destinatários de **e-mail,** separada por pontos de e-mail.

> [!NOTE]
> Pode introduzir um máximo de 20 endereços de e-mail por matriz virtual.

Depois de ativar a notificação por e-mail para um conjunto virtual, os membros da lista de notificações receberão uma mensagem de e-mail sempre que ocorrer um alerta crítico. As mensagens serão *enviadas de alertas de storsimple-nor \@ profundamente mail.windowsazure.com* e descreverão a condição de alerta. Os destinatários podem clicar em Cancelar a **subscrição** para se removerem da lista de notificações por e-mail.

#### <a name="to-enable-email-notification-for-alerts"></a>Para ativar a notificação de e-mail para alertas

1. Aceda ao seu serviço StorSimple Device Manager e na secção **Gestão,selecione** e clique em **Dispositivos**. A partir da lista de dispositivos apresentados, selecione e clique no seu dispositivo.
   
    ![definições de alerta](./media/storsimple-virtual-array-manage-alerts/alerts2.png)
2. Isto abre a lâmina **de definições.** Na secção **de definições** do dispositivo, selecione **General**. Isto abre a lâmina **de Configurações Gerais.**
   
    ![A screenshot mostra o painel de definições do dispositivo com a área de definições de alerta chamada.](./media/storsimple-virtual-array-manage-alerts/alerts4.png)
3. Na lâmina **de definições gerais,** aceda à secção **de definições de alerta** e defina o seguinte:
   
   1. No campo **de notificação de e-mail Ativa,** selecione **SIM**.
   2. No campo **de administradores de serviço de e-mail,** selecione **SIM** se desejar que o administrador de serviço e todos os coadministradores recebam as notificações de alerta.
   3. No campo **de destinatários de e-mail adicionais,** insira os endereços de e-mail de todos os outros destinatários que devem receber as notificações de alerta. Introduza nomes no formato *que alguém \@ somewhere.com*. Utilize pontos de segurção para separar os endereços de e-mail. Pode configurar um máximo de 20 endereços de e-mail por dispositivo virtual.
      
       ![A screenshot mostra os detalhes das definições de alerta com as definições descritas neste passo.](./media/storsimple-virtual-array-manage-alerts/alerts6.png)
   4. Para enviar uma notificação de email de teste, clique em **Enviar email de teste**. O serviço StorSimple Device Manager apresentará mensagens de estado à medida que reencaminha a notificação de teste.
      
       ![A screenshot mostra uma caixa de diálogo informacional verificando o e-mail de teste.](./media/storsimple-virtual-array-manage-alerts/alerts7.png)
      
      > [!NOTE]
      > Se a mensagem de notificação de teste não puder ser enviada, o serviço StorSimple Device Manager apresentará uma mensagem apropriada. Clique **em OK,** aguarde alguns minutos e tente enviar novamente a sua mensagem de notificação de teste.
      >
      >
   5. Na parte inferior da página, clique em **Guardar** para guardar a sua configuração. Quando lhe for pedida a confirmação, clique em **Sim**.
      
      ![A screenshot mostra o painel de definições com o botão Guardar selecionado.](./media/storsimple-virtual-array-manage-alerts/alerts10.png)

## <a name="common-alert-conditions"></a>Condições de alerta comuns

O seu StorSimple Virtual Array gera alertas em resposta a uma variedade de condições. Seguem-se os tipos mais comuns de condições de alerta:

* **Problemas de conectividade** – Estes alertas ocorrem quando há dificuldade em transferir dados. Problemas de comunicação podem ocorrer durante a transferência de dados de e para a conta de armazenamento Azure ou devido à falta de conectividade entre os dispositivos virtuais e o serviço StorSimple Device Manager. As questões de comunicação são algumas das mais difíceis de corrigir, porque há tantos pontos de falha. Deve verificar-se sempre primeiro se a conectividade da rede e o acesso à Internet estão disponíveis antes de continuar a uma resolução de problemas mais avançada. Para obter informações sobre portas e configurações de firewall, aceda aos [requisitos do sistema StorSimple Virtual Array](storsimple-ova-system-requirements.md). Para obter ajuda na resolução de problemas, vá à [resolução de problemas com o Test-Connection cmdlet](storsimple-troubleshoot-deployment.md).
* **Problemas de desempenho** – Estes alertas são causados quando o seu sistema não está a funcionar da melhor forma, como quando está sob uma carga pesada.

Além disso, poderá ver alertas relacionados com segurança, atualizações ou falhas de emprego.

## <a name="alert-severity-levels"></a>Níveis de gravidade de alerta

Os alertas têm diferentes níveis de gravidade, dependendo do impacto que a situação de alerta terá e da necessidade de uma resposta ao alerta. Os níveis de gravidade são:

* **Crítico** – Este alerta é uma resposta a uma condição que está a afetar o desempenho bem sucedido do seu sistema. São necessárias medidas para garantir que o serviço StorSimple não seja interrompido.
* **Aviso** – Esta condição pode tornar-se crítica se não for resolvida. Deve investigar a situação e tomar todas as medidas necessárias para resolver o problema.
* **Informação** – Este alerta contém informações que podem ser úteis no rastreio e gestão do seu sistema.

## <a name="view-and-track-alerts"></a>Ver e acompanhar os alertas

A lâmina de resumo do serviço StorSimple Device Manager proporciona-lhe um rápido olhar para o número de alertas nos seus dispositivos virtuais, organizados pelo nível de gravidade.

![Painel de alertas](./media/storsimple-virtual-array-manage-alerts/alerts14.png)

Clique no nível de gravidade para abrir a lâmina **alertas.** Os resultados incluem apenas os alertas que correspondem ao nível de gravidade.

![Relatório de alertas abordados para tipo de alerta](./media/storsimple-virtual-array-manage-alerts/alerts15.png)

Clique num alerta na lista para obter mais detalhes para o alerta, incluindo a última vez que o alerta foi reportado, o número de ocorrências do alerta no dispositivo, e a ação recomendada para resolver o alerta.

![Lista de alertas e detalhes](./media/storsimple-virtual-array-manage-alerts/alerts16.png)

Pode copiar os dados de alerta para um ficheiro de texto se precisar de enviar as informações para o Microsoft Support. Depois de ter seguido a recomendação e resolvido a condição de alerta no local, deverá limpar o alerta da lista. Selecione o alerta da lista e, em seguida, clique em **Limpar**. Para limpar vários alertas, selecione cada alerta, clique em qualquer coluna exceto na coluna **Alerta** e, em seguida, clique em **Limpar** depois de ter selecionado todos os alertas a serem limpos.

Quando clicar em **Limpar,** terá a oportunidade de fornecer comentários sobre o alerta e os passos que tomou para resolver o problema.

![comentários de alerta](./media/storsimple-virtual-array-manage-alerts/alerts17.png)

Alguns eventos serão apurados pelo sistema se outro evento for desencadeado com novas informações.

## <a name="sort-and-review-alerts"></a>Ordenar e rever alertas

A lâmina **alerta** pode apresentar até 250 alertas. Se tiver excedido esse número de alertas, nem todos os alertas serão apresentados na vista predefinida. Pode combinar os seguintes campos para personalizar quais os alertas apresentados:

* **Estado** – Pode apresentar alertas **ativos** ou **limpos.** Os alertas ativos ainda estão a ser ativados no seu sistema, enquanto os alertas limpos foram limpos manualmente por um administrador ou eliminados programáticamente porque o sistema atualizou a condição de alerta com novas informações.
* **Gravidade** – Pode apresentar alertas de todos os níveis de gravidade (críticos, avisos, informações) ou apenas uma certa gravidade, como apenas alertas críticos.
* **Fonte** – Pode apresentar alertas de todas as fontes, ou limitar os alertas aos que vêm do serviço ou de um ou de todos os dispositivos virtuais.
* **Intervalo de tempo** – Ao especificar as **datas** e datas e datas de **início,** pode olhar para os alertas durante o período de tempo em que está interessado.

## <a name="alerts-quick-reference"></a>Alertas de referência rápida

As tabelas que se seguem listam alguns dos alertas StorSimple que poderá encontrar, bem como informações e recomendações adicionais sempre que disponível. Os alertas StorSimple Virtual Array enquadram-se numa das seguintes categorias:

* [Alertas de conectividade na nuvem](#cloud-connectivity-alerts)
* [Alertas de configuração](#configuration-alerts)
* [Alertas de insuficiência de emprego](#job-failure-alerts)
* [Alertas de desempenho](#performance-alerts)
* [Alertas de segurança](#security-alerts)

### <a name="cloud-connectivity-alerts"></a>Alertas de conectividade na nuvem

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| O *<nome* do dispositivo> não está ligado à nuvem. |O dispositivo nomeado não pode ligar-se à nuvem. |Não conseguia ligar-se à nuvem. Isto pode dever-se a um dos seguintes motivos:<ul><li>Pode haver um problema com as definições de rede no seu dispositivo.</li><li>Pode haver um problema com as credenciais da conta de armazenamento.</li></ul>Para obter mais informações sobre problemas de conectividade de resolução de problemas, aceda à [uI web local](storsimple-ova-web-ui-admin.md) do dispositivo. |

### <a name="configuration-alerts"></a>Alertas de configuração

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Configuração do dispositivo virtual no local sem suporte. |Desempenho lento. |A configuração atual pode resultar na degradação do desempenho. Certifique-se de que o seu servidor cumpre os requisitos mínimos de configuração. Para mais informações, aceda aos [requisitos de matriz virtual StorSimple.](storsimple-ova-system-requirements.md) |
| Está a ficar sem espaço de disco previsto < *nome do dispositivo.* \> |Aviso de espaço em disco. |Estás a ficar sem espaço em disco. Para libertar o espaço, considere mover cargas de trabalho para outro volume ou partilhar ou eliminar dados. |

### <a name="job-failure-alerts"></a>Alertas de insuficiência de emprego

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| A cópia de segurança do *<nome* do dispositivo não \> foi concluída. |Falha no trabalho de reserva. |Não poderia criar um backup. Considere um dos seguintes:<ul><li>Problemas de conectividade podem estar a impedir que a operação de backup seja concluída com sucesso. Certifique-se de que não existem problemas de conectividade. Para obter mais informações sobre problemas de conectividade de resolução de problemas, aceda à [UI web local](storsimple-ova-web-ui-admin.md) para o seu dispositivo virtual.</li><li>Atingiu o limite de armazenamento disponível. Para libertar espaço, considere eliminar quaisquer backups que já não sejam necessários.</li></ul> Resolva os problemas, limpe o alerta e relemisse a operação. |
| O clone do *<nome* do dispositivo não \> pôde ser concluído. |Falha no trabalho do clone. |Não podia criar um clone. Considere um dos seguintes:<ul><li>A sua lista de cópias de segurança pode não ser válida. Refresque a lista para verificar se ainda é válida.</li><li>Problemas de conectividade podem estar a impedir que a operação do clone seja concluída com sucesso. Certifique-se de que não existem problemas de conectividade.</li><li>Atingiu o limite de armazenamento disponível. Para libertar espaço, considere eliminar quaisquer backups que já não sejam necessários.</li></ul>Resolva os problemas, limpe o alerta e relemisse a operação. |

### <a name="networking-alerts"></a>Alertas de rede

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Não foi possível ligar ao serviço de autenticação. |Erro do Datapath |O URL que é utilizado para autenticar não é acessível. Certifique-se de que as suas regras de firewall incluem os padrões de URL especificados para o dispositivo StorSimple. Para obter mais informações sobre padrões de URL no portal Azure, aceda aos [requisitos de rede StorSimple Virtual Array](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).|

### <a name="performance-alerts"></a>Alertas de desempenho

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Está a registar atrasos inesperados na transferência de dados. |Transferência lenta de dados. |Os erros de estrangulamento ocorrem quando excede os objetivos de escalabilidade de um serviço de armazenamento. O serviço de armazenamento faz isso para garantir que nenhum cliente ou inquilino possa usar o serviço em detrimento de outros. Para obter mais informações sobre a resolução de problemas da sua conta de armazenamento Azure, vá ao [Monitor, diagnostice e resolva problemas do Microsoft Azure Storage](../storage/common/storage-monitoring-diagnosing-troubleshooting.md). |
| Está a ficar sem espaço no disco de reservas local < *nome do dispositivo.* \> |Tempo de resposta lento. |10% do tamanho total previsto para <nome do *dispositivo* \> é reservado no dispositivo local e está agora a esgotar-se no espaço reservado. A carga de trabalho no nome < *dispositivo* \> está a gerar uma taxa mais elevada de churn ou pode ter migrado recentemente uma grande quantidade de dados. Isto pode resultar num desempenho reduzido. Considere uma das seguintes ações para resolver isto:<ul><li>Aumente a largura de banda da nuvem para este dispositivo.</li><li>Reduza ou mova cargas de trabalho para outro volume ou partilha.</li></ul> |

### <a name="security-alerts"></a>Alertas de segurança

| Texto de alerta | Evento | Mais informações / ações recomendadas |
|:--- |:--- |:--- |
| A palavra-passe para < *nome do dispositivo* \> expirará em <dias de *número.* \> |Aviso de senha. |A sua palavra-passe expirará em *number* \> <dias. Considere mudar a sua senha. Para obter mais informações, aceda à [palavra-passe do administrador do dispositivo StorSimple Virtual Array](storsimple-virtual-array-change-device-admin-password.md). |

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre o Conjunto Virtual StorSimple.](storsimple-ova-overview.md)
