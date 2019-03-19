---
title: Ver e gerir alertas do Microsoft Azure StorSimple Virtual Array | Documentos da Microsoft
description: Descreve como utilizar o serviço StorSimple Manager para gerir alertas e gravidade e condições de alerta do StorSimple Virtual Array.
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
ms.openlocfilehash: bb6ef5a87c5610d90188471db961ef20dfb18835
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57895469"
---
# <a name="use-storsimple-device-manager-to-manage-alerts-for-the-storsimple-virtual-array"></a>Utilize o StorSimple Device Manager para gerir os alertas para a matriz Virtual StorSimple

## <a name="overview"></a>Descrição geral

A funcionalidade de alertas no serviço StorSimple Device Manager fornece uma forma para que Revise e limpar alertas relacionados com as matrizes virtuais do StorSimple de forma em tempo real. Pode utilizar os alertas sobre o **resumo de serviço** painel para monitorizar centralmente os problemas de estado de funcionamento das suas matrizes virtuais do StorSimple e a solução geral do Microsoft Azure StorSimple.

Este tutorial descreve como configurar notificações de alerta, condições de alerta comuns, níveis de gravidade do alerta e como ver e acompanhar os alertas. Além disso, ele inclui tabelas de referência rápida de alerta, permitindo-lhe rapidamente localize um alerta específico e responder de forma apropriada.

![Página de alertas](./media/storsimple-virtual-array-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Configurar definições de alerta

Pode escolher se pretende ser notificado por e-mail as condições de alerta para cada uma das suas matrizes virtuais do StorSimple. Além disso, pode identificar outros destinatários de notificação de alerta, introduzindo os respetivos endereços de e-mail no **destinatários de e-mail adicionais** caixa, separada por ponto e vírgula.

> [!NOTE]
> Pode introduzir um máximo de 20 endereços de e-mail por matriz virtual.

Depois de ativar notificações por e-mail para uma matriz virtual, os membros da lista de notificação irão receber uma mensagem de e-mail sempre que ocorre de um alerta crítico. As mensagens serão enviadas *storsimple-alertas-noreply\@mail.windowsazure.com* e descreve a condição do alerta. Os destinatários podem clicam **Unsubscribe** para remover da lista de notificação de e-mail.

#### <a name="to-enable-email-notification-for-alerts"></a>Para ativar notificações por e-mail para alertas

1. Aceda ao seu serviço StorSimple Device Manager e, no **gerenciamento** secção, selecione e clique em **dispositivos**. Na lista de dispositivos apresentado, selecione e clique no seu dispositivo.
   
    ![definições de alerta](./media/storsimple-virtual-array-manage-alerts/alerts2.png)
2. Esta ação abre o **definições** painel. Na **definições do dispositivo** secção, selecione **geral**. Esta ação abre o **definições gerais** painel.
   
    ![configuração de notificação de alertas](./media/storsimple-virtual-array-manage-alerts/alerts4.png)
3. Na **definições gerais** painel, aceda à **definições de alerta** secção e defina o seguinte:
   
   1. Na **ativar a notificação de e-mail** campo, selecione **Sim**.
   2. Na **E-mail aos administradores de serviço** campo, selecione **Sim** se pretender que o administrador de serviço e todos os colegas administradores recebam notificações de alerta.
   3. Na **destinatários de e-mail adicionais** , insira os endereços de e-mail de todos os outros destinatários que devem receber notificações de alerta. Introduza os nomes no formato *alguém\@somewhere.com*. Utilize ponto e vírgula para separar os endereços de e-mail. Pode configurar um máximo de 20 endereços de e-mail por dispositivo virtual.
      
       ![configuração de notificação de alertas](./media/storsimple-virtual-array-manage-alerts/alerts6.png)
   4. Para enviar uma notificação de e-mail de teste, clique em **enviar e-mail de teste**. O serviço StorSimple Device Manager irá apresentar mensagens de estado, como ele encaminha a notificação de teste.
      
       ![Alertas de e-mail de notificação enviado de teste](./media/storsimple-virtual-array-manage-alerts/alerts7.png)
      
      > [!NOTE]
      > Se não é possível enviar a mensagem de notificação de teste, o serviço StorSimple Device Manager exibirá uma mensagem apropriada. Clique em **OK**, aguarde alguns minutos e, em seguida, tente enviar a mensagem de notificação de teste novamente.
      >
      >
   5. Na parte inferior da página, clique em **guardar** para guardar a configuração. Quando lhe for pedida a confirmação, clique em **Sim**.
      
      ![Alertas de e-mail de notificação enviado de teste](./media/storsimple-virtual-array-manage-alerts/alerts10.png)

## <a name="common-alert-conditions"></a>Condições de alerta comuns

A sua matriz Virtual StorSimple gera alertas em resposta a uma variedade de condições. Seguem-se os tipos mais comuns das condições de alerta:

* **Problemas de conectividade** – estes alertas ocorrem quando há dificuldade na transferência de dados. Podem ocorrer problemas de comunicação durante a transferência de dados para e da conta de armazenamento do Azure ou devido à falta de conectividade entre os dispositivos virtuais e o serviço StorSimple Device Manager. Problemas de comunicação são alguns dos mais difíceis de corrigir porque existem tantos pontos de falha. Deve sempre primeiro certifique-se de que o acesso à Internet e de conectividade de rede estão disponíveis antes de continuar para a resolução de problemas mais avançados. Para informações sobre as portas e as definições da firewall, aceda a [requisitos de sistema do StorSimple Virtual Array](storsimple-ova-system-requirements.md). Para obter ajuda na resolução de problemas, aceda a [resolver problemas com o cmdlet Test-Connection](storsimple-troubleshoot-deployment.md).
* **Problemas de desempenho** – estes alertas são causados quando de seu sistema não estiver a executar de forma otimizada, como quando ele é sob uma carga pesada.

Além disso, poderá ver alertas relacionados com segurança, atualizações ou falhas de tarefas.

## <a name="alert-severity-levels"></a>Níveis de gravidade do alerta

Os alertas têm níveis de gravidade de diferente, consoante o impacto que terá a situação de alerta e a necessidade de uma resposta ao alerta. Os níveis de gravidade são:

* **Crítico** – este alerta ocorre em resposta a uma condição que está a afetar o desempenho com êxito de seu sistema. Para garantir que o serviço não é interrompido o StorSimple, é necessária ação.
* **Aviso** – esta condição pode se tornar crítica, se não resolver. Deve investigar a situação e tomar nenhuma ação necessária para limpar o problema.
* **Informações** – este alerta contém informações que podem ser útil para controlar e gerir o sistema.

## <a name="view-and-track-alerts"></a>Ver e acompanhar os alertas

Painel de resumo do serviço StorSimple Device Manager fornece uma rápida olhada no número de alertas nos seus dispositivos virtuais, organizados por nível de gravidade.

![Dashboard de alertas](./media/storsimple-virtual-array-manage-alerts/alerts14.png)

Clique para abrir o nível de gravidade a **alertas** painel. Os resultados incluem apenas os alertas que correspondam a esse nível de gravidade.

![Relatório de alertas no âmbito de tipo de alerta](./media/storsimple-virtual-array-manage-alerts/alerts15.png)

Clique num alerta na lista para obter detalhes adicionais para o alerta, incluindo a última vez que o alerta foi reportado, o número de ocorrências do alerta no dispositivo e a ação recomendada para resolver o alerta.

![Lista de alertas e detalhes](./media/storsimple-virtual-array-manage-alerts/alerts16.png)

Pode copiar os detalhes do alerta para um arquivo de texto, se tiver de enviar as informações ao Support da Microsoft. Depois de seguir a recomendação e resolver a condição do alerta no local, deve limpar o alerta na lista. Selecione o alerta na lista e, em seguida, clique em **clara**. Para limpar vários alertas, selecione cada alerta, clique em qualquer coluna, exceto os **alerta** coluna e clique em **limpar** depois de selecionar todos os alertas para ser limpo.

Quando clica em **clara**, terá a oportunidade de fornecer comentários sobre o alerta e os passos que tirou para resolver o problema.

![comentários de alerta](./media/storsimple-virtual-array-manage-alerts/alerts17.png)

Alguns eventos serão limpos pelo sistema se outro evento for acionado com novas informações.

## <a name="sort-and-review-alerts"></a>Alertas de ordenação e revisão

O **alertas** painel pode apresentar até 250 alertas. Se tiver excedido o número de alertas, nem todos os alertas serão apresentados na vista predefinida. Pode combinar os campos seguintes para personalizar os alertas são apresentados:

* **Estado** – pode mostrar **Active Directory** ou **compensado** alertas. Ainda estão sendo acionados alertas ativos no seu sistema, enquanto os alertas desmarcadas foram seja limpos manualmente por um administrador ou por meio de programação apagadas porque o sistema atualizado a condição de alerta com novas informações.
* **Gravidade** – pode exibir os alertas de todos os níveis de gravidade (críticos, aviso, informações) ou apenas uma determinada gravidade, como alertas críticos apenas.
* **Origem** – pode apresentar os alertas de todas as origens ou limitar os alertas para aqueles que vierem a partir do serviço ou um ou todos os dispositivos virtuais.
* **Intervalo de tempo** – ao especificar o **da** e **para** datas e carimbos de data / hora, pode olhar alertas durante o período de tempo que está interessado.

## <a name="alerts-quick-reference"></a>Referência rápida de alertas

As tabelas seguintes listam alguns dos alertas StorSimple que poderá encontrar, bem como recomendações e informações adicionais que estiverem disponíveis. Alertas de matriz Virtual StorSimple se encaixam em uma das seguintes categorias:

* [Alertas de conectividade de cloud](#cloud-connectivity-alerts)
* [Alertas de configuração](#configuration-alerts)
* [Alertas de falha da tarefa](#job-failure-alerts)
* [Alertas de desempenho](#performance-alerts)
* [Alertas de segurança](#security-alerts)

### <a name="cloud-connectivity-alerts"></a>Alertas de conectividade de cloud

| Texto de alerta | Evento | Obter mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Dispositivo <*nome do dispositivo*> não está ligado à cloud. |O dispositivo com o nome não é possível ligar à cloud. |Não foi possível ligar à cloud. Isto pode dever-se a um dos seguintes motivos:<ul><li>Pode haver um problema com as definições de rede no seu dispositivo.</li><li>Pode haver um problema com as credenciais da conta de armazenamento.</li></ul>Para obter mais informações sobre como resolver problemas de conectividade, vá para o [da IU web local](storsimple-ova-web-ui-admin.md) do dispositivo. |

### <a name="configuration-alerts"></a>Alertas de configuração

| Texto de alerta | Evento | Obter mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Configuração do dispositivo virtual no local não suportada. |Desempenho lento. |A configuração atual pode resultar em degradação do desempenho. Certifique-se de que o servidor cumpre os requisitos mínimos de configuração. Para obter mais informações, aceda a [requisitos da matriz Virtual StorSimple](storsimple-ova-system-requirements.md). |
| Estiver a ficar sem espaço no disco aprovisionado <*nome do dispositivo*\>. |Aviso de espaço em disco. |Tiver pouco espaço em disco aprovisionado. Para libertar espaço, considere mover cargas de trabalho para outro volume ou partilha ou eliminá-los. |

### <a name="job-failure-alerts"></a>Alertas de falha da tarefa

| Texto de alerta | Evento | Obter mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Cópia de segurança de <*nome do dispositivo* \> não foi possível concluir. |Falha da tarefa de cópia de segurança. |Não foi possível criar uma cópia de segurança. Considere um dos seguintes:<ul><li>Problemas de conectividade podem estar a impedir a operação de cópia de segurança conclusão com êxito. Certifique-se de que não existem não existem problemas de conectividade. Para obter mais informações sobre como resolver problemas de conectividade, vá para o [da IU web local](storsimple-ova-web-ui-admin.md) para o seu dispositivo virtual.</li><li>Atingiu o limite de armazenamento disponível. Para libertar espaço, considere a eliminar quaisquer cópias de segurança que já não são necessários.</li></ul> Resolva os problemas, limpe o alerta e repita a operação. |
| Clone de <*nome do dispositivo* \> não foi possível concluir. |Falha da tarefa de clone. |Não foi possível criar um clone. Considere um dos seguintes:<ul><li>Sua lista de cópia de segurança pode não ser válida. Atualize a lista para verificar se que ele ainda é válido.</li><li>Problemas de conectividade podem estar a impedir a operação de clonagem conclusão com êxito. Certifique-se de que não existem não existem problemas de conectividade.</li><li>Atingiu o limite de armazenamento disponível. Para libertar espaço, considere a eliminar quaisquer cópias de segurança que já não são necessários.</li></ul>Resolva os problemas, limpe o alerta e repita a operação. |

### <a name="networking-alerts"></a>Alertas de sistema de rede

| Texto de alerta | Evento | Obter mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Não foi possível ligar ao serviço de autenticação. |Erro de DataPath |O URL que é utilizado para autenticar não está acessível. Certifique-se de que as regras da firewall incluem os padrões de URL especificados para o dispositivo StorSimple. Para obter mais informações sobre padrões de URL no portal do Azure, aceda a [StorSimple Virtual Array, requisitos de rede](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).|

### <a name="performance-alerts"></a>Alertas de desempenho

| Texto de alerta | Evento | Obter mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Ocorrem atrasos inesperados na transferência de dados. |Transferência de dados lentos. |Erros de limitação ocorrerem quando exceder os destinos de escalabilidade de um serviço de armazenamento. O serviço de armazenamento faz isso para garantir que nenhum inquilino ou cliente único pode utilizar o serviço às custas de outros utilizadores. Para obter mais informações sobre como resolver a sua conta de armazenamento do Azure, aceda a [monitorizar, diagnosticar e resolver problemas de armazenamento do Microsoft Azure](../storage/common/storage-monitoring-diagnosing-troubleshooting.md). |
| Há pouca reserva local espaço em disco no <*nome do dispositivo*\>. |Tempo lento de resposta. |10% do tamanho total aprovisionado para <*nome do dispositivo* \> está reservado no local e dispositivo são agora a esgotar no espaço reservado. A carga de trabalho no <*nome do dispositivo* \> está gerando uma maior taxa de alterações a dados, ou pode ter migrou recentemente uma grande quantidade de dados. Isso pode resultar num desempenho reduzido. Considere uma das seguintes ações para resolver este problema:<ul><li>Aumente a largura de banda de cloud neste dispositivo.</li><li>Reduzir ou mover cargas de trabalho para outro volume ou partilha.</li></ul> |

### <a name="security-alerts"></a>Alertas de segurança

| Texto de alerta | Evento | Obter mais informações / ações recomendadas |
|:--- |:--- |:--- |
| Palavra-passe para <*nome do dispositivo* \> irá expirar dentro de <*número* \> dias. |Aviso de palavra-passe. |A palavra-passe irá expirar dentro de <*número* \> dias. Considere alterar a palavra-passe. Para obter mais informações, aceda a [alterar a senha de administrador de dispositivo do StorSimple Virtual Array](storsimple-virtual-array-change-device-admin-password.md). |

## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre o StorSimple Virtual Array](storsimple-ova-overview.md).
